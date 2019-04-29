---
title: 'Atualização de aplicativos: parâmetros de atualização | Microsoft Docs'
description: Descreve os parâmetros relacionados à atualização de um aplicativo do Service Fabric, incluindo verificações de integridade a serem executadas e políticas para desfazer automaticamente a atualização.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: a4170ac6-192e-44a8-b93d-7e39c92a347e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2018
ms.author: subramar
ms.openlocfilehash: 9a93c0993ee45e72b11b023982dfbbe8c6528272
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614389"
---
# <a name="application-upgrade-parameters"></a>Parâmetros de atualização de aplicativo
Este artigo descreve os vários parâmetros que se aplicam durante a atualização de um aplicativo do Service Fabric do Azure. Parâmetros de atualização de aplicativo controlam o tempo limite e as verificações de integridade que são aplicadas durante a atualização, e especificam as políticas que devem ser aplicadas quando uma atualização falha. Parâmetros de aplicativo aplicam-se a atualizações usando:
- PowerShell
- Visual Studio
- SFCTL
- [REST](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

Atualizações de aplicativo são iniciadas por meio de um dos três modos de atualização selecionáveis pelo usuário. Cada modo tem o próprio conjunto de parâmetros do aplicativo:
- Monitorado
- Automático Não Monitorado
- Manual Não Monitorado

Os parâmetros obrigatórios e opcionais aplicáveis são descritos em cada seção da seguinte maneira.

## <a name="visual-studio-and-powershell-parameters"></a>Parâmetros do Visual Studio e PowerShell

Atualizações de aplicativo do Service Fabric usando o PowerShell usam o comando [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade). O modo de atualização é selecionado passando o parâmetro **Monitored**, **UnmonitoredAuto** ou **UnmonitoredManual** para [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade).

Parâmetros atualização de aplicativo do Visual Studio Service Fabric são definidos por meio da caixa de diálogo Configurações de Atualização do Visual Studio. O modo de atualização do Visual Studio é selecionado usando a caixa suspensa **Modo de Atualização** para **Monitored**, **UnmonitoredAuto** ou **UnmonitoredManual**. Para obter mais informações, veja [Configurar a atualização de um aplicativo do Service Fabric no Visual Studio](service-fabric-visualstudio-configure-upgrade.md).

### <a name="required-parameters"></a>Parâmetros obrigatórios
(PS=PowerShell, VS=Visual Studio)

| Parâmetro | Aplica-se A | DESCRIÇÃO |
| --- | --- | --- |
ApplicationName |PS| Nome do aplicativo que está sendo atualizado. Exemplos: fabric:/VisualObjects, fabric:/ClusterMonitor. |
ApplicationTypeVersion|PS|Versão do tipo de aplicativo visado pela atualização. |
FailureAction |PS, VS|Valores permitidos são **Rollback**, **Manual** e **Invalid**. A ação de compensação a ser executada quando uma atualização *Monitorada* encontra uma política de monitoramento ou violações da política de integridade. <br>**Reversão** especifica que a atualização será automaticamente revertida para a versão pré-atualização. <br>**Manual** indica que a atualização será mudada para o modo de atualização *UnmonitoredManual*. <br>**Inválido** indica que a ação de falha é inválida.|
Monitorado |PS|Indica que o modo de atualização é monitorado. Depois que o cmdlet terminar uma atualização para um domínio de atualização, se a integridade do domínio de atualização e o cluster atenderem às políticas de integridade que você definir, o Service Fabric atualizará o próximo domínio de atualização. Se o domínio ou o cluster de atualização falhar em cumprir as políticas de integridade, a atualização falhará e o Service Fabric reverterá a atualização para o domínio de atualização ou para o modo manual, conforme especificado pela política. Esse é o modo recomendado para atualizações de aplicativo em um ambiente de produção. |
UpgradeMode | VS | Os valores permitidos são **Monitored** (padrão), **UnmonitoredAuto** ou **UnmonitoredManual**. Consulte os parâmetros do PowerShell para cada modo neste artigo para obter detalhes. |
UnmonitoredAuto | PS | Indica que o modo de atualização e não monitorado automático. Depois que o Service Fabric atualiza um domínio de atualização, o Service Fabric atualiza o próximo domínio de atualização, independentemente do estado de integridade do aplicativo. Esse modo não é recomendado para produção e só é útil durante o desenvolvimento de um aplicativo. |
UnmonitoredManual | PS | Indica que o modo de atualização e não monitorado manual. Depois que o Service Fabric atualiza um domínio de atualização, ele aguarda você atualizar o próximo domínio de atualização usando o cmdlet *Resume-ServiceFabricApplicationUpgrade*. |

### <a name="optional-parameters"></a>Parâmetros opcionais

Os parâmetros de avaliação de integridade são opcionais. Se os critérios de avaliação de integridade não forem especificados ao iniciar uma atualização, o Service Fabric usará as políticas de integridade do aplicativo especificadas no ApplicationManifest.xml da instância do aplicativo.

Use a barra de rolagem horizontal na parte inferior da tabela para exibir o campo de descrição completo.

(PS=PowerShell, VS=Visual Studio)

| Parâmetro | Aplica-se A | DESCRIÇÃO |
| --- | --- | --- |
| ApplicationParameter |PS, VS| Especifica as substituições para os parâmetros do aplicativo.<br>Parâmetros de aplicativo do PowerShell são especificados como pares de nome/valor da tabela de hash. Por exemplo, @{ "VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1" }.<br>Os parâmetros de aplicativo do Visual Studio podem ser especificados na caixa de diálogo Publicar Aplicativo do Service Fabric no campo **Arquivo de Parâmetros do Aplicativo**.
| Confirmar |PS| Os valores permitidos são **True** e **False**. Solicita sua confirmação antes de executar o cmdlet. |
| ConsiderWarningAsError |PS, VS |Os valores permitidos são **True** e **False**. O valor padrão é **False**. Trata os eventos de integridade de aviso do aplicativo como erros ao avaliar a integridade do aplicativo durante a atualização. Por padrão, o Service Fabric não avalia os eventos de integridade de aviso como falhas (erros); portanto, a atualização pode continuar mesmo se houver eventos de aviso. |
| DefaultServiceTypeHealthPolicy | PS, VS |Especifica a política de integridade para o tipo de serviço padrão a ser usado para a atualização monitorada no formato MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices. Por exemplo, 5,10,15 indicam os seguintes valores: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
| Force | PS, VS | Os valores permitidos são **True** e **False**. Indica que o processo de atualização ignorará a mensagem de aviso e forçará a atualização mesmo quando o número de versão não tiver mudado. Isso é útil para teste local, mas não é recomendado para uso em um ambiente de produção, pois exige remover a implantação existente, provocando tempo de inatividade e possível perda de dados. |
| ForceRestart |PS, VS |Se você atualizar uma configuração ou um pacote de dados sem atualizar o código de serviço, o serviço será reiniciado somente se a propriedade ForceRestart estiver definida como **True**. Quando a atualização é concluída, a Malha do Serviço notifica o serviço de que um novo pacote de configuração ou pacote de dados está disponível. O serviço é responsável pela aplicação das alterações. Se necessário, o serviço pode se reiniciar. |
| HealthCheckRetryTimeoutSec |PS, VS |A duração (em segundos) em que a Malha de Serviço continua a executar a avaliação de integridade antes de declarar a atualização com falha. O padrão é 600 segundos. Esta duração é iniciada depois que a *HealthCheckWaitDurationSec* for atingida. Nesse *HealthCheckRetryTimeout*, o Service Fabric pode executar várias verificações de integridade do aplicativo. O valor padrão é 10 minutos e deve ser adequadamente personalizado para o seu aplicativo. |
| HealthCheckStableDurationSec |PS, VS |A duração (em segundos) para verificar se o aplicativo é estável antes de passar para o próximo domínio de atualização ou concluir a atualização. Essa duração de espera é usada para impedir que alterações na integridade deixem de ser detectadas logo após a verificação de integridade. O valor padrão é 120 segundos e deve ser adequadamente personalizado para o seu aplicativo. |
| HealthCheckWaitDurationSec |PS, VS | O tempo de espera (em segundos) após a conclusão da atualização no domínio de atualização antes que o Service Fabric avalie a integridade do aplicativo. Essa duração também pode ser considerada como o tempo pelo qual um aplicativo deve estar em execução antes que possa ser considerado íntegro. Se a verificação de integridade for aprovada, o processo de atualização passa para o próximo domínio de atualização.  Se a verificação de integridade falhar, o Service Fabric aguardará [UpgradeHealthCheckInterval](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#clustermanager) antes de tentar novamente a verificação de integridade até que o *HealthCheckRetryTimeoutSec* seja atingido. O valor padrão recomendado é 0 segundos. |
| MaxPercentUnhealthyDeployedApplications|PS, VS |O valor padrão e recomendado é 0. Especifica o número máximo de aplicativos implantados (confira a [seção Integridade](service-fabric-health-introduction.md)) que podem comprometer a integridade antes que o aplicativo seja considerado não íntegro e sofra falha na atualização. Esse parâmetro define a integridade do aplicativo no nó e ajuda a detectar problemas durante a atualização. Tipicamente, as réplicas do aplicativo têm a carga balanceada para outro nó, o que permite que o aplicativo pareça íntegro, permitindo assim que a atualização continue. Ao especificar uma integridade *MaxPercentUnhealthyDeployedApplications* estrita, o Service Fabric pode detectar um problema com o pacote de aplicativos rapidamente e ajudar a produzir em uma atualização fail fast. |
| MaxPercentUnhealthyServices |PS, VS |Um parâmetro para *DefaultServiceTypeHealthPolicy* e *ServiceTypeHealthPolicyMap*. O valor padrão e recomendado é 0. Especifica o número máximo de serviços na instância do aplicativo que podem não estar íntegros antes que o aplicativo seja considerado não íntegro e tenha atualização com falha. |
| MaxPercentUnhealthyPartitionsPerService|PS, VS |Um parâmetro para *DefaultServiceTypeHealthPolicy* e *ServiceTypeHealthPolicyMap*. O valor padrão e recomendado é 0. Especifica o número máximo de partições em um serviço que podem não estar íntegras antes que o serviço seja considerado não íntegro. |
| MaxPercentUnhealthyReplicasPerPartition|PS, VS |Um parâmetro para *DefaultServiceTypeHealthPolicy* e *ServiceTypeHealthPolicyMap*. O valor padrão e recomendado é 0. Especifica o número máximo de réplicas em partição que podem não estar íntegras antes que a partição seja considerada não íntegra. |
| ServiceTypeHealthPolicyMap | PS, VS | Representa a política de integridade usada para avaliar a integridade dos serviços que pertencem a um tipo de serviço. Utiliza uma entrada de tabela de hash no seguinte formato: @ {"ServiceTypeName": "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"} For example: @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" } |
| TimeoutSec | PS, VS | Especifica o período de tempo limite em segundos para a operação. |
| UpgradeDomainTimeoutSec |PS, VS |Tempo máximo (em segundos) para atualizar um único domínio de atualização. Se esse tempo limite for atingido, a atualização será interrompida e continuará com base na configuração para *FailureAction*. O valor padrão nunca é (infinito) e deve ser personalizado adequadamente para o seu aplicativo. |
| UpgradeReplicaSetCheckTimeoutSec |PS, VS |Medido em segundos.<br>**Serviço sem monitoração de estado**: em um único domínio de atualização, o Service Fabric tenta garantir que haja instâncias adicionais do serviço disponíveis. Se a contagem de instâncias de destino for mais de um, o Service Fabric esperará que mais de uma instância fique disponível, até um valor de tempo limite máximo. Esse tempo limite é especificado usando a propriedade *UpgradeReplicaSetCheckTimeoutSec*. Se o tempo limite expirar, o Service Fabric continua com a atualização, independentemente do número de instâncias de serviço. Se a contagem de instâncias de destino for um, a Malha do Serviço não espera e prossegue imediatamente com a atualização.<br><br>**Serviço com monitoração de estado**: em um único domínio de atualização, o Service Fabric tenta garantir que o conjunto de réplicas tenha um quorum. O Service Fabric aguarda pela disponibilidade de um quorum até um valor de tempo limite máximo (especificado pela propriedade *UpgradeReplicaSetCheckTimeoutSec*). Se o tempo limite expirar, o Service Fabric continuará com a atualização, independentemente de quorum. Essa configuração está definida como nunca (infinito) durante o roll forward e 1200 segundos durante a reversão. |
| UpgradeTimeoutSec |PS, VS |Um tempo limite (em segundos) que se aplica a toda a atualização. Se esse tempo limite for atingido, a atualização será interrompida e a *FailureAction* será disparada. O valor padrão nunca é (infinito) e deve ser personalizado adequadamente para o seu aplicativo. |
| WhatIf | PS | Os valores permitidos são **True** e **False**. Mostra o que aconteceria se o cmdlet fosse executado. O cmdlet não é executado. |

Os critérios *MaxPercentUnhealthyServices*, *MaxPercentUnhealthyPartitionsPerService* e *MaxPercentUnhealthyReplicasPerPartition* podem ser especificados por tipo de serviço para uma instância do aplicativo. Definir esses parâmetros por serviço permite que um aplicativo contenha tipos de serviços diferentes com políticas de avaliação diferentes. Por exemplo, um tipo de serviço de gateway sem monitoração de estado pode ter um *MaxPercentUnhealthyPartitionsPerService* diferente de um tipo de serviço de mecanismo com estado para uma determinada instância de aplicativo.

## <a name="sfctl-parameters"></a>Parâmetros SFCTL

As atualizações de aplicativos do Service Fabric usando a CLI do Service Fabric usam o comando [sfctl application upgrade](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade) junto com os seguintes parâmetros obrigatórios e opcionais.

### <a name="required-parameters"></a>Parâmetros obrigatórios

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| application-id  |ID do aplicativo que está sendo atualizado. <br> Normalmente, esse é o nome completo do aplicativo sem o esquema de URI 'fabric:'. A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo é ' fabric: / myapp/app1 ', a identidade do aplicativo será ' myapp\~app1' em 6.0 + e "meuaplicativo/Aplicativo1" nas versões anteriores.|
application-version |Versão do tipo de aplicativo visado pela atualização.|
parameters  |Uma lista codificada em JSON de substituições de parâmetro de aplicativo a serem aplicadas ao atualizar o aplicativo.|

### <a name="optional-parameters"></a>Parâmetros opcionais

| Parâmetro | DESCRIÇÃO |
| --- | --- |
default-service-health-policy | Especificação [JSON](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) codificada da política de integridade usada por padrão para avaliar a integridade de um tipo de serviço. O mapa está vazio por padrão. |
failure-action | Valores permitidos são **Rollback**, **Manual** e **Invalid**. A ação de compensação a ser executada quando uma atualização *Monitorada* encontra uma política de monitoramento ou violações da política de integridade. <br>**Reversão** especifica que a atualização será automaticamente revertida para a versão pré-atualização. <br>**Manual** indica que a atualização será mudada para o modo de atualização *UnmonitoredManual*. <br>**Inválido** indica que a ação de falha é inválida.|
force-restart | Se você atualizar uma configuração ou um pacote de dados sem atualizar o código de serviço, o serviço será reiniciado somente se a propriedade ForceRestart estiver definida como **True**. Quando a atualização é concluída, a Malha do Serviço notifica o serviço de que um novo pacote de configuração ou pacote de dados está disponível. O serviço é responsável pela aplicação das alterações. Se necessário, o serviço pode se reiniciar. |
health-check-retry-timeout | A quantidade de tempo para tentar novamente a avaliação de integridade quando o aplicativo ou cluster estiver não íntegro antes de *FailureAction* ser executado. Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. Padrão: PT0H10M0S. |
health-check-stable-duration | A quantidade de tempo que o aplicativo ou o cluster deve permanecer íntegro antes que a atualização passe para o próximo domínio de atualização. Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. Padrão: PT0H2M0S. |
health-check-wait-duration | A quantidade de tempo de espera após a conclusão de um domínio de atualização, antes de aplicar as políticas de integridade. Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. Padrão: 0.|
max-unhealthy-apps | O valor padrão e recomendado é 0. Especifica o número máximo de aplicativos implantados (confira a [seção Integridade](service-fabric-health-introduction.md)) que podem comprometer a integridade antes que o aplicativo seja considerado não íntegro e sofra falha na atualização. Esse parâmetro define a integridade do aplicativo no nó e ajuda a detectar problemas durante a atualização. Tipicamente, as réplicas do aplicativo têm a carga balanceada para outro nó, o que permite que o aplicativo pareça íntegro, permitindo assim que a atualização continue. Ao especificar um funcionamento *max-unhealthy-apps* estrito, o Service Fabric pode detectar um problema com o pacote de aplicativos rapidamente e ajudar a produzir uma atualização rápida com falha. Representado como um número entre 0 e 100. |
modo | Os valores permitidos são **Monitored**, **UpgradeMode**, **UnmonitoredAuto**, **UnmonitoredManual**. O padrão é **UnmonitoredAuto**. Consulte a seção *Parâmetros Necessários* do Visual Studio e PowerShell para obter as descrições desses valores.|
replica-set-check-timeout |Medido em segundos. <br>**Serviço sem monitoração de estado**: em um único domínio de atualização, o Service Fabric tenta garantir que haja instâncias adicionais do serviço disponíveis. Se a contagem de instâncias de destino for mais de um, o Service Fabric esperará que mais de uma instância fique disponível, até um valor de tempo limite máximo. Esse tempo limite é especificado usando a propriedade *replica-set-check-timeout*. Se o tempo limite expirar, o Service Fabric continua com a atualização, independentemente do número de instâncias de serviço. Se a contagem de instâncias de destino for um, a Malha do Serviço não espera e prossegue imediatamente com a atualização.<br><br>**Serviço com monitoração de estado**: em um único domínio de atualização, o Service Fabric tenta garantir que o conjunto de réplicas tenha um quorum. O Service Fabric aguarda a disponibilidade de um quórum, até um valor de tempo limite máximo (especificado pela propriedade *replica-set-check-timeout*). Se o tempo limite expirar, o Service Fabric continuará com a atualização, independentemente de quorum. Essa configuração está definida como nunca (infinito) durante o roll forward e 1200 segundos durante a reversão. |
service-health-policy | Mapa codificado em JSON com a política de integridade de tipo de serviço por nome de tipo de serviço. O mapa está vazio por padrão. [Parâmetro de formato JSON.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap). O JSON da parte "Valor" contém **MaxPercentUnhealthyServices**, **MaxPercentUnhealthyPartitionsPerService** e **MaxPercentUnhealthyReplicasPerPartition**. Consulte a seção Parâmetros Opcionais do Visual Studio e PowerShell para obter as descrições desses parâmetros.
Tempo limite | Especifica o período de tempo limite em segundos para a operação. Padrão: 60. |
upgrade-domain-timeout | A quantidade de tempo que cada domínio de atualização deve concluir antes de *FailureAction* ser executado. Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. O valor padrão nunca é (infinito) e deve ser personalizado adequadamente para o seu aplicativo. Padrão: P10675199DT02H48M05.4775807S. |
upgrade-timeout | A quantidade de tempo que cada domínio de atualização deve concluir antes de *FailureAction* ser executado. Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. O valor padrão nunca é (infinito) e deve ser personalizado adequadamente para o seu aplicativo. Padrão: P10675199DT02H48M05.4775807S.|
warning-as-error | Os valores permitidos são **True** e **False**. O valor padrão é **False**. Pode ser passado como um sinalizador. Trata os eventos de integridade de aviso do aplicativo como erros ao avaliar a integridade do aplicativo durante a atualização. Por padrão, o Service Fabric não avalia os eventos de integridade de aviso como falhas (erros); portanto, a atualização pode continuar mesmo se houver eventos de aviso. |

## <a name="next-steps"></a>Próximas etapas
[Atualização do aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta você durante a atualização de aplicativo usando o Visual Studio.

[Atualização do aplicativo usando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) orienta você uma atualização de aplicativo usando o PowerShell.

A [Atualização do aplicativo usando a CLI do Service Fabric no Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) orienta você ao longo de uma atualização de aplicativo usando a CLI do Service Fabric.

[Atualizar seu aplicativo usando o plug-in Eclipse do Service Fabric](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Torne suas atualizações de aplicativo compatíveis aprendendo a usar a [Serialização de Dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como usar a funcionalidade avançada ao atualizar seu aplicativo consultando os [Tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrija problemas comuns em atualizações de aplicativo consultando as etapas em [Solução de problemas de atualizações de aplicativo](service-fabric-application-upgrade-troubleshooting.md).
