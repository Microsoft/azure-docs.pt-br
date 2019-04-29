---
title: Solucionar problemas de sua configuração de cluster do Azure Service Fabric local | Microsoft Docs
description: Este artigo aborda um conjunto de sugestões para a solução de problemas do cluster de desenvolvimento local
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: chackdan
editor: ''
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 8bb32b2bded061bd19bcd7cfda4ef259a75b0626
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60864432"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Solucionar problemas de configuração do cluster de desenvolvimento local
Se você tiver um problema ao interagir com o cluster de desenvolvimento local do Service Fabric do Azure, examine as sugestões a seguir para ver as possíveis soluções.

## <a name="cluster-setup-failures"></a>Falhas de configuração do cluster
### <a name="cannot-clean-up-service-fabric-logs"></a>Não é possível limpar os logs da Malha do Serviço
#### <a name="problem"></a>Problema
Ao executar o script DevClusterSetup, você vê o seguinte erro:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Solução
Feche a janela atual do PowerShell e inicie uma nova janela como um administrador. Agora você pode executar adequadamente o script.

## <a name="cluster-connection-failures"></a>Falhas de conexão do cluster

### <a name="type-initialization-exception"></a>Exceção de Inicialização de Tipo
#### <a name="problem"></a>Problema
Ao conectar-se com o cluster no PowerShell, você vê o erro TypeInitializationException para System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Solução
A variável do caminho não foi definida corretamente durante a instalação. Saia do Windows e entre novamente. Isso atualiza o caminho.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Falha de conexão do cluster com “O objeto está fechado”
#### <a name="problem"></a>Problema
Uma chamada para Connect-ServiceFabricCluster falha com um erro parecido com este:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Solução
Feche a janela atual do PowerShell e inicie uma nova janela como um administrador.

### <a name="fabric-connection-denied-exception"></a>Exceção de Conexão ao Fabric Negada
#### <a name="problem"></a>Problema
Ao depurar no Visual Studio, você obtém um erro FabricConnectionDeniedException.

#### <a name="solution"></a>Solução
Esse erro normalmente ocorre quando você tenta iniciar um processo de host de serviço manualmente.

Verifique se você não possui um projeto de serviço definido como projeto de inicialização na sua solução. Somente projetos de aplicativo da Malha do Serviço devem ser definidos como projetos de inicialização.

> [!TIP]
> Se, após a instalação, o cluster local começar a se comportar de forma anormal, você poderá redefini-lo usando o aplicativo de bandeja de sistema de gerenciador de cluster local. Isso remove o cluster existente e configura um novo. Observe que todos os aplicativos implantados e os dados associados são removidos.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* [Entender e solucionar problemas de cluster com relatórios de integridade do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualizando o cluster com o Explorador do Service Fabric](service-fabric-visualizing-your-cluster.md)

