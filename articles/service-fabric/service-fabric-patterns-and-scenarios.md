---
title: "Cenários e padrões do Azure Service Fabric | Microsoft Docs"
description: "Conheça as práticas recomendadas e comprovadas, padrões reutilizáveis para projetar, desenvolver e operar seu microsserviços no Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: d5aa75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/04/2017
ms.author: ryanwi
ms.openlocfilehash: 8ac4a10b9030a4d56824d1a42ed2729ebb2d208e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-patterns-and-scenarios"></a>Cenários e padrões do Service Fabric
Se você pretende criar microsserviços em grande escala com o Azure Service Fabric, aprende com os especialistas que projetaram e criaram esta plataforma como serviço (PaaS). Comece com a arquitetura adequada e aprenda a otimizar os recursos para seu aplicativo. O curso [Práticas e padrões do Service Fabric](https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344) responde às perguntas mais frequentes feitas pelos clientes do mundo real sobre os cenários e áreas de aplicação do Service Fabric.
 
Descubra como projetar, desenvolver e operar seu microsserviços no Service Fabric usando práticas recomendadas e padrões comprovados e reutilizáveis. Obtenha uma visão geral do Service Fabric e aprofunde-se nos tópicos que abordam a otimização de cluster e segurança, migração de aplicativos herdados, IoT em grande escala, hospedagem de mecanismos de jogos e muito mais. Examine a entrega contínua de várias cargas de trabalho e até mesmo detalhes sobre os contêineres e suporte para Linux. 

## <a name="introduction"></a>Introdução
Explore as práticas recomendadas e saiba mais sobre a escolha de uma plataforma como serviço (PaaS) em vez de uma infraestrutura como serviço (IaaS). Obtenha os detalhes sobre os seguintes princípios de design comprovados de aplicativos.

<table><tr><th>Vídeo</th><th>Área do PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=N2KwbbSGD_6405167344">
<img src="./media/service-fabric-patterns-and-scenarios/intro.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344">Introdução ao Service Fabric</a></td></tr>
</table>

## <a name="cluster-planning-and-management"></a>Planejamento e gerenciamento de cluster
Saiba mais sobre o planejamento de capacidade, otimização e segurança de cluster nesta abordagem do Azure Service Fabric.

<table><tr><th>Vídeo</th><th>Área do PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=cyDYZcSGD_2805167344">
<img src="./media/service-fabric-patterns-and-scenarios/cluster.png" WIDTH="360" HEIGHT="244">
</a></td><td> <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=E5B3nJSGD_805167344">Planejamento e Gerenciamento de Cluster</a></td></tr>
</table>

## <a name="hyper-scale-web"></a>Hiperescala Web
Examine os conceitos de Web de hiperescala, incluindo disponibilidade e confiabilidade, hiperescala e gerenciamento de estado.

<table><tr><th>Vídeo</th><th>Área do PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=NgldAdSGD_405167344">
<img src="./media/service-fabric-patterns-and-scenarios/hyperscaleweb.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=CPMLBLSGD_7705167344">Hiperescala Web</a></td></tr>
</table>

## <a name="iot"></a>IoT
Explore o IoT (Internet das Coisas) no contexto do Azure Service Fabric, incluindo o pipeline IoT do Azure, multilocação e IoT em escala.

<table><tr><th>Vídeo</th><th>Área do PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=naFUVeSGD_1505167344">
<img src="./media/service-fabric-patterns-and-scenarios/iot.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">IoT</a></td></tr>
</table>

## <a name="gaming"></a>Jogos
Examine jogos baseados em turno, jogos interativos e mecanismos existentes de hospedagem de jogos.

<table><tr><th>Vídeo</th><th>Área do PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=6wECzeSGD_3805167344">
<img src="./media/service-fabric-patterns-and-scenarios/gaming.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">Jogos</a></td></tr>
</table>

## <a name="continuous-delivery"></a>Entrega contínua
Explore os conceitos, incluindo entrega/integração contínua com o Visual Studio Team Services, fluxo de trabalho de build/pacote/publicação, configuração de vários ambientes e pacote/compartilhamento do serviço.

<table><tr><th>Vídeo</th><th>Área do PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=78h5ofSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/cd.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=VlENvOSGD_105167344">Entrega contínua</a></td></tr>
</table>

## <a name="migration"></a>Migração
Saiba como migrar de um serviço de nuvem, além da migração de aplicativos herdados.

<table><tr><th>Vídeo</th><th>Área do PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=hd1cMgSGD_5605167344">
<img src="./media/service-fabric-patterns-and-scenarios/migration.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=GQAq4QSGD_8305167344">Migração</a></td></tr>
</table>

## <a name="containers-and-linux-support"></a>Suporte para Linux e Contêineres
Conheça a resposta para a pergunta "Por que contêineres?" Saiba mais sobre a visualização dos contêineres do Windows, suporte ao Linux e coordenação de contêineres do Linux. Além disso, descubra como migrar aplicativos .NET Core para o Linux.

<table><tr><th>Vídeo</th><th>Área do PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=V1ERJhSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/containers.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mlYsZRSGD_2105167344">Suporte para Linux e contêineres</a></td></tr>
</table>

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu sobre padrões e cenários do Service Fabric, leia mais sobre como [criar e gerenciar clusters](service-fabric-deploy-anywhere.md), [migrar aplicativos de Serviços de Nuvem para o Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md), [configurar a entrega contínua](service-fabric-set-up-continuous-integration.md) e [implantar contêineres](service-fabric-containers-overview.md).
