---
title: Perguntas comuns sobre Service Fabric clusters gerenciados
description: Perguntas frequentes sobre Service Fabric clusters gerenciados, incluindo recursos, casos de uso e cenários comuns.
ms.topic: troubleshooting
ms.author: pepogors
author: peterpogorski
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 4dc41d2c13c834657534971041440bb744cfca38
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319834"
---
# <a name="service-fabric-managed-clusters-frequently-asked-questions"></a>Perguntas mais frequentes sobre os clusters gerenciados do Service Fabric

Há muitas perguntas frequentes sobre o que Service Fabric clusters gerenciados (versão prévia) podem fazer e como eles devem ser usados. Este documento aborda muitas dessas perguntas comuns e suas respostas.

## <a name="general"></a>Geral

### <a name="what-are-service-fabric-managed-clusters"></a>O que são Service Fabric clusters gerenciados?

Service Fabric clusters gerenciados são uma evolução do modelo de recurso de Cluster Service Fabric projetado para facilitar a implantação e o gerenciamento de clusters. Um Service Fabric cluster gerenciado usa o modelo de encapsulamento de Azure Resource Manager para que um usuário só precise definir e implantar um único recurso de cluster em comparação com os muitos recursos independentes que eles devem implantar hoje (conjunto de dimensionamento de máquinas virtuais, Load Balancer, IP e muito mais).

### <a name="what-regions-are-supported-in-the-preview"></a>Quais regiões têm suporte na visualização?

As regiões com suporte para a visualização Service Fabric clusters gerenciados incluem `centraluseuap` , `eastus2euap` , `eastasia` ,, `northeurope` `westcentralus` e `eastus2` .

### <a name="can-i-do-an-in-place-migration-of-my-existing-service-fabric-cluster-to-a-managed-cluster-resource"></a>Posso fazer uma migração in-loco do meu cluster existente do Service Fabric para um recurso de cluster gerenciado?

Neste momento, você precisaria criar um novo recurso de Cluster Service Fabric para usar o novo Service Fabric tipo de recurso de cluster gerenciado.

### <a name="is-there-an-additional-cost-for-service-fabric-managed-clusters"></a>Há um custo adicional para Service Fabric clusters gerenciados?

Não, não há nenhum custo adicional associado a um Service Fabric cluster gerenciado além do custo dos recursos de computação, armazenamento e rede subjacentes necessários para o cluster.

### <a name="is-there-a-new-sla-introduced-by-the-service-fabric-managed-cluster-resource"></a>Há um novo SLA introduzido pelo Service Fabric recurso de cluster gerenciado?

O SLA não muda do modelo de recurso de Service Fabric atual.

### <a name="what-is-the-difference-between-a-basic-and-standard-sku-cluster"></a>Qual é a diferença entre um cluster SKU básico e Standard?

Um cluster de SKU básico significa que a maioria das configurações é fornecida pelo provedor de recursos Service Fabric. Os clusters de SKU básicos devem ser usados para ambientes de teste e pré produção. Um cluster SKU padrão permite que os usuários configurem o cluster para atender especificamente às suas necessidades. Para obter mais informações, consulte [Service Fabric SKUs de cluster gerenciado](./overview-managed-cluster.md#service-fabric-managed-cluster-skus) para obter mais detalhes.

## <a name="cluster-deployment-and-management"></a>Implantação e gerenciamento de cluster

### <a name="i-run-custom-script-extensions-on-my-virtual-machine-scale-set-can-i-continue-to-do-that-with-a-managed-service-fabric-resource"></a>Eu executo extensões de script personalizado no meu conjunto de dimensionamento de máquinas virtuais, posso continuar a fazer isso com um recurso gerenciado Service Fabric?

Sim, você ainda pode especificar extensões de VM em um tipo de nó. Para obter mais informações, consulte o exemplo de extensão do tipo de nó para obter mais detalhes.

### <a name="i-want-to-have-an-internal-only-load-balancer-is-that-possible"></a>Quero ter um balanceador de carga somente interno, isso é possível?

Atualmente, não é possível ter um balanceador de carga somente interno. Recomendamos bloquear as regras do grupo de segurança de rede para bloquear qualquer tráfego de entrada/saída indesejado.

### <a name="can-i-autoscale-my-cluster"></a>Posso fazer o dimensionamento automático do meu cluster? 
O dimensionamento automático não está disponível no momento na visualização.

### <a name="can-i-deploy-my-cluster-across-availability-zones"></a>Posso implantar meu cluster em zonas de disponibilidade? 
Os clusters de zona de disponibilidade cruzada não estão disponíveis no momento na visualização.

### <a name="can-i-select-between-automatic-and-manual-upgrades-for-my-cluster-runtime"></a>Posso selecionar entre atualizações automáticas e manuais para o tempo de execução do cluster? 
Na visualização, todas as atualizações de tempo de execução serão concluídas automaticamente.

## <a name="applications"></a>Aplicativos

### <a name="is-there-a-local-development-experience-for-service-fabric-managed-clusters"></a>Há uma experiência de desenvolvimento local para Service Fabric clusters gerenciados?

A experiência de desenvolvimento local permanece inalterada de clusters de Service Fabric existentes. Para obter mais informações, consulte [criar um aplicativo .net](./service-fabric-quickstart-dotnet.md) para obter mais detalhes sobre a experiência de desenvolvimento local.

### <a name="can-i-deploy-my-applications-as-an-azure-resource-manager-resource"></a>Posso implantar meus aplicativos como um recurso Azure Resource Manager?

Na visualização, você não pode implantar aplicativos como um recurso Azure Resource Manager. Os aplicativos devem ser implantados conectando-se diretamente ao cluster por meio do PowerShell ou da CLI. Essa funcionalidade será adicionada antes que os clusters de Service Fabric gerenciadas insiram disponibilidade geral.