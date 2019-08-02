---
title: Depurar contêineres do Windows com Service Fabric e VS | Microsoft Docs
description: Saiba como depurar contêineres do Windows no Azure Service Fabric usando o Visual Studio de 2019.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: 07df215d9c3435d5ca3668b3c8d1587dbb99dca1
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621945"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>Como: Depurar contêineres do Windows no Azure Service Fabric usando o Visual Studio de 2019

Com o Visual Studio de 2019, você pode depurar aplicativos .NET em contêineres como serviços do Service Fabric. Este artigo mostra como configurar seu ambiente e, em seguida, depurar um aplicativo .NET em um contêiner em execução em um cluster do Service Fabric local.

## <a name="prerequisites"></a>Pré-requisitos

* No Windows 10, siga este guia de início rápido para [Configurar o Windows 10 para executar contêineres do Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* No Windows Server 2016, siga este guia de início rápido para [Configurar o Windows 2016 para executar contêineres do Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Configurar seu ambiente do Service Fabric local seguindo [Preparar seu ambiente de desenvolvimento no Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Configurar seu ambiente de desenvolvedor para depurar contêineres

1. Verifique se que o serviço Docker for Windows está em execução antes de prosseguir com a próxima etapa.

1. Para dar suporte a resolução DNS entre contêineres, você precisará configurar o cluster de desenvolvimento local, usando o nome do computador. Essas etapas também são necessárias para serviços de endereço por meio do proxy reverso.
   1. Abra o PowerShell como administrador
   2. Navegue até a pasta de instalação do Cluster do SDK, normalmente `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`.
   3. Execute o script `DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > Você pode usar o `-CreateOneNodeCluster` para configurar um cluster de um nó. O padrão irá criar um cluster de cinco nós local.
      >

      Para saber mais sobre o Serviço DNS no Service Fabric, consulte [Serviço DNS no Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice). Para saber mais sobre o uso do Service Fabric proxy reverso de serviços em execução em um contêiner, consulte [Tratamento especial de proxy reverso para serviços em execução em contêineres](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Limitações conhecidas durante a depuração de Service Fabric

Abaixo está uma lista de limitações conhecidas com contêineres de depuração no Service Fabric e as possíveis resoluções:

* Usando localhost para ClusterFQDNorIP não dará suporte à resolução de DNS em contêineres.
    * Resolução: configurar o cluster local usando o nome do computador (veja acima)
* Executar o Windows 10 em uma máquina Virtual não receberá a resposta DNS para o contêiner.
    * Resolução: desabilitar o descarregamento de soma de verificação de UDP para IPv4 no NIC de Máquinas Virtuais
    * Executar o Windows 10 poderá degradar o desempenho de rede na máquina.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Determinar os serviços no mesmo aplicativo usando o DNS nome do serviço não funciona no Windows 10, se o aplicativo foi implantado usando o Docker Compose
    * Resolução: usar servicename.applicationname para resolver pontos de extremidade de serviço
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Se estiver usando o endereço IP para ClusterFQDNorIP, alterar o IP primário no host interromperá a funcionalidade DNS.
    * Resolução: recriar o cluster usando o novo IP primário no host ou usar o nome do computador. Essa quebra faz parte do design.
* Se o FQDN de cluster foi criado com não pode ser resolvido na rede, DNS falhará.
    * Resolução: recriar o cluster local usando o IP primário do host. Essa falha ocorre por design.
* Durante a depuração de um contêiner, os logs de docker só estarão disponíveis na janela de saída do Visual Studio, não por meio de APIs do Service Fabric, incluindo o Service Fabric Explorer

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Depurar um aplicativo .NET em execução em contêineres do docker no Service Fabric

1. Execute o Visual Studio como administrador.

1. Abra um aplicativo .NET existente ou crie um novo.

1. Clique com o botão direito do mouse no projeto e selecione **Adicionar -> Suporte de Orquestrador de Contêineres -> Service Fabric**

1. Pressione **F5** para iniciar a depuração do aplicativo.

    O Visual Studio oferece suporte ao console e aos tipos de projeto de ASP.NET para .NET e .NET Core.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os recursos do Service Fabric e contêineres, consulte overview](service-fabric-containers-overview.md) de contêineres do Service Fabric.
