---
title: Armazenamento de imagens no Registro de Contêiner do Azure
description: Detalhes sobre como as imagens de contêiner do Docker são armazenadas no Registro de Contêiner do Azure, incluindo segurança, redundância e capacidade.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/21/2018
ms.author: danlep
ms.openlocfilehash: 55c84907ab41f6da9d7a0989c68a1c1f90c5e424
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60827265"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Armazenamento de imagens de contêiner no Registro de Contêiner do Azure

Cada registro de contêiner do Azure [Básico, Standard e Premium](container-registry-skus.md) beneficia-se de recursos avançados de armazenamento do Azure, como criptografia em repouso para segurança de dados de imagem e redundância geográfica para proteção de dados de imagem. As seções a seguir descrevem os recursos e os limites do armazenamento de imagens no ACR (Registro de Contêiner do Azure).

## <a name="encryption-at-rest"></a>Criptografia em repouso

Todas as imagens de contêiner no registro são criptografadas em repouso. O Azure criptografa automaticamente uma imagem antes de armazená-la e descriptografa-a dinamicamente quando você ou seus aplicativos e serviços obtêm a imagem.

## <a name="geo-redundant-storage"></a>Armazenamento com redundância geográfica

O Azure usa um esquema de armazenamento com redundância geográfica para proteger contra perda de imagens de contêiner. O Registro de Contêiner do Azure replica automaticamente as imagens de contêiner para vários datacenters geograficamente distantes, evitando a perda no caso de uma falha de armazenamento regional.

## <a name="geo-replication"></a>Replicação geográfica

Para cenários que exigem ainda mais garantia de alta disponibilidade, utilize o recurso de [replicação geográfica](container-registry-geo-replication.md) dos registros Premium. A replicação geográfica ajuda a proteger contra a perda de acesso ao registro no caso de uma falha regional *total* e não apenas uma falha de armazenamento. A replicação geográfica também oferece outros benefícios, como armazenamento de imagens de proximidade de rede para efetuar pushes e pulls mais rápido em cenários de implantação ou desenvolvimento distribuído.

## <a name="image-limits"></a>Limites de imagem

A tabela a seguir descreve os limites de armazenamento e imagem de contêiner em vigor para os registros de contêiner do Azure.

| Resource | Limite |
| -------- | :---- |
| Repositórios | Sem limite |
| Imagens | Sem limite |
| Camadas | Sem limite |
| Marcas | Sem limite|
| Armazenamento | 5 TB |

Números muito altos de repositórios e marcas podem afetar o desempenho do registro. Exclua periodicamente repositórios, marcas e imagens não utilizados como parte da rotina de manutenção de registro. Recursos de registro excluídos, como repositórios, imagens e marcas *não poderão* ser recuperados após a exclusão. Para obter mais informações sobre a exclusão de recursos de registro, consulte [Excluir imagens de contêiner no Registro de Contêiner do Azure](container-registry-delete.md).

## <a name="storage-cost"></a>Custo de armazenamento

Para obter detalhes completos sobre preços, consulte [preços de Registro de Contêiner do Azure][pricing].

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as diferentes SKUs de Registro de Contêiner do Azure (Básico, Standard e Premium), consulte [SKUs do Registro de Contêiner do Azure](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
