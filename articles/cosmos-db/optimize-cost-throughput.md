---
title: Otimizar o custo de taxa de transferência no Azure Cosmos DB
description: Este artigo explica como otimizar os custos de taxa de transferência para dados armazenados no Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: 280d389875d5ac951e0a846f3331ea727176b5e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60929009"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>Otimizar a taxa de transferência provisionada no Azure Cosmos DB

Ao oferecer um modelo de taxa de transferência provisionada, o Azure Cosmos DB consegue fornecer um desempenho previsível em qualquer escala. A reserva ou o provisionamento da taxa de transferência elimina antecipadamente o “efeito de vizinho barulhento” sobre o desempenho. Você especifica a quantidade exata de taxa de transferência de que precisa e o Azure Cosmos DB garante a taxa de transferência configurada, com suporte do SLA.

Você pode começar com uma taxa de transferência mínima de 400 RU/s e escalar verticalmente para até dezenas de milhões de solicitações por segundo, ou até mesmo mais. Cada solicitação emitida em relação a seu contêiner ou banco de dados do Azure Cosmos, como uma solicitação de leitura, de gravação ou de consulta, tem um custo correspondente para procedimentos armazenados que é deduzido da taxa de transferência fornecida. Se você provisiona 400 RU/s e emite uma consulta que custa 40 RUs, pode emitir 10 dessas consultas por segundo. Qualquer solicitação além disso sofrerá limitação de taxa e você deverá repetir a solicitação. Se você estiver usando drivers de cliente, eles dão suporte à lógica de repetição automática.

Você pode provisionar a taxa de transferência em bancos de dados ou contêineres, e as duas estratégias podem ajudá-lo a economizar nos custos, dependendo do cenário.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Otimizar pelo provisionamento de taxa de transferência em níveis diferentes

* Se você provisionar a taxa de transferência em um banco de dados, todos os contêineres, por exemplo, coleções/tabelas/gráficos dentro desse banco de dados, poderão compartilhar a taxa de transferência com base na carga. A taxa de transferência reservada no nível do banco de dados é compartilhada sem uniformidade, dependendo da carga de trabalho em um conjunto específico de contêineres.

* Se você provisionar a taxa de transferência em um contêiner, ela será garantida para esse contêiner, com suporte do SLA. A escolha de uma chave de partição lógica é crucial para a distribuição uniforme de carga entre todas as partições lógicas de um contêiner. Consulte os artigos [Particionamento](partitioning-overview.md) e [Dimensionamento horizontal](partition-data.md) para obter mais detalhes.

Abaixo estão algumas diretrizes para a escolha de uma estratégia de taxa de transferência provisionada:

**Considere provisionar a taxa de transferência em um banco de dados do Azure Cosmos DB (que contém um conjunto de contêineres) se**:

1. Você tem algumas dezenas de contêineres do Azure Cosmos e deseja compartilhar a taxa de transferência entre alguns ou todos eles. 

2. Você está migrando de um banco de dados de um único locatário projetado para ser executado em VMs hospedadas IaaS ou locais, por exemplo, bancos de dados relacionais ou NoSQL, para o Azure Cosmos DB. E se você tiver muitos coleções/tabelas/gráficos e você não deseja fazer nenhuma alteração ao seu modelo de dados. Observe que talvez seja necessário comprometer alguns dos benefícios oferecidos pelo Azure Cosmos DB, se você não está atualizando seu modelo de dados durante a migração de um banco de dados local. É recomendável sempre reavaliar o modelo de dados para obter o máximo em termos de desempenho e também para otimizar os custos. 

3. Você deseja absorver picos não planejados em cargas de trabalho ocorridos quando uma taxa de transferência em pool no nível do banco de dados é sujeitada a picos inesperados em cargas de trabalho. 

4. Em vez de configurar uma taxa de transferência específica em contêineres individuais, você deseja obter a taxa de transferência agregada em um conjunto de contêineres no banco de dados.

**Considere o provisionamento da taxa de transferência em um contêiner individual se:**

1. Você tem alguns contêineres do Azure Cosmos. Como o Azure Cosmos DB independe do esquema, um contêiner pode conter itens que têm esquemas heterogêneos e que não exigem que os clientes criem vários tipos de contêineres, um para cada entidade. É sempre uma opção considerar se vale a pena agrupar contêineres separados, 10 a 20, por exemplo, em um único contêiner. Com o mínimo de 400 RUs para contêineres, o pooling de todos os 10 a 20 contêineres em um só pode ser mais econômico. 

2. Você deseja controlar a taxa de transferência em um contêiner específico e obter a taxa de transferência garantida em determinado contêiner com suporte do SLA.

**Considere um híbrido das duas estratégias acima:**

1. Como mencionado anteriormente, o Azure Cosmos DB permite que você misture e combine as duas estratégias acima, ou seja, você pode ter alguns contêineres dentro do banco de dados Azure Cosmos, que podem compartilhar a taxa de transferência provisionada no banco de dados, e alguns contêineres dentro do mesmo banco de dados que podem ter quantidades dedicadas de taxa de transferência provisionada. 

2. Você pode aplicar as estratégias acima para propor uma configuração híbrida, em que haja taxa de transferência provisionada no nível do banco de dados e alguns contêineres com taxa de transferência dedicada.

Conforme mostrado na tabela a seguir, dependendo da opção de API, você pode provisionar a taxa de transferência em granularidades diferentes.

|API|Na taxa de transferência **compartilhada**, configure |Na taxa de transferência **dedicada**, configure |
|----|----|----|
|API do SQL|Banco de dados|Contêiner|
|API do Azure Cosmos DB para MongoDB|Banco de dados|Coleção|
|API Cassandra|Keyspace|Tabela|
|API do Gremlin|Conta de banco de dados|Grafo|
|API de Tabela|Conta de banco de dados|Tabela|

Ao provisionar a produtividade em níveis diferentes, você pode otimizar seus custos com base nas características da carga de trabalho. Como mencionado anteriormente, você pode, programaticamente e a qualquer hora, aumentar ou diminuir a taxa de transferência fornecida para os contêineres individuais ou, coletivamente, em um conjunto de contêineres. Ao escalar a taxa de transferência com flexibilidade de acordo com as alterações da carga de trabalho, você paga apenas pela taxa de transferência configurada. Se o contêiner, ou um conjunto de contêineres, está distribuído entre várias regiões, a taxa de transferência que você configura no contêiner ou conjunto de contêineres tem a disponibilidade garantida em todas as regiões.

## <a name="optimize-with-rate-limiting-your-requests"></a>Otimizar suas solicitações com limitação de taxa

Para cargas de trabalho que não são afetadas pela latência, você pode provisionar uma taxa de transferência menor e permitir que o aplicativo lide com a limitação de taxa quando a taxa de transferência real exceder a taxa de transferência provisionada. O servidor encerrará antecipadamente a solicitação com RequestRateTooLarge (código de status HTTP 429) e retornará o cabeçalho `x-ms-retry-after-ms` indicando a quantidade de tempo, em milissegundos, que o usuário deve aguardar antes de tentar novamente a solicitação. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Lógica de repetição em SDKs 

Os SDKs nativos (.NET/.NET Core, Java, Node.js e Python) capturam essa resposta implicitamente, respeitam o cabeçalho retry-after especificado para o servidor e repetem a solicitação. A menos que sua conta seja acessada simultaneamente por vários clientes, a tentativa seguinte será bem-sucedida.

Se você tiver mais de um cliente operando cumulativamente e consistentemente acima da taxa de solicitação, a contagem de repetição padrão atualmente definida como 9 poderá não ser suficiente. Nesse caso, o cliente gerará um `DocumentClientException` com código de status 429 para o aplicativo. A contagem de repetição padrão pode ser alterada definindo `RetryOptions` na instância de ConnectionPolicy. Por padrão, a DocumentClientException com o código de status 429 será retornada após uma espera cumulativa de 30 segundos se a solicitação continuar a operar acima da taxa de solicitação. Isso ocorre mesmo quando a contagem de repetição atual é menor que a contagem de repetição máxima, seja o padrão 9 seja um valor definido pelo usuário. 

[MaxRetryAttemptsOnThrottledRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?view=azure-dotnet) é definido como 3, ou seja, nesse caso, se uma operação de solicitação tiver limitação de taxa ao exceder a taxa de transferência reservada para a coleção, a operação de solicitação tentará novamente três vezes antes de lançar a exceção para o aplicativo.  [MaxRetryWaitTimeInSeconds](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) é definido como 60, ou seja, nesse caso, se o tempo de espera para a repetição cumulativo desde a primeira solicitação exceder 60 segundos, a exceção será lançada.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 

connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 

connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Estratégia de particionamento e custos de taxa de transferência provisionada

Uma boa estratégia de particionamento é importante para otimizar os custos no Azure Cosmos DB. Verifique se não há nenhuma distorção de partições, que é exposta pelas métricas de armazenamento. Verifique se não há nenhuma distorção de taxa de transferência para uma partição, que é exposta com métricas de taxa de transferência. Verifique se não há nenhuma distorção em relação a chaves de partição específicas. As chaves dominantes no armazenamento são expostas por métricas, mas a chave dependerá do padrão de acesso do aplicativo. É melhor pensar na chave de partição lógica correta. Uma boa chave de partição deve ter as seguintes características:

* Escolha uma chave de partição que distribua a carga de trabalho uniformemente em todas as partições e uniformemente ao longo do tempo. Em outras palavras, você não deve ter algumas chaves com a maioria dos dados e outras com menos dados, ou nenhum. 

* Escolha uma chave de partição que permita a distribuição uniforme dos padrões de acesso entre as partições lógicas. A carga de trabalho deve estar razoavelmente uniforme entre todas as chaves. Em outras palavras, a maior parte da carga de trabalho não deve estar concentrada em algumas chaves específicas. 

* Escolha uma chave de partição que tenha um intervalo grande de valores. 

A ideia básica é distribuir os dados e a atividade em seu contêiner através do conjunto de partições lógicas, para que os recursos para armazenamento de dados e taxa de transferência possam ser distribuídos entre as partições lógicas. Os candidatos a chaves de partição podem incluir as propriedades que aparecem com frequência como um filtro nas suas consultas. Consultas podem ser roteadas com eficiência, incluindo a chave de partição no predicado de filtro. Com essa estratégia de particionamento, a otimização da taxa de transferência provisionada ficará muito mais fácil. 

### <a name="design-smaller-items-for-higher-throughput"></a>Criar itens menores para uma taxa de transferência maior 

O custo da solicitação ou o custo de processamento da solicitação de determinada operação está diretamente correlacionado com o tamanho do item. As operações em itens grandes custará mais do que as operações em itens menores. 

## <a name="data-access-patterns"></a>Padrões de acesso a dados 

É sempre uma boa prática separar logicamente os dados em categorias lógicas com base na frequência com a qual você acessa os dados. Ao categorizar os dados como de acesso frequente, médio ou esporádico, você poderá ajustar o armazenamento consumido e a taxa de transferência necessária. Dependendo da frequência de acesso, você pode colocar os dados em contêineres separados (por exemplo, tabelas, gráficos e coleções) e ajustar a taxa de transferência provisionada neles para acomodar as necessidades do segmento de dados. 

Além disso, se você está usando o Azure Cosmos DB e sabe que não vai pesquisar determinados valores de dados ou que vai acessá-los raramente, você deve armazenar os valores compactados desses atributos. Com esse método, você poupa espaço de armazenamento, espaço de índice e taxa de transferência provisionada, resultando em custos mais baixos.

## <a name="optimize-by-changing-indexing-policy"></a>Otimizar através da alteração da política de indexação 

Por padrão, o Azure Cosmos DB indexa automaticamente todas as propriedades de cada registro. Isso serve para facilitar o desenvolvimento e garantir um desempenho excelente em muitos tipos diferentes de consultas ad hoc. Se você tiver grandes registros com milhares de propriedades, pagar o custo da taxa de transferência para indexar todas as propriedades pode não ser útil, especialmente se você faz consulta apenas em relação a 10 ou 20 dessas propriedades. À medida que você for ficando mais familiarizado com sua carga de trabalho específica, nossa diretriz é a de ajustar sua política de índice. Obtenha detalhes completos sobre a política de indexação do Azure Cosmos DB [aqui](indexing-policies.md). 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Monitoramento provisionado e taxa de transferência consumida 

Você pode monitorar o número total de RUs provisionadas, o número de solicitações com taxa limitada e o número de RUs consumido no portal do Azure. A imagem abaixo mostra uma métrica de uso de exemplo:

![Monitorar unidades de solicitação no portal do Azure](./media/optimize-cost-throughput/monitoring.png)

Você também pode definir alertas para verificar se o número de solicitações com taxa limitada excede um limite específico. Consulte o artigo [Como monitorar o Azure Cosmos DB](use-metrics.md) para obter mais detalhes. Esses alertas podem enviar um email para os administradores de conta ou chamar um Webhook HTTP personalizado ou uma função do Azure para aumentar a taxa de transferência provisionada automaticamente. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Dimensionar a taxa de transferência com flexibilidade e sob demanda 

Como você será cobrado pela taxa de transferência provisionada, igualar a taxa de transferência provisionada às suas necessidades pode ajudar a evitar encargos sobre taxa de transferência não utilizada. Você pode escalar ou reduzir verticalmente sua taxa de transferência a qualquer hora e conforme necessário.  

* O monitoramento do consumo das RUs e a velocidade das solicitações com taxa limitada pode revelar que não é necessário manter uma taxa de transferência provisionada constante ao longo do dia ou da semana. Você pode receber menos tráfego durante a noite ou no fim de semana. Usando o portal do Azure, ou SDKs nativos ou API REST do Azure Cosmos DB, você pode dimensionar a taxa de transferência provisionada a qualquer hora. A API REST do Azure Cosmos DB fornece pontos de extremidade para atualizar o nível de desempenho de seus contêineres programaticamente, simplificando o ajuste da taxa de transferência em seu código dependendo da hora do dia ou do dia da semana. A operação é realizada sem nenhum tempo de inatividade e normalmente entra em vigor em menos de um minuto. 

* Uma das áreas em que você deve dimensionar a taxa de transferência é quando inclui dados no Azure Cosmos DB, por exemplo, durante a migração de dados. Depois de concluir a migração, você pode reduzir a taxa de transferência provisionada verticalmente para lidar com o estado estável da solução.  

* Lembre-se de que a cobrança é com granularidade no nível de hora, ou seja, você não vai economizar dinheiro se alterar a taxa de transferência provisionada mais de uma vez a cada hora.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Determinar a taxa de transferência necessária para uma nova carga de trabalho 

Para determinar a taxa de transferência provisionada para uma nova carga de trabalho, você pode seguir as etapas abaixo: 

1. Realize uma avaliação inicial aproximada usando o planejador de capacidade e ajuste suas estimativas com a ajuda do Azure Cosmos Explorer no portal do Azure. 

2. É recomendável criar os contêineres com uma taxa de transferência maior que a esperada e reduzir verticalmente conforme a necessidade. 

3. É recomendável usar um dos SDKs nativos do Azure Cosmos DB para se beneficiar das novas tentativas automáticas quando as solicitações sofrerem limitação de taxa. Se você estiver trabalhando em uma plataforma que não tem suporte e usar a API REST do Cosmos DB, implemente sua própria política de repetição usando o cabeçalho `x-ms-retry-after-ms`. 

4. Verifique se o código do aplicativo dá suporte normalmente ao caso quando todas as repetições falham. 

5. Você pode configurar alertas no portal do Azure para obter notificações de limitação de taxa. Você pode iniciar com limites conservadores, como 10 solicitações com taxa limitada nos últimos 15 minutos, e alternar para regras mais permissivas depois de descobrir seu consumo real. Limites de taxa ocasionais não são problema; eles mostram que você está interagindo com os limites definidos e isso é exatamente o que você deve fazer. 

6. Use o monitoramento para entender seu padrão de tráfego, para que você possa considerar a necessidade de ajustar dinamicamente o provisionamento de taxa de transferência ao longo do dia ou da semana. 

7. Monitore a proporção entre a taxa de transferência provisionada e a consumida regularmente para ter certeza de que não provisionou mais contêineres e bancos de dados do que o necessário. Ter um pouco a mais do que a taxa de transferência provisionada é uma boa medida de segurança.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Práticas recomendadas para otimizar a taxa de transferência provisionada 

As etapas a seguir ajudam a tornar as suas soluções altamente escalonáveis e econômicas ao usar o Azure Cosmos DB.  

1. Se você provisionou sua taxa de transferência muito acima do necessário em contêineres e bancos de dados, examine as RUs consumidas e as provisionadas e ajuste as cargas de trabalho.  

2. Um método para estimar a quantidade de produtividade reservada exigida pelo aplicativo é registrar o encargo de RUs ( unidades de solicitação) associado à execução de operações comuns em relação a um banco de dados ou contêiner do Azure Cosmos representativo usado pelo aplicativo e, em seguida, estimar o número de operações que você prevê que executará a cada segundo. Meça e inclua consultas comuns e seu uso também. Para saber como estimar os custos de RU de consultas programaticamente ou usando o portal, consulte [Otimizar o custo de consultas](online-backup-and-restore.md). 

3. É outra maneira de obter as operações e seus custos em RUs, habilitando os logs do Azure Monitor, que lhe dará a divisão de operação/duração e o encargo de solicitação. O Azure Cosmos DB fornece encargos de solicitação para cada operação, ou seja, o encargo de cada operação pode ser armazenado a partir da resposta e usado para análise. 

4. Você pode escalar e reduzir a taxa de transferência provisionada verticalmente, conforme necessário, para atender às suas necessidades de carga de trabalho. 

5. Você pode adicionar e remover regiões associadas à conta do Azure Cosmos conforme necessário, e controlar os custos. 

6. Verifique se você tem uma distribuição uniforme de dados e cargas de trabalho entre partições lógicas de seus contêineres. Se a distribuição de partições for desigual, isso pode causar um provisionamento de taxa de transferência maior do que o necessário. Se você identificar uma distribuição desigual, recomendamos redistribuir a carga de trabalho uniformemente entre as partições ou reparticionar os dados. 

7. Se você tiver muitos contêineres e eles não exigirem SLAs, você poderá usar a oferta baseada em banco de dados para os casos em que os SLAs de taxa de transferência por contêiner não se aplicam. Você deve identificar quais contêineres do Azure Cosmos você deseja migrar para a oferta de taxa de transferência no nível do banco de dados e migrá-los por meio de uma solução baseada no feed de alterações. 

8. Considere usar a “Camada gratuita do Cosmos DB” (gratuita por um ano), a opção Experimentar o Cosmos DB (até três regiões) ou o emulador do Cosmos DB que pode ser baixado para cenários de desenvolvimento/teste. Ao usar essas opções para desenvolvimento e teste, você pode reduzir os custos consideravelmente.  

9. Além disso, você pode executar otimizações específicas da carga de trabalho, por exemplo, aumentando o tamanho dos lotes, balanceando a carga de leituras entre várias regiões e eliminando a duplicação de dados, se for o caso.

10. Com a capacidade reservada do Azure Cosmos DB, você pode obter descontos consideráveis de até 65% por três anos. O modelo de capacidade reservada do Azure Cosmos DB é uma reserva antecipada em relação às unidades de solicitação necessárias ao longo do tempo. Os descontos são organizados em camadas, ou seja, quanto mais unidades de solicitação você usar em um período mais longo, maior será o desconto. Esses descontos são aplicados imediatamente. As RUs usadas acima dos valores provisionados serão cobradas com base no custo de capacidade não reservado. Consulte [Capacidade reservada do Cosmos DB](cosmos-db-reserved-capacity.md)) para obter mais detalhes. Considere adquirir capacidade reservada para reduzir ainda mais os custos com taxa de transferência provisionada.  

## <a name="next-steps"></a>Próximas etapas

A seguir, você poderá saber mais sobre a otimização de custos no Azure Cosmos DB nos seguintes artigos:

* Saiba mais sobre [Otimizando para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [Entender sua cobrança do Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre [Otimizando o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [Otimizando o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [Otimizando o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre [Otimizando o custo de contas do Azure Cosmos em várias regiões](optimize-cost-regions.md)

