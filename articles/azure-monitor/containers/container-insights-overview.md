---
title: Visão geral de informações de contêiner | Microsoft Docs
description: Este artigo descreve as informações de contêiner que monitoram a solução de informações de contêiner AKS e o valor que ele fornece monitorando a integridade dos clusters do AKS e das instâncias de contêiner no Azure.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: a29890f569c47c48aae7d5d61badee7edeef58a0
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102441509"
---
# <a name="container-insights-overview"></a>Visão geral do contêiner de informações

O contêiner insights é um recurso projetado para monitorar o desempenho de cargas de trabalho de contêiner implantadas em:

- Clusters Managed Kubernetes hospedados no [serviço de kubernetes do Azure (AKs)](../../aks/intro-kubernetes.md)
- Clusters kubernetes autogerenciados hospedados no Azure usando o [mecanismo AKs](https://github.com/Azure/aks-engine)
- [Instâncias de Contêiner do Azure](../../container-instances/container-instances-overview.md)
- Clusters kubernetes autogerenciados hospedados no [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) ou no local
- [Red Hat OpenShift no Azure](../../openshift/intro-openshift.md)
- [Kubernetes habilitado para Arc do Azure](../../azure-arc/kubernetes/overview.md) (versão prévia)

As informações de contêiner dão suporte a clusters que executam o sistema operacional Linux e Windows Server 2019. Os tempos de execução do contêiner aos quais ele dá suporte são Docker, Moby e qualquer tempo de execução compatível com o CRI, como O CRI-O e O contêiner.

Monitorar os contêineres é fundamental, principalmente ao executar um cluster de produção em grande escala e com vários aplicativos.

As informações de contêiner fornecem visibilidade de desempenho coletando métricas de memória e processador de controladores, nós e contêineres que estão disponíveis no kubernetes por meio da API de métricas. Os logs do contêiner também são coletados.  Depois de habilitar o monitoramento de clusters do kubernetes, as métricas e os logs são coletados automaticamente para você por meio de uma versão em contêiner do agente de Log Analytics para Linux. As métricas são gravadas no repositório de métricas e os dados de log são gravados no repositório de logs associado ao seu espaço de trabalho [log Analytics](../logs/log-query-overview.md) .

![Arquitetura de informações de contêiner](./media/container-insights-overview/azmon-containers-architecture-01.png)

## <a name="what-does-container-insights-provide"></a>O que o contêiner insights fornece?

As informações de contêiner fornecem uma experiência de monitoramento abrangente usando diferentes recursos do Azure Monitor. Esses recursos permitem que você entenda o desempenho e a integridade do cluster kubernetes executando o sistema operacional Linux e Windows Server 2019 e as cargas de trabalho do contêiner. Com as informações de contêiner, você pode:

* Identificar contêineres AKS que estão em execução no nó e sua utilização média de processador e memória. Esse conhecimento pode ajudá-lo a identificar gargalos de recursos.
* Identifique a utilização de processador e de memória de grupos de contêineres e seus contêineres hospedados em Instâncias de Contêiner do Azure.
* Identificar em que local o contêiner reside em um controlador ou em um pod. Esse conhecimento pode ajudá-lo a exibir o desempenho geral do controlador ou do pod.
* Examine a utilização de recursos de cargas de trabalho em execução no host não relacionadas aos processos padrão que dão suporte ao pod.
* Compreender o comportamento do cluster sob cargas mais pesadas e médias. Esse conhecimento pode ajudá-lo a identificar as necessidades de capacidade e determinar a carga máxima que o cluster pode sustentar.
* Configure alertas para notificá-lo proativamente ou registre-o quando a utilização de CPU e memória em nós ou contêineres exceder seus limites ou quando uma alteração de estado de integridade ocorrer no cluster no acúmulo de integridade de infraestrutura ou de nós.
* Integre com o [Prometheus](https://prometheus.io/docs/introduction/overview/) para exibir as métricas de aplicativo e carga de trabalho coletadas de nós e kubernetes usando [consultas](container-insights-log-search.md) para criar alertas personalizados, painéis e executar análise detalhada.
* Monitore cargas de trabalho de contêiner [implantadas](https://github.com/Azure/aks-engine) no mecanismo do AKS no local e no [mecanismo de AKs no Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview).
* Monitore cargas [de trabalho de contêiner implantadas no Azure Red Hat OpenShift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >O suporte para o Azure Red Hat OpenShift é um recurso em visualização pública no momento.
    >

* Monitore cargas [de trabalho de contêiner implantadas no Azure kubernetes habilitados para o Arc (versão prévia)](../../azure-arc/kubernetes/overview.md).

As principais diferenças no monitoramento de um cluster do Windows Server em comparação a um cluster do Linux são as seguintes:

- O Windows não tem uma métrica de RSS de memória e, como resultado, não está disponível para o nó e os contêineres do Windows. A métrica do [conjunto de trabalho](/windows/win32/memory/working-set) está disponível.
- As informações de capacidade de armazenamento em disco não estão disponíveis para nós do Windows.
- Somente ambientes de Pod são monitorados, não ambientes de Docker.
- Com a versão de visualização, há suporte para um máximo de 30 contêineres do Windows Server. Essa limitação não se aplica a contêineres do Linux.

Confira o vídeo a seguir fornecendo um aprofundamento no nível intermediário para ajudá-lo a saber mais sobre como monitorar o cluster do AKS com informações de contêiner.

> [!VIDEO https://www.youtube.com/embed/XEdwGvS2AwA]

## <a name="how-do-i-access-this-feature"></a>Como acessar esse recurso?

Você pode acessar as informações de contêiner de duas maneiras, de Azure Monitor ou diretamente do cluster AKS selecionado. De Azure Monitor, você tem uma perspectiva global de todos os contêineres implantados, que são monitorados e que não estão, permitindo que você pesquise e filtre entre suas assinaturas e grupos de recursos e, em seguida, aprofunde-se em informações de contêiner do contêiner selecionado.  Caso contrário, você pode acessar o recurso diretamente de um contêiner AKS selecionado na página AKS.

![Visão geral dos métodos para acessar informações de contêiner](./media/container-insights-overview/azmon-containers-experience.png)

Se você estiver interessado em monitorar e gerenciar seus hosts de contêiner do Docker e do Windows em execução fora do AKS para exibir a configuração, a auditoria e a utilização de recursos, consulte a [solução de monitoramento de contêiner](./containers.md).

## <a name="next-steps"></a>Próximas etapas

Para começar a monitorar o cluster kubernetes, examine [como habilitar o insights de contêiner](container-insights-onboard.md) para entender os requisitos e os métodos disponíveis para habilitar o monitoramento.
