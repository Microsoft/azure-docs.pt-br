---
title: Visualização de dados de Data Explorer do Azure
description: Saiba mais sobre as diferentes maneiras que você pode visualizar os dados do Data Explorer do Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: d1c73d8eb65ed5d67d5250b4a3bca3b80450001e
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536716"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Visualização de dados com o Data Explorer do Azure 

Gerenciador de dados do Azure é um serviço de exploração de dados rápida e altamente escalonável para dados de telemetria e de log que são usados para criar soluções de análises complexas para grandes quantidades de dados. Gerenciador de dados do Azure integra-se com várias ferramentas de visualização, portanto, você pode visualizar seus dados e compartilhar os resultados na sua organização. Esses dados podem ser transformados em informações acionáveis para causar impacto nos negócios.

Visualização de dados e emissão de relatórios é uma etapa crítica no processo de análise de dados. Gerenciador de dados do Azure dá suporte a vários serviços de BI, você pode usar aquele que melhor se adapta a seu cenário e orçamento.

## <a name="kusto-query-language-visualizations"></a>Visualizações de linguagem de consulta do Kusto

A linguagem de consulta Kusto [ `render operator` ](/azure/kusto/query/renderoperator) oferece várias visualizações, como tabelas, gráficos de pizza e gráficos de barras para descrever os resultados da consulta. Visualizações de consulta são úteis na detecção de anomalias e de previsão, aprendizado de máquina e muito mais.

## <a name="power-bi"></a>Power BI

Gerenciador de dados do Azure fornece a capacidade de se conectar ao [Power BI](https://powerbi.microsoft.com) usando vários métodos: 

  * [Conector interno de nativos do Power BI](/azure/data-explorer/power-bi-connector)

  * [Importar de consulta do Data Explorer do Azure no Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL query](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Gerenciador de dados do Azure fornece a capacidade de se conectar ao [o Microsoft Excel](https://products.office.com/excel) nativo interno usando o conector do Excel ou importar uma consulta de Data Explorer do Azure para o Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) fornece um plug-in do Gerenciador de dados do Azure que permite que você visualize os dados de Data Explorer do Azure. Você [configurar o Data Explorer do Azure como uma fonte de dados para o Grafana e depois visualize os dados](/azure/data-explorer/grafana). 

## <a name="odbc-connector"></a>Conector do ODBC

Gerenciador de dados do Azure fornece um [conector de conectividade de banco de dados aberto (ODBC)](connect-odbc.md) para qualquer aplicativo que oferece suporte ao ODBC possa se conectar ao Data Explorer do Azure.

## <a name="tableau"></a>Tableau

Gerenciador de dados do Azure fornece a capacidade de se conectar ao [Tableau](https://www.tableau.com) usando o [conector ODBC](/azure/data-explorer/connect-odbc) e, em seguida, [visualizar os dados no Tableau](tableau.md).

## <a name="qlik"></a>Qlik

Gerenciador de dados do Azure fornece a capacidade de se conectar ao [Qlik](https://www.qlik.com) usando o [conector ODBC](/azure/data-explorer/connect-odbc) e, em seguida, crie o Qlik Sense painéis e visualizar os dados. Usando o vídeo a seguir, você pode aprender a visualizar dados de Data Explorer do Azure com o Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Gerenciador de dados do Azure fornece a capacidade de se conectar ao [Sisense](https://www.sisense.com) usando o conector do JDBC. Você [configurar o Data Explorer do Azure como uma fonte de dados para Sisense e, em seguida, visualizar os dados](/azure/data-explorer/sisense).