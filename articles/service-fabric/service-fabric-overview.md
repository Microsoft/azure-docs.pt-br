---
title: Visão geral do Azure Service Fabric
description: O Service Fabric é uma plataforma de sistemas distribuídos para criar microsserviços escalonáveis, confiáveis e facilmente gerenciados.
ms.topic: overview
ms.date: 09/22/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: b6a14ba3f905fbef3fc3796b616cd594941a3d60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97028624"
---
# <a name="overview-of-azure-service-fabric"></a>Visão geral do Azure Service Fabric

O Azure Service Fabric é uma [plataforma de sistemas distribuídos](#container-orchestration) que facilita o empacotamento, a implantação e o gerenciamento de microsserviços e contêineres escalonáveis e confiáveis. O Service Fabric também lida com os desafios significativos do [desenvolvimento e gerenciamento](#application-lifecycle-management) de aplicativos nativos da nuvem.

Uma diferencial fundamental do Service Fabric é o seu grande foco na criação de serviços com estado. Você pode usar o [modelo de programação](#stateless-and-stateful-microservices) do Service Fabric ou executar serviços com estado em contêineres escritos em qualquer linguagem ou código. Você pode criar [clusters do Service Fabric em qualquer lugar](#any-os-any-cloud), incluindo no Windows Server, no Linux local e em outras nuvens públicas, além do Azure.

![A plataforma do Service Fabric fornece gerenciamento de ciclo de vida, disponibilidade, orquestração, modelos de programação, integridade e monitoramento, ferramentas de desenvolvimento e operações e dimensionamento automático – no Azure, no local, em outras nuvens e em seu computador de desenvolvimento][Image1]

O Service Fabric é a tecnologia de diversos serviços atuais da Microsoft, incluindo Banco de Dados SQL do Azure, Azure Cosmos DB, Cortana, Microsoft Power BI, Microsoft Intune, Hubs de Eventos do Azure, Hub IoT do Azure, Dynamics 365, Skype for Business e vários serviços principais do Azure.

## <a name="container-orchestration"></a>Orquestração de contêineres

O Service Fabric é o [orquestrador de contêineres](service-fabric-cluster-resource-manager-introduction.md) da Microsoft, usado para implantar e gerenciar microsserviços em um cluster de computadores, beneficiando-se das lições aprendidas com a execução dos serviços da Microsoft em grande escala. O Service Fabric pode implantar aplicativos em segundos, com alta densidade, com centenas ou milhares de aplicativos ou contêineres por computador. Com o Service Fabric, você pode misturar serviços em processos e serviços em contêineres no mesmo aplicativo.

[Saiba mais sobre o Service Fabric](service-fabric-content-roadmap.md) e seus principais conceitos, modelos de programação, ciclo de vida do aplicativo, testes, clusters e monitoramento de integridade.

## <a name="stateless-and-stateful-microservices"></a>Microsserviços com e sem estado

O Service Fabric fornece um runtime leve e sofisticado que dá suporte a microsserviços com e sem estado. Um diferencial importante do Service Fabric é seu robusto suporte à criação de serviços com estado, com seus [modelos de programação internos](service-fabric-choose-framework.md) ou seus serviços com estado em contêineres.

Saiba mais sobre os [cenários de aplicativo](service-fabric-application-scenarios.md) que se beneficiam dos serviços com estado do Service Fabric.

## <a name="application-lifecycle-management"></a>Gerenciamento do ciclo de vida de aplicativos

O Service Fabric dá suporte ao ciclo de vida completo e ao CI/CD dos aplicativos em nuvem, incluindo os contêineres: desde o desenvolvimento até a implantação, monitoramento diário, gerenciamento, manutenção e o possível encerramento. O Service Fabric é integrado com as ferramentas de CI/CD como [Azure Pipelines](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html) e [Octopus Deploy](https://octopus.com/) e pode ser usado com qualquer outra ferramenta de CI/CD popular.

Para saber mais sobre o gerenciamento do ciclo de vida do aplicativo, leia [Ciclo de vida do aplicativo](service-fabric-application-lifecycle.md). Para implantar aplicativos existentes no Service Fabric, confira [Implantar um executável convidado](service-fabric-deploy-existing-app.md).

## <a name="any-os-any-cloud"></a>Qualquer sistema operacional, qualquer nuvem

Você pode criar clusters para o Service Fabric em muitos ambientes, incluindo no [Azure ou no local](service-fabric-deploy-anywhere.md), no [Windows Server ou no Linux](service-fabric-linux-windows-differences.md). Você ainda pode até criar clusters em outras nuvens públicas. O ambiente de desenvolvimento no SDK do Service Fabric é idêntico ao ambiente de produção, sem nenhum emulador envolvido. Em outras palavras, o que é executado no cluster de desenvolvimento local é implantado nos clusters em outros ambientes.

Para [desenvolvimento no Windows](service-fabric-get-started.md), o SDK .NET do Service Fabric é integrado ao Visual Studio e ao PowerShell. Para [desenvolvimento no Linux](service-fabric-get-started-linux.md), o SDK de Java do Service Fabric é integrado ao Eclipse, e o Yeoman é usado para gerar modelos para aplicativos de contêiner, Java e .NET Core.

## <a name="compliance"></a>Conformidade

O provedor de recursos do Azure Service Fabric está disponível em todas as regiões do Azure e tem conformidade com todos os certificados de conformidade do Azure tem, incluindo: SOC, ISO, PCI DSS, HIPAA e RGPD. Para ver uma lista completa, confira as [Ofertas de conformidade da Microsoft](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Próximas etapas

Criar e implantar seu primeiro aplicativo no Azure Service Fabric:

> [!div class="nextstepaction"]
> [Início rápido do Service Fabric][sf-quickstart]

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
[sf-quickstart]: ./service-fabric-quickstart-dotnet.md
