---
title: Definição de esquema de aplicativo
description: O aplicativo LUIS é representado no `.json` ou `.lu` e inclui todas as intenções, entidades, exemplos de declarações, recursos e configurações.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 08/22/2020
ms.openlocfilehash: db4fd52dad82542f20e58ebb3b8526c5be7f2f88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327316"
---
# <a name="app-schema-definition"></a>Definição de esquema de aplicativo

O aplicativo LUIS é representado no `.json` ou `.lu` e inclui todas as intenções, entidades, exemplos de declarações, recursos e configurações.

## <a name="format"></a>Formatar

Ao importar e exportar o aplicativo, escolha `.json` ou `.lu` .

|Formatar|Informações|
|--|--|
|`.json`| Formato de programação padrão|
|`.lu`|Com suporte nas [ferramentas de bot Builder](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md)da estrutura de bot.|

## <a name="version-7x"></a>Versão 7. x

* Mudando para a versão 7. x, as entidades são representadas como entidades de aprendizado de máquina aninhadas.
* Suporte para a criação de entidades de aprendizado de máquina aninhadas com `enableNestedChildren` Propriedade nas seguintes APIs de criação:
    * [Adicionar rótulo](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)
    * [Adicionar rótulo de lote](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)
    * [Examinar rótulos](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c0a)
    * [Sugerir consultas de ponto de extremidade para entidades](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2e)
    * [Sugerir consultas de ponto de extremidade para tentativas](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2d)

```json
{
  "luis_schema_version": "7.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [
  ],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

| elemento                  | Comentário                              |
|--------------------------|--------------------------------------|
| "hierarquicamente": [],     | Preterido, use [entidades de aprendizado de máquina](luis-concept-entity-types.md).   |
| "composições": [],        | Preterido, use [entidades de aprendizado de máquina](luis-concept-entity-types.md). Referência de [entidade composta](reference-entity-composite.md) . |
| "closedLists": [],       | [Listar](reference-entity-list.md) referências de entidades, usadas principalmente como recursos para entidades.    |
| "VersionId": "0,1",      | Versão de um aplicativo LUIS.|
| "Name": "example-app",   | Nome do aplicativo LUIS. |
| "desc": "",              | Descrição opcional do aplicativo LUIS.  |
| "Culture": "en-US",      | [Idioma](luis-language-support.md) do aplicativo, afeta recursos subjacentes, como entidades predefinidas, aprendizado de máquina e criador.  |
| "tokenizerVersion": "1.0.0", | [Criador](luis-language-support.md#tokenization)  |
| "patternAnyEntities": [],   | [Entidade pattern.any](reference-entity-pattern-any.md)    |
| "regex_entities": [],    |  [Entidade de expressão regular](reference-entity-regular-expression.md)   |
| "phraselists": [],       |  [Listas de frases (recurso)](luis-concept-feature.md#create-a-phrase-list-for-a-concept)   |
| "regex_features": [],    |  Preterido, use [entidades de aprendizado de máquina](luis-concept-entity-types.md). |
| "padrões": [],          |  [Padrões melhoram a precisão da previsão](luis-concept-patterns.md) com [sintaxe de padrão](reference-pattern-syntax.md)   |
| "configurações": []           | [Configurações do aplicativo](luis-reference-application-settings.md)|

## <a name="version-6x"></a>Versão 6. x

* Passando para a versão 6. x, use a nova [entidade Machine-Learning](reference-entity-machine-learned-entity.md) para representar suas entidades.

```json
{
  "luis_schema_version": "6.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-4x"></a>Versão 4. x

```json
{
  "luis_schema_version": "4.0.0",
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "intents": [
    {
      "name": "None"
    }
  ],
  "entities": [],
  "composites": [],
  "closedLists": [],
  "patternAnyEntities": [],
  "regex_entities": [],
  "prebuiltEntities": [],
  "model_features": [],
  "regex_features": [],
  "patterns": [],
  "utterances": [],
  "settings": []
}
```

## <a name="next-steps"></a>Próximas etapas

* Migrar para as [APIs de criação v3](luis-migration-authoring-entities.md)