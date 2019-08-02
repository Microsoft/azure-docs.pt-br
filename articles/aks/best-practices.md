---
title: Práticas recomendadas para o Serviço de Kubernetes do Azure (AKS)
description: Coleção de práticas recomendadas para o operador e o desenvolvedor do cluster para criar e gerenciar aplicativos no Serviço de Kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 12/07/2018
ms.author: mlearned
ms.openlocfilehash: 7127894b364ac8f0fe1d87e13150d5522f5473e2
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615956"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Práticas recomendadas para o operador e o desenvolvedor do cluster para criar e gerenciar aplicativos no Serviço de Kubernetes do Azure (AKS)

Para compilar e executar aplicativos com êxito no Serviço de Kubernetes do Azure (AKS), há algumas considerações importantes para entender e implementar. Essas áreas incluem a multilocação e recursos do agendador, cluster e segurança do pod, ou continuidade dos negócios e recuperação de desastres. As seguintes práticas recomendadas são agrupadas para ajudar os operadores e desenvolvedores do cluster a entender as considerações para cada uma dessas áreas e implementarem os recursos adequados.

Essas práticas recomendadas e os artigos conceituais foram escritos em conjunto com o grupo de produtos do AKS, equipes de engenharia e equipes de campo incluindo especialistas experientes do mundo todo (GBBs).

## <a name="cluster-operator-best-practices"></a>Práticas recomendadas para o operador de cluster

Como um operador de cluster, trabalhe junto com os proprietários e desenvolvedores do aplicativo para compreender suas necessidades. Em seguida, você pode usar as seguintes práticas recomendadas para configurar seus clusters AKS, conforme necessário.

**Multilocação**

* [Práticas recomendadas para isolamento do cluster](operator-best-practices-cluster-isolation.md)
    * Inclui os componentes principais de multilocação e isolamento lógico com namespaces.
* [Práticas recomendadas para recursos básicos do agendador](operator-best-practices-scheduler.md)
    * Inclui o uso de cotas de recursos e os orçamentos de interrupção de pod.
* [Práticas recomendadas para recursos avançados do agendador](operator-best-practices-advanced-scheduler.md)
    * Inclui o uso de taints e tolerations, seletores de nó e afinidade e a afinidade entre pod e antiafinidade.
* [Práticas recomendadas para autenticação e autorização](operator-best-practices-identity.md)
    * Inclui a integração com o Azure Active Directory, usando controles de acesso baseado em função (RBAC) e identidades de pod.

**Segurança**

* [Práticas recomendadas para atualizações e segurança de cluster](operator-best-practices-cluster-security.md)
    * Inclui a proteção de acesso ao servidor de API, limitando o acesso do contêiner e o gerenciamento de atualizações e reinicializações de nó.
* [Práticas recomendadas para gerenciamento e segurança de imagens de contêiner](operator-best-practices-container-image-management.md).
    * Inclui proteção a imagem e tempos de execução e compilações automatizadas nas atualizações da imagem base.
* [Práticas recomendadas para segurança do pod](developer-best-practices-pod-security.md)
    * Inclui acesso seguro aos recursos, limitando a exposição de credencial e usando as identidades de pod e cofres de chaves digitais.

**Rede e armazenamento**

* [Práticas recomendadas para conectividade de rede](operator-best-practices-network.md)
    * Inclui modelos de rede diferentes, usando firewalls de aplicativo de entrada e da Web (WAF) e protegendo o acesso SSH ao nó.
* [Práticas recomendadas para armazenamento e backup](operator-best-practices-storage.md)
    * Inclui escolher o tipo de armazenamento e o tamanho de nó apropriados, os volumes de provisionamento dinâmico e os backups de dados.

**Executar cargas de trabalho prontas para empresas**

* [Práticas recomendadas para continuidade dos negócios e recuperação de desastres](operator-best-practices-multi-region.md)
    * Inclui o uso de pares de regiões, vários clusters com o Gerenciador de Tráfego do Azure e a replicação geográfica de imagens de contêiner.

## <a name="developer-best-practices"></a>Melhores práticas do desenvolvedor

Como desenvolvedor ou proprietário do aplicativo, você pode simplificar sua experiência de desenvolvimento e definir necessidades de desempenho do aplicativo.

* [Práticas recomendadas para os desenvolvedores de aplicativos gerenciarem recursos](developer-best-practices-resource-management.md)
    * Inclui a definição de pod solicitações de recursos e limites, de pod, configuração de ferramentas de desenvolvimento e verificação de problemas do aplicativo.
* [Práticas recomendadas para segurança do pod](developer-best-practices-pod-security.md)
    * Inclui acesso seguro aos recursos, limitando a exposição de credencial e usando as identidades de pod e cofres de chaves digitais.

## <a name="kubernetes--aks-concepts"></a>Conceitos de Kubernetes / AKS

Para ajudar a entender alguns dos recursos e componentes dessas práticas recomendadas, você também pode consultar os seguintes artigos conceituais para clusters no Serviço de Kubernetes do Azure (AKS):

* [Principais conceitos do Kubernetes](concepts-clusters-workloads.md)
* [Acesso e identidade](concepts-identity.md)
* [Conceitos de segurança](concepts-security.md)
* [Conceitos de rede](concepts-network.md)
* [Opções de armazenamento](concepts-storage.md)
* [Opções de dimensionamento](concepts-scale.md)

## <a name="next-steps"></a>Próximas etapas

Se você precisa para começar com o AKS, siga um dos guias de início rápido para implantar um cluster do Serviço de Kubernetes do Azure (AKS) usando a [CLI do Azure](kubernetes-walkthrough.md) ou o [portal do Azure](kubernetes-walkthrough-portal.md).
