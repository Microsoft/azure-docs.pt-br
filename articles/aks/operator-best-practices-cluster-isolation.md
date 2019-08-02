---
title: Melhores práticas do operador - Isolamento de cluster no Azure Kubernetes Services (AKS)
description: Aprenda as práticas recomendadas do operador de cluster para isolamento no Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: mlearned
ms.openlocfilehash: 8150e184f0c7533d5a6e7e4847bf126206f5e6c6
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614925"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para isolamento de cluster no Azure Kubernetes Service (AKS)

À medida que você gerencia clusters no Serviço de Kubernetes do Azure (AKS), geralmente é necessário isolar equipes e cargas de trabalho. O AKS fornece flexibilidade em como você pode executar clusters de vários inquilinos e isolar recursos. Para maximizar seu investimento no Kubernetes, esses recursos de multilocação e isolamento devem ser reconhecidos e implementados.

Este artigo de práticas recomendadas se concentra no isolamento para operadores de cluster. Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Planejar clusters de vários inquilinos e separação de recursos
> * Use isolamento lógico ou físico em seus clusters do AKS

## <a name="design-clusters-for-multi-tenancy"></a>Design de clusters para multi locação

O Kubernetes fornece recursos que permitem isolar logicamente equipes e cargas de trabalho no mesmo cluster. O objetivo deve ser fornecer o menor número de privilégios, com o escopo definido para os recursos de que cada equipe precisa. Um [Namespace][k8s-namespaces] no Kubernetes cria um limite de isolamento lógico. Recursos e considerações adicionais do kubernetes para isolamento e multi locação incluem as seguintes áreas:

* **O agendamento** inclui o uso de recursos básicos, como cotas de recursos e orçamentos de interrupção de conjuntos. Para obter mais informações sobre esses recursos, consulte [práticas recomendadas para recursos do Agendador básica no AKS][aks-best-practices-scheduler].
  * Os recursos mais avançados do planejador incluem manchas e tolerâncias, seletores de nó e afinidade de nó e pod ou antiafinidade. Para obter mais informações sobre esses recursos, consulte [práticas recomendadas para recursos avançados do Agendador no AKS][aks-best-practices-advanced-scheduler].
* **Rede** inclui o uso de políticas de rede para controlar o fluxo de tráfego dentro e fora dos pods.
* **Autenticação e autorização** incluem o usuário de RBAC (controle de acesso baseado em função) e integração do Azure Active Directory (AD), identidades de conjuntos e segredos no Cofre de Chaves do Azure. Para obter mais informações sobre esses recursos, consulte [práticas recomendadas para autenticação e autorização no AKS][aks-best-practices-identity].
* **Contêineres** incluem políticas de segurança do pod, contextos de segurança do pod, imagens de varredura e tempos de execução para vulnerabilidades. Envolve também o uso do App Armor ou do Seccomp (Secure Computing) para restringir o acesso ao contêiner ao nó subjacente.

## <a name="logically-isolate-clusters"></a>Isolar logicamente clusters

**Orientação sobre práticas recomendadas**: use isolamento lógico para separar equipes e projetos. Tente minimizar o número de clusters AKS físicos que você implanta para isolar equipes ou aplicativos.

Com o isolamento lógico, um único cluster AKS pode ser usado para várias cargas de trabalho, equipes ou ambientes. Kubernetes [Namespaces][k8s-namespaces] formam o limite de isolamento lógico para cargas de trabalho e recursos.

![Isolamento lógico de um cluster do Kubernetes em AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

A separação lógica de aglomerados geralmente fornece uma densidade de vagem mais alta do que aglomerados isolados fisicamente. Há menos capacidade de computação em excesso que fica inativa no cluster. Quando combinado com o auto escalador de cluster do Kubernetes, você pode dimensionar o número de nós para cima ou para baixo para atender às demandas. Essa abordagem de melhor prática para o escalonamento automático permite que você execute apenas o número de nós necessários e minimize os custos.

Os ambientes do Kubernetes, no AKS ou em outro lugar, não estão completamente seguros para uso de vários locatário hostis. Recursos de segurança adicionais, como *Política de Segurança Pod* e controles de acesso baseado em função (RBAC) mais refinados para nós dificultam as explorações. No entanto, para ter uma segurança de verdade ao executar cargas de trabalho de vários locatários hostis, um hipervisor é o único nível de segurança no qual você deve confiar. O domínio de segurança para o Kubernetes se torna o cluster inteiro, não um nó individual. Para esses tipos de cargas de trabalho de vários locatários hostis, você deve usar clusters fisicamente isolados.

## <a name="physically-isolate-clusters"></a>Isolar fisicamente clusters

**Orientação sobre práticas recomendadas**: minimize o uso de isolamento físico para cada equipe ou implantação de aplicativo separada. Em vez disso, use o isolamento *lógico*, conforme discutido na seção anterior.

Uma abordagem comum para o isolamento de cluster é usar clusters AKS fisicamente separados. Neste modelo de isolamento, as equipes ou cargas de trabalho são atribuídas a seu próprio cluster AKS. Essa abordagem geralmente se parece com a maneira mais fácil de isolar cargas de trabalho ou equipes, mas adiciona mais sobrecarga administrativa e financeira. Agora você precisa manter esses vários clusters e ter que fornecer acesso e atribuir permissões individualmente. Você também é cobrado por todos os nós individuais.

![Isolamento físico de clusters de Kubernetes individuais em AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Clusters fisicamente separados geralmente têm uma baixa densidade de vagem. Como cada equipe ou carga de trabalho possui seu próprio cluster AKS, o cluster geralmente é provisionado em excesso com recursos de computação. Geralmente, um pequeno número de pods é planejado nesses nós. A capacidade não utilizada nos nós não pode ser usada para aplicativos ou serviços em desenvolvimento por outras equipes. Esses recursos em excesso contribuem para os custos adicionais em clusters fisicamente separados.

## <a name="next-steps"></a>Próximas etapas

Este artigo concentra-se em isolamento de cluster. Para obter mais informações sobre operações de cluster no AKS, consulte as seguintes práticas recomendadas:

* [Recursos básicos de Agendador Kubernetes][aks-best-practices-scheduler]
* [Recursos avançados de Agendador Kubernetes][aks-best-practices-advanced-scheduler]
* [Autenticação e autorização][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
