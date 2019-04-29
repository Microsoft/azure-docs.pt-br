---
title: Diferenças no Azure Service Fabric entre Linux e Windows | Microsoft Docs
description: As diferenças entre o Azure Service Fabric no Linux e o Azure Service Fabric no Windows.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 829a3ea6e8fafa24831f1731e402a3991c0258c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60719984"
---
# <a name="differences-between-service-fabric-on-linux-and-windows"></a>As diferenças entre o Service Fabric no Linux e no Windows

Há alguns recursos que têm suporte no Windows, mas ainda não no Linux. Eventualmente, os conjuntos de recursos estarão em paridade e com cada versão esse intervalo de recursos será reduzido. As diferenças a seguir existem entre as versões mais recentes disponíveis.

* O Envoy (proxy reverso) está em versão prévia no Linux
* O instalador autônomo para Linux ainda não está disponível no Linux
* Redirecionamento de console (sem suporte em clusters de produção do Linux ou Windows)
* O Serviço de Análise de Falha (FAS) no Linux
* Serviço DNS para serviços do Service Fabric (o serviço DNS tem suporte para contêineres no Linux)
* Equivalentes de comando da CLI de determinados comandos do Powershell (lista abaixo, a maior parte se aplica somente aos clusters autônomos)

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>Cmdlets do PowerShell que não funcionam em um cluster Linux do Service Fabric

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy



## <a name="next-steps"></a>Próximas etapas
* [Preparar seu ambiente de desenvolvimento no Linux](service-fabric-get-started-linux.md)
* [Preparar seu ambiente de desenvolvimento no OSX](service-fabric-get-started-mac.md)
* [Criar e implantar seu primeiro aplicativo Java de do Service Fabric no Linux usando Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implantar seu primeiro aplicativo Java do Service Fabric no Linux usando o plug-in do Service Fabric para o Eclipse](service-fabric-get-started-eclipse.md)
* [Criar seu primeiro aplicativo do CSharp no Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Usar a CLI do Service Fabric para gerenciar seus aplicativos](service-fabric-application-lifecycle-sfctl.md)
