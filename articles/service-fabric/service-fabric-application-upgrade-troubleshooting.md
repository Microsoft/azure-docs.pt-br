---
title: Solucionando problemas de atualizações de aplicativo | Microsoft Docs
description: Este artigo aborda alguns problemas comuns em torno da atualização de um aplicativo Service Fabric e como resolvê-los.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 19ad152e-ec50-4327-9f19-065c875c003c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: e393eb92e11dc8dc296f1dc5f1c0036566c285c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60616030"
---
# <a name="troubleshoot-application-upgrades"></a>Solucionar problemas de atualizações de aplicativo

Este artigo aborda alguns dos problemas comuns na atualização de um aplicativo do Service Fabric e como resolvê-los.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Solucionar problemas de atualização de um aplicativo com falha

Quando uma atualização falha, a saída do comando **Get-ServiceFabricApplicationUpgrade** contém informações adicionais para depurar a falha.  A lista a seguir especifica como as informações adicionais podem ser usadas:

1. Identificar o tipo de falha.
2. Identificar o motivo da falha.
3. Isolar um ou mais componentes com falha para saber mais.

Essas informações estão disponíveis quando o Service Fabric detecta a falha, independentemente de a **FailureAction** reverter ou suspender a atualização.

### <a name="identify-the-failure-type"></a>Identificar o tipo de falha

Na saída de **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identifica o carimbo de hora (UTC) em que uma falha de atualização foi detectada pelo Service Fabric e **FailureAction** foi acionado. **FailureReason** identifica uma das três possíveis causas de alto nível da falha:

1. UpgradeDomainTimeout: indica que um determinado domínio de atualização demorou muito para ser concluído e **UpgradeDomainTimeout** expirou.
2. OverallUpgradeTimeout: indica que a atualização global demorou muito para ser concluída e **UpgradeTimeout** expirou.
3. HealthCheck: indica que, após a atualização de um domínio de atualização, o aplicativo permaneceu não íntegro de acordo com as políticas de integridade especificadas e **HealthCheckRetryTimeout** expirou.

Essas entradas somente são exibidas na saída quando a atualização falhar e começar a reversão. Outras Informações são exibidas dependendo do tipo de falha.

### <a name="investigate-upgrade-timeouts"></a>Investigar tempos limite de atualização

As falhas de tempo de execução de atualização são geralmente causadas por problemas de disponibilidade do serviço. A saída após este parágrafo é típica de atualizações em que as réplicas ou instâncias de serviço não são iniciadas na nova versão do código. O campo **UpgradeDomainProgressAtFailure** captura um instantâneo de trabalhos de atualização pendentes no momento da falha.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
```

Neste exemplo, a atualização falhou no domínio de atualização *MYUD1* e duas partições (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* e *4b43f4d8-b26b-424e-9307-7a7a62e79750*) foram bloqueadas. As partições foram bloqueadas porque o tempo de execução não pôde colocar réplicas primárias (*WaitForPrimaryPlacement*) em nós de destino *Node1* e *Node4*.

O comando **Get-ServiceFabricNode** pode ser usado para verificar se esses dois nós estão no domínio de atualização *MYUD1*. O *UpgradePhase* diz *PostUpgradeSafetyCheck*, que significa que essas verificações de segurança estão ocorrendo depois que todos os nós no domínio de atualização concluíram a atualização. Todas essas informações combinadas apontam para um possível problema com a nova versão do código do aplicativo. Os problemas mais comuns são erros de serviço em aberto ou promoção para caminhos de código principal.

Uma *UpgradePhase* de *PreUpgradeSafetyCheck* significa que houve problemas na preparação do domínio de atualização antes de a atualização ser executada. Nesse caso, os problemas mais comuns são erros de serviço no fechamento ou rebaixamento de caminhos de código principal.

O **UpgradeState** atual é *RollingBackCompleted*; portanto, a atualização original deve ter sido realizada com uma reversão **FailureAction**, que automaticamente reverteu a atualização em caso de falha. Se a atualização original tivesse sido realizada com um **FailureAction**manual, a atualização estaria em um estado suspenso para permitir a depuração dinâmica do aplicativo.

Em casos raros, o campo **UpgradeDomainProgressAtFailure** poderá estar vazio se a atualização geral atingir o tempo limite assim que o sistema concluir todo o trabalho para o domínio de atualização atual. Se isso acontecer, experimente aumentar os valores de parâmetro de atualização **UpgradeTimeout** e **UpgradeDomainTimeout** e tente novamente a atualização.

### <a name="investigate-health-check-failures"></a>Investigar falhas de verificação de integridade

As falhas de verificação de integridade podem ser acionadas por vários problemas que podem ocorrer depois que todos os nós em um domínio de atualização terminam a atualização e passam por todas as verificações de segurança. A saída após este parágrafo é típica de uma falha na atualização devido a falhas de verificação de integridade. O campo **UnhealthyEvaluations** captura um instantâneo das verificações de integridade com falha no momento da atualização de acordo com a [política de integridade](service-fabric-health-introduction.md)especificada.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

A investigação de falhas de verificação de integridade exige primeiro uma compreensão do modelo de integridade do Service Fabric. Mas mesmo sem essa compreensão detalhada, podemos ver que dois serviços não estão íntegros: *fabric:/DemoApp/Svc3* e *fabric:/DemoApp/Svc2*, junto com os relatórios de integridade de erro ("InjectedFault" nesse caso). Neste exemplo, dois de quatro serviços não estão íntegros, abaixo da meta padrão de 0% de não integridade (*MaxPercentUnhealthyServices*).

A atualização foi suspensa com falha, especificando um **FailureAction** manual quando iniciada a atualização. Esse modo permite investigar o sistema dinâmico no estado de falha antes de realizar qualquer outra ação.

### <a name="recover-from-a-suspended-upgrade"></a>Recuperar de uma atualização suspensa

Com uma reversão de **FailureAction**, não há nenhuma recuperação necessária, já que a atualização é revertida automaticamente após a falha. Com uma **FailureAction**manual, há várias opções de recuperação:

1.  disparar uma reversão
2. Continuar com o restante da atualização manualmente
3. Continuar a atualização monitorada

O comando **Start-ServiceFabricApplicationRollback** pode ser usado a qualquer momento para iniciar a reversão do aplicativo. Depois que o comando retornar com êxito, a solicitação de reversão foi registrada no sistema e começa em breve.

O comando **ServiceFabricApplicationUpgrade retomar** pode ser usado para percorrer o restante da atualização manualmente, um domínio de atualização de cada vez. Nesse modo, somente verificações de segurança são executadas pelo sistema. Nenhuma outra verificação de integridade é executada. Esse comando só pode ser usado quando o *UpgradeState* mostra *RollingForwardPending*, o que significa que o domínio de atualização atual concluiu a atualização, mas o próximo ainda não foi iniciado (pendente).

O comando **ServiceFabricApplicationUpgrade atualização** pode ser usado para retomar a atualização monitorada com a realização tanto da verificação de segurança quanto da de integridade.

```powershell
Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored
```

```Output
UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

A atualização continua no domínio de atualização no qual ela foi suspensa da última vez e usa os mesmos parâmetros de atualização e políticas de integridade de antes. Se necessário, qualquer um dos parâmetros de atualização e políticas de integridade mostrados na saída anterior poderá ser alterado no mesmo comando ao retomar a atualização. Neste exemplo, a atualização foi reiniciada no modo monitorado, com os parâmetros e as políticas de integridade inalteradas.

## <a name="further-troubleshooting"></a>Solução de problemas adicionais

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>O Service Fabric não está seguindo as políticas de integridade especificadas

Possível causa 1:

O Service Fabric converte todas as porcentagens em números reais de entidades (por exemplo, réplicas, partições e serviços) para avaliação de integridade e sempre arredonda para cima para entidades inteiras. Por exemplo, se o máximo de *MaxPercentUnhealthyReplicasPerPartition* for 21% e houver cinco réplicas, o Service Fabric permitirá até duas réplicas não íntegras (ou seja, `Math.Ceiling (5*0.21)`). Portanto, as políticas de integridade devem ser definidas adequadamente.

Possível causa 2:

as políticas de integridade são especificadas em termos de percentuais do total de serviço e não em instâncias de serviço específicas. Por exemplo, antes de uma atualização, se um aplicativo tem quatro instâncias de serviço A, B, C e D, em que o serviço D não está íntegro, mas tem pouco impacto no aplicativo. Vamos ignorar o serviço conhecido não íntegro D durante a atualização e definir o parâmetro *MaxPercentUnhealthyServices* a 25%, supondo que apenas A, B e C precisem ser íntegros.

No entanto, durante a atualização, D podem se tornar íntegro enquanto C se torna não íntegro. A atualização ainda teria êxito porque somente 25% dos serviços não estão íntegros. No entanto, isso poderia resultar em erros inesperados devido ao fato de C estar inesperadamente não íntegro em vez de D. Nessa situação, D deve ser modelado como um tipo diferente de serviço de A, B e C. Como as políticas de integridade são especificadas baseadas no tipo de serviço, podem ser aplicados limites de percentual de não integridade diferentes para diferentes serviços. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Eu não especifiquei uma política de integridade para a atualização do aplicativo, mas a atualização ainda falha em alguns tempos limite que nunca especifiquei

Quando as políticas de integridade não são fornecidas para a solicitação de atualização, elas são tiradas do *ApplicationManifest.xml* da versão atual do aplicativo. Por exemplo, se você estiver atualizando o Aplicativo X da versão 1.0 para a versão 2.0, as políticas de integridade do aplicativo especificadas na versão 1.0 serão usadas. Se uma política de integridade diferente tiver que ser usada para a atualização, a política precisa ser especificada como parte da chamada de API de atualização de aplicativo. As políticas especificadas como parte da chamada à API somente se aplicam durante a atualização. Depois que a atualização é concluída, as políticas especificadas no *ApplicationManifest.xml* são usadas.

### <a name="incorrect-time-outs-are-specified"></a>Os tempos limite incorretos estão especificados

Você pode já ter se perguntado sobre o que acontece quando os tempos limite são definidos de forma inconsistente. Por exemplo, você pode ter um *UpgradeTimeout* inferior ao *UpgradeDomainTimeout*. A resposta é que um erro é retornado. Erros serão retornados se *UpgradeDomainTimeout* for menor do que a soma de *HealthCheckWaitDuration* e *HealthCheckRetryTimeout* ou se *UpgradeDomainTimeout* for menor do que a soma de *HealthCheckWaitDuration* e *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Minhas atualizações estão demorando muito

O tempo para concluir uma atualização depende das verificações de integridade e dos tempos limite especificados. Verificações de integridade e tempos limite dependem de quanto tempo demora para copiar, implantar e estabilizar o aplicativo. Muita agressividade com tempos limite pode significar mais atualizações com falha e, portanto, recomendamos um início mais conservador com tempos limites mais longos.

Eis uma recapitulação de como os tempos limite interagem com os tempos de atualização:

A atualização de um domínio de atualização não pode ser concluída mais rapidamente do que *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

A falha de atualização não pode ocorrer mais rápido do que *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

O tempo de atualização para um domínio de atualização é limitado pelo *UpgradeDomainTimeout*.  Se *HealthCheckRetryTimeout* e *HealthCheckStableDuration* são diferentes de zero e a integridade do aplicativo alterna entre boa e ruim, a atualização pode esgotar o tempo limite em *UpgradeDomainTimeout*. *UpgradeDomainTimeout* inicia a contagem regressiva quando a atualização do domínio de atualização atual começa.

## <a name="next-steps"></a>Próximas etapas

[Atualização do aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta você durante a atualização de aplicativo usando o Visual Studio.

[Atualização do aplicativo usando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) orienta você uma atualização de aplicativo usando o PowerShell.

Controle como seu aplicativo é atualizado usando [Parâmetros de Atualização](service-fabric-application-upgrade-parameters.md).

Torne suas atualizações de aplicativo compatíveis aprendendo a usar a [Serialização de Dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como usar a funcionalidade avançada ao atualizar seu aplicativo consultando os [Tópicos Avançados](service-fabric-application-upgrade-advanced.md).