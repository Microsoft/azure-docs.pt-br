---
title: incluir arquivo
description: incluir arquivo
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 74f9c5304237ec77a629bc914d95c345882b784f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95561721"
---
| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| vCPUs por [assinatura](https://azure.microsoft.com/pricing/)<sup>1</sup> |20 |10.000 |
| [Coadministradores](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) por assinatura |200 |200 |
| [Contas de armazenamento](../articles/storage/common/storage-account-create.md) por assinatura<sup>2</sup> |100 |100 |
| [Serviços de nuvem](../articles/cloud-services/cloud-services-choose-me.md) por assinatura |20 |200 |
| [Redes locais](/previous-versions/azure/reference/jj157100(v=azure.100)) por assinatura |10 |500 |
| Servidores DNS por assinatura |9 |100 |
| IPs reservados por assinatura |20 |100 |
| [Grupos de afinidade](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet) por assinatura |256 |256 |
| Comprimento do nome da assinatura (caracteres) | 64 | 64 |

<sup>1</sup>Instâncias muito pequenas contam como um vCPU para o limite de vCPU, apesar de usarem um núcleo de CPU parcial.

<sup>2</sup>O limite da conta de armazenamento inclui contas de armazenamento Standard e Premium.