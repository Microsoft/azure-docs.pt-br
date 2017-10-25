---
title: "Esquema de linguagem de definição de fluxo de trabalho - Aplicativo Lógico do Azure | Microsoft Docs"
description: "Define fluxos de trabalho com base no esquema de definição de fluxo de trabalho para Aplicativos Lógico do Azure"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 6befc5b26f2b01113f1aa813125b33eb66ad6f6a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="workflow-definition-language-schema-for-azure-logic-apps"></a>Esquema de linguagem de definição de fluxo de trabalho para Aplicativo Lógico do Azure

Uma definição de fluxo de trabalho contém a lógica real que é executada como parte do seu aplicativo lógico. Essa definição inclui um ou mais gatilhos que iniciam o aplicativo lógico e uma ou mais ações a serem executadas pelo aplicativo lógico.  
  
## <a name="basic-workflow-definition-structure"></a>Estrutura da definição de fluxo de trabalho básico

Aqui está a estrutura básica de uma definição de fluxo de trabalho:  
  
```json
{
    "$schema": "<schema-of the-definition>",
    "contentVersion": "<version-number-of-definition>",
    "parameters": { <parameter-definitions-of-definition> },
    "triggers": [ { <definition-of-flow-triggers> } ],
    "actions": [ { <definition-of-flow-actions> } ],
    "outputs": { <output-of-definition> }
}
```
  
> [!NOTE]
> A documentação da [API REST de Gerenciamento de Fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflows) contém informações sobre como criar e gerenciar fluxos de trabalho de aplicativo lógico.
  
|Nome do elemento|Obrigatório|Descrição|  
|------------------|--------------|-----------------|  
|$schema|Não|Especifica o local para o arquivo de esquema JSON que descreve a versão da linguagem de definição. Esse local é necessário quando você faz referência a uma definição externamente. Para este documento, o local é: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#`|  
|contentVersion|Não|Especifica a versão de definição. Quando você implanta um fluxo de trabalho usando a definição, você pode usar esse valor para certificar-se de que a definição correta é usada.|  
|parameters|Não|Especifica os parâmetros usados para inserir os dados na definição. Podem ser definidos no máximo 50 parâmetros.|  
|gatilhos|Não|Especifica informações sobre os gatilhos que iniciam o fluxo de trabalho. Podem ser definidos no máximo 10 gatilhos.|  
|Ações|Não|Especifica as ações executadas durante a execução do fluxo. Podem ser definidas no máximo 250 ações.|  
|outputs|Não|Especifica as informações sobre o recurso implantado. Podem ser definidas no máximo 10 saídas.|  
  
## <a name="parameters"></a>parâmetros

Esta seção especifica todos os parâmetros que são usados na definição de fluxo de trabalho no momento da implantação. Todos os parâmetros devem ser declarados nesta seção antes que eles possam ser usados em outras seções da definição.  
  
O exemplo a seguir mostra a estrutura de uma definição de parâmetro:  

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": <default-value-of-parameter>,
        "allowedValues": [ <array-of-allowed-values> ],
        "metadata" : { "key": { "name": "value"} }
    }
}
```

|Nome do elemento|Obrigatório|Descrição|  
|------------------|--------------|-----------------|  
|type|Sim|**Tipo**: string <p> **Declaração**: `"parameters": {"parameter1": {"type": "string"}` <p> **Especificação**: `"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **Tipo**: securestring <p> **Declaração**: `"parameters": {"parameter1": {"type": "securestring"}}` <p> **Especificação**: `"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **Tipo**: int <p> **Declaração**: `"parameters": {"parameter1": {"type": "int"}}` <p> **Especificação**: `"parameters": {"parameter1": {"value" : 5}}` <p> **Tipo**: bool <p> **Declaração**: `"parameters": {"parameter1": {"type": "bool"}}` <p> **Especificação**: `"parameters": {"parameter1": { "value": true }}` <p> **Tipo**: array <p> **Declaração**: `"parameters": {"parameter1": {"type": "array"}}` <p> **Especificação**: `"parameters": {"parameter1": { "value": [ array-of-values ]}}` <p> **Tipo**: object <p> **Declaração**: `"parameters": {"parameter1": {"type": "object"}}` <p> **Especificação**: `"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **Tipo**: secureobject <p> **Declaração**: `"parameters": {"parameter1": {"type": "object"}}` <p> **Especificação**: `"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **Observação:** Os tipos `securestring` e `secureobject` não são retornados em operações `GET`. Todas as senhas, chaves e segredos devem usar esse tipo.|  
|defaultValue|Não|Especifica o valor padrão para o parâmetro quando nenhum valor for especificado no momento que o recurso é criado.|  
|allowedValues|Não|Especifica uma matriz de valores permitidos para o parâmetro.|  
|metadata|Não|Especifica informações adicionais sobre o parâmetro, como uma descrição legível ou dados de tempo de design usados pelo Visual Studio ou outras ferramentas.|  
  
Este exemplo mostra como você pode usar um parâmetro na seção do corpo de uma ação:  
  
```json
"body" :
{
  "property1": "@parameters('parameter1')"
}
```

 Os parâmetros também podem ser usados em saídas.  
  
## <a name="triggers-and-actions"></a>Gatilhos e ações  

Gatilhos e ações especificam as chamadas que podem participar na execução do fluxo de trabalho. Para obter detalhes sobre esta seção, consulte [Gatilhos e ações do fluxo de trabalho](logic-apps-workflow-actions-triggers.md).
  
## <a name="outputs"></a>outputs  

As saídas especificam informações que podem ser retornadas de uma execução de fluxo de trabalho. Por exemplo, se você tiver um status específico ou um valor que você deseja controlar para cada execução, você pode incluir esses dados nas saídas da execução. Os dados aparecem na API REST de Gerenciamento para essa execução e na interface do usuário de gerenciamento para essa execução no portal do Azure. Você também pode direcionar essas saídas para outros sistemas externos como o Power BI para a criação de painéis. As saídas *não* são usadas para responder a solicitações de entrada na API REST do serviço. Aqui está um exemplo de como responder a uma solicitação de entrada usando o tipo de ação `response`:
  
```json
"outputs": {  
  "key1": {  
    "value": "value1",  
    "type" : "<type-of-value>"  
  }  
} 
```

|Nome do elemento|Obrigatório|Descrição|  
|------------------|--------------|-----------------|  
|key1|Sim|Especifica o identificador de chave para a saída. Substitua **key1** por um nome que você deseja usar para identificar a saída.|  
|value|Sim|Especifica o valor da saída.|  
|type|Sim|Especifica o tipo para o valor especificado. Os tipos de valores possíveis são: <ul><li>`string`</li><li>`securestring`</li><li>`int`</li><li>`bool`</li><li>`array`</li><li>`object`</li></ul>|
  
## <a name="expressions"></a>Expressões  

Os valores JSON na definição podem ser literais ou podem ser expressões que são avaliadas quando a definição é usada. Por exemplo:  
  
```json
"name": "value"
```

 ou o  
  
```json
"name": "@parameters('password') "
```

> [!NOTE]
> Algumas expressões obtêm seus valores de ações de tempo de execução que podem não existir no início da execução. Você pode usar **funções** para ajudar a recuperar alguns desses valores.  
  
As expressões podem aparecer em qualquer lugar em um valor de cadeia de caracteres JSON e sempre retornam outro valor JSON. Quando um valor JSON tiver sido determinado como uma expressão, o corpo da expressão é extraído, removendo a arroba (@). Se for necessária uma cadeia de caracteres literal que comece com @, essa cadeia de caracteres deve ser substituída usando @@. Os exemplos a seguir mostram como as expressões são avaliadas.  
  
|Valor JSON|Result|  
|----------------|------------|  
|"parameters"|Os “parâmetros” dos caracteres são retornados.|  
|"parameters[1]"|Os “parâmetros[1]” dos caracteres são retornados.|  
|"@@"|Uma cadeia de caracteres de 1 caractere que contém '@' será retornada.|  
|" @"|Uma cadeia de caracteres de 2 caracteres que contém ' @' será retornada.|  
  
Com *interpolação de cadeia de caracteres*, as expressões também podem aparecer dentro de cadeias de caracteres onde as expressões são encapsuladas em `@{ ... }`. Por exemplo: <p>`"name" : "First Name: @{parameters('firstName')} Last Name: @{parameters('lastName')}"`

O resultado é sempre uma cadeia de caracteres, o que torna esse recurso semelhante à função `concat`. Suponha que você definiu `myNumber` como `42` e `myString` como `sampleString`:  
  
|Valor JSON|Result|  
|----------------|------------|  
|"@parameters('myString')"|Retorna `sampleString` como uma cadeia de caracteres.|  
|"@{parameters('myString')}"|Retorna `sampleString` como uma cadeia de caracteres.|  
|"@parameters('myNumber')"|Retorna `42` como um *número*.|  
|"@{parameters('myNumber')}"|Retorna `42` como uma *cadeia de caracteres*.|  
|"A resposta é: @{parameters('myNumber')}"|Retorna a cadeia de caracteres `Answer is: 42`.|  
|"@concat('A resposta é: ', string(parameters('myNumber')))"|Retorna a cadeia de caracteres `Answer is: 42`|  
|"A resposta é: @@{parameters('myNumber')}"|Retorna a cadeia de caracteres `Answer is: @{parameters('myNumber')}`.|  
  
## <a name="operators"></a>Operadores  

Os operadores são os caracteres que podem ser usados dentro de expressões ou funções. 
  
|operador|Descrição|  
|--------------|-----------------|  
|.|O operador ponto permite que você faça referência a propriedades de um objeto|  
|?|O operador de ponto de interrogação permite fazer referência a propriedades nulas de um objeto sem um erro de tempo de execução. Por exemplo, você pode usar essa expressão para tratar as saídas do gatilho nulas: <p>`@coalesce(trigger().outputs?.body?.property1, 'my default value')`|  
|'|A aspa simples é a única maneira de encapsular literais de cadeia de caracteres. Você não pode usar aspas duplas dentro de expressões porque essa pontuação está em conflito com a citação JSON que encapsula toda a expressão.|  
|[]|Os colchetes podem ser usados para obter um valor de uma matriz com um índice específico. Por exemplo, se você tiver uma ação que passa `range(0,10)`em para a função `forEach`, você pode usar essa função para obter itens de matrizes:  <p>`myArray[item()]`|  
  
## <a name="functions"></a>Funções  

Você também pode chamar funções dentro de expressões. A tabela a seguir mostra as funções que podem ser usadas em uma expressão.  
  
|Expression|Avaliação|  
|----------------|----------------|  
|"@function('Hello')"|Chama o membro da função da definição com a cadeia de caracteres literais Hello como o primeiro parâmetro.|  
|"@function('É incrível!')"|Chama o membro da função da definição com a cadeia de caracteres literais 'É incrível!' como o primeiro parâmetro|  
|"@function().prop1"|Retorna o valor da propriedade prop1 do membro `myfunction` da definição.|  
|"@function('Hello').prop1"|Chama o membro da função da definição com a cadeia de caracteres literais 'Hello' como o primeiro parâmetro e retorna a propriedade prop1 do objeto.|  
|"@function(parameters('Hello'))"|Avalia o parâmetro Hello e passa o valor para a função|  
  
### <a name="referencing-functions"></a>Referenciando funções  

Você pode usar essas funções para fazer referência a saídas de outras ações no aplicativo lógico ou valores passados quando o aplicativo lógico foi criado. Por exemplo, você pode fazer referência os dados de uma etapa para usá-los em outra etapa.  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|parameters|Retorna um valor de parâmetro que é definido na definição. <p>`parameters('password')` <p> **Número do parâmetro**: 1 <p> **Nome**: Parâmetro <p> **Descrição**: Obrigatório. O nome do parâmetro cujos valores você deseja.|  
|ação|Permite que uma expressão derive seu valor de outro nome JSON e de pares de valor ou da saída da ação atual em tempo de execução. A propriedade representada por propertyPath no exemplo a seguir é opcional. Se propertyPath não for especificada, a referência é feita a todo o objeto de ação. Essa função só pode ser usada sob as condições “do-until” de uma ação. <p>`action().outputs.body.propertyPath`|  
|Ações|Permite que uma expressão derive seu valor de outro nome JSON e de pares de valor ou da saída da ação de tempo de execução. Essas expressões declaram explicitamente que uma ação depende de outra ação. A propriedade representada por propertyPath no exemplo a seguir é opcional. Se propertyPath não for especificada, a referência é feita a todo o objeto de ação. Você pode usar esse elemento ou o elemento das condições para especificar dependências, mas não é necessário usar ambos para o mesmo recurso dependente. <p>`actions('myAction').outputs.body.propertyPath` <p> **Número do parâmetro**: 1 <p> **Nome**: Nome da ação <p> **Descrição**: Obrigatório. O nome da ação cujos valores você deseja. <p> As propriedades disponíveis no objeto de ação são: <ul><li>`name`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>Consulte a [API Rest](http://go.microsoft.com/fwlink/p/?LinkID=850646) para obter detalhes sobre essas propriedades.|
|gatilho|Permite que uma expressão derive seu valor de outro nome JSON e de pares de valor ou da saída do gatilho de tempo de execução. A propriedade representada por propertyPath no exemplo a seguir é opcional. Se propertyPath não for especificada, a referência é feita a todo o objeto do gatilho. <p>`trigger().outputs.body.propertyPath` <p>Quando usado nas entradas do gatilho, a função retornará as saídas da execução anterior. Entretanto, quando usado em uma condição do gatilho, a função `trigger` retornará as saídas da execução atual. <p> As propriedades disponíveis no objeto de gatilho são: <ul><li>`name`</li><li>`scheduledTime`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>Consulte a [API Rest](http://go.microsoft.com/fwlink/p/?LinkID=850644) para obter detalhes sobre essas propriedades.|
|actionOutputs|Essa é uma função abreviada para `actions('actionName').outputs` <p> **Número do parâmetro**: 1 <p> **Nome**: Nome da ação <p> **Descrição**: Obrigatório. O nome da ação cujos valores você deseja.|  
|actionBody e body|Essas são funções abreviadas para `actions('actionName').outputs.body` <p> **Número do parâmetro**: 1 <p> **Nome**: Nome da ação <p> **Descrição**: Obrigatório. O nome da ação cujos valores você deseja.|  
|triggerOutputs|Essa é uma função abreviada para `trigger().outputs`|  
|triggerBody|Essa é uma função abreviada para `trigger().outputs.body`|  
|item|Quando usado dentro de uma ação de repetição, essa função retorna o item na matriz para essa iteração da ação. Por exemplo, se você tiver uma ação que é executada para cada item de uma matriz de mensagens, você pode usar esta sintaxe: <p>`"input1" : "@item().subject"`| 
  
### <a name="collection-functions"></a>Funções de coleção  

Essas funções operam em coleções e geralmente se aplicam a matrizes, cadeias de caracteres e, às vezes, dicionários.  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|contains|Retorna true se o dicionário contém uma chave, se a lista contém um valor ou a cadeia de caracteres contém uma subcadeia de caracteres. Por exemplo, essa função retornará `true`: <p>`contains('abacaba','aca')` <p> **Número do parâmetro**: 1 <p> **Nome**: Dentro da coleção <p> **Descrição**: Obrigatório. A coleção a ser pesquisada. <p> **Número do parâmetro**: 2 <p> **Nome**: Encontrar objeto <p> **Descrição**: Obrigatório. O objeto a ser encontrado dentro de **Na coleção**.|  
|length|Retorna o número de elementos em uma matriz ou cadeia de caracteres. Por exemplo, essa função retornará `3`:  <p>`length('abc')` <p> **Número do parâmetro**: 1 <p> **Nome**: Coleção <p> **Descrição**: Obrigatório. A coleção para a qual se deseja obter o comprimento.|  
|empty|Retornará true se o objeto, matriz ou cadeia de caracteres estiverem vazios. Por exemplo, essa função retornará `true`: <p>`empty('')` <p> **Número do parâmetro**: 1 <p> **Nome**: Coleção <p> **Descrição**: Obrigatório. A coleção que será verificada para saber se está vazia.|  
|interseção|Retorna uma única matriz ou objeto que tem elementos comuns entre matrizes ou objetos passados. Por exemplo, essa função retornará `[1, 2]`: <p>`intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])` <p>Os parâmetros para a função podem ser um conjunto de objetos ou um conjunto de matrizes (não uma mistura de ambos). Se houver dois objetos com o mesmo nome, o último objeto com esse nome aparece no objeto final. <p> **Número do parâmetro**: 1 ... *n* <p> **Nome**: Coleção *n* <p> **Descrição**: Obrigatório. As coleções a serem avaliadas. Um objeto deve estar em todas as coleções passadas para aparecer no resultado.|  
|union|Retorna uma única matriz ou objeto com todos os elementos que estão em uma matriz ou objeto passado para esta função. Por exemplo, essa função retornará `[1, 2, 3, 10, 101]`: <p>`union([1, 2, 3], [101, 2, 1, 10])` <p>Os parâmetros para a função podem ser um conjunto de objetos ou um conjunto de matrizes (não uma mistura destes). Se houver dois objetos com o mesmo nome na saída final, o último objeto com esse nome aparece no objeto final. <p> **Número do parâmetro**: 1 ... *n* <p> **Nome**: Coleção *n* <p> **Descrição**: Obrigatório. As coleções a serem avaliadas. Um objeto que aparece em qualquer uma das coleções também aparece no resultado.|  
|first|Retorna o primeiro elemento da matriz ou da cadeia de caracteres passada. Por exemplo, essa função retornará `0`: <p>`first([0,2,3])` <p> **Número do parâmetro**: 1 <p> **Nome**: Coleção <p> **Descrição**: Obrigatório. A coleção que possui o primeiro objeto a ser retirado.|  
|last|Retorna o último elemento da matriz ou da cadeia de caracteres passada. Por exemplo, essa função retornará `3`: <p>`last('0123')` <p> **Número do parâmetro**: 1 <p> **Nome**: Coleção <p> **Descrição**: Obrigatório. A coleção que possui o último objeto a ser retirado.|  
|take|Retorna os primeiros elementos da **Contagem** matriz ou da cadeia de caracteres passada. Por exemplo, essa função retornará `[1, 2]`:  <p>`take([1, 2, 3, 4], 2)` <p> **Número do parâmetro**: 1 <p> **Nome**: Coleção <p> **Descrição**: Obrigatório. A coleção que possui os primeiros objetos de **Contagem** a serem retirados. <p> **Número do parâmetro**: 2 <p> **Nome**: Contagem <p> **Descrição**: Obrigatório. O número de objetos a serem retirados da **Coleção**. Deve ser um número inteiro positivo.|  
|skip|Retorna os elementos da matriz começando pelo índice de **Contagem**. Por exemplo, essa função retornará `[3, 4]`: <p>`skip([1, 2 ,3 ,4], 2)` <p> **Número do parâmetro**: 1 <p> **Nome**: Coleção <p> **Descrição**: Obrigatório. A coleção onde serão ignorados os primeiros objetos da **Contagem**. <p> **Número do parâmetro**: 2 <p> **Nome**: Contagem <p> **Descrição**: Obrigatório. O número de objetos a serem removidos da frente da **Coleção**. Deve ser um número inteiro positivo.|  
|join|Retorna uma cadeia de caracteres com cada item de uma matriz unida por um delimitador, por exemplo, isso retorna `"1,2,3,4"`:<br /><br /> `join([1, 2, 3, 4], ',')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Coleção<br /><br /> **Descrição**: Obrigatório. A coleção para adicionar itens.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Delimitador<br /><br /> **Descrição**: Obrigatório. A cadeia de caracteres para delimitação dos itens.|  
  
### <a name="string-functions"></a>Funções de cadeia de caracteres

As funções a seguir se aplicam somente a cadeias de caracteres. Você também pode usar algumas funções de coleção em cadeias de caracteres.  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|concat|Combina qualquer número de cadeias de caracteres. Por exemplo, se o parâmetro 1 é `p1`, essa função retorna `somevalue-p1-somevalue`: <p>`concat('somevalue-',parameters('parameter1'),'-somevalue')` <p> **Número do parâmetro**: 1 ... *n* <p> **Nome**: cadeia de caracteres *n* <p> **Descrição**: Obrigatório. As cadeias de caracteres para combinar em uma única cadeia de caracteres.|  
|substring|Retorna um subconjunto de caracteres de uma cadeia de caracteres. Por exemplo, essa função retornará `abc`: <p>`substring('somevalue-abc-somevalue',10,3)` <p> **Número do parâmetro**: 1 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. A cadeia de caracteres original da qual é obtida a subcadeia de caracteres. <p> **Número do parâmetro**: 2 <p> **Nome**: Índice inicial <p> **Descrição**: Obrigatório. O índice de onde a subcadeia de caracteres começa no parâmetro 1. <p> **Número do parâmetro**: 3 <p> **Nome**: Comprimento <p> **Descrição**: Obrigatório. Comprimento da subcadeia de caracteres.|  
|substitui|Substitui uma cadeia de caracteres por uma determinada sequência de caracteres. Por exemplo, essa função retornará `the new string`: <p>`replace('the old string', 'old', 'new')` <p> **Número do parâmetro**: 1 <p> **Nome**: cadeia de caracteres <p> **Descrição**: Obrigatório. A cadeia de caracteres que é pesquisada para o parâmetro 2 e atualizada com o parâmetro 3, quando o parâmetro 2 é encontrado no parâmetro 1. <p> **Número do parâmetro**: 2 <p> **Nome**: Cadeia de caracteres antiga <p> **Descrição**: Obrigatório. A cadeia de caracteres para substituir o parâmetro 3, quando uma correspondência é encontrada no parâmetro 1 <p> **Número do parâmetro**: 3 <p> **Nome**: Cadeia de caracteres nova <p> **Descrição**: Obrigatório. A cadeia de caracteres usada para substituir a cadeia de caracteres no parâmetro 2, quando uma correspondência é encontrada no parâmetro 1.|  
|GUID|Essa função gera uma cadeia de caracteres de identificador global exclusiva (GUID). Por exemplo, essa função pode gerar esse GUID:`c2ecc88d-88c8-4096-912c-d6f2e2b138ce` <p>`guid()` <p> **Número do parâmetro**: 1 <p> **Nome**: Formato <p> **Descrição**: Opcional. Um especificador de formato único que indica [como formatar o valor desse Guid](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). O parâmetro de formato pode ser "N", "D", "B", "P" ou "X". Se o formato não for fornecido, "D" é usado.|  
|toLower|Converte uma cadeia de caracteres em letras minúsculas. Por exemplo, essa função retornará `two by two is four`: <p>`toLower('Two by Two is Four')` <p> **Número do parâmetro**: 1 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. A cadeia de caracteres a ser convertida em letras minúsculas. Se um caractere na cadeia de caracteres tem um equivalente em letras minúsculas, o caractere será incluído inalterado na cadeia de caracteres retornada.|  
|toUpper|Converte uma cadeia de caracteres em letras maiúsculas. Por exemplo, essa função retornará `TWO BY TWO IS FOUR`: <p>`toUpper('Two by Two is Four')` <p> **Número do parâmetro**: 1 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. A cadeia de caracteres a ser convertida em letras maiúsculas. Se um caractere na cadeia de caracteres não tiver um equivalente maiúsculo, o caractere será incluído inalterado na cadeia de caracteres retornada.|  
|indexof|Encontra o índice de um valor dentro de uma cadeia de caracteres sem considerar letras maiúsculas e minúsculas. Por exemplo, essa função retornará `7`: <p>`indexof('hello, world.', 'world')` <p> **Número do parâmetro**: 1 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. A cadeia de caracteres que pode conter o valor. <p> **Número do parâmetro**: 2 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. O valor a ter o índice pesquisado.|  
|lastindexof|Encontra o último índice de um valor dentro de uma cadeia de caracteres sem considerar letras maiúsculas e minúsculas. Por exemplo, essa função retornará `3`: <p>`lastindexof('foofoo', 'foo')` <p> **Número do parâmetro**: 1 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. A cadeia de caracteres que pode conter o valor. <p> **Número do parâmetro**: 2 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. O valor a ter o índice pesquisado.|  
|startswith|Verifica se a cadeia de caracteres começa com um valor sem considerar letras maiúsculas e minúsculas. Por exemplo, essa função retornará `true`: <p>`startswith('hello, world', 'hello')` <p> **Número do parâmetro**: 1 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. A cadeia de caracteres que pode conter o valor. <p> **Número do parâmetro**: 2 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. O possível valor inicial da cadeia de caracteres.|  
|endswith|Verifica se a cadeia de caracteres termina com um valor sem considerar letras maiúsculas e minúsculas. Por exemplo, essa função retornará `true`: <p>`endswith('hello, world', 'world')` <p> **Número do parâmetro**: 1 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. A cadeia de caracteres que pode conter o valor. <p> **Número do parâmetro**: 2 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. O possível valor final da cadeia de caracteres.|  
|split|Divide a cadeia de caracteres usando um separador. Por exemplo, essa função retornará `["a", "b", "c"]`: <p>`split('a;b;c',';')` <p> **Número do parâmetro**: 1 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. A cadeia de caracteres que será dividida. <p> **Número do parâmetro**: 2 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. O separador.|  
  
### <a name="logical-functions"></a>Funções lógicas  

Essas funções são úteis em condições e podem ser usadas para avaliar qualquer tipo de lógica.  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|equals|Retorna true se dois valores são iguais. Por exemplo, se parameter1 é someValue, essa função retorna `true`: <p>`equals(parameters('parameter1'), 'someValue')` <p> **Número do parâmetro**: 1 <p> **Nome**: Objeto 1 <p> **Descrição**: Obrigatório. O objeto a ser comparado com o **Objeto 2**. <p> **Número do parâmetro**: 2 <p> **Nome**: Objeto 2 <p> **Descrição**: Obrigatório. O objeto a ser comparado ao **Objeto 1**.|  
|less|Retorna true se o primeiro argumento for menor do que o segundo. Observe que os valores só podem ser do tipo inteiro, float ou cadeia de caracteres. Por exemplo, essa função retornará `true`: <p>`less(10,100)` <p> **Número do parâmetro**: 1 <p> **Nome**: Objeto 1 <p> **Descrição**: Obrigatório. O objeto para verificar se é menor que o **Objeto 2**. <p> **Número do parâmetro**: 2 <p> **Nome**: Objeto 2 <p> **Descrição**: Obrigatório. O objeto para verificar se é maior que o **Objeto 1**.|  
|lessOrEquals|Retorna true se o primeiro argumento for menor ou igual ao segundo. Observe que os valores só podem ser do tipo inteiro, float ou cadeia de caracteres. Por exemplo, essa função retornará `true`: <p>`lessOrEquals(10,10)` <p> **Número do parâmetro**: 1 <p> **Nome**: Objeto 1 <p> **Descrição**: Obrigatório. O objeto para verificar se é menor ou igual ao **Objeto 2**. <p> **Número do parâmetro**: 2 <p> **Nome**: Objeto 2 <p> **Descrição**: Obrigatório. O objeto para verificar se é maior ou igual ao **Objeto 1**.|  
|greater|Retorna true se o primeiro argumento for maior do que o segundo. Observe que os valores só podem ser do tipo inteiro, float ou cadeia de caracteres. Por exemplo, essa função retornará `false`:  <p>`greater(10,10)` <p> **Número do parâmetro**: 1 <p> **Nome**: Objeto 1 <p> **Descrição**: Obrigatório. O objeto para verificar se é maior que o **Objeto 2**. <p> **Número do parâmetro**: 2 <p> **Nome**: Objeto 2 <p> **Descrição**: Obrigatório. O objeto para verificar se é menor que o **Objeto 1**.|  
|greaterOrEquals|Retorna true se o primeiro argumento for maior ou igual ao segundo. Observe que os valores só podem ser do tipo inteiro, float ou cadeia de caracteres. Por exemplo, essa função retornará `false`: <p>`greaterOrEquals(10,100)` <p> **Número do parâmetro**: 1 <p> **Nome**: Objeto 1 <p> **Descrição**: Obrigatório. O objeto para verificar se é maior ou igual ao **Objeto 2**. <p> **Número do parâmetro**: 2 <p> **Nome**: Objeto 2 <p> **Descrição**: Obrigatório. O objeto para verificar se é menor ou igual ao **Objeto 1**.|  
|e|Retorna true se ambos os parâmetros forem verdadeiros. Ambos os argumentos precisam ser boolianos. Por exemplo, essa função retornará `false`: <p>`and(greater(1,10),equals(0,0))` <p> **Número do parâmetro**: 1 <p> **Nome**: Booliano 1 <p> **Descrição**: Obrigatório. O primeiro argumento deve ser `true`. <p> **Número do parâmetro**: 2 <p> **Nome**: Booliano 2 <p> **Descrição**: Obrigatório. O segundo argumento deve ser `true`.|  
|ou o|Retorna true se o parâmetro for verdadeiro. Ambos os argumentos precisam ser boolianos. Por exemplo, essa função retornará `true`: <p>`or(greater(1,10),equals(0,0))` <p> **Número do parâmetro**: 1 <p> **Nome**: Booliano 1 <p> **Descrição**: Obrigatório. O primeiro argumento pode ser `true`. <p> **Número do parâmetro**: 2 <p> **Nome**: Booliano 2 <p> **Descrição**: Obrigatório. O segundo argumento pode ser `true`.|  
|não|Retorna true se os parâmetros forem `false`. Ambos os argumentos precisam ser boolianos. Por exemplo, essa função retornará `true`: <p>`not(contains('200 Success','Fail'))` <p> **Número do parâmetro**: 1 <p> **Nome**: Booliano <p> **Descrição**: Retorna true se os parâmetros forem `false`. Ambos os argumentos precisam ser boolianos. Adicione esta função a `true`:  `not(contains('200 Success','Fail'))`|  
|if|Retorna um valor especificado, com base em se a expressão resultou em `true` ou `false`.  Por exemplo, essa função retornará `"yes"`: <p>`if(equals(1, 1), 'yes', 'no')` <p> **Número do parâmetro**: 1 <p> **Nome**: Expressão <p> **Descrição**: Obrigatório. Um valor booliano que determina qual valor a expressão deve retornar. <p> **Número do parâmetro**: 2 <p> **Nome**: Verdadeiro <p> **Descrição**: Obrigatório. O valor a ser retornado se a expressão for `true`. <p> **Número do parâmetro**: 3 <p> **Nome**: Falso <p> **Descrição**: Obrigatório. O valor a ser retornado se a expressão for `false`.|  
  
### <a name="conversion-functions"></a>Funções de conversão  

Essas funções são usadas para converter entre cada um dos tipos nativos no idioma:  
  
- string  
  
- inteiro  
  
- flutuante  
  
- Booliano  
  
- matrizes  
  
- dicionários  

-   formulários  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|int|Converta o parâmetro em um inteiro. Por exemplo, essa função retorna 100 como um número, em vez de uma cadeia de caracteres: <p>`int('100')` <p> **Número do parâmetro**: 1 <p> **Nome**: Valor <p> **Descrição**: Obrigatório. O valor que é convertido em um inteiro.|  
|string|Converta o parâmetro em uma cadeia de caracteres. Por exemplo, essa função retornará `'10'`: <p>`string(10)` <p>Você também pode converter um objeto para uma cadeia de caracteres. Por exemplo, se o parâmetro `myPar` é um objeto com uma propriedade `abc : xyz`, a função retornará `{"abc" : "xyz"}`: <p>`string(parameters('myPar'))` <p> **Número do parâmetro**: 1 <p> **Nome**: Valor <p> **Descrição**: Obrigatório. O valor que é convertido em uma cadeia de caracteres.|  
|json|Converter o parâmetro para um valor de tipo JSON e que seja o oposto de `string()`. Por exemplo, essa função retorna `[1,2,3]` como uma matriz, em vez de uma cadeia de caracteres: <p>`json('[1,2,3]')` <p>Da mesma forma, você pode converter uma cadeia de caracteres em um objeto. Por exemplo, essa função retornará `{ "abc" : "xyz" }`: <p>`json('{"abc" : "xyz"}')` <p> **Número do parâmetro**: 1 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. A cadeia de caracteres que é convertida em um valor de tipo nativo. <p>A função `json()` também oferece suporte à entrada de XML. Por exemplo, o valor do parâmetro de: <p>`<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>` <p>é convertido nesse JSON: <p>`{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|flutuante|Converte o argumento do parâmetro para um número de ponto flutuante. Por exemplo, essa função retornará `10.333`: <p>`float('10.333')` <p> **Número do parâmetro**: 1 <p> **Nome**: Valor <p> **Descrição**: Obrigatório. O valor é convertido em um número de ponto flutuante.|  
|bool|Converte o parâmetro em um booliano. Por exemplo, essa função retornará `false`: <p>`bool(0)` <p> **Número do parâmetro**: 1 <p> **Nome**: Valor <p> **Descrição**: Obrigatório. O valor que é convertido em um booliano.|  
|coalesce|Retorna o primeiro objeto não nulos nos argumentos passados. **Observação**: Uma cadeia de caracteres vazia não é nula. Por exemplo, se os parâmetros 1 e 2 não forem definidos, essa função retornará `fallback`:  <p>`coalesce(parameters('parameter1'), parameters('parameter2') ,'fallback')` <p> **Número do parâmetro**: 1 ... *n* <p> **Nome**: Objeto*n* <p> **Descrição**: Obrigatório. Os objetos para verificar se são nulos.|  
|base64|Retorna a representação base64 da cadeia de caracteres de entrada. Por exemplo, essa função retornará `c29tZSBzdHJpbmc=`: <p>`base64('some string')` <p> **Número do parâmetro**: 1 <p> **Nome**: Cadeia de caracteres 1 <p> **Descrição**: Obrigatório. A cadeia de caracteres para codificar em uma representação base64.|  
|base64ToBinary|Retorna uma representação binária de uma cadeia de caracteres codificada em base64. Por exemplo, esta função retorna a representação binária de `some string`: <p>`base64ToBinary('c29tZSBzdHJpbmc=')` <p> **Número do parâmetro**: 1 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. A cadeia de caracteres codificada em base64.|  
|base64ToString|Retorna uma representação de cadeia de caracteres de uma cadeia de caracteres codificada em base64. Por exemplo, essa função retornará `some string`: <p>`base64ToString('c29tZSBzdHJpbmc=')` <p> **Número do parâmetro**: 1 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. A cadeia de caracteres codificada em base64.|  
|Binário|Retorna uma representação binária de um valor.  Por exemplo, esta função retorna uma representação binária de `some string`: <p>`binary('some string')` <p> **Número do parâmetro**: 1 <p> **Nome**: Valor <p> **Descrição**: Obrigatório. O valor que é convertido em binário.|  
|dataUriToBinary|Retorna uma representação binária de um URI de dados. Por exemplo, esta função retorna a representação binária de `some string`: <p>`dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')` <p> **Número do parâmetro**: 1 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. Os URI dos dados a serem convertidos para representação binária.|  
|dataUriToString|Retorna uma representação de cadeia de caracteres de um URI de dados. Por exemplo, essa função retornará `some string`: <p>`dataUriToString('data:;base64,c29tZSBzdHJpbmc=')` <p> **Número do parâmetro**: 1 <p> **Nome**: Cadeia de caracteres<p> **Descrição**: Obrigatório. Os URI dos dados a serem convertidos para representação em cadeia de caracteres.|  
|dataUri|Retorna um URI de dados de um valor. Por exemplo, essa função retornará o seguinte URI de dados `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=`: <p>`dataUri('some string')` <p> **Número do parâmetro**: 1<p> **Nome**: Valor<p> **Descrição**: Obrigatório. O valor a ser convertido em URI de dados.|  
|decodeBase64|Retorna uma representação de cadeia de caracteres de uma cadeia de caracteres com entrada baseada em base64. Por exemplo, essa função retornará `some string`: <p>`decodeBase64('c29tZSBzdHJpbmc=')` <p> **Número do parâmetro**: 1 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Retorna uma representação de cadeia de caracteres de uma cadeia de caracteres com entrada baseada em base64.|  
|encodeUriComponent|Codifica a URL para a cadeia de caracteres que é passada. Por exemplo, essa função retornará `You+Are%3ACool%2FAwesome`: <p>`encodeUriComponent('You Are:Cool/Awesome')` <p> **Número do parâmetro**: 1 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. A cadeia de caracteres para codificação de caracteres não seguros de URL.|  
|decodeUriComponent|Descodifica a URL para a cadeia de caracteres que é passada. Por exemplo, essa função retornará `You Are:Cool/Awesome`: <p>`encodeUriComponent('You+Are%3ACool%2FAwesome')` <p> **Número do parâmetro**: 1 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. A cadeia de caracteres para descodificação de caracteres não seguros de URL.|  
|decodeDataUri|Retorna uma representação binária de uma cadeia de caracteres de URI de dados de entrada. Por exemplo, esta função retorna a representação binária de `some string`: <p>`decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')` <p> **Número do parâmetro**: 1 <p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. O dataURI a ser decodificado para uma representação binária.|  
|uriComponent|Retorna uma representação codificada de URI de um valor. Por exemplo, essa função retornará `You+Are%3ACool%2FAwesome`: <p>`uriComponent('You Are:Cool/Awesome')` <p> **Número do parâmetro**: 1<p> **Nome**: Cadeia de caracteres <p> **Descrição**: Obrigatório. A cadeia de caracteres a ser codificada para URI.|  
|uriComponentToBinary|Retorna uma representação binária de uma cadeia de caracteres codificada em URI. Por exemplo, esta função retorna uma representação binária de `You Are:Cool/Awesome`: <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **Número do parâmetro**: 1 <p> **Nome**: Cadeia de caracteres<p> **Descrição**: Obrigatório. Cadeia de caracteres codificada em URI.|  
|uriComponentToString|Retorna uma representação de cadeia de caracteres de uma cadeia de caracteres codificada em URI. Por exemplo, essa função retornará `You Are:Cool/Awesome`: <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **Número do parâmetro**: 1<p> **Nome**: Cadeia de caracteres<p> **Descrição**: Obrigatório. Cadeia de caracteres codificada em URI.|  
|xml|Retorna uma representação XML do valor. Por exemplo, essa função retorna o conteúdo XML representado por `'\<name>Alan\</name>'`: <p>`xml('\<name>Alan\</name>')` <p>A função `xml()` também oferece suporte à entrada de objeto JSON. Por exemplo, o parâmetro `{ "abc": "xyz" }` é convertido em conteúdo XML: `\<abc>xyz\</abc>` <p> **Número do parâmetro**: 1<p> **Nome**: Valor<p> **Descrição**: Obrigatório. O valor a ser convertido para XML.|  
|xpath|Retorna uma matriz de nós XML que corresponde à expressão xpath de um valor que avalia a expressão xpath. <p> **Exemplo 1** <p>Suponha que o valor do parâmetro `p1` é uma representação de cadeia de caracteres desse XML: <p>`<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>` <p>Esse código: `xpath(xml(parameters('p1'), '/lab/robot/name')` <p>retorna <p>`[ <name>R1</name>, <name>R2</name> ]` <p>enquanto esse código: <p>`xpath(xml(parameters('p1'), ' sum(/lab/robot/parts)')` <p>retorna <p>`13` <p> <p> **Exemplo 2** <p>Dado o conteúdo XML a seguir: <p>`<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>` <p>Esse código: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')` <p>ou esse código: <p>`@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')` <p>retorna <p>`<Location xmlns="http://abc.com">xyz</Location>` <p>E esse código: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')` <p>retorna <p>``xyz`` <p> **Número do parâmetro**: 1 <p> **Nome**: Xml <p> **Descrição**: Obrigatório. O XML onde é avaliada a expressão XPath. <p> **Número do parâmetro**: 2 <p> **Nome**: XPath <p> **Descrição**: Obrigatório. A expressão XPath a ser avaliada.|  
|array|Converte o parâmetro em uma matriz. Por exemplo, essa função retornará `["abc"]`: <p>`array('abc')` <p> **Número do parâmetro**: 1 <p> **Nome**: Valor <p> **Descrição**: Obrigatório. O valor que é convertido em uma matriz.|
|createArray|Cria uma matriz de parâmetros. Por exemplo, essa função retornará `["a", "c"]`: <p>`createArray('a', 'c')` <p> **Número do parâmetro**: 1 ... *n* <p> **Nome**: Qualquer *n* <p> **Descrição**: Obrigatório. Os valores a serem combinados em uma matriz.|
|triggerFormDataValue|Retorna um único valor correspondente ao nome de chave de dados do formulário ou a saída do gatilho codificado de formulário.  Se houver várias correspondências ocorrerá um erro.  Por exemplo, o seguinte retornará `bar`: `triggerFormDataValue('foo')`<br /><br />**Número do parâmetro**: 1<br /><br />**Nome**: Nome da chave<br /><br />**Descrição**: Obrigatório. O nome da chave a ser retornado para o valor de dados de formulário.|
|triggerFormDataMultiValues|Retorna uma matriz de valores correspondentes ao nome de chave de dados do formulário ou a saída do gatilho codificado de formulário.  Por exemplo, o seguinte retornará `["bar"]`: `triggerFormDataValue('foo')`<br /><br />**Número do parâmetro**: 1<br /><br />**Nome**: Nome da chave<br /><br />**Descrição**: Obrigatório. O nome da chave dos valores de dados de formulário para retornar.|
|triggerMultipartBody|Retorna o corpo de uma parte em uma saída de várias partes do gatilho.<br /><br />**Número do parâmetro**: 1<br /><br />**Nome**: Índice<br /><br />**Descrição**: Obrigatório. O índice da parte a ser recuperada.|
|formDataValue|Retorna um único valor correspondente ao nome de chave de dados do formulário ou a saída de ação codificada em formulário.  Se houver várias correspondências ocorrerá um erro.  Por exemplo, o seguinte retornará `bar`: `formDataValue('someAction', 'foo')`<br /><br />**Número do parâmetro**: 1<br /><br />**Nome**: Nome da ação<br /><br />**Descrição**: Obrigatório. O nome da ação com uma resposta de dados de formulário ou codificada de formulário.<br /><br />**Número do parâmetro**: 2<br /><br />**Nome**: Nome da chave<br /><br />**Descrição**: Obrigatório. O nome da chave a ser retornado para o valor de dados de formulário.|
|formDataMultiValues|Retorna uma matriz de valores correspondentes ao nome de chave de dados do formulário ou à saída da ação codificada em formulário.  Por exemplo, o seguinte retornará `["bar"]`: `formDataMultiValues('someAction', 'foo')`<br /><br />**Número do parâmetro**: 1<br /><br />**Nome**: Nome da ação<br /><br />**Descrição**: Obrigatório. O nome da ação com uma resposta de dados de formulário ou codificada de formulário.<br /><br />**Número do parâmetro**: 2<br /><br />**Nome**: Nome da chave<br /><br />**Descrição**: Obrigatório. O nome da chave dos valores de dados de formulário para retornar.|
|multipartBody|Retorna o corpo de uma parte em uma saída de várias partes de uma ação.<br /><br />**Número do parâmetro**: 1<br /><br />**Nome**: Nome da ação<br /><br />**Descrição**: Obrigatório. O nome da ação com uma resposta com várias partes.<br /><br />**Número do parâmetro**: 2<br /><br />**Nome**: Índice<br /><br />**Descrição**: Obrigatório. O índice da parte a ser recuperada.|

### <a name="math-functions"></a>Funções matemáticas  

Essas funções podem ser usadas para qualquer um dos tipos de números: **inteiros** e **floats**.  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|adicionar|Retorna o resultado da adição de dois números. Por exemplo, essa função retornará `20.333`: <p>`add(10,10.333)` <p> **Número do parâmetro**: 1 <p> **Nome**: Soma 1 <p> **Descrição**: Obrigatório. O número a ser adicionar à **Soma 2**. <p> **Número do parâmetro**: 2 <p> **Nome**: Soma 2 <p> **Descrição**: Obrigatório. O número a ser adicionar à **Soma 1**.|  
|sub|Retorna o resultado da subtração de dois números. Por exemplo, essa função retornará `-0.333`: <p>`sub(10,10.333)` <p> **Número do parâmetro**: 1 <p> **Nome**: Minuendo <p> **Descrição**: Obrigatório. O número de onde é removido o **Subtraendo**. <p> **Número do parâmetro**: 2 <p> **Nome**: Subtraendo <p> **Descrição**: Obrigatório. O número a ser removido do **Minuendo**.|  
|mul|Retorna o resultado da multiplicação de dois números. Por exemplo, essa função retornará `103.33`: <p>`mul(10,10.333)` <p> **Número do parâmetro**: 1 <p> **Nome**: Multiplicando 1 <p> **Descrição**: Obrigatório. O número a ser multiplicado pelo **Multiplicando 2**. <p> **Número do parâmetro**: 2 <p> **Nome**: Multiplicando 2 <p> **Descrição**: Obrigatório. O número a ser multiplicado pelo **Multiplicando 1**.|  
|div|Retorna o resultado da divisão de dois números. Por exemplo, essa função retornará `1.0333`: <p>`div(10.333,10)` <p> **Número do parâmetro**: 1 <p> **Nome**: Dividendo <p> **Descrição**: Obrigatório. O número a ser dividido pelo **Divisor**. <p> **Número do parâmetro**: 2 <p> **Nome**: Divisor <p> **Descrição**: Obrigatório. O número para o qual o **Dividendo** será dividido.|  
|mod|Retorna o resto após dividir dois números (módulo). Por exemplo, essa função retornará `2`: <p>`mod(10,4)` <p> **Número do parâmetro**: 1 <p> **Nome**: Dividendo <p> **Descrição**: Obrigatório. O número a ser dividido pelo **Divisor**. <p> **Número do parâmetro**: 2 <p> **Nome**: Divisor <p> **Descrição**: Obrigatório. O número para o qual o **Dividendo** será dividido. Após a divisão, o resto é obtido.|  
|Min|Há dois padrões diferentes para chamar essa função. <p>Aqui `min` aceita uma matriz e a função retorna `0`: <p>`min([0,1,2])` <p>Como alternativa, essa função pode conter uma lista separada por vírgulas de valores e também retorna `0`: <p>`min(0,1,2)` <p> **Observação**: Todos os valores devem ser números, portanto, se o parâmetro for uma matriz, a matriz deve ter apenas números. <p> **Número do parâmetro**: 1 <p> **Nome**: Coleção ou Valor <p> **Descrição**: Obrigatório. Uma matriz de valores para encontrar o valor mínimo ou o primeiro valor de um conjunto. <p> **Número do parâmetro**: 2 ... *n* <p> **Nome**: Valor *n* <p> **Descrição**: Opcional. Se o primeiro parâmetro é um Valor, você pode passar valores adicionais e o mínimo de todos os valores passados é retornado.|  
|max|Há dois padrões diferentes para chamar essa função. <p>Aqui `max` aceita uma matriz e a função retorna `2`: <p>`max([0,1,2])` <p>Como alternativa, essa função pode conter uma lista separada por vírgulas de valores e também retorna `2`: <p>`max(0,1,2)` <p> **Observação**: Todos os valores devem ser números, portanto, se o parâmetro for uma matriz, a matriz deve ter apenas números. <p> **Número do parâmetro**: 1 <p> **Nome**: Coleção ou Valor <p> **Descrição**: Obrigatório. Uma matriz de valores para encontrar o valor máximo ou o primeiro valor de um conjunto. <p> **Número do parâmetro**: 2 ... *n* <p> **Nome**: Valor *n* <p> **Descrição**: Opcional. Se o primeiro parâmetro é um valor, você pode passar valores adicionais e o máximo de todos os valores passados é retornado.|  
|range|Gera uma matriz de inteiros a partir de um determinado número. Você define o comprimento da matriz retornada. <p>Por exemplo, essa função retornará `[3,4,5,6]`: <p>`range(3,4)` <p> **Número do parâmetro**: 1 <p> **Nome**: Índice inicial <p> **Descrição**: Obrigatório. O primeiro inteiro na matriz. <p> **Número do parâmetro**: 2 <p> **Nome**: Contagem <p> **Descrição**: Obrigatório. Esse valor é o número de inteiros que está na matriz.|  
|rand|Gera um inteiro aleatório dentro do intervalo especificado (inclusive somente o inicial). Por exemplo, essa função pode retornar `0` ou '1': <p>`rand(0,2)` <p> **Número do parâmetro**: 1 <p> **Nome**: Mínimo <p> **Descrição**: Obrigatório. O menor inteiro que pode ser retornado. <p> **Número do parâmetro**: 2 <p> **Nome**: Máximo <p> **Descrição**: Obrigatório. Esse valor é o número inteiro seguinte ao maior número inteiro que pode ser retornado.|  
  
### <a name="date-functions"></a>Funções de data  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|utcnow|Retorna o carimbo de data/hora atual como uma cadeia de caracteres, por exemplo: `2017-03-15T13:27:36Z`: <p>`utcnow()` <p> **Número do parâmetro**: 1 <p> **Nome**: Formato <p> **Descrição**: Opcional. Um [caractere do especificador de formato único](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|addseconds|Adiciona um número inteiro de segundos a um carimbo de data/hora de cadeia de caracteres passado. O número de segundos pode ser positivo ou negativo. Por padrão, o resultado é uma cadeia de caracteres no formato ISO 8601 ("o"), a menos que um especificador de formato seja fornecido. Por exemplo: `2015-03-15T13:27:00Z`: <p>`addseconds('2015-03-15T13:27:36Z', -36)` <p> **Número do parâmetro**: 1 <p> **Nome**: Carimbo de data/hora <p> **Descrição**: Obrigatório. Uma cadeia de caracteres que contém a hora. <p> **Número do parâmetro**: 2 <p> **Nome**: Segundos <p> **Descrição**: Obrigatório. O número de segundos a serem adicionados. Pode ser negativo para subtrair segundos. <p> **Número do parâmetro**: 3 <p> **Nome**: Formato <p> **Descrição**: Opcional. Um [caractere do especificador de formato único](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|addminutes|Adiciona um número inteiro de minutos a um carimbo de data/hora de cadeia de caracteres passado. O número de minutos pode ser positivo ou negativo. Por padrão, o resultado é uma cadeia de caracteres no formato ISO 8601 ("o"), a menos que um especificador de formato seja fornecido. Por exemplo: `2015-03-15T14:00:36Z`: <p>`addminutes('2015-03-15T13:27:36Z', 33)` <p> **Número do parâmetro**: 1 <p> **Nome**: Carimbo de data/hora <p> **Descrição**: Obrigatório. Uma cadeia de caracteres que contém a hora. <p> **Número do parâmetro**: 2 <p> **Nome**: Minutos <p> **Descrição**: Obrigatório. O número de minutos a serem adicionados. Pode ser negativo para subtrair minutos. <p> **Número do parâmetro**: 3 <p> **Nome**: Formato <p> **Descrição**: Opcional. Um [caractere do especificador de formato único](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|addhours|Adiciona um número inteiro de horas a um carimbo de data/hora de cadeia de caracteres passado. O número de horas pode ser positivo ou negativo. Por padrão, o resultado é uma cadeia de caracteres no formato ISO 8601 ("o"), a menos que um especificador de formato seja fornecido. Por exemplo: `2015-03-16T01:27:36Z`: <p>`addhours('2015-03-15T13:27:36Z', 12)` <p> **Número do parâmetro**: 1 <p> **Nome**: Carimbo de data/hora <p> **Descrição**: Obrigatório. Uma cadeia de caracteres que contém a hora. <p> **Número do parâmetro**: 2 <p> **Nome**: Horas <p> **Descrição**: Obrigatório. O número de horas a serem adicionadas. Pode ser negativo para subtrair horas. <p> **Número do parâmetro**: 3 <p> **Nome**: Formato <p> **Descrição**: Opcional. Um [caractere do especificador de formato único](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|adddays|Adiciona um número inteiro de dias a um carimbo de data/hora de cadeia de caracteres passado. O número de dias pode ser positivo ou negativo. Por padrão, o resultado é uma cadeia de caracteres no formato ISO 8601 ("o"), a menos que um especificador de formato seja fornecido. Por exemplo: `2015-02-23T13:27:36Z`: <p>`addseconds('2015-03-15T13:27:36Z', -20)` <p> **Número do parâmetro**: 1 <p> **Nome**: Carimbo de data/hora <p> **Descrição**: Obrigatório. Uma cadeia de caracteres que contém a hora. <p> **Número do parâmetro**: 2 <p> **Nome**: Dias <p> **Descrição**: Obrigatório. O número de dias a serem adicionados. Pode ser negativo para subtrair dias. <p> **Número do parâmetro**: 3 <p> **Nome**: Formato <p> **Descrição**: Opcional. Um [caractere do especificador de formato único](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|formatDateTime|Retorna uma cadeia de caracteres no formato de data. Por padrão, o resultado é uma cadeia de caracteres no formato ISO 8601 ("o"), a menos que um especificador de formato seja fornecido. Por exemplo: `2015-02-23T13:27:36Z`: <p>`formatDateTime('2015-03-15T13:27:36Z', 'o')` <p> **Número do parâmetro**: 1 <p> **Nome**: Data <p> **Descrição**: Obrigatório. Uma cadeia de caracteres que contém a data. <p> **Número do parâmetro**: 2 <p> **Nome**: Formato <p> **Descrição**: Opcional. Um [caractere do especificador de formato único](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|startOfHour|Retorna o início da hora em um carimbo de hora/data de cadeia de caracteres passado. Por exemplo `2017-03-15T13:00:00Z`:<br /><br /> `startOfHour('2017-03-15T13:27:36Z')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Carimbo de data/hora<br /><br /> **Descrição**: Obrigatório. Essa é uma cadeia de caracteres que contém a hora.<br /><br />**Número do parâmetro**: 2<br /><br /> **Nome**: Formato<br /><br /> **Descrição**: Opcional. Um [caractere do especificador de formato único](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|startOfDay|Retorna o início do dia para um carimbo de data/hora de cadeia de caracteres passado. Por exemplo `2017-03-15T00:00:00Z`:<br /><br /> `startOfDay('2017-03-15T13:27:36Z')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Carimbo de data/hora<br /><br /> **Descrição**: Obrigatório. Essa é uma cadeia de caracteres que contém a hora.<br /><br />**Número do parâmetro**: 2<br /><br /> **Nome**: Formato<br /><br /> **Descrição**: Opcional. Um [caractere do especificador de formato único](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.| 
|startOfMonth|Retorna o início do mês para um carimbo de data/hora de cadeia de caracteres passado. Por exemplo `2017-03-01T00:00:00Z`:<br /><br /> `startOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Carimbo de data/hora<br /><br /> **Descrição**: Obrigatório. Essa é uma cadeia de caracteres que contém a hora.<br /><br />**Número do parâmetro**: 2<br /><br /> **Nome**: Formato<br /><br /> **Descrição**: Opcional. Um [caractere do especificador de formato único](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.| 
|dayOfWeek|Retorna o componente de dia da semana de um carimbo de data/hora da cadeia de caracteres.  O domingo é 0, a segunda-feira é 1 e assim por diante. Por exemplo `3`:<br /><br /> `dayOfWeek('2017-03-15T13:27:36Z')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Carimbo de data/hora<br /><br /> **Descrição**: Obrigatório. Essa é uma cadeia de caracteres que contém a hora.| 
|dayOfMonth|Retorna o componente de dia do mês de um carimbo de data/hora da cadeia de caracteres. Por exemplo `15`:<br /><br /> `dayOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Carimbo de data/hora<br /><br /> **Descrição**: Obrigatório. Essa é uma cadeia de caracteres que contém a hora.| 
|dayOfYear|Retorna o componente de dia do ano de um carimbo de data/hora da cadeia de caracteres. Por exemplo `74`:<br /><br /> `dayOfYear('2017-03-15T13:27:36Z')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Carimbo de data/hora<br /><br /> **Descrição**: Obrigatório. Essa é uma cadeia de caracteres que contém a hora.| 
|ticks|Retorna a propriedade de tiques de um carimbo de data/hora da cadeia de caracteres. Por exemplo `1489603019`:<br /><br /> `ticks('2017-03-15T18:36:59Z')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Carimbo de data/hora<br /><br /> **Descrição**: Obrigatório. Essa é uma cadeia de caracteres que contém a hora.| 
  
### <a name="workflow-functions"></a>Funções de fluxo de trabalho  

Essas funções ajudarão a obter informações sobre o próprio fluxo de trabalho em tempo de execução.  
  
|Nome da função|Descrição|  
|-------------------|-----------------|  
|listCallbackUrl|Retorna uma cadeia de caracteres para chamada para invocar o gatilho ou ação. <p> **Observação**: Essa função só pode ser usada em uma **httpWebhook** e **apiConnectionWebhook**, não em uma **manual**, **recorrência**, **http**, ou **apiConnection**. <p>Por exemplo, a função `listCallbackUrl()` retorna: <p>`https://prod-01.westus.logic.azure.com:443/workflows/1235...ABCD/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxx...xxx` |  
|fluxo de trabalho|Essa função fornece todos os detalhes para o próprio fluxo de trabalho em tempo de execução. <p> As propriedades disponíveis no objeto de fluxo de trabalho são: <ul><li>`name`</li><li>`type`</li><li>`id`</li><li>`location`</li><li>`run`</li></ul> <p> O valor da propriedade `run` é um objeto com as seguintes propriedades: <ul><li>`name`</li><li>`type`</li><li>`id`</li></ul> <p>Consulte a [API Rest](http://go.microsoft.com/fwlink/p/?LinkID=525617) para obter detalhes sobre essas propriedades.<p> Por exemplo, para obter o nome da execução atual, use a expressão `"@workflow().run.name"`. |

## <a name="next-steps"></a>Próximas etapas

[Gatilhos e ações do fluxo de trabalho](logic-apps-workflow-actions-triggers.md)
