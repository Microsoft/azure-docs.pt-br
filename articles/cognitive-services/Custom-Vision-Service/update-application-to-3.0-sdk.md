---
title: Como migrar seu projeto para a API 3,0
titleSuffix: Azure Cognitive Services
description: Saiba como migrar projetos Visão Personalizada da versão anterior da API para a API 3,0.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: 353fc0a2d8396def17b8e23d9a1c685c755349c5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560892"
---
# <a name="migrate-to-the-30-api"></a>Migrar para a API 3,0

Agora Visão Personalizada atingiu a disponibilidade geral e passou por uma atualização de API.
Essa atualização inclui alguns recursos novos e, mais importante, algumas alterações significativas:

* A API de previsão agora é dividida em duas com base no tipo de projeto.
* A opção de exportação VAIDK (visão do kit de desenvolvedores do AI) requer a criação de um projeto de uma maneira específica.
* As iterações padrão foram removidas em favor de uma publicação/cancelamento da publicação de uma iteração nomeada.

Este guia mostrará como atualizar seus projetos para trabalhar com a nova versão de API. Consulte as [notas de versão](release-notes.md) para obter uma lista completa das alterações.

## <a name="use-the-updated-prediction-api"></a>Usar a API de previsão atualizada

As APIs 2. x usaram a mesma chamada de previsão para os classificadores de imagem e para os projetos de detector de objetos. Ambos os tipos de projeto eram aceitáveis para as chamadas **PredictImage** e **PredictImageUrl** . A partir do 3,0, dividimos essa API para que você precise corresponder ao tipo de projeto para a chamada:

* Use **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** e **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** para obter previsões para projetos de classificação de imagens.
* Use **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** e **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** para obter previsões para projetos de detecção de objetos.

## <a name="use-the-new-iteration-publishing-workflow"></a>Usar o novo fluxo de trabalho de publicação de iteração

As APIs 2. x usaram a iteração padrão ou uma ID de iteração especificada para escolher a iteração a ser usada para previsão. A partir do 3,0, adotamos um fluxo de publicação no qual você publica primeiro uma iteração em um nome especificado da API de treinamento. Em seguida, você passa o nome para os métodos de previsão para especificar qual iteração usar.

> [!IMPORTANT]
> As APIs 3,0 não usam o recurso de iteração padrão. Até que tenhamos substituído as APIs mais antigas, você pode continuar a usar as APIs 2. x para alternar uma iteração como padrão. Essas APIs serão mantidas por um período de tempo e você poderá chamar o método **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** para marcar uma iteração como padrão.

### <a name="publish-an-iteration"></a>Publicar uma iteração

Depois que uma iteração é treinada, você pode torná-la disponível para previsão usando o método **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** . Para publicar uma iteração, você precisará da ID de recurso de previsão, que está disponível na página de configurações do site do CustomVision.

![A página de configurações do site Visão Personalizada com a ID de recurso de previsão descrita.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Você também pode obter essas informações no [portal do Azure](https://portal.azure.com) acessando o visão personalizada recurso de previsão e selecionando **Propriedades**.

Depois que a iteração for publicada, os aplicativos poderão usá-lo para previsão especificando o nome em sua chamada à API de previsão. Para tornar uma iteração indisponível para chamadas de previsão, use a API **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** .

## <a name="additional-export-options"></a>Opções de exportação adicionais

Com as APIs 3,0, estamos expondo dois destinos de exportação adicionais: Kit de desenvolvedor de ia e visão da arquitetura do ARM.

* Para usar o ARM, você só precisa escolher um domínio compacto e, em seguida, escolher DockerFile e, em seguida, ARM como as opções de exportação.
* Para o kit de desenvolvimento de ia de visão, o projeto deve ser criado com o domínio __geral (compacto)__ , bem como especificando VAIDK no argumento de plataformas de exportação de destino.

## <a name="next-steps"></a>Próximas etapas

* [Documentação de referência da API de treinamento (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Documentação de referência da API de previsão (REST)](https://go.microsoft.com/fwlink/?linkid=865445)