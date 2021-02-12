---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 874d76bebdfd3bd0daba1f83cb1f06c093f192ec
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021225"
---
## <a name="get-the-training-and-prediction-keys"></a>Obter as chaves de treinamento e previsão

O projeto precisa de um conjunto válido de chaves de assinatura para interagir com o serviço. Você pode encontrar os itens no [site da Visão Personalizada](https://customvision.ai). Entre com a conta associada à conta do Azure usada para criar seus recursos de Visão Personalizada. Na página inicial (página com a opção de adicionar um novo projeto), selecione o __ícone de engrenagem__ no canto superior direito. Encontre seus recursos de treinamento e previsão na lista e expanda-os. Aqui você poderá encontrar a chave de treinamento, a chave de previsão e os valores da ID do recurso de previsão. Salve esses valores em um local temporário.

> [!NOTE]
> Se você estiver usando uma chave multifuncional de Serviços Cognitivos para acessar a Visão Personalizada, verá apenas uma chave na tela de configurações. Nesse caso, você usará a mesma chave para operações de treinamento e previsão.

![Imagem da interface do usuário de chaves](../media/csharp-tutorial/training-prediction-keys.png)

Ou, você poderá obter essas chaves e a ID do [portal do Azure](https://www.portal.azure.com) exibindo seus recursos de Previsão e Treinamento de Visão Personalizada e navegando até a guia __Chaves__. Lá você encontrará a chave de treinamento e a chave de previsão. Navegue até a guia __Propriedades__ do seu recurso de Previsão para obter a ID do recurso de previsão.

