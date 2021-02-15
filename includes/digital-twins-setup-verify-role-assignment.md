---
author: baanders
description: incluir arquivo para verificar a atribuição de função na configuração de gêmeos digital do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b4dfd154ff3fb7af48ef43b0a1dca168c5a93481
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489024"
---
Uma maneira de verificar se você configurou com êxito a atribuição de função é exibir as atribuições de função para a instância do gêmeos digital do Azure no [portal do Azure](https://portal.azure.com). Vá para a instância do gêmeos digital do Azure na portal do Azure (você pode procurá-lo na página de [instâncias do gêmeos digital do Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) ou pesquisar seu nome na barra de pesquisa do Portal).

Em seguida, exiba todas as suas funções atribuídas em *controle de acesso (iam) > atribuições de função*. O usuário deve aparecer na lista com uma função do proprietário de *dados do gêmeos digital do Azure*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Exibição das atribuições de função para uma instância do gêmeos digital do Azure no portal do Azure":::