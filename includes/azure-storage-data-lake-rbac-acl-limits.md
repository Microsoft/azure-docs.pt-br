---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 97b47b48a183951002d04cd58b08d5e2b9051eda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017655"
---
| Mecanismo | Escopo |Limites | Nível de permissão com suporte |
|---|---|---|---|
| RBAC do Azure | Contas de armazenamento, contêineres. <br>Atribuições de função do Azure entre recursos no nível de assinatura ou grupo de recursos. | 2000 atribuições de função do Azure em uma assinatura | Funções do Azure (internas ou personalizadas) |
| LCA| Diretório, arquivo |32 entradas de ACL (efetivamente 28 entradas de ACL) por arquivo e por diretório. As ACLs de acesso e padrão têm seu próprio limite de entrada de ACL 32. |Permissão de ACL|
