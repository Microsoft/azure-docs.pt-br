---
title: Usar o reconhecimento de entidade com a API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Aprenda a reconhecer entidades usando a API REST de Análise de Texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: ea7d3f56aa512b8f5998d710451ff3b37659ca13
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697838"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Como usar o reconhecimento de entidade nomeada no Análise de Texto

A [API de reconhecimento de entidade nomeada](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) usa texto não estruturado e, para cada documento JSON, retorna uma lista de entidades desambiguadas com links para mais informações na Web (Wikipédia e Bing).

## <a name="entity-linking-and-named-entity-recognition"></a>Vinculação de Entidade e Reconhecimento de Entidade Nomeada

O ponto de `entities` extremidade análise de texto ' dá suporte ao reconhecimento de entidade nomeada (Ner) e à vinculação de entidade.

### <a name="entity-linking"></a>Vinculação de Identidade
Vinculação de entidade é a capacidade de identificar e desambiguar a identidade de uma entidade encontrada no texto (por exemplo, determinar se o "Marte" está sendo usado como o planeta ou como o deus romano da guerra). Esse processo exige a presença de uma base de conhecimento a qual as entidades reconhecidas são vinculadas. A Wikipédia é usada como a base de conhecimento para o ponto de extremidade `entities` da Análise de Texto.

### <a name="named-entity-recognition-ner"></a>NER (Reconhecimento de Entidade Nomeada)
O NER (reconhecimento de entidade nomeada) é a capacidade de identificar diferentes entidades no texto e categorizá-las em classes predefinidas. As classes de entidades com suporte estão listadas abaixo.

No Análise de Texto [versão 2,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634), a vinculação de entidade e o reconhecimento de entidade nomeada (Ner) estão disponíveis para vários idiomas. Consulte o artigo [suporte ao idioma](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) para obter mais informações.

### <a name="language-support"></a>Suporte de idioma

O uso da vinculação de entidade em vários idiomas exige o uso de uma base de conhecimento correspondente em cada idioma. Para vinculação de entidade na Análise de Texto, isso significa que cada idioma com suporte do ponto de extremidade `entities` será vinculado ao corpus da Wikipédia correspondente nesse idioma. Como o tamanho do corpora varia entre as linguagens, espera-se que a funcionalidade de vinculação de entidade também variará.

## <a name="supported-types-for-named-entity-recognition"></a>Tipos com Suporte para Reconhecimento de Entidade Nomeada

| Tipo  | SubType | Exemplo |
|:-----------   |:------------- |:---------|
| Pessoa        | N/D\*         | "João", "Bill Gates"     |
| Location      | N/D\*         | "Redmond, Washington", "Paris"  |
| Organização  | N/D\*         | "Microsoft"   |
| Quantidade      | Número        | "6", "seis"     |
| Quantidade      | Percentual    | "50%", "cinquenta por cento"|
| Quantidade      | Ordinal       | "2º", "segundo"     |
| Quantidade      | NumberRange   | "4 a 8"     |
| Quantidade      | Idade           | "90 dias", "30 anos"    |
| Quantidade      | Currency      | "US$ 10,99"     |
| Quantidade      | Dimensão     | "10 milhas", "40 cm"     |
| Quantidade      | Temperatura   | "32 graus"    |
| DateTime      | N/D\*         | "18h30 em 4 de fevereiro de 2012"      |
| DateTime      | Date          | "2 de maio de 2017", "02/05/2017"   |
| DateTime      | Time          | "8h", "8:00"  |
| DateTime      | DateRange     | "2 de maio a 5 de maio"    |
| DateTime      | TimeRange     | "18h às 19h"     |
| DateTime      | Duração      | "1 minuto e 45 segundos"   |
| DateTime      | Definir           | "toda terça-feira"     |
| DateTime      | TimeZone      |    |
| URL           | N/D\*         | "https:\//www.bing.com"    |
| Email         | N/D\*         | "support@contoso.com" |

\* Dependendo da entrada e das entidades extraídas, determinadas entidades podem omitir o `SubType`.  Todos os tipos de entidade com suporte listados estão disponíveis apenas para os idiomas inglês, chinês simplificado, francês, alemão e espanhol.



## <a name="preparation"></a>Preparação

Você deve ter documentos JSON neste formato: ID, texto, idioma

Para os idiomas atualmente suportados, veja [esta lista](../text-analytics-supported-languages.md).

O tamanho do documento precisa ter menos de 5.120 caracteres por documento, e você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo da solicitação. O exemplo a seguir é uma ilustração do conteúdo que você pode enviar para a extremidade de vinculação de entidade.

```json
    {
        "documents": [
            {
                "id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
            },
            {
                "id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Etapa 1: Estruturar a solicitação

Detalhes sobre a definição de solicitação podem ser encontrados em [Como chamar a API de Análise de Texto](text-analytics-how-to-call-api.md). Os seguintes pontos são redeclarados para conveniência:

+ Criar uma solicitação de **Postagem**. Examine a documentação da API para esta solicitação: [API de entidades](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Defina o ponto de extremidade HTTP para extração de entidade. Deve incluir o recurso `/entities`: `https://[your-region].api.cognitive.microsoft.com/text/analytics/v2.1/entities`

+ Defina um cabeçalho de solicitação para incluir [a chave de acesso](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) para operações de análise de texto.

+ No corpo da solicitação, forneça a coleção de documentos JSON preparada para esta análise

> [!Tip]
> Use o [Postman](text-analytics-how-to-call-api.md) ou abra o **console de teste da API** na [documentação](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) para estruturar uma solicitação e POSTAR no serviço.

## <a name="step-2-post-the-request"></a>Etapa 2: Postar a solicitação

A análise é executada após o recebimento da solicitação. Consulte a seção [limites de dados](../overview.md#data-limits) na visão geral para obter informações sobre o tamanho e o número de solicitações que você pode enviar por minuto e segundo.

Lembre-se de que o serviço é sem estado. Nenhum dado é armazenado em sua conta. Os resultados são retornados imediatamente na resposta.

## <a name="step-3-view-results"></a>Etapa 3: Exibir resultados

Todas as solicitações POST retornam uma resposta formatada em JSON com as IDs e as propriedades detectadas.

A saída é retornada imediatamente. Você pode transmitir os resultados para um aplicativo que aceita JSON ou salvar a saída em um arquivo no sistema local e, em seguida, importá-lo para um aplicativo que permite que você classifique, pesquise e manipule os dados.

Um exemplo de saída da vinculação de entidade é mostrado a seguir:

```json
    {
        "Documents": [
            {
                "Id": "1",
                "Entities": [
                    {
                        "Name": "Jeff",
                        "Matches": [
                            {
                                "Text": "Jeff",
                                "Offset": 0,
                                "Length": 4
                            }
                        ],
                        "Type": "Person"
                    },
                    {
                        "Name": "three dozen",
                        "Matches": [
                            {
                                "Text": "three dozen",
                                "Offset": 12,
                                "Length": 11
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50",
                        "Matches": [
                            {
                                "Text": "50",
                                "Offset": 49,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50%",
                        "Matches": [
                            {
                                "Text": "50%",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            },
            {
                "Id": "2",
                "Entities": [
                    {
                        "Name": "Great Depression",
                        "Matches": [
                            {
                                "Text": "The Great Depression",
                                "Offset": 0,
                                "Length": 20
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Great Depression",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                        "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                    },
                    {
                        "Name": "1929",
                        "Matches": [
                            {
                                "Text": "1929",
                                "Offset": 30,
                                "Length": 4
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "By 1933",
                        "Matches": [
                            {
                                "Text": "By 1933",
                                "Offset": 36,
                                "Length": 7
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "Gross domestic product",
                        "Matches": [
                            {
                                "Text": "GDP",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Gross domestic product",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                        "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                    },
                    {
                        "Name": "United States",
                        "Matches": [
                            {
                                "Text": "America",
                                "Offset": 56,
                                "Length": 7
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "United States",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                        "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                        "Type": "Location"
                    },
                    {
                        "Name": "25",
                        "Matches": [
                            {
                                "Text": "25",
                                "Offset": 72,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "25%",
                        "Matches": [
                            {
                                "Text": "25%",
                                "Offset": 72,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            }
        ],
        "Errors": []
    }
```

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e fluxo de trabalho para detecção de idioma usando a Análise de Texto em Serviços Cognitivos. Em resumo:

+ A [API de Entidades](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) está disponível para os idiomas selecionados.
+ Documentos JSON no corpo da solicitação incluem um código de idioma, texto e ID.
+ A solicitação POST é um `/entities` ponto de extremidade, usando uma [chave de acesso personalizada e um ponto de extremidade](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) que é válido para sua assinatura.
+ A saída da resposta, composta por entidades vinculadas (incluindo pontuações de confiança, deslocamentos e links da Web, para cada ID de documento) pode ser usada em qualquer aplicativo

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [API de Análise de Texto do Azure Machine Learning](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [Visão geral da Análise de Texto](../overview.md)
* [Perguntas frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
* [Página de produto de Análise de Texto do Azure Machine Learning](//go.microsoft.com/fwlink/?LinkID=759712)
