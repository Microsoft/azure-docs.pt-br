---
title: Solução de gerenciamento do Office 365 no Azure
description: Este artigo apresenta detalhes sobre a configuração e o uso da solução Office 365 no Microsoft Azure.  Ele inclui uma descrição detalhada dos registros do Office 365 criados no Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2020
ms.openlocfilehash: bde1c503d0aaaff1afcee67a26245d5021c43bb4
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807743"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Solução de gerenciamento do Office 365 no Microsoft Azure | (Versão prévia)

![Logotipo do Office 365](media/solution-office-365/icon.png)

> [!IMPORTANT]
> ## <a name="solution-update"></a>Atualização da solução
> Esta solução foi substituída pela solução de disponibilidade geral do [Office 365](../../sentinel/connect-office-365.md) no [Azure Sentinel](../../sentinel/overview.md) e a [solução de monitoramento e relatório do Azure ad](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). Juntos, eles fornecem uma versão atualizada da solução anterior Azure Monitor Office 365 com uma experiência de configuração aprimorada. Você pode continuar a usar a solução existente até 31 de outubro de 2020.
> 
> O Azure Sentinel é uma solução de gerenciamento de eventos e informações de segurança nativa na nuvem que ingere logs e fornece uma funcionalidade adicional do SIEM, incluindo detecções, investigações, busca e insights orientados de aprendizado de máquina. Usar o Azure Sentinel agora fornecerá a ingestão de atividades do Office 365 SharePoint e logs de gerenciamento do Exchange.
> 
> Os relatórios do Azure AD fornecem uma exibição mais abrangente dos logs da atividade do Azure AD em seu ambiente, incluindo eventos de entrada, eventos de auditoria e alterações no diretório. Para conectar os logs do Azure AD, você pode usar o [Azure Azure ad Connector](../../sentinel/connect-azure-active-directory.md) ou configurar a [integração de logs do Azure ad com o Azure monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). 
>
> A coleção do log do Azure AD está sujeita a Azure Monitor preços.  Confira [Azure monitor preços](https://azure.microsoft.com/pricing/details/monitor/) para obter mais informações.
>
> Para usar a solução Office 365 do Azure Sentinel:
> 1. Usar o conector do Office 365 no Azure Sentinel afeta o preço do seu espaço de trabalho. Para obter mais informações, consulte [preços do Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).
> 2. Se você já estiver usando a solução Azure Monitor Office 365, deverá primeiro desinstalá-la usando o script na [seção desinstalar abaixo](#uninstall).
> 3. [Habilite a solução Sentinela do Azure](../../sentinel/quickstart-onboard.md) em seu espaço de trabalho.
> 4. Vá para a página **conectores de dados** no Azure Sentinel e habilite o conector do **Office 365** .
>
> ## <a name="frequently-asked-questions"></a>Perguntas frequentes
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-october-31"></a>P: é possível embutir a solução de Azure Monitor do Office 365 entre agora e 31 de outubro?
> Não, os scripts de integração da solução Azure Monitor Office 365 não estão mais disponíveis. A solução será removida em 31 de outubro.
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>P: as tabelas e os esquemas serão alterados?
> O nome e o esquema da tabela **OfficeActivity** permanecerão iguais aos da solução atual. Você pode continuar usando as mesmas consultas na nova solução, excluindo consultas que fazem referência a dados do Azure AD.
> 
> Os novos logs de [solução de monitoramento e relatório do Azure ad](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) serão incluídos nas tabelas [SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) e [AuditLogs](../../active-directory/reports-monitoring/concept-audit-logs.md) em vez de **OfficeActivity**. Para obter mais informações, consulte [como analisar logs do Azure ad](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md), que também são relevantes para usuários do Azure Sentinel e Azure monitor.
> 
> Veja a seguir exemplos de conversão de consultas de **OfficeActivity** para **SigninLogs**:
> 
> **Entradas com falha de consulta, por usuário:**
> 
> ```Kusto
> OfficeActivity
> | where TimeGenerated >= ago(1d) 
> | where OfficeWorkload == "AzureActiveDirectory"                      
> | where Operation == 'UserLoginFailed'
> | summarize count() by UserId    
> ```
> 
> ```Kusto
> SigninLogs
> | where ConditionalAccessStatus == "failure" or ConditionalAccessStatus == "notApplied"
> | summarize count() by UserDisplayName
> ```
> 
> **Exibir operações do Azure AD:**
> 
> ```Kusto
> OfficeActivity
> | where OfficeWorkload =~ "AzureActiveDirectory"
> | sort by TimeGenerated desc
> | summarize AggregatedValue = count() by Operation
> ```
> 
> ```Kusto
> AuditLogs
> | summarize count() by OperationName
> ```
> 
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>P: como posso integrar o Azure Sentinel?
> O Azure Sentinel é uma solução que você pode habilitar no espaço de trabalho Log Analytics novo ou existente. Para saber mais, confira [documentação de integração do Azure Sentinel](../../sentinel/quickstart-onboard.md).
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>P: preciso do Azure Sentinel para conectar os logs do Azure AD?
> Você pode configurar a [integração de logs do Azure AD com o Azure monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), que não está relacionado à solução Sentinela do Azure. O Azure Sentinel fornece um conector nativo e conteúdo pronto para uso para logs do Azure AD. Para obter mais informações, consulte a pergunta abaixo sobre o conteúdo pronto para uso orientado à segurança.
>
> ###    <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>P: quais são as diferenças ao conectar logs do Azure AD do Azure Sentinel e Azure Monitor?
> O Azure Sentinel e o Azure Monitor se conectam aos logs do Azure AD com base na mesma [solução de monitoramento e relatórios do Azure ad](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). O Azure Sentinel fornece um conector nativo de um clique que conecta os mesmos dados e fornece informações de monitoramento.
>
> ###    <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>P: o que preciso alterar ao mudar para as novas tabelas de relatórios e monitoramento do Azure AD?
> Todas as consultas que usam dados do Azure AD, incluindo consultas em alertas, painéis e qualquer conteúdo que você criou usando os dados do Azure AD do Office 365, devem ser recriadas usando as novas tabelas.
>
> O Azure Sentinel e o Azure AD fornecem conteúdo interno que você pode usar ao migrar para a solução de monitoramento e relatórios do Azure AD. Para obter mais informações, consulte a próxima pergunta sobre o conteúdo pronto para uso orientado à segurança e [como usar pastas de trabalho do Azure monitor para relatórios Azure Active Directory](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md). 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>P: como posso usar o conteúdo pronto para uso orientado à segurança do Azure Sentinel?
> O Azure Sentinel fornece painéis prontos para uso orientados à segurança, consultas de alerta personalizadas, consultas de busca, investigação e recursos de resposta automatizados com base nos logs do Office 365 e do Azure AD. Explore o GitHub e os tutoriais do Azure Sentinel para saber mais:
>
> - [Detecte ameaças prontas para uso](../../sentinel/tutorial-detect-threats-built-in.md)
> - [Criar regras analíticas personalizadas para detectar ameaças suspeitas](../../sentinel/tutorial-detect-threats-custom.md)
> - [Monitorar seus dados](../../sentinel/tutorial-monitor-your-data.md)
> - [Investigar incidentes com o Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)
> - [Configurar respostas de ameaças automatizadas no Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)
> - [Comunidade GitHub do Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>P: o Azure Sentinel fornece conectores adicionais como parte da solução?
> Sim, consulte [fontes de dados do Azure Sentinel Connect](../../sentinel/connect-data-sources.md).
> 
> ###    <a name="q-what-will-happen-on-october-31-do-i-need-to-offboard-beforehand"></a>P: o que acontecerá em 31 de outubro? Eu preciso transferir com antecedência?
> 
> - Você não poderá receber dados da solução do **Office365** . A solução será removida do seu espaço de trabalho e não estará mais disponível no Marketplace.
> - Para clientes do Azure Sentinel, a solução de espaço de trabalho Log Analytics **Office365** será incluída na solução **SecurityInsights** do Azure Sentinel.
> - Se você não transferir sua solução manualmente até 31 de outubro, seus dados serão desconectados automaticamente e a tabela **OfficeActivity** será removida. Mesmo assim, você ainda poderá restaurar a tabela quando habilitar o conector do Office 365 no Azure Sentinel, conforme explicado abaixo.
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>P: meus dados serão transferidos para a nova solução?
> Sim. Quando você remove a solução do **Office 365** do seu espaço de trabalho, seus dados ficarão temporariamente indisponíveis porque o esquema é removido. Quando você habilita o novo conector do **Office 365** no Azure Sentinel, o esquema é restaurado para o espaço de trabalho e todos os dados já coletados ficarão disponíveis. 
 

A solução de gerenciamento do Office 365 permite que você monitore o ambiente do Office 365 no Azure Monitor.

- Monitore atividades do usuário em suas contas do Office 365 para analisar padrões de uso, bem como identificar tendências de comportamentos. Por exemplo, você pode extrair os cenários de uso específicos, como arquivos que são compartilhados fora da sua organização ou os sites do SharePoint mais populares.
- Monitore atividades do administrador para controlar as alterações de configuração ou operações com privilégios elevados.
- Detecte e investigue comportamento indesejado do usuário, o que pode ser personalizado para suas necessidades organizacionais.
- Demonstre auditoria e conformidade. Por exemplo, você pode monitorar as operações de acesso a arquivos em arquivos confidenciais, o que pode ajudá-lo com o processo de conformidade e auditoria.
- Execute a solução de problemas operacionais usando [consultas de log](../log-query/log-query-overview.md) com base nos dados da atividade do Office 365 de sua organização.


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
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
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

2. Execute o script com o seguinte comando:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Exemplo:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

Suas credenciais serão solicitadas. Forneça as credenciais para seu espaço de trabalho do Log Analytics.

## <a name="data-collection"></a>Coleta de dados

Talvez demore algumas horas para coletar os dados pela primeira vez. Após o início da coleta, o Office 365 envia uma [notificação webhook](/office/office-365-management-api/office-365-management-activity-api-reference#receiving-notifications) com dados detalhados para o Azure Monitor sempre que um registro é criado. O registro fica disponível no Azure Monitor alguns minutos após o recebimento.

## <a name="using-the-solution"></a>Usando a solução

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Quando você adicionar a solução Office 365 ao espaço de trabalho do Log Analytics, o bloco **Office 365** será adicionado ao seu painel do OMS. Esse bloco exibe uma contagem e representação gráfica do número de computadores em seu ambiente e sua conformidade de atualização.<br><br>
![Bloco de Resumo do Office 365](media/solution-office-365/tile.png)  

Clique no bloco **Office 365** para abrir o painel **Office 365**.

![Painel do Office 365](media/solution-office-365/dashboard.png)  

O painel inclui as colunas na tabela a seguir. Cada coluna lista os dez principais alertas por contagem que correspondem aos critérios da coluna para o escopo e intervalo de tempo especificados. É possível executar uma pesquisa de log que fornece a lista inteira clicando em Ver todos na parte inferior da coluna ou clicando no cabeçalho de coluna.

| Coluna | Descrição |
|:--|:--|
| Operações | Fornece informações sobre os usuários ativos de todas as suas assinaturas do Office 365 monitoradas. Você também poderá ver o número de atividades que ocorrem ao longo do tempo.
| Exchange | Mostra a análise das atividades do Exchange Server, como a permissão Add-Mailbox ou Set-Mailbox. |
| SharePoint | Mostra as principais atividades que os usuários executam em documentos do SharePoint. Quando você faz drill down desse bloco, a página de pesquisa mostra os detalhes dessas atividades, como o documento de destino e o local dessa atividade. Por exemplo, para um evento acessado por arquivo, você poderá ver o documento que está sendo acessado, o nome da conta associada e o endereço IP. |
| Azure Active Directory | Inclui as principais atividades do usuário, como Tentativas de Logon e de Redefinição de Senha do Usuário. Quando você fizer o drill down, poderá ver os detalhes dessas atividades como o Status do Resultado. Isso é mais útil se você desejar monitorar atividades suspeitas no Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Registros de log do Azure Monitor

Todos os registros criados no espaço de trabalho do Log Analytics no Azure Monitor pela solução do Office 365 têm um **Tipo** de **OfficeActivity**.  A propriedade **OfficeWorkload** determina a qual serviço Office 365 o registro se refere: Exchange, AzureActiveDirectory, SharePoint ou OneDrive.  A propriedade **RecordType** especifica o tipo de operação.  As propriedades variam para cada tipo de operação e são mostradas nas tabelas a seguir.

### <a name="common-properties"></a>Propriedades comuns

As propriedades a seguir são comuns a todos os registros do Office 365.

| Propriedade | Descrição |
|:--- |:--- |
| Type | *OfficeActivity* |
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

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | O tipo de evento do Azure AD. |
| ExtendedProperties | As propriedades estendidas do evento do Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Logon na Conta do Azure Active Directory

Esses registros são criados quando um usuário do Active Directory tenta fazer logon.

| Propriedade | Descrição |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | O aplicativo que dispara o evento de logon de conta, como Office 15. |
| `Client` | Detalhes sobre o dispositivo cliente, o SO do dispositivo e o navegador do dispositivo que foi usado para o evento de logon na conta. |
| `LoginStatus` | Esta propriedade é diretamente de OrgIdLogon.LoginStatus. O mapeamento de várias falhas de logon interessantes pode ser feito por algoritmos de alerta. |
| `UserDomain` | As TII (informações de identidade de locatário). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Esses registros são criados quando adições ou alterações são feitas aos objetos do Azure Active Directory.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | O usuário em que a ação (identificada pela propriedade Operation) foi executada. |
| Ator | O usuário ou a entidade de serviço que executou a ação. |
| ActorContextId | O GUID da organização à qual pertence o ator. |
| ActorIpAddress | O endereço IP do ator no formato de endereço IPV4 ou IPV6. |
| InterSystemsId | O GUID que controla as ações entre componentes no serviço do Office 365. |
| IntraSystemId |     O GUID que é gerado pelo Azure Active Directory para controlar a ação. |
| SupportTicketId | A ID do tíquete de suporte ao cliente para a ação em situações "agir em nome de". |
| TargetContextId | O GUID da organização à qual o usuário de destino pertence. |


### <a name="data-center-security"></a>Segurança do Data Center

Esses registros são criados de dados de auditoria de Segurança do Data Center.  

| Propriedade | Descrição |
|:--- |:--- |
| EffectiveOrganization | O nome do locatário ao qual o cmdlet \elevation foi direcionado. |
| ElevationApprovedTime | O carimbo de data/hora de quando a elevação foi aprovada. |
| ElevationApprover | O nome de um gerente Microsoft. |
| ElevationDuration | A duração pela qual a elevação ficou ativa. |
| ElevationRequestId |     Um identificador exclusivo para a solicitação de elevação. |
| ElevationRole | A função para a qual a elevação foi solicitada. |
| ElevationTime | A hora de início da elevação. |
| Start_Time | A hora de início da execução do cmdlet. |


### <a name="exchange-admin"></a>Exchange Admin

Esses registros são criados quando são feitas alterações à configuração do Exchange.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |     Especifica se o cmdlet foi executado por um usuário em sua organização, por uma conta de serviço do datacenter ou pela equipe do datacenter da Microsoft ou por um administrador delegado. O valor False indica que o cmdlet foi executado por alguém de sua organização. O valor True indica que o cmdlet foi executado pela equipe do datacenter, uma conta de serviço do datacenter ou um administrador delegado. |
| ModifiedObjectResolvedName |     Esse é o nome amigável de usuário do objeto modificado pelo cmdlet. Isso é registrado apenas se o cmdlet modificar o objeto. |
| OrganizationName | O nome do locatário. |
| OriginatingServer | O nome do servidor do qual o cmdlet foi executado. |
| Parâmetros | O nome e o valor para todos os parâmetros que foram usados com o cmdlet identificado na propriedade Operations. |


### <a name="exchange-mailbox"></a>Caixa de correio do Exchange

Esses registros são criados quando alterações ou adições são feitas às caixas de correio do Exchange.

| Propriedade | Descrição |
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
| LogonUserDisplayName |     O nome amigável do usuário que realizou a operação. |
| LogonUserSid | O SID do usuário que realizou a operação. |
| MailboxGuid | O GUID do Exchange da caixa de correio que foi acessada. |
| MailboxOwnerMasterAccountSid | O SID da conta mestre da conta do proprietário da caixa de correio. |
| MailboxOwnerSid | O SID do proprietário da caixa de correio. |
| MailboxOwnerUPN | O endereço de email da pessoa que detém a caixa de correio acessada. |


### <a name="exchange-mailbox-audit"></a>Auditoria de Caixa de Correio do Exchange

Esses registros são criados quando é criada uma entrada de auditoria de caixa de correio.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Item | Representa o item no qual a operação foi executada | 
| SendAsUserMailboxGuid | O GUID do Exchange da caixa de correio acessada para enviar email como ela. |
| SendAsUserSmtp | Endereço SMTP do usuário que está sendo representado. |
| SendonBehalfOfUserMailboxGuid | O GUID do Exchange da caixa de correio acessada para enviar emails em nome dela. |
| SendOnBehalfOfUserSmtp | Endereço SMTP do usuário em cujo nome o email é enviado. |


### <a name="exchange-mailbox-audit-group"></a>Grupo de Auditoria da Caixa de Correio do Exchange

Esses registros são criados quando alterações ou adições são feitas a grupos do Exchange.

| Propriedade | Descrição |
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

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Identifica o que ocorreu um evento no SharePoint. Os valores possíveis são SharePoint ou ObjectModel. |
| ItemType | O tipo de objeto acessado ou modificado. Consulte a tabela ItemType para obter detalhes sobre os tipos de objetos. |
| MachineDomainInfo | Obter informações sobre operações de sincronização do dispositivo. Essas informações só serão relatadas se estiverem presentes na solicitação. |
| MachineId |     Obter informações sobre operações de sincronização do dispositivo. Essas informações só serão relatadas se estiverem presentes na solicitação. |
| Site_ | O GUID do site em que está localizado o arquivo ou a pasta acessado pelo usuário. |
| Source_Name | A entidade que disparou a operação auditada. Os valores possíveis são SharePoint ou ObjectModel. |
| UserAgent | Informações sobre o cliente ou o navegador do usuário. Essas informações são fornecidas pelo cliente ou pelo navegador. |


### <a name="sharepoint-schema"></a>Esquema do SharePoint

Esses registros são criados quando são feitas alterações de configuração do SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Cadeia de caracteres opcional para eventos personalizados. |
| Event_Data |     Conteúdo opcional para eventos personalizados. |
| ModifiedProperties | A propriedade é incluída para os eventos de administrador, como adicionar um usuário como um membro de um site ou um grupo de administração do conjunto de sites. A propriedade inclui o nome da propriedade que foi modificada (por exemplo, o grupo de Administrador do Site), o novo valor da propriedade modificada (como o usuário que foi adicionado como administrador do site) e o valor anterior do objeto modificado. |


### <a name="sharepoint-file-operations"></a>Operações de arquivo do SharePoint

Esses registros são criados em resposta às operações de arquivo no SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | A extensão de arquivo de um arquivo que é copiado ou movido. Essa propriedade é exibida apenas para eventos FileCopied e FileMoved. |
| DestinationFileName | O nome do arquivo que é copiado ou movido. Essa propriedade é exibida apenas para eventos FileCopied e FileMoved. |
| DestinationRelativeUrl | A URL da pasta de destino em que um arquivo é copiado ou movido. A combinação dos valores de parâmetros SiteURL, DestinationRelativeURL e DestinationFileName é igual ao valor da propriedade ObjectID, que é o nome de caminho completo para o arquivo copiado. Essa propriedade é exibida apenas para eventos FileCopied e FileMoved. |
| SharingType | O tipo de permissões de compartilhamento atribuídas ao usuário com o qual o recurso foi compartilhado. Esse usuário é identificado pelo parâmetro UserSharedWith. |
| Site_Url | A URL do site em que está localizado o arquivo ou a pasta acessado pelo usuário. |
| SourceFileExtension | A extensão de arquivo do arquivo que foi acessado pelo usuário. Essa propriedade ficará em branco se o objeto acessado for uma pasta. |
| SourceFileName |     O nome do arquivo ou pasta acessado pelo usuário. |
| SourceRelativeUrl | A URL da pasta que contém o arquivo acessado pelo usuário. A combinação de valores para os parâmetros SiteURL, SourceRelativeURL e SourceFileName é a mesma que o valor para a propriedade ObjectID, que é o nome de caminho completo do arquivo acessado pelo usuário. |
| UserSharedWith |     O usuário com o qual um recurso foi compartilhado. |




## <a name="sample-log-queries"></a>Consultas de log de exemplo

A tabela a seguir fornece exemplos de consultas de log para registros de atualização coletados por essa solução.

| Consulta | Descrição |
| --- | --- |
|Contagem de todas as operações em sua assinatura do Office 365 |OfficeActivity &#124; summarize count() by Operation |
|Uso de sites do SharePoint|OfficeActivity &#124; em que OfficeWorkload = ~ "SharePoint" &#124; resumir contagem () por SiteUrl \| classificar por contagem ASC|
|Operações de acesso de arquivos por tipo de usuário | OfficeActivity &#124; resumir contagem () por UserType |
|Monitorar de ações externas no Exchange|OfficeActivity &#124; where OfficeWorkload =~ "exchange" and ExternalAccess == true|



## <a name="next-steps"></a>Próximas etapas

* Use [consultas de log no Azure Monitor](../log-query/log-query-overview.md) para exibir dados detalhados das atualizações.
* [Crie seus próprios painéis](../learn/tutorial-logs-dashboards.md) para exibir suas consultas de pesquisa favoritas do Office 365.
* [Crie alertas](../platform/alerts-overview.md) para ser notificado proativamente das atividades importantes do Office 365.  
