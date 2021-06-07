---
title: Conceitos-armazenamento
description: Saiba mais sobre os principais recursos de armazenamento nas nuvens privadas da solução Azure VMware.
ms.topic: conceptual
ms.date: 03/13/2021
ms.openlocfilehash: 83a4a52f8414e656b5bc688796db6e93a53d4d76
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801614"
---
#  <a name="azure-vmware-solution-storage-concepts"></a>Conceitos de armazenamento da solução VMware do Azure

As nuvens privadas da solução Azure VMware fornecem armazenamento nativo em todo o cluster com a vSAN do VMware. Todo o armazenamento local de cada host em um cluster é usado em um armazenamento de dados vSAN, e a criptografia em repouso está disponível e habilitada por padrão. Você pode usar os recursos de armazenamento do Azure para estender os recursos de armazenamento de suas nuvens privadas.

## <a name="vsan-clusters"></a>clusters vSAN

O armazenamento local em cada host de cluster é usado como parte de um repositório de armazenamento vSAN. Todos os diskgroups usam uma camada de cache NVMe de 1,6 TB com a capacidade bruta, por host, baseada em SSD de 15,4 TB. O tamanho da camada de capacidade bruta de um cluster é a capacidade por host vezes o número de hosts. Por exemplo, um cluster de quatro hosts fornecerá capacidade bruta de 61,6 TB na camada de capacidade vSAN.

O armazenamento local em hosts de cluster é usado no repositório de armazenamento vSAN em todo o cluster. Todos os repositórios de armazenamento são criados como parte de uma implantação de nuvem privada e estão disponíveis para uso imediato. O usuário cloudadmin e todos os usuários no grupo CloudAdmin podem gerenciar repositórios de armazenamento com estes privilégios de vSAN:

- Datastore.AllocateSpace
- Datastore.Browse
- Datastore.Config
- Datastore. Excluirfile
- Repositório de armazenamento. filemanagement
- Datastore. UpdateVirtualMachineMetadata

## <a name="data-at-rest-encryption"></a>Criptografia de dados em repouso

Por padrão, os armazenamentos de dados vSAN usam a criptografia em repouso. A solução de criptografia é baseada em KMS e dá suporte a operações de vCenter para gerenciamento de chaves. As chaves são armazenadas criptografadas, encapsuladas por uma chave mestra de Azure Key Vault. Quando um host é removido de um cluster, os dados no SSDs são invalidados imediatamente.

## <a name="scaling"></a>Dimensionamento

A capacidade de armazenamento de cluster nativo é dimensionada adicionando hosts a um cluster. Para clusters que usam hosts AVS36, a capacidade bruta em todo o cluster é aumentada em 15,4 TB com cada host adicionado. Os hosts levam cerca de 10 minutos para serem adicionados a um cluster.  Para obter instruções sobre o dimensionamento de clusters, consulte o [tutorial dimensionar nuvem privada][tutorial-scale-private-cloud].

## <a name="azure-storage-integration"></a>Integração do armazenamento do Azure

Você pode usar os serviços de armazenamento do Azure em cargas de trabalho em execução na sua nuvem privada. Os serviços de armazenamento do Azure incluem contas de armazenamento, armazenamento de tabelas e armazenamento de BLOBs. A conexão de cargas de trabalho para os serviços de armazenamento do Azure não atravessa a Internet. Essa conectividade fornece mais segurança e permite que você use serviços de armazenamento do Azure baseados em SLA em suas cargas de trabalho de nuvem privada.

## <a name="next-steps"></a>Próximas etapas

Agora que você abordou os conceitos de armazenamento da solução Azure VMware, talvez queira saber mais sobre:

- [Conceitos de identidade de nuvem privada](concepts-identity.md).
- [controle de acesso baseado em função do vSphere para a solução do Azure VMware](concepts-identity.md).
- [Como habilitar o recurso de solução VMware do Azure](enable-azure-vmware-solution.md).
- [Azure NetApp Files com a solução VMware do Azure](netapp-files-with-azure-vmware-solution.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
