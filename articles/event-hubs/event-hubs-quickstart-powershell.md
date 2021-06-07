---
title: 'Guia de início rápido: criar um hub de eventos usando o PowerShell – Hubs de Eventos do Azure'
description: Este início rápido descreve como criar um hub de eventos usando o Azure PowerShell e, em seguida, enviar e receber eventos usando o SDK do .NET Standard.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 1c81024a9195e463eeaaf384d5cbbd1e0054c485
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88933946"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Início Rápido: criar um hub de eventos usando o Azure PowerShell

Os Hubs de Eventos do Azure são uma plataforma de streaming de Big Data e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

Neste início rápido, você cria um hub de eventos usando o Azure PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial, verifique se você tem:

- Assinatura do Azure. Se você não tiver [uma conta gratuita][], crie uma antes de começar.
- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [SDK do .NET Standard](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você estiver usando o PowerShell localmente, você deve executar a versão mais recente do PowerShell para concluir este início rápido. Se precisar instalar ou atualizar, confira [Instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é uma coleção lógica de recursos do Azure e você precisa de um grupo de recursos para criar um hub de eventos. 

O exemplo a seguir cria um grupo de recursos na região Leste dos EUA. Substitua `myResourceGroup` pelo nome do grupo de recursos e local que você deseja usar:

```azurepowershell-interactive
New-AzResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Criar um namespace dos hubs de eventos

Após o seu grupo de recursos ser criado, crie um namespace de Hubs de Eventos dentro deste mesmo grupo de recursos. Um namespace de Hubs de Eventos fornece um nome de domínio exclusivo totalmente qualificado no qual você pode criar seu hub de eventos. Substitua `namespace_name` por um nome exclusivo para o seu namespace:

```azurepowershell-interactive
New-AzEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>Criar um Hub de Evento

Agora que você possui um namespace de Hubs de Eventos, crie um hub de eventos dentro desse namespace:  
O período permitido para `MessageRetentionInDays` é entre 1 e 7 dias.

```azurepowershell-interactive
New-AzEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

Parabéns! Você usou o Azure PowerShell para criar um namespace de Hubs de Eventos e um hub de eventos dentro desse namespace. 

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um namespace de Hubs de Eventos e aplicativo de exemplo usados para enviar e receber eventos do seu hub de eventos. Para obter instruções passo a passo sobre como enviar eventos (ou) receber eventos de um hub de eventos, confira os tutoriais para **Enviar e receber eventos**: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (somente enviar)](event-hubs-c-getstarted-send.md)
- [Apache Storm (somente receber)](event-hubs-storm-getstarted-receive.md)


[uma conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: /powershell/azure/install-az-ps
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
