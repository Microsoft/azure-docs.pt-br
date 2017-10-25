---
title: "Solução do Office 365 no OMS (Operations Management Suite) | Microsoft Docs"
description: "Este artigo apresenta detalhes sobre a configuração e o uso da solução Office 365 no OMS.  Ele inclui uma descrição detalhada dos registros do Office 365 criados no Log Analytics."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.openlocfilehash: dcc44986acbb76eafc3cfacb79acf237802de021
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="office-365-solution-in-operations-management-suite-oms"></a>A solução do Office 365 no OMS (Operations Management Suite)

![Logotipo do Office 365](media/oms-solution-office-365/icon.png)

A solução do Office 365 para OMS (Operations Management Suite) permite que você monitore seu ambiente do Office 365 no Log Analytics.  

- Monitore atividades do usuário em suas contas do Office 365 para analisar padrões de uso, bem como identificar tendências de comportamentos. Por exemplo, você pode extrair os cenários de uso específicos, como arquivos que são compartilhados fora da sua organização ou os sites do SharePoint mais populares.
- Monitore atividades do administrador para controlar as alterações de configuração ou operações com privilégios elevados.
- Detecte e investigue comportamento indesejado do usuário, o que pode ser personalizado para suas necessidades organizacionais.
- Demonstre auditoria e conformidade. Por exemplo, você pode monitorar as operações de acesso a arquivos em arquivos confidenciais, o que pode ajudá-lo com o processo de conformidade e auditoria.
- Execute a solução de problemas operacionais usando a Pesquisa do OMS sobre os dados de atividade do Office 365 da sua organização.

## <a name="prerequisites"></a>Pré-requisitos
É necessário o seguinte antes de essa solução ser instalada e configurada.

- Assinatura organizacional do Office 365.
- Credenciais para uma conta de usuário que seja um Administrador Global.
- Para receber dados de auditoria, você deve [configurar auditoria](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) na sua assinatura do Office 365.  Observe que a [auditoria de caixa de correio](https://technet.microsoft.com/library/dn879651.aspx) é configurada separadamente.  Você ainda poderá instalar a solução e coletar outros dados se a auditoria não estiver configurada.
 


## <a name="management-packs"></a>Pacotes de gerenciamento
Essa solução não instala nenhum pacote de gerenciamento nos grupos de gerenciamento conectados.
  

## <a name="configuration"></a>Configuração
Depois que você [adicionar a solução do Office 365 à sua assinatura](../log-analytics/log-analytics-add-solutions.md), precisará conectar-se à sua assinatura do Office 365.

1. Adicione a solução de Gerenciamento de Alertas ao seu espaço de trabalho do OMS usando o processo descrito em [Adicionar soluções](../log-analytics/log-analytics-add-solutions.md).
2. Vá para **Configurações** no portal do OMS.
3. Em **Fontes Conectadas**, selecione **Office 365**.
4. Clique em **Conectar o Office 365**.<br>![Conectar o Office 365](media/oms-solution-office-365/configure.png)
5. Entre no Office 365 com uma conta que seja um Administrador Global para sua assinatura. 
6. A assinatura será listada com cargas de trabalho que a solução monitorará.<br>![Conectar o Office 365](media/oms-solution-office-365/connected.png) 


## <a name="data-collection"></a>Coleta de dados
### <a name="supported-agents"></a>Agentes com suporte
A solução do Office 365 não recupera os dados de nenhum [agente do OMS](../log-analytics/log-analytics-data-sources.md).  Ela recupera dados diretamente do Office 365.

### <a name="collection-frequency"></a>Frequência de coleta
O Office 365 envia uma [notificação webhook](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) incluindo dados detalhados para o Log Analytics sempre que um registro é criado.

## <a name="using-the-solution"></a>Usando a solução
Quando você adicionar a solução Office 365 ao espaço de trabalho do OMS, o bloco **Office 365** será adicionado ao seu painel do OMS. Esse bloco exibe uma contagem e representação gráfica do número de computadores em seu ambiente e sua conformidade de atualização.<br><br>
![Bloco de Resumo do Office 365](media/oms-solution-office-365/tile.png)  

Clique no bloco **Office 365** para abrir o painel **Office 365**.

![Painel do Office 365](media/oms-solution-office-365/dashboard.png)  

O painel inclui as colunas na tabela a seguir. Cada coluna lista os dez principais alertas por contagem que correspondem aos critérios da coluna para o escopo e intervalo de tempo especificados. É possível executar uma pesquisa de log que fornece a lista inteira clicando em Ver todos na parte inferior da coluna ou clicando no cabeçalho de coluna.

| Coluna | Descrição |
|:--|:--|
| Operações | Fornece informações sobre os usuários ativos de todas as suas assinaturas do Office 365 monitoradas. Você também poderá ver o número de atividades que ocorrem ao longo do tempo.
| Exchange | Mostra a análise das atividades do Exchange Server, como a permissão Add-Mailbox ou Set-Mailbox. |
| SharePoint | Mostra as principais atividades que os usuários executam em documentos do SharePoint. Quando você faz drill down desse bloco, a página de pesquisa mostra os detalhes dessas atividades, como o documento de destino e o local dessa atividade. Por exemplo, para um evento Arquivo Acessado, você poderá ver o documento que está sendo acessado, o nome da sua conta associada e o endereço IP. |
| Azure Active Directory | Inclui as principais atividades do usuário, como Tentativas de Logon e de Redefinição de Senha do Usuário. Quando você fizer o drill down, poderá ver os detalhes dessas atividades como o Status do Resultado. Isso é mais útil se você desejar monitorar atividades suspeitas no Azure Active Directory. |




## <a name="log-analytics-records"></a>Registros do Log Analytics

Todos os registros criados no espaço de trabalho do Log Analytics da solução do Office 365 têm um **Tipo** de **OfficeActivity**.  A propriedade **OfficeWorkload** determina a qual serviço Office 365 o registro se refere: Exchange, AzureActiveDirectory, SharePoint ou OneDrive.  A propriedade **RecordType** especifica o tipo de operação.  As propriedades variam para cada tipo de operação e são mostradas nas tabelas a seguir.

### <a name="common-properties"></a>Propriedades comuns
As propriedades a seguir são comuns a todos os registros do Office 365.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo | *OfficeActivity* |
| ClientIP | O endereço IP do dispositivo que foi usado quando a atividade foi registrada. O endereço IP é exibido no formato de endereço IPv4 ou IPv6. |
| OfficeWorkload | Serviço Office 365 ao qual o registro se refere.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Operação | O nome da atividade do usuário ou administrador.  |
| OrganizationId | O GUID do locatário do Office 365 da sua organização. Esse valor sempre será o mesmo para a sua organização, independentemente do serviço do Office 365 em que ele ocorre. |
| RecordType | Tipo de operação executada. |
| ResultStatus | Indica se a ação (especificada na propriedade Operation) foi bem-sucedida ou não. Os valores possíveis são Succeeded, PartiallySucceded ou Failed. Para a atividade de administração do Exchange, o valor é True ou False. |
| UserId | O nome UPN do usuário que executou a ação que resultou em o registro ser incluído em log. Por exemplo, my_name@my_domain_name. Observe que os registros para a atividade realizada por contas do sistema (como SHAREPOINT\system ou NTAUTHORITY\SYSTEM) também são incluídos. | 
| UserKey | Uma ID alternativa para o usuário identificado na propriedade UserId.  Por exemplo, essa propriedade é preenchida com a PUID (ID exclusiva do passport) para eventos executadas por usuários no SharePoint, no OneDrive for Business e no Exchange. Essa propriedade também pode especificar o mesmo valor que a propriedade UserID para eventos que ocorrem em outros serviços e eventos executados por contas do sistema|
| UserType | O tipo de usuário que realizou a operação.<br><br>Administrador<br>Aplicativo<br>DcAdmin<br>Regular <br>Reservado<br>ServicePrincipal<br>Sistema |


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
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| Aplicativo | O aplicativo que dispara o evento de logon de conta, como Office 15. |
| Cliente | Detalhes sobre o dispositivo cliente, o SO do dispositivo e o navegador do dispositivo que foi usado para o evento de logon na conta. |
| LoginStatus | Esta propriedade é diretamente de OrgIdLogon.LoginStatus. O mapeamento de várias falhas de logon interessantes pode ser feito por algoritmos de alerta. |
| UserDomain | As TII (informações de identidade de locatário). | 


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
| IntraSystemId |   O GUID que é gerado pelo Azure Active Directory para controlar a ação. |
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
| ElevationRequestId |  Um identificador exclusivo para a solicitação de elevação. |
| ElevationRole | A função para a qual a elevação foi solicitada. |
| ElevationTime | A hora de início da elevação. |
| Start_Time | A hora de início da execução do cmdlet. |


### <a name="exchange-admin"></a>Exchange Admin
Esses registros são criados quando são feitas alterações à configuração do Exchange.

| Propriedade | Descrição |
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
| LogonUserDisplayName |    O nome amigável do usuário que realizou a operação. |
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
| MachineId |   Obter informações sobre operações de sincronização do dispositivo. Essas informações só serão relatadas se estiverem presentes na solicitação. |
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
| Event_Data |  Conteúdo opcional para eventos personalizados. |
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
| SourceFileName |  O nome do arquivo ou pasta acessado pelo usuário. |
| SourceRelativeUrl | A URL da pasta que contém o arquivo acessado pelo usuário. A combinação de valores para os parâmetros SiteURL, SourceRelativeURL e SourceFileName é a mesma que o valor para a propriedade ObjectID, que é o nome de caminho completo do arquivo acessado pelo usuário. |
| UserSharedWith |  O usuário com o qual um recurso foi compartilhado. |




## <a name="sample-log-searches"></a>Pesquisas de log de exemplo
A tabela a seguir fornece pesquisas de log de exemplo para os registros de atualização coletados por essa solução.

| Consultar | Descrição |
| --- | --- |
|Contagem de todas as operações em sua assinatura do Office 365 |`Type = OfficeActivity | measure count() by Operation` |
|Uso de sites do SharePoint|`Type=OfficeActivity OfficeWorkload=sharepoint | measure count() as Count by SiteUrl | sort Count asc`|
|Operações de acesso de arquivos por tipo de usuário|`Type=OfficeActivity OfficeWorkload=sharepoint Operation=FileAccessed | measure count() by UserType`|
|Pesquisar com uma palavra-chave específica|`Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"`|
|Monitorar de ações externas no Exchange|`Type=OfficeActivity OfficeWorkload=exchange ExternalAccess = true`|



## <a name="troubleshooting"></a>Solucionar problemas

Se sua solução do Office 365 não estiver coletando dados conforme o esperado, verifique seu status no portal do OMS em **Configurações** -> **Fontes Conectadas** -> **Office 365**. A tabela a seguir descreve cada status.

| Status | Descrição |
|:--|:--|
| Ativo | A assinatura do Office 365 está ativa e a carga de trabalho está conectada com sucesso ao seu espaço de trabalho do OMS. |
| Pendente | A assinatura do Office 365 está ativa, mas a carga de trabalho ainda não está conectada ao seu espaço de trabalho do OMS com êxito. Na primeira vez que você conectar a assinatura do Office 365, as cargas de trabalho terão esse status até que sejam conectadas com êxito. Aguarde 24 horas para todas as cargas de trabalho mudarem para ativo. |
| Inativo | A assinatura do Office 365 está em um estado inativo. Consulte a página de Administração do Office 365 para obter detalhes. Depois de ativar sua assinatura do Office 365, desvincule-a do seu espaço de trabalho do OMS e vincule-a novamente para iniciar o recebimento de dados. |



## <a name="next-steps"></a>Próximas etapas
* Use Pesquisas de Log no [Log Analytics](../log-analytics/log-analytics-log-searches.md) para exibir dados detalhados das atualizações.
* [Crie seus próprios painéis](../log-analytics/log-analytics-dashboards.md) para exibir suas consultas de pesquisa favoritas do Office 365.
* [Crie alertas](../log-analytics/log-analytics-alerts.md) para ser notificado proativamente das atividades importantes do Office 365.  
