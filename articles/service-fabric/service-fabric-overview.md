---
title: "Visão geral da Service Fabric no Azure | Microsoft Docs"
description: "Uma visão geral do Service Fabric no qual os aplicativos são compostos por muitos microsserviços para fornecer dimensionamento e resiliência. O Service Fabric é uma plataforma de sistemas distribuídos usada para compilar aplicativos escalonáveis, confiáveis e facilmente gerenciados para a nuvem."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: masnider
ms.assetid: bbcc652a-a790-4bc4-926b-e8cd966587c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: overview
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: msfussell
ms.custom: mvc
ms.openlocfilehash: 8ff0d38a679b673b148dd808050eda82060cfe80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-service-fabric"></a>Visão geral do Azure Service Fabric
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, implantação e gerenciamento de microsserviços e contêineres escalonáveis e confiáveis. O Service Fabric resolve os desafios significativos de desenvolvimento e gerenciamento de aplicativos em nuvem nativos. Desenvolvedores e administradores podem evitar problemas complexos de infraestrutura e se concentrarem na implementação de cargas de trabalho essenciais e exigentes que são escalonáveis, confiáveis e gerenciáveis. O Service Fabric representa a plataforma de última geração para criação e gerenciamento de aplicativos escalonáveis de nuvem de camada 1 e nível corporativo.

Este breve vídeo apresenta o Service Fabric e os microsserviços: <center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-overview/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="applications-composed-of-microservices"></a>Aplicativos compostos por microsserviços 
O Service Fabric permite compilar e gerenciar aplicativos escalonáveis e confiáveis compostos por microsserviços executados a uma densidade alta em um pool compartilhado de computadores, conhecido como um cluster. Ele fornece um tempo de execução leve e sofisticado para compilar microsserviços distribuídos e escalonáveis com e sem estado que são executados em contêineres. Também fornece recursos de gerenciamento de aplicativos abrangentes para provisionar, implantar, monitorar, atualizar/aplicar patch e excluir aplicativos implantados incluindo serviços em contêineres.

O Service Fabric é a tecnologia de diversos serviços atuais da Microsoft, incluindo Banco de Dados SQL do Azure, Azure Cosmos DB, Cortana, Microsoft Power BI, Microsoft Intune, Hubs de Eventos do Azure, Hub IoT do Azure, Dynamics 365, Skype for Business e vários serviços principais do Azure.

O Service Fabric é projetado para criar serviços em nuvem nativos que podem começar pequenos, conforme a necessidade, e expandirem-se para escala em massa com centenas ou milhares de computadores.

Os serviços em escala da Internet de hoje são criados por microsserviços. Exemplos de microsserviços são gateways de protocolo, perfis de usuário, carrinhos de compra, processamento de inventário, filas e caches. O Service Fabric é uma plataforma de microsserviços que fornece a cada microsserviço (ou contêiner) um nome exclusivo que pode ser com ou sem estado.

O Service Fabric fornece recursos abrangentes de gerenciamento de ciclo de vida e tempo de execução para aplicativos compostos por esses microsserviços. Ela hospeda microsserviços em contêineres que são implantados e ativados no cluster da Malha do Serviço. Passar de máquinas virtuais para contêineres possibilita um aumento de ordem de magnitude na densidade. De modo similar, outra ordem de magnitude na densidade torna-se possível passando de contêineres para microsserviços nesses contêineres. Por exemplo, um único cluster para um Banco de Dados SQL do Azure engloba centenas de computadores que executam dezenas de milhares de contêineres que hospedam um total de centenas de milhares de bancos de dados. Cada banco de dados é um microsserviço com estado do Service Fabric. 

Para saber mais sobre a abordagem de microsserviços, leia o artigo [Por que usar uma abordagem de microsserviços para construir aplicativos?](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>Orquestração e implantação de contêiner
O Service Fabric é um [orquestrador de cotêiner](service-fabric-cluster-resource-manager-introduction.md) da Microsoft que implanta microsserviços em um cluster de computadores. Os microsserviços podem ser desenvolvidos de várias maneiras usando os [modelos de programação do Service Fabric](service-fabric-choose-framework.md), o [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), para implantar [qualquer código que você escolher](service-fabric-deploy-existing-app.md). É importante observar que você pode misturar serviços em processos e serviços em contêineres no mesmo aplicativo. Se você quiser apenas [implantar e gerenciar contêineres](service-fabric-containers-overview.md) o Service Fabric é uma opção perfeita de orquestrador de contêiner.

## <a name="any-os-any-cloud"></a>Qualquer sistema operacional, qualquer nuvem
O Service Fabric pode ser executado em qualquer lugar. Você pode criar clusters para o Service Fabric em muitos ambientes, incluindo o Azure, ou localmente, no Windows Server ou no Linux. Você ainda pode até criar clusters em outras nuvens públicas. Além disso, o ambiente de desenvolvimento no SDK é **idêntico** ao ambiente de produção, sem nenhum emulador envolvido. Em outras palavras, o que é executado no cluster de desenvolvimento local será implantado nos clusters em outros ambientes.

![Plataforma Service Fabric][Image1]

Para desenvolvimento no Windows, o SDK .NET do Service Fabric é integrado com o Visual Studio e com o PowerShell. Consulte [Prepare your development environment on Windows](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started.md) (Prepare seu ambiente de desenvolvimento no Windows). Para desenvolvimento no Linux, o SDK de Java do Service Fabric é integrado com o Eclipse e o Yeoman é usado para gerar modelos para Java, .NET Core e aplicativos de contêiner. Consulte [Prepare your development environment on Linux](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started.md) (Preparar seu ambiente de desenvolvimento no Linux)

Para obter mais informações sobre a criação de clusters, leia sobre [como criar um cluster no Windows Server ou no Linux](service-fabric-deploy-anywhere.md) ou, para o Azure, como criar um cluster [por meio do Portal do Azure](service-fabric-cluster-creation-via-portal.md).

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Microsserviços com e sem estado para o Service Fabric
O Service Fabric permite compilar aplicativos que consistam em microsserviços ou contêineres. Os microsserviços sem estado (como gateways de protocolo e proxies Web) não mantêm um estado mutável fora de uma solicitação e sua resposta do serviço. As funções de trabalho dos Serviços de Nuvem do Azure são um exemplo de serviço sem estado. Os microsserviços com estado (como contas de usuário, bancos de dados, dispositivos, carrinhos de compra e filas) mantêm um estado mutável e autoritativo além da solicitação e sua resposta. Os aplicativos em escala da Internet de hoje consistem em uma combinação de microsserviços com e sem estado. 

Uma diferença importante com o Service Fabric é seu foco na criação de serviços com estado, com os [modelos de programação internos ](service-fabric-choose-framework.md) ou com serviços com estado em contêineres. Os [cenários de aplicativo](service-fabric-application-scenarios.md) descrevem os cenários em que os serviços com monitoração de estado são usados.


## <a name="application-lifecycle-management"></a>Gerenciamento do ciclo de vida de aplicativos
O Service Fabric oferece suporte para o gerenciamento de todo o ciclo de vida e CI/CD dos aplicativos em nuvem incluindo contêineres. Esse ciclo de vida inclui o desenvolvimento por meio de implantação, gerenciamento diário e manutenção até o possível encerramento.

As funcionalidades de gerenciamento de ciclo de vida do aplicativo do Service Fabric permitem que os administradores de aplicativos e os operadores de TI usem fluxos de trabalho simples com pouco envolvimento humano para provisionar, implantar e aplicar patch e monitorar aplicativos. Esses fluxos de trabalho internos reduzem enormemente a carga em operações de TI para manter os aplicativos disponíveis continuamente.

A maioria dos aplicativos consiste em uma combinação de microsserviços com e sem estado, contêineres e outros executáveis que são implantados juntos. Tendo tipos fortes nos aplicativos, o Service Fabric permite a implantação de várias instâncias de aplicativo. Cada instância é gerenciada e atualizada independentemente. Vale destacar que o Service Fabric pode implantar contêineres ou qualquer executável e torná-los confiáveis. Por exemplo, o Service Fabric implanta .NET, ASP.NET Core, node.js, contêineres do Windows, contêineres do Linux, máquinas virtuais Java, scripts, Angular ou literalmente qualquer outro item que componha seu aplicativo.

O Service Fabric é integrado com as ferramentas de CI/CD como [Visual Studio Team Services](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html) e [Octopus Deploy](https://octopus.com/) e pode ser usado com qualquer outra ferramenta de CI/CD popular.

Para saber mais sobre o gerenciamento do ciclo de vida do aplicativo, leia [Ciclo de vida do aplicativo](service-fabric-application-lifecycle.md). Para obter mais informações sobre como implantar qualquer código, consulte [implantar um executável convidado](service-fabric-deploy-existing-app.md).

## <a name="key-capabilities"></a>Principais recursos
Ao usar a Malha do Serviço, você pode:

* Implante no Azure ou em data centers locais que executem o Windows ou Linux com zero alteração de código. Escreva uma vez e implante em qualquer lugar para qualquer cluster do Service Fabric.
* Desenvolva aplicativos compostos por microsserviços usando o modelo de programação do Service Fabric, contêineres ou qualquer código.
* Desenvolva microsserviços com e sem monitoração de estado altamente confiáveis. Simplifique o design do seu aplicativo usando microsserviços com monitoramento de estado. 
* Use o novo modelo de programação Reliable Actors para criar objetos de nuvem com o estado e código independentes.
* Implante e orquestre contêineres que incluam contêineres do Windows e contêineres do Linux. O Service Fabric é um orquestrador de contêiner com monitoração de estado e com reconhecimento de dados.
* Implante aplicativos em segundos, de alta densidade com centenas ou milhares de aplicativos ou contêineres por computador.
* Implante diferentes versões do mesmo aplicativo lado a lado e atualize cada aplicativo de modo independente.
* Gerencie o ciclo de vida dos seus aplicativos sem nenhum tempo de inatividade, incluindo atualizações imediatas ou não.
* Aumente ou reduza o número de nós em um cluster. À medida que você dimensiona nós, seus aplicativos são dimensionados automaticamente.
* Monitorar e diagnosticar a integridade de seus aplicativos e definir políticas para realizar reparos automáticos.
* Observe o balanceador de recursos orquestrar a redistribuição de aplicativos no cluster. O Service Fabric se recupera de falhas e otimiza a distribuição a distribuição de carga com base nos recursos disponíveis.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
* Para mais informações:
  * [Por que usar uma abordagem de microsserviço para construir aplicativos?](service-fabric-overview-microservices.md)
  * [Visão geral da terminologia](service-fabric-technical-overview.md)
* Configurando o [ambiente de desenvolvimento do Windows](service-fabric-get-started.md)  
* Configurando o [ambiente de desenvolvimento do Linux](service-fabric-get-started-linux.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
