---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 518c57bc3327511b70deef143826f2a1b9df8639
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171592"
---
Antes de atribuir uma função RBAC para uma entidade de segurança, determine o escopo de acesso que a entidade de segurança deve ter. As práticas recomendadas ditam que sempre é melhor conceder o escopo mais restrito possível.

A lista a seguir descreve os níveis em que você pode definir o escopo de acesso aos recursos de blob e fila do Azure, começando com o escopo mais restrito:

- **Um contêiner individual.** A este escopo, uma atribuição de função se aplica a todos os blobs no contêiner, bem como propriedades do contêiner e metadados.
- **Uma fila individual.** A este escopo, uma atribuição de função se aplica às mensagens na fila, bem como propriedades da fila e metadados.
- **A conta de armazenamento.** A este escopo, uma atribuição de função se aplica a todos os contêineres e seus blobs, ou para todas as filas e suas mensagens.
- **O grupo de recursos.** A este escopo, uma atribuição de função se aplica a todos os contêineres ou filas em todas as contas de armazenamento no grupo de recursos.
- **A assinatura.** A este escopo, uma atribuição de função se aplica a todos os contêineres ou filas em todas as contas de armazenamento em todos os grupos de recursos na assinatura.

> [!IMPORTANT]
> Se sua assinatura inclui um namespace do Azure DataBricks, funções atribuídas no escopo da assinatura serão impedidas de conceder acesso aos dados de blob e fila.
