---
title: Fazer failover e dimensionar um aplicativo de contêineres no Service Fabric no Azure | Microsoft Docs
description: Neste tutorial, você aprende como o failover é manipulado em um aplicativo de contêineres do Azure Service Fabric.  Além disso, saiba como dimensionar os contêineres e os serviços em execução em um cluster.
services: service-fabric
documentationcenter: ''
author: suhuruli
manager: chackdan
editor: suhuruli
tags: servicefabric
keywords: Docker, Contêineres, Microservices, Service Fabric, Azure
ms.assetid: ''
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2019
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: a13ce98eeebf60a6b61ee9aff01107c61fa94641
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385114"
---
# <a name="tutorial-demonstrate-fail-over-and-scaling-of-container-services-with-service-fabric"></a>Tutorial: Demonstrar o failover e o dimensionamento de serviços de contêiner com o Service Fabric

Este tutorial é a parte três de uma série. Neste tutorial, você aprenderá como o failover é manipulado em aplicativos de contêiner do Service Fabric. Além disso, você aprenderá como dimensionar contêineres. Neste tutorial, você:

> [!div class="checklist"]
> * Saiba mais sobre o failover do contêiner em um cluster do Service Fabric
> * Dimensionar os contêineres de front-end da Web em um aplicativo

## <a name="prerequisites"></a>Pré-requisitos

O aplicativo da [Parte 2](service-fabric-tutorial-package-containers.md) está em execução em um cluster ativo do Service Fabric.

## <a name="fail-over-a-container-in-a-cluster"></a>Fazer failover de um contêiner em um cluster

O Service Fabric certifica-se de que suas instâncias de contêiner sejam movidas automaticamente para outros nós no cluster caso ocorra uma falha. Também é possível drenar um nó de contêineres manualmente e movê-los normalmente para outros nós no cluster. Há vários modos de dimensionar seus serviços. Neste exemplo, estamos usando o Service Fabric Explorer.

Para fazer failover do contêiner de front-end, execute as seguintes etapas:

1. Abra o Service Fabric Explorer no cluster – por exemplo, `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Clique no nó **fabric:/TestContainer/azurevotefront** no modo de exibição de árvore e expanda o nó de partição (representado por um GUID). Observe que o nome de nó no modo de exibição de árvore, que mostra os nós no qual o contêiner está em execução no momento – por exemplo `_nodetype_1`
3. Expanda o nó **Nós** no modo de exibição de árvore. Clique nas reticências (três pontos) ao lado do nó, que está executando o contêiner.
4. Escolha **Reiniciar** para reiniciar o nó e confirmar a ação de reinicialização. A reinicialização faz o contêiner fazer failover para outro nó no cluster.

![noderestart][noderestart]

Observe como o nome do nó (que indica onde os contêineres de front-end são executados) é alterado para outro nó no cluster. Após alguns instantes, você deverá conseguir procurar o aplicativo novamente e vê-lo em execução em um nó diferente.

## <a name="scale-containers-and-services-in-a-cluster"></a>Dimensionar contêineres e serviços em um cluster

Os contêineres do Service Fabric podem ser dimensionados em um cluster para acomodar a carga nos serviços. Dimensione um contêiner alterando o número de instâncias em execução no cluster.

Para dimensionar o front-end da Web, siga estas etapas:

1. Abra o Service Fabric Explorer em seu cluster – por exemplo, `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Clique nas reticências (três pontos) ao lado do nó **fabric:/TestContainer/azurevotefront** no modo de exibição de árvore e escolha **Dimensionar Serviço**.

![sfxscale][sfxscale]

Agora é possível optar por dimensionar o número de instâncias do front-end da Web.

1. Altere o número para **2** e clique em **Dimensionar Serviço**.
1. Clique no nó **fabric:/TestContainer/azurevotefront** no modo de exibição de árvore e expanda o nó de partição (representado por um GUID).

![sfxscaledone][sfxscaledone]

Agora é possível ver que o serviço tem duas instâncias. No modo de exibição de árvore, você vê em quais nós as instâncias são executadas.

Com essa tarefa de gerenciamento simples, dobramos o número de recursos disponíveis para nosso serviço de front-end para processar a carga do usuário. É importante entender que você não precisa de várias instâncias de um serviço para que ele seja executado de forma confiável. Se um serviço falhar, o Service Fabric garantirá que uma nova instância de serviço seja executada no cluster.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, foi demonstrado o failover do contêiner, bem como o dimensionamento de um aplicativo. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Saiba mais sobre o failover do contêiner em um cluster do Service Fabric
> * Dimensionar os contêineres de front-end da Web em um aplicativo

Nesta série de tutoriais, você aprendeu a:
> [!div class="checklist"]
> * Criar imagens de contêiner
> * Efetuar push de imagens de contêiner para o Registro de Contêiner do Azure
> * Empacotar contêineres para o Service Fabric usando o Yeoman
> * Criar e executar um aplicativo do Service Fabric com contêineres
> * Como o failover e o dimensionamento são manipulados no Service Fabric

[noderestart]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialnoderestart.png
[sfxscale]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscale.png
[sfxscaledone]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscaledone.png