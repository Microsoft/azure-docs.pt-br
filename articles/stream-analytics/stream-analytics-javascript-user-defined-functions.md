---
title: 'Tutorial: Funções definidas pelo usuário do JavaScript do Stream Analytics do Azure | Microsoft Docs '
description: Neste tutorial, execute o mecanismo de consulta avançada com funções definidas pelo usuário do JavaScript
services: stream-analytics
author: rodrigoamicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
ms.date: 04/01/2018
ms.openlocfilehash: c7414ee159303465d6698ce9c47d04ba37c0c46e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329372"
---
# <a name="tutorial-azure-stream-analytics-javascript-user-defined-functions"></a>Tutorial: Funções definidas pelo usuário do JavaScript do Stream Analytics do Azure
 
O Stream Analytics do Azure dá suporte a funções definidas pelo usuário gravadas em JavaScript. Com o conjunto avançado dos métodos **String**, **RegExp**, **Math**, **Array** e **Date** fornecidos pelo JavaScript, as transformações de dados complexas com trabalhos do Stream Analytics se tornam mais fáceis de serem criadas.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Definir uma função definida pelo usuário de JavaScript
> * Adicionar a função ao Portal
> * Definir uma consulta que executa a função

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

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
Para criar uma função simples do JavaScript definida por usuário sob um trabalho do Stream Analytics existente, siga estas etapas:

> [!NOTE]
> Essas etapas funcionam nos trabalhos do Stream Analytics configurados para serem executados na nuvem. Se o trabalho do Stream Analytics é configurado para ser executado no Azure IoT Edge, use, em vez disso, o Visual Studio e [grave a função definida pelo usuário usando C# ](stream-analytics-edge-csharp-udf.md).

1.  No portal do Azure, encontre seu trabalho do Stream Analytics.

2. No cabeçalho **Topologia de trabalhos**, selecione **Funções**. Uma lista vazia de funções é exibida.

3.  Para criar uma nova função definida pelo usuário, selecione **+ Adicionar**.

4.  Na folha **Nova Função**, para **Tipo de Função**, selecione **JavaScript**. Um modelo de função padrão aparece no editor.

5.  Para o **alias da UDF**, insira **hex2Int** e altere a implementação da função, conforme mostrado a seguir:

    ```javascript
    // Convert Hex value to integer.
    function hex2Int(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Clique em **Salvar**. Sua função é exibida na lista de funções.
7.  Selecione a nova função **hex2Int** e verifique a definição de função. Todas as funções têm um prefixo **UDF** adicionado ao alias de função. Você precisa *incluir o prefixo* ao chamar a função na consulta do Stream Analytics. Nesse caso, você chama **UDF.hex2Int**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Chamar uma função definida pelo usuário do JavaScript em uma consulta

1. No editor de consulta, sob o cabeçalho de **Topologia de trabalho**, selecione **Consulta**.
2.  Edite sua consulta e, em seguida, chame a função definida pelo usuário da seguinte forma:

    ```SQL
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

Stream Analytics | JavaScript
--- | ---
bigint | Número (o JavaScript pode representar apenas o inteiro até 2^53 exatamente)
Datetime | Data (o JavaScript dá suporte somente a milissegundos)
double | Número
nvarchar(MAX) | string
Registro | Objeto
Matriz | Matriz
NULO | Nulo


Aqui estão as conversões de JavaScript para Stream Analytics:


JavaScript | Stream Analytics
--- | ---
Número | Bigint (se o número for arredondado e estiver entre long.MinValue e long.MaxValue; caso contrário, será dobrado)
Data | Datetime
string | nvarchar(MAX)
Objeto | Registro
Matriz | Matriz
Null, Undefined | NULO
Qualquer outro tipo (por exemplo, uma função ou um erro) | Sem suporte (resulta em erro de tempo de execução)

A linguagem JavaScript diferencia maiúsculas de minúsculas e maiúsculas e minúsculas dos campos de objeto no código JavaScript devem coincidir com o uso de maiúsculas e minúsculas dos campos nos dados de entrada. Observe que os trabalhos com o nível de compatibilidade 1.0 convertem os campos da instrução do SQL SELECT em minúsculas. No nível de compatibilidade 1.1 e superior, os campos da instrução SELECT terão as mesmas maiúsculas e minúsculas conforme especificado na consulta SQL.

## <a name="troubleshooting"></a>solução de problemas
Os erros de tempo de execução do JavaScript são considerados fatais e exibidos no Log de atividades. Para recuperar o log, no Portal do Azure, vá para o seu trabalho e clique em **Log de atividades**.


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
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua o grupo de recursos, o trabalho de streaming e todos os recursos relacionados. A exclusão do trabalho evita a cobrança das unidades de streaming consumidas por ele. Se você está planejando usar o trabalho no futuro, pode interrompê-lo e reiniciar mais tarde, quando necessário. Se você não for mais usar o trabalho, exclua todos os recursos criados neste início rápido usando as seguintes etapas:

1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e depois clique no nome do recurso criado.  
2. Em sua página de grupo de recursos, clique em **Excluir**, digite o nome do recurso para excluir na caixa de texto e depois clique em **Excluir**.

## <a name="get-help"></a>Obter ajuda
Para obter ajuda adicional, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um trabalho do Stream Analytics que executa uma função simples definida pelo usuário de JavaScript. Para saber mais sobre o Stream Analytics, confira os artigos de cenários em tempo real:

> [!div class="nextstepaction"]
> [Análise de sentimento do Twitter em tempo real no Azure Stream Analytics](stream-analytics-twitter-sentiment-analysis-trends.md)
