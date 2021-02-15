---
title: Um guia de referência para as partes do designer de exibição no Azure Monitor | Microsoft Docs
description: Usando o designer de exibição no Azure Monitor, você pode criar exibições personalizadas que são exibidas na portal do Azure e conter uma variedade de visualizações nos dados no espaço de trabalho Log Analytics. Este artigo é um guia de referência das configurações para as partes de visualização que estão disponíveis em modos de exibição personalizados.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2018
ms.openlocfilehash: 7b670cafa4d643e37fae068a4c0033fc97a96ccd
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92166583"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-azure-monitor"></a>Guia de referência para exibir partes de visualização do designer no Azure Monitor
Usando o designer de exibição no Azure Monitor, você pode criar uma variedade de exibições personalizadas na portal do Azure que podem ajudá-lo a Visualizar dados em seu espaço de trabalho do Log Analytics. Este artigo é um guia de referência das configurações para as partes de visualização que estão disponíveis em modos de exibição personalizados.

Para obter mais informações sobre o Designer de Exibição, consulte:

* [Designer de Modos de Exibição](view-designer.md): fornece uma visão geral do Designer de Modos de Exibição e dos procedimentos para criar e editar exibições personalizadas.
* [Referência de bloco](view-designer-tiles.md): fornece uma referência das configurações de cada um dos blocos disponíveis nas suas exibições personalizadas.


Os tipos de blocos do Designer de Modos de Exibição disponíveis são descritos na tabela a seguir:

| Tipo de exibição | Descrição |
|:--- |:--- |
| [Lista de consultas](#list-of-queries-part) |Exibe uma lista de consultas de log. Você pode selecionar cada consulta para exibir seus resultados. |
| [Número e lista](#number-and-list-part) |O cabeçalho exibe um único número que mostra uma contagem de registros de uma consulta de log. A lista exibe os dez primeiros resultados de uma consulta com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo. |
| [Dois números e lista](#two-numbers-and-list-part) |O cabeçalho exibe dois números que mostram contagens de registros de consultas de log separadas. A lista exibe os dez primeiros resultados de uma consulta com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo. |
| [Lista e rosca](#donut-and-list-part) |O cabeçalho exibe um único número que resume uma coluna de valor em uma consulta de log. A rosca exibe graficamente os resultados dos três registros principais. |
| [Duas linhas do tempo e lista](#two-timelines-and-list-part) |O cabeçalho exibe os resultados de duas consultas de log ao longo do tempo como gráficos de coluna com um balão mostrando um único número que resume de uma coluna de valor em uma consulta de log. A lista exibe os dez primeiros resultados de uma consulta com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo. |
| [Informações](#information-part) |O cabeçalho exibe texto estático e um link opcional. A lista exibe um ou mais itens com texto e título estático. |
| [Gráfico de linhas, balão e lista](#line-chart-callout-and-list-part) |O cabeçalho exibe um gráfico de linhas com várias séries de uma consulta de log ao longo do tempo, além de um balão com um valor resumido. A lista exibe os dez primeiros resultados de uma consulta com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo. |
| [Gráfico de linhas e balão](#line-chart-and-list-part) |O cabeçalho exibe um gráfico de linhas com várias séries de uma consulta de log ao longo do tempo. A lista exibe os dez primeiros resultados de uma consulta com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo. |
| [Pilha de parte de gráficos de linha](#stack-of-line-charts-part) |Exibe três gráficos de linhas separados com várias séries de uma consulta de log ao longo do tempo. |

As próximas seções descrevem os tipos de bloco e suas propriedades em detalhes.

> [!NOTE]
> As partes em exibições são baseadas em [consultas de log](../log-query/log-query-overview.md) em seu espaço de trabalho log Analytics. Atualmente, eles não dão suporte a [consultas entre recursos](../log-query/cross-workspace-query.md) para recuperar dados de Application insights.

## <a name="list-of-queries-part"></a>Lista da parte de consultas
A parte da lista de consultas exibe uma lista de consultas de log. Você pode selecionar cada consulta para exibir seus resultados. O modo de exibição inclui uma única consulta por padrão e você pode selecionar **+ Consulta** para incluir consultas adicionais.

![Captura de tela da parte de visualização da lista de consultas no designer de exibição de Azure Monitor.](media/view-designer-parts/view-list-queries.png)

| Configuração | Descrição |
|:--- |:--- |
| **Geral** | |
| Título |O texto que é exibido na parte superior da exibição. |
| Novo Grupo |Selecione este link para criar um novo grupo na exibição, iniciando na exibição atual. |
| Filtros pré-selecionados |Uma lista delimitada por vírgulas de propriedades para incluir no painel de filtro esquerdo quando você seleciona uma consulta. |
| Modo de renderização |A exibição inicial que aparece quando a consulta é selecionada. Você pode selecionar qualquer exibição disponível depois de abrir a consulta. |
| **Consultas** | |
| Consulta de pesquisa |A consulta a ser executada. |
| Nome amigável | O nome descritivo que é exibido. |

## <a name="number-and-list-part"></a>Número e parte da lista
O cabeçalho exibe um único número que mostra uma contagem de registros de uma consulta de log. A lista exibe os dez primeiros resultados de uma consulta com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo.

![Captura de tela das partes de visualização número e lista no designer de exibição de Azure Monitor.](media/view-designer-parts/view-number-list.png)

| Configuração | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do Grupo |O texto que é exibido na parte superior da exibição. |
| Novo Grupo |Selecione este link para criar um novo grupo na exibição, iniciando na exibição atual. |
| ícone |O arquivo de imagem que é exibido ao lado do resultado no cabeçalho. |
| Usar Ícone |Selecione este link para exibir o ícone. |
| **Título** | |
| Legenda |O texto que é exibido na parte superior do cabeçalho. |
| Consulta |A consulta a ser executada para o cabeçalho. A contagem de registros que são retornados pela consulta é exibida. |
| Navegação clickthrough | Ação executada quando você clica no cabeçalho.  Para saber mais, veja [Configurações comuns](#click-through-navigation). |
| **Lista** | |
| Consulta |A consulta a ser executada para a lista. As duas primeiras propriedades para os dez primeiros registros nos resultados são exibidas. A primeira propriedade é um valor de texto e a segunda é um valor numérico. As barras são criadas automaticamente com base no valor relativo da coluna numérica.<br><br>Use o comando `Sort` na consulta para classificar os registros na lista. Para executar a consulta e retornar todos os registros você pode selecionar **Ver tudo**. |
| Ocultar grafo |Selecione este link para desabilitar o grafo à direita da coluna numérica. |
| Habilitar minigráficos |Selecione este link para exibir um minigráfico em vez de uma barra horizontal. Para saber mais, veja [Configurações comuns](#sparklines). |
| Color |A cor das barras ou minigráficos. |
| Separador de valor e nome |O delimitador de caractere único a ser usado para analisar a próxima propriedade em vários valores. Para saber mais, veja [Configurações comuns](#sparklines). |
| Navegação clickthrough | Ação executada quando você clica em um item na lista.  Para saber mais, veja [Configurações comuns](#click-through-navigation). |
| **Lista** |**> Títulos de coluna** |
| Nome |O texto que é exibido na parte superior da primeira coluna. |
| Valor |O texto que é exibido na parte superior da segunda coluna. |
| **Lista** |**> Limites** |
| Habilitar limites |Selecione este link para habilitar limites. Para saber mais, veja [Configurações comuns](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Dois números e parte da lista
O cabeçalho tem dois números que exibem uma contagem de registros de consultas de log separadas. A lista exibe os dez primeiros resultados de uma consulta com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo.

![Exibição de dois números e lista](media/view-designer-parts/view-two-numbers-list.png)

| Configuração | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do Grupo |O texto que é exibido na parte superior da exibição. |
| Novo Grupo |Selecione este link para criar um novo grupo na exibição, iniciando na exibição atual. |
| ícone |O arquivo de imagem que é exibido ao lado do resultado no cabeçalho. |
| Usar Ícone |Selecione este link para exibir o ícone. |
| **Navegação de título** | |
| Navegação clickthrough | Ação executada quando você clica no cabeçalho.  Para saber mais, veja [Configurações comuns](#click-through-navigation). |
| **Título** | |
| Legenda |O texto que é exibido na parte superior do cabeçalho. |
| Consulta |A consulta a ser executada para o cabeçalho. A contagem de registros que são retornados pela consulta é exibida. |
| **Lista** | |
| Consulta |A consulta a ser executada para a lista. As duas primeiras propriedades para os dez primeiros registros nos resultados são exibidas. A primeira propriedade é um valor de texto e a segunda é um valor numérico. As barras são criadas automaticamente com base no valor relativo da coluna numérica.<br><br>Use o comando `Sort` na consulta para classificar os registros na lista. Para executar a consulta e retornar todos os registros você pode selecionar **Ver tudo**. |
| Ocultar grafo |Selecione este link para desabilitar o grafo à direita da coluna numérica. |
| Habilitar minigráficos |Selecione este link para exibir um minigráfico em vez de uma barra horizontal. Para saber mais, veja [Configurações comuns](#sparklines). |
| Color |A cor das barras ou minigráficos. |
| Operação |A operação a ser executada para o minigráfico. Para saber mais, veja [Configurações comuns](#sparklines). |
| Separador de valor e nome |O delimitador de caractere único a ser usado para analisar a próxima propriedade em vários valores. Para saber mais, veja [Configurações comuns](#sparklines). |
| Navegação clickthrough | Ação executada quando você clica em um item na lista.  Para saber mais, veja [Configurações comuns](#click-through-navigation). |
| **Lista** |**> Títulos de coluna** |
| Nome |O texto que é exibido na parte superior da primeira coluna. |
| Valor |O texto que é exibido na parte superior da segunda coluna. |
| **Lista** |**> Limites** |
| Habilitar limites |Selecione este link para habilitar limites. Para saber mais, veja [Configurações comuns](#thresholds). |

## <a name="donut-and-list-part"></a>Parte da lista e rosca
O cabeçalho exibe um único número que resume uma coluna de valor em uma consulta de log. A rosca exibe graficamente os resultados dos três registros principais.

![Exibição da lista e rosca](media/view-designer-parts/view-donut-list.png)

| Configuração | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do Grupo |O texto que é exibido na parte superior do bloco. |
| Novo Grupo |Selecione este link para criar um novo grupo na exibição, iniciando na exibição atual. |
| ícone |O arquivo de imagem que é exibido ao lado do resultado no cabeçalho. |
| Usar Ícone |Selecione este link para exibir o ícone. |
| **Cabeçalho** | |
| Título |O texto que é exibido na parte superior do cabeçalho. |
| Subtítulo |O texto que é exibido sob o título na parte superior do cabeçalho. |
| **Donut** | |
| Consulta |A consulta a ser executada para a rosca. A primeira propriedade é um valor de texto e a segunda é um valor numérico. |
| Navegação clickthrough | Ação executada quando você clica no cabeçalho.  Para saber mais, veja [Configurações comuns](#click-through-navigation). |
| **Donut** |**> Centro** |
| Texto |O texto que é exibido sob o valor dentro da rosca. |
| Operação |A operação a executar na propriedade de valor a resumir como um único valor.<ul><li>Soma: adiciona os valores de todos os registros.</li><li>Porcentagem: a taxa dos registros retornados pelos valores em **Valores de resultado usados na operação do centro** para o total de registros na consulta.</li></ul> |
| Valores de resultado usados na operação do centro |Opcionalmente, selecione o sinal de adição (+) para adicionar um ou mais valores. Os resultados da consulta são limitados aos registros com os valores de propriedade que você especifica. Se nenhum valor for adicionado, todos os registros serão incluídos na consulta. |
| **Opções adicionais** |**> Cores** |
| Cor 1<br>Cor 2<br>Cor 3 |Selecione a cor de cada um dos valores que são exibidos na rosca. |
| **Opções adicionais** |**> Mapeamento de cores avançado** |
| Valor do campo |Digite o nome de um campo para exibi-lo como uma cor diferente se ele estiver incluído na rosca. |
| Color |Selecione a cor do campo exclusivo. |
| **Lista** | |
| Consulta |A consulta a ser executada para a lista. A contagem de registros que são retornados pela consulta é exibida. |
| Ocultar grafo |Selecione este link para desabilitar o grafo à direita da coluna numérica. |
| Habilitar minigráficos |Selecione este link para exibir um minigráfico em vez de uma barra horizontal. Para saber mais, veja [Configurações comuns](#sparklines). |
| Color |A cor das barras ou minigráficos. |
| Operação |A operação a ser executada para o minigráfico. Para saber mais, veja [Configurações comuns](#sparklines). |
| Separador de valor e nome |O delimitador de caractere único a ser usado para analisar a próxima propriedade em vários valores. Para saber mais, veja [Configurações comuns](#sparklines). |
| Navegação clickthrough | Ação executada quando você clica em um item na lista.  Para saber mais, veja [Configurações comuns](#click-through-navigation). |
| **Lista** |**> Títulos de coluna** |
| Nome |O texto que é exibido na parte superior da primeira coluna. |
| Valor |O texto que é exibido na parte superior da segunda coluna. |
| **Lista** |**> Limites** |
| Habilitar limites |Selecione este link para habilitar limites. Para saber mais, veja [Configurações comuns](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Duas linhas do tempo e parte da lista
O cabeçalho exibe os resultados de duas consultas de log ao longo do tempo como gráficos de coluna com um balão mostrando um único número que resume de uma coluna de valor em uma consulta de log. A lista exibe os dez primeiros resultados de uma consulta com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo.

![Duas linhas do tempo e exibição da lista](media/view-designer-parts/view-two-timelines-list.png)

| Configuração | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do Grupo |O texto que é exibido na parte superior do bloco. |
| Novo Grupo |Selecione este link para criar um novo grupo na exibição, iniciando na exibição atual. |
| ícone |O arquivo de imagem que é exibido ao lado do resultado no cabeçalho. |
| Usar Ícone |Selecione este link para exibir o ícone. |
| **Navegação de título** | |
| Navegação clickthrough | Ação executada quando você clica no cabeçalho.  Para saber mais, veja [Configurações comuns](#click-through-navigation). |
| **Primeiro gráfico<br>Segundo gráfico** | |
| Legenda |O texto que é exibido sob o balão para a primeira série. |
| Color |A cor a ser usada para as colunas na série. |
| Consulta |A consulta a executar para a primeira série. A contagem de registros em cada intervalo de tempo são representada pelas colunas do gráfico. |
| Operação |A operação a executar na propriedade de valor a resumir como um único valor para o balão.<ul><li>Soma: a soma dos valores de todos os registros.</li><li>Média: a média do valor de todos os registros.</li><li>Última amostra: o valor do último intervalo que é incluído no gráfico.</li><li>Primeira amostra: o valor do primeiro intervalo que é incluído no gráfico.</li><li>Contagem: a contagem de registros que são retornados pela consulta.</li></ul> |
| **Lista** | |
| Consulta |A consulta a ser executada para a lista. A contagem de registros que são retornados pela consulta é exibida. |
| Ocultar grafo |Selecione este link para desabilitar o grafo à direita da coluna numérica. |
| Habilitar minigráficos |Selecione este link para exibir um minigráfico em vez de uma barra horizontal. Para saber mais, veja [Configurações comuns](#sparklines). |
| Color |A cor das barras ou minigráficos. |
| Operação |A operação a ser executada para o minigráfico. Para saber mais, veja [Configurações comuns](#sparklines). |
| Navegação clickthrough | Ação executada quando você clica em um item na lista.  Para saber mais, veja [Configurações comuns](#click-through-navigation). |
| **Lista** |**> Títulos de coluna** |
| Nome |O texto que é exibido na parte superior da primeira coluna. |
| Valor |O texto que é exibido na parte superior da segunda coluna. |
| **Lista** |**> Limites** |
| Habilitar limites |Selecione este link para habilitar limites. Para saber mais, veja [Configurações comuns](#thresholds). |

## <a name="information-part"></a>Parte de informações
O cabeçalho exibe texto estático e um link opcional. A lista exibe um ou mais itens com texto e título estático.

![Exibição de informações](media/view-designer-parts/view-information.png)

| Configuração | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do Grupo |O texto que é exibido na parte superior do bloco. |
| Novo Grupo |Selecione este link para criar um novo grupo na exibição, iniciando na exibição atual. |
| Color |A cor da tela de fundo do cabeçalho. |
| **Cabeçalho** | |
| Imagem |O arquivo de imagem que é exibido no cabeçalho. |
| Rotular |O texto que é exibido no cabeçalho. |
| **Cabeçalho** |**> Link** |
| Rotular |O texto do link. |
| Url |A URL para o link. |
| **Itens de informações** | |
| Título |O texto que é exibido para o título de cada item. |
| Conteúdo |O texto que é exibido para cada item. |

## <a name="line-chart-callout-and-list-part"></a>Gráfico de linhas, balão e parte da lista
O cabeçalho exibe um gráfico de linhas com várias séries de uma consulta de log ao longo do tempo, além de um balão com um valor resumido. A lista exibe os dez primeiros resultados de uma consulta com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo.

![Gráfico de linhas, balão e exibição da lista](media/view-designer-parts/view-line-chart-callout-list.png)

| Configuração | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do Grupo |O texto que é exibido na parte superior do bloco. |
| Novo Grupo |Selecione este link para criar um novo grupo na exibição, iniciando na exibição atual. |
| ícone |O arquivo de imagem que é exibido ao lado do resultado no cabeçalho. |
| Usar Ícone |Selecione este link para exibir o ícone. |
| **Cabeçalho** | |
| Título |O texto que é exibido na parte superior do cabeçalho. |
| Subtítulo |O texto que é exibido sob o título na parte superior do cabeçalho. |
| **Gráfico de linhas** | |
| Consulta |A consulta a executar para o gráfico de linhas. A primeira propriedade é um valor de texto e a segunda é um valor numérico. Essa consulta normalmente usa a palavra-chave *medida* para resumir resultados. Se a consulta usar a palavra-chave *intervalo*, o eixo X do gráfico usará esse intervalo de tempo. Se a consulta não inlcui a palavra-chave *intervalo*, o eixo x usará intervalos de horas. |
| Navegação clickthrough | Ação executada quando você clica no cabeçalho.  Para saber mais, veja [Configurações comuns](#click-through-navigation). |
| **Gráfico de linhas** |**> Balão** |
| Título do balão |O texto que é exibido acima do valor do balão. |
| Nome da Série |Valor da propriedade para a série a ser usado para o valor do balão. Se nenhuma série for fornecida, todos os registros da consulta serão usados. |
| Operação |A operação a executar na propriedade de valor a resumir como um único valor para o balão.<ul><li>Média: a média do valor de todos os registros.</li><li>Contagem: a contagem de registros que são retornados pela consulta.</li><li>Última amostra: o valor do último intervalo que é incluído no gráfico.</li><li>Máx.: o valor máximo dos intervalos que são incluídos no gráfico.</li><li>Min.: o valor mínimo dos intervalos que são incluídos no gráfico.</li><li>Soma: a soma dos valores de todos os registros.</li></ul> |
| **Gráfico de linhas** |**> Eixo Y** |
| Usar Escala Logarítmica |Selecione este link para usar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores a serem retornados pela consulta. Essas informações são usadas para exibir rótulos do gráfico que indicam os tipos de valor e, opcionalmente, para converter os valores. O tipo *Unidade* especifica a categoria da unidade e define os valores que estão disponíveis para os valores do tipo *Unidade Atual* disponíveis. Se você selecionar um valor em *Converter para*, os valores numéricos serão convertidos do tipo *Unidade Atual* para o tipo *Converter para*. |
| Rótulo personalizado |O texto que é exibido para o eixo y ao lado do rótulo para o tipo *Unidade*. Se nenhum rótulo for especificado, somente o tipo *Unidade* será exibido. |
| **Lista** | |
| Consulta |A consulta a ser executada para a lista. A contagem de registros que são retornados pela consulta é exibida. |
| Ocultar grafo |Selecione este link para desabilitar o grafo à direita da coluna numérica. |
| Habilitar minigráficos |Selecione este link para exibir um minigráfico em vez de uma barra horizontal. Para saber mais, veja [Configurações comuns](#sparklines). |
| Color |A cor das barras ou minigráficos. |
| Operação |A operação a ser executada para o minigráfico. Para saber mais, veja [Configurações comuns](#sparklines). |
| Separador de valor e nome |O delimitador de caractere único a ser usado para analisar a próxima propriedade em vários valores. Para saber mais, veja [Configurações comuns](#sparklines). |
| Navegação clickthrough | Ação executada quando você clica em um item na lista.  Para saber mais, veja [Configurações comuns](#click-through-navigation). |
| **Lista** |**> Títulos de coluna** |
| Nome |O texto que é exibido na parte superior da primeira coluna. |
| Valor |O texto que é exibido na parte superior da segunda coluna. |
| **Lista** |**> Limites** |
| Habilitar limites |Selecione este link para habilitar limites. Para saber mais, veja [Configurações comuns](#thresholds). |

## <a name="line-chart-and-list-part"></a>Gráfico de linhas e parte da lista
O cabeçalho exibe um gráfico de linhas com várias séries de uma consulta de log ao longo do tempo. A lista exibe os dez primeiros resultados de uma consulta com um grafo que indica o valor relativo de uma coluna numérica ou sua alteração ao longo do tempo.

![Gráfico de linhas e exibição da lista](media/view-designer-parts/view-line-chart-callout-list.png)

| Configuração | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do Grupo |O texto que é exibido na parte superior do bloco. |
| Novo Grupo |Selecione este link para criar um novo grupo na exibição, iniciando na exibição atual. |
| ícone |O arquivo de imagem que é exibido ao lado do resultado no cabeçalho. |
| Usar Ícone |Selecione este link para exibir o ícone. |
| **Cabeçalho** | |
| Título |O texto que é exibido na parte superior do cabeçalho. |
| Subtítulo |O texto que é exibido sob o título na parte superior do cabeçalho. |
| **Gráfico de linhas** | |
| Consulta |A consulta a executar para o gráfico de linhas. A primeira propriedade é um valor de texto e a segunda é um valor numérico. Essa consulta normalmente usa a palavra-chave *medida* para resumir resultados. Se a consulta usar a palavra-chave *intervalo*, o eixo X do gráfico usará esse intervalo de tempo. Se a consulta não inlcui a palavra-chave *intervalo*, o eixo x usará intervalos de horas. |
| Navegação clickthrough | Ação executada quando você clica no cabeçalho.  Para saber mais, veja [Configurações comuns](#click-through-navigation). |
| **Gráfico de linhas** |**> Eixo Y** |
| Usar Escala Logarítmica |Selecione este link para usar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores a serem retornados pela consulta. Essas informações são usadas para exibir rótulos do gráfico que indicam os tipos de valor e, opcionalmente, para converter os valores. O tipo *Unidade* especifica a categoria da unidade e define os valores que estão disponíveis para os valores do tipo *Unidade Atual* disponíveis. Se você selecionar um valor em *Converter para*, os valores numéricos serão convertidos do tipo *Unidade Atual* para o tipo *Converter para*. |
| Rótulo personalizado |O texto que é exibido para o eixo y ao lado do rótulo para o tipo *Unidade*. Se nenhum rótulo for especificado, somente o tipo *Unidade* será exibido. |
| **Lista** | |
| Consulta |A consulta a ser executada para a lista. A contagem de registros que são retornados pela consulta é exibida. |
| Ocultar grafo |Selecione este link para desabilitar o grafo à direita da coluna numérica. |
| Habilitar minigráficos |Selecione este link para exibir um minigráfico em vez de uma barra horizontal. Para saber mais, veja [Configurações comuns](#sparklines). |
| Color |A cor das barras ou minigráficos. |
| Operação |A operação a ser executada para o minigráfico. Para saber mais, veja [Configurações comuns](#sparklines). |
| Separador de valor e nome |O delimitador de caractere único a ser usado para analisar a próxima propriedade em vários valores. Para saber mais, veja [Configurações comuns](#sparklines). |
| Navegação clickthrough | Ação executada quando você clica em um item na lista.  Para saber mais, veja [Configurações comuns](#click-through-navigation). |
| **Lista** |**> Títulos de coluna** |
| Nome |O texto que é exibido na parte superior da primeira coluna. |
| Valor |O texto que é exibido na parte superior da segunda coluna. |
| **Lista** |**> Limites** |
| Habilitar limites |Selecione este link para habilitar limites. Para saber mais, veja [Configurações comuns](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Pilha de parte de gráficos de linha
A pilha de gráficos de linha exibe três gráficos de linhas separados com várias séries de uma consulta de log ao longo do tempo, conforme mostrado aqui:

![Pilha de gráficos de linha](media/view-designer-parts/view-stack-line-charts.png)

| Configuração | Descrição |
|:--- |:--- |
| **Geral** | |
| Título do Grupo |O texto que é exibido na parte superior do bloco. |
| Novo Grupo |Selecione este link para criar um novo grupo na exibição, iniciando na exibição atual. |
| ícone |O arquivo de imagem que é exibido ao lado do resultado no cabeçalho. |
| **Gráfico 1<br>Gráfico 2<br>Gráfico 3** |**> Cabeçalho** |
| Título |O texto que é exibido na parte superior do gráfico. |
| Subtítulo |O texto que é exibido sob o título na parte superior do gráfico. |
| **Gráfico 1<br>Gráfico 2<br>Gráfico 3** |**Gráfico de linhas** |
| Consulta |A consulta a executar para o gráfico de linhas. A primeira propriedade é um valor de texto e a segunda é um valor numérico. Essa consulta normalmente usa a palavra-chave *medida* para resumir resultados. Se a consulta usar a palavra-chave *intervalo*, o eixo X do gráfico usará esse intervalo de tempo. Se a consulta não inlcui a palavra-chave *intervalo*, o eixo x usará intervalos de horas. |
| Navegação clickthrough | Ação executada quando você clica no cabeçalho.  Para saber mais, veja [Configurações comuns](#click-through-navigation). |
| **Gráfico** |**> Eixo Y** |
| Usar Escala Logarítmica |Selecione este link para usar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores a serem retornados pela consulta. Essas informações são usadas para exibir rótulos do gráfico que indicam os tipos de valor e, opcionalmente, para converter os valores. O tipo *Unidade* especifica a categoria da unidade e define os valores que estão disponíveis para os valores do tipo *Unidade Atual* disponíveis. Se você selecionar um valor em *Converter para*, os valores numéricos serão convertidos do tipo *Unidade Atual* para o tipo *Converter para*. |
| Rótulo personalizado |O texto que é exibido para o eixo y ao lado do rótulo para o tipo *Unidade*. Se nenhum rótulo for especificado, somente o tipo *Unidade* será exibido. |

## <a name="common-settings"></a>Configurações padrão
As seções a seguir descrevem as configurações que são comuns para várias partes de visualização.

### <a name="name-and-value-separator"></a><a name="name-value-separator"></a>Separador de valor e nome
O separador de valor e nome é o delimitador de caractere único a ser usado para analisar a propriedade de texto de uma consulta de lista em vários valores. Se você especificar um delimitador, poderá fornecer nomes para cada campo, separados pelo mesmo delimitador na caixa **nome** .

Por exemplo, considere uma propriedade chamada *Localização* que incluía valores como *Redmond-Building 41* e *Bellevue-Building12*. Você poderia especificar um traço (-) para o separador de valor e nome e *City-Building* para o nome. Essa abordagem analisa cada valor em duas propriedades chamadas *Cidade* e *Edifício*.

### <a name="click-through-navigation"></a><a name="click-through-navigation"></a>Navegação clickthrough
A navegação clickthrough define qual ação será tomada quando você clicar em um cabeçalho ou item de lista em uma exibição.  Isso abrirá uma consulta no [log Analytics](../log-query/log-query-overview.md) ou iniciará outra exibição.

A tabela a seguir descreve as configurações de navegação clickthrough.

| Configuração           | Descrição |
|:--|:--|
| Pesquisa de Logs (Automática) | Consulta de log a ser executada quando você seleciona um item de cabeçalho.  Essa é a mesma consulta de log na qual o item se baseia.
| Pesquisa de log        | Consulta de log a ser executada quando você seleciona um item em uma lista.  Digite a consulta na caixa **Consulta de navegação**.   Use *{selected item}* para incluir a sintaxe para o item selecionado pelo usuário.  Por exemplo, se a consulta tiver uma coluna denominada *Computer* e a consulta de navegação for *{selected item}*, uma consulta como *Computer="MyComputer"* é executada quando você seleciona um computador. Se a consulta de navegação for do tipo *Type=Event {selected item}*, a consulta *Type=Event Computer="MyComputer"* é executada. |
| Exibir              | Modo de exibição para abrir quando você seleciona um item de cabeçalho ou um item em uma lista.  Selecione o nome de uma exibição no seu workspace na caixa **Nome de exibição**. |



### <a name="sparklines"></a><a name="sparklines"></a>Minigráficos
Um minigráfico é um pequeno gráfico de linha que ilustra o valor de uma entrada na lista ao longo do tempo. Para partes de visualização com uma lista, é possível selecionar se deseja exibir uma barra horizontal que indica o valor relativo de uma coluna numérica ou de um minigráfico que indica seu valor ao longo do tempo horizontal.

A tabela a seguir descreve as configurações de minigráficos:

| Configuração | Descrição |
|:--- |:--- |
| Habilitar minigráficos |Selecione este link para exibir um minigráfico em vez de uma barra horizontal. |
| Operação |Se os minigráficos estiverem habilitados, essa será a operação a ser executada em cada propriedade na lista para calcular os valores do minigráfico.<ul><li>Última Amostra: O último valor da série durante o intervalo.</li><li>Máx: O valor máximo da série durante o intervalo.</li><li>Min: O valor mínimo da série durante o intervalo.</li><li>Soma: A soma dos valores da série durante o intervalo.</li><li>Resumo: Usa o mesmo comando `measure` da consulta no cabeçalho.</li></ul> |

### <a name="thresholds"></a><a name="thresholds"></a>Limites
Usando limites, você pode exibir um ícone de cor ao lado de cada item em uma lista. Os limites oferecem um indicador visual rápido de itens que excedem um valor específico ou ficam dentro de um intervalo específico. Por exemplo, você pode exibir um ícone verde para itens com um valor aceitável; amarelo, se o valor estiver dentro de um intervalo que indica um aviso, e vermelho se ele exceder um valor de erro.

Quando você habilitar os limites para uma parte, será necessário especificar um ou mais limites. Se o valor de um item for maior do que um valor limite e menor do que o próximo valor limite, a cor para esse valor será usada. Se o item for maior do que o maior valor limite, outra cor será usada. 

Cada conjunto de limite tem um limite com um valor de **Padrão**. Essa é a cor que é definida se nenhum outro valor for excedido. É possível adicionar ou remover os limites selecionando o botão **Adicionar** (+) ou ** Excluir** (x).

A tabela a seguir descreve as configurações de limites:

| Configuração | Descrição |
|:--- |:--- |
| Habilitar limites |Clique neste link para exibir um ícone de cor à esquerda de cada valor. O ícone indica a integridade do valor em relação a limites especificados. |
| Nome |O nome do valor limite. |
| Limite |O valor para o limite. A cor de integridade para cada item de lista é definida como a cor do maior valor limite que é excedido pelo valor do item. Se nenhum valor limite for excedido, será usada uma cor padrão. |
| Color |A cor que indica o valor limite. |

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [consultas de log](../log-query/log-query-overview.md) para dar suporte às consultas em partes de visualização.
