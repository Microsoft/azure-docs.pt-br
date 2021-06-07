---
title: Armazenamento de imagens de contêiner
description: Detalhes sobre como as imagens de contêiner e outros artefatos são armazenados no registro de contêiner do Azure, incluindo segurança, redundância e capacidade.
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: references_regions
ms.openlocfilehash: a9c8ec877ddb17603e82b763223278a2e5e36714
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047738"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Armazenamento de imagens de contêiner no Registro de Contêiner do Azure

Cada registro de contêiner do Azure [básico, Standard e Premium](container-registry-skus.md) se beneficia dos recursos avançados de armazenamento do Azure, incluindo criptografia em repouso. As seções a seguir descrevem os recursos e limites do armazenamento de imagem no ACR (registro de contêiner do Azure).

## <a name="encryption-at-rest"></a>Criptografia em repouso

Todas as imagens de contêiner e outros artefatos em seu registro são criptografados em repouso. O Azure criptografa automaticamente uma imagem antes de armazená-la e descriptografa-a dinamicamente quando você ou seus aplicativos e serviços obtêm a imagem. Opcionalmente, aplique uma camada de criptografia extra a uma [chave gerenciada pelo cliente](container-registry-customer-managed-keys.md).

## <a name="regional-storage"></a>Armazenamento regional

O registro de contêiner do Azure armazena dados na região em que o registro é criado, para ajudar os clientes a atender aos requisitos de conformidade e residência de dados. Em todas as regiões, exceto sul do Brasil e sudeste asiático, o Azure também pode armazenar dados de registro em uma região emparelhada na mesma geografia. Nas regiões do Sul do Brasil e do Sudeste Asiático, os dados do registro são sempre confinados para a região, para acomodar os requisitos de residência de dados para essas regiões.

Se ocorrer uma interrupção regional, os dados do registro poderão ficar indisponíveis e não serão recuperados automaticamente. Os clientes que desejam ter seus dados de registro armazenados em várias regiões para melhorar o desempenho em diferentes geografias ou que desejam ter resiliência no caso de uma interrupção regional devem habilitar [a replicação geográfica](container-registry-geo-replication.md).

## <a name="geo-replication"></a>Replicação geográfica

Para cenários que exigem garantia de alta disponibilidade, considere o uso do recurso de [replicação geográfica](container-registry-geo-replication.md) de registros Premium. A replicação geográfica ajuda a proteger contra a perda de acesso ao registro no caso de uma falha regional. A replicação geográfica também oferece outros benefícios, como armazenamento de imagens de proximidade de rede para efetuar pushes e pulls mais rápido em cenários de implantação ou desenvolvimento distribuído.

## <a name="zone-redundancy"></a>Redundância de zona

Para ajudar a criar um registro de contêiner do Azure resiliente e de alta disponibilidade, opcionalmente, habilite a [redundância de zona](zone-redundancy.md) em selecionar regiões do Azure. Um recurso da camada de serviço Premium, a redundância de zona usa [zonas de disponibilidade](../availability-zones/az-overview.md) do Azure para replicar o registro para um mínimo de três zonas separadas em cada região habilitada. Combine a replicação geográfica e a redundância de zona para aprimorar a confiabilidade e o desempenho de um registro. 

## <a name="scalable-storage"></a>Armazenamento escalonável

O registro de contêiner do Azure permite que você crie quantos repositórios, imagens, camadas ou marcas forem necessárias, até o limite de [armazenamento do registro](container-registry-skus.md#service-tier-features-and-limits). 

Números altos de repositórios e marcas podem afetar o desempenho do registro. Exclua periodicamente repositórios, marcas e imagens não utilizados como parte da rotina de manutenção do registro e, opcionalmente, defina uma [política de retenção](container-registry-retention-policy.md) para manifestos não marcados. Os recursos de registro excluídos, como repositórios, imagens e marcas, *não podem* ser recuperados após a exclusão. Para obter mais informações sobre a exclusão de recursos de registro, consulte [Excluir imagens de contêiner no Registro de Contêiner do Azure](container-registry-delete.md).

## <a name="storage-cost"></a>Custo de armazenamento

Para obter detalhes completos sobre preços, consulte [Preços de Registro de Contêiner do Azure][pricing].

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre registros de contêiner Básico, Standard e Premium, consulte [Camadas de serviço do Registro de Contêiner do Azure](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
