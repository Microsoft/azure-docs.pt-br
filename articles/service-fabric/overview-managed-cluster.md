---
title: Clusters gerenciados do Service Fabric (versão prévia)
description: Os clusters gerenciados do Service Fabric são uma evolução do modelo de recurso de cluster do Azure Service Fabric que simplifica a sua implantação e gerenciamento de cluster.
ms.topic: overview
ms.date: 02/15/2021
ms.openlocfilehash: 271852214097ee96ba6b10de7a94904981cd8ef8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041222"
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

## <a name="whats-new-for-service-fabric-managed-clusters"></a>Novidades para os clusters gerenciados pelo Service Fabric

Os recursos mais recentes para a versão prévia dos clusters gerenciados pelo Service Fabric incluem suporte para:

* [Implantação de aplicativos usando modelos do ARM](how-to-managed-cluster-app-deployment-template.md)
* [Atualizações automáticas do SO](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades)
* [Criptografia do disco](how-to-enable-managed-cluster-disk-encryption.md)
* [Aplicação de regras NSG](how-to-managed-cluster-networking.md)

Os recursos a serem adicionados em versões futuras incluem:

* Implantação de aplicativos usando o Visual Studio
* Suporte a identidades gerenciadas
* Zonas de Disponibilidades
* Proxy inverso
* Dimensionamento automático

## <a name="next-steps"></a>Próximas etapas

Para começar a usar clusters gerenciados pelo Service Fabric, experimente o guia de início rápido:

> [!div class="nextstepaction"]
> [Criar um cluster gerenciado do Service Fabric (versão prévia)](quickstart-managed-cluster-template.md)


[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png