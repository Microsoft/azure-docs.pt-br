---
title: Configurar o Azure Active Directory para autenticação de cliente
description: Saiba como configurar o Azure AD (Azure Active Directory) para autenticar clientes para clusters do Service Fabric.
ms.topic: conceptual
ms.date: 6/28/2019
ms.openlocfilehash: a1f89e144f9cef12c5bff87befb00a88bad8d7d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102215962"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurar o Azure Active Directory para autenticação de cliente

Para clusters em execução no Azure, o Azure AD (Azure Active Directory) é recomendado para proteger o acesso a pontos de extremidade de gerenciamento. Este artigo descreve como configurar o Azure AD para autenticar clientes para um Cluster Service Fabric.

Neste artigo, o termo "aplicativo" será usado para fazer referência a [aplicativos Azure Active Directory](../active-directory/develop/developer-glossary.md#client-application), não Service Fabric aplicativos; a distinção será feita quando necessário. O Azure AD permite que as organizações (conhecidas como locatários) gerenciem o acesso dos usuários aos aplicativos.

Um cluster do Service Fabric oferece vários pontos de entrada para a funcionalidade de gerenciamento, incluindo o [Service Fabric Explorer][service-fabric-visualizing-your-cluster] e o [Visual Studio][service-fabric-manage-application-in-visual-studio] baseados na Web. Como resultado, você criará dois aplicativos do Azure AD para controlar o acesso ao cluster: um aplicativo Web e um aplicativo nativo. Depois que os aplicativos forem criados, você atribuirá usuários a funções de administrador e somente leitura.

> [!NOTE]
> No Linux, você deve concluir as etapas a seguir antes de criar o cluster. No Windows, você também tem a opção de [Configurar a autenticação do Azure ad para um cluster existente](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/Configure%20Azure%20Active%20Directory%20Authentication%20for%20Existing%20Cluster.md).

> [!NOTE]
> É um [problema conhecido](https://github.com/microsoft/service-fabric/issues/399) que aplicativos e nós em clusters habilitados para AAD do Linux não podem ser exibidos no portal do Azure.



## <a name="prerequisites"></a>Pré-requisitos
Neste artigo, partimos do pressuposto que você já tenha criado um locatário. Se não for o caso, comece lendo [Como obter um locatário do Azure Active Directory][active-directory-howto-tenant].

Para simplificar algumas das etapas envolvidas na configuração do Azure AD com um cluster do Service Fabric, criamos um conjunto de scripts do Windows PowerShell.

1. [Clone o repositório](https://github.com/Azure-Samples/service-fabric-aad-helpers) em seu computador.
2. [Verifique se você tem todos os pré-requisitos](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) para os scripts instalados.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Criar aplicativos do Azure AD e atribuir usuários a funções

Usaremos os scripts para criar dois aplicativos do Azure AD para controlar o acesso ao cluster: um aplicativo Web e um aplicativo nativo. Depois de criar aplicativos para representar o cluster, você criará usuários para as [funções com suporte pelo Service Fabric](service-fabric-cluster-security-roles.md): somente leitura e administrador.

Execute `SetupApplications.ps1` e forneça a ID de locatário, o nome do cluster e a URL de resposta do aplicativo Web como parâmetros.  Especifique também nomes de usuário e senhas para os usuários. Por exemplo:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Para nuvens nacionais (por exemplo, Azure Governamental, Azure China e Azure Alemanha), você também deve especificar o parâmetro `-Location`.

Você pode encontrar o *TenantId* executando o comando `Get-AzureSubscription` do PowerShell. A execução desse comando exibe a TenantId para cada assinatura.

O *ClusterName* é usado para prefixar os aplicativos do Azure AD criados pelo script. Ele não precisa corresponder exatamente ao nome real do cluster. Serve apenas para tornar mais fácil mapear artefatos do Azure AD para o cluster do Service Fabric com que estão sendo usados.

*WebApplicationReplyUrl* é o ponto de extremidade padrão retornado pelo Azure AD para os usuários depois que eles concluem a entrada. Defina esse ponto de extremidade como o ponto de extremidade Service Fabric Explorer para o cluster. Se você estiver criando aplicativos do Azure AD para representar um cluster existente, verifique se essa URL corresponde ao ponto de extremidade do cluster existente. Se você estiver criando aplicativos para um novo cluster, planeje o ponto de extremidade que o seu cluster terá e certifique-se de não usar o ponto de extremidade de um cluster existente. Por padrão, o ponto de extremidade Service Fabric Explorer é:

https://&lt;cluster_domain&gt;:19080/Explorer

Você precisará entrar em uma conta que tenha privilégios administrativos para o locatário do Azure AD. Depois de entrar, o script criará aplicativos Web e nativos para representar seu cluster do Service Fabric. Se você observar os aplicativos do locatário no [Portal do Azure][azure-portal], você verá duas novas entradas:

   * *ClusterName*\_Cluster
   * *ClusterName*\_Cliente

O script imprime o JSON exigido pelo modelo de Azure Resource Manager quando você [cria o cluster habilitado do AAD](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access), portanto, é uma boa ideia manter a janela do PowerShell aberta.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Ajuda da solução de problemas na configuração do Azure Active Directory
Configurar e usar o Azure AD - Microsoft Azure Active Directory, pode ser um desafio, portanto, aqui estão algumas dicas sobre o que você pode fazer para depurar o problema.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>O Service Fabric Explorer solicita que você selecione um certificado
#### <a name="problem"></a>Problema
Depois de entrar no Azure AD no Service Fabric Explorer, o navegador retornará para a home page, mas uma mensagem solicitará que você selecione um certificado.

![Caixa de diálogo do certificado SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Motivo
O usuário não recebeu uma função no aplicativo de cluster do Azure AD. Assim, a autenticação do Azure AD falha no cluster do Service Fabric. O Service Fabric Explorer reverterá para autenticação de certificado.

#### <a name="solution"></a>Solução
Siga as instruções para configurar o Azure AD e atribuir funções de usuário. Além disso, recomendamos que você habilite a "Atribuição de usuário necessária para acessar o aplicativo" da mesma forma feita por `SetupApplications.ps1`.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>A conexão com o PowerShell falha e exibe o seguinte erro: “as credenciais especificadas são inválidas”
#### <a name="problem"></a>Problema
Quando você usa o PowerShell para se conectar ao cluster usando o modo de segurança "AzureActiveDirectory", ao entrar no AD do Azure, a conexão falhará com um erro: "as credenciais especificadas são inválidas."

#### <a name="solution"></a>Solução
Essa solução é igual à anterior.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>O Service Fabric Explorer retorna uma falha quando você entra: "AADSTS50011"
#### <a name="problem"></a>Problema
Ao tentar entrar no Azure AD no Service Fabric Explorer, a página retorna uma falha: “AADSTS50011: a &lt;URL&gt; do endereço de resposta não coincide com os endereços de resposta configurados para o aplicativo: &lt;GUID&gt;”.

![O endereço de resposta SFX não corresponde][sfx-reply-address-not-match]

#### <a name="reason"></a>Motivo
O aplicativo do cluster (Web) que representa o Service Fabric Explorer tenta se autenticar no Azure AD como parte da solicitação que ele fornece à URL de retorno de redirecionamento. Mas a URL não está listada na lista **URL DE RESPOSTA** do aplicativo Azure AD.

#### <a name="solution"></a>Solução
Na página de registro do aplicativo do Azure AD para seu cluster, selecione **autenticação** e, na seção **URIs de redirecionamento** , adicione a URL de Service Fabric Explorer à lista. Salve sua alteração.

![URL de resposta do aplicativo Web][web-application-reply-url]

### <a name="connecting-to-the-cluster-using-azure-ad-authentication-via-powershell-gives-an-error-when-you-sign-in-aadsts50011"></a>Conectar-se ao cluster usando a autenticação do Azure AD por meio do PowerShell fornece um erro quando você entra: "AADSTS50011"
#### <a name="problem"></a>Problema
Quando você tenta se conectar a um Cluster Service Fabric usando o Azure AD por meio do PowerShell, a página de entrada retorna uma falha: "AADSTS50011: a URL de resposta especificada na solicitação não corresponde às URLs de resposta configuradas para o aplicativo: &lt; GUID &gt; ".

#### <a name="reason"></a>Motivo
Semelhante ao problema anterior, o PowerShell tenta se autenticar no Azure AD, que fornece uma URL de redirecionamento que não está listada na lista de **URLs de resposta** do aplicativo do Azure AD.  

#### <a name="solution"></a>Solução
Use o mesmo processo que o problema anterior, mas a URL deve ser definida como `urn:ietf:wg:oauth:2.0:oob` um redirecionamento especial para autenticação de linha de comando.

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Conectar o cluster usando a autenticação do Azure AD por meio do PowerShell
Para se conectar ao cluster do Service Fabric, use o seguinte exemplo de comando do PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Para saber mais, consulte o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Posso reutilizar o mesmo locatário do Azure AD em vários clusters?
Sim. Mas lembre-se de adicionar a URL do Service Fabric Explorer ao aplicativo do cluster (Web). Caso contrário, o Service Fabric Explorer não funcionará.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Por que eu ainda preciso de um certificado de servidor quando o Azure AD está habilitado?
FabricClient e FabricGateway realizam autenticação mútua. Durante a autenticação do Azure AD, a integração do AD do Azure fornece uma identidade de cliente para o servidor e o certificado do servidor é usado pelo cliente para verificar a identidade do servidor. Para saber mais sobre certificados do Service Fabric, confira [Certificados X.509 e Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Próximas etapas
Depois de configurar os aplicativos do Azure Active Directory e definir as funções para os usuários, [configure e implante um cluster](service-fabric-cluster-creation-via-arm.md).


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
