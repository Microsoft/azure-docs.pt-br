---
title: Recibos – Reconhecimento de Formulários
titleSuffix: Azure Cognitive Services
description: Aprenda os conceitos relacionados à análise de recibos com a API de Reconhecimento de Formulários – uso e limites.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 8248b3ed21561340e963c848dee4430c48829ab1
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285290"
---
# <a name="form-recognizer-prebuilt-receipt-model"></a>Modelo de recibo predefinido do Reconhecimento de Formulários

O Reconhecimento de Formulários do Azure pode analisar e extrair informações de recibos de vendas usando um modelo de recebimento predefinido. Ele combina nossas poderosas funcionalidades de [OCR (reconhecimento óptico de caracteres)](../computer-vision/overview-ocr.md) com modelos de aprendizado profundo para extrair informações importantes de recibos escritos em inglês.

## <a name="understanding-receipts"></a>Como entender os recibos

Muitas empresas e pessoas ainda dependem de dados extraídos manualmente de recibos de vendas. A extração automática de dados desses recibos pode ser complicada. Os recibos podem estar amassados, ser difíceis de ler, ter partes manuscritas e conter imagens de smartphone de baixa qualidade. Além disso, os modelos e os campos de recibos podem variar muito por mercado, região e comerciante. Esses desafios de detecção de campo e extração de dados fazem com que o processamento de recibos seja um problema exclusivo.  

A API de Recibos usa o OCR (reconhecimento óptico de caracteres) e nosso modelo predefinido para habilitar os vastos cenários de processamento de recibos. Com a API de Recibos, não é necessário treinar um modelo. Basta enviar a imagem do recibo à API de Análise de Recibo para que os dados sejam extraídos.

![exemplo de recibo](./media/receipts-example.jpg)


## <a name="what-does-the-receipt-service-do"></a>O que o serviço de Recibos faz? 

O serviço de Recibos predefinido extrai o conteúdo dos recibos de vendas – o tipo de recibo que você geralmente recebe em restaurantes, varejistas ou mercados.

### <a name="fields-extracted"></a>Campos extraídos

|Nome| Tipo | Descrição | Texto | Valor (saída padronizada) |
|:-----|:----|:----|:----| :----|
| ReceiptType | string | Tipo de recibo de vendas | Itemizado |  |
| MerchantName | string | Nome do comerciante que está emitindo o recibo | Contoso |  |
| MerchantPhoneNumber | phoneNumber | Número de telefone listado do comerciante | 987-654-3210 | +19876543210 |
| MerchantAddress | string | Endereço listado do comerciante | 123 Main St Redmond WA 98052 |  |
| Data da transação | date | Data em que o recibo foi emitido | 06 de junho de 2019 | 26-06-2019  |
| TransactionTime | time | Hora em que o recibo foi emitido | 16h49 | 16:49:00  |
| Total | número | Total de transações completas do recibo | US$ 14,34 | 14.34 |
| Subtotal | número | Subtotal do recibo, geralmente antes da aplicação de impostos | US$ 12,34 | 12.34 |
| Imposto | número | Imposto no recibo, geralmente o imposto sobre vendas ou equivalente | $2.00 | 2,00 |
| Dica | número | Gorjeta incluída pelo comprador | US$ 1,00 | 1.00 |
| Itens | matriz de objetos | Itens de linha extraídos, com nome, quantidade, preço unitário e preço total extraído | |
| Nome | cadeia de caracteres | Nome do item | Surface Pro 6 | |
| Quantidade | número | Quantidade de cada item | 1 | |
| Preço | número | Preço individual de cada unidade de item | US$ 999,00 | 999.00 |
| Preço total | número | Preço total do item de linha | US$ 999,00 | 999.00 |

### <a name="additional-features"></a>Recursos adicionais

A API de Recibos também retorna as seguintes informações:

* Nível de confiança do campo (cada campo retorna um valor de confiança associado)
* Texto bruto de OCR (saída de texto extraída por OCR do recibo inteiro)
* Caixa delimitadora de cada valor, linha e palavra

## <a name="try-it-out"></a>Experimente

Para experimentar o serviço Reconhecimento de Formulários, acesse a ferramenta de interface do usuário de exemplo online:

> [!div class="nextstepaction"]
> [Experimentar modelos predefinidos](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Requisitos de entrada

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Localidades com suporte 

* O **Recibo Predefinido v2.0** (GA) dá suporte a recibos de vendas na localidade EN-US
* O **Recibo Predefinido v2.1-preview.3** (versão prévia pública) adiciona suporte para as seguintes localidades de recibo em inglês: 
  * EN-AU 
  * EN-CA 
  * EN-GB 
  * EN-IN 

  > [!NOTE]
  > Entrada de idioma 
  >
  > O Recibo Predefinido v2.1-preview.3 tem um parâmetro de solicitação opcional para especificar uma localidade de recibo de outros mercados em inglês. Para recibos de vendas em inglês da Austrália (EN-AU), do Canadá (EN-CA), da Grã-Bretanha (EN-GB) e da Índia (EN-IN), você pode especificar a localidade para obter resultados aprimorados. Se não for especificada nenhuma localidade no v2.1-preview.3, o modelo usará como padrão a EN-US.


## <a name="the-analyze-receipt-operation"></a>A operação Analisar Recibo

A operação [Analisar Recibo](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync) usa uma imagem ou um PDF de um recibo como entrada e extrai os valores de interesse e o texto. A chamada retorna um campo de cabeçalho de resposta chamado `Operation-Location`. O valor `Operation-Location` é uma URL que contém a ID do Resultado a ser usada na próxima etapa.

|Cabeçalho de resposta| URL do Resultado |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/56a36454-fc4d-4354-aa07-880cfbf0064f` |

## <a name="the-get-analyze-receipt-result-operation"></a>A operação Obter o Resultado da Análise de Recibo

A segunda etapa é chamar a operação [Obter o Resultado da Análise de Recibo](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeReceiptResult). Essa operação usa como entrada a ID de Resultado que foi criada pela operação de Análise de Recibo. Ela retorna uma resposta JSON que contém um campo de **status** com os valores possíveis a seguir. Execute uma chamada a essa operação de modo iterativo até que ela retorne o valor **succeeded**. Use um intervalo de três a cinco segundos para evitar que a taxa de RPS (solicitações por segundo) seja excedida.

|Campo| Tipo | Valores possíveis |
|:-----|:----:|:----|
|status | string | notStarted: a operação não foi iniciada. |
| |  | running: a operação de análise está em andamento. |
| |  | failed: falha na operação de análise. |
| |  | succeeded: a operação de análise foi executada com êxito. |

Quando o campo **status** tiver o valor **succeeded**, a resposta JSON incluirá os resultados de reconhecimento de texto e de entendimento do recibo. O resultado do entendimento do recibo é organizado como um dicionário de valores de campo nomeado. Cada valor contém o texto extraído, o valor normalizado, a caixa delimitadora, a confiança e os elementos de palavras correspondentes. O resultado do reconhecimento de texto é organizado como uma hierarquia de linhas e palavras, com informações de texto, de caixa delimitadora e de confiança.

![resultados de recibo de exemplo](./media/contoso-receipt-2-information.png)

### <a name="sample-json-output"></a>Saída JSON de exemplo


A resposta para a operação Obter o Resultado da Análise de Recibo será a representação estruturada do recibo com todas as informações extraídas.  Veja aqui um [exemplo de arquivo de recibo](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-allinone.jpg) e a [saída do recibo de exemplo](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json) de saída estruturada.

Confira o seguinte exemplo de uma resposta JSON bem-sucedida:
* O nó `"readResults"` contém todo o texto reconhecido. O texto é organizado por página, depois por linha e, em seguida, por palavras individuais. 
* O nó `"documentResults"` contém os valores específicos do cartão de visita descobertos pelo modelo. É nele que você encontrará os pares chave-valor úteis, como o nome, o sobrenome, o nome da empresa e outros.

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[ 
      { 
        "docType":"prebuilt:receipt",
        "pageRange":[ 
          1,
          1
        ],
        "fields":{ 
          "ReceiptType":{ 
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{ 
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[ 
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[ 
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{ 
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[ 
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{ 
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[ 
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{ 
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[ 
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{ 
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[ 
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[ 
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{ 
            "type":"array",
            "valueArray":[ 
              { 
                "type":"object",
                "valueObject":{ 
                  "Quantity":{ 
                    "type":"number",
                    "text":"1",
                    "boundingBox":[ 
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{ 
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[ 
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{ 
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[ 
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[ 
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{ 
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[ 
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[ 
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{ 
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[ 
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[ 
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{ 
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[ 
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[ 
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{ 
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[ 
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[ 
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="customer-scenarios"></a>Cenários de clientes  

Os dados extraídos com a API de Recibos podem ser usados para executar uma variedade de tarefas. Veja abaixo alguns exemplos das ações realizadas pelos clientes com a API de Recibos.

### <a name="business-expense-reporting"></a>Relatório de despesas empresariais  

Geralmente, o registro das despesas empresariais exige um tempo necessário para inserir manualmente os dados das imagens dos recibos. Com a API de Recibos, você pode usar os campos extraídos para automatizar parcialmente esse processo e analisar seus recibos rapidamente.  

A API de Recibos é uma saída JSON simples que permite usar os valores de campo extraídos de várias maneiras. Integre-a com aplicativos de despesas internos para preencher previamente os relatórios de despesas. Para saber mais sobre esse cenário, leia sobre como a Acumatica está utilizando a API de Recibos para [reduzir a dificuldade do processo de relatório de despesas](https://customers.microsoft.com/story/762684-acumatica-partner-professional-services-azure).  

### <a name="auditing-and-accounting"></a>Auditoria e contabilidade

A saída da API de Recibos também pode ser usada para executar a análise em muitas despesas em vários pontos no processo de relatório e reembolso de despesas. Você pode processar os recibos a fim de fazer uma triagem para auditoria manual ou aprovações rápidas.  

A saída de Recibo também é útil para a contabilidade de uso pessoal ou empresarial. Use a API de Recibos para transformar todos os dados brutos de imagem/PDF de recibo em uma saída digital acionável.

### <a name="consumer-behavior"></a>Comportamento do consumidor 

Os recibos contêm dados úteis que você pode usar para analisar o comportamento do consumidor e as tendências de compra.

A API de Recibos também alimenta o [recurso de processamento de recibos do AI Builder](/ai-builder/prebuilt-receipt-processing).

## <a name="next-steps"></a>Próximas etapas

 Comece a criar um aplicativo de processamento de recibos com o Reconhecimento de Formulários na linguagem de desenvolvimento da sua escolha.

> [!div class="nextstepaction"]
> [Conclua um guia de início rápido do Reconhecimento de Formulários](quickstarts/client-library.md)

## <a name="see-also"></a>Confira também

* [O que é o Reconhecimento de Formulários?](overview.md)
* [Referência da API do Reconhecimento de Formulários](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync)
>
