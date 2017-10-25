---
title: "Referência de bloco do Criador de Modos de Exibição no OMS Log Analytics | Microsoft Docs"
description: "O Designer de modos de exibição do Log Analytics permite que você crie exibições personalizadas no console do OMS que contêm diferentes visualizações de dados no repositório do OMS. Este artigo fornece uma referência de configurações para cada um dos blocos disponíveis para uso em exibições personalizadas."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: 41787c8f-6c13-4520-b0d3-5d3d84fcf142
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: bwren
ms.openlocfilehash: 2bce5c63b4c6edd3753f1b234cc4f493dcf53dea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-view-designer-tile-reference"></a>Referência sobre blocos do Criador de Modos de Exibição do Log Analytics
O Designer de Modos de Exibição do Log Analytics permite que você crie modos de exibição personalizados no console do OMS que contêm diferentes visualizações de dados no repositório do OMS. Este artigo fornece uma referência de configurações para cada um dos blocos disponíveis para uso em exibições personalizadas.

Outros artigos disponíveis para o Designer de Modos de Exibição são:

* [Designer de Modos de Exibição](log-analytics-view-designer.md) – visão geral do Designer de Modos de Exibição e procedimentos para criar e editar modos de exibição personalizados.
* [Referência de parte de visualização](log-analytics-view-designer-parts.md) – Referência das configurações para cada um dos blocos disponíveis para uso nas exibições personalizadas.

>[!NOTE]
> Se o seu espaço de trabalho tiver sido atualizado para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md), consultas em todas as exibições deverão ser gravadas na [nova linguagem de consulta](https://go.microsoft.com/fwlink/?linkid=856078).  Todas as exibições que foram criadas antes de atualizar o espaço de trabalho serão automaticamente convertidas.

A tabela a seguir lista os diferentes tipos de blocos disponíveis no Designer de Modos de Exibição.  As seções a seguir descrevem cada tipo de bloco em detalhes e suas propriedades.

| Bloco | Descrição |
|:--- |:--- |
| [Número](#number-tile) |Número único mostrando a contagem de registros de uma consulta. |
| [Dois números](#two-numbers-tile) |Dois números únicos mostrando contagens de registros de duas consultas diferentes. |
| [Rosca](#donut-tile) |Gráfico de rosca com base em uma consulta com um valor de resumo no centro. |
| [Gráfico de linhas e balão](#line-chart-amp-callout-tile) |Gráfico de linhas com base em uma consulta e um balão com um valor de resumo. |
| [Gráfico de linhas](#line-chart-tile) |Gráfico de linhas com base em uma consulta. |
| [Duas linhas do tempo](#two-timelines-tile) |Gráfico de colunas com duas séries, cada uma com base em uma consulta separada. |

## <a name="number-tile"></a>Bloco Número
O bloco **Número** exibe um único número mostrando a contagem de registros de uma consulta de log e um rótulo.

![Bloco Número](media/log-analytics-view-designer/tile-number.png)

| Configuração | Descrição |
|:--- |:--- |
| Nome |Texto a exibir na parte superior do bloco. |
| Descrição |Texto exibido abaixo do nome do bloco. |
| **Bloco** | |
| Legenda |Texto a exibir abaixo do valor. |
| Consultar |Consulta a executar.  A contagem do número de registros retornados pela consulta será exibida. |
| **Avançado** |**> Verificação do fluxo de dados** |
| Habilitado |Selecione se a verificação do fluxo de dados deve ser habilitada para o bloco.  Isso fornecerá uma mensagem alternativa se não houver dados disponíveis para o bloco.  Isso normalmente é usado para fornecer uma mensagem durante o período temporário quando a exibição é instalada e os dados tornam-se disponíveis. |
| Consultar |Consulta a ser executada para verificar se os dados estão disponíveis para o modo de exibição.  Se a consulta não retornar nenhum resultado, uma mensagem será exibida em vez do valor da consulta principal. |
| Mensagem |Mensagem a ser exibida se a consulta de verificação do fluxo de dados não retornar nenhum dado.  Se você não fornecer nenhuma mensagem, a mensagem *Executando Avaliação* será exibida. |


## <a name="two-numbers-tile"></a>Bloco Dois Números
Bloco **Dois Números** exibe dois números mostrando a contagem de registros de um rótulo e duas consultas de log diferentes para cada um.

![Bloco Dois Números](media/log-analytics-view-designer/tile-two-numbers.png)

| Configuração | Descrição |
|:--- |:--- |
| Nome |Texto a exibir na parte superior do bloco. |
| Descrição |Texto exibido abaixo do nome do bloco. |
| **Primeiro Bloco** | |
| Legenda |Texto a exibir abaixo do valor. |
| Consultar |Consulta a executar.  A contagem do número de registros retornados pela consulta será exibida. |
| **Segundo Bloco** | |
| Legenda |Texto a exibir abaixo do valor. |
| Consultar |Consulta a executar.  A contagem do número de registros retornados pela consulta será exibida. |
| **Avançado** |**> Verificação do fluxo de dados** |
| Habilitado |Selecione se a verificação do fluxo de dados deve ser habilitada para o bloco.  Isso fornecerá uma mensagem alternativa se não houver dados disponíveis para o bloco.  Isso normalmente é usado para fornecer uma mensagem durante o período temporário quando a exibição é instalada e os dados tornam-se disponíveis. |
| Consultar |Consulta a ser executada para verificar se os dados estão disponíveis para o modo de exibição.  Se a consulta não retornar nenhum resultado, uma mensagem será exibida em vez do valor da consulta principal. |
| Mensagem |Mensagem a ser exibida se a consulta de verificação do fluxo de dados não retornar nenhum dado.  Se você não fornecer nenhuma mensagem, a mensagem *Executando Avaliação* será exibida. |


## <a name="donut-tile"></a>Bloco Rosca
O bloco **Rosca** exibe um único número resumido de uma coluna de valor em uma consulta de log.  A rosca exibe graficamente os resultados dos três registros principais.

![Bloco Rosca](media/log-analytics-view-designer/tile-donut.png)

| Configuração | Descrição |
|:--- |:--- |
| Nome |Texto a exibir na parte superior do bloco. |
| Descrição |Texto exibido abaixo do nome do bloco. |
| **Rosca** | |
| Consultar |Consulta a executar para a rosca.  A primeira propriedade deve ser um valor de texto e a segunda um valor numérico.  Isso é normalmente uma consulta que usa a palavra-chave **medida** para resumir resultados. |
| **Rosca** |**> Centro** |
| Texto |Texto a exibir abaixo do valor dentro da rosca. |
| Operação |A operação a executar na propriedade de valor a resumir como um único valor.<br><br>– Soma: adicione os valores de todos os registros com o valor da propriedade.<br>– Percentual: o percentual dos valores somados de registros com o valor da propriedade em comparação com os valores somados de todos os registros. |
| Valores de resultado usados na operação do centro |Opcionalmente, clique no sinal de adição para adicionar um ou mais valores.  Os resultados da consulta serão limitados aos registros com os valores de propriedade que você especificar.  Se nenhum valor for adicionado, todos os registros serão incluídos na consulta. |
| **Rosca** |**> Opções adicionais** |
| Cores |A cor a ser exibida para cada uma das três propriedades principais.  Se você quiser especificar cores alternativas para valores de propriedade específicos, use o Mapeamento de Cores Avançado. |
| Mapeamento de Cores Avançado |Exibe uma cor para valores de propriedade específicos.  Se o valor especificado estiver entre os três principais, a cor alternativa será exibida em vez da cor padrão.  Se a propriedade não estiver entre as três principais, a cor não será exibida. |
| **Avançado** |**> Verificação do fluxo de dados** |
| Habilitado |Selecione se a verificação do fluxo de dados deve ser habilitada para o bloco.  Isso fornecerá uma mensagem alternativa se não houver dados disponíveis para o bloco.  Isso normalmente é usado para fornecer uma mensagem durante o período temporário quando a exibição é instalada e os dados tornam-se disponíveis. |
| Consultar |Consulta a ser executada para verificar se os dados estão disponíveis para o modo de exibição.  Se a consulta não retornar nenhum resultado, uma mensagem será exibida em vez do valor da consulta principal. |
| Mensagem |Mensagem a ser exibida se a consulta de verificação do fluxo de dados não retornar nenhum dado.  Se você não fornecer nenhuma mensagem, a mensagem *Executando Avaliação* será exibida. |


## <a name="line-chart-tile"></a>Bloco Gráfico de linhas
O bloco **Gráfico de linhas** exibe um gráfico de linhas com várias séries de uma consulta de log ao longo do tempo.  

![Bloco Gráfico de Linhas e Balão](media/log-analytics-view-designer/tile-line-chart.png)

| Configuração | Descrição |
|:--- |:--- |
| Nome |Texto a exibir na parte superior do bloco. |
| Descrição |Texto exibido abaixo do nome do bloco. |
| **Gráfico de Linhas** | |
| Consultar |Consulta a executar para o gráfico de linhas.  A primeira propriedade deve ser um valor de texto e a segunda um valor numérico.  Isso é normalmente uma consulta que usa a palavra-chave **medida** para resumir resultados.  Se a consulta usar a palavra-chave **intervalo**, o eixo X do gráfico usará esse intervalo de tempo.  Se a consulta não incluir a palavra-chave **intervalo**, intervalos de uma hora serão usados para o eixo X. |
| **Gráfico de Linhas** |**> Eixo Y** |
| Usar Escala Logarítmica |Selecione para usar uma escala logarítmica para o eixo Y. |
| Unidades |Especifique as unidades para os valores retornados pela consulta.  Essas informações são usadas para exibir rótulos no gráfico que indicam os tipos de valor e, opcionalmente, para converter os valores.  O **Tipo de Unidade** Especifica a categoria da unidade e define os valores que estão disponíveis para o **Tipo de Unidade Atual**.  Se você selecionar um valor em **Converter**, os valores numéricos serão convertidos do tipo **Unidade Atual** para o tipo **Converter em**. |
| Rótulo Personalizado |Texto a exibir para o eixo Y ao lado do rótulo para o tipo de unidade.  Se nenhum nome for especificado, somente o tipo de unidade será exibido. |
| **Avançado** |**> Verificação do fluxo de dados** |
| Habilitado |Selecione se a verificação do fluxo de dados deve ser habilitada para o bloco.  Isso fornecerá uma mensagem alternativa se não houver dados disponíveis para o bloco.  Isso normalmente é usado para fornecer uma mensagem durante o período temporário quando a exibição é instalada e os dados tornam-se disponíveis. |
| Consultar |Consulta a ser executada para verificar se os dados estão disponíveis para o modo de exibição.  Se a consulta não retornar nenhum resultado, uma mensagem será exibida em vez do valor da consulta principal. |
| Mensagem |Mensagem a ser exibida se a consulta de verificação do fluxo de dados não retornar nenhum dado.  Se você não fornecer nenhuma mensagem, a mensagem *Executando Avaliação* será exibida. |


## <a name="line-chart--callout-tile"></a>Bloco Gráfico de linhas e balão
O bloco **Gráfico de linhas e balão** exibe um gráfico de linhas com várias séries de uma consulta de log ao longo do tempo, além de um balão com um valor resumido.  

![Bloco Gráfico de Linhas e Balão](media/log-analytics-view-designer/tile-line-chart-callout.png)

| Configuração | Descrição |
|:--- |:--- |
| Nome |Texto a exibir na parte superior do bloco. |
| Descrição |Texto exibido abaixo do nome do bloco. |
| **Gráfico de Linhas** | |
| Consultar |Consulta a executar para o gráfico de linhas.  A primeira propriedade deve ser um valor de texto e a segunda um valor numérico.  Isso é normalmente uma consulta que usa a palavra-chave **medida** para resumir resultados.  Se a consulta usar a palavra-chave **intervalo**, o eixo X do gráfico usará esse intervalo de tempo.  Se a consulta não incluir a palavra-chave **intervalo**, intervalos de uma hora serão usados para o eixo X. |
| **Gráfico de Linhas** |**> Balão** |
| Balão |Título    Texto a ser exibido acima do valor do balão. |
| Nome da Série |Valor da propriedade para a série a ser usado para o valor do balão.  Se nenhuma série for fornecida, todos os registros da consulta serão usados. |
| Operação |A operação a executar na propriedade de valor a resumir como um único valor para o balão.<br>– Média: média do valor de todos os registros.<br><br>– Contagem: contagem de todos os registros retornados pela consulta.<br>– Última Amostra: o valor do último intervalo incluído no gráfico.<br>– Máx.: o valor máximo dos intervalos incluídos no gráfico.<br>– Mín.: o valor mínimo dos intervalos incluídos no gráfico.<br>– Soma: soma dos valores de todos os registros. |
| **Gráfico de Linhas** |**> Eixo Y** |
| Usar Escala Logarítmica |Selecione para usar uma escala logarítmica para o eixo Y. |
| Unidades |Especifique as unidades para os valores retornados pela consulta.  Essas informações são usadas para exibir rótulos no gráfico que indicam os tipos de valor e, opcionalmente, para converter os valores.  O **Tipo de Unidade** Especifica a categoria da unidade e define os valores que estão disponíveis para o **Tipo de Unidade Atual**.  Se você selecionar um valor em **Converter**, os valores numéricos serão convertidos do tipo **Unidade Atual** para o tipo **Converter em**. |
| Rótulo Personalizado |Texto a exibir para o eixo Y ao lado do rótulo para o tipo de unidade.  Se nenhum nome for especificado, somente o tipo de unidade será exibido. |
| **Avançado** |**> Verificação do fluxo de dados** |
| Habilitado |Selecione se a verificação do fluxo de dados deve ser habilitada para o bloco.  Isso fornecerá uma mensagem alternativa se não houver dados disponíveis para o bloco.  Isso normalmente é usado para fornecer uma mensagem durante o período temporário quando a exibição é instalada e os dados tornam-se disponíveis. |
| Consultar |Consulta a ser executada para verificar se os dados estão disponíveis para o modo de exibição.  Se a consulta não retornar nenhum resultado, uma mensagem será exibida em vez do valor da consulta principal. |
| Mensagem |Mensagem a ser exibida se a consulta de verificação do fluxo de dados não retornar nenhum dado.  Se você não fornecer nenhuma mensagem, a mensagem *Executando Avaliação* será exibida. |


## <a name="two-timelines-tile"></a>Bloco Duas linhas do tempo
O bloco **Duas linhas do tempo** exibe os resultados das duas consultas de log ao longo do tempo como gráficos de colunas.  Um balão é exibido para cada série.  

![Bloco Duas linhas do tempo](media/log-analytics-view-designer/tile-two-timelines.png)

| Configuração | Descrição |
|:--- |:--- |
| Nome |Texto a exibir na parte superior do bloco. |
| Descrição |Texto exibido abaixo do nome do bloco. |
| Primeiro Gráfico | |
| Legenda |Texto exibido sob o balão para a primeira série. |
| Cor |Cor a usar para as colunas na primeira série. |
| Consulta de Gráfico |Consulta a executar para a primeira série.  A contagem do número de registros em cada intervalo de tempo será representada pelas colunas do gráfico. |
| Operação |A operação a executar na propriedade de valor a resumir como um único valor para o balão.<br><br>– Média: média do valor de todos os registros.<br>– Contagem: contagem de todos os registros retornados pela consulta.<br>– Última Amostra: o valor do último intervalo incluído no gráfico.<br>– Máx.: o valor máximo dos intervalos incluídos no gráfico. |
| **Segundo Gráfico** | |
| Legenda |Texto exibido sob o balão para a segunda série. |
| Cor |Cor a usar para as colunas na segunda série. |
| Consulta de Gráfico |Consulta a executar para a segunda série.  A contagem do número de registros em cada intervalo de tempo será representada pelas colunas do gráfico. |
| Operação |A operação a executar na propriedade de valor a resumir como um único valor para o balão.<br><br>– Média: média do valor de todos os registros.<br>– Contagem: contagem de todos os registros retornados pela consulta.<br>– Última Amostra: o valor do último intervalo incluído no gráfico.<br>– Máx.: o valor máximo dos intervalos incluídos no gráfico. |
| **Avançado** |**> Verificação do fluxo de dados** |
| Habilitado |Selecione se a verificação do fluxo de dados deve ser habilitada para o bloco.  Isso fornecerá uma mensagem alternativa se não houver dados disponíveis para o bloco.  Isso normalmente é usado para fornecer uma mensagem durante o período temporário quando a exibição é instalada e os dados tornam-se disponíveis. |
| Consultar |Consulta a ser executada para verificar se os dados estão disponíveis para o modo de exibição.  Se a consulta não retornar nenhum resultado, uma mensagem será exibida em vez do valor da consulta principal. |
| Mensagem |Mensagem a ser exibida se a consulta de verificação do fluxo de dados não retornar nenhum dado.  Se você não fornecer nenhuma mensagem, a mensagem *Executando Avaliação* será exibida. |


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para dar suporte às consultas em blocos.
* Adicione [Componentes de Visualização](log-analytics-view-designer-parts.md) ao modo de exibição personalizado.
