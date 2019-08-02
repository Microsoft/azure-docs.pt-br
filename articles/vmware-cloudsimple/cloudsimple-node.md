---
title: Visão geral de nós para a solução do VMware CloudSimple - Azure
description: Saiba mais sobre conceitos e CloudSimple nós.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fb82e31d58d9955efc3b147eccf2b82b8768aeee
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165797"
---
# <a name="cloudsimple-nodes-overview"></a>Visão geral de nós CloudSimple

Um nó é:

* Um dedicado bare-metal de computação host em que o hipervisor VMware ESXi está instalado  
* Uma unidade de computação que você pode provisionar ou reserva para criar nuvens privadas  
* Disponível para provisionar ou reservar em uma região em que o serviço de CloudSimple está disponível

Nós são blocos de construção de uma nuvem privada.  Para criar uma nuvem privada, é necessário um mínimo de três nós da mesma SKU.  Para expandir uma nuvem privada, adicione nós adicionais.  Você pode adicionar nós a um cluster existente. Ou, você pode criar um novo cluster de provisionamento de nós no portal do Azure e associando-os com o serviço CloudSimple.  Todos os nós provisionado são visíveis no serviço CloudSimple.  Criar uma nuvem privada de nós provisionados no Portal de CloudSimple.

## <a name="provisioned-nodes"></a>Nós provisionado

Nós provisionados fornecem a capacidade de pago conforme o uso. Provisionamento de nós ajuda você a dimensionar rapidamente seu cluster do VMware sob demanda. Você pode adicionar nós conforme necessário, ou excluir um nó provisionado para escalar verticalmente o cluster do VMware. nós provisionados são cobrados mensalmente e cobradas na assinatura em que eles são provisionados:

* Se você pagar sua assinatura do Azure com cartão de crédito, cartão será cobrado imediatamente.
* Se você é cobrado por fatura, as cobranças aparecem na sua próxima fatura.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>Solução VMware por nós CloudSimple SKU

Os nós de tipos a seguir estão disponíveis para provisionamento ou reserva.

| SKU | CS28 - nó | CS36 - nó |
|-----|-------------|-------------|
| CPU | 2x2.2 GHz, 28 núcleos (56 HT) | 2x2.3 GHz, 36 núcleos (72 HT) |
| RAM | 256 GB | 512 GB |
| Cache Disk |  NVMe 1,6 TB | NVMe 3.2 TB |
| Disco de capacidade | 5.625 TB brutos | 11,25 TB brutos |
| Tipo de armazenamento | Todos os Flash | Todos os Flash |

## <a name="limits"></a>limites

Os limites de nó a seguir se aplicam a nuvens privadas.

| Resource | Limite |
|----------|-------|
| Número mínimo de nós para criar uma nuvem privada | 3 |
| Número máximo de nós em um Cluster em uma nuvem privada | 16 |
| Número máximo de nós em uma nuvem privada | 64 |
| Número mínimo de nós em um novo Cluster | 3 |

## <a name="next-steps"></a>Próximas etapas

* Saiba como [provisionar nós](create-nodes.md)
* Saiba mais sobre [nuvem privada](cloudsimple-private-cloud.md)