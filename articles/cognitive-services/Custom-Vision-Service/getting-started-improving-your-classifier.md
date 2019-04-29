---
title: Melhorar o classificador - Serviço de Visão Personalizada
titlesuffix: Azure Cognitive Services
description: Saiba como melhorar a qualidade do classificador.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 35f83832b0ceb7507b39095e9cc974d82a480c69
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60606940"
---
# <a name="how-to-improve-your-classifier"></a>Como melhorar o seu classificador

Neste guia, você aprenderá a melhorar a qualidade do seu classificador do Serviço de Visão Personalizada. A qualidade do classificador depende da quantidade, qualidade e variedade dos dados rotulados que você fornecer e de qual é o balanceamento do conjunto de dados geral. Um bom classificador tem um conjunto de dados de treinamento balanceado que é representativo do que será enviado ao classificador. O processo de criação desse classificador é iterativo; é comum realizar algumas etapas de treinamento para alcançar os resultados esperados.

Este é um padrão geral para ajudá-lo a criar um classificador mais preciso:

1. Treinamento de primeira rodada
1. Adicionar mais imagens e balancear dados; treinar novamente
1. Adicionar imagens com variação de tela de fundo, iluminação, tamanho do objeto, ângulo da câmera e estilo; treinar novamente
1. Usar novas imagens para testar a previsão
1. Modificar os dados de treinamento existente de acordo com os resultados da previsão

## <a name="prevent-overfitting"></a>Evitar o superajuste

Às vezes, um classificador aprenderá fazer previsões com base nas características arbitrárias que suas imagens têm em comum. Por exemplo, se estiver criando um classificador para maçãs em comparação com frutas cítricas e tiver usado imagens de maçãs em mãos e de frutas cítricas em pratos brancos, o classificador poderá dar uma importância indevida a mãos em comparação com pratos, em vez de maçãs em comparação com cítricos.

![Imagem de classificação inesperada](./media/getting-started-improving-your-classifier/unexpected.png)

Para corrigir esse problema, use as diretrizes a seguir sobre treinamento com imagens mais variadas: forneça imagens com diferentes ângulos, telas de fundo, tamanho do objeto, grupos e outras variantes.

## <a name="data-quantity"></a>Quantidade de dados

O número de imagens de treinamento é o fator mais importante. É recomendável usar pelo menos 50 imagens por rótulo como ponto de partida. Com menos imagens, há maior risco de sobreajuste e, enquanto os números de desempenho possam sugerir boa qualidade, seu modelo pode apresentar dificuldades com os dados do mundo real. 

## <a name="data-balance"></a>Balanceamento de dados

Também é importante considerar as quantidades relativas dos dados de treinamento. Por exemplo, usar 500 imagens para um rótulo e 50 imagens para outro cria um conjunto de dados de treinamento em desequilíbrio. Isso fará o modelo ser mais preciso na previsão de um rótulo do que o outro. É provável que você veja melhores resultados se mantiver uma proporção de pelo menos 1: 2 entre o rótulo com o menor número de imagens e o rótulo com o maior número de imagens. Por exemplo, se o rótulo com maior número de imagens tiver 500 imagens, o rótulo com o mínimo de imagens deverá ter pelo menos 250 imagens para treinamento.

## <a name="data-variety"></a>Variedade de dados

Use imagens representativas do que será enviado ao classificador durante o uso normal. Caso contrário, seu classificador poderá aprender a fazer previsões com base nas características arbitrárias que suas imagens têm em comum. Por exemplo, se estiver criando um classificador para maçãs em comparação com frutas cítricas e tiver usado imagens de maçãs em mãos e de frutas cítricas em pratos brancos, o classificador poderá dar uma importância indevida a mãos em comparação com pratos, em vez de maçãs em comparação com cítricos.

![Imagem de classificação inesperada](./media/getting-started-improving-your-classifier/unexpected.png)

Para corrigir esse problema, inclua uma variedade de imagens para garantir que seu classificador possa generalizar bem. Abaixo, estão algumas maneiras de tornar o treinamento mais diversificado:

* __Em Segundo Plano:__ Forneça imagens do seu objeto na frente de diferentes telas de fundo. Fotos em contextos naturais são melhores que fotos na frente de telas de fundo neutras, pois fornecem mais informações ao classificador.

    ![Imagem de exemplos de tela de fundo](./media/getting-started-improving-your-classifier/background.png)

* __Iluminação:__ Forneça imagens com iluminação variada (ou seja, tiradas com flash, alta exposição e assim por diante), principalmente se as imagens usadas para previsão tiverem uma iluminação diferente. Também é útil usar imagens com saturação, matiz e brilho variados.

    ![Imagem de exemplos de iluminação](./media/getting-started-improving-your-classifier/lighting.png)

* __Tamanho do objeto:__ fornecer imagens nas quais os objetos variam em tamanho e número (por exemplo, uma foto de muitas bananas e um close-up de uma única banana). O dimensionamento diferente ajuda o classificador a generalizar melhor.

    ![Imagem de exemplos de tamanho](./media/getting-started-improving-your-classifier/size.png)

* __Ângulo da câmera:__ forneça imagens tiradas com diferentes ângulos de câmera. Ou, se todas as fotos precisarem ser tiradas com câmeras fixas (como câmeras de vigilância), não se esqueça de atribuir um rótulo diferente a cada objeto de ocorrência regular para evitar o sobreajuste&mdash;a interpretação de objetos não relacionados (como postes de luz) como o recurso principal.

    ![Imagem de amostras de ângulo](./media/getting-started-improving-your-classifier/angle.png)

* __Estilo:__ forneça imagens de estilos diferentes da mesma classe (por exemplo, diferentes variedades da mesma fruta). No entanto, se você tem objetos de estilos drasticamente diferentes (como Mickey Mouse em comparação com um rato da vida real), recomendamos rotulá-los como classes separadas para representar melhor seus diferentes recursos.

    ![Imagem de exemplos de estilo](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images"></a>Imagens negativas

Em algum ponto do seu projeto, você precisa adicionar _exemplos negativos_ para ajudar a tornar o seu classificador mais preciso. Exemplos negativos são aqueles que não correspondem a outras marcas. Quando você carregar essas imagens, aplique o rótulo especial **Negativo** a elas.

> [!NOTE]
> O Serviço de Visão Personalizada dá suporte a alguns tratamentos de imagem negativo automática. Por exemplo, se você está compilando um classificador de uva em comparação com banana e envia uma imagem de um sapato para previsão, o classificador deve classificar essa imagem em cerca de 0 % tanto para uva como para banana.
> 
> Por outro lado, nos casos em que as imagens negativas são apenas uma variação das imagens utilizadas no treinamento, é provável que o modelo classifique as imagens negativas como uma classe rotulada devido às grandes semelhanças. Por exemplo, se você tiver um classificador de laranja em comparação com toranja, e envia em uma imagem de clementina, o classificador poderá classificar a clementina como laranja porque a clementina possui muitas características semelhantes às das laranjas. Se as imagens negativas forem dessa natureza, recomendamos criar uma ou mais marcas adicionais (como **Outro**) e rotular as imagens negativas com essa marca durante o treinamento para permitir que o modelo diferencie melhor essas classes.

## <a name="use-prediction-images-for-further-training"></a>Usar imagens de previsão para obter treinamento adicional

Quando você usa ou testa o classificador de imagens enviando imagens para o ponto de extremidade de previsão, o Serviço de Visão Personalizada armazena essas imagens. É possível usá-las para melhorar o modelo.

1. Para exibir as imagens enviadas ao classificador, abra a [página da Web Visão Personalizada](https://customvision.ai), acesse o projeto e selecione a guia __Previsões__. A exibição padrão mostra imagens da iteração atual. É possível usar o menu suspenso __Iteração__ para exibir imagens enviadas durante iterações anteriores.

    ![captura de tela da guia de previsões com imagens em exibição](./media/getting-started-improving-your-classifier/predictions.png)

2. Passe o mouse sobre uma imagem para ver as marcas que foram previstas pelo classificador. As imagens são classificadas para que aquelas que puderem trazer o maior número de melhorias ao classificador sejam listadas primeiro. Para usar um método de classificação diferente, crie uma seleção na seção __Classificar__. 

    Para adicionar uma imagem aos dados de treinamento existentes, selecione a imagem, defina as tags corretas e clique em __Salvar e fechar__. A imagem será removida de __Previsões__ e adicionada ao conjunto de imagens de treinamento. Você pode exibi-la selecionando a guia __Imagens de Treinamento__.

    ![Imagem da página de marcação](./media/getting-started-improving-your-classifier/tag.png)

3. Use o botão __Treinar__ para treinar novamente o classificador.

## <a name="visually-inspect-predictions"></a>Inspecionar visualmente previsões

Para inspecionar as previsões de imagem, vá até a guia __Imagens de Treinamento__, selecione a iteração de treinamento anterior no menu suspenso **Iteração** e marque uma ou mais marcas na seção **Marcas**. Agora a exibição deve exibir uma caixa vermelha ao redor de cada uma das imanes para as quais o modelo não conseguiu prever corretamente a tag específica.

![Imagem do histórico de iteração](./media/getting-started-improving-your-classifier/iteration.png)

Às vezes, uma inspeção visual pode identificar padrões que você pode corrigir adicionando mais dados de treinamento ou modificando os existentes. Por exemplo, um classificador para maçãs em comparação com limões pode rotular incorretamente todas as maçãs verdes como limões. É possível corrigir esse problema adicionando e fornecendo dados de treinamento que contêm imagens marcadas de maçãs verdes.

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu várias técnicas para criar tornar seu modelo de classificação de imagem personalizada mais preciso. Em seguida, aprenda a testar imagens programaticamente enviando-as à API de Previsão.

> [!div class="nextstepaction"]
> [Use a API de previsão](use-prediction-api.md)
