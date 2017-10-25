---
title: Utilizando o portal de Pesquisa de Logs no Azure Log Analytics | Microsoft Docs
description: "Este artigo inclui um tutorial que descreve como criar pesquisas de logs e analisar dados armazenados em seu espaço de trabalho do Log Analytics utilizando o portal de Pesquisa de Logs.  O tutorial inclui executar algumas consultas simples para retornar diferentes tipos de dados e analisar os resultados."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: bwren
ms.openlocfilehash: 6fc556ceb34cde26d5f3789a2397cdaa34b0b84d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-log-searches-in-azure-log-analytics-using-the-log-search-portal"></a>Crie pesquisas de logs no Azure Log Analytics utilizando o portal de Pesquisa de Logs

> [!NOTE]
> Este artigo descreve o portal de Pesquisa de Logs no Azure Log Analytics utilizando a nova linguagem de consulta.  Você pode saber mais sobre a nova linguagem e obter o procedimento para fazer upgrade do espaço de trabalho em [Fazer upgrade do espaço de trabalho do Azure Log Analytics para uma nova pesquisa de logs](log-analytics-log-search-upgrade.md).  
>
> Se o espaço de trabalho não foi atualizado para a nova linguagem de consulta, você deverá consultar [Localizar dados usando pesquisas de logs no Log Analytics](log-analytics-log-searches.md) para obter informações sobre a versão atual do portal de Pesquisa de Logs.

Este artigo inclui um tutorial que descreve como criar pesquisas de logs e analisar dados armazenados em seu espaço de trabalho do Log Analytics utilizando o portal de Pesquisa de Logs.  O tutorial inclui executar algumas consultas simples para retornar diferentes tipos de dados e analisar os resultados.  Ele concentra-se em recursos no portal de Pesquisa de Logs para modificar a consulta em vez de modificá-la diretamente.  Para obter detalhes sobre a edição direta da consulta, consulte a [Referência de linguagem de consulta](https://go.microsoft.com/fwlink/?linkid=856079).

Para criar pesquisas no portal de Análise Avançada em vez do portal de Pesquisa de Logs, consulte [Introdução ao Portal de Análise](https://go.microsoft.com/fwlink/?linkid=856587).  Ambos os portais utilizam a mesma linguagem de consulta para acessar os mesmos dados no espaço de trabalho do Log Analytics.

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial assume que você já possui um espaço de trabalho do Log Analytics com pelo menos uma fonte conectada que gera dados para as consultas a serem analisadas.  

- Se você não possuir um espaço de trabalho, poderá criar um livre utilizando o procedimento em [Introdução a um espaço de trabalho do Log Analytics](log-analytics-get-started.md).
- Conecte ao menos um [agente do Windows](log-analytics-windows-agents.md) ou um [agente do Linux](log-analytics-linux-agents.md) ao espaço de trabalho.  

## <a name="open-the-log-search-portal"></a>Abra o portal de Pesquisa de Logs
Inicie abrindo o portal de Pesquisa de Logs.  É possível acessá-lo no portal do Azure ou no portal do OMS.

1. Abra o portal do Azure.
2. Navegue até o Log Analytics e selecione seu espaço de trabalho.
3. Selecione **Pesquisa de Logs**para permanecer no Portal Azure ou inicie o portal do OMS, selecionando **Portal do OMS** e, em seguida, clicando no botão Pesquisa de Logs.

![Botão Pesquisar Log](media/log-analytics-log-search-log-search-portal/log-search-button.png)

## <a name="create-a-simple-search"></a>Crie uma pesquisa simples
A maneira mais rápida de recuperar alguns dados para trabalhar é uma consulta simples que retorna todos os registros na tabela.  Se você tiver algum cliente Windows ou Linux conectado ao seu espaço de trabalho, você terá dados na tabela de Eventos (Windows) ou Syslog (Linux).

Digite um das seguintes consultas na caixa de pesquisa e clique no botão de pesquisa.  

```
Event
```
```
Syslog
```

Os dados são retornados na exibição de lista padrão e você poderá consultar quantos registros foram retornados no total.

![Consulta simples](media/log-analytics-log-search-log-search-portal/log-search-portal-01.png)

Apenas as primeiras propriedades de cada registro são exibidas.  Clique em **mostrar mais** para exibir todas as propriedades de um registro particular.

![Detalhes do registro](media/log-analytics-log-search-log-search-portal/log-search-portal-02.png)

## <a name="set-the-time-scope"></a>Defina o escopo de tempo
Cada registro coletado pelo Log Analytics possui uma propriedade **TimeGenerated** que contém a data e hora em que a gravação foi criada.  Uma consulta no portal de Pesquisa de Logs somente retorna registros com **TimeGenerated** dentro do escopo de tempo exibido no lado esquerdo da tela.  

É possível alterar o filtro de tempo, selecionando o menu suspenso ou modificando o controle deslizante.  O controle deslizante exibe um gráfico de barras que mostra o número relativo de registros para cada segmento de tempo dentro do intervalo.  Este segmento irá variar dependendo do intervalo.

O escopo de tempo padrão é **1 dia**.  Altere esse valor para **7 dias** e o número total de registros deverá aumentar.

![Escopo de data e hora](media/log-analytics-log-search-log-search-portal/log-search-portal-03.png)

## <a name="filter-results-of-the-query"></a>Filtre os resultados da consulta
No lado esquerdo da tela está o painel de filtro que permite adicionar filtragem à consulta sem modificá-la diretamente.  Várias propriedades dos registros retornados são exibidas com seus dez melhores valores com a contagem de registros.

Se você estiver trabalhando com **Evento**, marque a caixa de seleção próxima ao **Erro** em **EVENTLEVELNAME**.   Se estiver trabalhando com **Syslog**, marque a caixa de seleção próxima ao **erro** em **SEVERITYLEVEL**.  Isso altera a consulta para uma das seguintes opções para limitar os resultados aos eventos de erro.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Filtro](media/log-analytics-log-search-log-search-portal/log-search-portal-04.png)

Adicione propriedades ao painel de filtro, selecionando **Adicionar para filtros** do menu de propriedades em um dos registros.

![Adicionar ao menu de filtro](media/log-analytics-log-search-log-search-portal/log-search-portal-02a.png)

É possível definir o mesmo filtro, selecionando **Filtro** no menu de propriedades para um registro com o valor que deseja filtrar.  

Você possui somente a opção **Filtro** para propriedades com seu nome em azul.  Estes são campos *pesquisáveis* que estão indexados para as condições de pesquisa.  Os campos em cinza são *campos pesquisáveis de texto livre* que somente possuem a opção **Mostrar referências**.  Esta opção retorna registros que possuem esse valor em qualquer propriedade.

![Menu de filtro](media/log-analytics-log-search-log-search-portal/log-search-portal-01a.png)

É possível agrupar os resultados em uma propriedade única, selecionando a opção **Agrupar por** no menu de registro.  Isso adicionará um operador [resumido](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) à sua consulta que exibirá os resultados em um gráfico.  É possível agrupar em mais de uma propriedade, mas será necessário editar a consulta diretamente.  Selecione o menu de registro próximo à propriedade **Computador** e selecione **Agrupar por 'Computador'**.  

![Agrupar por computador](media/log-analytics-log-search-log-search-portal/log-search-portal-10.png)

## <a name="work-with-results"></a>Trabalhar com os resultados
O portal de Pesquisa de Logs possui uma variedade de recursos para trabalhar com os resultados de uma consulta.  É possível classificar, filtrar e agrupar os resultados para analisar os dados sem modificar a consulta real.  Os resultados de uma consulta não são classificados por padrão.

Para exibir os dados em formulário de tabela que fornece opções adicionais para filtrar e classificar, clique em **Tabela**.  

![Exibição da tabela](media/log-analytics-log-search-log-search-portal/log-search-portal-05.png)

Clique na seta por um registro para exibir os detalhes desse registro.

![Classificar resultados](media/log-analytics-log-search-log-search-portal/log-search-portal-06.png)

Classifique em qualquer campo, clicando no cabeçalho de coluna.

![Classificar resultados](media/log-analytics-log-search-log-search-portal/log-search-portal-07.png)

Filtre os resultados em um valor específico na coluna, clicando no botão de filtro e fornecendo uma condição de filtro.

![Resultados do filtro](media/log-analytics-log-search-log-search-portal/log-search-portal-08.png)

Agrupe uma coluna, arrastando o cabeçalho da coluna para o topo dos resultados.  É possível agrupar em campos múltiplos, arrastando colunas múltiplas para o topo.

![Resultados de grupo](media/log-analytics-log-search-log-search-portal/log-search-portal-09.png)



## <a name="work-with-performance-data"></a>Trabalhe com dados de desempenho
Os dados de desempenho para agentes do Windows e Linux são armazenados no espaço de trabalho do Log Analytics na tabela **Perf**.  Os registros de desempenho são semelhante a qualquer outro registro e é possível gravar uma consulta simples que retorna todos os registros de desempenho, assim como eventos.

```
Perf
```

![Dados de desempenho](media/log-analytics-log-search-log-search-portal/log-search-portal-11.png)

Retornando milhões de registros para todos contadores e objetos de desempenho, porém, não é muito útil.  É possível utilizar os mesmos métodos usados acima para filtrar os dados ou apenas digitar a seguinte consulta diretamente na caixa de pesquisa de logs.  Isso retorna somente os registros de utilização do processador para computadores Windows e Linux.

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![Utilização do processador](media/log-analytics-log-search-log-search-portal/log-search-portal-12.png)

Isso limita os dados para um contador particular, mas ainda não o coloca em uma forma particularmente útil.  É possível exibir os dados em um gráfico de linhas, mas primeiro deverá agrupar por computador e TimeGenerated.  Para agrupar em campos múltiplo será necessário modificar a consulta diretamente, então, modifique a consulta para o seguinte.  Isso usa a função [avg](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) na propriedade **CounterValue** para calcular o valor médio ao longo de cada hora.

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![Gráfico de dados de desempenho](media/log-analytics-log-search-log-search-portal/log-search-portal-13.png)

Agora que os dados estão adequadamente agrupados, você poderá exibi-los em um gráfico visual, adicionando o operador [renderizar](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator).  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![Gráfico de Linhas](media/log-analytics-log-search-log-search-portal/log-search-portal-14.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a linguagem de consulta do Log Analytics em [Introdução ao Portal de Análise](https://go.microsoft.com/fwlink/?linkid=856079).
- Explore um tutorial utilizando o [Portal de Análise Avançada](https://go.microsoft.com/fwlink/?linkid=856587), o qual permite executar as mesmas consultas e acessar os mesmos dados que o portal de Pesquisa de Logs.
