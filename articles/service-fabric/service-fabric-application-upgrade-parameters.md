---
title: "Atualização de aplicativos: parâmetros de atualização | Microsoft Docs"
description: "Descreve os parâmetros relacionados à atualização de um aplicativo do Service Fabric, incluindo verificações de integridade a serem executadas e políticas para desfazer automaticamente a atualização."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: a4170ac6-192e-44a8-b93d-7e39c92a347e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: f09dad590f32c10f75484bba9afb7ea60f29d81e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="application-upgrade-parameters"></a>Parâmetros de atualização de aplicativo
Este artigo descreve os vários parâmetros que se aplicam durante a atualização de um aplicativo do Service Fabric do Azure. Os parâmetros incluem o nome e a versão do aplicativo. Eles são botões que controlam o tempo limite e as verificações de integridade que são aplicadas durante a atualização, e especificam as políticas que devem ser aplicadas quando uma atualização falha.

<br>

| Parâmetro | Descrição |
| --- | --- |
| ApplicationName |Nome do aplicativo que está sendo atualizado. Exemplos: fabric:/VisualObjects, fabric:/ClusterMonitor |
| TargetApplicationTypeVersion |Versão do tipo de aplicativo visado pela atualização. |
| FailureAction |A ação tomada pelo Service Fabric se a atualização falhar. O aplicativo pode ser revertido para a versão anterior à atualização (reversão) ou a atualização pode ser interrompida no domínio de atualização atual. No último caso, o modo de atualização também será alterado para Manual. Os valores permitidos são Manual e Reversão. |
| HealthCheckWaitDurationSec |O tempo de espera (em segundos) após a conclusão da atualização no domínio de atualização antes que o Service Fabric avalie a integridade do aplicativo. Essa duração também pode ser considerada como o tempo pelo qual um aplicativo deve estar em execução antes que possa ser considerado íntegro. Se a verificação de integridade for aprovada, o processo de atualização passa para o próximo domínio de atualização.  Se a verificação de integridade falhar, a Malha do Serviço aguarda um intervalo (o UpgradeHealthCheckInterval) antes de tentar novamente a verificação de integridade até que o HealthCheckRetryTimeout seja atingido. O valor padrão recomendado é 0 segundos. |
| HealthCheckRetryTimeoutSec |A duração (em segundos) em que a Malha de Serviço continua a executar a avaliação de integridade antes de declarar a atualização com falha. O padrão é 600 segundos. Esta duração é iniciada depois que a HealthCheckWaitDuration for atingida. Nesse HealthCheckRetryTimeout, a Malha de Serviço pode executar várias verificações de integridade no aplicativo. O valor padrão é 10 minutos e deve ser adequadamente personalizado para o seu aplicativo. |
| HealthCheckStableDurationSec |A duração (em segundos) para verificar se o aplicativo é estável antes de passar para o próximo domínio de atualização ou concluir a atualização. Essa duração de espera é usada para impedir que alterações na integridade deixem de ser detectadas logo após a verificação de integridade. O valor padrão é 120 segundos e deve ser adequadamente personalizado para o seu aplicativo. |
| UpgradeDomainTimeoutSec |Tempo máximo (em segundos) para atualizar um único domínio de atualização. Se esse tempo limite for atingido, a atualização será interrompida e continuará com base na configuração para UpgradeFailureAction. O valor padrão nunca é (infinito) e deve ser personalizado adequadamente para o seu aplicativo. |
| UpgradeTimeout |Um tempo limite (em segundos) que se aplica a toda a atualização. Se esse tempo limite for atingido, a atualização é interrompida e a UpgradeFailureAction será disparada. O valor padrão nunca é (infinito) e deve ser personalizado adequadamente para o seu aplicativo. |
| UpgradeHealthCheckInterval |A frequência com que o status de integridade é verificado. Este parâmetro é especificado na seção ClusterManager do *manifesto* do *cluster* e não é especificado como parte do cmdlet de atualização. O valor padrão é de 60 segundos. |

<br>

## <a name="service-health-evaluation-during-application-upgrade"></a>Avaliação de integridade do serviço durante a atualização do aplicativo
<br>
Os critérios de avaliação de integridade são opcionais. Se os critérios de avaliação de integridade não forem especificados ao iniciar uma atualização, o Service Fabric usará as políticas de integridade do aplicativo especificadas no ApplicationManifest.xml da instância do aplicativo.

<br>

| Parâmetro | Descrição |
| --- | --- |
| ConsiderWarningAsError |O valor padrão é Falso. Trata os eventos de integridade de aviso do aplicativo como erros ao avaliar a integridade do aplicativo durante a atualização. Por padrão, o Service Fabric não avalia os eventos de integridade de aviso como falhas (erros); portanto, a atualização pode continuar mesmo se houver eventos de aviso. |
| MaxPercentUnhealthyDeployedApplications |O valor padrão e recomendado é 0. Especifica o número máximo de aplicativos implantados (confira a [seção Integridade](service-fabric-health-introduction.md)) que podem comprometer a integridade antes que o aplicativo seja considerado não íntegro e sofra falha na atualização. Esse parâmetro define a integridade do aplicativo no nó e ajuda a detectar problemas durante a atualização. Tipicamente, as réplicas do aplicativo têm a carga balanceada para outro nó, o que permite que o aplicativo pareça íntegro, permitindo assim que a atualização continue. Ao especificar uma integridade MaxPercentUnhealthyDeployedApplications estrita, o Service Fabric pode detectar um problema com o pacote de aplicativos rapidamente e ajudar a produzir em uma atualização rápida em caso de falha. |
| MaxPercentUnhealthyServices |O valor padrão e recomendado é 0. Especifica o número máximo de serviços na instância do aplicativo que podem não estar íntegros antes que o aplicativo seja considerado não íntegro e tenha atualização com falha. |
| MaxPercentUnhealthyPartitionsPerService |O valor padrão e recomendado é 0. Especifica o número máximo de partições em um serviço que podem não estar íntegras antes que o serviço seja considerado não íntegro. |
| MaxPercentUnhealthyReplicasPerPartition |O valor padrão e recomendado é 0. Especifica o número máximo de réplicas em partição que podem não estar íntegras antes que a partição seja considerada não íntegra. |
| UpgradeReplicaSetCheckTimeout |**Serviço sem monitoração de estado**: em um único domínio de atualização, o Service Fabric tenta garantir que haja instâncias adicionais do serviço disponíveis. Se a contagem de instâncias de destino for mais de um, o Service Fabric esperará que mais de uma instância fique disponível, até um valor de tempo limite máximo. Esse tempo limite é especificado usando a propriedade UpgradeReplicaSetCheckTimeout. Se o tempo limite expirar, o Service Fabric continua com a atualização, independentemente do número de instâncias de serviço. Se a contagem de instâncias de destino for um, a Malha do Serviço não espera e prossegue imediatamente com a atualização. **Serviço com monitoração de estado**: em um único domínio de atualização, o Service Fabric tenta garantir que o conjunto de réplicas tenha um quorum. O Service Fabric aguarda pela disponibilidade de um quorum até um valor de tempo limite máximo (especificado pela propriedade UpgradeReplicaSetCheckTimeout). Se o tempo limite expirar, o Service Fabric continuará com a atualização, independentemente de quorum. Essa configuração está definida como nunca (infinito) durante o roll forward e 900 segundos durante a reversão. |
| ForceRestart |Se você atualizar uma configuração ou um pacote de dados sem atualizar o código de serviço, o serviço será reiniciado somente se a propriedade ForceRestart estiver definida como verdadeira. Quando a atualização é concluída, a Malha do Serviço notifica o serviço de que um novo pacote de configuração ou pacote de dados está disponível. O serviço é responsável pela aplicação das alterações. Se necessário, o serviço pode se reiniciar. |

<br>
<br>
Os critérios MaxPercentUnhealthyServices, MaxPercentUnhealthyPartitionsPerService e MaxPercentUnhealthyReplicasPerPartition podem ser especificados por tipo de serviço para uma instância do aplicativo. Definir esses parâmetros por serviço permite que um aplicativo contenha tipos de serviços diferentes com políticas de avaliação diferentes. Por exemplo, um tipo de serviço de gateway sem monitoração de estado pode ter um MaxPercentUnhealthyPartitionsPerService que é diferente de um tipo de serviço de mecanismo com monitoração de estado para uma determinada instância de aplicativo.

## <a name="next-steps"></a>Próximas etapas
[Atualização do aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta você durante a atualização de aplicativo usando o Visual Studio.

[Atualização do aplicativo usando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) orienta você uma atualização de aplicativo usando o PowerShell.

A [Atualização do aplicativo usando a CLI do Service Fabric no Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) orienta você ao longo de uma atualização de aplicativo usando a CLI do Service Fabric.

[Atualizar seu aplicativo usando o plug-in Eclipse do Service Fabric](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Torne suas atualizações de aplicativo compatíveis aprendendo a usar a [Serialização de Dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como usar a funcionalidade avançada ao atualizar seu aplicativo consultando os [Tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrija problemas comuns em atualizações de aplicativo consultando as etapas em [Solução de problemas de atualizações de aplicativo](service-fabric-application-upgrade-troubleshooting.md).
