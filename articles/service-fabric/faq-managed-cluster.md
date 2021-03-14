---
title: Perguntas comuns sobre Service Fabric clusters gerenciados
description: Perguntas frequentes sobre Service Fabric clusters gerenciados, incluindo recursos, casos de uso e cenários comuns.
ms.topic: troubleshooting
ms.author: pepogors
author: peterpogorski
ms.date: 02/15/2021
ms.custom: references_regions
ms.openlocfilehash: aa77896ba88d0ffd0a6f94a84603b5f4a1803357
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633080"
---
# <a name="service-fabric-managed-clusters-frequently-asked-questions"></a>Perguntas mais frequentes sobre os clusters gerenciados do Service Fabric

Aqui estão algumas perguntas frequentes e respostas para Service Fabric clusters gerenciados (versão prévia).

## <a name="general"></a>Geral

### <a name="what-are-service-fabric-managed-clusters"></a>O que são Service Fabric clusters gerenciados?

Service Fabric clusters gerenciados são uma evolução do modelo de recurso de Cluster Service Fabric projetado para facilitar a implantação e o gerenciamento de clusters. Um Service Fabric cluster gerenciado usa o modelo de encapsulamento de Azure Resource Manager para que um usuário só precise definir e implantar um único recurso de cluster em comparação com os muitos recursos independentes que eles devem implantar hoje (conjunto de dimensionamento de máquinas virtuais, Load Balancer, IP e muito mais).

### <a name="what-regions-are-supported-in-the-preview"></a>Quais regiões têm suporte na visualização?

As regiões com suporte para a visualização Service Fabric clusters gerenciados incluem `centraluseuap` , `eastus2euap` , `eastasia` ,, `northeurope` `westcentralus` e `eastus2` .

### <a name="can-i-do-an-in-place-migration-of-my-existing-service-fabric-cluster-to-a-managed-cluster-resource"></a>Posso fazer uma migração in-loco do meu cluster existente do Service Fabric para um recurso de cluster gerenciado?

Não. Neste momento, você precisaria criar um novo recurso de Cluster Service Fabric para usar o novo Service Fabric tipo de recurso de cluster gerenciado.

### <a name="is-there-an-additional-cost-for-service-fabric-managed-clusters"></a>Há um custo adicional para Service Fabric clusters gerenciados?

Não. Não há nenhum custo adicional associado a um Service Fabric cluster gerenciado além do custo dos recursos de computação, armazenamento e rede subjacentes necessários para o cluster.

### <a name="is-there-a-new-sla-introduced-by-the-service-fabric-managed-cluster-resource"></a>Há um novo SLA introduzido pelo Service Fabric recurso de cluster gerenciado?

O SLA não muda do modelo de recurso de Service Fabric atual.

### <a name="what-is-the-difference-between-a-basic-and-standard-sku-cluster"></a>Qual é a diferença entre um cluster SKU básico e Standard?

Um cluster de SKU básico significa que a maioria das configurações é fornecida pelo provedor de recursos Service Fabric. Os clusters de SKU básicos devem ser usados para ambientes de teste e pré produção. Um cluster SKU padrão permite que os usuários configurem o cluster para atender especificamente às suas necessidades. Para obter mais informações, consulte [Service Fabric SKUs de cluster gerenciado](./overview-managed-cluster.md#service-fabric-managed-cluster-skus).

## <a name="cluster-deployment-and-management"></a>Implantação e gerenciamento de cluster

### <a name="i-run-custom-script-extensions-on-my-virtual-machine-scale-set-can-i-continue-to-do-that-with-a-managed-service-fabric-resource"></a>Eu executo extensões de script personalizado no meu conjunto de dimensionamento de máquinas virtuais, posso continuar a fazer isso com um recurso gerenciado Service Fabric?

Sim, você pode especificar extensões de VM em tipos de nó de cluster gerenciado. Para obter mais informações, consulte [Adicionar uma extensão de conjunto de dimensionamento a um Service Fabric tipo de nó de cluster gerenciado](how-to-managed-cluster-vmss-extension.md).

### <a name="i-want-to-have-an-internal-only-load-balancer-is-that-possible"></a>Quero ter um balanceador de carga somente interno, isso é possível?

Não. Atualmente, não é possível ter um balanceador de carga somente interno. Recomendamos bloquear as regras do NSG (grupo de segurança de rede) para bloquear qualquer tráfego de entrada/saída indesejado.

### <a name="can-i-autoscale-my-cluster"></a>Posso fazer o dimensionamento automático do meu cluster?

O dimensionamento automático não está disponível no momento na visualização.

### <a name="can-i-deploy-my-cluster-across-availability-zones"></a>Posso implantar meu cluster em zonas de disponibilidade?

Os clusters de zona de disponibilidade cruzada não estão disponíveis no momento na visualização.

### <a name="can-i-select-between-automatic-and-manual-upgrades-for-my-cluster-runtime"></a>Posso selecionar entre atualizações automáticas e manuais para o tempo de execução do cluster?

Na visualização, todas as atualizações de tempo de execução serão concluídas automaticamente.

## <a name="applications"></a>Aplicativos

### <a name="is-there-a-local-development-experience-for-service-fabric-managed-clusters"></a>Há uma experiência de desenvolvimento local para Service Fabric clusters gerenciados?

A experiência de desenvolvimento local permanece inalterada de clusters de Service Fabric existentes. Para obter mais informações, consulte [configurar seu ambiente de desenvolvimento](./service-fabric-get-started.md) para obter mais detalhes sobre a experiência de desenvolvimento local.

### <a name="can-i-deploy-my-applications-as-an-azure-resource-manager-resource"></a>Posso implantar meus aplicativos como um recurso Azure Resource Manager?

Sim. Foi adicionado suporte para implantar aplicativos como um recurso de Azure Resource Manager (além da implantação usando o PowerShell e a CLI). Para começar, consulte [implantar um Service Fabric aplicativo de cluster gerenciado usando o modelo ARM](how-to-managed-cluster-app-deployment-template.md).
