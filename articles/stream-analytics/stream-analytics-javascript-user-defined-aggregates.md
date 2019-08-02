---
title: Agregações do JavaScript definidas pelo usuário no Azure Stream Analytics
description: Este artigo descreve como executar a mecânica de consulta avançada com agregações definidas pelo usuário do JavaScript no Azure Stream Analytics.
services: stream-analytics
author: rodrigoamicrosoft
ms.author: rodrigoa
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.openlocfilehash: 6c590ae62e080a6681e49c87264089f9a5f4ce2f
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489530"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates"></a>Azure Stream Analytics agregações definidas pelo usuário do JavaScript
 
O Azure Stream Analytics dá suporte a UDA (agregações definidas pelo usuário) escritas em JavaScript, permitindo a você implementar uma lógica de negócios com estado complexa. Dentro da UDA, você tem controle total sobre a estrutura de dados de estado, acumulação de estado, desacumulação de estado e computação de resultados de agregação. O artigo apresenta duas interfaces diferentes de UDA do JavaScript, as etapas para criar uma UDA e como usar a UDA com operações com base em janela na consulta do Stream Analytics.

## <a name="javascript-user-defined-aggregates"></a>Agregações definidas pelo usuário do JavaScript

Uma agregação definida pelo usuário é usada na parte superior de uma especificação de janela de tempo para agregar os eventos dessa janela e produzir um resultado de valor único. Há dois tipos de interfaces de UDA com suporte no Stream Analytics atualmente, AccumulateOnly e AccumulateDeaccumulate. Os dois tipos de UDA podem ser usados por em cascata, salto, deslizante e janela de sessão. AccumulateDeaccumulate UDA tem um desempenho melhor do que o AccumulateOnly UDA quando usado junto com a janela de salto, deslizante e sessão. Escolha um dos dois tipos de acordo com o algoritmo que você usa.

### <a name="accumulateonly-aggregates"></a>Agregações AccumulateOnly

Agregações AccumulateOnly só podem se acumular novos eventos para seu estado, pois o algoritmo não permite desacumulação de valores. Escolha esse tipo de agregação quando for impossível implementar a desacumulação de uma informação de evento do valor de estado. Veja a seguir o modelo de JavaScript para agregações AccumulatOnly:

```JavaScript
// Sample UDA which state can only be accumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

### <a name="accumulatedeaccumulate-aggregates"></a>Agregações AccumulateDeaccumulate

Agregações AccumulateDeaccumulate possibilitam a desacumulação de um valor previamente acumulado do estado, por exemplo, remover um par chave-valor de uma lista de valores de evento ou subtrair um valor de um estado de agregação de soma. Veja a seguir o modelo de JavaScript para agregações AccumulateDeaccumulate:

```JavaScript
// Sample UDA which state can be accumulated and deaccumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.deaccumulate = function (value, timestamp) {
        this.state -= value;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

## <a name="uda---javascript-function-declaration"></a>UDA – Declaração da função JavaScript

Cada UDA do JavaScript é definida por uma declaração de objeto Function. Veja a seguir os principais elementos de uma definição de UDA.

### <a name="function-alias"></a>Alias da função

O alias da função é o identificador da UDA. Quando chamado em consultas do Stream Analytics, sempre use o alias da UDA junto com "uda." prefixo.

### <a name="function-type"></a>Tipo de função

Para a UDA, o tipo da função deve ser **UDA do JavaScript**.

### <a name="output-type"></a>Tipo de saída

Um tipo específico com suporte no trabalho do Stream Analytics ou “Any” se desejar lidar com o tipo na sua consulta.

### <a name="function-name"></a>Nome da função

O nome deste objeto Function. O nome da função deve corresponder ao alias UDA.

### <a name="method---init"></a>Método – init()

O método init() inicializa o estado da agregação. Esse método é chamado quando a janela inicia.

### <a name="method--accumulate"></a>Método – accumulate()

O método accumulate() calcula o estado da UDA com base no estado anterior e os valores atuais do evento. Esse método é chamado quando um evento entra em uma janela de tempo (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW ou SESSIONWINDOW).

### <a name="method--deaccumulate"></a>Método – deaccumulate()

O método deaccumulate() recalcula o estado com base no estado anterior e os valores atuais do evento. Esse método é chamado quando um evento deixa um SLIDINGWINDOW ou um SESSIONWINDOW.

### <a name="method--deaccumulatestate"></a>Método – deaccumulateState()

O método deaccumulateState() recalcula o estado com base no estado anterior e o estado de um salto. Esse método é chamado quando um conjunto de eventos deixa uma HOPPINGWINDOW.

### <a name="method--computeresult"></a>Método – computeResult()

O método computeResult() retorna resultados de agregação com base no estado atual. Esse método é chamado no final de uma janela de tempo (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW ou SESSIONWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>Tipos de dados de entrada e saída com suporte na UDA do JavaScript
Para ver os tipos de dados de UDA do JavaScript, consulte a seção **Conversão de tipo do Stream Analytics e do JavaScript** de [Integrar UDFs do JavaScript](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Adicionar uma UDA do JavaScript do Portal do Azure

Abaixo mostramos passo a passo o processo de criação de uma UDA no Portal. O exemplo que usamos aqui é a computação de média de tempo ponderada.

Agora vamos criar uma UDA do JavaScript em um trabalho ASA existente seguindo as etapas.

1. Faça logon no Portal do Azure e localize o trabalho existente do Stream Analytics.
1. Em seguida, clique no link de funções em **TOPOLOGIA DO TRABALHO**.
1. Clique no ícone **Adicionar** para adicionar uma nova função.
1. Na exibição Nova Função, selecione **UDA do JavaScript** como o Tipo de Função e você verá um modelo de UDA padrão aparecer no editor.
1. Preencha “TWA” como o alias da UDA e altere a implementação da função como mostrado a seguir:

    ```JavaScript
    // Sample UDA which calculate Time-Weighted Average of incoming values.
    function main() {
        this.init = function () {
            this.totalValue = 0.0;
            this.totalWeight = 0.0;
        }

        this.accumulate = function (value, timestamp) {
            this.totalValue += value.level * value.weight;
            this.totalWeight += value.weight;

        }

        // Uncomment below for AccumulateDeaccumulate implementation
        /*
        this.deaccumulate = function (value, timestamp) {
            this.totalValue -= value.level * value.weight;
            this.totalWeight -= value.weight;
        }

        this.deaccumulateState = function (otherState){
            this.state -= otherState.state;
            this.totalValue -= otherState.totalValue;
            this.totalWeight -= otherState.totalWeight;
        }
        */

        this.computeResult = function () {
            if(this.totalValue == 0) {
                result = 0;
            }
            else {
                result = this.totalValue/this.totalWeight;
            }
            return result;
        }
    }
    ```

1. Depois de clicar no botão "Salvar", a UDA aparecerá na lista de funções.

1. Clique na nova função "TWA" e você poderá verificar a definição da função.

## <a name="calling-javascript-uda-in-asa-query"></a>Chamando a UDA do JavaScript em uma consulta ASA

No portal do Azure, abra o trabalho, edite a consulta e chame a função TWA() com um prefixo de carta de ordem "uda.". Por exemplo:

```SQL
WITH value AS
(
    SELECT
    NoiseLevelDB as level,
    DurationSecond as weight
FROM
    [YourInputAlias] TIMESTAMP BY EntryTime
)
SELECT
    System.Timestamp as ts,
    uda.TWA(value) as NoseDoseTWA
FROM value
GROUP BY TumblingWindow(minute, 5)
```

## <a name="testing-query-with-uda"></a>Testando a consulta UDA

Crie um arquivo JSON local com o conteúdo abaixo, carregue o arquivo para o trabalho do Stream Analytics e teste a consulta acima.

```JSON
[
  {"EntryTime": "2017-06-10T05:01:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 22.0},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 81, "DurationSecond": 37.8},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 85, "DurationSecond": 26.3},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 95, "DurationSecond": 13.7},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 88, "DurationSecond": 10.3},
  {"EntryTime": "2017-06-10T05:05:00-07:00", "NoiseLevelDB": 103, "DurationSecond": 5.5},
  {"EntryTime": "2017-06-10T05:06:00-07:00", "NoiseLevelDB": 99, "DurationSecond": 23.0},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 1.76},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 79, "DurationSecond": 17.9},
  {"EntryTime": "2017-06-10T05:08:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 27.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 91, "DurationSecond": 17.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 115, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 28.3},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 55, "DurationSecond": 18.2},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 25.8},
  {"EntryTime": "2017-06-10T05:11:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 11.4},
  {"EntryTime": "2017-06-10T05:12:00-07:00", "NoiseLevelDB": 89, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 112, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 9.7},
  {"EntryTime": "2017-06-10T05:18:00-07:00", "NoiseLevelDB": 96, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 0.99},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 113, "DurationSecond": 25.1},
  {"EntryTime": "2017-06-10T05:22:00-07:00", "NoiseLevelDB": 110, "DurationSecond": 5.3}
]
```

## <a name="get-help"></a>Obter ajuda

Para obter ajuda adicional, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do gerenciamento do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
