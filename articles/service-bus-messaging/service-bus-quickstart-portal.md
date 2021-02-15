---
title: Usar o portal do Azure para criar uma fila do Barramento de Serviço
description: Neste início rápido, você aprenderá a criar um namespace do Barramento de Serviço e uma fila no namespace usando o portal do Azure.
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 79dd751c43443790aafc494d89ad45e3b6705a64
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95799221"
---
# <a name="use-azure-portal-to-create-a-service-bus-namespace-and-a-queue"></a>Usar o portal do Azure para criar um namespace e uma fila do Barramento de Serviço
Este guia de início rápido mostra como criar um namespace e uma fila do Barramento de Serviço usando o [portal do Azure][Azure portal]. Ele também mostra como obter credenciais de autorização que um aplicativo cliente pode usar para enviar/receber mensagens de/para a fila. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa tem uma assinatura do Azure. Se você não tiver uma assinatura do Azure, poderá criar uma [conta gratuita][] antes de começar.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou um namespace do Barramento de Serviço e uma fila no namespace. Para saber como enviar/receber mensagens de/para a fila, consulte um dos guias de início rápido a seguir na seção **Enviar e receber mensagens**. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
