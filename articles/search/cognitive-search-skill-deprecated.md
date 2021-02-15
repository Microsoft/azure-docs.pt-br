---
title: Habilidades cognitivas preteridas
titleSuffix: Azure Cognitive Search
description: Esta página contém uma lista de habilidades cognitivas que são consideradas preteridas e que não terão suporte em um futuro próximo no Azure Pesquisa Cognitiva habilidades.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 85f3b9862bd8155c1a4b11860dc82d92a2f9e810
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88936088"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Habilidades cognitivas preteridas no Azure Pesquisa Cognitiva

Este documento descreve as habilidades cognitivas que são consideradas obsoletas. Use o seguinte guia para o conteúdo:

* Nome da Habilidade: O nome da habilidade que será descontinuada, mapeia para o atributo @odata.type.
* Última versão da API disponível: a última versão da API pública do Azure Pesquisa Cognitiva por meio da qual habilidades que contém as habilidades preteridas correspondentes pode ser criada/atualizada.
* Fim do suporte: O último dia após o qual a habilidade correspondente é considerada sem suporte. Os conjuntos de habilidades criados anteriormente ainda devem continuar funcionando, mas recomenda-se que os usuários migrem de uma habilidade obsoleta.
* Recomendações: caminho de migração para usar uma habilidade suportada. Os usuários são aconselhados a seguir as recomendações para continuar recebendo suporte.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Última versão disponível do api

2017-11-11-Versão prévia

### <a name="end-of-support"></a>Fim do suporte

15 de fevereiro de 2019

### <a name="recommendations"></a>Recomendações 

Use [ Microsoft.Skills.Text.EntityRecognitionSkill ](cognitive-search-skill-entity-recognition.md). Ele fornece a maior parte da funcionalidade do NamedEntityRecognitionSkill em uma qualidade superior. Também possui informações mais completas em seus campos de saída complexos.

Para migrar para a [Habilidade de Reconhecimento de Entidade](cognitive-search-skill-entity-recognition.md), você terá que executar uma ou mais das seguintes alterações na sua definição de habilidade. Você pode atualizar a definição de habilidade usando a [API Update Skillset](/rest/api/searchservice/update-skillset).

> [!NOTE]
> Atualmente, não há suporte para a pontuação de confiança como um conceito. O `minimumPrecision` parâmetro existe no `EntityRecognitionSkill` para uso futuro e para compatibilidade com versões anteriores.

1. *(Obrigatório)* Altere o `@odata.type` de `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` para `"#Microsoft.Skills.Text.EntityRecognitionSkill"`.

2. *(Opcional) * Se você estiver fazendo uso do `entities` de saída, use o `namedEntities` saída de coleção complexa do `EntityRecognitionSkill` em vez disso. Você pode usar o `targetName` na definição de habilidade para mapeá-lo para uma anotação chamada `entities`.

3. *(Opcional)* Se você não especificar explicitamente o `categories`, o `EntityRecognitionSkill` poderá retornar diferentes tipos de categorias além daquelas que foram suportadas pelo `NamedEntityRecognitionSkill`. Se esse comportamento for indesejável, certifique-se de definir explicitamente o `categories` parâmetro para `["Person", "Location", "Organization"]`.

    _ Exemplos de definições de migração _

    * Migração simples

        _(Before) NomeouEntityRecognition, definição de habilidade_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
        _(Depois) Definição de habilidades EntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
    
    * Um pouco complicado de migração

        _(Before) NomeouEntityRecognition, definição de habilidade_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "entities"
            }
            ]
        }
        ```
        _(Depois) Definição de habilidades EntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "namedEntities",
                "targetName": "entities"
            }
            ]
        }
        ```

## <a name="see-also"></a>Confira também

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Habilidade de reconhecimento de entidade](cognitive-search-skill-entity-recognition.md)