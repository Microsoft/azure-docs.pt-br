---
title: Visão geral do Azure e clusters de Service Fabric autônomos
description: Você pode criar clusters do Service Fabric em qualquer VM ou computador que execute o Windows Server ou o Linux. Isso significa que é possível implantar e executar aplicativos do Service Fabric em qualquer ambiente em que tenha um conjunto de computadores do Windows Server ou do Linux interconectados, podendo ser locais, do Microsoft Azure ou de um provedor de nuvem.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: d83ec139c367d9a539db173a42ae909f57d5d71a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576121"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Comparando clusters do Service Fabric independentes e do Azure no Windows Server e no Linux

Um cluster do Service Fabric é um conjunto de computadores físicos ou virtuais conectados via rede, nos quais os microsserviços são implantados e gerenciados. Um computador ou VM que faz parte de um cluster é chamado de nó de cluster. Os clusters podem ser dimensionados para milhares de nós. Se você adiciona novos nós ao cluster, o Service Fabric reequilibra as réplicas de partição de serviço e instâncias entre o número aumentado de nós. O desempenho geral do aplicativo é melhorado e a contenção para o acesso à memória é reduzida. Se os nós no cluster não estiverem sendo usados com eficiência, você poderá diminuir o número de nós no cluster. O Service Fabric redistribui novamente as réplicas de partição e instâncias entre o número reduzido de nós, para fazer melhor uso do hardware em cada nó.

O Service Fabric permite a criação de clusters do Service Fabric em qualquer VM ou computador que esteja executando o Windows Server ou o Linux. Isso significa que você pode implantar e executar aplicativos do Service Fabric em qualquer ambiente em que tenha um conjunto de computadores do Windows Server ou do Linux interconectados, podendo ser locais, do Microsoft Azure ou de um provedor de nuvem.

## <a name="benefits-of-clusters-on-azure"></a>Benefícios dos clusters no Azure

No Azure, fornecemos integração a outros recursos e serviços do Azure, o que facilita e torna mais confiável as operações e o gerenciamento do cluster.

* **Portal do Azure:** o Portal do Azure facilita a criação e o gerenciamento de clusters.
* **Gerenciador de Recursos do Azure** : o uso do Gerenciador de Recursos do Azure permite o fácil gerenciamento de todos os recursos usados pelo cluster como uma unidade e simplifica o acompanhamento de custos e faturamento.
* **Cluster do Service Fabric como um recurso do Azure** Um cluster do Service Fabric é um recurso do Azure. Portanto, é possível modelá-lo como faz com outros recursos no Azure.
* **Integração à infraestrutura do Azure** O Service Fabric é coordenado com a infraestrutura subjacente do Azure para sistema operacional, rede e outras atualizações de modo a melhorar a disponibilidade e a confiabilidade dos seus aplicativos.  
* **Diagnóstico:** No Azure, fornecemos integração com o diagnóstico do Azure e logs de Azure Monitor.
* **Dimensionamento automático:** Para clusters no Azure, fornecemos a funcionalidade de dimensionamento automático interna por meio de conjuntos de escala de máquina virtual. No local e em outros ambientes de nuvem, você precisa criar seu próprio recurso de dimensionamento automático ou dimensionar manualmente usando as APIs que o Service Fabric expõe para dimensionar clusters.

## <a name="benefits-of-standalone-clusters"></a>Benefícios de clusters independentes

* Você é livre para escolher qualquer provedor de nuvem para hospedar o cluster.
* Os aplicativos do Service Fabric, uma vez escritos, podem ser executados em vários ambientes de hospedagem com pouca ou nenhuma alteração.
* O conhecimento sobre a criação de aplicativos do Service Fabric é transferido de um ambiente de hospedagem para outro.
* A experiência operacional da execução e do gerenciamento de clusters do Service Fabric é transferida de um ambiente para outro.
* Amplo alcance de clientes, sem limitação de restrições de ambiente de hospedagem.
* Uma camada extra de confiabilidade e proteção contra interrupções generalizadas, permitindo que você mova os serviços para outro ambiente de implantação se um datacenter ou provedor de nuvem sofrer um blecaute.

## <a name="next-steps"></a>Próximas etapas

* Leia a visão geral dos [clusters do Service Fabric no Azure](service-fabric-azure-clusters-overview.md)
* Leia a visão geral de [clusters do Service Fabric independentes](service-fabric-standalone-clusters-overview.md)
* Saiba mais sobre [as opções de suporte do Service Fabric](service-fabric-support.md)