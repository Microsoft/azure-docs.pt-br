---
title: Visualizar seu cluster usando o Service Fabric Explorer | Microsoft Docs
description: "O Explorador do Service Fabric é uma ferramenta baseada na Web para inspecionar e gerenciar aplicativos em nuvem e nós em um cluster do Service Fabric do Microsoft Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: ryanwi
ms.openlocfilehash: 965ffc0f8cec26cccbe6e6459731afc234111f4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Visualizando o cluster com o Service Fabric Explorer
O Explorador do Service Fabric é uma ferramenta baseada na Web para inspecionar e gerenciar aplicativos e nós em um cluster do Service Fabric do Azure. O Explorador do Service Fabric é hospedado diretamente dentro do cluster para estar sempre disponível, independentemente de onde o cluster estiver sendo executado.

## <a name="video-tutorial"></a>Tutorial em vídeo

Para saber como usar o Service Fabric Explorer, assista ao seguinte vídeo da Microsoft Virtual Academy:

[<center><img src="./media/service-fabric-visualizing-your-cluster/SfxVideo.png" WIDTH="360" HEIGHT="244"></center>](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=bBTFg46yC_9806218965)

## <a name="connect-to-service-fabric-explorer"></a>Conectar ao Explorador do Service Fabric
Se você seguiu as instruções para [preparar o seu ambiente de desenvolvimento](service-fabric-get-started.md), pode inicializar o Explorador do Service Fabric no cluster local navegando para http://localhost:19080/Explorer.

## <a name="understand-the-service-fabric-explorer-layout"></a>Entender o layout do Explorador do Service Fabric
Você pode navegar pelo Explorador do Service Fabric usando a árvore à esquerda. Na raiz da árvore, o painel do cluster fornece uma visão geral do cluster, incluindo um resumo do aplicativo e a integridade do nó.

![Painel de clusters do Explorador do Service Fabric][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Exibir o layout do cluster
Nós em um cluster do Service Fabric são colocados em uma grade bidimensional de domínios de falha e domínios de atualização. Esse posicionamento garante que seus aplicativos permaneçam disponíveis na presença de falhas de hardware e atualizações de aplicativos. Você pode exibir o layout do cluster atual usando o mapa de clusters.

![Mapa de clusters do Explorador do Service Fabric][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Exibir aplicativos e serviços
O cluster contém duas subárvores: uma para aplicativos e outra para nós.

Você pode usar a exibição de aplicativos para navegar pela hierarquia lógica do Service Fabric: aplicativos, serviços, partições e réplicas.

No exemplo abaixo, o aplicativo **MyApp** é composto por dois serviços, **MyStatefulService** e **WebService**. Como o **MyStatefulService** tem monitoração de estado, ele inclui uma partição com uma réplica principal e duas secundárias. Por outro lado, o WebSvcService é sem monitoração de estado e contém uma única instância.

![Exibição do aplicativo do Explorador do Service Fabric][sfx-application-tree]

Em cada nível da árvore, o painel principal mostra informações pertinentes sobre o item. Por exemplo, você pode ver o status de integridade e a versão de um serviço específico.

![Painel essencial do Explorador do Service Fabric][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Exibir os nós do cluster
A exibição de nós mostra o layout físico do cluster. Para um nó específico, você pode inspecionar quais aplicativos têm código implantado naquele nó. Mais especificamente, você pode ver quais réplicas estão sendo executadas lá atualmente.

## <a name="actions"></a>Ações
O Explorador do Service Fabric oferece uma maneira rápida de invocar ações em nós, aplicativos e serviços no cluster.

Por exemplo, para excluir uma instância do aplicativo, escolha o aplicativo na árvore à esquerda e escolha **Ações** > **Excluir Aplicativo**.

![Excluir um aplicativo no Explorador do Service Fabric][sfx-delete-application]

> [!TIP]
> Você pode executar as mesmas ações clicando nas reticências ao lado de cada elemento.
>
>

A tabela a seguir lista as ações disponíveis para cada entidade:

| **Entidade** | **Ação** | **Descrição** |
| --- | --- | --- |
| Tipo de aplicativo |Remover o provisionamento de tipo |Remover o pacote de aplicativos do repositório de imagens do cluster. Requer que todos os aplicativos desse tipo sejam removidos em primeiro lugar. |
| Aplicativo |Excluir Aplicativo |Exclua o aplicativo, incluindo todos os seus serviços e o estado (se houver). |
| O Barramento de |Excluir serviço |Exclua o serviço e seu estado (se houver). |
| Nó |Ativar |Ative o nó. |
| Nó | Desativar (pausa) | Pause o nó em seu estado atual. Os serviços continuam sendo executados, mas o Service Fabric não move nada dele ou para ele proativamente, exceto se for necessário para impedir uma interrupção ou inconsistência de dados. Essa ação normalmente é usada para habilitar serviços de depuração em um nó específico a fim de garantir que não sejam movidos durante a inspeção. | |
| Nó | Desativar (reiniciar) | Mova todos os serviços na memória de um nó com segurança e feche os serviços persistentes. Normalmente usado quando os processos de host ou o computador precisam ser reiniciados. | |
| Nó | Desativar (remover dados) | Feche com segurança todos os serviços em execução no nó após a criação de réplicas de reposição suficientes. Normalmente usado quando um nó (ou pelo menos seu armazenamento) é desabilitado permanentemente. | |
| Nó | Remover o estado do nó | Remova o conhecimento das réplicas do nó do cluster. Normalmente usado quando um nó com falha já é considerado irrecuperável. | |
| Nó | Reiniciar | Simule a falha de nó reiniciando o nó. Mais informações podem ser obtidas [aqui](/powershell/module/servicefabric/restart-servicefabricnode?view=azureservicefabricps) | |

Como muitas ações são destrutivas, talvez seja necessário confirmar sua intenção antes que a ação seja concluída.

> [!TIP]
> Todas as ações que podem ser executadas usando o Explorador do Service Fabric também podem ser executadas usando o PowerShell ou uma API REST para habilitar a automação.
>
>

Você também pode usar o Service Fabric Explorer para criar instâncias de aplicativo para um determinado tipo e versão de aplicativo. Escolha o tipo de aplicativo no modo de exibição de árvore, clique no link **Create app instance (Criar instância de aplicativo)** ao lado da versão desejada no painel da direita.

![Criar uma instância de aplicativo no Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Instâncias de aplicativos criadas por meio do Service Fabric Explorer no momento não podem ser parametrizadas. Elas são criadas usando os valores de parâmetro padrão.
>
>

## <a name="connect-to-a-remote-service-fabric-cluster"></a>Conectar a um cluster remoto do Service Fabric
Conhecendo o ponto de extremidade do cluster e com permissões suficientes, você pode acessar o Service Fabric Explorer de qualquer navegador. Isso porque o Service Fabric Explorer é apenas outro serviço executado no cluster.

### <a name="discover-the-service-fabric-explorer-endpoint-for-a-remote-cluster"></a>Descobrir o ponto de extremidade do Explorador do Service Fabric para um cluster remoto
A fim de acessar o Service Fabric Explorer para um determinado cluster, aponte o navegador para:

http://&lt;your-cluster-endpoint&gt;:19080/Explorer

Para clusters do Azure, a URL completa também está disponível no painel de noções básicas do cluster do Portal do Azure.

### <a name="connect-to-a-secure-cluster"></a>Conectar a um cluster seguro
Você pode controlar o acesso do cliente ao cluster do Service Fabric com certificados ou usando o AAD (Azure Active Directory).

Se você tentar se conectar ao Service Fabric Explorer em um cluster seguro, dependendo da configuração do cluster, será preciso apresentar um certificado de cliente ou fazer logon usando o AAD.

## <a name="next-steps"></a>Próximas etapas
* [Visão geral da Possibilidade de Teste](service-fabric-testability-overview.md)
* [Gerenciando aplicativos da Malha do Serviço no Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Implantação de aplicativos de Malha do Serviço usando o PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png
