---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/25/2019
ms.author: glenga
ms.openlocfilehash: 437b4ab62cc8c4903af88ca2f9632e89b953c798
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010431"
---
Para ativar os logs de streaming para seu aplicativo de funções no Azure:

1. Selecione F1 para abrir a paleta de comandos, pesquise e execute o comando **Azure Functions: Iniciar Logs de Streaming**.

1. Selecione seu aplicativo de funções no Azure e, em seguida, selecione **Sim** para habilitar o log do aplicativo para o aplicativo de funções.

1. Dispare suas funções no Azure. Observe que os dados de log são exibidos na janela de Saída no Visual Studio Code.

1. Quando terminar, lembre-se de executar o comando **Azure Functions: Parar Logs de Streaming** para desabilitar o registro em log do aplicativo de funções.