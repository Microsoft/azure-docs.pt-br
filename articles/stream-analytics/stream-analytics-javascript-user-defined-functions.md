---
title: "Funções definidas pelo usuário do JavaScript do Stream Analytics do Azure | Microsoft Docs"
description: "Executar o mecanismo de consulta avançada com funções definidas pelo usuário do JavaScript"
keywords: "javascript, funções definidas pelo usuário, udf"
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: e8c1c784a598416b478d1430258201053185fdee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stream-analytics-javascript-user-defined-functions"></a>Funções definidas pelo usuário do JavaScript do Stream Analytics do Azure
O Stream Analytics do Azure dá suporte a funções definidas pelo usuário gravadas em JavaScript. Com o conjunto avançado dos métodos **String**, **RegExp**, **Math**, **Array** e **Date** fornecidos pelo JavaScript, as transformações de dados complexas com trabalhos do Stream Analytics se tornam mais fáceis de serem criadas.

## <a name="javascript-user-defined-functions"></a>Funções definidas pelo usuário de JavaScript
As funções definidas pelo usuário de JavaScript dão suporte a funções escalares sem monitoração de estado somente para computação que não requerem conectividade externa. O valor retornado de uma função pode ser apenas um valor escalar (único). Depois de adicionar uma função definida pelo usuário do JavaScript a um trabalho, você poderá usar a função em qualquer lugar na consulta, como uma função escalar interna.

Aqui estão alguns cenários nos quais as funções definidas pelo usuário JavaScript podem ser úteis:
* Análise e manipulação de cadeia de caracteres com funções de expressão regular, por exemplo, **Regexp_Replace()** e **Regexp_Extract()**
* Decodificação e codificação de dados, por exemplo, conversão de binário em hexadecimal
* Executando cálculos matemáticos com funções **Matemáticas** do JavaScript
* Executando operações de matriz como classificar, juntar, localizar e preencher

Aqui estão ações que não podem ser realizadas com uma função definida pelo usuário do JavaScript no Stream Analytics:
* Chamar pontos de extremidade REST externos, por exemplo, executando pesquisa inversa de IP ou extração de dados de referência de uma fonte externa
* Executar serialização ou desserialização de formato de evento personalizado em entradas/saídas
* Criar agregações personalizadas

Embora funções como **Date.GetDate()** ou **Math.random()** não estejam bloqueadas na definição de funções, você deve evitar usá-las. Essas funções **não** retornam o mesmo resultado sempre que você as chama e o serviço Stream Analytics do Azure não mantém um diário das invocações da função e dos resultados retornados. Se uma função retornar resultados diferentes nos mesmos eventos, a capacidade de repetição não será garantida quando um trabalho for reiniciado por você ou pelo serviço Stream Analytics.

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Adicionar uma função definida pelo usuário do JavaScript no portal do Azure
Para criar uma função definida pelo usuário do JavaScript simples em um trabalho do Stream Analytics existente, siga essas etapas:

1.  No portal do Azure, encontre seu trabalho do Stream Analytics.
2.  Em **TOPOLOGIA DO TRABALHO**, selecione sua função. Uma lista vazia de funções é exibida.
3.  Para criar uma nova função definida pelo usuário, selecione **Adicionar**.
4.  Na folha **Nova Função**, para **Tipo de Função**, selecione **JavaScript**. Um modelo de função padrão aparece no editor.
5.  Para o **alias da UDF**, insira **hex2Int** e altere a implementação da função, conforme mostrado a seguir:

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Selecione **Salvar**. Sua função é exibida na lista de funções.
7.  Selecione a nova função **hex2Int** e verifique a definição de função. Todas as funções têm um prefixo **UDF** adicionado ao alias de função. Você precisa *incluir o prefixo* ao chamar a função na consulta do Stream Analytics. Nesse caso, você chama **UDF.hex2Int**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Chamar uma função definida pelo usuário do JavaScript em uma consulta

1. No editor de consulta, em **TOPOLOGIA DO TRABALHO**, selecione **Consulta**.
2.  Edite sua consulta e, em seguida, chame a função definida pelo usuário da seguinte forma:

    ```
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Para carregar o arquivo de dados de exemplo, clique com o botão direito do mouse na entrada do trabalho.
4.  Para testar sua consulta, selecione **Teste**.


## <a name="supported-javascript-objects"></a>Objetos JavaScript com suporte
As funções definidas pelo usuário do JavaScript do Stream Analytics do Azure dão suporte a objetos JavaScript internos e padrão. Para obter uma lista desses objetos, consulte [Objetos Globais](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Conversão de tipo do Stream Analytics e JavaScript

Há diferenças nos tipos com suporte na linguagem de consulta do Stream Analytics e no JavaScript. Esta tabela lista os mapeamentos de conversão entre os dois:

Análise de fluxo | JavaScript
--- | ---
bigint | Número (o JavaScript pode representar apenas o inteiro até 2^53 exatamente)
DateTime | Data (o JavaScript dá suporte somente a milissegundos)
double | Número
nvarchar(MAX) | Cadeia de caracteres
Registro | Objeto
Matriz | Matriz
NULO | Nulo


Aqui estão as conversões de JavaScript para Stream Analytics:


JavaScript | Análise de fluxo
--- | ---
Número | Bigint (se o número for arredondado e estiver entre long.MinValue e long.MaxValue; caso contrário, será dobrado)
Data | DateTime
Cadeia de caracteres | nvarchar(MAX)
Objeto | Registro
Matriz | Matriz
Null, Undefined | NULO
Qualquer outro tipo (por exemplo, uma função ou um erro) | Sem suporte (resulta em erro de tempo de execução)

## <a name="troubleshooting"></a>Solucionar problemas
Os erros de tempo de execução do JavaScript são considerados fatais e exibidos no Log de atividades. Para recuperar o log, no Portal do Azure, vá para o seu trabalho e clique em **Log de atividades**.


## <a name="other-javascript-user-defined-function-patterns"></a>Outros padrões de função definida pelo usuário do JavaScript

### <a name="write-nested-json-to-output"></a>Gravar JSON aninhado na saída
Se você tiver uma etapa de processamento de acompanhamento que usa a saída de trabalho do Stream Analytics como entrada e seja necessário um formato JSON, grave uma cadeia de caracteres JSON em uma saída. O exemplo a seguir chama a função **JSON.stringify()** para empacotar todos os pares nome-valor da entrada e gravá-las como um valor de cadeia de caracteres único na saída.

**Definição de funções definidas pelo usuário de JavaScript:**

```
function main(x) {
return JSON.stringify(x);
}
```

**Consulta de exemplo:**
```
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="get-help"></a>Obter ajuda
Para obter ajuda adicional, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do gerenciamento do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
