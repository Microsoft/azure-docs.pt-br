---
title: Extração de frases-chave usando a API REST de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Saiba como extrair frases-chave usando a API REST de Análise de Texto dos Serviços Cognitivos do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/29/2019
ms.author: raymondl
ms.openlocfilehash: 1d94cff3eb3299692fc4172f5bb5211532ef1002
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697831"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Exemplo: Como extrair frases-chave na Análise de Texto

A [API de Extração de Frases-chave](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) avalia o texto desestruturado e para cada documento JSON, retorna uma lista de frases-chave.

Esse recurso é útil se você precisar identificar rapidamente os principais pontos de estratégias em uma coleção de documentos. Por exemplo, para o texto de entrada dado "A comida estava deliciosa e a equipe era maravilhosa", o serviço retorna os principais pontos de discussão: "comida" e "equipe maravilhosa".

Para obter mais informações, consulte [Linguagens com suporte](../text-analytics-supported-languages.md).

> [!TIP]
> A Análise de Texto também fornece uma imagem de contêiner do Docker baseado em Linux para extração de frases-chave, para que você possa [instalar e executar o contêiner de Análise de Texto](text-analytics-how-to-install-containers.md) perto de seus dados.

## <a name="preparation"></a>Preparação

A extração de frase-chave funciona melhor quando você concede trechos maiores de texto com os quais ela pode trabalhar. Isso é o oposto da análise de sentimento, que executa melhor em blocos de texto menores. Para obter os melhores resultados de ambas as operações, considere a reestruturação de entradas adequadamente.

Você deve ter documentos JSON neste formato: ID, texto, idioma

O tamanho do documento precisa ter 5.120 caracteres ou menos por documento, e você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo da solicitação. O exemplo a seguir é uma ilustração do conteúdo que você pode enviar para extração de frase-chave.

```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Etapa 1: Estruturar a solicitação

Para saber mais sobre a definição de solicitação, confira [Como chamar a API de Análise de Texto](text-analytics-how-to-call-api.md). Os seguintes pontos são redeclarados para conveniência:

+ Criar uma solicitação de **Postagem**. Examine a documentação da API para esta solicitação: [API de Frases-Chave](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6).

+ Defina o ponto de extremidade HTTP para extração de frases-chave usando um recurso de Análise de Texto no Azure ou um [contêiner de Análise de Texto](text-analytics-how-to-install-containers.md) instanciado. Ele precisa incluir o recurso `/keyPhrases`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`.

+ Defina um cabeçalho de solicitação para incluir a [chave de acesso](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) para operações de análise de texto.

+ No corpo da solicitação, forneça a coleção de documentos JSON preparada para esta análise

> [!Tip]
> Use o [Postman](text-analytics-how-to-call-api.md) ou abra o **console de teste da API** na [documentação](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) para estruturar uma solicitação e POSTAR no serviço.

## <a name="step-2-post-the-request"></a>Etapa 2: Postar a solicitação

A análise é executada após o recebimento da solicitação. Para saber mais sobre o tamanho e o número de solicitações que você pode enviar por minuto ou por segundo, confira a seção [limites de dados](../overview.md#data-limits) na visão geral.

Lembre-se de que o serviço é sem estado. Nenhum dado é armazenado em sua conta. Os resultados são retornados imediatamente na resposta.

## <a name="step-3-view-results"></a>Etapa 3: Exibir resultados

Todas as solicitações POST retornam uma resposta formatada em JSON com as IDs e as propriedades detectadas.

A saída é retornada imediatamente. Você pode transmitir os resultados para um aplicativo que aceita JSON ou salvar a saída em um arquivo no sistema local e, em seguida, importá-lo para um aplicativo que permite que você classifique, pesquise e manipule os dados.

Um exemplo de saída para extração de frase-chave é mostrado aqui:

```json
    {
        "documents": [
            {
                "keyPhrases": [
                    "year",
                    "trail",
                    "trip",
                    "views"
                ],
                "id": "1"
            },
            {
                "keyPhrases": [
                    "marked trails",
                    "Worst hike",
                    "goners"
                ],
                "id": "2"
            },
            {
                "keyPhrases": [
                    "trail",
                    "small children",
                    "family"
                ],
                "id": "3"
            },
            {
                "keyPhrases": [
                    "spectacular views",
                    "trail",
                    "area"
                ],
                "id": "4"
            },
            {
                "keyPhrases": [
                    "places",
                    "beautiful views",
                    "favorite trail"
                ],
                "id": "5"
            }
        ],
        "errors": []
    }
```

Conforme observado, o analisador localiza e descarta as palavras não essenciais e mantém frases ou termos únicos que parecem ser o sujeito ou o objeto de uma sentença.

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e o fluxo de trabalho de extração de frases-chave usando a Análise de Texto nos Serviços Cognitivos. Em resumo:

+ [API de extração de frase-chave](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) está disponível para os idiomas selecionados.
+ Documentos JSON no corpo da solicitação incluem um código de idioma, texto e ID.
+ A solicitação POST é um `/keyphrases` ponto de extremidade, usando uma [chave de acesso personalizada e um ponto de extremidade](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) que é válido para sua assinatura.
+ A saída de resposta, que consiste em palavras e frases-chave para cada ID do documento, pode ser transmitida para qualquer aplicativo que aceita JSON, incluindo o Microsoft Office Excel e o Power BI, para citar alguns.

## <a name="see-also"></a>Consulte também

 [Visão geral da Análise de Texto](../overview.md) [Perguntas frequentes](../text-analytics-resource-faq.md)</br>
 [Página de produto de Análise de Texto do Azure Machine Learning](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [API de Análise de Texto do Azure Machine Learning](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)
