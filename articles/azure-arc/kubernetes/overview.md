---
title: Visão geral do Kubernetes habilitado para o Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Este artigo fornece uma visão geral do Kubernetes habilitado para o Azure Arc.
keywords: Kubernetes, Arc, Azure, contêineres
ms.custom: references_regions
ms.openlocfilehash: 7e48ebf98f12e79cb154fb50d8e6dbdfaea1cd95
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371300"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>O que é a versão prévia do Kubernetes habilitado para o Azure Arc?

Você pode anexar e configurar clusters do Kubernetes dentro ou fora do Azure usando a versão prévia do Kubernetes habilitado para o Azure Arc. Quando um cluster do Kubernetes é anexado ao Azure Arc, ele aparecerá na portal do Azure. Ele terá uma ID do Azure Resource Manager e uma identidade gerenciada. Os clusters são anexados a assinaturas do Azure padrão, estão localizados em um grupo de recursos e podem receber marcas, assim como qualquer outro recurso do Azure. 

Para conectar um cluster do Kubernetes ao Azure, o administrador do cluster precisa implantar agentes. Esses agentes são executados em um namespace do Kubernetes chamado `azure-arc` e são implantações padrão do Kubernetes. Os agentes são responsáveis pela conectividade com o Azure, coletando métricas e logs do Azure Arc e inspecionando as solicitações de configuração. 

O Kubernetes habilitado para o Azure Arc dá suporte ao SSL padrão do setor para proteger dados em trânsito. Além disso, os dados são armazenados criptografados quando em repouso em um banco de dados Azure Cosmos DB para garantir a confidencialidade dos dados.
 
> [!NOTE]
> O Kubernetes habilitado para Azure Arc está em versão prévia. Não o recomendamos para carga de trabalho de produção.

## <a name="supported-kubernetes-distributions"></a>Distribuições do Kubernetes compatíveis

O Kubernetes habilitado para o Azure Arc funciona com qualquer cluster do Kubernetes certificado pela CNCF (Cloud Native Computing Foundation), como o Mecanismo do AKS no Azure, o Mecanismo do AKS no Azure Stack Hub, o GKE, o EKS e o cluster do VMware vSphere.

Os recursos do Kubernetes habilitado para o Azure Arc foram testados pela equipe do Arc nas seguintes distribuições:
* Red Hat OpenShift 4.3
* Rancher RKE 1.0.8
* Canonical Charmed Kubernetes 1.18
* Mecanismo do AKS
* Mecanismo do AKS no Azure Stack Hub
* AKS no Azure Stack HCI
* Provedor de API de cluster do Azure

## <a name="supported-scenarios"></a>Cenários com suporte 

O Kubernetes habilitado para o Azure Arc dá suporte a estes cenários: 

* Conectar o Kubernetes em execução fora do Azure para inventário, agrupamento e marcação.

* Implantar aplicativos e aplicar a configuração usando o gerenciamento de configuração baseado em GitOps. 

* Usar o Azure Monitor para contêineres para exibir e monitorar seus clusters. 

* Aplicar políticas usando o Azure Policy para Kubernetes. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Regiões com suporte 

Atualmente, há suporte para o Kubernetes habilitado para o Azure Arc nestas regiões: 

* Leste dos EUA 
* Europa Ocidental

## <a name="frequently-asked-questions"></a>Perguntas frequentes

* Qual é a diferença entre o Kubernetes habilitado para o Azure Arc e o AKS (Serviço Kubernetes do Azure)?

    O AKS (Serviço de Kubernetes do Azure) é a oferta de Kubernetes gerenciado do Azure. O AKS simplifica a implantação de um cluster do Kubernetes gerenciado no Azure. O AKS reduz a complexidade e a sobrecarga operacional de gerenciar o Kubernetes passando grande parte dessa responsabilidade para o Azure. Os mestres de Kubernetes são gerenciados pelo Azure. Você só gerencia e mantém os nós de agente.

    O Kubernetes habilitado para o Azure Arc permite que você conecte clusters do Kubernetes ao Azure para estender as funcionalidades de gerenciamento do Azure, como Azure Monitor e Azure Policy. A manutenção do cluster do Kubernetes subjacente é feita por você.

* Preciso conectar meus clusters do Serviço de Kubernetes do Azure em execução no Azure ao Azure Arc?

    Não. Todos os recursos do Kubernetes habilitado para o Azure Arc, como Azure Monitor e Azure Policy (Gatekeeper), estão disponíveis nativamente com o AKS, que já tem uma representação de recursos no Azure.
    
* Devo conectar meu cluster do AKS no Azure Stack HCI ao Azure Arc? E quanto aos clusters do Kubernetes em execução no Azure Stack Hub ou no Azure Stack Edge?

    Sim, há benefícios ao conectar esses clusters ao Azure Arc. Ele fornece uma representação de recursos para esses clusters do Kubernetes no Azure Resource Manager. Usando essa representação de recursos, funcionalidades como a Configuração de Cluster, o Azure Monitor e o Azure Policy (Gatekeeper) podem ser estendidas para esses clusters do Kubernetes

## <a name="next-steps"></a>Próximas etapas

* [Conectar um cluster](./connect-cluster.md)
