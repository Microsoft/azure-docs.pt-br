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
ms.date: 03/29/2021
ms.author: aahi
ms.openlocfilehash: fdf24fdc56d39d93fed0009e2fadbafd7f97db6c
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280489"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Exemplo: como extrair frases-chave usando a Análise de Texto

A [API de Extração de Frases-chave](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) avalia o texto desestruturado e para cada documento JSON, retorna uma lista de frases-chave.

Esse recurso é útil se você precisar identificar rapidamente os principais pontos de estratégias em uma coleção de documentos. Por exemplo, para o texto de entrada dado "A comida estava deliciosa e a equipe era maravilhosa", o serviço retorna os principais pontos de discussão: "comida" e "equipe maravilhosa".

Para obter mais informações, consulte [Linguagens com suporte](../language-support.md).

> [!TIP]
> * A Análise de Texto também fornece uma imagem de contêiner do Docker baseado em Linux para extração de frases-chave, para que você possa [instalar e executar o contêiner de Análise de Texto](text-analytics-how-to-install-containers.md) perto de seus dados.
> * Você também pode usar esse recurso [de maneira assíncrona](text-analytics-how-to-call-api.md) usando o ponto de extremidade `/analyze`.

## <a name="preparation"></a>Preparação

A extração de frase-chave funciona melhor quando você concede trechos maiores de texto com os quais ela pode trabalhar. Isso é o oposto da análise de sentimento, que executa melhor em blocos de texto menores. Para obter os melhores resultados de ambas as operações, considere a reestruturação de entradas adequadamente.

Você deve ter documentos JSON neste formato: ID, texto, idioma

O tamanho do documento precisa ter 5.120 caracteres ou menos por documento, e você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo da solicitação. O exemplo a seguir é uma ilustração do conteúdo que você pode enviar para extração de frase-chave. 

Confira [Como chamar a API d Análise de Texto](text-analytics-how-to-call-api.md) para saber mais sobre objetos de solicitação e resposta.  

### <a name="example-synchronous-request-object"></a>Exemplo de objeto de solicitação síncrona


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

### <a name="example-asynchronous-request-object"></a>Exemplo de objeto de solicitação assíncrona

Começando no `v3.1-preview.3`, você pode enviar solicitações NER de maneira assíncrona usando o ponto de extremidade `/analyze`.


```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
    }
}
```

## <a name="step-1-structure-the-request"></a>Etapa 1: Estruturar a solicitação

Para saber mais sobre a definição de solicitação, confira [Como chamar a API de Análise de Texto](text-analytics-how-to-call-api.md). Os seguintes pontos são redeclarados para conveniência:

+ Criar uma solicitação de **Postagem**. Examine a documentação da API para esta solicitação: [API de Frases-chave](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases).

+ Defina o ponto de extremidade HTTP para extração de frases-chave usando um recurso de Análise de Texto no Azure ou um [contêiner de Análise de Texto](text-analytics-how-to-install-containers.md) instanciado. Se estiver usando a API de maneira síncrona, você deverá incluir `/text/analytics/v3.0/keyPhrases` na URL. Por exemplo: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`.

+ Defina um cabeçalho de solicitação para incluir a [chave de acesso](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) para operações de Análise de Texto do Azure Machine Learning.

+ No corpo da solicitação, forneça a coleção de documentos JSON preparada para esta análise

> [!Tip]
> Use o [Postman](text-analytics-how-to-call-api.md) ou abra o **console de teste da API** na [documentação](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) para estruturar uma solicitação e POSTAR no serviço.

## <a name="step-2-post-the-request"></a>Etapa 2: postar a solicitação

A análise é executada após o recebimento da solicitação. Para saber mais sobre o tamanho e o número de solicitações que você pode enviar por minuto ou por segundo, confira o artigo sobre [limites de dados](../concepts/data-limits.md).

Lembre-se de que o serviço é sem estado. Nenhum dado é armazenado em sua conta. Os resultados são retornados imediatamente na resposta.

## <a name="step-3-view-results"></a>Etapa 3: exibir resultados

Todas as solicitações POST retornam uma resposta formatada em JSON com as IDs e as propriedades detectadas. A ordem das frases-chave retornadas é determinada internamente pelo modelo.

A saída é retornada imediatamente. Você pode transmitir os resultados para um aplicativo que aceita JSON ou salvar a saída em um arquivo no sistema local e, em seguida, importá-lo para um aplicativo que permite que você classifique, pesquise e manipule os dados.

Um exemplo da saída para extração de frases-chave do ponto de extremidade v31-preview.2 é mostrado aqui:

### <a name="synchronous-result"></a>Resultado síncrono

```json
    {
       "documents":[
          {
             "id":"1",
             "keyPhrases":[
                "year",
                "trail",
                "trip",
                "views",
                "hike"
             ],
             "warnings":[]
          },
          {
             "id":"2",
             "keyPhrases":[
                "marked trails",
                "Worst hike",
                "goners"
             ],
             "warnings":[]
          },
          {
             "id":"3",
             "keyPhrases":[
                "trail",
                "small children",
                "family"
             ],
             "warnings":[]
          },
          {
             "id":"4",
             "keyPhrases":[
                "spectacular views",
                "trail",
                "Worth",
                "area"
             ],
             "warnings":[]
          },
          {
             "id":"5",
             "keyPhrases":[
                "places",
                "beautiful views",
                "favorite trail",
                "rest"
             ],
             "warnings":[]
          }
       ],
       "errors":[],
       "modelVersion":"2020-07-01"
    }

```
Conforme observado, o analisador localiza e descarta as palavras não essenciais e mantém frases ou termos únicos que parecem ser o sujeito ou o objeto de uma sentença.

### <a name="asynchronous-result"></a>Resultado assíncrono

Se usar o ponto de extremidade `/analyze` para a operação assíncrona, você receberá uma resposta contendo as tarefas que enviou para a API.

```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```


## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e o fluxo de trabalho de extração de frases-chave usando a Análise de Texto nos Serviços Cognitivos. Em resumo:

+ [API de extração de frase-chave](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) está disponível para os idiomas selecionados.
+ Documentos JSON no corpo da solicitação incluem um código de idioma, texto e ID.
+ A solicitação POST é para um ponto de extremidade `/keyphrases` ou `/analyze`, usando uma [chave de acesso personalizada e um ponto de extremidade](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) válido para sua assinatura.
+ A saída de resposta, que consiste em palavras e frases-chave para cada ID do documento, pode ser transmitida para qualquer aplicativo que aceita JSON, incluindo o Microsoft Office Excel e o Power BI, para citar alguns.

## <a name="see-also"></a>Confira também

 [Visão geral da Análise de Texto](../overview.md) [Perguntas frequentes](../text-analytics-resource-faq.md)</br>
 [Página de produto de Análise de Texto do Azure Machine Learning](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da Análise de Texto](../overview.md)
* [Como usar a biblioteca de clientes da Análise de Texto](../quickstarts/client-libraries-rest-api.md)
* [Novidades](../whats-new.md)
