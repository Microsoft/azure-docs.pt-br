---
title: Um guia de referência para os blocos do Designer de exibição no Azure Monitor | Microsoft Docs
description: Usando o Designer de exibição no Azure Monitor, você pode criar exibições personalizadas que são exibidas no portal do Azure e contenham uma variedade de visualizações de dados no espaço de trabalho do Log Analytics. Este artigo é um guia de referência das configurações para os blocos que estão disponíveis em modos de exibição personalizados.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 41787c8f-6c13-4520-b0d3-5d3d84fcf142
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: bwren
ms.openlocfilehash: 9c0283081bd7245b1b886ed82ba03130a7a3bf2c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341649"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Guia de referência para blocos do Designer de exibição no Azure Monitor
Usando o Designer de exibição no Azure Monitor, você pode criar uma variedade de modos de exibição personalizados no portal do Azure que pode ajudar você a visualizar os dados em seu espaço de trabalho do Log Analytics. Este artigo é um guia de referência das configurações para os blocos que estão disponíveis em modos de exibição personalizados.

Para obter mais informações sobre o Designer de Exibição, consulte:

* [Designer de exibição](view-designer.md): fornece uma visão geral do Designer de exibição e dos procedimentos para criar e editar exibições personalizadas.
* [Referência de parte da visualização](view-designer-parts.md): fornece um guia de referência das configurações das partes de visualização disponíveis nas exibições personalizadas.


Os blocos do Designer de Modos de Exibição disponíveis são descritos na tabela a seguir:  

| Bloco | DESCRIÇÃO |
|:--- |:--- |
| [Número](#number-tile) |A contagem de registros de uma consulta. |
| [Dois números](#two-numbers-tile) |As contagens de registros de duas consultas diferentes. |
| [Rosca](#donut-tile) | Um gráfico que se baseia em uma consulta com um valor de resumo no centro. |
| Gráfico de linhas e balão | Um gráfico de linhas com base em uma consulta e um balão com um valor de resumo. |
| [Gráfico de linhas](#line-chart-tile) |Um gráfico de linhas com base em uma consulta. |
| [Duas linhas do tempo](#two-timelines-tile) | Um gráfico de colunas com duas séries, cada uma com base em uma consulta separada. |

As próximas seções descrevem os tipos de bloco e suas propriedades em detalhes.

> [!NOTE]
> Blocos nos modos de exibição se baseiam [registrar consultas](../log-query/log-query-overview.md) no espaço de trabalho do Log Analytics. Eles não têm suporte no momento [consultas de recurso entre](../log-query/cross-workspace-query.md) para recuperar dados do Application Insights.

## <a name="number-tile"></a>Bloco Número
O bloco **Número** exibe a contagem de registros de uma consulta de log e um rótulo.

![Bloco Número](media/view-designer-tiles/tile-number.png)

| Configuração | DESCRIÇÃO |
|:--- |:--- |
| NOME |O texto que é exibido na parte superior do bloco. |
| DESCRIÇÃO |O texto que é exibido sob o nome do bloco. |
| **Bloco** | |
| Legenda |O texto que é exibido sob o valor. |
| Consultar |A consulta que é executada. A contagem de registros que são retornados pela consulta é exibida. |
| **Avançado** |**> Verificação do fluxo de dados** |
| Enabled |Selecione este link se a verificação do fluxo de dados deve ser habilitada para o bloco. Essa abordagem fornecerá uma mensagem alternativa se os dados não estiverem disponíveis. Você normalmente usa a abordagem para fornecer uma mensagem durante o período temporário quando a exibição é instalada e os dados tornam-se disponíveis. |
| Consultar |A consulta é executada para determinar se os dados estão disponíveis para o modo de exibição. Se a consulta não retornar nenhum resultado, uma mensagem será exibida em vez do valor da consulta principal. |
| Mensagem |A mensagem que é exibida se a consulta de verificação do fluxo de dados não retornar nenhum dado. Se você não fornecer nenhuma mensagem, uma mensagem de status *Executando Avaliação* será exibida. |


## <a name="two-numbers-tile"></a>Bloco Dois Números
Este bloco exibe a contagem de registros de duas consultas de log diferentes e um rótulo para cada uma.

![Bloco Dois Números](media/view-designer-tiles/tile-two-numbers.png)

| Configuração | DESCRIÇÃO |
|:--- |:--- |
| NOME |O texto que é exibido na parte superior do bloco. |
| DESCRIÇÃO |O texto que é exibido sob o nome do bloco. |
| **Primeiro Bloco** | |
| Legenda |O texto que é exibido sob o valor. |
| Consultar |A consulta que é executada. A contagem de registros que são retornados pela consulta é exibida. |
| **Segundo Bloco** | |
| Legenda |O texto que é exibido sob o valor. |
| Consultar |A consulta que é executada. A contagem de registros que são retornados pela consulta é exibida. |
| **Avançado** |**> Verificação do fluxo de dados** |
| Enabled |Selecione este link se a verificação do fluxo de dados deve ser habilitada para o bloco. Essa abordagem fornecerá uma mensagem alternativa se os dados não estiverem disponíveis. Você normalmente usa a abordagem para fornecer uma mensagem durante o período temporário quando a exibição é instalada e os dados tornam-se disponíveis. |
| Consultar |A consulta é executada para determinar se os dados estão disponíveis para o modo de exibição. Se a consulta não retornar nenhum resultado, uma mensagem será exibida em vez do valor da consulta principal. |
| Mensagem |A mensagem que é exibida se a consulta de verificação do fluxo de dados não retornar nenhum dado. Se você não fornecer nenhuma mensagem, uma mensagem de status *Executando Avaliação* será exibida. |


## <a name="donut-tile"></a>Bloco Rosca
O bloco **Rosca** exibe um único número que resume uma coluna de valor em uma consulta de log. A rosca exibe graficamente os resultados dos três registros principais.

![Bloco Rosca](media/view-designer-tiles/tile-donut.png)

| Configuração | DESCRIÇÃO |
|:--- |:--- |
| NOME |O texto que é exibido na parte superior do bloco. |
| DESCRIÇÃO |O texto que é exibido sob o nome do bloco. |
| **Rosca** | |
| Consultar |A consulta que é executada para a rosca. A primeira propriedade é um valor de texto e a segunda é um valor numérico. Essa consulta normalmente usa a palavra-chave *medida* para resumir resultados. |
| **Rosca** |**> Centro** |
| Text |O texto que é exibido sob o valor dentro da rosca. |
| Operação |A operação que é executada na propriedade de valor para resumi-la como um único valor.<ul><li>Soma: adicione os valores de todos os registros com o valor da propriedade.</li><li>Percentual: o percentual dos valores somados de registros com o valor da propriedade em comparação com os valores somados de todos os registros.</li></ul> |
| Valores de resultado usados na operação do centro |Opcionalmente, selecione o sinal de adição (+) para adicionar um ou mais valores. Os resultados da consulta são limitados aos registros com os valores de propriedade que você especifica. Se nenhum valor for adicionado, todos os registros serão incluídos na consulta. |
| **Rosca** |**> Opções adicionais** |
| Cores |A cor que é exibida para cada uma das três propriedades principais. Para especificar cores alternativas para valores de propriedade específicos, use o *Mapeamento de Cores Avançado*. |
| Mapeamento de Cores Avançado |Exibe uma cor que representa valores de propriedade específicos. Se o valor especificado estiver entre os três principais, a cor alternativa será exibida em vez da cor padrão. Se a propriedade não estiver entre as três principais, a cor não será exibida. |
| **Avançado** |**> Verificação do fluxo de dados** |
| Enabled |Selecione este link se a verificação do fluxo de dados deve ser habilitada para o bloco. Essa abordagem fornecerá uma mensagem alternativa se os dados não estiverem disponíveis. Você normalmente usa a abordagem para fornecer uma mensagem durante o período temporário quando a exibição é instalada e os dados tornam-se disponíveis. |
| Consultar |A consulta é executada para determinar se os dados estão disponíveis para o modo de exibição. Se a consulta não retornar nenhum resultado, uma mensagem será exibida em vez do valor da consulta principal. |
| Mensagem |A mensagem que é exibida se a consulta de verificação do fluxo de dados não retornar nenhum dado. Se você não fornecer nenhuma mensagem, uma mensagem de status *Executando Avaliação* será exibida. |


## <a name="line-chart-tile"></a>Bloco Gráfico de linhas
Este bloco é um gráfico de linhas que exibe várias séries de uma consulta de log ao longo do tempo. 

![Bloco gráfico de linhas e balão](media/view-designer-tiles/tile-line-chart.png)

| Configuração | DESCRIÇÃO |
|:--- |:--- |
| NOME |O texto que é exibido na parte superior do bloco. |
| DESCRIÇÃO |O texto que é exibido sob o nome do bloco. |
| **Gráfico de linhas** | |
| Consultar |A consulta que é executada para o gráfico de linhas. A primeira propriedade é um valor de texto e a segunda é um valor numérico. Essa consulta normalmente usa a palavra-chave *medida* para resumir resultados. Se a consulta usar a palavra-chave *intervalo*, o eixo x usará esse intervalo de tempo. Se a consulta não usar a palavra-chave *intervalo*, o eixo x usará intervalos de horas. |
| **Gráfico de linhas** |**> Eixo Y** |
| Usar Escala Logarítmica |Selecione este link para usar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores retornados pela consulta. Essas informações são usadas para exibir rótulos no gráfico que indicam os tipos de valor e, opcionalmente, para converter os valores. O **Tipo de Unidade** Especifica a categoria da unidade e define os valores que estão disponíveis para o **Tipo de Unidade Atual**. Se você selecionar um valor em **Converter**, os valores numéricos serão convertidos do tipo **Unidade Atual** para o tipo **Converter em**. |
| Rótulo personalizado |O texto que é exibido para o eixo y ao lado do rótulo para o tipo *Unidade*. Se nenhum rótulo for especificado, somente o tipo *Unidade* será exibido. |
| **Avançado** |**> Verificação do fluxo de dados** |
| Enabled |Selecione este link se a verificação do fluxo de dados deve ser habilitada para o bloco. Essa abordagem fornecerá uma mensagem alternativa se os dados não estiverem disponíveis. Você normalmente usa a abordagem para fornecer uma mensagem durante o período temporário quando a exibição é instalada e os dados tornam-se disponíveis. |
| Consultar |A consulta é executada para determinar se os dados estão disponíveis para o modo de exibição. Se a consulta não retornar nenhum resultado, uma mensagem será exibida em vez do valor da consulta principal. |
| Mensagem |A mensagem que é exibida se a consulta de verificação do fluxo de dados não retornar nenhum dado. Se você não fornecer nenhuma mensagem, uma mensagem de status *Executando Avaliação* será exibida. |


## <a name="line-chart-and-callout-tile"></a>Bloco gráfico de linhas e balão
Este bloco tem um gráfico de linhas que exibe várias séries de uma consulta de log ao longo do tempo, além de um balão com um valor resumido. 

![Bloco gráfico de linhas e balão](media/view-designer-tiles/tile-line-chart-callout.png)

| Configuração | DESCRIÇÃO |
|:--- |:--- |
| NOME |O texto que é exibido na parte superior do bloco. |
| DESCRIÇÃO |O texto que é exibido sob o nome do bloco. |
| **Gráfico de linhas** | |
| Consultar |A consulta que é executada para o gráfico de linhas. A primeira propriedade é um valor de texto e a segunda é um valor numérico. Essa consulta normalmente usa a palavra-chave *medida* para resumir resultados. Se a consulta usar a palavra-chave *intervalo*, o eixo x usará esse intervalo de tempo. Se a consulta não usar a palavra-chave *intervalo*, o eixo x usará intervalos de horas. |
| **Gráfico de linhas** |**> Balão** |
| Título do balão | O texto que é exibido acima do valor do balão. |
| Nome da série |O valor da propriedade da série a ser usado como o valor do balão. Se nenhuma série for fornecida, todos os registros da consulta serão usados. |
| Operação |A operação que é executada na propriedade de valor para resumi-la como um único valor para o balão.<ul><li>Média: a média do valor de todos os registros.</li><li>Contagem: a contagem de registros que são retornados pela consulta.</li><li>Última amostra: o valor do último intervalo que é incluído no gráfico.</li><li>Máx.: o valor máximo dos intervalos que são incluídos no gráfico.</li><li>Mín.: o valor mínimo dos intervalos que são incluídos no gráfico.</li><li>Soma: a soma dos valores de todos os registros.</li></ul> |
| **Gráfico de linhas** |**> Eixo Y** |
| Usar Escala Logarítmica |Selecione este link para usar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores a serem retornados pela consulta. Essas informações são usadas para exibir rótulos do gráfico que indicam os tipos de valor e, opcionalmente, para converter os valores. O tipo *Unidade* especifica a categoria da unidade e define os valores que estão disponíveis para os valores do tipo *Unidade Atual* disponíveis. Se você selecionar um valor em *Converter para*, os valores numéricos serão convertidos do tipo *Unidade Atual* para o tipo *Converter para*. |
| Rótulo personalizado |O texto que é exibido para o eixo y ao lado do rótulo para o tipo *Unidade*. Se nenhum rótulo for especificado, somente o tipo *Unidade* será exibido. |
| **Avançado** |**> Verificação do fluxo de dados** |
| Enabled |Selecione este link se a verificação do fluxo de dados deve ser habilitada para o bloco. Essa abordagem fornecerá uma mensagem alternativa se os dados não estiverem disponíveis. Você normalmente usa a abordagem para fornecer uma mensagem durante o período temporário quando a exibição é instalada e os dados tornam-se disponíveis. |
| Consultar |A consulta é executada para determinar se os dados estão disponíveis para o modo de exibição. Se a consulta não retornar nenhum resultado, uma mensagem será exibida em vez do valor da consulta principal. |
| Mensagem |A mensagem que é exibida se a consulta de verificação do fluxo de dados não retornar nenhum dado. Se você não fornecer nenhuma mensagem, uma mensagem de status *Executando Avaliação* será exibida. |


## <a name="two-timelines-tile"></a>Bloco Duas linhas do tempo
O bloco **Duas linhas do tempo** exibe os resultados das duas consultas de log ao longo do tempo como gráficos de colunas. Um balão é exibido para cada série. 

![Bloco Duas linhas do tempo](media/view-designer-tiles/tile-two-timelines.png)

| Configuração | DESCRIÇÃO |
|:--- |:--- |
| NOME |O texto que é exibido na parte superior do bloco. |
| DESCRIÇÃO |O texto que é exibido sob o nome do bloco. |
| Primeiro Gráfico | |
| Legenda |O texto que é exibido sob o balão para a primeira série. |
| Cor |A cor que é usada para as colunas na primeira série. |
| Consulta de gráfico |A consulta que é executada para a primeira série. A contagem dos registros em cada intervalo de tempo são representada pelas colunas do gráfico. |
| Operação |A operação que é executada na propriedade de valor para resumi-la como um único valor para o balão.<ul><li>Média: a média do valor de todos os registros.</li><li>Contagem: a contagem de registros que são retornados pela consulta.</li><li>Última amostra: o valor do último intervalo que é incluído no gráfico.</li><li>Máx.: o valor máximo dos intervalos que são incluídos no gráfico.</li></ul> |
| **Segundo gráfico** | |
| Legenda |O texto que é exibido sob o balão para a segunda série. |
| Cor |A cor que é usada para as colunas na segunda série. |
| Consulta de Gráfico |A consulta que é executada para a segunda série. A contagem dos registros em cada intervalo de tempo são representada pelas colunas do gráfico. |
| Operação |A operação que é executada na propriedade de valor para resumi-la como um único valor para o balão.<ul><li>Média: a média do valor de todos os registros.</li><li>Contagem: a contagem de registros que são retornados pela consulta.</li><li>Última amostra: o valor do último intervalo que é incluído no gráfico.</li><li>Máx.: o valor máximo dos intervalos que são incluídos no gráfico. |
| **Avançado** |**> Verificação do fluxo de dados** |
| Enabled |Selecione este link se a verificação do fluxo de dados deve ser habilitada para o bloco. Essa abordagem fornecerá uma mensagem alternativa se os dados não estiverem disponíveis. Você normalmente usa a abordagem para fornecer uma mensagem durante o período temporário quando a exibição é instalada e os dados tornam-se disponíveis. |
| Consultar |A consulta é executada para determinar se os dados estão disponíveis para o modo de exibição. Se a consulta não retornar nenhum resultado, uma mensagem será exibida em vez do valor da consulta principal. |
| Mensagem |A mensagem que é exibida se a consulta de verificação do fluxo de dados não retornar nenhum dado. Se você não fornecer nenhuma mensagem, uma mensagem de status *Executando Avaliação* será exibida. |


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [registrar consultas](../log-query/log-query-overview.md) para oferecer suporte as consultas em blocos.
* Adicione [componentes de visualização](view-designer-parts.md) ao modo de exibição personalizado.
