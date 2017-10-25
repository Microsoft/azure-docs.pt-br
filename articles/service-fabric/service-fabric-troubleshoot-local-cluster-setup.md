---
title: "Solucionar problemas de sua configuração de cluster do Service Fabric local | Microsoft Docs"
description: "Este artigo aborda um conjunto de sugestões para a solução de problemas do cluster de desenvolvimento local"
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: mikkelhegn
ms.openlocfilehash: aa393f884b564cee81fcf75cc2eff895efea9471
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Solucionar problemas de configuração do cluster de desenvolvimento local
Se você tiver um problema ao interagir com o cluster de desenvolvimento local do Service Fabric do Azure, examine as sugestões a seguir para ver as possíveis soluções.

## <a name="cluster-setup-failures"></a>Falhas de configuração do cluster
### <a name="cannot-clean-up-service-fabric-logs"></a>Não é possível limpar os logs da Malha do Serviço
#### <a name="problem"></a>Problema
Ao executar o script DevClusterSetup, você vê um erro como este:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Solução
Feche a janela atual do PowerShell e inicie uma nova janela como um administrador. Agora você pode executar o script com êxito.

## <a name="cluster-connection-failures"></a>Falhas de conexão do cluster
### <a name="service-fabric-powershell-cmdlets-are-not-recognized-in-azure-powershell"></a>Cmdlets do PowerShell do Service Fabric não são reconhecidos no Azure PowerShell
#### <a name="problem"></a>Problema
Se você tentar executar qualquer um dos cmdlets do PowerShell do Service Fabric, como `Connect-ServiceFabricCluster` em uma janela do Azure PowerShell, ele falhará, dizendo que o cmdlet não é reconhecido. A razão para isso é que o Azure PowerShell usa a versão de 32 bits do Windows PowerShell (mesmo em versões de sistema operacional de 64 bits), enquanto os cmdlets do Service Fabric só funcionam em ambientes de 64 bits.

#### <a name="solution"></a>Solução
Sempre execute os cmdlets do Service Fabric diretamente do Windows PowerShell.

> [!NOTE]
> A versão mais recente do Azure PowerShell não cria um atalho especial, então isso não deverá mais ocorrer.
> 
> 

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
Feche a janela atual do PowerShell e inicie uma nova janela como um administrador. Agora você pode se conectar com êxito.

### <a name="fabric-connection-denied-exception"></a>Exceção de Conexão ao Fabric Negada
#### <a name="problem"></a>Problema
Ao depurar no Visual Studio, você obtém um erro FabricConnectionDeniedException.

#### <a name="solution"></a>Solução
Geralmente, esse erro ocorre quando você tenta iniciar um processo de host de serviço manualmente, em vez de permitir que o tempo de execução do Service Fabric inicie-o para você.

Verifique se você não possui um projeto de serviço definido como projeto de inicialização na sua solução. Somente projetos de aplicativo da Malha do Serviço devem ser definidos como projetos de inicialização.

> [!TIP]
> Se, após a instalação, o cluster local começar a se comportar de forma anormal, você poderá redefini-lo usando o aplicativo de bandeja de sistema de gerenciador de cluster local. Isso remove o cluster existente e configura um novo. Observe que todos os aplicativos implantados e os dados associados são removidos.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* [Entender e solucionar problemas de cluster com relatórios de integridade do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualizando o cluster com o Explorador do Service Fabric](service-fabric-visualizing-your-cluster.md)

