---
title: incluir arquivo
description: incluir arquivo
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/15/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: f02b7f0f80cfb875cc6207b542db90607b379b67
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800114"
---
| Recurso | Limite |
| --- | --- |
| Assinaturas [associadas a um locatário do Azure Active Directory](../articles/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) | Ilimitado |
| [Coadministradores](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) por assinatura |Ilimitado |
| [Grupos de recursos](../articles/azure-resource-manager/management/overview.md) por assinatura |980 |
| Tamanho da solicitação de API do Azure Resource Manager |4.194.304 bytes |
| Marcas por assinatura<sup>1</sup> |50 |
| Cálculos de marca exclusivos por assinatura<sup>1</sup> | 80.000 |
| [Implantações de nível de assinatura](../articles/azure-resource-manager/templates/deploy-to-subscription.md) por local | 800<sup>2</sup> |

<sup>1</sup>Você pode aplicar até 50 marcas diretamente a uma assinatura. No entanto, a assinatura pode conter um número ilimitado de marcas aplicadas a recursos e grupos de recursos na assinatura. O número de marcas por recurso ou grupo de recursos é limitado a 50. O Resource Manager retorna uma [lista de nomes e valores de marca exclusivos](/rest/api/resources/tags) na assinatura somente quando o número de marcas é no máximo 80 mil. Você ainda poderá encontrar um recurso por marca quando o número exceder 80 mil.

<sup>2</sup>Caso você atinja o limite de 800 implantações, exclua do histórico as implantações que não são mais necessárias. Para excluir implantações de nível de assinatura, use [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) ou [az deployment sub delete](/cli/azure/deployment/sub#az_deployment_sub_delete).
