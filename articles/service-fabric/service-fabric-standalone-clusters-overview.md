---
title: Visão geral de clusters independentes do Service Fabric | Microsoft Docs
description: Os clusters do Service Fabric são executados no Windows Server e Linux, o que significa que você poderá implantar e hospedar aplicativos do Service Fabric em qualquer lugar que possa executar o Windows Server ou Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: b4b7759d1dc23c1a1b3a9b5aeb2a181923e14d40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543729"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Visão geral de clusters independentes do Service Fabric

Um cluster do Service Fabric é um conjunto de computadores físicos ou virtuais conectados via rede, nos quais os microsserviços são implantados e gerenciados. Um computador ou VM que faz parte de um cluster é chamado de nó de cluster. Os clusters podem ser dimensionados para milhares de nós. Se você adiciona novos nós ao cluster, o Service Fabric reequilibra as réplicas de partição de serviço e instâncias entre o número aumentado de nós. O desempenho geral do aplicativo é melhorado e a contenção para o acesso à memória é reduzida. Se os nós no cluster não estiverem sendo usados com eficiência, você poderá diminuir o número de nós no cluster. O Service Fabric redistribui novamente as réplicas de partição e instâncias entre o número reduzido de nós, para fazer melhor uso do hardware em cada nó.

Um tipo de nó define o tamanho, o número e as propriedades de um conjunto de nós no cluster. Cada tipo de nó pode ser escalado verticalmente para cima ou para baixo de forma independente, tem conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente. Os tipos de nós são usados na definição de funções para um conjunto de nós de cluster, como "front-end" ou "back-end". Seu cluster pode ter mais de um tipo de nó, mas o tipo de nó primário deve ter pelo menos cinco VMs para clusters de produção (ou pelo menos três VMs para clusters de teste). [Os serviços de sistema do Service Fabric](service-fabric-technical-overview.md#system-services) são colocados nos nós do tipo primário.

O processo de criação de um cluster do Service Fabric local é semelhante ao processo de criação de um cluster em qualquer nuvem escolhida com um conjunto de VMs. As etapas iniciais para provisionar as VMs são administradas pelo provedor da nuvem ou pelo ambiente local que você está usando. Depois que você tiver um conjunto de VMs com conectividade de rede habilitada entre elas, as etapas para configurar o pacote do Service Fabric, editar as configurações do cluster e executar os scripts de gerenciamento e criação do cluster serão idênticas. Isso garante que o seu conhecimento e experiência com a operação e o gerenciamento de clusters do Service Fabric poderão ser transferidos quando você optar por novos ambientes de hospedagem.

## <a name="cluster-security"></a>Segurança de cluster
Um cluster do Service Fabric é um recurso que pertence a você.  É sua responsabilidade proteger os clusters para ajudar a impedir que usuários não autorizados se conectem a eles. Um cluster seguro é especialmente importante quando você está executando cargas de trabalho de produção no cluster.

### <a name="node-to-node-security"></a>Segurança de nó para nó
A segurança de nó para nó protege a comunicação entre as VMs ou os computadores em um cluster. Esse cenário de segurança faz com que somente os computadores autorizados a ingressar no cluster possam hospedar aplicativos e serviços no cluster. O Service Fabric usa certificados x.509 para proteger um cluster e fornecer recursos de segurança do aplicativo.  É necessário um certificado de cluster para proteger o tráfego de cluster e fornecer autenticação de servidor e de cluster.  Os certificados autoassinados podem ser usados para clusters de teste, mas deve ser usado um certificado de uma autoridade de certificação confiável para proteger clusters de produção.

A segurança do Windows também pode ser habilitada para um cluster independente do Windows. Se você tem o Windows Server 2012 R2 e o Windows Active Directory, é recomendável usar a segurança do Windows com contas de serviço gerenciado de grupo. Caso contrário, use a segurança do Windows com contas do Windows.

Para obter mais informações, confira a [Segurança de nó para nó](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Segurança de cliente para nó
A segurança de cliente para nó autentica clientes e ajuda a proteger a comunicação entre um cliente e nós individuais no cluster. Esse tipo de segurança ajuda a assegurar que somente usuários autorizados possam acessar o cluster e os aplicativos implantados no cluster. Os clientes são identificados exclusivamente por meio de suas credenciais de segurança do certificado X.509. Qualquer número de certificados de cliente opcionais podem ser usados para autenticar clientes usuários ou administradores no cluster.

Além de certificados de cliente, o Azure Active Directory também pode ser configurado para autenticar clientes no cluster.

Para obter mais informações, confira a [Segurança de cliente para nó](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control-rbac"></a>RBAC (Controle de Acesso Baseado em Função)
O Service Fabric também dá suporte ao controle de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de usuários. Isso ajuda a tornar o cluster mais seguro. Dois tipos de controle de acesso têm suporte para clientes que se conectam a um cluster: Função de administrador e função de usuário.  

Para obter mais informações, leia [RBAC (Controle de acesso baseado em função)](service-fabric-cluster-security.md#role-based-access-control-rbac).

## <a name="scaling"></a>Dimensionamento

As demandas de aplicativos alteram ao longo do tempo. Talvez seja necessário aumentar os recursos de cluster para atender ao aumento do tráfego ou de carga de trabalho do aplicativo ou diminuir os recursos de cluster quando houver queda de demanda. Após criar um cluster do Service Fabric, será possível dimensionar o cluster horizontalmente (alterar o número de nós) ou verticalmente (alterar os recursos dos nós). É possível dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster. Na medida em que o cluster for dimensionado, os aplicativos também serão dimensionados automaticamente.

Para obter mais informações, leia [Dimensionamento de clusters independentes](service-fabric-cluster-scaling-standalone.md).

## <a name="upgrading"></a>Atualizando

Um cluster autônomo é um recurso que é totalmente seu. Você é responsável por corrigir o sistema operacional subjacente e iniciar atualizações de malha. É possível configurar seu cluster para receber atualizações automáticas de tempo de execução quando a Microsoft lançar uma nova versão ou é possível optar por selecionar uma versão de tempo de execução com suporte que você queira. Além das atualizações de malha, você também pode aplicar patches no SO e atualizar a configuração do cluster, como os certificados ou as portas de aplicativo. 

Para obter mais informações, leia [Atualização de clusters independentes](service-fabric-cluster-upgrade-standalone.md).

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte
É possível criar clusters em VMs ou em computadores que executem estes sistemas operacionais (ainda não há suporte para o Linux):

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [proteger](service-fabric-cluster-security.md), [dimensionar](service-fabric-cluster-scaling-standalone.md) e [atualizar](service-fabric-cluster-upgrade-standalone.md) clusters independentes.

Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md).