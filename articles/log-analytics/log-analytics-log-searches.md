---
title: Localizar dados com as pesquisas de logs no Azure Log Analytics | Microsoft Docs
description: "As pesquisas de log permitem combinar e correlacionar quaisquer dados de computador de várias fontes em seu ambiente."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: 0d7b6712-1722-423b-a60f-05389cde3625
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: bwren
ms.openlocfilehash: bf237a837297cb8f1ab3a3340139133adcd2b244
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="find-data-using-log-searches-in-log-analytics"></a>Localizar dados usando as pesquisas de logs no Log Analytics

>[!NOTE]
> Este artigo descreve pesquisas de logs usando a linguagem de consulta atual no Log Analytics.  Se o seu espaço de trabalho foi atualizado para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md), você deverá consultar [Noções básicas sobre pesquisas de logs no Log Analytics (novo)](log-analytics-log-search-new.md).


No núcleo do Log Analytics está o recurso de pesquisa de log que permite combinar e correlacionar quaisquer dados de computador de várias fontes em seu ambiente. As soluções também são alimentadas pela pesquisa de log para fornecer métricas que giram em torno de uma área de problema específica.

Na página de Pesquisa, crie uma consulta e, em então, quando pesquisar, poderá filtrar os resultados por meio de controles da faceta. Você também pode criar consultas avançadas para transformar, filtrar e relatar sobre seus resultados.

As consultas de pesquisa de log comuns aparecem na maioria das páginas de solução. No console do OMS, você pode clicar em blocos ou fazer uma busca de outros itens para exibir detalhes sobre o item usando a pesquisa de log.

Neste tutorial, examinaremos exemplos para cobrir todas as noções básicas do uso da pesquisa de log.

Vamos começar com exemplos práticos, simples e desenvolvê-los para que você possa ver casos práticos de como usar a sintaxe para extrair as ideias que desejar dos dados.

Depois que você se acostumar com as técnicas de pesquisa, poderá examinar a [referência de pesquisa de log do Log Analytics](log-analytics-search-reference.md).

## <a name="use-basic-filters"></a>Usar filtros básicos
A primeira coisa a saber é que a primeira parte de uma consulta de pesquisa, antes de um caractere de barra vertical "|", é sempre um *filtro*. Você pode pensar nela como uma cláusula WHERE no TSQL: ela determina *qual* subconjunto desses dados ela deve retirar do repositório de dados do OMS. A pesquisa no repositório de dados é basicamente a especificação das características dos dados que você deseja extrair; portanto, é natural que uma consulta deva começar com a cláusula WHERE.

Os filtros mais básicos que você pode usar são *palavras-chave*, como 'erro', 'tempo limite' ou um nome de computador. Esses tipos de consultas simples normalmente retornam diversas formas de dados dentro do mesmo conjunto de resultados. Isso acontece porque o Log Analytics tem diferentes *tipos* de dados no sistema.

### <a name="to-conduct-a-simple-search"></a>Para realizar uma pesquisa simples
1. No portal do OMS, clique em **Pesquisa de Log**.  
    ![pesquisar bloco](./media/log-analytics-log-searches/oms-overview-log-search.png)
2. No campo de consulta, digite `error` e clique em **Pesquisar**.  
    ![pesquisar erro](./media/log-analytics-log-searches/oms-search-error.png)  
    Por exemplo, a consulta de `error` na imagem a seguir retornou 100.000 registros de **Event** (coletados pelo Gerenciamento de Logs), 18 registros de **ConfigurationAlert** (gerados pela Avaliação de Configuração) e 12 registros **ConfigurationChange** (capturados pelo Controle de Alterações).   
    ![pesquisar resultados](./media/log-analytics-log-searches/oms-search-results01.png)  

Esses filtros não são realmente tipos/classes de objeto. *Type* é uma marca, uma propriedade ou uma cadeia de caracteres/nome/categoria, que é anexada a uma porção dos dados. Alguns documentos no sistema são marcados como **Type:ConfigurationAlert**, alguns são marcados como **Type:Perf** ou **Type:Event** e assim por diante. Cada resultado da pesquisa, documento, registro ou entrada exibe todas as propriedades brutas e seus valores para cada porção de dados, e você pode usar esses nomes de campo para especificar o filtro quando desejar recuperar somente os registros onde o campo tiver aquele valor.

*Type* é realmente apenas um campo que existe em todos os registros; ele não é diferente de qualquer outro campo. Isso foi estabelecido com base no valor do campo Type. Esse registro terá uma forma diferente. Por acaso, **Type=Perf** ou **Type=Event** também é a sintaxe que você precisa saber para consultar eventos de dados de desempenho.

Você pode usar dois pontos (:) ou um sinal de igual (=) a seguir ao nome do campo e antes do valor. **Type:Event** and **Type=Event** são equivalentes em significado; você pode escolher o estilo desejado.

Por isso, se os registros Type=Perf tiverem um campo chamado 'CounterName', então você poderá escrever uma consulta semelhante a `Type=Perf CounterName="% Processor Time"`.

Isso lhe dará apenas os dados de desempenho onde o nome do contador de desempenho é "% de tempo do processador".

### <a name="to-search-for-processor-time-performance-data"></a>Para procurar dados de desempenho de tempo do processador
* No campo pesquisar consulta, digite `Type=Perf CounterName="% Processor Time"`

Você também pode ser mais específico e usar **InstanceName=_'Total'** na consulta, que é um contador de desempenho do Windows. Você também pode selecionar uma faceta e outro **field:value**. O filtro é adicionado automaticamente ao seu filtro na barra de consulta. Você pode ver isso na imagem a seguir. Ela mostra onde clicar para adicionar **InstanceName:’_Total’** à consulta sem digitar nada.

![pesquisar faceta](./media/log-analytics-log-searches/oms-search-facet.png)

A sua consulta agora se torna `Type=Perf CounterName="% Processor Time" InstanceName="_Total"`

Nesse exemplo, você não precisa especificar **Type=Perf** para obter esse resultado. Como os campos CounterName e InstanceName existem somente para registros de Type=Perf, a consulta é específica o suficiente para retornar os mesmos resultados da anterior, mais longa:

```
CounterName="% Processor Time" InstanceName="_Total"
```

Isso ocorre porque todos os filtros na consulta são avaliados como estando em *AND* uns com os outros. Efetivamente, quanto mais campos você adicionar ao critério, mais específicos e refinados são os resultados.

Por exemplo, a consulta `Type=Event EventLog="Windows PowerShell"` é idêntica à `Type=Event AND EventLog="Windows PowerShell"`. Ela retorna todos os eventos que foram registrados no e coletados do log de eventos do Windows PowerShell. Se você adicionar um filtro várias vezes selecionando repetidamente a mesma faceta, o problema é apenas superficial: ele pode sobrecarregar a barra de pesquisa, mas ainda retorna os mesmos resultados porque o operador AND implícito sempre está lá.

Você pode facilmente reverter o operador AND implícito usando um operador NOT explicitamente. Por exemplo:

`Type:Event NOT(EventLog:"Windows PowerShell")` ou seu equivalente `Type=Event EventLog!="Windows PowerShell"` retorna todos os eventos de todos os outros logs que NÃO sejam o log do Windows PowerShell.

Você pode usar outro operador booliano, como 'OR'. A consulta a seguir retorna registros para os quais o log de eventos é um sistema OU aplicativo.

```
EventLog=Application OR EventLog=System
```

Usando a consulta anterior, você obterá as entradas para ambos os logs no mesmo conjunto de resultados.

No entanto, se você remover o OR deixando o AND implícito funcionando, a consulta a seguir não produzirá resultados porque não existe uma entrada de log de eventos que pertença a AMBOS os logs. Cada entrada de log de eventos foi escrita em apenas um dos dois logs.

```
EventLog=Application EventLog=System
```


## <a name="use-additional-filters"></a>Usar filtros adicionais
A consulta a seguir retorna entradas de dois logs de eventos para todos os computadores que enviaram dados.

```
EventLog=Application OR EventLog=System
```

![pesquisar resultados](./media/log-analytics-log-searches/oms-search-results03.png)

A seleção de um dos campos ou filtros restringirá a consulta a um computador específico, excluindo todos os outros. A consulta resultante seria semelhante à seguinte:

```
EventLog=Application OR EventLog=System Computer=SERVER1.contoso.com
```

Que é equivalente à seguinte, por causa do AND implícito.

```
EventLog=Application OR EventLog=System AND Computer=SERVER1.contoso.com
```

Cada consulta é avaliada na ordem explícita abaixo. Observe o parêntese.

```
(EventLog=Application OR EventLog=System) AND Computer=SERVER1.contoso.com
```

Assim como no campo de log de eventos, você pode recuperar dados apenas para um conjunto de computadores específicos adicionando OR. Por exemplo:

```
(EventLog=Application OR EventLog=System) AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com OR Computer=SERVER3.contoso.com)
```

Da mesma forma, a consulta a seguir retorna **% CPU Time** somente para os dois computadores selecionados.

```
CounterName="% Processor Time"  AND InstanceName="_Total" AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```

### <a name="field-types"></a>Tipos de campo
Ao criar filtros, você deve entender as diferenças de trabalhar com tipos diferentes de campos retornados pelas pesquisas de log.

**Campos de pesquisa** aparecem em azul nos resultados da pesquisa.  Você pode usar campos de pesquisa em condições de pesquisa específicas do campo, como as seguintes:

```
Type: Event EventLevelName: "Error"
Type: SecurityEvent Computer:Contains("contoso.com")
Type: Event EventLevelName IN {"Error","Warning"}
```

**Campos de pesquisa de texto livre** são mostrados em cinza nos resultados da pesquisa.  Eles não podem ser usados com os critérios de pesquisa específicos para o campo, como campos pesquisáveis.  Eles apenas são pesquisados ao executar uma consulta em todos os campos, como a seguir.

```
"Error"
Type: Event "Exception"
```


### <a name="boolean-operators"></a>Operadores boolianos
Com campos numéricos e de datetime, você pode procurar por valores usando *maior que*, *menor que* e *menor ou igual*. Você pode usar operadores simples como >, <, =, < =,! = na barra de pesquisa de consulta.

Você pode consultar um log de eventos específico para um período de tempo específico. Por exemplo, as últimas 24 horas são indicadas com a seguinte expressão mnemônica:

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### <a name="to-search-using-a-boolean-operator"></a>Para pesquisar usando um operador booliano
* No campo pesquisar consulta, digite `EventLog=System TimeGenerated>NOW-24HOURS`  
    ![pesquisar com booliano](./media/log-analytics-log-searches/oms-search-boolean.png)

Embora você pode controlar o intervalo de tempo graficamente, e talvez queira fazer isso na maioria das vezes, há vantagens em incluir um filtro de tempo diretamente na consulta. Por exemplo, isso funciona muito bem com painéis em você pode substituir o tempo para cada bloco, independentemente do seletor de tempo *global* na página do painel. Para saber mais, confira [Assuntos de tempo no Painel](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/).

Ao filtrar por tempo, tenha em mente que você obterá resultados para a *interseção* dos dois períodos de tempo: o especificado no portal do OMS (S1) e o especificado na consulta (S2).

![interseção](./media/log-analytics-log-searches/oms-search-intersection.png)

Isso significa que, se os períodos de tempo não se cruzarem, por exemplo, no portal do OMS onde você escolhe **Esta semana** e na consulta onde você define **última semana**, não haverá nenhuma interseção e você não receberá nenhum resultado.

Os operadores de comparação usados para o campo TimeGenerated também são úteis em outras situações. Por exemplo, com campos numéricos.

Seria o caso de, já que os alertas da análise de configuração têm os valores de severidade abaixo:

* 0 = Informações
* 1= Aviso
* 2 = Crítico

Você pode fazer a consulta de alertas de aviso e críticos e também excluir os informativos com a seguinte consulta:

```
Type=ConfigurationAlert  Severity>=1
```


Você também pode usar consultas de intervalo. Isso significa que você pode fornecer o intervalo de início e de fim dos valores em uma sequência. Por exemplo, se desejar que os eventos do log de eventos do Operations Manager em que o EventID é maior ou igual a 2100, mas abaixo de 2199, a consulta a seguir retornaria esses resultados.

```
Type=Event EventLog="Operations Manager" EventID:[2100..2199]
```


> [!NOTE]
> A sintaxe de intervalo que você deve usar é o separador de valor de campo de dois pontos (:) valor de dois-pontos (:) e *não* o sinal de igual (=). Coloque as extremidades inferior e superior do intervalo entre colchetes e separe-as com dois pontos finais (..).
>
>

## <a name="manipulate-search-results"></a>Manipular resultados da pesquisa
Quando você estiver procurando por dados, vai querer refinar sua consulta de pesquisa e ter um bom nível de controle sobre os resultados. Quando os resultados são recuperados, você pode aplicar comandos para transformá-los.

Os comandos em pesquisas do Log Analytics *devem* vir após o caractere de barra vertical (|). Um filtro deve ser sempre a primeira parte de uma cadeia de caracteres de consulta. Ele define o conjunto de dados em que você está trabalhando e "canaliza" esses resultados para um comando. Você pode usar a barra vertical para adicionar comandos adicionais. Isso é vagamente semelhante ao pipeline do Windows PowerShell.

Em geral, o idioma da pesquisa do Log Analytics tenta seguir o estilo e as diretrizes do PowerShell para torná-lo semelhante aos profissionais de TI e para facilitar a curva de aprendizado.

Comandos têm nomes de verbos para que você possa perceber facilmente o que eles fazem.  

### <a name="sort"></a>Classificar
O comando classificar permite definir a ordem de classificação por um ou vários campos. Mesmo se você não usá-lo, por padrão, a ordem imposta é decrescente por tempo. Os resultados mais recentes ficam sempre na parte superior dos resultados da pesquisa. Isso significa que quando você executa uma pesquisa com `Type=Event EventID=1234` , o que realmente é executado para você é:

```
Type=Event EventID=1234 **| Sort TimeGenerated desc**
```

Isso ocorre porque é o tipo de experiência com que você está familiarizado nos logs. Por exemplo, no Visualizador de Eventos do Windows.

Você pode usar Classificar para alterar o modo como os resultados são retornados. Os exemplos a seguir mostram como isso funciona.

```
Type=Event EventID=1234 | Sort TimeGenerated asc
```

```
Type=Event EventID=1234 | Sort Computer asc
```

```
Type=Event EventID=1234 | Sort Computer asc,TimeGenerated desc
```


Os exemplos simples acima mostram como os comandos funcionam: eles alteram a forma dos resultados que o filtro retornou.

### <a name="limit-and-top"></a>Limite e superior
Outro comando menos conhecido é LIMITE. O limite é um verbo do tipo do PowerShell. O Limite é funcionalmente idêntico ao comando SUPERIOR. As consultas a seguir retornam os mesmos resultados.

```
Type=Event EventID=600 | Limit 1
```

```
Type=Event EventID=600 | Top 1
```


#### <a name="to-search-using-top"></a>Para pesquisar usando superior
* No campo pesquisar consulta, digite `Type=Event EventID=600 | Top 1`   
    ![pesquisar superior](./media/log-analytics-log-searches/oms-search-top.png)

Na imagem acima, existem 358 mil registros com EventID = 600. Os campos, as facetas e os filtros à esquerda sempre mostram informações sobre os resultados retornados *pela parte de filtro* da consulta, que é a parte antes de qualquer caractere de barra vertical. O painel **Resultados** somente retorna 1 resultado mais recente, pois o comando de exemplo transformou os resultados.

### <a name="select"></a>Selecionar
O comando SELECIONAR se comporta como Select-Object no PowerShell. Ele retorna resultados filtrados que não têm todas as suas propriedades originais. Em vez disso, ele seleciona somente as propriedades que você especifica.

#### <a name="to-run-a-search-using-the-select-command"></a>Para executar uma pesquisa usando o comando selecionar
1. Na pesquisa, digite `Type=Event` e clique em **Pesquisar**.
2. Clique em **+ mostrar mais** em um dos resultados para exibir todas as propriedades que eles têm.
3. Selecione alguns deles explicitamente e a consulta muda para `Type=Event | Select Computer,EventID,RenderedDescription`.  
    ![pesquisar selecionar](./media/log-analytics-log-searches/oms-search-select.png)

Esse comando é particularmente útil quando você deseja controlar a saída de pesquisa e escolher apenas as partes de dados que realmente importam para sua exploração, o que geralmente não é o registro completo. Isso também é útil quando os registros de tipos diferentes têm *algumas* propriedades em comum, mas não *todas*. Você pode gerar uma saída mais naturalmente parecida com uma tabela ou que funciona bem quando exportada para um arquivo CSV e processada no Excel.

## <a name="use-the-measure-command"></a>Usar o comando medir
MEDIDA é um dos comandos mais versáteis em pesquisas do Log Analytics. Ele permite que você aplique *funções* estatísticas aos seus dados e agregue os resultados agrupados por um determinado campo. Há várias funções estatísticas que têm suporte de Medida.

### <a name="measure-count"></a>Contagem de medida()
A primeira função estatística com a qual trabalhar e uma das mais simples de entender é a função *count()* .

Os resultados de qualquer consulta de pesquisa, como `Type=Event`, mostram filtros também chamados de facetas à esquerda dos resultados da pesquisa. Os filtros mostram uma distribuição de valores por um determinado campo para os resultados da pesquisa executada.

![pesquisar contagem de medida](./media/log-analytics-log-searches/oms-search-measure-count01.png)

Por exemplo, na imagem acima, você verá o campo **Computer** e ele mostra que em quase 739 mil de eventos nos resultados, há 68 valores exclusivos e distintos para o campo **Computer** nesses registros. O bloco mostra somente os 5 principais, que são os 5 valores mais comuns gravados nos campos **Computer** , classificados pelo número de documentos que contêm esse valor específico nesse campo. Na imagem, você pode ver que, dentre os quase 369 mil eventos, 90 mil vêm do computador OpsInsights04.contoso.com, 83 mil vêm do computador DB03.contoso.com e assim por diante.

E se você quiser ver todos os valores, já que o bloco mostra somente os primeiros cinco?

Isso é que o comando de medida pode fazer com a função contagem(). Essa função não usa nenhum parâmetro. Você simplesmente especifica o campo pelo qual deseja agrupar — o campo **Computer** nesse caso:

`Type=Event | Measure count() by Computer`

![pesquisar contagem de medida](./media/log-analytics-log-searches/oms-search-measure-count-computer.png)

No entanto, **Computer** é somente um campo usado *em* cada porção de dados — nenhum banco de dados relacional está envolvido e não há nenhum objeto **Computer** separado em lugar algum. Apenas os valores *nos* dados podem descrever qual entidade os gerou, além de várias outras características e aspectos dos dados; por isso, o termo *faceta*. No entanto, você pode também agrupar por outros campos. Como os resultados originais de quase 739 mil eventos que foram canalizados para o comando de medidas também têm um campo chamado **EventID**, você pode aplicar a mesma técnica para agrupar por esse campo e obter uma contagem de eventos por EventID:

```
Type=Event | Measure count() by EventID
```

Se não estiver interessado na contagem de registro real que contém um valor específico, mas apenas em uma lista dos próprios valores, você poderá adicionar um comando *Select* no final dela e selecionar somente a primeira coluna:

```
Type=Event | Measure count() by EventID | Select EventID
```

Você pode obter resultados mais complexos e classificar previamente os resultados da consulta, ou pode simplesmente clicar nas colunas na grade.

```
Type=Event | Measure count() by EventID | Select EventID | Sort EventID asc
```

#### <a name="to-search-using-measure-count"></a>Para pesquisar usando contagem de medida
* No campo pesquisar consulta, digite `Type=Event | Measure count() by EventID`
* Acrescente `| Select EventID` ao fim da consulta.
* Por fim, acrescente `| Sort EventID asc` ao fim da consulta.

Há alguns pontos importantes a observar e a enfatizar:

Primeiro, os resultados que você vê não são mais os resultados brutos originais. Em vez disso, eles são resultados agregados: essencialmente, grupos de resultados. Isso não é um problema, mas você deve entender que você está interagindo com uma forma muito diferente de dados que não é igual à forma bruta original que é criada espontaneamente como resultado da função de agregação/estatística.

Segundo, atualmente, a **Contagem de medidas** retorna somente os 100 resultados distintos principais. Esse limite não se aplica às outras funções estatísticas. Portanto, você geralmente precisará usar um filtro mais preciso primeiro para procurar itens específicos antes de aplicar contagem de medida().

## <a name="use-the-max-and-min-functions-with-the-measure-command"></a>Usar as funções máx e mín com o comando de medida
Há várias situações em que**Measure Max()** e **Measure Min()** são úteis. No entanto, já que elas são opostas, vamos exemplificar Máx() e você pode testar Mín() por conta própria.

Se você consultar eventos de segurança, eles têm uma propriedade de **nível** que pode variar. Por exemplo:

```
Type=SecurityEvent
```

![pesquisar início da contagem de medida](./media/log-analytics-log-searches/oms-search-measure-max01.png)

Se você deseja exibir o valor mais alto para todos os eventos de segurança de um mesmo computador, o agrupar por campo, pode usar

```
Type=ConfigurationAlert | Measure Max(Level) by Computer
```

![pesquisar medir máxima computador](./media/log-analytics-log-searches/oms-search-measure-max02.png)

Isso será exibido para os computadores que têm registros de **nível**, a maioria deles tem pelo menos o nível 8, muitos tinham um nível de 16.

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![pesquisar tempo máximo gerado computador](./media/log-analytics-log-searches/oms-search-measure-max03.png)

Essa função funciona bem com números, mas também funciona com campos de data e hora. Ela é útil para verificar o último ou mais recente carimbo de hora de qualquer porção de dados indexados para cada computador. Por exemplo: quando foi relatado o evento de segurança mais recente para cada máquina?

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## <a name="use-the-avg-function-with-the-measure-command"></a>Usar a função méd com o comando de medida
A função estatística Méd() usada com medidas permite que você calcule o valor médio para alguns campos e agrupar os resultados por tal campo ou por outro. Isso é útil em muitos casos, como dados de desempenho.

Vamos começar com os dados de desempenho. Observe que o OMS atualmente coleta contadores de desempenho para computadores Windows e Linux.

Para pesquisar *todos* os dados de desempenho, a consulta mais básica é:

```
Type=Perf
```

![pesquisar média início](./media/log-analytics-log-searches/oms-search-avg01.png)

A primeira coisa que você notará é que o Log Analytics mostra três perspectivas: a lista, que mostra os registros reais por trás dos gráficos; a tabela, que mostra uma exibição tabular dos dados do contador de desempenho; e as métricas, que mostram gráficos dos contadores de desempenho.

Na imagem acima, há dois conjuntos de campos marcados que indicam o seguinte:

* O primeiro conjunto identifica o nome de contador de desempenho, o nome do objeto e o nome da instância do Windows no filtro da consulta. Esses são os campos que você provavelmente usará com mais frequência como facetas/filtros
* **CounterValue** é o valor real do contador. Neste exemplo, o valor é *75*.
* **TimeGenerated** é 12:51, no formato de 24 horas.

Esta é uma exibição das métricas em um gráfico.

![pesquisar média início](./media/log-analytics-log-searches/oms-search-avg02.png)

Depois de ler sobre a forma de registro Perf e de ler sobre outras técnicas de pesquisa, você pode usar medir Méd() para agregar este tipo de dados numéricos.

Eis um exemplo simples:

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(CounterValue) by Computer
```

![pesquisar média valordeexemplo](./media/log-analytics-log-searches/oms-search-avg03.png)

Neste exemplo, você deve selecionar o contador de desempenho de Tempo Total de CPU e média por Computador Se você quiser restringir os resultados às seis últimas horas, poderá usar o controle de filtro de tempo ou especificá-lo na sua consulta da seguinte maneira:

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer
```

### <a name="to-search-using-the-avg-function-with-the-measure-command"></a>Para pesquisar usando a função méd com o comando de medida
* Na caixa de consulta Pesquisar, digite `Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer`.

Você pode agregar e correlacionar dados *entre* computadores. Por exemplo, imagine que você tenha um conjunto de hosts em algum tipo de farm onde os nós são uns iguais aos outros, fazem o mesmo tipo de trabalho e a carga deve ser balanceada por aproximação. Você pode fazer com que seus contadores todos sigam a consulta abaixo e obtenham as médias para todo o farm. Você pode começar escolhendo os computadores com o exemplo a seguir:

```
Type=Perf AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

Agora que você tem os computadores, só deve querer selecionar dois KPIs (indicadores chave de desempenho): % de uso de CPU e % de espaço livre em disco. Portanto, essa parte da consulta se torna:

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

Agora você pode adicionar computadores e contadores com o exemplo abaixo:

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

Como você tem uma seleção muito específica, o comando **measure Avg()** pode retornar a média não por computador, mas pelo farm, simplesmente agrupando por CounterName. Por exemplo:

```
Type=Perf  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03") | Measure Avg(CounterValue) by CounterName
```

Isso dá a você uma visualização compacta útil de alguns dos KPIs do seu ambiente.

![pesquisar média agrupamento](./media/log-analytics-log-searches/oms-search-avg04.png)

Você pode usar a consulta de pesquisa facilmente em um painel. Por exemplo, você pode salvar a consulta de pesquisa e criar um painel a partir dela chamado *Web Farm KPIs*. Para saber mais sobre o uso de painéis, consulte [Create a custom dashboard in Log Analytics (Criar um painel personalizado no Log Analytics)](log-analytics-dashboards.md).

![pesquisar média painel](./media/log-analytics-log-searches/oms-search-avg05.png)

### <a name="use-the-sum-function-with-the-measure-command"></a>Usar a função soma com o comando de medida
A função soma é semelhante a outras funções do comando de medida. Você pode ver um exemplo de como usar a função de soma em [Pesquisa de Logs IIS W3C no Insights Operacionais do Microsoft Azure](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx).

Você pode usar Máx() e Mín() com números, datetimes e cadeias de caracteres de texto. Com cadeias de caracteres de texto, eles são classificados em ordem alfabética e você obtém a primeira e a última.

No entanto, você não pode usar Soma() com algo diferente de campos numéricos. Isso também se aplica a Méd().

### <a name="use-the-percentile-function-with-the-measure-command"></a>Usar a função percentil com o comando de medida
A função de percentil é semelhante a Méd() e Soma(), pois você só poderá usá-la para campos numéricos. Você pode usar qualquer percentil entre 1 e 99 em um campo numérico. Você também pode usar os comandos **percentile** e **pct**. Veja alguns exemplos:  

```
Type:Perf CounterName:"DiskTransers/sec" |measure percentile95(CurrentValue) by Computer
```
```
Type:Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" | measure pct65(CurrentValue) by InstanceName
```

## <a name="use-the-where-command"></a>Usar o comando where
O comando where funciona como um filtro, mas ele pode ser aplicado ao pipeline para filtrar os resultados agregados que foram produzidos pelo comando Medir, em vez de resultados brutos que são filtrados no início de uma consulta.

Por exemplo:

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer
```

Você pode adicionar outro caractere de barra vertical "|" e o comando Where para obter apenas os computadores cuja média de CPU esteja acima de 80%, como no exemplo abaixo:

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer | Where AVGCPU>80
```

Se você estiver familiarizado com o Microsoft System Center - Operations Manager, pode pensar no comando where em termos de pacote de gerenciamento de comando. Se o exemplo fosse uma regra, a primeira parte da consulta seria a fonte de dados e o comando where seria a detecção de condição.

Você pode usar a consulta como um bloco em **Meu Painel**, como um monitor de classificações, para ver quando as CPUs do computador são utilizadas em excesso. Para saber mais sobre painéis, consulte [Create a custom dashboard in Log Analytics (Criar um painel personalizado no Log Analytics)](log-analytics-dashboards.md). Você também pode criar e usar painéis utilizando o aplicativo móvel. Para saber mais, veja [Aplicativo móvel do OMS ](http://www.windowsphone.com/en-us/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865). Nos dois blocos inferiores da imagem a seguir, você pode ver o monitor exibindo uma lista e como um número. No fundo, convém sempre ter o número como zero e a lista em branco. Caso contrário, isso indica uma condição de alerta. Se necessário, você pode usá-lo para dar uma olhada em quais computadores estão sob pressão.

![painel móvel](./media/log-analytics-log-searches/oms-search-mobile.png)

## <a name="use-the-in-operator"></a>Usar o operador in
O operador *IN*, juntamente com *NOT IN* permite que você use as subpesquisas, que são as pesquisas que incluem outra pesquisa como um argumento. Elas estão contidas em chaves {} dentro de outra pesquisa *primária* ou *externa*. O resultado de um subpesquisa, muitas vezes uma lista de resultados distintos, é usado em seguida como um argumento em sua pesquisa primária.

Você pode usar subpesquisas para fazer a correspondência dos subconjuntos de dados que não podem ser descritos diretamente em uma expressão de pesquisa, mas que podem ser gerados a partir de uma pesquisa. Por exemplo, se você estiver interessado em usar uma pesquisa para localizar todos os eventos de *computadores sem atualizações de segurança*, precisará projetar uma subpesquisa que primeiro identifique esses *computadores sem atualizações de segurança* antes de encontrar eventos que pertençam a esses hosts.

Portanto, você poderia expressar os *computadores que no momento não têm as atualizações de segurança necessárias* com a seguinte consulta:

```
Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer
```    

![Exemplo de pesquisa IN](./media/log-analytics-log-searches/oms-search-in01-revised.png)

Assim que você tiver a lista, poderá usar a pesquisa como uma pesquisa interna para alimentar a lista de computadores em uma pesquisa externa (primária) que irá procurar eventos para esses computadores. Você pode fazer isso colocando a pesquisa entre chaves e alimentando seus resultados como valores possíveis para um filtro/campo na pesquisa externa usando o operador IN. A consulta é semelhante a:

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer}
```
![Exemplo de pesquisa IN](./media/log-analytics-log-searches/oms-search-in02-revised.png)

Além disso, observe o filtro de tempo usado na pesquisa interna porque a Avaliação da Atualização do Sistema tira um instantâneo de todos os computadores a cada 24 horas. Você pode tornar a consulta interna mais leve e precisa pesquisando apenas por um dia. Em vez disso, a pesquisa externa usa a seleção de tempo na interface do usuário, recuperando os eventos dos últimos sete dias. Confira [Operadores boolianos](#boolean-operators) para saber mais sobre operadores de tempo.

Como na verdade você só usa os resultados da pesquisa interna como um valor de filtro para a externa, ainda poderá aplicar comandos à pesquisa externa. Por exemplo, você ainda pode agrupar os eventos acima com outro comando de medida:

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer} | measure count() by Source
```

![Exemplo de pesquisa IN](./media/log-analytics-log-searches/oms-search-in03-revised.png)

Em geral, você deseja que sua pesquisa interna seja executada rapidamente, já que o Log Analytics tem tempos limite no lado do serviço para ele e também para retornar uma pequena quantidade de resultados. Se a consulta interna retornar mais resultados, a lista de resultados será truncada, o que poderá fazer com que a pesquisa externa retorne resultados incorretos.

Outra regra é que a pesquisa interna atualmente precisa fornecer resultados *agregados*. Em outras palavras, ela deve conter um comando *measure*. Atualmente, você não pode alimentar uma pesquisa externa com resultados brutos.

Além disso, pode haver apenas um operador IN e ele deve ser o último filtro na consulta. Vários operadores IN não podem ser usados com O. Isso basicamente impede a execução de várias subpesquisas: o ponto importante é que apenas uma subpesquisa/pesquisa interna pode ser feita para cada pesquisa externa.

Mesmo com esses limites, o IN habilita vários tipos de pesquisa correlacionados e permite que você defina algo semelhante aos grupos, como computadores, usuários ou arquivos. Seja o que for que os campos em seus dados contenham. Veja mais alguns exemplos:

**Todas as atualizações ausentes em computadores em que a configuração de Atualização Automática está desabilitada**

```
Type=Update UpdateState=Needed Optional=false Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual | measure count() by Computer} | measure count() by KBID
```

**Todos os eventos de erro de computadores que estejam executando o SQL Server (=onde a Avaliação do SQL foi executada)**

```
Type=Event EventLevelName=error Computer IN {Type=SQLAssessmentRecommendation | measure count() by Computer}
```

**Todos os eventos de segurança a partir de computadores que sejam Controladores de Domínio (= onde a Avaliação do AD foi executada)**

```
Type=SecurityEvent Computer IN { Type=ADAssessmentRecommendation | measure count() by Computer }
```

**Que outras contas fizeram logon nos mesmos computadores onde a conta BACONLAND\jochan fez logon?**

```
Type=SecurityEvent EventID=4624   Account!="BACONLAND\\jochan" Computer IN { Type=SecurityEvent EventID=4624   Account="BACONLAND\\jochan" | measure count() by Computer } | measure count() by Account
```

## <a name="use-the-distinct-command"></a>Usar o comando distinct
Como o nome sugere, esse comando fornece uma lista de valores distintos para um campo. É surpreendentemente simples, mas muito útil. O mesmo pode ser obtido com o comando measure count(), como mostrado abaixo.

```
Type=Event | Measure count() by Computer
```

![Exemplo de comando de pesquisa DISTINCT](./media/log-analytics-log-searches/oms-search-distinct01-revised.png)

No entanto, se você só estiver interessado em uma lista de valores distintos e não na contagem de documentos que tenham esses valores, o DISTINCT pode fornecer uma saída mais limpa e mais fácil de ler, além de uma sintaxe mais curta, como mostrado abaixo.

```
Type=Event | Distinct Computer
```
![Exemplo de comando de pesquisa DISTINCT](./media/log-analytics-log-searches/oms-search-distinct02-revised.png)

## <a name="use-the-countdistinct-function-with-the-measure-command"></a>Usar a função countdistinct com o comando measure
A função countdistinct conta o número de valores distintos em cada grupo. Por exemplo, ela poderia ser usada para contar o número de computadores exclusivos relatados para cada Tipo:

```
* | measure countdistinct(Computer) by Type
```

![OMS-countdistinct](./media/log-analytics-log-searches/oms-countdistinct.png)

## <a name="use-the-measure-interval-command"></a>Usar o comando measure interval
Com a coleta de dados de desempenho em tempo real, você pode coletar e visualizar qualquer contador de desempenho no Log Analytics. Simplesmente inserindo a consulta **Type:Perf** retornará milhares de gráficos de métricas com base no número de contadores e de servidores no ambiente do Log Analytics. Com a agregação de métrica sob demanda, você pode examinar as métricas gerais em seu ambiente em um alto nível e analisar mais profundamente dados mais granulares conforme o necessário.

Digamos que você queira saber qual é a CPU média entre todos os seus computadores. Examinar a CPU média de todos os computadores pode não ser útil porque os resultados podem ser reduzidos. Para ver mais detalhes, você pode agregar o resultado em períodos menores de tempo, além de examinar uma série de tempo em dimensões diferentes. Por exemplo, você pode executar a medição do uso médio de CPU por hora em todos os computadores, da seguinte maneira:

```
Type:Perf CounterName="% Processor Time" InstanceName="_Total" | measure avg(CounterValue) by Computer Interval 1HOUR
```

![intervalo médio de medidas](./media/log-analytics-log-searches/oms-measure-avg-interval.png)

Por padrão, esses resultados serão exibidos em um gráfico de linhas interativo com várias séries.  Esse gráfico oferece suporte à alternância de séries (com o redimensionamento do eixo y), ao zoom e à focalização.  A opção de exibição de tabela ainda está disponível para exibir os dados brutos, se necessário.

Você também pode agrupar por outros campos. Neste exemplo, estou examinando todos os contadores de % para um computador específico e quero saber quais são os 70 percentuais por hora de cada contador:

```
Type:Perf Computer=beefpatty4 CounterName=%* InstanceName=_Total | measure percentile70(CounterValue) by CounterName Interval 1HOUR
```
Uma coisa a ser observada é que essas consultas não estão limitadas aos contadores de desempenho. Você pode aplicá-las a qualquer métrica. Neste exemplo, examinarei os logs do IIS W3C. Quero saber qual é o tempo máximo durante um intervalo de cinco minutos para o processamento de cada solicitação:

```
Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES
```

### <a name="use-multiple-aggregates-in-one-query"></a>Usar várias agregações em uma consulta
Você pode especificar várias cláusulas agregadas em um comando de medida.  Cada uma delas pode ter um alias de forma independente.  Se não for fornecido um alias, o nome de campo resultante será a função de agregação usada (ou seja, "avg(CounterValue)" para avg(CounterValue)).

 ```
Type=WireData | measure avg(ReceivedBytes), avg(SentBytes) by Direction interval 1hour
```
![OMS-multiaggregates1](./media/log-analytics-log-searches/oms-multiaggregates1.png)

Veja outro exemplo:

 ```
* | measure countdistinct(Computer) as Computers, count() as TotalRecords by Type
```


## <a name="next-steps"></a>Próximas etapas
Para obter outras informações sobre pesquisas de log, veja:

* Use [Campos personalizados no Log Analytics](log-analytics-custom-fields.md) para estender as pesquisas de log.
* Examine a [referência de pesquisa de log do Log Analytics](log-analytics-search-reference.md) para exibir todos os campos de pesquisa e as facetas disponíveis no Log Analytics.
