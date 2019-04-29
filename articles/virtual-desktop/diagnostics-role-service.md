---
title: Identificar problemas com o recurso de diagnóstico de visualização de área de trabalho Virtual do Windows - Azure
description: Descreve o recurso de diagnóstico de visualização de área de trabalho Virtual do Windows e como usá-lo.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 6b79a26d63c02dd06b62ea6ad09941f947704dc0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870482"
---
# <a name="identify-issues-with-the-diagnostics-feature"></a>Identificar problemas com o recurso de diagnóstico

Visualização de área de trabalho Virtual do Windows oferece um recurso de diagnóstico que permite ao administrador identificar problemas por meio de uma única interface. As funções de área de trabalho Virtual do Windows uma atividade de diagnóstico de log sempre que um usuário interage com o sistema. Cada log contém informações relevantes, como as funções de área de trabalho Virtual do Windows envolvidas na transação, mensagens de erro, informações de locatário e informações do usuário. Atividades de diagnóstico são criadas pelo usuário final e ações administrativas e podem ser categorizadas em três principais buckets:

* Feed de atividades de assinatura: o usuário final dispara essas atividades, sempre que eles tentarem se conectar ao seu feed por meio de aplicativos de área de trabalho remota Microsoft.
* Atividades de Conexão: o usuário final dispara essas atividades, sempre que eles tentarem se conectar a uma área de trabalho ou o RemoteApp por meio de aplicativos de área de trabalho remota Microsoft.
* Atividades de gerenciamento: o administrador dispara essas atividades, sempre que eles executam operações de gerenciamento no sistema, como criação de pools de host, atribuindo usuários a grupos de aplicativos e criar atribuições de função.
  
As conexões que não alcançaram a área de trabalho Virtual do Windows não aparecerão nos resultados de diagnóstico, porque o próprio serviço de função de diagnóstico é parte da área de trabalho Virtual do Windows. Problemas de conexão de área de trabalho Virtual do Windows podem acontecer quando o usuário final está passando por problemas de conectividade de rede.

Para começar, [Baixe e importe o módulo do Windows PowerShell de área de trabalho Virtual](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) usar em sua sessão do PowerShell se você ainda não fez isso.

## <a name="diagnose-issues-with-powershell"></a>Diagnosticar problemas com o PowerShell

Diagnóstico de área de trabalho Virtual do Windows usa apenas um cmdlet do PowerShell, mas contém muitos parâmetros opcionais para ajudar a restringir e isolar problemas. As seções a seguir listam os cmdlets que você pode executar para diagnosticar problemas. A maioria dos filtros podem ser aplicados juntos. Os valores entre colchetes, como `<tenantName>`, devem ser substituídos pelos valores que se aplicam à sua situação.

### <a name="retrieve-diagnostic-activities-in-your-tenant"></a>Recuperar atividades de diagnóstico em seu locatário

Você pode recuperar as atividades de diagnóstico, inserindo o **Get-RdsDiagnosticActivities** cmdlet. O cmdlet de exemplo a seguir retornará uma lista de atividades de diagnóstico, classificados do mais específico para o menos recente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName>
```

Como os outros cmdlets do Windows PowerShell de área de trabalho Virtual, você deve usar o **- TenantName** parâmetro para especificar o nome do locatário que você deseja usar para a sua consulta. O nome do locatário é aplicável para quase todas as consultas de atividade de diagnóstico.

### <a name="retrieve-detailed-diagnostic-activities"></a>Recuperar as atividades detalhadas de diagnóstico

O **-detalhadas** parâmetro fornece detalhes adicionais para cada atividade diagnóstico retornada. O formato para cada atividade varia dependendo do seu tipo de atividade. O **-detalhada** parâmetro pode ser adicionado a qualquer **Get-RdsDiagnosticActivities** de consulta, conforme mostrado no exemplo a seguir.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Detailed
```

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Recuperar uma ID de atividade a atividade de diagnóstico específica

O **- ActivityId** parâmetro retorna uma determinada atividade de diagnóstico se ele existir, conforme mostrado no seguinte exemplo de cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="filter-diagnostic-activities-by-user"></a>Filtrar diagnóstico atividades por usuário

O **- UserName** parâmetro retorna uma lista de diagnóstico atividades iniciadas pelo usuário especificado, conforme mostrado no seguinte exemplo de cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

O **- UserName** parâmetro também pode ser combinado com outros parâmetros de filtragem opcionais.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrar as atividades de diagnóstico por tempo

Você pode filtrar a lista de atividade de diagnóstico retornado com o **- StartTime** e **- EndTime** parâmetros. O **- StartTime** parâmetro retornará uma lista de atividade de diagnóstico a partir de uma data específica, conforme mostrado no exemplo a seguir.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018"
```

O **- EndTime** parâmetro pode ser adicionado a um cmdlet com o **- StartTime** parâmetro para especificar um período de tempo que você deseja receber os resultados para específico. O cmdlet de exemplo a seguir retornará uma lista de atividades de diagnóstico entre 1º de agosto e 10 de agosto.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

O **- StartTime** e **- EndTime** parâmetros também podem ser combinados com outros parâmetros de filtragem opcionais.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrar diagnóstico atividades por tipo de atividade

Você também pode filtrar as atividades de diagnóstico por tipo de atividade com o **- ActivityType** parâmetro. O cmdlet a seguir retornará uma lista de conexões do usuário final:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Connection
```

O cmdlet a seguir retornará uma lista de tarefas de gerenciamento do administrador:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

O **Get-RdsDiagnosticActivities** cmdlet atualmente não dá suporte a especificação de Feed como o ActivityType.

### <a name="filter-diagnostic-activities-by-outcome"></a>Atividades de diagnóstico de filtro por resultado

Você pode filtrar a lista de atividade de diagnóstico retornado por resultado com o **-resultado** parâmetro. O cmdlet de exemplo a seguir retornará uma lista de atividades bem-sucedidas de diagnóstico.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Success
```

O cmdlet de exemplo a seguir retornará uma lista de atividades com falha de diagnóstico.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

O **-resultado** parâmetro também pode ser combinado com outros parâmetros de filtragem opcionais.

## <a name="common-error-scenarios"></a>Cenários comuns de erro

Cenários de erro são categorizados em internos para o serviço e externos para a área de trabalho Virtual do Windows.

* Problema interno: especifica os cenários que não podem ser atenuados pelo administrador de locatários e precisam ser resolvidos como problema de suporte. Quando gerando um tíquete de fornecer a ID da atividade, o nome do locatário e o período de tempo aproximado o problema ocorreu.
* Problema externo: estão relacionadas a cenários que podem ser reduzidos com o administrador do sistema. Esses são externos à área de trabalho Virtual do Windows.

A tabela a seguir lista erros comuns de que seus administradores podem enfrentar.

>[!NOTE]
>Essa visualização não inclui uma categorização completa de erros e será atualizada regularmente. Para garantir que as informações mais atualizadas, certifique-se de verificar neste artigo, pelo menos uma vez por mês.

### <a name="external-management-error-codes"></a>Códigos de erro de gerenciamento externo

|Código numérico|Código do erro|Solução sugerida|
|---|---|---|
|3|UnauthorizedAccess|O usuário que tentou executar o cmdlet do PowerShell administrativo não tem permissões para fazer isso ou digitado incorretamente o nome de usuário.|
|1000|TenantNotFound|O nome do locatário que você inseriu não corresponde dos locatários existentes. Examine o nome do locatário para erros de digitação e tente novamente.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Você não pode excluir um locatário como pelo tempo que ele contém objetos. Excluir os pools de host de sessão pela primeira vez e tente novamente.|
|2000|HostPoolNotFound|O nome de pool de host que você inseriu não corresponde a qualquer pools existentes do host. Examine o nome do pool de host para erros de digitação e tente novamente.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Não é possível excluir um pool de host, desde que ele contém objetos. Primeiro, remova todos os grupos de aplicativos no pool de host.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Remova todos os hosts de sessões antes de excluí o pool de host de sessão.|
|5001|SessionHostNotFound|O host da sessão que é consultado pode estar offline. Verificar o status do pool de host.|
|5008|SessionHostUserSessionsExist |Você deve se desconecte todos os usuários no host de sessão antes de executar sua atividade de gerenciamento planejados.|
|6000|AppGroupNotFound|O nome de grupo do aplicativo inserido não corresponde a qualquer grupos existentes do aplicativo. Examine o nome do grupo de aplicativo para erros de digitação e tente novamente.|
|6022|RemoteAppNotFound|O nome do RemoteApp que você inseriu não corresponde a qualquer RemoteApps. Examine o nome do RemoteApp para erros de digitação e tente novamente.|
|6010|PublishedItemsExist|O nome do recurso que você está tentando publicar é o mesmo que um recurso que já existe. Altere o nome do recurso e tente novamente.|
|7002|NameNotValidWhiteSpace|Não use espaço em branco no nome.|
|8000|InvalidAuthorizationRoleScope|O nome de função que você inseriu não corresponde ao quaisquer nomes de função existente. Examine o nome da função para erros de digitação e tente novamente. |
|8001|UserNotFound |O nome de usuário inserido não corresponde a qualquer nome de usuário existente. Examine o nome para erros de digitação e tente novamente.|
|8005|UserNotFoundInAAD |O nome de usuário inserido não corresponde a qualquer nome de usuário existente. Examine o nome para erros de digitação e tente novamente.|
|8008|TenantConsentRequired|Siga as instruções [aqui](tenant-setup-azure-active-directory.md#grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service) para fornecer consentimento para seu locatário.|

### <a name="external-connection-error-codes"></a>Códigos de erro de conexão externa

|Código numérico|Código do erro|Solução sugerida|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|O host da sessão não seja corretamente associado ao Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|As conexões falhou porque o host da sessão não está disponível. Verifique a integridade do host da sessão.|
|-2146233088|ConnectionFailedClientDisconnect|Se você vir esse erro com frequência, verifique se que o computador do usuário está conectado à rede.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|A sessão de que usuário host tentou se conectar ao não está íntegra. Depure a máquina virtual.|
|-2146233088|ConnectionFailedUserNotAuthorized|O usuário não tem permissão para acessar o aplicativo publicado ou a área de trabalho. O erro poderá aparecer depois que o administrador removido recursos publicados. Peça ao usuário para atualizar o feed no aplicativo de área de trabalho remota.|
|2|FileNotFound|O aplicativo que o usuário tentou acessar está instalado incorretamente ou definido como um caminho incorreto.|
|3|InvalidCredentials|O nome de usuário ou a senha inseridos pelo usuário não corresponde a qualquer nomes de usuário ou senhas existentes. Examine as credenciais para erros de digitação e tente novamente.|
|8|ConnectionBroken|A conexão entre o cliente e o Gateway ou servidor é descartada. Nenhuma ação é necessária, a menos que ele acontece inesperadamente.|
|14|UnexpectedNetworkDisconnect|A conexão à rede é descartado. Peça ao usuário para conectar-se novamente.|
|24|ReverseConnectFailed|Máquina virtual do host não tem nenhuma linha de visão direta para o Gateway de área de trabalho remota. Certifique-se de que o endereço IP do Gateway pode ser resolvido.|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as funções dentro de área de trabalho Virtual do Windows, consulte [ambiente de visualização de área de trabalho Virtual do Windows](environment-setup.md).

Para ver uma lista de cmdlets do PowerShell disponíveis para a área de trabalho Virtual do Windows, consulte o [referência do PowerShell](/powershell/windows-virtual-desktop/overview).
