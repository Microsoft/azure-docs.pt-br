---
author: baanders
description: incluir arquivo para publicar e configurar o aplicativo de funções
ms.service: digital-twins
ms.topic: include
ms.date: 03/15/2021
ms.author: baanders
ms.openlocfilehash: f9740c50bbfd178bccbbc2424796475f1eee12bb
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950951"
---
Para obter instruções sobre como fazer isso, consulte a seção [**publicar o aplicativo de funções no Azure**](../articles/digital-twins/how-to-create-azure-function.md#publish-the-function-app-to-azure) do artigo *como: configurar uma função para processamento de dados* .

> [!IMPORTANT]
> Ao criar o aplicativo de funções pela primeira vez na seção [pré-requisitos](#prerequisites) , talvez você já tenha atribuído uma função de acesso para a função e configurado as configurações do aplicativo para que ele acesse a instância do gêmeos digital do Azure. Eles precisam ser feitos uma vez para todo o aplicativo de funções, portanto, verifique se eles foram concluídos em seu aplicativo antes de continuar. Você pode encontrar instruções no artigo [**Configurar o acesso de segurança para o aplicativo de funções**](../articles/digital-twins/how-to-create-azure-function.md#set-up-security-access-for-the-function-app) do *como: ingerir dados do Hub IOT.*