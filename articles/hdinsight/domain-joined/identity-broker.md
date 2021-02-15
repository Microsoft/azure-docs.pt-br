---
title: Agente de ID do Azure HDInsight (HIB)
description: Saiba mais sobre o agente de ID do Azure HDInsight para simplificar a autenticação para clusters de Apache Hadoop ingressados no domínio.
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 47ba11260c3b58566963e5a3ffac80ca461a8a23
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946824"
---
# <a name="azure-hdinsight-id-broker-hib"></a>Agente de ID do Azure HDInsight (HIB)

Este artigo descreve como configurar e usar o recurso agente de ID do Azure HDInsight. Você pode usar esse recurso para obter a autenticação OAuth moderna para o Apache Ambari enquanto tem a imposição de autenticação multifator sem precisar de hashes de senha herdada no Azure Active Directory Domain Services (Azure AD DS).

## <a name="overview"></a>Visão geral

O agente de ID do HDInsight simplifica as configurações de autenticação complexas nos seguintes cenários:

* Sua organização depende da Federação para autenticar usuários para acessar recursos de nuvem. Anteriormente, para usar os clusters do HDInsight Enterprise Security Package, era necessário habilitar a sincronização de hash de senha do seu ambiente local para Azure Active Directory (AD do Azure). Esse requisito pode ser difícil ou indesejável para algumas organizações.
* Sua organização deseja impor a autenticação multifator para acesso baseado na Web ou HTTP ao Apache Ambari e outros recursos de cluster.

O agente de ID do HDInsight fornece a infraestrutura de autenticação que habilita a transição de protocolo do OAuth (moderno) para o Kerberos (Herdado) sem a necessidade de sincronizar hashes de senha para o Azure AD DS. Essa infraestrutura consiste em componentes em execução em uma VM (máquina virtual) do Windows Server com o nó agente de ID do HDInsight habilitado, juntamente com nós de gateway de cluster.

Use a tabela a seguir para determinar a melhor opção de autenticação com base nas necessidades da sua organização.

|Opções de autenticação |Configuração do HDInsight | Fatores a serem considerados |
|---|---|---|
| OAuth totalmente | Agente de ID do Enterprise Security Package + HDInsight | Opção mais segura. (Há suporte para autenticação multifator.) *Não* é necessário passar a sincronização de hash. Nenhum acesso SSH/kinit/keytab para contas locais, que não têm hash de senha no Azure AD DS. Contas somente em nuvem ainda podem SSH/kinit/keytab. Acesso baseado na Web a Ambari por meio do OAuth. Requer a atualização de aplicativos herdados (por exemplo, JDBC/ODBC) para dar suporte ao OAuth.|
| OAuth + autenticação básica | Agente de ID do Enterprise Security Package + HDInsight | Acesso baseado na Web a Ambari por meio do OAuth. Os aplicativos herdados continuam a usar a autenticação básica. A autenticação multifator deve ser desabilitada para acesso básico à autenticação. *Não* é necessário passar a sincronização de hash. Nenhum acesso SSH/kinit/keytab para contas locais, que não têm hash de senha no Azure AD DS. Contas somente em nuvem ainda podem SSH/kinit. |
| Autenticação totalmente básica | Enterprise Security Package | Mais semelhante às configurações locais. A sincronização de hash de senha para o Azure AD DS é necessária. As contas locais podem SSH/kinit ou usar keytab. A autenticação multifator deverá ser desabilitada se o armazenamento de backup estiver Azure Data Lake Storage Gen2. |

O diagrama a seguir mostra o fluxo de autenticação baseado em OAuth moderno para todos os usuários, incluindo usuários federados, depois que o agente de ID do HDInsight está habilitado:

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="Diagrama que mostra o fluxo de autenticação com o agente de ID do HDInsight.":::

Neste diagrama, o cliente (ou seja, um navegador ou aplicativo) precisa adquirir o token OAuth primeiro. Em seguida, ele apresenta o token para o gateway em uma solicitação HTTP. Se você já tiver entrado em outros serviços do Azure, como o portal do Azure, você pode entrar em seu cluster HDInsight com uma experiência de logon único.

Ainda pode haver muitos aplicativos herdados que dão suporte apenas à autenticação básica (isto é, nome de usuário e senha). Para esses cenários, você ainda pode usar a autenticação básica HTTP para se conectar aos gateways de cluster. Nessa configuração, você deve garantir a conectividade de rede dos nós de gateway para o ponto de extremidade de Serviços de Federação do Active Directory (AD FS) (AD FS) para garantir uma linha de visão direta dos nós de gateway.

O diagrama a seguir mostra o fluxo de autenticação básica para usuários federados. Primeiro, o gateway tenta concluir a autenticação usando o [fluxo ROPC](../../active-directory/develop/v2-oauth-ropc.md). Caso não haja hashes de senha sincronizados com o Azure AD, ele voltará a descobrir o ponto de extremidade AD FS e concluirá a autenticação acessando o ponto de extremidade AD FS.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="Diagrama que mostra a arquitetura com autenticação básica.":::


## <a name="enable-hdinsight-id-broker"></a>Habilitar agente de ID do HDInsight

Para criar um cluster de Enterprise Security Package com o agente de ID do HDInsight habilitado:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Siga as etapas de criação básicas para um cluster Enterprise Security Package. Para obter mais informações, consulte [criar um cluster HDInsight com Enterprise Security Package](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Selecione **habilitar agente de ID do HDInsight**.

O recurso agente de ID do HDInsight adiciona uma VM extra ao cluster. Essa VM é o nó agente de ID do HDInsight e inclui componentes de servidor para dar suporte à autenticação. O nó do agente de ID do HDInsight está ingressado no domínio do Azure AD DS domínio.

![Diagrama que mostra a opção para habilitar o agente de ID do HDInsight.](./media/identity-broker/identity-broker-enable.png)

### <a name="use-azure-resource-manager-templates"></a>Usar modelos do Gerenciador de Recursos do Azure

Se você adicionar uma nova função chamada `idbrokernode` com os seguintes atributos ao perfil de computação do seu modelo, o cluster será criado com o nó agente de ID do HDInsight habilitado:

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 2,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

Para ver um exemplo completo de um modelo do ARM, consulte o modelo publicado [aqui](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).


## <a name="tool-integration"></a>Integração de ferramentas

As ferramentas do HDInsight são atualizadas para dar suporte nativo ao OAuth. Use essas ferramentas para acesso baseado em OAuth moderno aos clusters. O [plug-in](../spark/apache-spark-intellij-tool-plugin.md#integrate-with-hdinsight-identity-broker-hib) do HDInsight IntelliJ pode ser usado para aplicativos baseados em Java, como escalabilidade. As [Ferramentas do Spark e do hive para Visual Studio Code](../hdinsight-for-vscode.md) podem ser usadas para trabalhos do PySpark e do hive. As ferramentas dão suporte a trabalhos de lote e interativos.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Acesso SSH sem um hash de senha no Azure AD DS

|Opções de SSH |Fatores a serem considerados |
|---|---|
| Conta VM local (por exemplo, sshuser) | Você forneceu essa conta no momento da criação do cluster. Não há nenhuma autenticação Kerberos para esta conta. |
| Conta somente em nuvem (por exemplo, alice@contoso.onmicrosoft.com ) | O hash de senha está disponível no Azure AD DS. A autenticação Kerberos é possível por meio do Kerberos SSH. |
| Conta local (por exemplo, alice@contoso.com ) | A autenticação Kerberos SSH só será possível se um hash de senha estiver disponível no Azure AD DS. Caso contrário, esse usuário não poderá SSH para o cluster. |

Para realizar o SSH em uma VM ingressada no domínio ou para executar o `kinit` comando, você deve fornecer uma senha. A autenticação Kerberos do SSH requer que o hash esteja disponível no Azure AD DS. Se você quiser usar o SSH somente para cenários administrativos, poderá criar uma conta somente em nuvem e usá-la para o SSH para o cluster. Outros usuários locais ainda podem usar as ferramentas Ambari ou HDInsight ou HTTP Basic auth sem ter o hash de senha disponível no Azure AD DS.

Se sua organização não estiver sincronizando hashes de senha para o Azure AD DS, como prática recomendada, crie um usuário somente em nuvem no Azure AD. Em seguida, atribua-o como um administrador de cluster ao criar o cluster e use-o para fins de administração. Você pode usá-lo para obter acesso de raiz às VMs via SSH.

Para solucionar problemas de autenticação, consulte [este guia](./domain-joined-authentication-issues.md).

## <a name="clients-using-oauth-to-connect-to-an-hdinsight-gateway-with-hdinsight-id-broker"></a>Clientes que usam o OAuth para se conectar a um gateway do HDInsight com o agente de ID do HDInsight

Na instalação do agente de ID do HDInsight, os aplicativos personalizados e clientes que se conectam ao gateway podem ser atualizados para adquirir o token OAuth necessário primeiro. Siga as etapas neste [documento](../../storage/common/storage-auth-aad-app.md) para adquirir o token com as seguintes informações:

*    URI do recurso OAuth: `https://hib.azurehdinsight.net` 
*   AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*    Permissão: (nome: cluster. ReadWrite, ID: 8f89faa0-ffef-4007-974d-4989b39ad77d)

Depois de adquirir o token OAuth, use-o no cabeçalho Authorization da solicitação HTTP para o gateway de cluster (por exemplo, https:// <clustername> -int.azurehdinsight.net). Um comando de ondulação de exemplo para a API do Apache Livy pode ser semelhante a este exemplo:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

Para usar beeline e Livy, você também pode seguir os códigos de exemplos fornecidos [aqui](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/HIB/HIBSamples) para configurar seu cliente para usar o OAuth e conectar-se ao cluster.

## <a name="faq"></a>Perguntas frequentes
### <a name="what-app-is-created-by-hdinsight-in-aad"></a>Qual aplicativo é criado pelo HDInsight no AAD?
Para cada cluster, um aplicativo de terceiros será registrado no AAD com o URI do cluster como o identifierUri (como `https://clustername.azurehdinsight.net` ).

### <a name="why-are-users-prompted-for-consent-before-using-hib-enabled-clusters"></a>Por que os usuários são solicitados a fornecer consentimento antes de usar os clusters habilitados para HIB?
No AAD, o consentimento é necessário para todos os aplicativos de terceiros antes de poder autenticar usuários ou acessar dados.

### <a name="can-the-consent-be-approved-programatically"></a>O consentimento pode ser aprovado de forma programática?
Microsoft Graph API permite automatizar o consentimento, consulte a documentação da [API](/graph/api/resources/oauth2permissiongrant) a sequência para automatizar o consentimento é:

* Registre um aplicativo e conceda ao Application. ReadWrite. todas as permissões para o aplicativo para acessar Microsoft Graph
* Depois que um cluster é criado, consulte o aplicativo de cluster com base no URI do identificador
* Registrar consentimento para o aplicativo

Quando o cluster é excluído, o HDInsight exclui o aplicativo e não há necessidade de limpar nenhum consentimento.

 


## <a name="next-steps"></a>Próximas etapas

* [Configurar um cluster HDInsight com Enterprise Security Package usando Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Sincronizar usuários do Azure Active Directory para um cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorar o desempenho do cluster](../hdinsight-key-scenarios-to-monitor.md)