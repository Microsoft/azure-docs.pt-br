---
title: Monitorar a proteção de senha do Azure AD local
description: Saiba como monitorar e examinar os logs da proteção de senha do Azure AD para um ambiente de Active Directory Domain Services local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: edc246a414401c4c1c0248787eda0381fcd63037
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96741755"
---
# <a name="monitor-and-review-logs-for-on-premises-azure-ad-password-protection-environments"></a>Monitorar e examinar os logs de ambientes de proteção de senha do Azure AD locais

Após a implantação da Proteção de Senha do Azure AD, o monitoramento e o relatório são tarefas essenciais. Este artigo apresenta detalhes para entender várias técnicas de monitoramento, incluindo o local em que cada serviço registra informações e como relatar o uso da Proteção de Senha do Azure AD.

O monitoramento e os relatórios são feitos por mensagens de log de eventos ou pela execução de cmdlets do PowerShell. O agente de DC e os serviços de proxy registram mensagens de log de eventos. Todos os cmdlets do PowerShell descritos abaixo estão disponíveis somente no servidor proxy (consulte o módulo AzureADPasswordProtection PowerShell). O software do agente de controlador de domínio não instala um módulo do PowerShell.

## <a name="dc-agent-event-logging"></a>Log de eventos do agente do DC

Em cada controlador de domínio, o software de serviço do agente do DC grava os resultados de cada operação de validação de senha individual (e outro status) em um log de eventos local:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

O log de administração do agente do DC é a principal fonte de informações de como o software está se comportando.

Observe que o log de rastreamento está desativado por padrão.

Os eventos registrados pelos vários componentes do agente do DC se enquadram nos intervalos a seguir:

|Componente |Intervalo de ID de evento|
| --- | --- |
|Dll do filtro de senha do Agente DC| 10000-19999|
|Processo de hospedagem de serviço do agente DC| 20000-29999|
|Lógica de validação da política de serviço do agente DC| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>Log de eventos do administrador do agente do DC

### <a name="password-validation-outcome-events"></a>Eventos de resultado de validação de senha

Em cada controlador de domínio, o software de serviço do agente do DC grava os resultados de cada validação de senha individual no log de eventos do administrador do agente do DC.

Para uma operação de validação de senha com êxito, geralmente há um evento registrado da dll do filtro de senha do agente DC. Para uma operação de validação de senha com falha, geralmente há dois eventos registrados, um do serviço do agente DC e um da dll do filtro de senha do Agente DC.

Eventos discretos para capturar essas situações são registrados com base nos fatores a seguir:

* Se uma determinada senha está sendo definida ou alterada.
* Se a validação de uma determinada senha foi aprovada ou falhou.
* Se a validação falhou devido à política global da Microsoft, a política organizacional ou uma combinação.
* Se o modo somente auditoria está atualmente ativado ou desativado para a política de senha atual.

Os principais eventos relacionados à validação de senha são os seguintes:

| Evento |Alteração de senha |Definição de senha|
| --- | :---: | :---: |
|Aprovado |10014 |10015|
|Falha (devido à política de senha do cliente)| 10016, 30002| 10017, 30003|
|Falha (devido à política de senha da Microsoft)| 10016, 30004| 10017, 30005|
|Falha (devido à combinação das políticas de senha da Microsoft e do cliente)| 10016, 30026| 10017, 30027|
|Falha (devido ao nome de usuário)| 10016, 30021| 10017, 30022|
|Aprovado somente auditoria (teria falhado a política de senha do cliente)| 10024, 30008| 10025, 30007|
|Aprovado somente auditoria (teria falhado a política de senha da Microsoft)| 10024, 30010| 10025, 30009|
|Aprovado somente auditoria (teria falhado na combinação das políticas de senha da Microsoft e do cliente)| 10024, 30028| 10025, 30029|
|Passagem somente de auditoria (falharia devido a nome de usuário)| 10016, 30024| 10017, 30023|

Os casos que fazem referência a "políticas combinadas" na tabela acima são referindo-se a situações em que a senha do usuário foi encontrada para conter pelo menos um token da lista de senhas banida do Microsoft e a lista de senhas banida do cliente.

Os casos na tabela acima que se referem a "nome de usuário" estão se referindo a situações em que a senha de um usuário foi encontrada para conter o nome da conta do usuário e/ou um dos nomes amigáveis do usuário. Qualquer um dos cenários fará com que a senha do usuário seja rejeitada quando a política for definida como impor ou passada se a política estiver no modo de auditoria.

Quando um par de eventos é registrado em conjunto, ambos os eventos são explicitamente associados tendo a mesma CorrelationId.

### <a name="password-validation-summary-reporting-via-powershell"></a>Relatório do resumo da validação de senha via PowerShell

O cmdlet `Get-AzureADPasswordProtectionSummaryReport` pode ser usado para produzir uma exibição resumida da atividade de validação de senha. Um exemplo de saída deste cmdlet é o seguinte:

```powershell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

O escopo do relatório do cmdlet pode ser influenciado usando um dos parâmetros – floresta,-Domain ou – DomainController. Não especificar um parâmetro implica – Forest.

O cmdlet `Get-AzureADPasswordProtectionSummaryReport` funciona consultando o log de eventos do administrador do agente do DC e, em seguida, contando o número total de eventos que correspondem a cada categoria de resultado exibida. A tabela a seguir contém os mapeamentos entre cada resultado e sua ID de evento correspondente:

|Propriedade Get-AzureADPasswordProtectionSummaryReport |ID do evento correspondente|
| :---: | :---: |
|PasswordChangesValidated |10014|
|PasswordSetsValidated |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejected |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

O cmdlet `Get-AzureADPasswordProtectionSummaryReport` é enviado em forma de script do PowerShell e, se necessário, pode ser referenciado diretamente no seguinte local:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> Esse cmdlet funciona abrindo uma sessão do PowerShell para cada controlador de domínio. Para ter êxito, o suporte de sessão remota do PowerShell deve estar habilitado em cada controlador de domínio e o cliente deve ter privilégios suficientes. Para obter mais informações sobre requisitos de sessão remota do PowerShell, execute "Get-Help about_Remote_Troubleshooting" em uma janela do PowerShell.

> [!NOTE]
> Esse cmdlet funciona consultando remotamente cada log de eventos de administrador do serviço de agente de DC. Se os logs de eventos contiverem um grande número de eventos, o cmdlet pode levar muito tempo para ser concluído. Além disso, consultas de rede em massa de grandes conjuntos de dados podem afetar o desempenho do controlador de domínio. Portanto, esse cmdlet deve ser usado com cuidado em ambientes de produção.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Mensagem de registro de eventos de amostra para a ID de Evento 10014 (alteração de senha bem-sucedido)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Mensagem de registro de eventos de amostra para ID de Evento 10017 e 30003 (conjunto de senha com falha)

10017:

```text
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```text
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Mensagem de registro de eventos de amostra para o ID do Evento 30001 (senha aceita devido a nenhuma política disponível)

```text
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Mensagem de registro de eventos de amostra para a ID de Evento 30006 (nova política sendo aplicada)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Mensagem de log de eventos de exemplo para a ID de Evento 30019 (A Proteção de Senha do Azure AD está desabilitada)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>Log operacional de agente do controlador de domínio

O serviço do agente DC também registrará eventos relacionados à operação no log a seguir:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>Log de rastreamento do agente de controlador de domínio

O serviço do agente DC também pode registrar eventos de rastreamento detalhados no nível de depuração no seguinte log:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Log de rastreamento está desabilitado por padrão.

> [!WARNING]
> Quando ativado, o log de rastreamento recebe um alto volume de eventos e pode afetar o desempenho do controlador de domínio. Portanto, esse log aprimorado somente deverá ser habilitado quando um problema exigir uma investigação mais profunda e apenas por um período mínimo.

## <a name="dc-agent-text-logging"></a>Registro de texto do agente do DC

O serviço do agente DC pode ser configurado para gravar em um log de texto, definindo o seguinte valor do Registro:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

O log de texto é desabilitado por padrão. Uma reinicialização do serviço do agente DC é necessária para que as alterações nesse valor entrem em vigor. Quando ativado, o serviço do agente DC gravará em um arquivo de log localizado em:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> O log de texto recebe as mesmas entradas de nível de depuração que podem ser registradas no log de rastreamento, mas geralmente tem um formato mais fácil de revisar e analisar.

> [!WARNING]
> Quando habilitado, esse log recebe um alto volume de eventos e pode afetar o desempenho do controlador de domínio. Portanto, esse log aprimorado somente deverá ser habilitado quando um problema exigir uma investigação mais profunda e apenas por um período mínimo.

## <a name="dc-agent-performance-monitoring"></a>Monitoramento de desempenho do agente do DC

O software de serviço do agente DC instala um objeto de contador de desempenho denominado **Proteção de senha do Microsoft Azure Active Directory**. Atualmente, estão disponíveis os contadores de desempenho a seguir:

|Nome do contador de desempenho | Descrição|
| --- | --- |
|Senhas processadas |Esse contador exibe o número total de senhas processadas (aceitas ou rejeitadas) desde o último reinício.|
|Senhas aceitas |Esse contador exibe o número total de senhas aceitas desde o último reinício.|
|Senhas rejeitadas |Esse contador exibe o número total de senhas que foram rejeitadas desde o último reinício.|
|Solicitações de filtro de senha em andamento |Esse contador exibe o número de solicitações de filtro de senha atualmente em andamento.|
|Solicitações de filtro de senha de pico |Esse contador exibe o número de pico das solicitações de filtro de senha simultâneas desde o último reinício.|
|Erros de solicitação de filtro de senha |Este contador exibe o número total de solicitações de filtro de senha que falharam devido a um erro desde o último reinício. Erros podem ocorrer quando o serviço do agente DC de Proteção de Senha do Azure AD não estiver em execução.|
|Solicitações de filtro de senha/s |Esse contador exibe a taxa na qual as senhas estão sendo processadas.|
|Tempo de processamento de solicitação de filtro de senha |Esse contador exibe o tempo médio necessário para processar uma solicitação de filtro de senha.|
|Tempo de processamento da solicitação de filtro de senha de pico |Esse contador exibe o tempo de processamento da solicitação do filtro de senha de pico desde o último reinício.|
|Senhas aceitas devido ao modo de auditoria |Esse contador exibe o número total de senhas que normalmente teriam sido rejeitadas, mas foram aceitas porque a política de senha foi configurada para estar no modo de auditoria (desde o último reinício).|

## <a name="dc-agent-discovery"></a>Descoberta do Agente DC

O cmdlet `Get-AzureADPasswordProtectionDCAgent` pode ser usado para exibir informações básicas sobre os vários agentes DC em execução em um domínio ou floresta. Essas informações são recuperadas dos objetos serviceConnectionPoint registrados pelo(s) serviço(s) do agente DC em execução.

Um exemplo de saída deste cmdlet é o seguinte:

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

As várias propriedades são atualizadas por cada serviço do agente DC em uma base horária aproximada. Os dados ainda estão sujeitos à latência de replicação do Active Directory.

O escopo da consulta do cmdlet pode ser influenciado usando os parâmetros – Forest ou-Domain.

Se o valor de HeartbeatUTC se torna obsoleto, isso pode ser um sintoma de que o Agente do DC de Proteção de Senha do Azure AD no controlador de domínio não está em execução ou foi desinstalado ou o computador foi rebaixado e não é mais um controlador de domínio.

Se o valor de PasswordPolicyDateUTC ficar obsoleto, isso poderá ser um sintoma de que o agente DC da proteção por senha do Azure AD nesse computador não está funcionando corretamente.

## <a name="dc-agent-newer-version-available"></a>Versão mais recente do agente de DC disponível

O serviço de agente de controlador de domínio registrará um evento de aviso 30034 no log operacional ao detectar que uma versão mais recente do software do agente de DC está disponível, por exemplo:

```text
An update for Azure AD Password Protection DC Agent is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
```

O evento acima não especifica a versão do software mais recente. Você deve ir para o link na mensagem de evento para obter essas informações.

> [!NOTE]
> Apesar das referências a "AutoUpgrade" na mensagem de evento acima, o software do agente de controlador de domínio não oferece suporte a esse recurso no momento.

## <a name="proxy-service-event-logging"></a>Logs de eventos do serviço de proxy

O serviço Proxy emite um conjunto mínimo de eventos para os seguintes logs de eventos:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Observe que o log de rastreamento está desativado por padrão.

> [!WARNING]
> Quando ativado, o log de rastreamento recebe um grande volume de eventos e isso pode afetar o desempenho do host proxy. Portanto, esse log deverá ser habilitado apenas quando um problema exigir uma investigação mais profunda e apenas por um período mínimo.

Os eventos são registrados pelos vários componentes de Proxy usando os intervalos a seguir:

|Componente |Intervalo de ID de evento|
| --- | --- |
|Processo de hospedagem de serviço do proxy| 10000-19999|
|Lógica de negócios do núcleo de serviço de proxy| 20000-29999|
|Cmdlets do PowerShell| 30000-39999|

## <a name="proxy-service-text-logging"></a>Log de texto do serviço de proxy

O serviço de proxy pode ser configurado para gravar em um log de texto, definindo o seguinte valor do Registro:

HKLM \ System \ CurrentControlSet \ Services \ AzureADPasswordProtectionProxy \ Parâmetros! EnableTextLogging = 1 (valor REG_DWORD)

O log de texto é desabilitado por padrão. Uma reinicialização do serviço Proxy é necessária para que as alterações nesse valor entrem em vigor. Quando ativado, o serviço Proxy gravará em um arquivo de log localizado em:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> O log de texto recebe as mesmas entradas de nível de depuração que podem ser registradas no log de rastreamento, mas geralmente tem um formato mais fácil de revisar e analisar.

> [!WARNING]
> Quando habilitado, esse log recebe um alto volume de eventos e pode afetar o desempenho do computador. Portanto, esse log aprimorado somente deverá ser habilitado quando um problema exigir uma investigação mais profunda e apenas por um período mínimo.

## <a name="powershell-cmdlet-logging"></a>Registro de cmdlet PowerShell

Os cmdlets do PowerShell que podem resultar em uma alteração de estado (por exemplo, Register-AzureADPasswordProtectionProxy) normalmente registrarão um evento de saída no Log operacional.

Além disso, a maioria dos cmdlets do PowerShell de proteção por senha do Azure AD será gravada em um log de texto localizado em:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Se ocorrer um erro de cmdlet e a causa e \ ou a solução não forem aparentes, esses logs de texto também poderão ser consultados.

## <a name="proxy-discovery"></a>Descoberta de proxy

O cmdlet `Get-AzureADPasswordProtectionProxy` pode ser usado para exibir informações básicas sobre os vários serviços de Proxy de Proteção de Senha do Azure AD em execução em um domínio ou floresta. Essas informações são recuperadas dos objetos serviceConnectionPoint registrados pelos serviços de Proxy em execução.

Um exemplo de saída deste cmdlet é o seguinte:

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

As várias propriedades são atualizadas por cada serviço de Proxy em uma base horária aproximada. Os dados ainda estão sujeitos à latência de replicação do Active Directory.

O escopo da consulta do cmdlet pode ser influenciado usando os parâmetros – Forest ou-Domain.

Se o valor de HeartbeatUTC se torna obsoleto, isso pode ser um sintoma de que o Proxy de Proteção de Senha do Azure AD nesse computador não está em execução ou foi desinstalado.

## <a name="proxy-agent-newer-version-available"></a>Versão mais recente do agente de proxy disponível

O serviço de proxy registrará um evento de aviso 20002 no log operacional ao detectar que uma versão mais recente do software proxy está disponível, por exemplo:

```text
An update for Azure AD Password Protection Proxy is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
.
```

O evento acima não especifica a versão do software mais recente. Você deve ir para o link na mensagem de evento para obter essas informações.

Esse evento será emitido mesmo se o agente de proxy estiver configurado com a atualização autohabilitada.

## <a name="next-steps"></a>Próximas etapas

[Solução de problemas de Proteção de Senha do Azure AD](howto-password-ban-bad-on-premises-troubleshoot.md)

Para obter mais informações sobre as listas de senhas proibidas globais e personalizadas, consulte o artigo [Proibir senhas incorretas](concept-password-ban-bad.md)
