---
title: incluir arquivo
description: incluir arquivo
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 10/12/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 20d80fe1a09d388552b6289f8a9b23c878672f94
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974271"
---
Estes exemplos mostram como usar o Azure Policy com assinaturas que foram integradas ao Azure Lighthouse.

| **Modelo** | **Descrição** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Atribui uma política que adiciona ou remove uma marca (usando o efeito modificar) a uma assinatura delegada. Para saber mais, confira [Implantar uma política que pode ser corrigida em uma assinatura delegada](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-allow-certain-managing-tenants](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-allow-certain-managing-tenants) | Atribui uma política que restringe as delegações do Azure Lighthouse para locatários de gerenciamento específicos. |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Atribui uma política que fará a auditoria de atribuições de delegação. |
| [policy-delegate-management-groups](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) | Atribui uma política para confirmar que as assinaturas dentro de um grupo de gerenciamento foram delegadas a um locatário de gerenciamento e, caso contrário, cria a atribuição.
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Atribui uma política que habilita o diagnóstico nos recursos do Azure Key Vault em assinaturas delegadas (usando o efeito deployIfNotExists). Para saber mais, confira [Implantar uma política que pode ser corrigida em uma assinatura delegada](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Atribui várias políticas para habilitar o diagnóstico em uma assinatura delegada e conecta todas as VMs Windows e Linux ao workspace do Log Analytics criado pela política. Para saber mais, confira [Implantar uma política que pode ser corrigida em uma assinatura delegada](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Aplica uma iniciativa (várias definições de política relacionadas) a uma assinatura delegada. |

