---
title: Criar cluster do Azure Service Fabric no Windows Server e Linux | Microsoft Docs
description: "Os clusters do Service Fabric são executados no Windows Server e Linux, o que significa que você poderá implantar e hospedar aplicativos do Service Fabric em qualquer lugar que possa executar o Windows Server ou Linux."
services: service-fabric
documentationcenter: .net
author: Chackdan
manager: timlt
editor: 
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/19/2017
ms.author: chackdan
ms.openlocfilehash: e3cfad19e42af24edd68befd7b1eac8cef41a1d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Criar clusters do Service Fabric no Windows Server ou no Linux
Um cluster do Azure Service Fabric é um conjunto de máquinas físicas ou virtuais conectadas em rede, no qual os microsserviços são implantados e gerenciados. Um computador ou VM que faz parte de um cluster é chamado de nó de cluster. Os clusters podem ser dimensionados para milhares de nós. Se você adiciona novos nós ao cluster, o Service Fabric reequilibra as réplicas de partição de serviço e instâncias entre o número aumentado de nós. O desempenho geral do aplicativo é melhorado e a contenção para o acesso à memória é reduzida. Se os nós no cluster não estiverem sendo usados com eficiência, você poderá diminuir o número de nós no cluster. O Service Fabric redistribui novamente as réplicas de partição e instâncias entre o número reduzido de nós, para fazer melhor uso do hardware em cada nó.

O Service Fabric permite a criação de clusters do Service Fabric em qualquer VM ou computador que esteja executando o Windows Server ou o Linux. Isso significa que você pode implantar e executar aplicativos do Service Fabric em qualquer ambiente em que tenha um conjunto de computadores do Windows Server ou do Linux interconectados, sejam eles locais, do Microsoft Azure ou de qualquer provedor de nuvem.

## <a name="create-service-fabric-clusters-on-azure"></a>Criar clusters do Service Fabric no Azure
A criação de um cluster no Azure é feita por intermédio de um Modelo do recurso ou do [Portal do Azure](https://portal.azure.com). Leia [Criar um cluster do Service Fabric usando um modelo do Resource Manager](service-fabric-cluster-creation-via-arm.md) ou [Criar um cluster do Service Fabric no Portal do Azure](service-fabric-cluster-creation-via-portal.md) para obter mais informações.

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Sistemas operacionais com suporte para clusters no Azure
É possível criar clusters em máquinas virtuais que executem estes sistemas operacionais:

* Windows Server 2012 R2
* Windows Server 2016 
* Linux Ubuntu 16.04  

## <a name="create-service-fabric-standalone-clusters-on-premises-or-with-any-cloud-provider"></a>Crie clusters autônomos do Service Fabric localmente ou com qualquer provedor de nuvem
O Service Fabric fornece um pacote de instalação para você criar clusters autônomos do Service Fabric localmente ou em qualquer provedor de nuvem.

Para obter mais informações sobre como configurar os clusters autônomos do Service Fabric no Windows Server, leia [Service Fabric cluster creation for Windows Server (Criação de cluster do Service Fabric para Windows Server)](service-fabric-cluster-creation-for-windows-server.md)

  > [!NOTE]
  > No momento, não há suporte para clusters autônomos no Linux. Há suporte para o Linux no one-box para desenvolvimento e em clusters de vários computadores Linux do Azure.
  >

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>Qualquer implantação na nuvem vs. implantações locais
O processo de criação de um cluster do Service Fabric local é semelhante ao processo de criação de um cluster em qualquer nuvem escolhida com um conjunto de VMs. As etapas iniciais para provisionar as VMs são administradas pelo provedor da nuvem ou pelo ambiente local que você está usando. Depois que você tiver um conjunto de VMs com conectividade de rede habilitada entre elas, as etapas para configurar o pacote do Service Fabric, editar as configurações do cluster e executar os scripts de gerenciamento e criação do cluster serão idênticas. Isso garante que o seu conhecimento e experiência com a operação e o gerenciamento de clusters do Service Fabric poderão ser transferidos quando você optar por novos ambientes de hospedagem.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>Benefícios de criar clusters autônomos do Service Fabric
* Você é livre para escolher qualquer provedor de nuvem para hospedar o cluster.
* Os aplicativos do Service Fabric, uma vez escritos, podem ser executados em vários ambientes de hospedagem com pouca ou nenhuma alteração.
* O conhecimento sobre a criação de aplicativos do Service Fabric é transferido de um ambiente de hospedagem para outro.
* A experiência operacional da execução e do gerenciamento de clusters do Service Fabric é transferida de um ambiente para outro.
* Amplo alcance de clientes, sem limitação de restrições de ambiente de hospedagem.
* Uma camada extra de confiabilidade e proteção contra interrupções generalizadas, permitindo que você mova os serviços para outro ambiente de implantação se um datacenter ou provedor de nuvem sofrer um blecaute.

## <a name="supported-operating-systems-for-standalone-clusters"></a>Sistemas operacionais com suporte para clusters autônomos
É possível criar clusters em VMs ou em computadores que executem estes sistemas operacionais (ainda não há suporte para o Linux):

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>Vantagens dos clusters do Service Fabric no Azure sobre os clusters autônomos do Service Fabric criados no local
A execução de clusters do Service Fabric no Azure apresenta vantagens em relação à opção local, de modo que se você não tiver necessidades específicas para onde executar seus clusters, sugerimos que os execute no Azure. No Azure, fornecemos integração a outros recursos e serviços do Azure, o que facilita e torna mais confiável as operações e o gerenciamento do cluster.

* **Portal do Azure:** o Portal do Azure facilita a criação e o gerenciamento de clusters.
* **Gerenciador de Recursos do Azure** : o uso do Gerenciador de Recursos do Azure permite o fácil gerenciamento de todos os recursos usados pelo cluster como uma unidade e simplifica o acompanhamento de custos e faturamento.
* **Cluster do Service Fabric como um recurso do Azure** Um cluster do Service Fabric é um recurso do Azure. Portanto, é possível modelá-lo como faz com outros recursos no Azure.
* **Integração à infraestrutura do Azure** O Service Fabric é coordenado com a infraestrutura subjacente do Azure para sistema operacional, rede e outras atualizações de modo a melhorar a disponibilidade e a confiabilidade dos seus aplicativos.  
* **Diagnóstico** : no Azure, fornecemos a integração no diagnóstico do Azure e no Log Analytics.
* **Dimensionamento automático** : para os clusters no Azure, fornecemos uma funcionalidade interna de dimensionamento automático devido aos conjuntos de escala da Máquina Virtual. Em ambientes locais e outros de nuvem, você precisa criar seu próprio recurso de dimensionamento automático ou dimensionar manualmente usando as APIs que o Service Fabric expõe para dimensionar os clusters.

## <a name="next-steps"></a>Próximas etapas

* Crie um cluster em VMs ou em computadores que estejam executando o Windows Server: [Criação de um cluster do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Criar um cluster em VMs ou em computadores que estejam executando o Linux: [Criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

