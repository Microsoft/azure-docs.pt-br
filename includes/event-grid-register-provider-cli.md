---
title: incluir arquivo
description: incluir arquivo
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd778da5fd53cb8744a9f267384fcc8e11941582
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645399"
---
## <a name="enable-the-event-grid-resource-provider"></a>Habilitar o provedor de recursos da Grade de Eventos

Se você ainda não tiver usado a Grade de Eventos em sua assinatura do Azure, talvez seja necessário registrar o provedor de recursos desse serviço. Execute o seguinte comando para registrar o provedor:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

A conclusão do registro pode demorar um pouco. Para verificar o status, execute:

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

Quando `registrationState` for `Registered`, você está pronto para continuar.
