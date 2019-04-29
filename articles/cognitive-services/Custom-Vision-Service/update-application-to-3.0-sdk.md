---
title: Como migrar seu projeto para o 3.0 API
titlesuffix: Azure Cognitive Services
description: Saiba como migrar projetos de visão personalizada da versão anterior da API para o 3.0 API.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: 9dd473aadd7123cafc27209f5c34322fdbcffb71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60816468"
---
# <a name="migrate-to-the-30-api"></a>Migrar para o 3.0 API

Visão personalizada agora atingiu a disponibilidade geral e passou por uma atualização de API.
Esta atualização inclui alguns novos recursos e, o que é importante, algumas alterações significativas:

* A API de previsão agora é dividida em duas com base no tipo de projeto.
* A opção de exportação da pesquisa Visual computacional AI Developer Kit (VAIDK) requer a criação de um projeto de uma maneira específica.
* Iterações padrão foram removidas em favor de publicar / cancelar a publicação de uma iteração nomeada.

Este guia mostrará como atualizar seus projetos para trabalhar com a nova versão de API. Consulte a [notas de versão](release-notes.md) para obter uma lista completa das alterações.

## <a name="use-the-updated-prediction-api"></a>Use a API de previsão atualizada

As APIs de 2. x usado a mesma chamada de previsão para projetos de detector de objeto e de classificadores de imagem. Ambos os tipos de projeto foram aceitáveis para o **PredictImage** e **PredictImageUrl** chamadas. A partir do 3.0, podemos dividir essa API para que você precisa corresponder ao tipo de projeto para a chamada:

* Use **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** e **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** para obter previsões para projetos de classificação de imagem.
* Use **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** e **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** para obter previsões para projetos de detecção de objeto.

## <a name="use-the-new-iteration-publishing-workflow"></a>A usar o novo fluxo de publicação de iteração

As APIs de 2. x usada a iteração de padrão ou uma ID de iteração especificada para escolher a iteração a ser usado para previsão. A partir do 3.0, adotamos um fluxo de publicação na qual você primeiro publique uma iteração em um nome especificado da API de treinamento. Você, em seguida, passe o nome para os métodos de previsão para especificar quais iteração para usar.

> [!IMPORTANT]
> O 3.0 APIs não usam o recurso de iteração padrão. Até que substituímos as APIs mais antigas, você pode continuar a usar as APIs de 2. x para alternar uma iteração, como o padrão. Essas APIs serão mantidas por um período de tempo, e você pode chamar o **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** método para marcar uma iteração como padrão.

### <a name="publish-an-iteration"></a>Publicar uma iteração

Depois que uma iteração é treinada, você pode torná-lo disponível para previsão usando o **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** método. Para publicar uma iteração, será necessário a ID de recurso de previsão, que está disponível na página de configurações do site CustomVision.

![A página de configurações de site de visão personalizada com a ID de recurso de previsão descrita.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Você também pode obter essas informações do [Portal do Azure](https://portal.azure.com) indo para o recurso de previsão de visão personalizada e selecionando **propriedades**.

Depois que a iteração é publicada, aplicativos podem usá-lo para previsão, especificando o nome em sua chamada de API de previsão. Para fazer uma iteração não está disponível para chamadas de previsão, use o **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API.

## <a name="additional-export-options"></a>Opções de exportação adicionais

Com o 3.0 destinos de exportação de APIs que estamos expondo dois adicionais: Arquitetura ARM e o Kit de desenvolvedor de AI de visão.

* Para usar o ARM, basta escolher um domínio Compact e, em seguida, escolha o DockerFile e ARM, em seguida, como as opções de exportação.
* Para a visão AI Dev Kit, o projeto deve ser criado com o __geral (CD)__ argumento de plataformas de exportação de domínio, bem como especificar VAIDK no destino.

## <a name="next-steps"></a>Próximas etapas

* [Documentação de referência de API de treinamento (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Documentação de referência de API de previsão (REST)](https://go.microsoft.com/fwlink/?linkid=865445)