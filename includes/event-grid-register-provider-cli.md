---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ace22a6896a121f5cd8af838c7b0e427bd0287dc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171604"
---
## <a name="enable-event-grid-resource-provider"></a>Habilitar provedor de recursos da Grade de Eventos

Se você ainda não tiver usado a Grade de Eventos em sua assinatura do Azure, talvez seja necessário registrar o provedor de recursos da Grade de Eventos. Execute o seguinte comando para registrar o provedor:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

A conclusão do registro pode demorar um pouco. Para verificar o status, execute:

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

Quando `registrationState` for `Registered`, você está pronto para continuar.