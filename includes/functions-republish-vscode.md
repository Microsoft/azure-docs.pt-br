---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/20/2020
ms.author: glenga
ms.openlocfilehash: 574756aa9d9bac4aa42febf6a4fca4c62014db3f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010428"
---
1. No Visual Studio Code, pressione F1 para abrir a paleta de comandos. Na paleta de comandos, pesquise e selecione **Azure Functions: implantar para aplicativo de funções**.

1. Se você não estiver conectado, será solicitado a **Entrar no Azure**. Após você entrar pelo navegador, retorne ao Visual Studio Code. Se você tiver várias assinaturas, **selecione uma assinatura** que contenha o aplicativo de funções.

1. Selecione seu aplicativo de funções existente no Azure. Quando você for avisado sobre a substituição de todos os arquivos no aplicativo de funções, selecione **Implantar** para reconhecer o aviso e continuar.

O projeto é recriado, reempacotado e carregado no Azure. O projeto existente é substituído pelo novo pacote e o aplicativo de funções é reiniciado.