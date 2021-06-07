---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 3c9679f3d66d58c7a6c847b54c84438c979ecd39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936848"
---
O [Ponto de Extremidade Privado do Azure](../articles/private-link/private-endpoint-overview.md) é um adaptador de rede que conecta você de maneira privada e segura a um serviço que conta com o Link Privado do Azure.  O Ponto de Extremidade Privado usa um endereço IP privado da rede virtual, colocando efetivamente o serviço na sua rede virtual.

Você pode usar o ponto de extremidade privado para suas funções hospedadas nos planos de [serviço de aplicativo](../articles/azure-functions/dedicated-plan.md) e [Premium](../articles/azure-functions/functions-premium-plan.md) .

Ao criar uma conexão de ponto de extremidade privada de entrada para funções, você também precisará de um registro DNS para resolver o endereço privado.  Por padrão, um registro DNS privado será criado para você ao criar um ponto de extremidade privado usando o portal do Azure.

Para saber mais, consulte [usando pontos de extremidade privados para aplicativos Web](../articles/app-service/networking/private-endpoint.md).