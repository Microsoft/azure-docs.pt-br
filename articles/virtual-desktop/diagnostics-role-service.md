---
title: Identificar problemas com o recurso de diagnóstico do Windows Virtual Desktop Preview-Azure
description: Descreve o recurso de diagnóstico de visualização de área de trabalho virtual do Windows e como usá-lo.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: f5869cbb51cf1c968ee8ca1e2286416fd263d647
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68224630"
---
# <a name="identify-issues-with-the-diagnostics-feature"></a>Identificar problemas com o recurso de diagnóstico

A versão prévia da área de trabalho virtual do Windows oferece um recurso de diagnóstico que permite ao administrador identificar problemas por meio de uma única interface. As funções de área de trabalho virtual do Windows registram uma atividade de diagnóstico sempre que um usuário interage com o sistema. Cada log contém informações relevantes, como as funções de área de trabalho virtual do Windows envolvidas na transação, mensagens de erro, informações de locatário e informações do usuário. As atividades de diagnóstico são criadas pelo usuário final e por ações administrativas e podem ser categorizadas em três buckets principais:

* Atividades de assinatura do feed: o usuário final dispara essas atividades sempre que tenta se conectar ao feed por meio de aplicativos Área de Trabalho Remota da Microsoft.
* Atividades de conexão: o usuário final dispara essas atividades sempre que tenta se conectar a um desktop ou RemoteApp por meio de aplicativos Área de Trabalho Remota da Microsoft.
* Atividades de gerenciamento: o administrador dispara essas atividades sempre que executam operações de gerenciamento no sistema, como a criação de pools de hosts, a atribuição de usuários a grupos de aplicativos e a criação de atribuições de função.
  
As conexões que não chegam à área de trabalho virtual do Windows não aparecerão nos resultados do diagnóstico porque o próprio serviço de função de diagnóstico faz parte da área de trabalho virtual do Windows. Problemas de conexão de área de trabalho virtual do Windows podem ocorrer quando o usuário final está enfrentando problemas de conectividade de rede.

Para começar, [Baixe e importe o módulo do PowerShell de área de trabalho virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para usar em sua sessão do PowerShell, se ainda não tiver feito isso.

## <a name="diagnose-issues-with-powershell"></a>Diagnosticar problemas com o PowerShell

O diagnóstico de área de trabalho virtual do Windows usa apenas um cmdlet do PowerShell, mas contém muitos parâmetros opcionais para ajudar a restringir e isolar problemas. As seções a seguir listam os cmdlets que você pode executar para diagnosticar problemas. A maioria dos filtros pode ser aplicada em conjunto. Os valores listados entre colchetes, `<tenantName>`como, devem ser substituídos pelos valores que se aplicam à sua situação.

### <a name="retrieve-diagnostic-activities-in-your-tenant"></a>Recuperar atividades de diagnóstico em seu locatário

Você pode recuperar atividades de diagnóstico inserindo o cmdlet **Get-RdsDiagnosticActivities** . O cmdlet de exemplo a seguir retornará uma lista de atividades de diagnóstico, classificadas da mais para a menos recente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName>
```

Assim como outros cmdlets do PowerShell de área de trabalho virtual do Windows, você deve usar o parâmetro **-tenantname** para especificar o nome do locatário que deseja usar para a consulta. O nome do locatário é aplicável para quase todas as consultas de atividade de diagnóstico.

### <a name="retrieve-detailed-diagnostic-activities"></a>Recuperar atividades de diagnóstico detalhadas

O parâmetro **-detailed** fornece detalhes adicionais para cada atividade de diagnóstico retornada. O formato de cada atividade varia dependendo de seu tipo de atividade. O parâmetro **-detailed** pode ser adicionado a qualquer consulta **Get-RdsDiagnosticActivities** , conforme mostrado no exemplo a seguir.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Detailed
```

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Recuperar uma atividade de diagnóstico específica por ID da atividade

O parâmetro **-ActivityId** retorna uma atividade de diagnóstico específica, se existir, conforme mostrado no cmdlet de exemplo a seguir.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Exibir mensagens de erro para uma atividade com falha por ID da atividade

Para exibir as mensagens de erro de uma atividade com falha, você deve executar o cmdlet com o parâmetro **-detailed** . Você pode exibir a lista de erros executando o cmdlet **Select-Object** .

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="filter-diagnostic-activities-by-user"></a>Filtrar atividades de diagnóstico por usuário

O parâmetro **-username** retorna uma lista de atividades de diagnóstico iniciadas pelo usuário especificado, conforme mostrado no cmdlet de exemplo a seguir.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

O parâmetro **-username** também pode ser combinado com outros parâmetros de filtragem opcionais.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrar atividades de diagnóstico por tempo

Você pode filtrar a lista de atividades de diagnóstico retornada com os parâmetros **-StartTime** e **-EndTime** . O parâmetro **-StartTime** retornará uma lista de atividades de diagnóstico a partir de uma data específica, conforme mostrado no exemplo a seguir.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018"
```

O parâmetro **-EndTime** pode ser adicionado a um cmdlet com o parâmetro **-StartTime** para especificar um período de tempo específico para o qual você deseja receber resultados. O cmdlet de exemplo a seguir retornará uma lista de atividades de diagnóstico entre 1º de agosto e 10 de agosto.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

Os parâmetros **-StartTime** e **-EndTime** também podem ser combinados com outros parâmetros de filtragem opcionais.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrar atividades de diagnóstico por tipo de atividade

Você também pode filtrar atividades de diagnóstico por tipo de atividade com o parâmetro **-ActivityType** . O cmdlet a seguir retornará uma lista de conexões de usuário final:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Connection
```

O cmdlet a seguir retornará uma lista de tarefas de gerenciamento do administrador:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

Atualmente, o cmdlet **Get-RdsDiagnosticActivities** não dá suporte à especificação de feed como ActivityType.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrar atividades de diagnóstico por resultado

Você pode filtrar a lista de atividades de diagnóstico retornada por resultado com o parâmetro **-Outcome** . O cmdlet de exemplo a seguir retornará uma lista de atividades de diagnóstico bem-sucedidas.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Success
```

O cmdlet de exemplo a seguir retornará uma lista de atividades de diagnóstico com falha.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

O parâmetro **-Outcome** também pode ser combinado com outros parâmetros de filtragem opcionais.

## <a name="common-error-scenarios"></a>Cenários de erro comuns

Os cenários de erro são categorizados em interno ao serviço e externos à área de trabalho virtual do Windows.

* Problema interno: especifica cenários que não podem ser mitigados pelo administrador de locatários e precisam ser resolvidos como um problema de suporte. Ao fornecer comentários por meio da [comunidade de tecnologia de área de trabalho virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), inclua a ID da atividade e o período de tempo aproximado de quando o problema ocorreu.
* Problema externo: relaciona-se a cenários que podem ser atenuados pelo administrador do sistema. Eles são externos à área de trabalho virtual do Windows.

A tabela a seguir lista os erros comuns que seus administradores podem encontrar.

>[!NOTE]
>Essa visualização não inclui uma categorização completa de erros e será atualizada regularmente. Para garantir que você tenha as informações mais atualizadas, certifique-se de verificar este artigo pelo menos uma vez por mês.

### <a name="external-management-error-codes"></a>Códigos de erro de gerenciamento externo

|Código numérico|Código do erro|Solução sugerida|
|---|---|---|
|3|UnauthorizedAccess|O usuário que tentou executar o cmdlet administrador do PowerShell não tem permissões para fazer isso ou digitar seu nome de usuário indigitadamente.|
|1000|TenantNotFound|O nome do locatário que você inseriu não corresponde a nenhum locatário existente. Examine o nome do locatário para erros de digitação e tente novamente.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Você não pode excluir um locatário desde que ele contenha objetos. Exclua primeiro os pools de hosts de sessão e tente novamente.|
|2000|HostPoolNotFound|O nome do pool de hosts que você inseriu não corresponde a nenhum pool de hosts existente. Examine o nome do pool de hosts para erros de digitação e tente novamente.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Não é possível excluir um pool de hosts desde que ele contenha objetos. Primeiro remova todos os grupos de aplicativos no pool de hosts.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Remova todos os hosts de sessões primeiro antes de excluir o pool de hosts de sessão.|
|5001|SessionHostNotFound|O host de sessão que você consultou pode estar offline. Verifique o status do pool de hosts.|
|5008|SessionHostUserSessionsExist |Você deve desconectar todos os usuários no host de sessão antes de executar a atividade de gerenciamento pretendida.|
|6000|AppGroupNotFound|O nome do grupo de aplicativos que você inseriu não corresponde a nenhum grupo de aplicativos existente. Examine o nome do grupo de aplicativos para erros de digitação e tente novamente.|
|6022|RemoteAppNotFound|O nome do RemoteApp que você inseriu não corresponde a nenhum RemoteApp. Examine o nome do RemoteApp para erros de digitação e tente novamente.|
|6010|PublishedItemsExist|O nome do recurso que você está tentando publicar é o mesmo que um recurso que já existe. Altere o nome do recurso e tente novamente.|
|7002|NameNotValidWhiteSpace|Não use o espaço em branco no nome.|
|8000|InvalidAuthorizationRoleScope|O nome da função que você inseriu não corresponde aos nomes de função existentes. Revise o nome da função para erros de digitação e tente novamente. |
|8001|UserNotFound |O nome de usuário que você inseriu não corresponde aos nomes de usuário existentes. Revise o nome para erros de digitação e tente novamente.|
|8005|UserNotFoundInAAD |O nome de usuário que você inseriu não corresponde aos nomes de usuário existentes. Revise o nome para erros de digitação e tente novamente.|
|8008|TenantConsentRequired|Siga as instruções [aqui](tenant-setup-azure-active-directory.md#grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service) para fornecer consentimento para seu locatário.|

### <a name="external-connection-error-codes"></a>Códigos de erro de conexão externa

|Código numérico|Código do erro|Solução sugerida|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|O host da sessão não está ingressado corretamente no Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|As conexões falharam porque o host da sessão não está disponível. Verifique a integridade do host da sessão.|
|-2146233088|ConnectionFailedClientDisconnect|Se você vir esse erro com frequência, verifique se o computador do usuário está conectado à rede.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|A sessão à qual o usuário do host tentou se conectar não está íntegra. Depure a máquina virtual.|
|-2146233088|ConnectionFailedUserNotAuthorized|O usuário não tem permissão para acessar o aplicativo ou a área de trabalho publicada. O erro pode aparecer depois que o administrador removeu os recursos publicados. Peça ao usuário para atualizar o feed no aplicativo Área de Trabalho Remota.|
|2|FileNotFound|O aplicativo que o usuário tentou acessar foi instalado incorretamente ou definido como um caminho incorreto.|
|3|InvalidCredentials|O nome de usuário ou a senha digitada pelo usuário não corresponde a nenhum nome de usuários ou senhas existentes. Examine as credenciais para erros de digitação e tente novamente.|
|8|ConnectionBroken|A conexão entre o cliente e o gateway ou o servidor foi descartada. Nenhuma ação é necessária, a menos que ocorra inesperadamente.|
|14|UnexpectedNetworkDisconnect|A conexão com a rede foi descartada. Peça ao usuário para se conectar novamente.|
|24|ReverseConnectFailed|A máquina virtual do host não tem uma linha de visão direta para o gateway de área de trabalho remota. Verifique se o endereço IP do gateway pode ser resolvido.|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as funções na área de trabalho virtual do Windows, consulte [ambiente de visualização de área de trabalho virtual do Windows](environment-setup.md).

Para ver uma lista de cmdlets do PowerShell disponíveis para a área de trabalho virtual do Windows, consulte a [referência do PowerShell](/powershell/windows-virtual-desktop/overview).
