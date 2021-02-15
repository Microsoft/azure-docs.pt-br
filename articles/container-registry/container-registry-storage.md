---
title: Armazenamento de imagens de contêiner
description: Detalhes sobre como as imagens de contêiner do Docker são armazenadas no Registro de Contêiner do Azure, incluindo segurança, redundância e capacidade.
ms.topic: article
ms.date: 06/18/2020
ms.openlocfilehash: d51014e9e0769091aba42682cce3a6a01cfa19de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85214053"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Armazenamento de imagens de contêiner no Registro de Contêiner do Azure

Cada registro de contêiner do Azure [Básico, Standard e Premium](container-registry-skus.md) beneficia-se de recursos avançados de armazenamento do Azure, como criptografia em repouso para segurança de dados de imagem e redundância geográfica para proteção de dados de imagem. As seções a seguir descrevem os recursos e os limites do armazenamento de imagens no ACR (Registro de Contêiner do Azure).

## <a name="encryption-at-rest"></a>Criptografia em repouso

Todas as imagens de contêiner no registro são criptografadas em repouso. O Azure criptografa automaticamente uma imagem antes de armazená-la e descriptografa-a dinamicamente quando você ou seus aplicativos e serviços obtêm a imagem. Opcionalmente, aplique uma camada de criptografia adicional com uma [chave gerenciada pelo cliente](container-registry-customer-managed-keys.md).

## <a name="geo-redundant-storage"></a>Armazenamento com redundância geográfica

O Azure usa um esquema de armazenamento com redundância geográfica para proteger contra perda de imagens de contêiner. O Registro de Contêiner do Azure replica automaticamente as imagens de contêiner para vários datacenters geograficamente distantes, evitando a perda no caso de uma falha de armazenamento regional.

## <a name="geo-replication"></a>Replicação geográfica

Para cenários que exigem ainda mais garantia de alta disponibilidade, utilize o recurso de [replicação geográfica](container-registry-geo-replication.md) dos registros Premium. A replicação geográfica ajuda a proteger contra a perda de acesso ao registro no caso de uma falha regional *total* e não apenas uma falha de armazenamento. A replicação geográfica também oferece outros benefícios, como armazenamento de imagens de proximidade de rede para efetuar pushes e pulls mais rápido em cenários de implantação ou desenvolvimento distribuído.

## <a name="scalable-storage"></a>Armazenamento escalonável

O registro de contêiner do Azure permite que você crie quantos repositórios, imagens, camadas ou marcas forem necessárias, até o limite de [armazenamento do registro](container-registry-skus.md#service-tier-features-and-limits). 

Números muito altos de repositórios e marcas podem afetar o desempenho do registro. Exclua periodicamente repositórios, marcas e imagens não utilizados como parte da rotina de manutenção do registro e, opcionalmente, defina uma [política de retenção](container-registry-retention-policy.md) para manifestos não marcados. Os recursos de registro excluídos, como repositórios, imagens e marcas, *não podem* ser recuperados após a exclusão. Para obter mais informações sobre a exclusão de recursos de registro, consulte [Excluir imagens de contêiner no Registro de Contêiner do Azure](container-registry-delete.md).

## <a name="storage-cost"></a>Custo de armazenamento

Para obter detalhes completos sobre preços, consulte [Preços de Registro de Contêiner do Azure][pricing].

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre registros de contêiner Básico, Standard e Premium, consulte [Camadas de serviço do Registro de Contêiner do Azure](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
