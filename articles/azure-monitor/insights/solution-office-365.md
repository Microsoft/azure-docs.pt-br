---
title: Solução de gerenciamento do Office 365 no Microsoft Azure | Microsoft Docs
description: Este artigo apresenta detalhes sobre a configuração e o uso da solução Office 365 no Microsoft Azure.  Ele inclui uma descrição detalhada dos registros do Office 365 criados no Azure Monitor.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/01/2019
ms.author: bwren
ms.openlocfilehash: 3f4b0ad8b7aad01472a76db67f2c07e03e978e41
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673054"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Solução de gerenciamento do Office 365 no Microsoft Azure | (Versão prévia)

![Logotipo do Office 365](media/solution-office-365/icon.png)


> [!NOTE]
> O método recomendado para instalar e configurar a solução do Office 365 é a habilitação de [conector do Office 365](../../sentinel/connect-office-365.md) na [Azure Sentinel](../../sentinel/overview.md) em vez de usar as etapas neste artigo. Isso é uma versão atualizada da solução do Office 365 com uma experiência aprimorada de configuração. Para conectar os logs do Azure AD, você pode usar o [Sentinela do Azure do Azure AD connector](../../sentinel/connect-azure-active-directory.md) ou [definir configurações de diagnóstico do Azure AD](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), que fornece dados de log avançados que os logs de gerenciamento do Office 365. 
>
> Quando você [Sentinel integrados do Azure](../../sentinel/quickstart-onboard.md), especifique o espaço de trabalho do Log Analytics que você deseja que a solução do Office 365 instalada no. Depois de habilitar o conector, a solução estará disponível no espaço de trabalho e usado exatamente como outras soluções de monitoramento que você instalou.
>
> Os usuários da nuvem do Azure governamental devem instalar o Office 365 usando as etapas neste artigo, pois o Azure Sentinel ainda não está disponível na nuvem do governo.

A solução de gerenciamento do Office 365 permite que você monitore o ambiente do Office 365 no Azure Monitor.

- Monitore atividades do usuário em suas contas do Office 365 para analisar padrões de uso, bem como identificar tendências de comportamentos. Por exemplo, você pode extrair os cenários de uso específicos, como arquivos que são compartilhados fora da sua organização ou os sites do SharePoint mais populares.
- Monitore atividades do administrador para controlar as alterações de configuração ou operações com privilégios elevados.
- Detecte e investigue comportamento indesejado do usuário, o que pode ser personalizado para suas necessidades organizacionais.
- Demonstre auditoria e conformidade. Por exemplo, você pode monitorar as operações de acesso a arquivos em arquivos confidenciais, o que pode ajudá-lo com o processo de conformidade e auditoria.
- Execute a solução de problemas operacionais usando [consultas de log](../log-query/log-query-overview.md) com base nos dados da atividade do Office 365 de sua organização.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

É necessário o seguinte antes de essa solução ser instalada e configurada.

- Assinatura organizacional do Office 365.
- Credenciais para uma conta de usuário que seja um Administrador Global.
- Para receber dados de auditoria, você deve [configurar auditoria](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) na sua assinatura do Office 365.  Observe que a [auditoria de caixa de correio](https://technet.microsoft.com/library/dn879651.aspx) é configurada separadamente.  Você ainda poderá instalar a solução e coletar outros dados se a auditoria não estiver configurada.
 

## <a name="management-packs"></a>Pacotes de gerenciamento

Essa solução não instala nenhum pacote de gerenciamento nos [grupos de gerenciamento conectados](../platform/om-agents.md).
  

## <a name="install-and-configure"></a>Instalar e configurar

Comece adicionando a [solução do Office 365 à sua assinatura](solutions.md#install-a-monitoring-solution). Depois, execute as etapas de configuração desta seção para conceder acesso à sua assinatura do Office 365.

### <a name="required-information"></a>Informações necessárias

Antes de iniciar este procedimento, reúna as informações a seguir.

Do seu workspace do Log Analytics:

- Nome do workspace: O workspace de onde os dados do Office 365 serão coletados.
- Nome do grupo de recursos: O grupo de recursos que contém os workspace.
- ID de assinatura do Azure: A assinatura que contém os workspace.

De sua assinatura do Office 365:

- Nome de Usuário: O endereço de email de uma conta administrativa.
- ID do locatário: ID exclusiva da assinatura do Office 365.
- ID do cliente: Cadeia com 16 caracteres que representa o cliente do Office 365.
- Segredo do cliente: Cadeia de caracteres criptografada, necessária para autenticação.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Criar um aplicativo do Office 365 no Azure Active Directory

A primeira etapa é criar um aplicativo no Azure Active Directory que a solução de gerenciamento usará para acessar sua solução do Office 365.

1. Faça logon no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).
1. Selecione **Azure Active Directory** e depois **Registros de aplicativo**.
1. Clique em **Novo registro de aplicativo**.

    ![Adicionar registro do aplicativo](media/solution-office-365/add-app-registration.png)
1. Insira um **Nome** de aplicativo e **URL de Logon**.  O nome deve ser descritivo.  Use `http://localhost` para a URL e manter _aplicativo Web / API_ para o **tipo de aplicativo**
    
    ![Criar aplicativo](media/solution-office-365/create-application.png)
1. Clique em **Criar** e valide as informações do aplicativo.

    ![Aplicativo registrado](media/solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>Configurar o aplicativo para Office 365

1. Clique em **Configurações** para abrir o menu **Configurações**.
1. Selecione **Propriedades**. Altere **Multilocatário** para _Sim_.

    ![Configurações de multilocatário](media/solution-office-365/settings-multitenant.png)

1. Selecione **Permissões necessárias** no menu **Configurações** e clique em **Adicionar**.
1. Clique em **Selecionar uma API** e **APIs de Gerenciamento do Office 365**. Clique em **APIs de Gerenciamento do Office 365**. Clique em **Selecionar**.

    ![Selecionar API](media/solution-office-365/select-api.png)

1. Em **Selecionar permissões**, escolha as opções a seguir para **Permissões de aplicativo** e **Permissões delegadas**:
   - Ler informações de integridade do serviço de sua organização
   - Ler dados de atividade de sua organização
   - Ler relatórios de atividade de sua organização

     ![Selecionar API](media/solution-office-365/select-permissions.png)

1. Clique em **Selecionar** e em **Concluído**.
1. Clique em **Conceder permissões** e, quando receber uma solicitação de verificação, clique em **Sim**.

    ![Conceder permissões](media/solution-office-365/grant-permissions.png)

### <a name="add-a-key-for-the-application"></a>Adicionar uma chave ao aplicativo

1. Selecione **Chaves** no menu **Configurações**.
1. Digite uma **Descrição** e a **Duração** da nova chave.
1. Clique em **Salvar** e, depois, copie o **Valor** gerado.

    ![simétricas](media/solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Adicionar consentimento do administrador

Para habilitar a conta administrativa pela primeira vez, forneça consentimento administrativo para o aplicativo. Faça isso com um script do PowerShell. 

1. Salve o script a seguir como *office365_consent.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $WorkspaceLocation= $Workspace.Location
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
    switch ($WorkspaceLocation.Replace(" ","").ToLower()) {
           "eastus"   {$OfficeAppClientId="d7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westeurope"   {$OfficeAppClientId="c9005da2-023d-40f1-a17a-2b7d91af4ede"; break}
           "southeastasia"   {$OfficeAppClientId="09c5b521-648d-4e29-81ff-7f3a71b27270"; break}
           "australiasoutheast"  {$OfficeAppClientId="f553e464-612b-480f-adb9-14fd8b6cbff8"; break}   
           "westcentralus"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break}
           "japaneast"   {$OfficeAppClientId="b07d97d3-731b-4247-93d1-755b5dae91cb"; break}
           "uksouth"   {$OfficeAppClientId="f232cf9b-e7a9-4ebb-a143-be00850cd22a"; break}
           "centralindia"   {$OfficeAppClientId="ffbd6cf4-cba8-4bea-8b08-4fb5ee2a60bd"; break}
           "canadacentral"  {$OfficeAppClientId="c2d686db-f759-43c9-ade5-9d7aeec19455"; break}
           "eastus2"  {$OfficeAppClientId="7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westus2"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break} #Need to check
           "usgovvirginia" {$OfficeAppClientId="c8b41a87-f8c5-4d10-98a4-f8c11c3933fe"; 
                             $domain='login.microsoftonline.us'; break}
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345"
    }
    
    AdminConsent -ErrorAction Stop
    ```

2. Execute o script com o comando a seguir. Você deverá inserir suas credenciais duas vezes. Forneça as credenciais do espaço de trabalho do Log Analytics primeiro e, em seguida, as credenciais de administrador global do locatário do Office 365.

    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```

    Exemplo:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. Você verá uma janela semelhante à mostrada abaixo. Clique em **Aceitar**.
    
    ![Consentimento do administrador](media/solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>Assinar o espaço de trabalho do Log Analytics

A última etapa é assinar o aplicativo em seu workspace do Log Analytics. Faça isso também com um script do PowerShell.

1. Salve o script a seguir como *office365_subscription.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeUsername,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId,
        [Parameter(Mandatory=$True)][string]$OfficeClientId,
        [Parameter(Mandatory=$True)][string]$OfficeClientSecret
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    $OfficeClientSecret =[uri]::EscapeDataString($OfficeClientSecret)
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant = $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Failure {
    $line
    $formatstring = "{0} : {1}`n{2}`n" +
                    "    + CategoryInfo          : {3}`n" +
                    "    + FullyQualifiedErrorId : {4}`n"
    $fields = $_.InvocationInfo.MyCommand.Name,
              $_.ErrorDetails.Message,
              $_.InvocationInfo.PositionMessage,
              $_.CategoryInfo.ToString(),
              $_.FullyQualifiedErrorId
    
    $formatstring -f $fields
    $_.Exception.Response
    
    $line
    break
    }
    
    Function Connection-API
    {
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    
    $line
    $connectionAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/connections/office365connection_' + $SubscriptionId + $OfficeTennantId + '?api-version=2017-04-26-preview'
    $connectionAPIUrl
    $line
    
    $xms_client_tenant_Id ='1da8f770-27f4-4351-8cb3-43ee54f14759'
    
    $BodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'clientId': '" + $OfficeClientId + "',
                                    'clientSecret': '" + $OfficeClientSecret + "',
                                    'Username': '" + $OfficeUsername   + "',
                                    'Url': 'https://$($domain)/" + $OfficeTennantId + "/oauth2/token',
                                  },
                    'etag': '*',
                    'kind': 'Connection',
                    'solution': 'Connection',
                   }"
    
    $params = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id #Prod-'1da8f770-27f4-4351-8cb3-43ee54f14759'
        'Content-Type' = 'application/json'
        }
        Body = $BodyString
        Method = 'Put'
        URI = $connectionAPIUrl
    }
    $response = Invoke-WebRequest @params 
    $response
    $line
    
    }
    
    Function Office-Subscribe-Call{
    try{
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_' + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $OfficeBodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'office365TenantID': '" + $OfficeTennantId + "',
                                    'connectionID': 'office365connection_" + $SubscriptionId + $OfficeTennantId + "',
                                    'office365AdminUsername': '" + $OfficeUsername + "',
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory,Audit.SharePoint'
                                  },
                    'etag': '*',
                    'kind': 'Office365',
                    'solution': 'Office365',
                   }"
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Body = $OfficeBodyString
        Method = 'Put'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    }
    catch{ Failure }
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Connection-API -ErrorAction Stop
    Office-Subscribe-Call -ErrorAction Stop
    ```

2. Execute o script com o comando a seguir:

    ```
    .\office365_subscription.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeUsername <OfficeUsername> -OfficeTennantID <Tenant ID> -OfficeClientId <Client ID> -OfficeClientSecret <Client secret>
    ```

    Exemplo:

    ```powershell
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troubleshooting"></a>Solução de problemas

Você poderá ver o erro a seguir se o aplicativo já estiver inscrito nesse workspace ou se esse locatário estiver inscrito em outro workspace.

```Output
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Você poderá receber o seguinte erro se fornecer valores de parâmetro inválidos.

```Output
Select-AzSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzContextCommand

```

## <a name="uninstall"></a>Desinstalar

Remova a solução de gerenciamento do Office 365 usando o processo em [Remover uma solução de gerenciamento](solutions.md#remove-a-monitoring-solution). No entanto, isso não interromperá a coleta de dados do Office 365 pelo Azure Monitor. Siga o procedimento abaixo para cancelar a assinatura do Office 365 e parar de coletar dados.

1. Salve o script a seguir como *office365_unsubscribe.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Execute o script com o comando a seguir:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Exemplo:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

## <a name="data-collection"></a>Coleta de dados

### <a name="supported-agents"></a>Agentes com suporte

A solução do Office 365 não recupera dados de nenhum dos [agentes do Log Analytics](../platform/agent-data-sources.md).  Ela recupera dados diretamente do Office 365.

### <a name="collection-frequency"></a>Frequência de coleta

Talvez demore algumas horas para coletar os dados pela primeira vez. Após o início da coleta, o Office 365 envia uma [notificação webhook](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) com dados detalhados para o Azure Monitor sempre que um registro é criado. O registro fica disponível no Azure Monitor alguns minutos após o recebimento.

## <a name="using-the-solution"></a>Usando a solução

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Quando você adicionar a solução Office 365 ao espaço de trabalho do Log Analytics, o bloco **Office 365** será adicionado ao seu painel do OMS. Esse bloco exibe uma contagem e representação gráfica do número de computadores em seu ambiente e sua conformidade de atualização.<br><br>
![Bloco de Resumo do Office 365](media/solution-office-365/tile.png)  

Clique no bloco **Office 365** para abrir o painel **Office 365**.

![Painel do Office 365](media/solution-office-365/dashboard.png)  

O painel inclui as colunas na tabela a seguir. Cada coluna lista os dez principais alertas por contagem que correspondem aos critérios da coluna para o escopo e intervalo de tempo especificados. É possível executar uma pesquisa de log que fornece a lista inteira clicando em Ver todos na parte inferior da coluna ou clicando no cabeçalho de coluna.

| Coluna | DESCRIÇÃO |
|:--|:--|
| Operações | Fornece informações sobre os usuários ativos de todas as suas assinaturas do Office 365 monitoradas. Você também poderá ver o número de atividades que ocorrem ao longo do tempo.
| Exchange | Mostra a análise das atividades do Exchange Server, como a permissão Add-Mailbox ou Set-Mailbox. |
| SharePoint | Mostra as principais atividades que os usuários executam em documentos do SharePoint. Quando você faz drill down desse bloco, a página de pesquisa mostra os detalhes dessas atividades, como o documento de destino e o local dessa atividade. Por exemplo, para um evento Arquivo Acessado, você poderá ver o documento que está sendo acessado, o nome da sua conta associada e o endereço IP. |
| Azure Active Directory | Inclui as principais atividades do usuário, como Tentativas de Logon e de Redefinição de Senha do Usuário. Quando você fizer o drill down, poderá ver os detalhes dessas atividades como o Status do Resultado. Isso é mais útil se você desejar monitorar atividades suspeitas no Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Registros de log do Azure Monitor

Todos os registros criados no espaço de trabalho do Log Analytics no Azure Monitor pela solução do Office 365 têm um **Tipo** de **OfficeActivity**.  A propriedade **OfficeWorkload** determina a qual serviço Office 365 o registro se refere: Exchange, AzureActiveDirectory, SharePoint ou OneDrive.  A propriedade **RecordType** especifica o tipo de operação.  As propriedades variam para cada tipo de operação e são mostradas nas tabelas a seguir.

### <a name="common-properties"></a>Propriedades comuns

As propriedades a seguir são comuns a todos os registros do Office 365.

| Propriedade | Description |
|:--- |:--- |
| Tipo | *OfficeActivity* |
| ClientIP | O endereço IP do dispositivo que foi usado quando a atividade foi registrada. O endereço IP é exibido no formato de endereço IPv4 ou IPv6. |
| OfficeWorkload | Serviço Office 365 ao qual o registro se refere.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Operação | O nome da atividade do usuário ou administrador.  |
| OrganizationId | O GUID do locatário do Office 365 da sua organização. Esse valor sempre será o mesmo para a sua organização, independentemente do serviço do Office 365 em que ele ocorre. |
| RecordType | Tipo de operação executada. |
| ResultStatus | Indica se a ação (especificada na propriedade Operation) foi bem-sucedida ou não. Os valores possíveis são Succeeded, PartiallySucceeded ou Failed. Para a atividade de administração do Exchange, o valor é True ou False. |
| UserId | O nome UPN do usuário que executou a ação que resultou em o registro ser incluído em log. Por exemplo, my_name@my_domain_name. Observe que os registros para a atividade realizada por contas do sistema (como SHAREPOINT\system ou NTAUTHORITY\SYSTEM) também são incluídos. | 
| UserKey | Uma ID alternativa para o usuário identificado na propriedade UserId.  Por exemplo, essa propriedade é preenchida com a PUID (ID exclusiva do passport) para eventos executadas por usuários no SharePoint, no OneDrive for Business e no Exchange. Essa propriedade também pode especificar o mesmo valor que a propriedade UserID para eventos que ocorrem em outros serviços e eventos executados por contas do sistema|
| UserType | O tipo de usuário que realizou a operação.<br><br>Administrador<br>Aplicativo<br>DcAdmin<br>Regular<br>Reservado<br>ServicePrincipal<br>Sistema |


### <a name="azure-active-directory-base"></a>Base do Azure Active Directory

As propriedades a seguir são comuns a todos os registros do Azure Active Directory.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | O tipo de evento do Azure AD. |
| ExtendedProperties | As propriedades estendidas do evento do Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Logon na Conta do Azure Active Directory

Esses registros são criados quando um usuário do Active Directory tenta fazer logon.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| Aplicativo | O aplicativo que dispara o evento de logon de conta, como Office 15. |
| Cliente | Detalhes sobre o dispositivo cliente, o SO do dispositivo e o navegador do dispositivo que foi usado para o evento de logon na conta. |
| LoginStatus | Esta propriedade é diretamente de OrgIdLogon.LoginStatus. O mapeamento de várias falhas de logon interessantes pode ser feito por algoritmos de alerta. |
| UserDomain | As TII (informações de identidade de locatário). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Esses registros são criados quando adições ou alterações são feitas aos objetos do Azure Active Directory.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | O usuário em que a ação (identificada pela propriedade Operation) foi executada. |
| Ator | O usuário ou a entidade de serviço que executou a ação. |
| ActorContextId | O GUID da organização à qual pertence o ator. |
| ActorIpAddress | O endereço IP do ator no formato de endereço IPV4 ou IPV6. |
| InterSystemsId | O GUID que controla as ações entre componentes no serviço do Office 365. |
| IntraSystemId |   O GUID que é gerado pelo Azure Active Directory para controlar a ação. |
| SupportTicketId | A ID do tíquete de suporte ao cliente para a ação em situações "agir em nome de". |
| TargetContextId | O GUID da organização à qual o usuário de destino pertence. |


### <a name="data-center-security"></a>Segurança do Data Center

Esses registros são criados de dados de auditoria de Segurança do Data Center.  

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| EffectiveOrganization | O nome do locatário ao qual o cmdlet \elevation foi direcionado. |
| ElevationApprovedTime | O carimbo de data/hora de quando a elevação foi aprovada. |
| ElevationApprover | O nome de um gerente Microsoft. |
| ElevationDuration | A duração pela qual a elevação ficou ativa. |
| ElevationRequestId |  Um identificador exclusivo para a solicitação de elevação. |
| ElevationRole | A função para a qual a elevação foi solicitada. |
| ElevationTime | A hora de início da elevação. |
| Start_Time | A hora de início da execução do cmdlet. |


### <a name="exchange-admin"></a>Exchange Admin

Esses registros são criados quando são feitas alterações à configuração do Exchange.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Especifica se o cmdlet foi executado por um usuário em sua organização, por uma conta de serviço do datacenter ou pela equipe do datacenter da Microsoft ou por um administrador delegado. O valor False indica que o cmdlet foi executado por alguém de sua organização. O valor True indica que o cmdlet foi executado pela equipe do datacenter, uma conta de serviço do datacenter ou um administrador delegado. |
| ModifiedObjectResolvedName |  Esse é o nome amigável de usuário do objeto modificado pelo cmdlet. Isso é registrado apenas se o cmdlet modificar o objeto. |
| OrganizationName | O nome do locatário. |
| OriginatingServer | O nome do servidor do qual o cmdlet foi executado. |
| parâmetros | O nome e o valor para todos os parâmetros que foram usados com o cmdlet identificado na propriedade Operations. |


### <a name="exchange-mailbox"></a>Caixa de correio do Exchange

Esses registros são criados quando alterações ou adições são feitas às caixas de correio do Exchange.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Informações sobre o cliente de email que foi usado para executar a operação, como uma versão do navegador, versão do Outlook e informações do dispositivo móvel. |
| Client_IPAddress | O endereço IP do dispositivo que foi usado quando a operação foi registrada. O endereço IP é exibido no formato de endereço IPv4 ou IPv6. |
| ClientMachineName | O nome do computador que hospeda o cliente do Outlook. |
| ClientProcessName | O cliente de email usado para acessar a caixa de correio. |
| ClientVersion | A versão do cliente de email. |
| InternalLogonType | Reservado para uso interno. |
| Logon_Type | Indica o tipo de usuário que acessou a caixa de correio e executou a operação que foi registrada. |
| LogonUserDisplayName |    O nome amigável do usuário que realizou a operação. |
| LogonUserSid | O SID do usuário que realizou a operação. |
| MailboxGuid | O GUID do Exchange da caixa de correio que foi acessada. |
| MailboxOwnerMasterAccountSid | O SID da conta mestre da conta do proprietário da caixa de correio. |
| MailboxOwnerSid | O SID do proprietário da caixa de correio. |
| MailboxOwnerUPN | O endereço de email da pessoa que detém a caixa de correio acessada. |


### <a name="exchange-mailbox-audit"></a>Auditoria de Caixa de Correio do Exchange

Esses registros são criados quando é criada uma entrada de auditoria de caixa de correio.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| item | Representa o item no qual a operação foi executada | 
| SendAsUserMailboxGuid | O GUID do Exchange da caixa de correio acessada para enviar email como ela. |
| SendAsUserSmtp | Endereço SMTP do usuário que está sendo representado. |
| SendonBehalfOfUserMailboxGuid | O GUID do Exchange da caixa de correio acessada para enviar emails em nome dela. |
| SendOnBehalfOfUserSmtp | Endereço SMTP do usuário em cujo nome o email é enviado. |


### <a name="exchange-mailbox-audit-group"></a>Grupo de Auditoria da Caixa de Correio do Exchange

Esses registros são criados quando alterações ou adições são feitas a grupos do Exchange.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informações sobre cada item no grupo. |
| CrossMailboxOperations | Indica se a operação envolveu mais de uma caixa de correio. |
| DestMailboxId | Definido somente se o parâmetro CrossMailboxOperations for True. Especifica o GUID da caixa de correio de destino. |
| DestMailboxOwnerMasterAccountSid | Definido somente se o parâmetro CrossMailboxOperations for True. Especifica o SID para o SID da conta mestra do proprietário de caixa de correio de destino. |
| DestMailboxOwnerSid | Definido somente se o parâmetro CrossMailboxOperations for True. Especifica o SID da caixa de correio de destino. |
| DestMailboxOwnerUPN | Definido somente se o parâmetro CrossMailboxOperations for True. Especifica o UPN do proprietário da caixa de correio de destino. |
| DestFolder | A pasta de destino, para operações como Mover. |
| Pasta | A pasta na qual se encontra um grupo de itens. |
| Pastas |     Obter informações sobre as pastas de origem envolvidas em uma operação; por exemplo, se as pastas são selecionadas e então, excluídas. |


### <a name="sharepoint-base"></a>Base do SharePoint

Essas propriedades são comuns a todos os registros do SharePoint.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Identifica o que ocorreu um evento no SharePoint. Os valores possíveis são SharePoint ou ObjectModel. |
| ItemType | O tipo de objeto acessado ou modificado. Consulte a tabela ItemType para obter detalhes sobre os tipos de objetos. |
| MachineDomainInfo | Obter informações sobre operações de sincronização do dispositivo. Essas informações só serão relatadas se estiverem presentes na solicitação. |
| MachineId |   Obter informações sobre operações de sincronização do dispositivo. Essas informações só serão relatadas se estiverem presentes na solicitação. |
| Site_ | O GUID do site em que está localizado o arquivo ou a pasta acessado pelo usuário. |
| Source_Name | A entidade que disparou a operação auditada. Os valores possíveis são SharePoint ou ObjectModel. |
| UserAgent | Informações sobre o cliente ou o navegador do usuário. Essas informações são fornecidas pelo cliente ou pelo navegador. |


### <a name="sharepoint-schema"></a>Esquema do SharePoint

Esses registros são criados quando são feitas alterações de configuração do SharePoint.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Cadeia de caracteres opcional para eventos personalizados. |
| Event_Data |  Conteúdo opcional para eventos personalizados. |
| ModifiedProperties | A propriedade é incluída para os eventos de administrador, como adicionar um usuário como um membro de um site ou um grupo de administração do conjunto de sites. A propriedade inclui o nome da propriedade que foi modificada (por exemplo, o grupo de Administrador do Site), o novo valor da propriedade modificada (como o usuário que foi adicionado como administrador do site) e o valor anterior do objeto modificado. |


### <a name="sharepoint-file-operations"></a>Operações de arquivo do SharePoint

Esses registros são criados em resposta às operações de arquivo no SharePoint.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | A extensão de arquivo de um arquivo que é copiado ou movido. Essa propriedade é exibida apenas para eventos FileCopied e FileMoved. |
| DestinationFileName | O nome do arquivo que é copiado ou movido. Essa propriedade é exibida apenas para eventos FileCopied e FileMoved. |
| DestinationRelativeUrl | A URL da pasta de destino em que um arquivo é copiado ou movido. A combinação dos valores de parâmetros SiteURL, DestinationRelativeURL e DestinationFileName é igual ao valor da propriedade ObjectID, que é o nome de caminho completo para o arquivo copiado. Essa propriedade é exibida apenas para eventos FileCopied e FileMoved. |
| SharingType | O tipo de permissões de compartilhamento atribuídas ao usuário com o qual o recurso foi compartilhado. Esse usuário é identificado pelo parâmetro UserSharedWith. |
| Site_Url | A URL do site em que está localizado o arquivo ou a pasta acessado pelo usuário. |
| SourceFileExtension | A extensão de arquivo do arquivo que foi acessado pelo usuário. Essa propriedade ficará em branco se o objeto acessado for uma pasta. |
| SourceFileName |  O nome do arquivo ou pasta acessado pelo usuário. |
| SourceRelativeUrl | A URL da pasta que contém o arquivo acessado pelo usuário. A combinação de valores para os parâmetros SiteURL, SourceRelativeURL e SourceFileName é a mesma que o valor para a propriedade ObjectID, que é o nome de caminho completo do arquivo acessado pelo usuário. |
| UserSharedWith |  O usuário com o qual um recurso foi compartilhado. |




## <a name="sample-log-searches"></a>Pesquisas de log de exemplo

A tabela a seguir fornece pesquisas de log de exemplo para os registros de atualização coletados por essa solução.

| Consultar | DESCRIÇÃO |
| --- | --- |
|Contagem de todas as operações em sua assinatura do Office 365 |OfficeActivity &#124; summarize count() by Operation |
|Uso de sites do SharePoint|OfficeActivity &#124; onde OfficeWorkload = ~ "sharepoint" &#124; resumem Count () by SiteUrl \| classificar por contagem de asc|
|Operações de acesso de arquivos por tipo de usuário|search in (OfficeActivity) OfficeWorkload =~ "azureactivedirectory" and "MyTest"|
|Pesquisar com uma palavra-chave específica|Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"|
|Monitorar de ações externas no Exchange|OfficeActivity &#124; where OfficeWorkload =~ "exchange" and ExternalAccess == true|



## <a name="next-steps"></a>Próximas etapas

* Use [consultas de log no Azure Monitor](../log-query/log-query-overview.md) para exibir dados detalhados das atualizações.
* [Crie seus próprios painéis](../learn/tutorial-logs-dashboards.md) para exibir suas consultas de pesquisa favoritas do Office 365.
* [Crie alertas](../platform/alerts-overview.md) para ser notificado proativamente das atividades importantes do Office 365.  