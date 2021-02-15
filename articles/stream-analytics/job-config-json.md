---
title: Azure Stream Analytics JobConfig.jsem campos
description: Este artigo lista os campos com suporte para o Azure Stream Analytics JobConfig.jsno arquivo usado para criar trabalhos no Visual Studio Code.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: 0eebd0b62e973572a40b7b141ae908046700ba3c
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020478"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure Stream Analytics JobConfig.jsem campos

Os campos a seguir têm suporte no *JobConfig.jsno* arquivo usado para [criar um trabalho de Azure Stream Analytics usando Visual Studio Code](quick-create-visual-studio-code.md).

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|Nome|Type|Obrigatório|Valor|
|----|----|--------|-----|
|Datalocale|Cadeia de caracteres|No|A localidade de dados do trabalho do Stream Analytics. O valor deve ser o nome de um com suporte. O padrão é "en-US" se não houver nenhum especificado.|
|OutputErrorPolicy|Cadeia de caracteres|No|Indica a política a ser aplicada aos eventos que chegam à saída e não pode ser gravada no armazenamento externo devido a estar malformado (faltam valores de coluna, valores de coluna de tipo ou tamanho incorreto). -Parar ou descartar|
|EventsLateArrivalMaxDelayInSeconds|inteiro|Não|O atraso máximo tolerável em segundos em que os eventos chegando ao final podem ser incluídos. O intervalo com suporte é de-1 a 1814399 (20.23:59:59 dias) e-1 é usado para especificar a espera indefinidamente. Se a propriedade estiver ausente, ela será interpretada para ter um valor de-1.|
|EventsOutOfOrderMaxDelayInSeconds|inteiro|Não|O atraso máximo tolerável em segundos em que os eventos fora de ordem podem ser ajustados para volta na ordem.|
|EventsOutOfOrderPolicy|Cadeia de caracteres|No|Indica a política a ser aplicada aos eventos que chegam fora de ordem no fluxo de eventos de entrada. -Ajustar ou descartar|
|StreamingUnits|inteiro|Yes|Especifica o número de unidades de streaming que o trabalho de streaming usa.|
|CompatibilityLevel|Cadeia de caracteres|No|Controla determinados comportamentos de tempo de execução do trabalho de streaming. -Os valores aceitáveis são "1,0", "1,1", "1,2"|
|UseSystemAssignedIdentity|booleano|Não|Defina true para permitir que esse trabalho se comunique com outros serviços do Azure usando uma identidade de Azure Active Directory gerenciada.|
|GlobalStorage. AccountName|Cadeia de caracteres|No|A conta de armazenamento global é usada para armazenar o conteúdo relacionado ao trabalho do Stream Analytics, como instantâneos de dados de referência do SQL.|
|GlobalStorage. AccountKey|Cadeia de caracteres|No|Chave correspondente para a conta de armazenamento global.|
|DataSourceCredentialDomain|Cadeia de caracteres|No|Propriedade reservada para armazenamento local de credencial.|
|ScriptType|string|Yes|Propriedade reservada para indicar o tipo desse arquivo de origem. O valor aceitável é "JobConfig" para JobConfig.js.|
|Marcações|Pares chave-valor JSON|Não|Marcas são pares nome/valor que permitem categorizar recursos e exibir a cobrança consolidada por meio da aplicação da mesma marca a vários recursos e grupos de recursos. Os nomes de marca diferenciam maiúsculas de minúsculas e os valores de marca diferenciam maiúsculas de minúsculas.|

## <a name="next-steps"></a>Próximas etapas

* [Criar um trabalho de Azure Stream Analytics no Visual Studio Code](quick-create-visual-studio-code.md)
* [Testar consultas do Stream Analytics localmente com os dados de exemplo usando o Visual Studio Code](visual-studio-code-local-run.md)
* [Testar Stream Analytics consultas localmente contra entrada de fluxo ao vivo usando Visual Studio Code](visual-studio-code-local-run-live-input.md) 
* [Implantar um trabalho de Azure Stream Analytics usando o pacote NPM de CI/CD](./cicd-overview.md)