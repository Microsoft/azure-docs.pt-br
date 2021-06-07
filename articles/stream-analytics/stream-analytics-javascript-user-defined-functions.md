---
title: Funções definidas pelo usuário do JavaScript do Stream Analytics do Azure
description: Este artigo é uma introdução às funções definidas pelo usuário do JavaScript no Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc, devx-track-js
ms.date: 12/15/2020
ms.openlocfilehash: 70015ef24039694789ce96a6c4853221fe2377c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020376"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Funções definidas pelo usuário do JavaScript no Azure Stream Analytics
 
O Stream Analytics do Azure dá suporte a funções definidas pelo usuário gravadas em JavaScript. Com o conjunto avançado dos métodos **String**, **RegExp**, **Math**, **Array** e **Date** fornecidos pelo JavaScript, as transformações de dados complexas com trabalhos do Stream Analytics se tornam mais fáceis de serem criadas.

## <a name="overview"></a>Visão geral

As funções definidas pelo usuário do JavaScript dão suporte a funções escalares sem estado somente para computação que não requerem conectividade externa. O valor retornado de uma função pode ser apenas um valor escalar (único). Depois de adicionar uma função definida pelo usuário do JavaScript a um trabalho, você poderá usar a função em qualquer lugar na consulta, como uma função escalar interna.

Aqui estão alguns cenários nos quais as funções definidas pelo usuário JavaScript podem ser úteis:
* Análise e manipulação de cadeia de caracteres com funções de expressão regular, por exemplo, **Regexp_Replace()** e **Regexp_Extract()**
* Decodificação e codificação de dados, por exemplo, conversão de binário em hexadecimal
* Efetuar cálculos matemáticos com funções **matemáticas** do JavaScript
* Executar operações de matriz como classificar, unir, localizar e preencher

Aqui estão ações que não podem ser executadas com uma função definida pelo usuário do JavaScript no Stream Analytics:
* Chamar pontos de extremidade REST externos, por exemplo, executar pesquisa inversa de IP ou extrair de dados de referência de uma fonte externa
* Executar serialização ou desserialização de formato de evento personalizado em entradas/saídas
* Criar agregações personalizadas

Embora funções como **Date.GetDate()** ou **Math.random()** não estejam bloqueadas na definição de funções, você deve evitar usá-las. Essas funções **não** retornam o mesmo resultado sempre que você as chama e o serviço Azure Stream Analytics não mantém um diário das invocações da função e dos resultados retornados. Se uma função retornar resultados diferentes nos mesmos eventos, a capacidade de repetição não será garantida quando um trabalho for reiniciado por você ou pelo serviço Stream Analytics.

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>Adicionar uma função definida pelo usuário do JavaScript ao seu trabalho

> [!NOTE]
> Essas etapas funcionam nos trabalhos do Stream Analytics configurados para serem executados na nuvem. Se o trabalho do Stream Analytics é configurado para ser executado no Azure IoT Edge, use, em vez disso, o Visual Studio e [grave a função definida pelo usuário usando C# ](stream-analytics-edge-csharp-udf.md).

Para criar uma função definida pelo usuário do JavaScript em seu trabalho do Stream Analytics, selecione **Funções** em **Topologia do Trabalho**. Em seguida, selecione **UDF do JavaScript** no menu suspenso **+Adicionar**. 

![Adicionar UDF do JavaScript](./media/javascript/stream-analytics-jsudf-add.png)

Você deve fornecer as propriedades a seguir e selecionar **Salvar**.

|Propriedade|Descrição|
|--------|-----------|
|Alias da função|Insira um nome para invocar a função em sua consulta.|
|Tipo de saída|Tipo que será retornado pela função definida pelo usuário do JavaScript à sua consulta do Stream Analytics.|
|Definição de função|Implementação da sua função JavaScript que será executada sempre que o UDF for invocado de sua consulta.|

## <a name="test-and-troubleshoot-javascript-udfs"></a>Testar e solucionar problemas de UDFs do JavaScript 

Você pode testar e depurar a lógica de UDF do JavaScript em qualquer navegador. Atualmente, não há suporte para a depuração e o teste da lógica dessas funções definidas pelo usuário no portal Stream Analytics. Depois que a função funcionar conforme o esperado, você poderá adicioná-la ao trabalho do Stream Analytics conforme mencionado acima e, em seguida, chamá-la diretamente da sua consulta. Você pode testar sua lógica de consulta com a UDF do JavaScript usando as [ferramentas do Stream Analytics para Visual Studio](./stream-analytics-tools-for-visual-studio-install.md).

Os erros de runtime do JavaScript são considerados fatais e exibidos no Log de atividades. Para recuperar o log, no Portal do Azure, vá para o seu trabalho e clique em **Log de atividades**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Chamar uma função definida pelo usuário do JavaScript em uma consulta

Você pode facilmente invocar sua função JavaScript em sua consulta usando o alias de função prefixado com **udf**. Veja um exemplo de uma UDF do JavaScript que converte valores hexadecimais em inteiros sendo invocados em uma consulta do Stream Analytics.

```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
```

## <a name="supported-javascript-objects"></a>Objetos JavaScript com suporte

As funções definidas pelo usuário do JavaScript do Stream Analytics do Azure dão suporte a objetos JavaScript internos e padrão. Para obter uma lista desses objetos, consulte [Objetos Globais](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Conversão de tipo do Stream Analytics e JavaScript

Há diferenças nos tipos com suporte na linguagem de consulta do Stream Analytics e no JavaScript. Esta tabela lista os mapeamentos de conversão entre os dois:

Stream Analytics | JavaScript
--- | ---
BIGINT | Número (o JavaScript pode representar apenas o inteiro até 2^53 exatamente)
Datetime | Data (o JavaScript dá suporte somente a milissegundos)
double | Número
nvarchar(MAX) | String
Record | Objeto
Array | Array
NULO | Nulo

Aqui estão as conversões de JavaScript para Stream Analytics:

JavaScript | Stream Analytics
--- | ---
Número | Bigint (se o número for arredondado e estiver entre long.MinValue e long.MaxValue; caso contrário, será dobrado)
Data | Datetime
String | nvarchar(MAX)
Objeto | Record
Array | Array
Null, Undefined | NULO
Qualquer outro tipo (por exemplo, uma função ou um erro) | Sem suporte (resulta em erro de runtime)

A linguagem JavaScript diferencia maiúsculas de minúsculas, e as maiúsculas e minúsculas dos campos de objeto no código JavaScript devem coincidir com o uso de maiúsculas e minúsculas dos campos nos dados de entrada. Os trabalhos com o nível de compatibilidade 1.0 converterão os campos da instrução SQL SELECT em letras minúsculas. No nível de compatibilidade 1.1 e superior, os campos da instrução SELECT terão as mesmas maiúsculas e minúsculas conforme especificado na consulta SQL.

## <a name="other-javascript-user-defined-function-patterns"></a>Outros padrões de função definida pelo usuário do JavaScript

### <a name="write-nested-json-to-output"></a>Gravar JSON aninhado na saída

Se você tiver uma etapa de processamento de acompanhamento que usa a saída de trabalho do Stream Analytics como entrada e seja necessário um formato JSON, grave uma cadeia de caracteres JSON em uma saída. O exemplo a seguir chama a função **JSON.stringify()** para empacotar todos os pares nome-valor da entrada e gravá-las como um valor de cadeia de caracteres único na saída.

**Definição de funções definidas pelo usuário de JavaScript:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Consulta de exemplo:**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

### <a name="cast-string-to-json-object-to-process"></a>Converter uma cadeia de caracteres em um objeto JSON para processamento

Se você tiver um campo de cadeia de caracteres que seja JSON e quiser convertê-lo em um objeto JSON para processamento em uma UDF do JavaScript, use a função **JSON.parse()** para criar um objeto JSON que possa ser usado em seguida.

**Definição de funções definidas pelo usuário de JavaScript:**

```javascript
function main(x) {
var person = JSON.parse(x);  
return person.name;
}
```

**Consulta de exemplo:**
```SQL
SELECT
    UDF.getName(input) AS Name
INTO
    output
FROM
    input
```

### <a name="use-trycatch-for-error-handling"></a>Usar try/catch para tratamento de erro

Os blocos try/catch podem ajudar você a identificar problemas em dados de entrada malformados transmitidos para uma UDF do JavaScript.

**Definição de funções definidas pelo usuário de JavaScript:**

```javascript
function main(input, x) {
    var obj = null;

    try{
        obj = JSON.parse(x);
    }catch(error){
        throw input;
    }
    
    return obj.Value;
}
```

**Exemplo de consulta: transmitir o registro inteiro como o primeiro parâmetro, de modo que ele possa ser retornado em caso de erro.**
```SQL
SELECT
    A.context.company AS Company,
    udf.getValue(A, A.context.value) as Value
INTO
    output
FROM
    input A
```

### <a name="tolocalestring"></a>toLocaleString()
O método **toLocaleString** em JavaScript pode ser usado para retornar uma cadeia de caracteres com sensibilidade para linguagem que representa os dados de data e hora de onde esse método é chamado.
Embora o Azure Stream Analytics aceite apenas data e hora em UTC como carimbo de data/hora do sistema, esse método pode ser usado para converter o carimbo de data/hora do sistema em outra localidade e fuso horário.
Esse método segue o mesmo comportamento de implementação que o disponível no Internet Explorer.

**Definição de funções definidas pelo usuário de JavaScript:**

```javascript
function main(datetime){
    const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
    return event.toLocaleDateString('de-DE', options);
}
```

**Exemplo de consulta: Passar um datetime como valor de entrada**
```SQL
SELECT
    udf.toLocaleString(input.datetime) as localeString
INTO
    output
FROM
    input
```

A saída dessa consulta será o datetime de entrada em **de-DE** com as opções fornecidas.
```
Samstag, 28. Dezember 2019
```

## <a name="user-logging"></a>Registro em log do usuário
O mecanismo de registro em log permite que você capture informações personalizadas enquanto um trabalho está em execução. Você pode usar dados de log para depurar ou avaliar a exatidão do código personalizado em tempo real. Esse mecanismo está disponível por meio do método Console.Log().

```javascript
console.log('my error message');
```

Você pode acessar mensagens de log por meio de [logs de diagnóstico](data-errors.md).
## <a name="next-steps"></a>Próximas etapas

* [UDF do Machine Learning](./machine-learning-udf.md)
* [UDF do C#](./stream-analytics-edge-csharp-udf-methods.md)
