---
title: Cartões de visita-reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados à análise de cartão de negócios com a API do reconhecedor de formulário-uso e limites.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: c2543f74b90205a36d3f5b4481beca35c779f77e
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546016"
---
# <a name="form-recognizer-prebuilt-business-cards-model"></a>Modelo de cartões de visita pré-criados do reconhecedor de formulário 

O reconhecedor de formulários do Azure pode analisar e extrair informações de contato de cartões de visita usando seu modelo de cartões de visita predefinidos. Ele combina recursos avançados de OCR (reconhecimento óptico de caracteres) com nosso modelo de compreensão de cartão de negócios para extrair informações importantes de cartões de visita em inglês. Ele extrai informações de contato pessoal, nome da empresa, cargo e muito mais. A API de cartão de visita predefinida está publicamente disponível no formulário versão prévia do Recognizer v 2.1. 

## <a name="what-does-the-business-card-service-do"></a>O que o serviço de cartão de negócios faz?

A API de cartão de visita predefinida extrai campos de chave de cartões de visita e os retorna em uma resposta de JSON organizada.

![Imagem do contoso discriminada da saída FOTT + JSON](./media/business-card-example.jpg)



### <a name="fields-extracted"></a>Campos extraídos:

|Nome| Type | Descrição | Texto | 
|:-----|:----|:----|:----|
| ContactNames | matriz de objetos | Nome do contato extraído do cartão de visita | [{"FirstName": "John", "LastName": "Doe"}] |
| Nome | string | Primeiro (fornecido) nome do contato | "John" | 
| LastName | string | Último nome (família) do contato |     "Doe" | 
| Empresasnames | Matriz de cadeias de caracteres | Nome da empresa extraído do cartão de visita | ["Contoso"] | 
| Departments | Matriz de cadeias de caracteres | Departamento ou organização de contato | ["R&D"] | 
| JobTitles | Matriz de cadeias de caracteres | Título de trabalho de contato listado | ["Engenheiro de software"] | 
| Emails | Matriz de cadeias de caracteres | Email de contato extraído do cartão de visita | ["johndoe@contoso.com"] | 
| Sites | Matriz de cadeias de caracteres | Site extraído do cartão de visita | ["https://www.contoso.com"] | 
| Endereços | Matriz de cadeias de caracteres | Endereço extraído do cartão de visita | ["rua principal 123, Redmond, WA 98052"] | 
| MobilePhones | matriz de números de telefone | Número de telefone celular extraído do cartão de visita | ["+ 19876543210"] |
| Cujo | matriz de números de telefone | Número de telefone de fax extraído do cartão de visita | ["+ 19876543211"] |
| WorkPhones | matriz de números de telefone | Número de telefone de trabalho extraído do cartão de visita | ["+ 19876543231"] |
| OtherPhones     | matriz de números de telefone | Outro número de telefone extraído do cartão de visita | ["+ 19876543233"] |


A API do cartão de negócios também pode retornar todo o texto reconhecido do cartão de visita. Essa saída de OCR é incluída na resposta JSON.  

### <a name="input-requirements"></a>Requisitos de entrada 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-business-card-operation"></a>A operação analisar cartão de negócios

O [cartão de visita de análise](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync) usa uma imagem ou um PDF de um cartão de visita como entrada e extrai os valores de interesse. A chamada retorna um campo de cabeçalho de resposta chamado `Operation-Location` . O `Operation-Location` valor é uma URL que contém a ID de resultado a ser usada na próxima etapa.

|Cabeçalho de resposta| URL do resultado |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-business-card-result-operation"></a>A operação obter resultado do cartão de negócios Get Analyze

A segunda etapa é chamar a operação [obter resultado do cartão de negócios Get Analyze](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult) . Essa operação usa como entrada a ID de resultado que foi criada pela operação de análise de cartão de negócios. Ele retorna uma resposta JSON que contém um campo de **status** com os seguintes valores possíveis. Você chama essa operação iterativamente até que ela retorne com o valor **Succeeded** . Use um intervalo de 3 a 5 segundos para evitar exceder a taxa de solicitações por segundo (RPS).

|Campo| Type | Valores possíveis |
|:-----|:----:|:----|
|status | string | não iniciado: a operação de análise não foi iniciada.<br /><br />em execução: a operação de análise está em andamento.<br /><br />falha: falha na operação de análise.<br /><br />êxito: a operação de análise foi bem-sucedida.|

Quando o campo **status** tiver o valor com **êxito** , a resposta JSON incluirá o conhecimento do cartão de visita e os resultados de reconhecimento de texto opcional, se solicitado. O resultado da compreensão do cartão de visita é organizado como um dicionário de valores de campo nomeados, em que cada valor contém o texto extraído, o valor normalizado, a caixa delimitadora, a confiança e os elementos correspondentes do Word. O resultado do reconhecimento de texto é organizado como uma hierarquia de linhas e palavras, com informações de texto, de caixa delimitadora e de confiança.

![saída do cartão de visita de exemplo](./media/business-card-results.png)

### <a name="sample-json-output"></a>Saída JSON de exemplo

A resposta à operação obter resultado do cartão de negócios obter será a representação estruturada do cartão de visita com todas as informações extraídas.  Consulte aqui para obter um [exemplo de arquivo de cartão de visita](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-english.jpg) e sua saída estruturada de amostra de exemplo de cartão de [visita](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json).

Consulte o exemplo a seguir de uma resposta JSON bem-sucedida:
* O nó `"readResults"` contém todo o texto reconhecido. O texto é organizado por página, depois por linha e, em seguida, por palavras individuais. 
* O nó `"documentResults"` contém os valores específicos do cartão de visita descobertos pelo modelo. É aí que você encontrará informações úteis de contato como nome, sobrenome, nome da empresa e muito mais.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T17:41:19Z",
    "lastUpdatedDateTime": "2020-08-20T17:41:24Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel",
                   "lines": 
                             {
                        "text": "Dr. Avery Smith",
                        "boundingBox": [
                            419.3,
                            1154.6,
                            1589.6,
                            877.9,
                            1618.9,
                            1001.7,
                            448.6,
                            1278.4
                        ],
                        "words": [
                            {
                                "text": "Dr.",
                                "boundingBox": [
                                    419,
                            ]
    
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1186,
                                            976,
                                            1585,
                                            879,
                                            1618,
                                            998,
                                            1218,
                                            1096
                                        ],
                                        "page": 1
                                    }
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.97
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    451.8,
                                    1301.9,
                                    1313.5,
                                    1099.9,
                                    1333.8,
                                    1186.7,
                                    472.2,
                                    1388.7
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    480.1,
                                    1403.3,
                                    1590.5,
                                    1129.6,
                                    1612.6,
                                    1219.6,
                                    502.3,
                                    1493.3
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 7911 123456",
                                "boundingBox": [
                                    2434.9,
                                    1033.3,
                                    3072,
                                    836,
                                    3096.2,
                                    914.3,
                                    2459.1,
                                    1111.6
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "OtherPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 9876 5432",
                                "boundingBox": [
                                    2473.2,
                                    1115.4,
                                    3139.2,
                                    907.7,
                                    3163.2,
                                    984.7,
                                    2497.2,
                                    1192.4
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 6789 2345",
                                "boundingBox": [
                                    2525,
                                    1185.4,
                                    3192.4,
                                    977.9,
                                    3217.9,
                                    1060,
                                    2550.5,
                                    1267.5
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "2 Kingdom Street Paddington, London, W2 6BD",
                                "text": "2 Kingdom Street Paddington, London, W2 6BD",
                                "boundingBox": [
                                    1230,
                                    2138,
                                    2535.2,
                                    1678.6,
                                    2614.2,
                                    1903.1,
                                    1309,
                                    2362.5
                                ],
                                "page": 1,
                                "confidence": 0.977
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Contoso",
                                "text": "Contoso",
                                "boundingBox": [
                                    1152,
                                    1916,
                                    2293,
                                    1552,
                                    2358,
                                    1733,
                                    1219,
                                    2105
                                ],
                                "page": 1,
                                "confidence": 0.97
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Siga o guia de início rápido do [início](./QuickStarts/client-library.md) rápido para implementar a extração de dados de cartão de visita usando Python e a API REST.

## <a name="customer-scenarios"></a>Cenários de clientes  

Os dados extraídos com a API do cartão de negócios podem ser usados para executar uma variedade de tarefas. Extrair essas informações de contato poupa automaticamente o tempo para aqueles em funções voltadas para o cliente. Veja a seguir alguns exemplos do que nossos clientes têm feito com a API do cartão de visita:

* Extraia informações de contato de cartões de visita e crie rapidamente contatos de telefone. 
* Integre com o CRM para criar contato automaticamente usando imagens de cartão de visita. 
* Mantenha o controle dos leads de vendas.  
* Extraia informações de contato em massa de imagens de cartões de visita existentes. 

A API do cartão de negócios também alimenta o [recurso de processamento do cartão de negócios do ia Builder](/ai-builder/prebuilt-business-card).

## <a name="next-steps"></a>Próximas etapas

- Siga o guia de [início rápido](./quickstarts/client-library.md) para começar a reconhecer os cartões de visita.

## <a name="see-also"></a>Confira também

* [O que é o Reconhecimento de Formulários?](./overview.md)
* [Documentos de referência da API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
