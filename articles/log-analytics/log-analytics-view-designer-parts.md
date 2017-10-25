---
title: "Referência da parte do Criador de Modos de Exibição no OMS Log Analytics | Microsoft Docs"
description: "O Designer de modos de exibição do Log Analytics permite que você crie exibições personalizadas no console do OMS que contêm diferentes visualizações de dados no repositório do OMS. Este artigo fornece uma referência de configurações para cada uma das partes de visualização disponíveis para uso em exibições personalizadas."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: 5718d620-b96e-4d33-8616-e127ee9379c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: bwren
ms.openlocfilehash: 40a6101576708936404447576d704a49666143fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-view-designer-visualization-part-reference"></a>Referência da parte de visualização do Designer de modos de exibição do Log Analytics
O Designer de modos de exibição do Log Analytics permite que você crie exibições personalizadas no console do OMS que contêm diferentes visualizações de dados do repositório do OMS. Este artigo fornece uma referência de configurações para cada uma das partes de visualização disponíveis para uso em exibições personalizadas.

Outros artigos disponíveis para o Designer de modo de exibição:

* [Designer de Modos de Exibição](log-analytics-view-designer.md) – visão geral do Designer de Modos de Exibição e procedimentos para criar e editar modos de exibição personalizados.
* [Referência de bloco](log-analytics-view-designer-tiles.md) – Referência das configurações de cada um dos blocos disponíveis para uso nas exibições personalizadas.

>[!NOTE]
> Se o seu espaço de trabalho tiver sido atualizado para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md), consultas em todas as exibições deverão ser gravadas na [nova linguagem de consulta](https://go.microsoft.com/fwlink/?linkid=856078).  Todas as exibições que foram criadas antes de atualizar o espaço de trabalho serão automaticamente convertidas.

A tabela a seguir lista os diferentes tipos de blocos disponíveis no Designer de modos de exibição.  As seções a seguir descrevem cada tipo de bloco em detalhes e suas propriedades.

| Tipo de exibição | Descrição |
|:--- |:--- |
| [Lista de consultas](#list-of-queries-part) |Exibe uma lista de consultas de pesquisa de log.  O usuário pode clicar em cada consulta para exibir seus resultados. |
| [Número e lista](#number-amp-list-part) |O cabeçalho tem um número único mostrando a contagem de registros de uma consulta de pesquisa de log.  A lista exibe os dez primeiros resultados de uma consulta com um gráfico indicando o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo. |
| [Dois números e lista](#two-numbers-amp-list-part) |O cabeçalho tem dois números mostrando a contagem de registros de consultas de pesquisa de log separadas.  A lista exibe os dez primeiros resultados de uma consulta com um gráfico indicando o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo. |
| [Rosca e lista](#donut-amp-list-part) |O cabeçalho exibe um único número resumido de uma coluna de valor em uma consulta de log.  A rosca exibe graficamente os resultados dos três registros principais. |
| [Duas linhas do tempo e lista](#two-timelines-amp-list-part) |O cabeçalho exibe os resultados de duas consultas de log ao longo do tempo como gráficos de coluna com um balão exibindo um único número resumido de uma coluna de valor em uma consulta de log.  A lista exibe os dez primeiros resultados de uma consulta com um gráfico indicando o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo. |
| [Informações](#information-part) |O cabeçalho exibe texto estático e um link opcional.  A lista exibe um ou mais itens com texto estático e o título. |
| [Gráfico de linhas, balão e lista](#line-chart-callout-amp-list-part) |O cabeçalho exibe um gráfico de linhas com várias séries de uma consulta de log ao longo do tempo, além de um balão com um valor resumido.  A lista exibe os dez primeiros resultados de uma consulta com um gráfico indicando o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo. |
| [Gráfico de linhas e lista](#line-chart-amp-list-part) |O cabeçalho exibe um gráfico de linhas com várias séries de uma consulta de log ao longo do tempo.  A lista exibe os dez primeiros resultados de uma consulta com um gráfico indicando o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo. |
| [Pilha de parte de gráficos de linha](#stack-of-line-charts-part) |Exibe três gráficos de linhas separados com várias séries de uma consulta de log ao longo do tempo. |

## <a name="list-of-queries-part"></a>Lista da parte de consultas
Exibe uma lista de consultas de pesquisa de log.  O usuário pode clicar em cada consulta para exibir seus resultados.  O modo de exibição incluirá uma única consulta por padrão e você pode clicar em **+ Consulta** para incluir consultas adicionais.

![Lista da exibição de consultas](media/log-analytics-view-designer/view-list-queries.png)

| Configuração | Descrição |
|:--- |:--- |
| **Geral** | |
| Title |Texto a ser exibido na parte superior da exibição. |
| Novo Grupo |Selecione para criar um novo grupo na exibição, iniciando na exibição atual. |
| Filtros pré-selecionados |Lista delimitada por vírgulas de propriedades para incluir no painel de filtro esquerdo quando o usuário seleciona uma consulta. |
| Modo de renderização |Exibição inicial que aparece quando a consulta é selecionada.  O usuário pode selecionar qualquer exibição disponível depois de abrir a consulta. |
| **Consultas** | |
| Consulta de pesquisa |Consulta a executar. |
| Nome amigável |Nome descritivo da consulta para exibir para o usuário. |

## <a name="number--list-part"></a>Parte de número e lista
O cabeçalho tem um número único mostrando a contagem de registros de uma consulta de pesquisa de log.  A lista exibe os dez primeiros resultados de uma consulta com um gráfico indicando o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo.

![Lista da exibição de consultas](media/log-analytics-view-designer/view-number-list.png)

| Configuração | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do Grupo |Texto a ser exibido na parte superior da exibição. |
| Novo Grupo |Selecione para criar um novo grupo na exibição, iniciando na exibição atual. |
| ícone |Arquivo de imagem para exibir ao lado do resultado no cabeçalho. |
| Usar Ícone |Selecione para que o ícone seja exibido. |
| **Título** | |
| Legenda |Texto a ser exibido na parte superior do cabeçalho. |
| Consultar |Consulta a executar para o cabeçalho.  A contagem do número de registros retornados pela consulta será exibida. |
| **Lista** | |
| Consultar |Consulta a executar para a lista.  As duas primeiras propriedades para os dez primeiros registros nos resultados serão exibidas.  A primeira propriedade deve ser um valor de texto e a segunda um valor numérico.  As barras são criadas automaticamente com base no valor relativo da coluna numérica.<br><br>Use o comando Classificar na consulta para classificar os registros na lista.  O usuário pode clicar em Ver tudo para executar a consulta e retornar todos os registros. |
| Ocultar gráfico |Selecione para desabilitar o gráfico à direita da coluna numérica. |
| Habilitar minigráficos |Selecione para exibir minigráficos em vez de barras horizontais.  Consulte [Configurações Comuns](#sparklines) para obter detalhes. |
| Cor |Cor das barras ou minigráficos. |
| Separador de Valor e Nome |Delimitador de caractere único se desejar analisar a próxima propriedade em vários valores.  Consulte [Configurações Comuns](#name-value-separator) para obter detalhes. |
| Consulta de navegação |Consulta a executar quando o usuário seleciona um item na lista.  Consulte [Configurações Comuns](#navigation-query) para obter detalhes. |
| **Lista** |**> Títulos de coluna** |
| Nome |Texto a ser exibido na parte superior da primeira coluna da lista. |
| Valor |Texto a ser exibido na parte superior da segunda coluna da lista. |
| **Lista** |**> Limites** |
| Habilitar limites |Selecione para habilitar limites.  Consulte [Configurações Comuns](#thresholds) para obter detalhes. |

## <a name="two-numbers--list-part"></a>Parte de dois números e lista
O cabeçalho tem dois números mostrando a contagem de registros de consultas de pesquisa de log separadas.  A lista exibe os dez primeiros resultados de uma consulta com um gráfico indicando o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo.

![Exibição de dois números e lista](media/log-analytics-view-designer/view-two-numbers-list.png)

| Configuração | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do Grupo |Texto a ser exibido na parte superior da exibição. |
| Novo Grupo |Selecione para criar um novo grupo na exibição, iniciando na exibição atual. |
| ícone |Arquivo de imagem para exibir ao lado do resultado no cabeçalho. |
| Usar Ícone |Selecione para que o ícone seja exibido. |
| **Título** | |
| Legenda |Texto a ser exibido na parte superior do cabeçalho. |
| Consultar |Consulta a executar para o cabeçalho.  A contagem do número de registros retornados pela consulta será exibida. |
| **Lista** | |
| Consultar |Consulta a executar para a lista.  As duas primeiras propriedades para os dez primeiros registros nos resultados serão exibidas.  A primeira propriedade deve ser um valor de texto e a segunda um valor numérico.  As barras são criadas automaticamente com base no valor relativo da coluna numérica.<br><br>Use o comando Classificar na consulta para classificar os registros na lista.  O usuário pode clicar em Ver tudo para executar a consulta e retornar todos os registros. |
| Ocultar gráfico |Selecione para desabilitar o gráfico à direita da coluna numérica. |
| Habilitar minigráficos |Selecione para exibir minigráficos em vez de barras horizontais.  Consulte [Configurações Comuns](#sparklines) para obter detalhes. |
| Cor |Cor das barras ou minigráficos. |
| Operação |Operação a ser executada para o minigráfico.  Consulte [Configurações Comuns](#sparklines) para obter detalhes. |
| Separador de Valor e Nome |Delimitador de caractere único se desejar analisar a próxima propriedade em vários valores.  Consulte [Configurações Comuns](#name-value-separator) para obter detalhes. |
| Consulta de navegação |Consulta a executar quando o usuário seleciona um item na lista.  Consulte [Configurações Comuns](#navigation-query) para obter detalhes. |
| **Lista** |**> Títulos de coluna** |
| Nome |Texto a ser exibido na parte superior da primeira coluna da lista. |
| Valor |Texto a ser exibido na parte superior da segunda coluna da lista. |
| **Lista** |**> Limites** |
| Habilitar limites |Selecione para habilitar limites.  Consulte [Configurações Comuns](#thresholds) para obter detalhes. |

## <a name="donut--list-part"></a>Parte de rosca e lista
O cabeçalho exibe um único número resumido de uma coluna de valor em uma consulta de log.  A rosca exibe graficamente os resultados dos três registros principais.

![Exibição de rosca e lista](media/log-analytics-view-designer/view-donut-list.png)

| Configuração | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do Grupo |Texto a exibir na parte superior do bloco. |
| Novo Grupo |Selecione para criar um novo grupo na exibição, iniciando na exibição atual. |
| ícone |Arquivo de imagem para exibir ao lado do resultado no cabeçalho. |
| Usar Ícone |Selecione para que o ícone seja exibido. |
| **Cabeçalho** | |
| Title |Texto a ser exibido na parte superior do cabeçalho. |
| Subtítulo |Texto a ser exibido abaixo do Título na parte superior do cabeçalho. |
| **Rosca** | |
| Consultar |Consulta a executar para a rosca.  A primeira propriedade deve ser um valor de texto e a segunda um valor numérico. |
| **Rosca** |**> Centro** |
| Texto |Texto a exibir abaixo do valor dentro da rosca. |
| Operação |A operação a executar na propriedade de valor a resumir como um único valor.<br><br>– Soma: adicionar os valores de todos os registros.<br>– Porcentagem: porcentagem dos registros retornados pelos valores em **Valores de resultado usados na operação do centro** para o total de registros na consulta. |
| Valores de resultado usados na operação do centro |Opcionalmente, clique no sinal de adição para adicionar um ou mais valores.  Os resultados da consulta serão limitados aos registros com os valores de propriedade que você especificar.  Se nenhum valor for adicionado, todos os registros serão incluídos na consulta. |
| **Opções adicionais** |**> Cores** |
| Cor 1<br>Cor 2<br>Cor 3 |Selecione a cor de cada um dos valores exibidos na rosca. |
| **Opções adicionais** |**> Mapeamento de Cores Avançado** |
| Valor do campo |Digite o nome de um campo para exibi-lo como uma cor diferente se ele estiver incluído na rosca. |
| Cor |Selecione a cor do campo exclusivo. |
| **Lista** | |
| Consultar |Consulta a executar para a lista.  A contagem do número de registros retornados pela consulta será exibida. |
| Ocultar gráfico |Selecione para desabilitar o gráfico à direita da coluna numérica. |
| Habilitar minigráficos |Selecione para exibir minigráficos em vez de barras horizontais.  Consulte [Configurações Comuns](#sparklines) para obter detalhes. |
| Cor |Cor das barras ou minigráficos. |
| Operação |Operação a ser executada para o minigráfico.  Consulte [Configurações Comuns](#sparklines) para obter detalhes. |
| Separador de Valor e Nome |Delimitador de caractere único se desejar analisar a próxima propriedade em vários valores.  Consulte [Configurações Comuns](#name-value-separator) para obter detalhes. |
| Consulta de navegação |Consulta a executar quando o usuário seleciona um item na lista.  Consulte [Configurações Comuns](#navigation-query) para obter detalhes. |
| **Lista** |**> Títulos de coluna** |
| Nome |Texto a ser exibido na parte superior da primeira coluna da lista. |
| Valor |Texto a ser exibido na parte superior da segunda coluna da lista. |
| **Lista** |**> Limites** |
| Habilitar limites |Selecione para habilitar limites.  Consulte [Configurações Comuns](#thresholds) para obter detalhes. |

## <a name="two-timelines--list-part"></a>Parte de duas linhas do tempo e lista
O cabeçalho exibe os resultados de duas consultas de log ao longo do tempo como gráficos de coluna com um balão exibindo um único número resumido de uma coluna de valor em uma consulta de log.  A lista exibe os dez primeiros resultados de uma consulta com um gráfico indicando o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo.

![Exibição de duas linhas do tempo e lista](media/log-analytics-view-designer/view-two-timelines-list.png)

| Configuração | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do Grupo |Texto a exibir na parte superior do bloco. |
| Novo Grupo |Selecione para criar um novo grupo na exibição, iniciando na exibição atual. |
| ícone |Arquivo de imagem para exibir ao lado do resultado no cabeçalho. |
| Usar Ícone |Selecione para que o ícone seja exibido. |
| **Primeiro gráfico<br>Segundo gráfico** | |
| Legenda |Texto exibido sob o balão para a primeira série. |
| Cor |Cor a ser usada para as colunas na série. |
| Consultar |Consulta a executar para a primeira série.  A contagem do número de registros em cada intervalo de tempo será representada pelas colunas do gráfico. |
| Operação |A operação a executar na propriedade de valor a resumir como um único valor para o balão.<br><br>– Soma: soma dos valores de todos os registros.<br>– Média: média do valor de todos os registros.<br>– Última Amostra: o valor do último intervalo incluído no gráfico.<br>– Primeira Amostra: o valor do primeiro intervalo incluído no gráfico.<br>– Contagem: contagem de todos os registros retornados pela consulta. |
| **Lista** | |
| Consultar |Consulta a executar para a lista.  A contagem do número de registros retornados pela consulta será exibida. |
| Ocultar gráfico |Selecione para desabilitar o gráfico à direita da coluna numérica. |
| Habilitar minigráficos |Selecione para exibir minigráficos em vez de barras horizontais.  Consulte [Configurações Comuns](#sparklines) para obter detalhes. |
| Cor |Cor das barras ou minigráficos. |
| Operação |Operação a ser executada para o minigráfico.  Consulte [Configurações Comuns](#sparklines) para obter detalhes. |
| Consulta de navegação |Consulta a executar quando o usuário seleciona um item na lista.  Consulte [Configurações Comuns](#navigation-query) para obter detalhes. |
| **Lista** |**> Títulos de coluna** |
| Nome |Texto a ser exibido na parte superior da primeira coluna da lista. |
| Valor |Texto a ser exibido na parte superior da segunda coluna da lista. |
| **Lista** |**> Limites** |
| Habilitar limites |Selecione para habilitar limites.  Consulte [Configurações Comuns](#thresholds) para obter detalhes. |

## <a name="information-part"></a>Parte de informações
O cabeçalho exibe texto estático e um link opcional.  A lista exibe um ou mais itens com texto estático e o título.

![Exibição de informações](media/log-analytics-view-designer/view-information.png)

| Configuração | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do Grupo |Texto a exibir na parte superior do bloco. |
| Novo Grupo |Selecione para criar um novo grupo na exibição, iniciando na exibição atual. |
| Cor |Cor da tela de fundo do cabeçalho. |
| **Cabeçalho** | |
| Imagem |Arquivo de imagem a ser exibida no cabeçalho. |
| Rótulo |Texto a ser exibido no cabeçalho. |
| **Cabeçalho** |**> Link** |
| Rótulo |Texto do link. |
| Url |URL do link. |
| **Itens de Informações** | |
| Title |Texto a ser exibido para o título de cada item. |
| Conteúdo |Texto a ser exibido para cada item. |

## <a name="line-chart-callout--list-part"></a>Parte de gráfico de linhas, balão e lista
O cabeçalho exibe um gráfico de linhas com várias séries de uma consulta de log ao longo do tempo, além de um balão com um valor resumido.  A lista exibe os dez primeiros resultados de uma consulta com um gráfico indicando o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo.

![Exibição de gráfico de linhas, balão e lista](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Configuração | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do Grupo |Texto a exibir na parte superior do bloco. |
| Novo Grupo |Selecione para criar um novo grupo na exibição, iniciando na exibição atual. |
| ícone |Arquivo de imagem para exibir ao lado do resultado no cabeçalho. |
| Usar Ícone |Selecione para que o ícone seja exibido. |
| **Cabeçalho** | |
| Title |Texto a ser exibido na parte superior do cabeçalho. |
| Subtítulo |Texto a ser exibido abaixo do Título na parte superior do cabeçalho. |
| **Gráfico de Linhas** | |
| Consultar |Consulta a executar para o gráfico de linhas.  A primeira propriedade deve ser um valor de texto e a segunda um valor numérico.  Isso é normalmente uma consulta que usa a palavra-chave **medida** para resumir resultados.  Se a consulta usar a palavra-chave **intervalo**, o eixo X do gráfico usará esse intervalo de tempo.  Se a consulta não incluir a palavra-chave **intervalo**, intervalos de uma hora serão usados para o eixo X. |
| **Gráfico de Linhas** |**> Balão** |
| Título do balão |Texto a ser exibido acima do valor do balão. |
| Nome da Série |Valor da propriedade para a série a ser usado para o valor do balão.  Se nenhuma série for fornecida, todos os registros da consulta serão usados. |
| Operação |A operação a executar na propriedade de valor a resumir como um único valor para o balão.<br><br>– Média: média do valor de todos os registros.<br>– Contagem: contagem de todos os registros retornados pela consulta.<br>– Última Amostra: o valor do último intervalo incluído no gráfico.<br>– Máx.: o valor máximo dos intervalos incluídos no gráfico.<br>– Mín.: o valor mínimo dos intervalos incluídos no gráfico.<br>– Soma: soma dos valores de todos os registros. |
| **Gráfico de Linhas** |**> Eixo Y** |
| Usar Escala Logarítmica |Selecione para usar uma escala logarítmica para o eixo Y. |
| Unidades |Especifique as unidades para os valores retornados pela consulta.  Essas informações são usadas para exibir rótulos no gráfico que indicam os tipos de valor e, opcionalmente, para converter os valores.  O Tipo de Unidade especifica a categoria da unidade e define os valores que estão disponíveis para o Tipo de Unidade Atual.  Se você selecionar um valor em Converter, os valores numéricos serão convertidos do tipo Unidade Atual para o tipo Converter em. |
| Rótulo Personalizado |Texto a exibir para o eixo Y ao lado do rótulo para o tipo de unidade.  Se nenhum nome for especificado, somente o tipo de unidade será exibido. |
| **Lista** | |
| Consultar |Consulta a executar para a lista.  A contagem do número de registros retornados pela consulta será exibida. |
| Ocultar gráfico |Selecione para desabilitar o gráfico à direita da coluna numérica. |
| Habilitar minigráficos |Selecione para exibir minigráficos em vez de barras horizontais.  Consulte [Configurações Comuns](#sparklines) para obter detalhes. |
| Cor |Cor das barras ou minigráficos. |
| Operação |Operação a ser executada para o minigráfico.  Consulte [Configurações Comuns](#sparklines) para obter detalhes. |
| Separador de Valor e Nome |Delimitador de caractere único se desejar analisar a próxima propriedade em vários valores.  Consulte [Configurações Comuns](#name-value-separator) para obter detalhes. |
| Consulta de navegação |Consulta a executar quando o usuário seleciona um item na lista.  Consulte [Configurações Comuns](#navigation-query) para obter detalhes. |
| **Lista** |**> Títulos de coluna** |
| Nome |Texto a ser exibido na parte superior da primeira coluna da lista. |
| Valor |Texto a ser exibido na parte superior da segunda coluna da lista. |
| **Lista** |**> Limites** |
| Habilitar limites |Selecione para habilitar limites.  Consulte [Configurações Comuns](#thresholds) para obter detalhes. |

## <a name="line-chart--list-part"></a>Parte de gráfico de linhas e lista
O cabeçalho exibe um gráfico de linhas com várias séries de uma consulta de log ao longo do tempo.  A lista exibe os dez primeiros resultados de uma consulta com um gráfico indicando o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo.

![Exibição de gráfico de linhas e lista](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Configuração | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do Grupo |Texto a exibir na parte superior do bloco. |
| Novo Grupo |Selecione para criar um novo grupo na exibição, iniciando na exibição atual. |
| ícone |Arquivo de imagem para exibir ao lado do resultado no cabeçalho. |
| Usar Ícone |Selecione para que o ícone seja exibido. |
| **Cabeçalho** | |
| Title |Texto a ser exibido na parte superior do cabeçalho. |
| Subtítulo |Texto a ser exibido abaixo do Título na parte superior do cabeçalho. |
| **Gráfico de Linhas** | |
| Consultar |Consulta a executar para o gráfico de linhas.  A primeira propriedade deve ser um valor de texto e a segunda um valor numérico.  Isso é normalmente uma consulta que usa a palavra-chave **medida** para resumir resultados.  Se a consulta usar a palavra-chave **intervalo**, o eixo X do gráfico usará esse intervalo de tempo.  Se a consulta não incluir a palavra-chave **intervalo**, intervalos de uma hora serão usados para o eixo X. |
| **Gráfico de Linhas** |**> Eixo Y** |
| Usar Escala Logarítmica |Selecione para usar uma escala logarítmica para o eixo Y. |
| Unidades |Especifique as unidades para os valores retornados pela consulta.  Essas informações são usadas para exibir rótulos no gráfico que indicam os tipos de valor e, opcionalmente, para converter os valores.  O Tipo de Unidade especifica a categoria da unidade e define os valores que estão disponíveis para o Tipo de Unidade Atual.  Se você selecionar um valor em Converter, os valores numéricos serão convertidos do tipo Unidade Atual para o tipo Converter em. |
| Rótulo Personalizado |Texto a exibir para o eixo Y ao lado do rótulo para o tipo de unidade.  Se nenhum nome for especificado, somente o tipo de unidade será exibido. |
| **Lista** | |
| Consultar |Consulta a executar para a lista.  A contagem do número de registros retornados pela consulta será exibida. |
| Ocultar gráfico |Selecione para desabilitar o gráfico à direita da coluna numérica. |
| Habilitar minigráficos |Selecione para exibir minigráficos em vez de barras horizontais.  Consulte [Configurações Comuns](#sparklines) para obter detalhes. |
| Cor |Cor das barras ou minigráficos. |
| Operação |Operação a ser executada para o minigráfico.  Consulte [Configurações Comuns](#sparklines) para obter detalhes. |
| Separador de Valor e Nome |Delimitador de caractere único se desejar analisar a próxima propriedade em vários valores.  Consulte [Configurações Comuns](#name-value-separator) para obter detalhes. |
| Consulta de navegação |Consulta a executar quando o usuário seleciona um item na lista.  Consulte [Configurações Comuns](#navigation-query) para obter detalhes. |
| **Lista** |**> Títulos de coluna** |
| Nome |Texto a ser exibido na parte superior da primeira coluna da lista. |
| Valor |Texto a ser exibido na parte superior da segunda coluna da lista. |
| **Lista** |**> Limites** |
| Habilitar limites |Selecione para habilitar limites.  Consulte [Configurações Comuns](#thresholds) para obter detalhes. |

## <a name="stack-of-line-charts-part"></a>Pilha de parte de gráficos de linha
Exibe três gráficos de linhas separados com várias séries de uma consulta de log ao longo do tempo.

![Pilha de gráficos de linha](media/log-analytics-view-designer/view-stack-line-charts.png)

| Configuração | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do Grupo |Texto a exibir na parte superior do bloco. |
| Novo Grupo |Selecione para criar um novo grupo na exibição, iniciando na exibição atual. |
| ícone |Arquivo de imagem para exibir ao lado do resultado no cabeçalho. |
| **Gráfico 1<br>Gráfico 2<br>Gráfico 3** |**> Cabeçalho** |
| Title |Texto a ser exibido na parte superior do gráfico. |
| Subtítulo |Texto a ser exibido abaixo do Título na parte superior do gráfico. |
| **Gráfico 1<br>Gráfico 2<br>Gráfico 3** |**Gráfico de Linhas** |
| Consultar |Consulta a executar para o gráfico de linhas.  A primeira propriedade deve ser um valor de texto e a segunda um valor numérico.  Isso é normalmente uma consulta que usa a palavra-chave **medida** para resumir resultados.  Se a consulta usar a palavra-chave **intervalo**, o eixo X do gráfico usará esse intervalo de tempo.  Se a consulta não incluir a palavra-chave **intervalo**, intervalos de uma hora serão usados para o eixo X. |
| **Gráfico** |**> Eixo Y** |
| Usar Escala Logarítmica |Selecione para usar uma escala logarítmica para o eixo Y. |
| Unidades |Especifique as unidades para os valores retornados pela consulta.  Essas informações são usadas para exibir rótulos no gráfico que indicam os tipos de valor e, opcionalmente, para converter os valores.  O Tipo de Unidade especifica a categoria da unidade e define os valores que estão disponíveis para o Tipo de Unidade Atual.  Se você selecionar um valor em Converter, os valores numéricos serão convertidos do tipo Unidade Atual para o tipo Converter em. |
| Rótulo Personalizado |Texto a exibir para o eixo Y ao lado do rótulo para o tipo de unidade.  Se nenhum nome for especificado, somente o tipo de unidade será exibido. |

## <a name="common-settings"></a>Configurações comuns
As seções a seguir descrevem as configurações comuns para várias partes de visualização.

### <a name="name-value-separator">Separador de valor e nome</a>
Delimitador de caractere único se você desejar analisar a propriedade de texto de uma consulta de lista em vários valores.  Se você especificar um delimitador, poderá fornecer nomes para cada campo separado pelo mesmo delimitador na caixa Nome.

Por exemplo, considere uma propriedade chamada *Localização* que incluía valores como *Redmond-Building 41* e *Bellevue-Building12*.  Você poderia especificar – para o Separador de Valor e nome e *Cidade–Edifício* para o Nome.  Isso analisaria cada valor em duas propriedades chamadas *Cidade* e *Edifício*.

### <a name="navigation-query">Consulta de navegação</a>
Consulta a executar quando o usuário seleciona um item na lista.  Use *{selected item}* para incluir a sintaxe para o item selecionado pelo usuário.

Por exemplo, se a consulta tiver uma coluna chamada *Computer* e a consulta de navegação for *{selected item}*, uma consulta como *Computer="MyComputer"* seria executado quando o usuário selecionasse um computador.  Se a consulta de navegação fosse do tipo *Type=Event {selected item}*, a consulta *Type=Event Computer="MyComputer"* seria executada.

### <a name="sparklines">Minigráficos</a>
Um minigráfico é um pequeno gráfico de linha que ilustra o valor de uma entrada na lista ao longo do tempo.  Para partes de visualização com uma lista, é possível selecionar se deseja exibir uma barra horizontal que indica o valor relativo de uma coluna numérica ou de um minigráfico indicando seu valor ao longo do tempo horizontal.

A tabela a seguir descreve as configurações de minigráficos.

| Configuração | Descrição |
|:--- |:--- |
| Habilitar minigráficos |Selecione para exibir minigráficos em vez de barras horizontais. |
| Operação |Se os minigráficos estiverem habilitados, essa será a operação a ser executada em cada propriedade na lista para calcular os valores do minigráfico.<br><br>– Última Amostra: o último valor da série durante o intervalo.<br>– Máx: o valor máximo da série durante o intervalo.<br>– Mín: o valor mínimo da série durante o intervalo.<br>– Soma: soma dos valores da série durante o intervalo.<br>– Resumo: usa o mesmo comando de medida da consulta no cabeçalho. |

### <a name="thresholds">Limites</a>
Os limites permitem que você exiba um ícone colorido ao lado de cada item em uma lista, oferecendo a você um indicador visual rápido de itens que excedem um valor específico ou se enquadram em um determinado intervalo.  Por exemplo, será possível exibir um ícone verde para itens com um valor aceitável; amarelo, se o valor estiver dentro de um intervalo que indica um aviso, e vermelho se ele exceder um valor de erro.

Quando você habilitar os limites para uma parte, será necessário especificar um ou mais limites.  Se o valor de um item for maior do que um valor limite e menor do que o próximo valor limite, essa cor será usada.  Se o item for maior do que o maior valor limite, essa cor será definida.   

Cada conjunto de limite tem um limite com um valor de **Padrão**.  Essa será a cor definida se nenhum outro valor for excedido.  É possível adicionar ou remover os limites clicando no botão **+** ou **x**.

A tabela a seguir descreve as configurações de limites.

| Configuração | Descrição |
|:--- |:--- |
| Habilitar limites |Selecione para exibir um ícone de cor à esquerda de cada valor que indica sua integridade relativa a limites especificados. |
| Nome |Nome para identificar o valor limite. |
| Limite |Valor para o limite.  A cor de integridade para cada item de lista é definida como a cor do maior valor limite excedido pelo valor do item.  Haverá um limite padrão que será a cor se nenhum valor de limite for excedido. |
| Cor |Cor do valor limite. |

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para dar suporte às consultas partes da visualização.
