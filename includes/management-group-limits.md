---
title: incluir arquivo
description: incluir arquivo
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: cdcf6215973755444da9e513761de7ac71e479d4
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98738399"
---
| Recurso | Limite |
| --- | --- |
| Grupos de gerenciamento por locatário do Azure AD | 10.000 |
| Assinaturas por grupo de gerenciamento | Ilimitado. |
| Níveis de hierarquia do grupo de gerenciamento | Nível raiz mais seis níveis<sup>1</sup> |
| Grupo de gerenciamento pai direto por grupo de gerenciamento | Um |
| [Implantações no nível do grupo de gerenciamento](../articles/azure-resource-manager/templates/deploy-to-management-group.md) por local | 800<sup>2</sup> |

<sup>1</sup>Os seis níveis não incluem o nível da assinatura.

<sup>2</sup>Caso você atinja o limite de 800 implantações, exclua do histórico as implantações que não são mais necessárias. Para excluir implantações no nível do grupo de gerenciamento, use [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) ou [az deployment mg delete](/cli/azure/deployment/mg#az-deployment-mg-delete).
