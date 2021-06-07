---
title: Visão geral da solução Azure VMware por CloudSimple nós
description: Saiba mais sobre os conceitos de CloudSimple, incluindo nós, nós provisionados, uma nuvem privada e solução VMware por SKUs de nós CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7de4b5faa997d909089daedab7e48e5d5a6de2e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88140829"
---
# <a name="cloudsimple-nodes-overview"></a>Visão geral dos nós do CloudSimple

Os nós são os blocos de construção de uma nuvem privada. Um nó é:

* Um host de computação bare-metal dedicado em que um hipervisor VMware ESXi está instalado  
* Uma unidade de computação que você pode provisionar ou reservar para criar nuvens privadas
* Disponível para provisionar ou reservar em uma região onde o serviço CloudSimple está disponível

Você cria uma nuvem privada a partir dos nós provisionados. Para criar uma nuvem privada, você precisa de um mínimo de três nós do mesmo SKU. Para expandir uma nuvem privada, adicione outros nós.  Você pode adicionar nós a um cluster existente ou criar um novo cluster Provisionando nós no portal do Azure e associando-os ao serviço CloudSimple.  Todos os nós provisionados são visíveis no serviço CloudSimple.  

## <a name="provisioned-nodes"></a>Nós provisionados

Os nós provisionados fornecem capacidade de pagamento conforme o uso. O provisionamento de nós ajuda você a dimensionar rapidamente seu cluster VMware sob demanda. Você pode adicionar nós conforme necessário ou excluir um nó provisionado para reduzir verticalmente o cluster do VMware. Os nós provisionados são cobrados mensalmente e cobrados na assinatura em que são provisionados.

* Se você pagar pela sua assinatura do Azure por cartão de crédito, o cartão será cobrado imediatamente.
* Se você for cobrado por fatura, os encargos serão exibidos na próxima fatura.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>Solução VMware por SKU de nós CloudSimple

Os seguintes tipos de nós estão disponíveis para provisionamento ou reserva.

| SKU           | CS28-nó                 | CS36-nó                 | CS36m-nó                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Região        | Leste dos EUA, Oeste dos EUA            | Leste dos EUA, Oeste dos EUA            | Europa Ocidental                 |
| CPU           | 2x 2,2 GHz, 28 núcleos (56 HT) | 2x 2,3 GHz, 36 núcleos (72 HT) | 2x 2,3 GHz, 36 núcleos (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Disco de cache    | 1,6-TB NVMe                 | 3,2-TB NVMe                 | 3,2-TB NVMe                 |
| Disco de capacidade | 5,625 TB brutos                | 11,25 TB brutos                | 15,36 TB brutos                |
| Tipo de armazenamento  | Todos os flash                   | Todos os flash                   | Todos os flash                   |

## <a name="limits"></a>Limites

Os limites de nó a seguir se aplicam a nuvens privadas.

| Recurso | Limite |
|----------|-------|
| Número mínimo de nós para criar uma nuvem privada | 3 |
| Número máximo de nós em um cluster em uma nuvem privada | 16 |
| Número máximo de nós em uma nuvem privada | 64 |
| Número mínimo de nós em um novo cluster | 3 |

## <a name="next-steps"></a>Próximas etapas

* Saiba como [provisionar nós](create-nodes.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)
