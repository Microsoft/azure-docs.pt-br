---
title: Clusters gerenciados do Service Fabric (versão prévia)
description: Os clusters gerenciados do Service Fabric são uma evolução do modelo de recurso de cluster do Azure Service Fabric que simplifica a sua implantação e gerenciamento de cluster.
ms.topic: overview
ms.date: 09/28/2020
ms.openlocfilehash: 3d26a92126491662d5c51b3c4e8900ffa547f830
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410367"
---
# <a name="service-fabric-managed-clusters-preview"></a>Clusters gerenciados do Service Fabric (versão prévia)

Os clusters gerenciados do Service Fabric são uma evolução do modelo de recurso de cluster do Azure Service Fabric que simplifica a sua experiência de implantação e gerenciamento de cluster.

O modelo do ARM (Azure Resource Manager) para clusters tradicionais do Service Fabric exige que você defina um recurso de cluster junto com vários recursos de suporte, que precisam estar "conectados" corretamente (na implantação e durante todo o ciclo de vida do cluster) para que o cluster e os serviços dele funcionem corretamente. Por outro lado, o modelo de encapsulamento dos clusters gerenciados do Service Fabric consiste em um recurso do *cluster gerenciado do Service Fabric*. Todos os recursos subjacentes do cluster são abstraídos e gerenciados pelo Azure em seu nome.

**Modelo de cluster tradicional do Service Fabric**
![Modelo de cluster tradicional do Service Fabric][sf-composition]

**Modelo de cluster gerenciado do Service Fabric**
![Modelo de cluster encapsulado do Service Fabric][sf-encapsulation]

Em termos de tamanho e complexidade, o modelo do ARM para um cluster gerenciado do Service Fabric tem de cerca de cem linhas de JSON em relação à cerca de mil linhas necessárias para definir um cluster do Service Fabric típico:

| Recursos do Service Fabric | Recursos de cluster gerenciado do Service Fabric |
|----------|-----------|
| Cluster do Service Fabric | Cluster gerenciado do Service Fabric |
| Conjuntos de dimensionamento de máquinas virtuais | |
| Balanceador de carga | |
| Endereço IP público | |
| Conta(s) de armazenamento | |
| Rede virtual | |

Os clusters gerenciados do Service Fabric fornecem uma série de vantagens em relação aos clusters tradicionais:

**Implantação e gerenciamento de cluster simplificados**
- Implantar e gerenciar um recurso do Azure
- Gerenciamento de certificados e rotação automática
- Operações de escala simplificadas

**Impedir erros operacionais**
- Impedir incompatibilidades de configuração com os recursos subjacentes
- Bloquear operações não seguras (como excluir um nó de semente)

**Práticas recomendadas por padrão**
- Configurações de durabilidade e confiabilidade simplificadas

Não há nenhum custo adicional para os clusters gerenciados do Service Fabric além do custo dos recursos subjacentes exigidos para o cluster.

## <a name="service-fabric-managed-cluster-skus"></a>SKUs do cluster gerenciado do Service Fabric

Os clusters gerenciados do Service Fabric estão disponíveis nos SKUs Básico e Standard.

| Recurso | Basic | Standard |
| ------- | ----- | -------- |
| Recurso de rede (SKU para [Load Balancer](../load-balancer/skus.md), [IP público](../virtual-network/public-ip-addresses.md)) | Basic | Standard |
| Contagem mínima de nós (instância de VM) | 3 | 5 |
| Contagem máxima de nós por tipo de nó | 100 | 100 |
| Contagem máxima de tipo de nó | 1 | 20 |
| Adicionar/remover tipos de nós | Não | Sim |
| Redundância de zona | Não | Sim |

## <a name="service-fabric-managed-cluster-feature-roadmap"></a>Implantar um roteiro do recurso de cluster gerenciado do Service Fabric
Como esta é uma versão prévia inicial dos clusters gerenciados do Service Fabric, há algumas lacunas a serem consideradas. Esses recursos estarão disponíveis em versões futuras. 

* Como publicar aplicativos em clusters diretamente do Visual Studio
* Identidades gerenciadas 
* Implantações de aplicativo do ARM 
* Zonas de Disponibilidades 
* Proxy inverso 
* Dimensionamento automático 
* Atualização de regras de NSG 
* Atualizações automáticas do sistema operacional

## <a name="next-steps"></a>Próximas etapas

Para começar a usar os clusters gerenciados do Service Fabric, experimente o guia de início rápido:

> [!div class="nextstepaction"]
> [Criar um cluster gerenciado do Service Fabric (versão prévia)](quickstart-managed-cluster-template.md)


[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png