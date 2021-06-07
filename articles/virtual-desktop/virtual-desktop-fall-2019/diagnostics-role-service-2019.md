---
title: Problemas de diagnóstico da Área de Trabalho Virtual do Windows (clássica) – Azure
description: Como usar o recurso de diagnóstico da Área de Trabalho Virtual do Windows (clássica) para diagnosticar problemas.
author: Heidilohr
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: e23a1e9a2a0118402df0d9b8869f170762a52284
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444948"
---
# <a name="identify-and-diagnose-issues-in-windows-virtual-desktop-classic"></a>Identificar e diagnosticar problemas na Área de Trabalho Virtual do Windows (clássica)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager, confira [este artigo](../diagnostics-role-service.md).

A Área de Trabalho Virtual do Windows oferece um recurso de diagnóstico que permite ao administrador identificar problemas por meio de uma única interface. As funções da Área de Trabalho Virtual do Windows registram uma atividade de diagnóstico sempre que um usuário interage com o sistema. Cada log contém informações relevantes, como as funções da Área de Trabalho Virtual do Windows envolvidas na transação, mensagens de erro, informações do locatário e informações do usuário. As atividades de diagnóstico são criadas pelas ações do usuário final e do administrador e podem ser categorizadas em três buckets principais:

* Atividades de assinatura do feed: o usuário final aciona essas atividades sempre que tenta se conectar ao feed por meio de aplicativos da Área de Trabalho Remota da Microsoft.
* Atividades de conexão: o usuário final aciona essas atividades sempre que tenta se conectar a uma área de trabalho ou RemoteApp por meio de aplicativos da Área de Trabalho Remota da Microsoft.
* Atividades de gerenciamento: o administrador aciona essas atividades sempre que realiza operações de gerenciamento no sistema, como a criação de pools de hosts, a atribuição de usuários a grupos de aplicativos e a criação de atribuições de função.

As conexões que não acessam a Área de Trabalho Virtual do Windows não são exibidas nos resultados do diagnóstico porque o próprio serviço da função de diagnóstico faz parte da Área de Trabalho Virtual do Windows. Problemas de conexão da Área de Trabalho Virtual do Windows podem ocorrer quando o usuário final enfrenta problemas de conectividade de rede.

Para começar, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso. Depois disso, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>Diagnosticar problemas com o PowerShell

O diagnóstico da Área de Trabalho Virtual do Windows usa apenas um cmdlet do PowerShell, mas contém muitos parâmetros opcionais para ajudar a restringir e isolar problemas. As seções a seguir listam os cmdlets que você pode executar para diagnosticar problemas. A maioria dos filtros pode ser aplicada em conjunto. Os valores listados entre parênteses, como `<tenantName>`, devem ser substituídos pelos valores que se aplicam à situação.

>[!IMPORTANT]
>O recurso de diagnóstico é para a solução de problemas de usuário único. Todas as consultas que usam o PowerShell devem incluir os parâmetros *-UserName* ou *-ActivityID*. Para recursos de monitoramento, use o Log Analytics. Confira [Usar Log Analytics para o recurso de diagnóstico](diagnostics-log-analytics-2019.md) para obter mais informações sobre como enviar dados de diagnóstico para o espaço de trabalho.

### <a name="filter-diagnostic-activities-by-user"></a>Filtrar atividades de diagnóstico por usuário

O parâmetro **-UserName** retorna uma lista de atividades de diagnóstico iniciadas pelo usuário especificado, conforme mostrado no exemplo de cmdlet a seguir.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

O parâmetro **-UserName** também pode ser combinado com outros parâmetros de filtragem opcionais.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrar atividades de diagnóstico por tempo

Você pode filtrar a lista de atividades de diagnóstico retornada com os parâmetros **-StartTime** e **-EndTime**. O parâmetro **-StartTime** retorna uma lista de atividades de diagnóstico a partir de uma data específica, conforme mostrado no exemplo a seguir.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

O parâmetro **-EndTime** pode ser adicionado a um cmdlet com o parâmetro **-StartTime**, para especificar um período específico para o qual você deseja receber resultados. O exemplo de cmdlet a seguir retorna uma lista de atividades de diagnóstico entre 1º de agosto e 10 de agosto.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

Os parâmetros **-StartTime** e **-EndTime** também podem ser combinados com outros parâmetros de filtragem opcionais.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrar atividades de diagnóstico por tipo

Você também pode filtrar atividades de diagnóstico por tipo de atividade com o parâmetro **-ActivityType**. O cmdlet a seguir retorna uma lista de conexões do usuário final:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

O cmdlet a seguir retorna uma lista de tarefas de gerenciamento do administrador:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

No momento, o cmdlet **Get-RdsDiagnosticActivities** não é compatível com a especificação de feed como ActivityType.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrar atividades de diagnóstico por resultado

Você pode filtrar a lista de atividades de diagnóstico retornada por resultado com o parâmetro **-Outcome**. O exemplo de cmdlet a seguir retorna uma lista de atividades de diagnóstico com sucesso.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

O exemplo de cmdlet a seguir retorna uma lista de atividades de diagnóstico com falha.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

O parâmetro **-Outcome** também pode ser combinado com outros parâmetros de filtragem opcionais.

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Recuperar uma atividade de diagnóstico específica por ID da atividade

O parâmetro **-ActivityId** retorna uma atividade de diagnóstico específica, se existir, conforme mostrado no exemplo de cmdlet a seguir.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Exibir mensagens de erro para uma atividade com falha por ID da atividade

Para exibir as mensagens de erro de uma atividade com falha, você deve executar o cmdlet com o parâmetro **-Detailed**. Você pode exibir a lista de erros executando o cmdlet **Select-Object**.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>Recuperar atividades de diagnóstico detalhadas

O parâmetro **-Detailed** fornece detalhes adicionais para cada atividade de diagnóstico retornada. O formato de cada atividade varia dependendo do tipo de atividade. O parâmetro **-Detailed** pode ser adicionado a qualquer consulta **Get-RdsDiagnosticActivities**, conforme mostrado no exemplo a seguir.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>Cenários de erro comuns

Os cenários de erro são categorizados como internos ao serviço e externos à Área de Trabalho Virtual do Windows.

* Problema interno: especifica cenários que não podem ser mitigados pelo administrador de locatários e precisam ser resolvidos como um problema de suporte. Ao fornecer comentários por meio da [comunidade técnica da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), inclua a ID da atividade e o período aproximado de quando o problema ocorreu.
* Problema externo: está relacionado a cenários que podem ser mitigados pelo administrador do sistema. São externos à Área de Trabalho Virtual do Windows.

A tabela a seguir lista os erros comuns que os administradores podem encontrar.

>[!NOTE]
>Essa lista inclui os erros mais comuns e é atualizada regularmente. Para garantir que você tenha as informações mais atualizadas, verifique este artigo pelo menos uma vez por mês.

### <a name="external-management-error-codes"></a>Códigos de erro de gerenciamento externo

|Código numérico|Código do erro|Solução sugerida|
|---|---|---|
|1322|ConnectionFailedNoMappingOfSIDinAD|O usuário não é membro do Azure Active Directory. Siga as instruções no [Centro Administrativo do Active Directory](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) para adicioná-lo.|
|3|UnauthorizedAccess|O usuário que tentou executar o cmdlet do PowerShell do administrador não tem permissões para fazer isso ou digitou o nome de usuário incorretamente.|
|1000|TenantNotFound|O nome do locatário que você inseriu não corresponde aos locatários existentes. Examine o nome do locatário quanto a erros de digitação e tente novamente.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Não é possível excluir um locatário enquanto ele contiver objetos. Exclua primeiro os pools de host da sessão e tente novamente.|
|2000|HostPoolNotFound|O nome do pool de host que você inseriu não corresponde aos pools de host existentes. Examine o nome do pool de host quanto a erros de digitação e tente novamente.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Não é possível excluir um pool de host enquanto ele contiver objetos. Primeiro remova todos os grupos de aplicativos no pool de hosts.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Remova todos os hosts da sessão antes de excluir o pool de hosts da sessão.|
|5001|SessionHostNotFound|O host da sessão que você consultou pode estar offline. Verifique o status do pool de host.|
|5008|SessionHostUserSessionsExist |Você deve desconectar todos os usuários no host da sessão antes de executar a atividade de gerenciamento pretendida.|
|6000|AppGroupNotFound|O nome do grupo de aplicativos que você inseriu não corresponde aos grupos de aplicativos existentes. Examine o nome do grupo de aplicativos quanto a erros de digitação e tente novamente.|
|6022|RemoteAppNotFound|O nome do RemoteApp que você digitou não corresponde aos RemoteApps. Examine o nome do RemoteApp quanto a erros de digitação e tente novamente.|
|6010|PublishedItemsExist|O nome do recurso que você está tentando publicar é o mesmo de um recurso que já existe. Altere o nome do recurso e tente novamente.|
|7002|NameNotValidWhiteSpace|Não use um espaço em branco no nome.|
|8000|InvalidAuthorizationRoleScope|O nome da função que você inseriu não corresponde aos nomes de função existentes. Examine o nome da função quanto a erros de digitação e tente novamente. |
|8001|UserNotFound |O nome de usuário que você inseriu não corresponde aos nomes de usuário existentes. Examine o nome quanto a erros de digitação e tente novamente.|
|8005|UserNotFoundInAAD |O nome de usuário que você inseriu não corresponde aos nomes de usuário existentes. Examine o nome quanto a erros de digitação e tente novamente.|
|8008|TenantConsentRequired|Siga as instruções [aqui](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) para dar consentimento para o locatário.|

### <a name="external-connection-error-codes"></a>Códigos de erro de conexão externa

|Código numérico|Código do erro|Solução sugerida|
|---|---|---|
|-2147467259|ConnectionFailedAdErrorNoSuchMember|O usuário não é membro do Active Directory. Siga as instruções no [Centro Administrativo do Active Directory](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) para adicioná-lo.|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|O host da sessão não entrou corretamente no Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|As conexões falharam porque o host da sessão não está disponível. Verifique a integridade do host da sessão.|
|-2146233088|ConnectionFailedClientDisconnect|Se você observar esse erro com frequência, verifique se o computador do usuário está conectado à rede.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|A sessão à qual o usuário do host tentou se conectar não está íntegra. Depurar a máquina virtual.|
|-2146233088|ConnectionFailedUserNotAuthorized|O usuário não tem permissão para acessar a área de trabalho ou o aplicativo publicado. O erro pode aparecer depois que o administrador removeu os recursos publicados. Peça ao usuário para atualizar o feed no aplicativo da Área de Trabalho Remota.|
|2|FileNotFound|O aplicativo que o usuário tentou acessar foi instalado incorretamente ou definido como um caminho incorreto.|
|3|InvalidCredentials|O nome de usuário ou a senha digitada pelo usuário não corresponde aos nomes de usuário ou senhas existentes. Examine as credenciais quanto a erros de digitação e tente novamente.|
|8|ConnectionBroken|A conexão entre o cliente e o gateway ou o servidor caiu. Nenhuma ação necessária, a menos que isso ocorra inesperadamente.|
|14|UnexpectedNetworkDisconnect|A conexão com a rede caiu. Peça ao usuário para se conectar novamente.|
|24|ReverseConnectFailed|A máquina virtual do host não tem uma linha de visão direta para o gateway de área de trabalho remota. Verifique se o endereço IP do gateway pode ser resolvido.|
|1322|ConnectionFailedNoMappingOfSIDinAD|O usuário não é membro do Active Directory. Siga as instruções no [Centro Administrativo do Active Directory](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) para adicioná-lo.|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as funções na Área de Trabalho Virtual do Windows, confira [ambiente da Área de Trabalho Virtual do Windows](environment-setup-2019.md).

Para ver uma lista dos cmdlets do PowerShell disponíveis para a Área de Trabalho Virtual do Windows, confira a [referência do PowerShell](/powershell/windows-virtual-desktop/overview).
