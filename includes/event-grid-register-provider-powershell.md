---
title: incluir arquivo
description: incluir arquivo
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 68a208af1a9aa9e73f2af99021d195f264fb21f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67172538"
---
## <a name="enable-event-grid-resource-provider"></a>Habilitar provedor de recursos da Grade de Eventos

Se você ainda não tiver usado a Grade de Eventos em sua assinatura do Azure, talvez seja necessário registrar o provedor de recursos da Grade de Eventos. Execute o comando a seguir:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

A conclusão do registro pode demorar um pouco. Para verificar o status, execute:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Quando `RegistrationStatus` for `Registered`, você está pronto para continuar.
