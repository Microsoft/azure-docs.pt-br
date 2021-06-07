---
title: Configurar clusters para integração de Azure Active Directory
titleSuffix: Azure HDInsight
description: Saiba como configurar e configurar um cluster HDInsight integrado com o Active Directory usando Azure Active Directory Domain Services e o recurso Enterprise Security Package.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seodec18,seoapr2020, contperf-fy21q2
ms.date: 10/30/2020
ms.openlocfilehash: 6f478b97464cd47e9d0e04bfe83bd48a2b3bfe7c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867093"
---
# <a name="configure-hdinsight-clusters-for-azure-active-directory-integration-with-enterprise-security-package"></a>Configurar clusters HDInsight para integração de Azure Active Directory com o Enterprise Security Package

Este artigo fornece um resumo e uma visão geral do processo de criação e configuração de um cluster HDInsight integrado com o Azure Active Directory. Essa integração depende de um recurso do HDInsight chamado Enterprise Security Package (ESP), Azure Active Directory Domain Services (Azure AD-DS) e seu Active Directory local já existente.

Para obter um tutorial detalhado passo a passo sobre como configurar e configurar um domínio no Azure e criar um cluster habilitado para ESP e, em seguida, sincronizar usuários locais, consulte [criar e configurar clusters de Enterprise Security Package no Azure HDInsight](apache-domain-joined-create-configure-enterprise-security-cluster.md).

## <a name="background"></a>Tela de fundo

O Enterprise Security Package (ESP) fornece integração de Active Directory para o Azure HDInsight. Essa integração permite que os usuários de domínio usem suas credenciais de domínio para autenticar com clusters HDInsight e executar trabalhos de Big Data.

> [!NOTE]  
> O ESP está geralmente disponível no HDInsight 3,6 e 4,0 para esses tipos de cluster: Apache Spark, Interactive, Hadoop e HBase. O ESP para o tipo de cluster Apache Kafka está em versão prévia com suporte de melhor esforço. Os clusters ESP criados antes da data de GA do ESP (1 de outubro de 2018) não têm suporte.

## <a name="prerequisites"></a>Pré-requisitos

Há alguns pré-requisitos a serem concluídos para que você possa criar um cluster HDInsight habilitado para ESP:

- Um Active Directory e Azure Active Directory locais existentes.
- Habilite o Azure AD-DS.
- Verifique o status de integridade do Azure AD-DS para garantir que a sincronização foi concluída.
- Criar e autorizar uma identidade gerenciada.
- Conclua a configuração de rede para DNS e problemas relacionados.

Cada um desses itens será discutido em detalhes abaixo. Para obter uma explicação sobre como concluir todas essas etapas, consulte [criar e configurar clusters Enterprise Security Package no Azure HDInsight](apache-domain-joined-create-configure-enterprise-security-cluster.md).

### <a name="enable-azure-ad-ds"></a>Habilitar o Azure AD DS

Habilitar o Azure AD DS é um pré-requisito para que você possa criar um cluster HDInsight com o ESP. Para obter mais informações, consulte [habilitar Azure Active Directory Domain Services usando o portal do Azure](../../active-directory-domain-services/tutorial-create-instance.md).

Quando o AD DS do Azure é habilitado, todos os usuários e objetos iniciam a sincronização do Azure Active Directory (Azure AD) para o Azure AD DS por padrão. A duração da operação de sincronização depende do número de objetos no Azure AD. A sincronização pode levar alguns dias para centenas de milhares de objetos.

O nome de domínio que você usa com o Azure AD DS deve ter 39 caracteres ou menos, para trabalhar com o HDInsight.

Você pode optar por sincronizar apenas os grupos que precisam de acesso aos clusters HDInsight. Essa opção de sincronizar apenas determinados grupos é chamada de *sincronização com escopo*. Para obter instruções, consulte [Configurar a sincronização com escopo do Azure ad para seu domínio gerenciado](../../active-directory-domain-services/scoped-synchronization.md).

Quando você estiver habilitando o LDAP seguro, coloque o nome de domínio no nome da entidade. E o nome alternativo da entidade no certificado. Se o nome de domínio for *contoso100.onmicrosoft.com*, verifique se o nome exato existe no nome da entidade do certificado e no nome alternativo da entidade. Para obter mais informações, consulte [Configurar LDAP seguro para um domínio gerenciado do Azure AD DS](../../active-directory-domain-services/tutorial-configure-ldaps.md).

O exemplo a seguir cria um certificado autoassinado. O nome de domínio *contoso100.onmicrosoft.com* está em ambos `Subject` (nome da entidade) e `DnsName` (nome alternativo da entidade).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

> [!NOTE]  
> Somente administradores de locatários têm privilégios para habilitar o Azure AD DS. Se o armazenamento de cluster for Azure Data Lake Storage Gen1 ou Gen2, você deverá desabilitar a autenticação multifator do Azure AD somente para usuários que precisarão acessar o cluster usando a autenticação Kerberos básica.
>
> Você pode usar [IPs confiáveis](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) ou [acesso condicional](../../active-directory/conditional-access/overview.md) para desabilitar a autenticação multifator para usuários específicos *somente* quando eles estiverem acessando o intervalo de IP para a rede virtual do cluster HDInsight. Se você estiver usando o acesso condicional, certifique-se de que o ponto de extremidade de serviço do Active Directory em habilitado na rede virtual do HDInsight.
>
> Se o armazenamento de cluster for o armazenamento de BLOBs do Azure, não desabilite a autenticação multifator.

### <a name="check-azure-ad-ds-health-status"></a>Verificar o status de integridade do Azure AD DS

Exiba o status de integridade de Azure Active Directory Domain Services selecionando **integridade** na categoria **gerenciar** . Verifique se o status da AD DS do Azure está verde (em execução) e se a sincronização está concluída.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png" alt-text="Integridade de AD DS do Azure" border="true":::

### <a name="create-and-authorize-a-managed-identity"></a>Criar e autorizar uma identidade gerenciada

Use uma *identidade gerenciada atribuída pelo usuário* para simplificar as operações de serviços de domínio seguro. Quando você atribui a função **colaborador de serviços de domínio do HDInsight** à identidade gerenciada, ela pode ler, criar, modificar e excluir operações de serviços de domínio.

Determinadas operações de serviços de domínio, como a criação de UOs e entidades de serviço, são necessárias para o Enterprise Security Package do HDInsight. Você pode criar identidades gerenciadas em qualquer assinatura. Para obter mais informações sobre identidades gerenciadas em geral, consulte [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md). Para obter mais informações sobre como as identidades gerenciadas funcionam no Azure HDInsight, consulte [identidades gerenciadas no Azure hdinsight](../hdinsight-managed-identities.md).

Para configurar clusters ESP, crie uma identidade gerenciada atribuída pelo usuário se você ainda não tiver uma. Confira [`Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal`](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

Em seguida, atribua a função **colaborador dos serviços de domínio do HDInsight** à identidade gerenciada no **controle de acesso** para AD DS do Azure. Você precisa de privilégios de administrador de AD DS do Azure para fazer essa atribuição de função.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png" alt-text="Controle de acesso do Azure Active Directory Domain Services" border="true":::

A atribuição da função **colaborador dos serviços de domínio do HDInsight** garante que essa identidade tenha `on behalf of` acesso adequado () às operações de serviços de domínio no domínio AD DS do Azure. Essas operações incluem a criação e a exclusão de UOs.

Depois que a identidade gerenciada recebe a função, o administrador de AD DS do Azure gerencia quem a utiliza. Primeiro, o administrador seleciona a identidade gerenciada no Portal. Em seguida, seleciona o **controle de acesso (iam)** em **visão geral**. O administrador atribui a função de **operador de identidade gerenciada** a usuários ou grupos que desejam criar clusters ESP.

Por exemplo, o administrador de AD DS do Azure pode atribuir essa função ao grupo **MarketingTeam** para a identidade gerenciada **sjmsi** . Um exemplo é mostrado na imagem a seguir. Essa atribuição garante que as pessoas certas na organização possam usar a identidade gerenciada para criar clusters ESP.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png" alt-text="Atribuição da Função de Operador de Identidade Gerenciada do HDInsight" border="true":::

### <a name="network-configuration"></a>Configuração de rede

> [!NOTE]  
> O Azure AD DS deve ser implantado em uma rede virtual baseada em Azure Resource Manager. Não há suporte para redes virtuais clássicas no Azure AD DS. Para obter mais informações, consulte [habilitar Azure Active Directory Domain Services usando o portal do Azure](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network).

Habilitar o Azure AD DS. Em seguida, um servidor DNS (sistema de nomes de domínio) local é executado no Active Directory VMs (máquinas virtuais). Configure sua rede virtual do Azure AD DS para usar esses servidores DNS personalizados. Para localizar os endereços IP corretos, selecione **Propriedades** na categoria **gerenciar** e procure em **endereço IP na rede virtual**.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png" alt-text="Localizar endereços IP para servidores DNS locais" border="true":::

Altere a configuração dos servidores DNS na rede virtual AD DS do Azure. Para usar esses IPs personalizados, selecione **servidores DNS** na categoria **configurações** . Em seguida, selecione a opção **personalizado** , insira o primeiro endereço IP na caixa de texto e selecione **salvar**. Adicione mais endereços IP usando as mesmas etapas.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png" alt-text="Atualizando a configuração de DNS da rede virtual" border="true":::

É mais fácil colocar a instância do Azure AD DS e o cluster HDInsight na mesma rede virtual do Azure. Se você planeja usar redes virtuais diferentes, você deve emparelhar essas redes virtuais para que o controlador de domínio fique visível para VMs do HDInsight. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure](../../virtual-network/virtual-network-peering-overview.md).

Depois que as redes virtuais estiverem emparelhadas, configure a rede virtual do HDInsight para usar um servidor DNS personalizado. E insira os IPs privados do Azure AD DS como os endereços do servidor DNS. Quando ambas as redes virtuais usam os mesmos servidores DNS, seu nome de domínio personalizado será resolvido para o IP correto e poderá ser acessado do HDInsight. Por exemplo, se o nome de domínio for `contoso.com` , após essa etapa, `ping contoso.com` o deverá ser resolvido para o IP de AD DS correto do Azure.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png" alt-text="Configurando servidores DNS personalizados para uma rede virtual emparelhada" border="true":::

Se você estiver usando regras de NSG (grupo de segurança de rede) em sua sub-rede do HDInsight, deverá permitir os [IPS necessários](../hdinsight-management-ip-addresses.md) para o tráfego de entrada e de saída.

Para testar a configuração de rede, ingresse uma VM do Windows na rede virtual/sub-rede do HDInsight e execute o ping no nome de domínio. (Ele deve ser resolvido para um IP.) Execute **ldp.exe** para acessar o domínio de AD DS do Azure. Em seguida, ingresse essa VM do Windows no domínio para confirmar que todas as chamadas RPC necessárias são bem sucedidos entre o cliente e o servidor.

Use **nslookup** para confirmar o acesso à rede para sua conta de armazenamento. Ou qualquer banco de dados externo que você possa usar (por exemplo, metastore do Hive externo ou o Ranger DB). Verifique se as [portas necessárias](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) são permitidas nas regras NSG da sub-rede do Azure AD DS, se um NSG proteger o AD DS do Azure. Se o domínio que ingressar nessa VM do Windows for bem-sucedido, você poderá continuar na próxima etapa e criar clusters ESP.

## <a name="create-an-hdinsight-cluster-with-esp"></a>Criar um cluster HDInsight com ESP

Depois de configurar as etapas anteriores corretamente, a próxima etapa é criar o cluster HDInsight com o ESP habilitado. Ao criar um cluster HDInsight, você pode habilitar Enterprise Security Package na guia **segurança + rede** . Para um modelo de Azure Resource Manager para implantação, use a experiência do portal uma vez. Em seguida, baixe o modelo preenchedo na página **revisar + criar** para reutilização futura.

Você também pode habilitar o recurso [agente de ID do HDInsight](identity-broker.md) durante a criação do cluster. O recurso de agente de ID permite que você entre no Ambari usando a autenticação multifator e obtenha os tíquetes Kerberos necessários sem precisar de hashes de senha no Azure AD DS.

> [!NOTE]  
> Os primeiros seis caracteres dos nomes de cluster do ESP devem ser exclusivos em seu ambiente. Por exemplo, se você tiver vários clusters ESP em redes virtuais diferentes, escolha uma Convenção de nomenclatura que garanta que os primeiros seis caracteres nos nomes de cluster sejam exclusivos.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png" alt-text="Validação de domínio para o Azure HDInsight Enterprise Security Package" border="true":::

Depois de habilitar o ESP, as configurações incorretas comuns relacionadas à AD DS do Azure são detectadas e validadas automaticamente. Depois de corrigir esses erros, você pode continuar com a próxima etapa.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png" alt-text="Falha na validação de domínio do Azure HDInsight Enterprise Security Package" border="true":::

Ao criar um cluster HDInsight com o ESP, você deve fornecer os seguintes parâmetros:

* **Usuário administrador do cluster**: escolha um administrador para o cluster da instância do Azure AD DS sincronizada. Essa conta de domínio já deve estar sincronizada e disponível no Azure AD DS.

* **Grupos de acesso de cluster**: os grupos de segurança cujos usuários você deseja sincronizar e que têm acesso ao cluster devem estar disponíveis no Azure AD DS. Um exemplo é o grupo HiveUsers. Para obter mais informações, consulte [Criar um grupo e adicionar membros no Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* **URL de LDAPS**: um exemplo é `ldaps://contoso.com:636` .

A identidade gerenciada que você criou pode ser escolhida na lista suspensa **identidade gerenciada atribuída pelo usuário** quando você está criando um novo cluster.

O :::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png" alt-text="Azure HDINSIGHT ESP Active Directory Domain Services identidade gerenciada" border="true":::.

## <a name="next-steps"></a>Próximas etapas

* Para configurar as políticas do Hive e executar as consultas do Apache Hive, consulte [Configurar políticas do Apache Hive para clusters HDInsight com ESP](apache-domain-joined-run-hive.md).
* Para usar SSH para conectar clusters HDInsight com ESP, consulte [Usar SSH com Apache Hadoop baseado em Linux no HDInsight do Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
