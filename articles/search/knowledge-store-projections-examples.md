---
title: Definir projeções em um repositório de conhecimento
titleSuffix: Azure Cognitive Search
description: Exemplos de padrões comuns de como projetar documentos aprimorados na loja de conhecimento para uso com o Power BI ou o Azure ML.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3985564d49ce8a5c62b15f9537364418c0a8f5da
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509913"
---
# <a name="how-to-shape-and-export-enrichments"></a>Como formatar e exportar aprimoramentos

As projeções são a expressão física de documentos aprimorados em uma loja de conhecimento. O uso efetivo de documentos aprimorados requer estrutura. Neste artigo, você explorará a estrutura e as relações, aprenderá a criar propriedades de projeção, bem como a relacionar dados entre os tipos de projeção criados. 

Para criar uma projeção, os dados são moldados usando uma [habilidade de formador](cognitive-search-skill-shaper.md) para criar um objeto personalizado ou usando a sintaxe de modelagem embutida dentro de uma definição de projeção. 

Uma forma de dados contém todos os dados destinados ao projeto, formados como uma hierarquia de nós. Este artigo mostra várias técnicas para formatar dados para que ele possa ser projetado em estruturas físicas conduza a relatórios, análise ou processamento downstream. 

Os exemplos apresentados neste artigo podem ser encontrados neste exemplo de [API REST](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json), que você pode baixar e executar em um cliente http.

## <a name="introduction-to-projection-examples"></a>Introdução aos exemplos de projeção

Há três tipos de [projeções](knowledge-store-projection-overview.md):

+ Tabelas
+ Objetos
+ Arquivos

As projeções de tabela são armazenadas no armazenamento de tabelas do Azure. As projeções de objeto e arquivo são gravadas no armazenamento de BLOBs, em que as projeções de objeto são salvas como arquivos JSON e podem conter conteúdo do documento de origem, bem como saídas ou aprimoramentos de habilidades. O pipeline de enriquecimento também pode extrair binários como imagens, esses binários são projetados como projeções de arquivo. Quando um objeto binário é projetado como uma projeção de objeto, somente os metadados associados a ele são salvos como um blob JSON. 

Para entender a interseção entre a modelagem de dados e as projeções, usaremos o seguinte qualificable como base para explorar várias configurações. Esse qualificable processa conteúdo bruto de imagem e texto. As projeções serão definidas a partir do conteúdo do documento e das saídas das habilidades para os cenários desejados.

> [!IMPORTANT] 
> Ao experimentar projeções, é útil [definir a propriedade cache do indexador](search-howto-incremental-index.md) para garantir o controle de custos. A edição das projeções fará com que todo o documento seja aprimorado novamente se o cache do indexador não estiver definido. Quando o cache é definido e apenas as projeções são atualizadas, as execuções do conjunto de qualificações para documentos previamente aprimorados não resultam em novos encargos de serviços cognitivas.

```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

Usando esse Configurador, com seu nulo `knowledgeStore` como a base, nosso primeiro exemplo preenche o `knowledgeStore` objeto, configurado com projeções que criam estruturas de dados de tabela que podemos usar em outros cenários. 

## <a name="projecting-to-tables"></a>Projeção para tabelas

A projeção para tabelas no armazenamento do Azure é útil para relatórios e análises usando ferramentas como Power BI. Power BI pode ler de tabelas e descobrir relações com base nas chaves que são geradas durante a projeção. Se você estiver tentando criar um painel, ter dados relacionados simplificará essa tarefa. 

Vamos criar um painel para visualizar as frases-chave extraídas dos documentos como uma palavra em nuvem. Para criar a estrutura de dados correta, adicione uma habilidade de forma do condador para criar uma forma personalizada que tenha detalhes específicos do documento e frases-chave. A forma personalizada será chamada `pbiShape` no `document` nó raiz.

> [!NOTE] 
> As projeções de tabela são tabelas de armazenamento do Azure, governadas pelos limites de armazenamento impostos pelo armazenamento do Azure. Para obter mais informações, consulte [limites de armazenamento de tabelas](/rest/api/storageservices/understanding-the-table-service-data-model). É útil saber que o tamanho da entidade não pode exceder 1 MB e uma única propriedade não pode ser maior que 64 KB. Essas restrições tornam as tabelas uma boa solução para armazenar um grande número de pequenas entidades.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Usando uma habilidade de formador para criar uma forma personalizada

Crie uma forma personalizada que você pode projetar no armazenamento de tabelas. Sem uma forma personalizada, uma projeção só pode fazer referência a um único nó (uma projeção por saída). A criação de uma forma personalizada agrega vários elementos em um novo lógico inteiro que pode ser projetado como uma única tabela ou segmentado e distribuído por uma coleção de tabelas. 

Neste exemplo, a forma personalizada combina metadados e entidades identificadas e frases-chave. O objeto é chamado `pbiShape` e é pai em `/document` . 

> [!IMPORTANT] 
> Uma finalidade de Formatar é garantir que todos os nós de enriquecimento sejam expressos em JSON bem formado, o que é necessário para projetar na loja de conhecimento. Isso é especialmente verdadeiro quando uma árvore de enriquecimento contém nós que não são JSON bem formados (por exemplo, quando um enriquecimento é pai de um primitivo como uma cadeia de caracteres).
>
> Observe os dois últimos nós `KeyPhrases` e `Entities` . Eles são encapsulados em um objeto JSON válido com o `sourceContext` . Isso é necessário como `keyphrases` e `entities` são aprimoramentos em primitivos e precisam ser convertidos em JSON válido antes que possam ser projetados.
>


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "pbiShape"
        }
    ]
}
```

Adicione a habilidade de Modelador acima ao skillset. 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

Agora que temos todos os dados necessários para projetar em tabelas, atualize o objeto knowledgeStore com as definições de tabela. Neste exemplo, temos três tabelas, definidas definindo as `tableName` `source` `generatedKeyName` Propriedades e.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

Você pode processar seu trabalho seguindo estas etapas:

1. Defina a ```storageConnectionString``` propriedade como uma cadeia de conexão de conta de armazenamento de uso geral v2 válida.  

1. Atualize o configurador emitindo a solicitação PUT.

1. Depois de atualizar o skillset, execute o indexador. 

Agora você tem uma projeção de trabalho com três tabelas. A importação dessas tabelas para Power BI deve resultar na Power BI descoberta automática das relações.

Antes de passar para o próximo exemplo, Vamos revisitar os aspectos da projeção da tabela para entender a mecânica de fatiar e relacionar os dados.

### <a name="slicing"></a>Divisão 

A divisão é uma técnica que subdivide uma forma consolidada inteira em partes constituintes. O resultado consiste em tabelas separadas, mas relacionadas com as quais você pode trabalhar individualmente.

No exemplo, `pbiShape` é a forma consolidada (ou nó de enriquecimento). Na definição de projeção, `pbiShape` é segmentada em tabelas adicionais, o que permite que você retire partes da forma ```keyPhrases``` e ```Entities``` . No Power BI, isso é útil, pois várias entidades e keyfrases são associadas a cada documento, e você obterá mais informações se puder ver entidades e keyfrases como dados categorizados.

A divisão gera implicitamente uma relação entre as tabelas pai e filho, usando o ```generatedKeyName``` na tabela pai para criar uma coluna com o mesmo nome na tabela filho. 

### <a name="naming-relationships"></a>Relações de nomenclatura

As ```generatedKeyName``` ```referenceKeyName``` Propriedades e são usadas para relacionar dados entre tabelas ou até mesmo entre tipos de projeção. Cada linha na tabela/projeção filho tem uma propriedade apontando de volta para o pai. O nome da coluna ou propriedade no filho é o ```referenceKeyName``` do pai. Quando o ```referenceKeyName``` não é fornecido, o serviço o padroniza para o ```generatedKeyName``` do pai. 

Power BI conta com essas chaves geradas para descobrir relações dentro das tabelas. Se você precisar da coluna na tabela filho denominada de modo diferente, defina a ```referenceKeyName``` Propriedade na tabela pai. Um exemplo seria definir a ID de as ```generatedKeyName``` na tabela pbiDocument e ```referenceKeyName``` como DocumentID. Isso resultaria na coluna nas tabelas pbiEntities e pbiKeyPhrases que contêm a ID do documento que está sendo nomeada DocumentID.

## <a name="projecting-to-objects"></a>Projeção para objetos

As projeções de objeto não têm as mesmas limitações que as projeções de tabela e são mais adequadas para a projeção de documentos grandes. Neste exemplo, o documento inteiro é enviado como uma projeção de objeto. As projeções de objeto são limitadas a uma única projeção em um contêiner e não podem ser segmentadas.

Para definir uma projeção de objeto, use a ```objects``` matriz nas projeções. Você pode gerar uma nova forma usando a habilidade do modelador ou usar o Shaping embutido da projeção do objeto. Embora o exemplo de tabelas tenha demonstrado a abordagem de criação de uma forma e de divisão, este exemplo demonstra o uso de shaping embutido. 

O Shaping embutido é a capacidade de criar uma nova forma na definição das entradas para uma projeção. A modelagem embutida cria um objeto anônimo que é idêntico ao que a habilidade de um Shaper produziria (em nosso caso, `pbiShape` ). O Shaping embutido é útil se você estiver definindo uma forma que não planeja reutilizar.

A propriedade projetions é uma matriz. Este exemplo adiciona uma nova instância de projeção à matriz, em que a definição de knowledgeStore contém projeções embutidas. Ao usar projeções embutidas, você pode omitir a habilidade do modelador.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [ ],
                "objects": [
                    {
                        "storageContainer": "sampleobject",
                        "source": null,
                        "generatedKeyName": "myobject",
                        "sourceContext": "/document",
                        "inputs": [
                            {
                                "name": "metadata_storage_name",
                                "source": "/document/metadata_storage_name"
                            },
                            {
                                "name": "metadata_storage_path",
                                "source": "/document/metadata_storage_path"
                            },
                            {
                                "name": "content",
                                "source": "/document/content"
                            },
                            {
                                "name": "keyPhrases",
                                "source": "/document/merged_content/keyphrases/*"
                            },
                            {
                                "name": "entities",
                                "source": "/document/merged_content/entities/*/name"
                            },
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            },
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]

                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="projecting-to-file"></a>Projeção para arquivo

Projeções de arquivos são imagens que são extraídas do documento de origem ou saídas de enriquecimento que podem ser projetadas fora do processo de enriquecimento. As projeções de arquivo, semelhantes às projeções de objeto, são implementadas como BLOBs no armazenamento do Azure e contêm a imagem. 

Para gerar uma projeção de arquivo, use a `files` matriz no objeto de projeção. Este exemplo projeta todas as imagens extraídas do documento para um contêiner chamado `samplefile` .

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>Projeção para vários tipos

Um cenário mais complexo pode exigir que você projeto o conteúdo entre os tipos de projeção. Por exemplo, se você precisar projetar alguns dados como frases-chave e entidades para tabelas, salve os resultados do OCR de texto e de layout como objetos e, em seguida, projetar as imagens como arquivos. 

Este exemplo atualiza o skillset com as seguintes alterações:

1. Crie uma tabela com uma linha para cada documento.
1. Crie uma tabela relacionada à tabela de documentos com cada frase-chave identificada como uma linha nesta tabela.
1. Crie uma tabela relacionada à tabela de documentos com cada entidade identificada como uma linha nesta tabela.
1. Crie uma projeção de objeto com o texto de layout para cada imagem.
1. Crie uma projeção de arquivo, projetando cada imagem extraída.
1. Crie uma tabela de referência cruzada que contenha referências à tabela de documentos, projeção de objeto com o texto de layout e a projeção de arquivo.

Essas alterações são refletidas na definição de knowledgeStore mais adiante. 

### <a name="shape-data-for-cross-projection"></a>Formatar dados para projeção cruzada

Para obter as formas necessárias para essas projeções, comece adicionando uma nova habilidade de forma que cria um objeto moldado chamado `crossProjection` . 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCross",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>Definir projeções de tabela, objeto e arquivo

A partir do objeto crossProjection consolidado, divida o objeto em várias tabelas, Capture a saída do OCR como BLOBs e salve a imagem como arquivos (também no armazenamento de BLOBs).

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [
                    {
                        "tableName": "crossDocument",
                        "generatedKeyName": "Id",
                        "source": "/document/crossProjection"
                    },
                    {
                        "tableName": "crossEntities",
                        "generatedKeyName": "EntityId",
                        "source": "/document/crossProjection/entities/*"
                    },
                    {
                        "tableName": "crossKeyPhrases",
                        "generatedKeyName": "KeyPhraseId",
                        "source": "/document/crossProjection/keyPhrases/*"
                    },
                    {
                        "tableName": "crossReference",
                        "generatedKeyName": "CrossId",
                        "source": "/document/crossProjection/images/*"
                    }
                     
                ],
                "objects": [
                    {
                        "storageContainer": "crossobject",
                        "generatedKeyName": "crosslayout",
                        "source": null,
                        "sourceContext": "/document/crossProjection/images/*/layoutText",
                        "inputs": [
                            {
                                "name": "OcrLayoutText",
                                "source": "/document/crossProjection/images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": [
                    {
                        "storageContainer": "crossimages",
                        "generatedKeyName": "crossimages",
                        "source": "/document/crossProjection/images/*/image"
                    }
                ]
            }
        ]
    }
```

As projeções de objeto exigem um nome de contêiner para cada projeção, projeções de objeto ou projeções de arquivo não podem compartilhar um contêiner. 

### <a name="relationships-among-table-object-and-file-projections"></a>Relações entre tabelas, objetos e projeções de arquivo

Este exemplo também destaca outro recurso de projeções. Ao definir vários tipos de projeções dentro do mesmo objeto de projeção, há uma relação expressa dentro e entre os diferentes tipos (tabelas, objetos, arquivos). Isso permite que você comece com uma linha de tabela para um documento e localize todo o texto OCR das imagens dentro desse documento na projeção do objeto. 

Se você não quiser os dados relacionados, defina as projeções em diferentes objetos de projeção. Por exemplo, o trecho a seguir fará com que as tabelas estejam relacionadas, mas sem relações entre as tabelas e as projeções de objeto (texto OCR). 

Os grupos de projeção são úteis quando você deseja projetar os mesmos dados em diferentes formas para diferentes necessidades. Por exemplo, um grupo de projeção para o painel de Power BI e outro grupo de projeção para capturar dados usados para treinar um modelo de aprendizado de máquina encapsulado em uma habilidade personalizada.

Ao criar projeções de tipos diferentes, as projeções de arquivo e de objeto são geradas primeiro e os caminhos são adicionados às tabelas.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
                    }
                ],
                "objects": [
                    
                ],
                "files": []
            }, 
            {
                "tables": [],
                "objects": [
                    {
                        "storageContainer": "unrelatedocrtext",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/text",
                        "inputs": [
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            }
                        ]
                    },
                    {
                        "storageContainer": "unrelatedocrlayout",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/layoutText",
                        "inputs": [
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="common-issues"></a>Problemas comuns

Ao definir uma projeção, há alguns problemas comuns que podem causar resultados imprevistos. Verifique esses problemas se a saída na loja de conhecimento não for o que você espera.

+ Não moldar os aprimoramentos de cadeia de caracteres em um JSON válido. Quando as cadeias de caracteres são aprimoradas, por exemplo, `merged_content` aprimoradas com frases-chave, a propriedade enriqueceda é representada como um filho de `merged_content` dentro da árvore de enriquecimento. A representação padrão não é JSON bem formado. Portanto, no momento da projeção, certifique-se de transformar o enriquecimento em um objeto JSON válido com um nome e um valor.

+ Omitindo o ```/*``` ao final de um caminho de origem. Se a origem de uma projeção for `/document/pbiShape/keyPhrases` , a matriz de frases-chave será projetada como um único objeto/linha. Em vez disso, defina o caminho de origem como `/document/pbiShape/keyPhrases/*` para gerar uma única linha ou objeto para cada uma das frases-chave.

+ Erros de sintaxe de caminho. Os seletores de caminho diferenciam maiúsculas de minúsculas e podem levar a avisos de entrada ausentes se você não usar o caso exato para o seletor.

## <a name="next-steps"></a>Próximas etapas

Os exemplos neste artigo demonstram padrões comuns de como criar projeções. Agora que você tem uma boa compreensão dos conceitos, está mais bem equipado para criar projeções para seu cenário específico.

Ao explorar novos recursos, considere o aprimoramento incremental como a próxima etapa. O aprimoramento incremental se baseia no cache, o que permite reutilizar os aprimoramentos que não são afetados de outra forma por uma modificação do Configurador de habilidades. Isso é especialmente útil para pipelines que incluem a análise de OCR e imagem.

> [!div class="nextstepaction"]
> [Introdução ao Caching e enriquecimento incremental](cognitive-search-incremental-indexing-conceptual.md)

Para obter uma visão geral das projeções, saiba mais sobre os recursos como grupos e fatias e como [defini-los em um](knowledge-store-projection-overview.md) tipo de habilidade

> [!div class="nextstepaction"]
> [Projeções em um repositório de conhecimento](knowledge-store-projection-overview.md)