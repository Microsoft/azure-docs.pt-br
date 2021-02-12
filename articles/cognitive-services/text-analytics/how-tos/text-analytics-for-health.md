---
title: Como usar Análise de Texto para integridade
titleSuffix: Azure Cognitive Services
description: Saiba como extrair e rotular informações médicas de textos clínicos não estruturados com o Análise de Texto para fins de integridade.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: f7ba6363ec3a38d37ea3df0f76409289069638e8
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537789"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Como usar Análise de Texto para integridade (versão prévia)

> [!IMPORTANT] 
> O Análise de Texto for Health é uma funcionalidade de visualização fornecida "no estado em que se encontra" e "com todas as falhas". Dessa forma, o **análise de texto para integridade (versão prévia) não deve ser implementado ou implantado em nenhum uso de produção.** A Análise de Texto para a integridade não é pretendida ou disponibilizada para uso como um dispositivo médico, suporte clínico, ferramenta de diagnóstico ou outra tecnologia destinada a ser usada no diagnóstico, remediação, redução, tratamento ou prevenção de doenças ou outras condições, e nenhuma licença ou direito é concedido pela Microsoft para usar esse recurso para tais finalidades. Essa funcionalidade não foi projetada ou destinada a ser implementada ou implantada como um substituto para consultoria Médica profissional ou opinião de saúde, diagnóstico, tratamento ou julgamento clínico de um profissional de saúde, e não deve ser usada como tal. O cliente é exclusivamente responsável por qualquer uso de Análise de Texto para fins de integridade. A Microsoft não garante que Análise de Texto de saúde ou qualquer material fornecido em conexão com a capacidade seja suficiente para qualquer finalidade médica ou atenda aos requisitos de saúde ou médicos de qualquer pessoa. 


O Análise de Texto for Health é um recurso do serviço de API de Análise de Texto que extrai e rotula informações médicas relevantes de textos não estruturados, como notas do médico, resumos de descarga, documentos clínicos e registros eletrônicos de saúde.  Há duas maneiras de utilizar esse serviço: 

* [A API baseada na Web (assíncrona)](#structure-the-api-request-for-the-hosted-asynchronous-web-api)
* [Um contêiner do Docker (síncrono)](#hosted-asynchronous-web-api-response)   

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>Recursos

O Análise de Texto for Health realiza o reconhecimento de entidade nomeada (NER), extração de relações, negação de entidade e vinculação de entidade em texto em idioma Inglês para revelar informações em texto clínico e biomédico não estruturado.

### <a name="named-entity-recognition"></a>[Reconhecimento de entidade nomeada](#tab/ner)

O reconhecimento de entidade nomeada detecta palavras e frases mencionadas em texto não estruturado que pode ser associado a um ou mais tipos semânticos, como diagnóstico, nome do medicamentos, sintoma/sinal ou idade.

> [!div class="mx-imgBorder"]
> ![NER de integridade](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[Extração de relações](#tab/relation-extraction)

A extração de relações identifica conexões significativas entre os conceitos mencionados no texto. Por exemplo, uma relação "hora da condição" é encontrada pela Associação de um nome de condição com um horário. 

> [!div class="mx-imgBorder"]
> ![Renovar integridade](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Vinculação de Identidade](#tab/entity-linking)

A vinculação de entidade ambiguidade entidades distintas associando entidades nomeadas mencionadas em texto a conceitos encontrados em um banco de dados predefinido de conceitos. Por exemplo, o sistema de idiomas médicos unificados (UMLS).

> [!div class="mx-imgBorder"]
> ![Integridade EL](../media/ta-for-health/health-entity-linking.png)

O Análise de Texto para integridade dá suporte à vinculação com os vocabulários de integridade e biomédica encontrados na fonte de conhecimento do metadicionário de[UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)(Unified Medical Language System).

### <a name="negation-detection"></a>[Detecção de negação](#tab/negation-detection) 

O significado do conteúdo médico é altamente afetado por modificadores como negação, o que pode ter implicação crítica se for diagnosticado. O Análise de Texto para integridade dá suporte à detecção de negação para as diferentes entidades mencionadas no texto. 

> [!div class="mx-imgBorder"]
> ![NEG de integridade](../media/ta-for-health/health-negation.png)

---

Consulte as [categorias de entidade](../named-entity-types.md?tabs=health) retornadas por análise de texto para integridade para obter uma lista completa de entidades com suporte. Para obter informações sobre pontuações de confiança, consulte a [análise de texto a nota de transparência](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context). 

### <a name="supported-languages-and-regions"></a>Idiomas e regiões com suporte

O Análise de Texto para fins de integridade só dá suporte a documentos de idioma inglês. 

O Análise de Texto para a API web hospedada de integridade está disponível atualmente somente nestas regiões: oeste dos EUA 2, leste dos EUA 2, EUA Central, Europa Setentrional e Europa Ocidental.

## <a name="request-access-to-the-public-preview"></a>Solicitar acesso à visualização pública

Preencha e envie o [formulário de solicitação de serviços cognitivas](https://aka.ms/csgate) para solicitar acesso ao análise de texto para visualização pública de integridade. Você não será cobrado por Análise de Texto para uso de integridade. 

O formulário solicita informações sobre você, sua empresa e o cenário do usuário para o qual você usará o contêiner. Depois de enviar o formulário, a equipe de serviços cognitivas do Azure irá examiná-lo e enviar uma decisão por email.

> [!IMPORTANT]
> * No formulário, você deve usar um endereço de email associado a uma ID de assinatura do Azure.
> * O recurso do Azure que você usa deve ter sido criado com a ID de assinatura aprovada do Azure. 
> * Verifique seu email (pastas de caixa de entrada e lixo eletrônico) para obter atualizações sobre o status do seu aplicativo da Microsoft.

## <a name="using-the-docker-container"></a>Usando o contêiner do Docker 

Para executar o Análise de Texto para o contêiner de integridade em seu próprio ambiente, siga estas [instruções para baixar e instalar o contêiner](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare).

## <a name="using-the-client-library"></a>Como usar a biblioteca de clientes

A versão mais recente da biblioteca de cliente do Análise de Texto permite que você chame Análise de Texto para a integridade usando um objeto de cliente. Consulte a documentação de referência e veja os exemplos no GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>Como enviar uma solicitação da API REST 

### <a name="preparation"></a>Preparação

Análise de Texto para integridade produz um resultado de qualidade mais alta quando você dá a ele quantidades menores de texto para trabalhar. Isso é oposto a alguns dos outros Análise de Texto recursos, como a extração de frases-chave, que tem um desempenho melhor em blocos de texto maiores. Para obter os melhores resultados dessas operações, considere reestruturar as entradas de acordo.

Você deve ter documentos JSON neste formato: ID, texto e idioma. 

O tamanho do documento deve ser inferior a 5.120 caracteres. Para obter o número máximo de documentos permitidos em uma coleção, confira o artigo [Limites de dados](../concepts/data-limits.md?tabs=version-3) em Conceitos. A coleção é enviada no corpo da solicitação.

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>Estruturar a solicitação de API para a API Web assíncrona hospedada

Para o contêiner e a API web hospedada, você deve criar uma solicitação POST. Você pode [usar o postmaster](text-analytics-how-to-call-api.md), um comando de ondulação ou o **console de teste de API** no [análise de texto para a referência de API hospedada de integridade](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) para construir e enviar rapidamente uma solicitação post para a API web hospedada na região desejada. 

> [!NOTE]
> Os pontos assíncronos `/analyze` e de `/health` extremidade só estão disponíveis nas seguintes regiões: oeste dos EUA 2, leste dos EUA 2, EUA Central, Europa Setentrional e Europa Ocidental.  Para fazer solicitações bem-sucedidas a esses pontos de extremidade, verifique se o recurso foi criado em uma dessas regiões.

Abaixo está um exemplo de um arquivo JSON anexado à Análise de Texto para o corpo da solicitação da API de integridade:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

### <a name="hosted-asynchronous-web-api-response"></a>Resposta da API Web assíncrona hospedada 

Como essa solicitação POST é usada para enviar um trabalho para a operação assíncrona, não há nenhum texto no objeto de resposta.  No entanto, você precisa do valor da chave de local de operação nos cabeçalhos de resposta para fazer uma solicitação GET para verificar o status do trabalho e a saída.  Abaixo está um exemplo do valor da chave de local de operação no cabeçalho de resposta da solicitação POST:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/health/jobs/<jobID>`

Para verificar o status do trabalho, faça uma solicitação GET para a URL no valor do cabeçalho da chave da operação de localização da resposta POST.  Os seguintes Estados são usados para refletir o status de um trabalho:,,,,, `NotStarted` `running` `succeeded` `failed` `rejected` `cancelling` e `cancelled` .  

Você pode cancelar um trabalho com um `NotStarted` `running` status ou com uma chamada http Delete para a mesma URL que a solicitação get.  Mais informações sobre a chamada de exclusão estão disponíveis no [análise de texto para referência de API hospedada de integridade](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob).

Veja a seguir um exemplo da resposta de uma solicitação GET.  Observe que a saída está disponível para recuperação até que o `expirationDateTime` (24 horas a partir do momento em que o trabalho foi criado) tenha passado após o qual a saída é limpa.

```json
{
    "jobId": "b672c6f5-7c0d-4783-ba8c-4d0c47213454",
    "lastUpdateDateTime": "2020-11-18T01:45:00Z",
    "createdDateTime": "2020-11-18T01:44:55Z",
    "expirationDateTime": "2020-11-19T01:44:55Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "isNegated": false,
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 56,
                        "length": 4,
                        "text": "over",
                        "category": "Time",
                        "confidenceScore": 0.87,
                        "isNegated": false
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.99,
                        "isNegated": false
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/0",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/2",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/3",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/4",
                        "target": "#/results/documents/0/entities/1"
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2020-09-03"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>Estruturar a solicitação de API para o contêiner

Você pode [usar o postmaster](text-analytics-how-to-call-api.md) ou a solicitação de ondulação de exemplo abaixo para enviar uma consulta para o contêiner que você implantou, substituindo a `serverURL` variável pelo valor apropriado.  Observe que a versão da API na URL para o contêiner é diferente da API hospedada.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

O JSON a seguir é um exemplo de um arquivo JSON anexado à Análise de Texto para o corpo do POST da solicitação de API de integridade:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>Corpo da resposta do contêiner

O JSON a seguir é um exemplo da Análise de Texto para o corpo de resposta da API de integridade da chamada síncrona em contêineres:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "3",
                    "offset": 56,
                    "length": 4,
                    "text": "over",
                    "category": "Time",
                    "confidenceScore": 0.87,
                    "isNegated": false
                },
                {
                    "id": "4",
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.99,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/0",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "RouteOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/2",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/3",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/4",
                    "target": "#/documents/0/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-09-03"
}
```

### <a name="negation-detection-output"></a>Saída de detecção de negação

Ao usar a detecção de negação, em alguns casos um único termo de negação pode abordar vários termos ao mesmo tempo. A negação de uma entidade reconhecida é representada na saída JSON pelo valor booliano do `isNegated` sinalizador, por exemplo:

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>Saída de extração de relação

A saída de extração de relação contém referências de URI para a *origem* da relação e seu *destino*. As entidades com a função de relação de `ENTITY` são atribuídas ao `target` campo. As entidades com a função de relação de `ATTRIBUTE` são atribuídas ao `source` campo. As relações de abreviação contêm bidirecional `source` e `target` campos e `bidirectional` serão definidas como `true` . 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>Confira também

* [Visão geral da Análise de Texto](../overview.md)
* [Categorias de entidade nomeada](../named-entity-types.md)
* [Novidades](../whats-new.md)
