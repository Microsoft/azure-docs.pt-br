---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "75893968"
---
Ao criar um workspace do Azure Machine Learning ou um recurso usado pelo workspace, você pode receber um erro semelhante às seguintes mensagens:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

A maioria dos provedores de recursos é automaticamente registrada, mas não todos. Se você receber essa mensagem, será necessário registrar o provedor mencionado.

Para obter informações sobre o registro de provedores de recursos, confira [Resolver erros de registro de provedor de recursos](../articles/azure-resource-manager/templates/error-register-resource-provider.md).