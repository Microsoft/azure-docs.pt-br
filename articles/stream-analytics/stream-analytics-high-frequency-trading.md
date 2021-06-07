---
title: Negociação de alta frequência usando Azure Stream Analytics
description: Como fazer um treinamento e pontuação do modelo de regressão linear em um trabalho do Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2021
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: f632c916c3de61b94532e96be23da511ad5863ea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593026"
---
# <a name="high-frequency-trading-simulation-with-stream-analytics"></a>Simulação de transação de alta frequência com o Stream Analytics
A combinação da linguagem SQL e das funções UDFs (funções definidas pelo usuário) e UDAs (agregações definidas pelo usuário) de JavaScript no Azure Stream Analytics permite aos usuários a execução de análises avançadas. As análises avançadas podem incluir treinamento de aprendizado de máquina online e pontuação, bem como a simulação de processo com estado. Este artigo descreve como executar regressão linear em um trabalho do Azure Stream Analytics que faz classificação e pontuação contínuas em um cenário de transação de alta frequência.

## <a name="high-frequency-trading"></a>Transação de alta frequência
O fluxo lógico de transação de alta frequência é sobre:
1. Obter cotações em tempo real de uma troca de segurança.
2. Criar um modelo de previsão em torno das cotações, para podermos antecipar a movimentação de preços.
3. Fazer pedidos de compra ou venda para ganhar dinheiro com a previsão bem-sucedida de movimentos de preço. 

Como resultado, precisamos:
* De um feed de cotação em tempo real.
* De um modelo preditivo que possa operar com cotações em tempo real.
* Uma simulação de negociação que demonstra o lucro ou perda do algoritmo de negociação.

### <a name="real-time-quote-feed"></a>Feed de cotação em tempo real
O IEX oferece [cotações de compra e venda em tempo real](https://iextrading.com/developer/docs/#websockets) usando socket.io. Um programa de console simples pode ser escrito para receber cotações em tempo real e enviar por push para o Hubs de Eventos do Azure como uma fonte de dados. O código a seguir é um esqueleto do programa. O código omite o tratamento de erros para fins de brevidade. Você também precisa incluir pacotes NuGet SocketIoClientDotNet e WindowsAzure.ServiceBus no seu projeto.

```csharp
using Quobject.SocketIoClientDotNet.Client;
using Microsoft.ServiceBus.Messaging;
var symbols = "msft,fb,amzn,goog";
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
var socket = IO.Socket("https://ws-api.iextrading.com/1.0/tops");
socket.On(Socket.EVENT_MESSAGE, (message) =>
{
    eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes((string)message)));
});
socket.On(Socket.EVENT_CONNECT, () =>
{
    socket.Emit("subscribe", symbols);
});
```

Aqui estão alguns exemplos de eventos gerados:

```json
{"symbol":"MSFT","marketPercent":0.03246,"bidSize":100,"bidPrice":74.8,"askSize":300,"askPrice":74.83,volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,lastUpdated":1506953357170,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04825,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953357633,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"MSFT","marketPercent":0.03244,"bidSize":100,"bidPrice":74.8,"askSize":100,"askPrice":74.83,volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,lastUpdated":1506953359118,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"FB","marketPercent":0.01211,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.67,volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,lastUpdated":1506953359641,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04795,"bidSize":100,"bidPrice":959.19,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953360949,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"FB","marketPercent":0.0121,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.7,volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,lastUpdated":1506953362205,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04795,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953362629,"sector":"softwareservices","securityType":"commonstock"}
```

>[!NOTE]
>O carimbo de data/hora do evento é **lastUpdated**, em época.

### <a name="predictive-model-for-high-frequency-trading"></a>Modelo preditivo para a transação de alta frequência
Para fins de demonstração, usamos um modelo linear descrito por Darryl Shen [neste documento](https://docplayer.net/23038840-Order-imbalance-based-strategy-in-high-frequency-trading.html).

Desequilíbrio de ordem de volume (VOI) é uma função do preço de compra e venda atual e do volume e preço de compra e venda do último tique. O papel identifica a correlação entre VOI e a movimentação de preços futura. Ele cria um modelo linear entre os últimos 5 valores de VOI, e a mudança de preço nos próximos 10 tiques. O modelo é treinado usado os dados do dia anterior com regressão linear. 

Depois, o modelo treinado é usado para fazer previsões de alteração do preço nas cotações no dia da negociação em tempo real. Quando uma alteração de preço grande o suficiente é prevista, uma transação é executada. Dependendo da configuração do limite, milhares de transações podem ser esperadas para uma única ação em um dia de negociação.

![Definição de desequilíbrio de ordem de volume](./media/stream-analytics-high-frequency-trading/volume-order-imbalance-formula.png)

Agora, vamos expressar as operações de treinamento e previsão em um trabalho do Azure Stream Analytics.

Primeiro, as entradas são apagadas. O tempo em época é convertido em datetime usando **DATEADD**. O **TRY_CAST** é usado para forçar os tipos de dados sem causar falhas na consulta. Sempre é uma boa prática converter os campos de entrada para os tipos de dados esperado, para que não haja nenhum comportamento inesperado na manipulação ou comparação dos campos.

```SQL
WITH
typeconvertedquotes AS (
    /* convert all input fields to proper types */
    SELECT
        System.Timestamp AS lastUpdated,
        symbol,
        DATEADD(millisecond, CAST(lastSaleTime as bigint), '1970-01-01T00:00:00Z') AS lastSaleTime,
        TRY_CAST(bidSize as bigint) AS bidSize,
        TRY_CAST(bidPrice as float) AS bidPrice,
        TRY_CAST(askSize as bigint) AS askSize,
        TRY_CAST(askPrice as float) AS askPrice,
        TRY_CAST(volume as bigint) AS volume,
        TRY_CAST(lastSaleSize as bigint) AS lastSaleSize,
        TRY_CAST(lastSalePrice as float) AS lastSalePrice
    FROM quotes TIMESTAMP BY DATEADD(millisecond, CAST(lastUpdated as bigint), '1970-01-01T00:00:00Z')
),
timefilteredquotes AS (
    /* filter between 7am and 1pm PST, 14:00 to 20:00 UTC */
    /* clean up invalid data points */
    SELECT * FROM typeconvertedquotes
    WHERE DATEPART(hour, lastUpdated) >= 14 AND DATEPART(hour, lastUpdated) < 20 AND bidSize > 0 AND askSize > 0 AND bidPrice > 0 AND askPrice > 0
),
```

Em seguida, usamos a função **LAG** para obter valores do último tick. Uma hora do valor de **LIMIT DURATION** valor é escolhida arbitrariamente. Dada a frequência da cotação, é seguro supor que você pode encontrar o tique anterior voltando uma hora.  

```SQL
shiftedquotes AS (
    /* get previous bid/ask price and size in order to calculate VOI */
    SELECT
        symbol,
        (bidPrice + askPrice)/2 AS midPrice,
        bidPrice,
        bidSize,
        askPrice,
        askSize,
        LAG(bidPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidPricePrev,
        LAG(bidSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidSizePrev,
        LAG(askPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askPricePrev,
        LAG(askSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askSizePrev
    FROM timefilteredquotes
),
```

Em seguida podemos calcular o valor de VOI. Filtramos os valores nulos se o tique anterior não existir, só por precaução.

```SQL
currentPriceAndVOI AS (
    /* calculate VOI */
    SELECT
        symbol,
        midPrice,
        (CASE WHEN (bidPrice < bidPricePrev) THEN 0
            ELSE (CASE WHEN (bidPrice = bidPricePrev) THEN (bidSize - bidSizePrev) ELSE bidSize END)
         END) -
        (CASE WHEN (askPrice < askPricePrev) THEN askSize
            ELSE (CASE WHEN (askPrice = askPricePrev) THEN (askSize - askSizePrev) ELSE 0 END)
         END) AS VOI
    FROM shiftedquotes
    WHERE
        bidPrice IS NOT NULL AND
        bidSize IS NOT NULL AND
        askPrice IS NOT NULL AND
        askSize IS NOT NULL AND
        bidPricePrev IS NOT NULL AND
        bidSizePrev IS NOT NULL AND
        askPricePrev IS NOT NULL AND
        askSizePrev IS NOT NULL
),
```

Agora, podemos usar **LAG** novamente para criar uma sequência com 2 valores de VOI consecutivos, seguido de 10 valores de preços médio consecutivos.

```SQL
shiftedPriceAndShiftedVOI AS (
    /* get 10 future prices and 2 previous VOIs */
    SELECT
        symbol,
        midPrice AS midPrice10,
        LAG(midPrice, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice9,
        LAG(midPrice, 2) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice8,
        LAG(midPrice, 3) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice7,
        LAG(midPrice, 4) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice6,
        LAG(midPrice, 5) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice5,
        LAG(midPrice, 6) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice4,
        LAG(midPrice, 7) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice3,
        LAG(midPrice, 8) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice2,
        LAG(midPrice, 9) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice1,
        LAG(midPrice, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice,
        LAG(VOI, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI1,
        LAG(VOI, 11) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
    FROM currentPriceAndVOI
),
```

Em seguida reformatamos os dados em entradas para um modelo linear de duas variáveis. Filtramos novamente os eventos onde não temos todos os dados.

```SQL
modelInput AS (
    /* create feature vector, x being VOI, y being delta price */
    SELECT
        symbol,
        (midPrice1 + midPrice2 + midPrice3 + midPrice4 + midPrice5 + midPrice6 + midPrice7 + midPrice8 + midPrice9 + midPrice10)/10.0 - midPrice AS y,
        VOI1 AS x1,
        VOI2 AS x2
    FROM shiftedPriceAndShiftedVOI
    WHERE
        midPrice1 IS NOT NULL AND
        midPrice2 IS NOT NULL AND
        midPrice3 IS NOT NULL AND
        midPrice4 IS NOT NULL AND
        midPrice5 IS NOT NULL AND
        midPrice6 IS NOT NULL AND
        midPrice7 IS NOT NULL AND
        midPrice8 IS NOT NULL AND
        midPrice9 IS NOT NULL AND
        midPrice10 IS NOT NULL AND
        midPrice IS NOT NULL AND
        VOI1 IS NOT NULL AND
        VOI2 IS NOT NULL
),
```

Como o Azure Stream Analytics não tem uma função de regressão linear interna, usamos as funções de agregação **SUM** e **AVG** para computar os coeficientes para o modelo linear.

![Fórmula matemática de regressão linear](./media/stream-analytics-high-frequency-trading/linear-regression-formula.png)

```SQL
modelagg AS (
    /* get aggregates for linear regression calculation,
     http://faculty.cas.usf.edu/mbrannick/regression/Reg2IV.html */
    SELECT
        symbol,
        SUM(x1 * x1) AS x1x1,
        SUM(x2 * x2) AS x2x2,
        SUM(x1 * y) AS x1y,
        SUM(x2 * y) AS x2y,
        SUM(x1 * x2) AS x1x2,
        AVG(y) AS avgy,
        AVG(x1) AS avgx1,
        AVG(x2) AS avgx2
    FROM modelInput
    GROUP BY symbol, TumblingWindow(hour, 24, -4)
),
modelparambs AS (
    /* calculate b1 and b2 for the linear model */
    SELECT
        symbol,
        (x2x2 * x1y - x1x2 * x2y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b1,
        (x1x1 * x2y - x1x2 * x1y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b2,
        avgy,
        avgx1,
        avgx2
    FROM modelagg
),
model AS (
    /* calculate a for the linear model */
    SELECT
        symbol,
        avgy - b1 * avgx1 - b2 * avgx2 AS a,
        b1,
        b2
    FROM modelparambs
),
```

Para usar o modelo do dia anterior para a pontuação do evento atual, queremos unir as cotações com o modelo. Mas, em vez de usar **JOIN**, usamos **UNION** nos eventos de modelo e eventos de cotação. Em seguida, usamos **LAG** para emparelhar os eventos com o modelo do dia anterior, para obter uma correspondência exata. Por causa do fim de semana precisamos examinar os três últimos dias. Se tiver usado um **JOIN** simples, obteremos três modelos para cada evento de cotação.

```SQL
shiftedVOI AS (
    /* get two consecutive VOIs */
    SELECT
        symbol,
        midPrice,
        VOI AS VOI1,        
        LAG(VOI, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
    FROM currentPriceAndVOI
),
VOIAndModel AS (
    /* combine VOIs and models */
    SELECT
        'voi' AS type,
        symbol,
        midPrice,
        VOI1,
        VOI2,
        0.0 AS a,
        0.0 AS b1,
        0.0 AS b2
    FROM shiftedVOI
    UNION
    SELECT
        'model' AS type,
        symbol,
        0.0 AS midPrice,
        0 AS VOI1,
        0 AS VOI2,
        a,
        b1,
        b2
    FROM model
),
VOIANDModelJoined AS (
    /* match VOIs with the latest model within 3 days (72 hours, to take the weekend into account) */
    SELECT
        symbol,
        midPrice,
        VOI1 as x1,
        VOI2 as x2,
        LAG(a, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS a,
        LAG(b1, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b1,
        LAG(b2, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b2
    FROM VOIAndModel
    WHERE type = 'voi'
),
```

Agora, podemos fazer previsões e gerar sinais de compra/venda com base no modelo, com um valor limite de 0.02. Um valor de transação de 10 representa compra. Um valor de transação de -10 representa venda.

``` SQL
prediction AS (
    /* make prediction if there is a model */
    SELECT
        symbol,
        midPrice,
        a + b1 * x1 + b2 * x2 AS efpc
    FROM VOIANDModelJoined
    WHERE
        a IS NOT NULL AND
        b1 IS NOT NULL AND
        b2 IS NOT NULL AND
        x1 IS NOT NULL AND
        x2 IS NOT NULL
),
tradeSignal AS (
    /* generate buy/sell signals */
    SELECT
        DateAdd(hour, -7, System.Timestamp) AS time,
        symbol,     
        midPrice,
        efpc,
        CASE WHEN (efpc > 0.02) THEN 10 ELSE (CASE WHEN (efpc < -0.02) THEN -10 ELSE 0 END) END AS trade,
        DATETIMEFROMPARTS(DATEPART(year, System.Timestamp), DATEPART(month, System.Timestamp), DATEPART(day, System.Timestamp), 0, 0, 0, 0) as date
    FROM prediction
),
```

### <a name="trading-simulation"></a>Simulação da transação
Após termos os sinais da transação, desejamos testar a eficiência da estratégia comercial, sem realmente fazer a transação. 

Para atingir esse teste, usamos uma UDA, com uma janela de salto, saltando a cada um minuto. O agrupamento adicional na data e a cláusula having permitem que a janela considere somente eventos pertencentes ao mesmo dia. Para uma janela de salto que inclua dois dias, **GROUP BY** por data, separa o agrupamento em dia anterior e atual. A cláusula **HAVING** filtra as janelas que terminam no dia atual, mas o agrupamento no dia anterior.

```SQL
simulation AS
(
    /* perform trade simulation for the past 7 hours to cover an entire trading day, and generate output every minute */
    SELECT
        DateAdd(hour, -7, System.Timestamp) AS time,
        symbol,
        date,
        uda.TradeSimulation(tradeSignal) AS s
    FROM tradeSignal
    GROUP BY HoppingWindow(minute, 420, 1), symbol, date
    Having DateDiff(day, date, time) < 1 AND DATEPART(hour, time) < 13
)
```

O UDA do JavaScript inicializa todos os acumuladores na função `init`, calcula a transição de estado com todos os eventos adicionados à janela e retorna os resultados da simulação no final da janela. O processo de transação geral serve para:

- Comprar ações quando um sinal de compra é recebido, e não há exploração de reserva.
- Vender ações quando um sinal de venda é recebido, e não há exploração de reserva.
- Venda a descoberto, se não houver exploração de reserva. 

Se estiver na posição vendida e um sinal de compra é recebido, compre para cobrir. Guardamos ou rebaixamos 10 compartilhamentos de um estoque nessa simulação. O custo de transações são simples $8.

```javascript
function main() {
    var TRADE_COST = 8.0;
    var SHARES = 10;
    this.init = function () {
        this.own = false;
        this.pos = 0;
        this.pnl = 0.0;
        this.tradeCosts = 0.0;
        this.buyPrice = 0.0;
        this.sellPrice = 0.0;
        this.buySize = 0;
        this.sellSize = 0;
        this.buyTotal = 0.0;
        this.sellTotal = 0.0;
    }
    this.accumulate = function (tradeSignal, timestamp) {
        if(!this.own && tradeSignal.trade == 10) {
          // Buy to open
          this.own = true;
          this.pos = 1;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.buySize += SHARES;
          this.buyTotal += SHARES * tradeSignal.midprice;
        } else if(!this.own && tradeSignal.trade == -10) {
          // Sell to open
          this.own = true;
          this.pos = -1
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.sellSize += SHARES;
          this.sellTotal += SHARES * tradeSignal.midprice;
        } else if(this.own && this.pos == 1 && tradeSignal.trade == -10) {
          // Sell to close
          this.own = false;
          this.pos = 0;
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
          this.sellSize += SHARES;
          this.sellTotal += SHARES * tradeSignal.midprice;
          // Sell to open
          this.own = true;
          this.pos = -1;
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.sellSize += SHARES;        
          this.sellTotal += SHARES * tradeSignal.midprice;
        } else if(this.own && this.pos == -1 && tradeSignal.trade == 10) {
          // Buy to close
          this.own = false;
          this.pos = 0;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
          this.buySize += SHARES;
          this.buyTotal += SHARES * tradeSignal.midprice;
          // Buy to open
          this.own = true;
          this.pos = 1;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.buySize += SHARES;         
          this.buyTotal += SHARES * tradeSignal.midprice;
        }
    }
    this.computeResult = function () {
        var result = {
            "pnl": this.pnl,
            "buySize": this.buySize,
            "sellSize": this.sellSize,
            "buyTotal": this.buyTotal,
            "sellTotal": this.sellTotal,
            "tradeCost": this.tradeCost
            };
        return result;
    }
}
```

Por fim, enviamos o resultado para o painel do Power BI para visualização.

```SQL
SELECT * INTO tradeSignalDashboard FROM tradeSignal /* output tradeSignal to PBI */
SELECT
    symbol,
    time,
    date,
    TRY_CAST(s.pnl as float) AS pnl,
    TRY_CAST(s.buySize as bigint) AS buySize,
    TRY_CAST(s.sellSize as bigint) AS sellSize,
    TRY_CAST(s.buyTotal as float) AS buyTotal,
    TRY_CAST(s.sellTotal as float) AS sellTotal
    INTO pnlDashboard
FROM simulation /* output trade simulation to PBI */
```

![Visual de gráfico das transações do Power BI](./media/stream-analytics-high-frequency-trading/trades-power-bi-chart.png)

![Visual de gráfico do Power BI de PNL](./media/stream-analytics-high-frequency-trading/pnl-power-bi-chart.png)


## <a name="summary"></a>Resumo
Podemos implementar um modelo de transação de alta frequência realista com uma consulta moderadamente complexa no Azure Stream Analytics. Temos que simplificar o modelo de cinco variáveis de entrada para duas, devido à falta de uma função de regressão linear interna. Porém, para um determinado usuário, os algoritmos com mais dimensões e sofisticação podem possivelmente ser implementados também como UDA do JavaScript. 

Vale a pena observar que a maioria das consultas, que não seja UDA do JavaScript, pode ser testada e depurada dentro do Visual Studio com a [Ferramenta do Azure Stream Analytics para Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Depois que a consulta inicial foi escrita, o autor gasta menos de 30 minutos testando e depurando a consulta no Visual Studio. 

No momento, o UDA não pode ser depurado no Visual Studio. Estamos trabalhando para habilitar essa funcionalidade com a capacidade de percorrer o código JavaScript. Além disso, observe que os campos que acessam a UDA têm nomes em minúsculas. Isso não era um comportamento óbvio durante o teste de consulta. Porém, com o nível de compatibilidade do Azure Stream Analytics 1.1, preservamos as maiúsculas e minúsculas do nome do campo para que o comportamento seja mais natural.

Espero que este artigo sirva como inspiração para todos os usuários do Azure Stream Analytics, que podem usar o nosso serviço para executar análises avançadas quase em tempo real, de forma contínua. Os seus comentários são importantes para que possamos facilitar a implementação de consultas para cenários de consulta avançada.
