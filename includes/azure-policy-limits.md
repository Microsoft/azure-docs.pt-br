---
title: incluir arquivo
description: incluir arquivo
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: f3f706789e14cb20214bf17fd91f6ec1e503848f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91605956"
---
Há uma contagem máxima para cada tipo de objeto do Azure Policy. Para as definições, uma entrada de _Escopo_ significa o [grupo de gerenciamento](../articles/governance/management-groups/overview.md) ou a assinatura.
Para as atribuições e isenções, uma entrada de _Escopo_ significa o [grupo de gerenciamento](../articles/governance/management-groups/overview.md), a assinatura, o grupo de recursos ou um recurso individual.

| Where | O que | Contagem máxima |
|---|---|---|
| Escopo | Definições de política | 500 |
| Escopo | Definições de iniciativa | 200 |
| Locatário | Definições de iniciativa | 2\.500 |
| Escopo | Atribuições de iniciativa ou política | 200 |
| Escopo | Isenções | 1000 |
| Definição de política | Parâmetros | 20 |
| Definição de iniciativa | Políticas | 1000 |
| Definição de iniciativa | Parâmetros | 100 |
| Atribuições de iniciativa ou política | Exclusões (notScopes) | 400 |
| Regra de política | Condicionais aninhadas | 512 |
| Tarefa de correção | Recursos | 500 |
