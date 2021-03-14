---
title: Recibos-reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados à análise de recebimento com a API do reconhecedor de formulário-uso e limites.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 565ba3f7cd02a5ca8a3a858dc29a8fa6c7df16c1
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545999"
---
# <a name="form-recognizer-prebuilt-receipt-model"></a>Modelo de recebimento predefinido do reconhecedor de formulário

O reconhecedor de formulários do Azure pode analisar e extrair informações de recibos de vendas usando seu modelo de recebimento predefinido. Ele combina nossos poderosos recursos de [OCR (reconhecimento óptico de caracteres)](../computer-vision/concept-recognizing-text.md) com recebimento, entendendo modelos de aprendizado profundo para extrair informações importantes dos recibos em inglês. A API de recebimento extrai informações de chave de recibos de vendas em inglês, como o nome do comerciante, a data da transação, o total da transação, os itens de linha e muito mais. 

## <a name="understanding-receipts"></a>Entendendo os recibos 

Muitas empresas e indivíduos ainda dependem da extração manual de dados de seus recibos de vendas, seja para relatórios de despesas comerciais, reembolsos, auditoria, propósitos fiscais, orçamento, marketing ou outras finalidades. Geralmente nesses cenários, as imagens do recebimento físico são necessárias para fins de validação.  

A extração automática de dados dessas confirmações pode ser complicada. Os recibos podem ser crumpleddos e difíceis de ler, as partes impressa ou manuscritas e as imagens do smartphone de recibos podem ser de baixa qualidade. Além disso, modelos de recebimento e campos podem variar muito por mercado, região e comerciante. Esses desafios na extração dos dados e na detecção de campos fazem com que o processamento de recibos seja um problema exclusivo.  

Usando o OCR (reconhecimento óptico de caracteres) e nosso modelo de recebimento precompilado, a API de recebimento permite esses cenários de processamento de recebimento e extrai dados dos recibos, por exemplo, nome do comerciante, gorjeta, total, itens de linha e muito mais. Com essa API, não há necessidade de treinar um modelo, basta enviar a imagem de recebimento para a API de análise de recebimento e os dados são extraídos.

![exemplo de recibo](./media/receipts-example.jpg)


## <a name="what-does-the-receipt-service-do"></a>O que o serviço de recebimento faz? 

O serviço de recebimento predefinido extrai o conteúdo de recibos de vendas &mdash; o tipo de recibo que você normalmente obteria em um restaurante, varejista ou armazenamento de supermercado.

### <a name="fields-extracted"></a>Campos extraídos

|Nome| Type | Descrição | Texto | Valor (saída padronizada) |
|:-----|:----|:----|:----| :----|
| Recibotype | string | Tipo de recibo de vendas | Detalhadas |  |
| Comerciantename | string | Nome do comerciante que está emitindo o recebimento | Contoso |  |
| MerchantPhoneNumber | phoneNumber | Número de telefone listado do comerciante | 987-654-3210 | + 19876543210 |
| MerchantAddress | string | Endereço listado do comerciante | 123 principal Saint Redmond WA 98052 |  |
| Data da transação | date | Data em que o recebimento foi emitido | 06 de junho de 2019 | 2019-06-26  |
| Transacionatime | time | Hora em que o recebimento foi emitido | 4:49 PM | 16:49:00  |
| Total | número | Total de transações completas de recebimento | $14.34 | 14,34 |
| Subtotal | número | Subtotal do recibo, geralmente antes que os impostos sejam aplicados | $12.34 | 12.34 |
| Imposto | número | Imposto sobre o recebimento, geralmente imposto sobre vendas ou equivalente | $2.00 | 2,00 |
| Dica | número | Dica incluída pelo comprador | $1 | 1.00 |
| Itens | matriz de objetos | Itens de linha extraídos, com nome, quantidade, preço unitário e preço total extraído | |
| Nome | cadeia de caracteres | Nome do item | Surface Pro 6 | |
| Quantidade | número | Quantidade de cada item | 1 | |
| Preço | número | Preço individual de cada unidade de item | $999 | 999, 0 |
| Preço total | número | Preço total do item de linha | $999 | 999, 0 |

### <a name="additional-features"></a>Recursos adicionais

A API de recebimento também retorna as seguintes informações:

* Nível de confiança de campo (cada campo retorna um valor de confiança associado)
* Texto bruto de OCR (saída de texto extraído por OCR para o recebimento inteiro)
* Caixa delimitadora para cada valor, linha e palavra

## <a name="try-it-out"></a>Experimente

Para experimentar o serviço de recebimento do reconhecedor de formulário, vá para a ferramenta de interface do usuário de exemplo online:

> [!div class="nextstepaction"]
> [Experimentar modelos predefinidos](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Requisitos de entrada

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Localidades com suporte 

* O recebimento predefinido **v 2.0** (GA) dá suporte a recibos de vendas na localidade en-US
* **Recebimento predefinido v 2.1-Preview. 2** (visualização pública) adiciona suporte adicional para as seguintes localidades de recebimento: 
  * EN-AU 
  * EN-AC 
  * EN-GB 
  * EN-IN 

  > [!NOTE]
  > Entrada de idioma 
  >
  > Confirmação predefinida v 2.1-Preview. 2 tem um parâmetro de solicitação opcional para especificar uma localidade de recebimento de outros mercados em inglês. Para recibos de vendas em inglês da Austrália (EN-AU), Canadá (EN-CA), Grã-Bretanha (EN-GB) e Índia (EN-IN), você pode especificar a localidade para obter resultados aprimorados. Se nenhuma localidade for especificada em v 2.1-Preview. 2, o modelo usará como padrão o modelo EN-US.


## <a name="the-analyze-receipt-operation"></a>A operação de análise de recebimento

A [confirmação de análise](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeReceiptAsync) usa uma imagem ou um PDF de um recibo como entrada e extrai os valores de interesse e texto. A chamada retorna um campo de cabeçalho de resposta chamado `Operation-Location` . O `Operation-Location` valor é uma URL que contém a ID de resultado a ser usada na próxima etapa.

|Cabeçalho de resposta| URL do resultado |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/56a36454-fc4d-4354-aa07-880cfbf0064f` |

## <a name="the-get-analyze-receipt-result-operation"></a>A operação obter resultado da confirmação de análise

A segunda etapa é chamar a operação [obter resultado de recebimento de análise](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeReceiptResult) . Essa operação usa como entrada a ID de resultado que foi criada pela operação de confirmação de análise. Ele retorna uma resposta JSON que contém um campo de **status** com os seguintes valores possíveis. Você chama essa operação iterativamente até que ela retorne com o valor **Succeeded** . Use um intervalo de 3 a 5 segundos para evitar exceder a taxa de solicitações por segundo (RPS).

|Campo| Type | Valores possíveis |
|:-----|:----:|:----|
|status | string | não iniciado: a operação de análise não foi iniciada. |
| |  | em execução: a operação de análise está em andamento. |
| |  | falha: falha na operação de análise. |
| |  | êxito: a operação de análise foi bem-sucedida. |

Quando o campo **status** tiver o valor **êxito** , a resposta JSON incluirá os resultados de reconhecimento de texto e compreensão de recibo. O resultado da compreensão de recebimento é organizado como um dicionário de valores de campo nomeados, em que cada valor contém o texto extraído, o valor normalizado, a caixa delimitadora, a confiança e os elementos correspondentes do Word. O resultado do reconhecimento de texto é organizado como uma hierarquia de linhas e palavras, com informações de texto, de caixa delimitadora e de confiança.

![resultados de recibo de exemplo](./media/contoso-receipt-2-information.png)

### <a name="sample-json-output"></a>Saída JSON de exemplo


A resposta para a operação obter resultado da confirmação de análise será a representação estruturada do recibo com todas as informações extraídas.  Consulte aqui para obter um [exemplo de arquivo de recebimento](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-allinone.jpg) e sua saída de saída estruturada de [exemplo](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json).

Consulte o exemplo a seguir de uma resposta JSON bem-sucedida:
* O nó `"readResults"` contém todo o texto reconhecido. O texto é organizado por página, depois por linha e, em seguida, por palavras individuais. 
* O nó `"documentResults"` contém os valores específicos do cartão de visita descobertos pelo modelo. É aí que você encontrará pares úteis de chave/valor como nome, sobrenome, nome da empresa e muito mais.

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

Os dados extraídos com a API de recebimento podem ser usados para executar uma variedade de tarefas. Veja a seguir alguns exemplos do que nossos clientes fizeram com a API de recebimento. 

### <a name="business-expense-reporting"></a>Relatório de despesas comerciais  

Com frequência, o arquivamento das despesas de negócios envolve o tempo de gastos inserindo manualmente dados de imagens de recibos. Com a API de recebimento, você pode usar os campos extraídos para automatizar parcialmente esse processo e analisar seus recibos rapidamente.  

Como a API de recebimento tem uma saída JSON simples, você pode usar os valores de campo extraídos de várias maneiras. Integre-se com aplicativos de despesas internos para preencher previamente os relatórios de despesas. Para saber mais sobre esse cenário, leia sobre como o Acumatica está utilizando a API de recebimento para [tornar o relatório de despesas um processo menos trabalhoso](https://customers.microsoft.com/story/762684-acumatica-partner-professional-services-azure).  

### <a name="auditing-and-accounting"></a>Auditoria e contabilidade 

A saída da API de recebimento também pode ser usada para executar a análise em um grande número de despesas em vários pontos no processo de relatório e reembolso de despesas. Você pode processar recibos para fazer a triagem para auditoria manual ou aprovações rápidas.  

A saída de recebimento também é útil para o livro geral-mantendo para uso pessoal ou comercial. Use a API de recebimento para transformar todos os dados brutos de imagem/PDF em uma saída digital que é acionável.

### <a name="consumer-behavior"></a>Comportamento do consumidor 

Os recibos contêm dados úteis que você pode usar para analisar o comportamento do consumidor e as tendências de compra.

A API de recebimento também alimenta o [recurso de processamento de recibo do ia Builder](/ai-builder/prebuilt-receipt-processing).

## <a name="next-steps"></a>Próximas etapas

- Conclua um [início rápido do reconhecedor de formulário](quickstarts/client-library.md) para começar a gravar um aplicativo de processamento de recibo com o reconhecedor de formulário na linguagem de desenvolvimento de sua escolha.

## <a name="see-also"></a>Confira também

* [O que é o Reconhecimento de Formulários?](./overview.md)
* [Documentos de referência da API REST](./index.yml)