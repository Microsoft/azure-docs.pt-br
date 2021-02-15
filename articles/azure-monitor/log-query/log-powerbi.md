---
title: Integração do Log Analytics com o Power BI e o Excel
description: Como enviar resultados de Log Analytics para Power BI
ms.subservice: logs
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: a4e2faf87ac3dddf91e6945343a46b02df72db0a
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507426"
---
# <a name="log-analytics-integration-with-power-bi"></a>Integração do Log Analytics com o Power BI

Este artigo se concentra em maneiras de alimentar dados de Log Analytics no Microsoft Power BI para criar painéis e relatórios mais atraentes visualmente. 

## <a name="background"></a>Tela de fundo 

Os logs de Azure Monitor é uma plataforma que fornece uma solução de ponta a ponta para ingestão de logs. [Azure Monitor log Analytics](../platform/data-platform.md#) é a interface para consultar esses logs. Para obter mais informações sobre toda a plataforma de dados de Azure Monitor, incluindo Log Analytics, consulte [Azure monitor Data Platform](../platform/data-platform.md). 

O Microsoft Power BI é a plataforma de visualização de dados da Microsoft. Para obter mais informações sobre como começar, consulte a [Home Page do Power bi](https://powerbi.microsoft.com/). 


Em geral, você pode usar recursos gratuitos do Power BI para integrar e criar relatórios e painéis visualmente atraentes.

Recursos mais avançados podem exigir a compra de uma conta Power BI Pro ou Premium. Esses recursos incluem: 
 - compartilhando seu trabalho 
 - atualizações agendadas
 - Aplicativos do Power BI 
 - fluxo de os e atualização incremental 

Para obter mais informações, consulte [saiba mais sobre preços e recursos de Power bi](https://powerbi.microsoft.com/pricing/) 

## <a name="integrating-queries"></a>Integrando consultas  

Power BI usa a [linguagem de consulta M](/powerquery-m/power-query-m-language-specification/) como sua linguagem de consulta principal. 

Log Analytics consultas podem ser exportadas para M e usadas no Power BI diretamente. Depois de executar uma consulta bem-sucedida, selecione **exportar para Power bi (consulta M)** no botão **Exportar** na barra de ação superior da interface do usuário do log Analytics.

:::image type="content" source="media/log-powerbi-excel/export-query2.png" alt-text="Menu suspenso da consulta de Log Analytics mostrando a opção de exportação" border="true":::

Log Analytics cria um arquivo. txt contendo o código M que pode ser usado diretamente no Power BI.

## <a name="connecting-your-logs-to-a-dataset"></a>Conectando seus logs a um conjunto de registros 

Um conjunto de dados Power BI é uma fonte de dado pronta para geração de relatórios e visualização. Para conectar uma consulta Log Analytics a um conjunto de um, copie o código M exportado de Log Analytics em uma consulta em branco no Power BI. 

Para obter mais informações, consulte [noções básicas sobre conjuntos](/power-bi/service-datasets-understand/)de dados Power bi. 

## <a name="collect-data-with-power-bi-dataflows"></a>Coletar dados com Power BI Dataflows 

Os fluxos de dados Power BI também permitem que você colete e armazene. Para obter mais informações, consulte fluxos de dados do [Power bi](/power-bi/service-dataflows-overview).

Um fluxo de dados é um tipo de "ETL de nuvem" projetado para ajudá-lo a coletar e preparar seus dados. Um DataSet é o "modelo" projetado para ajudá-lo a conectar diferentes entidades e modelá-las para suas necessidades.

## <a name="incremental-refresh"></a>Atualização incremental 

Ambos os conjuntos de Power BI e os Power BI de fluxo de os contêm uma opção de atualização incremental. Power BI fluxos de Power BI de fluxo de os e os conjuntos de Power bi Premium oferecem suporte a esse recurso, mas você precisa usá-lo.  


A atualização incremental executa consultas pequenas e atualiza quantidades menores de dados por execução em vez de ingerir todos os dados de novo e novamente quando você executa a consulta. Você tem a opção de salvar grandes quantidades de dados, mas adicionar um novo incremento de dados toda vez que a consulta for executada. Esse comportamento é ideal para a execução de relatórios mais longos.

Power BI atualização incremental depende da existência de um *DateTime* arquivado no conjunto de resultados. Antes de configurar a atualização incremental, verifique se o conjunto de resultados da consulta Log Analytics inclui pelo menos um *DateTime* arquivado. 

Para saber mais e como configurar a atualização incremental, confira [Power bi conjuntos de valores e a atualização incremental](/power-bi/service-premium-incremental-refresh) e Power bi de fluxo de os e a [atualização incremental](/power-bi/service-dataflows-incremental-refresh).

## <a name="reports-and-dashboards"></a>Relatórios e dashboards

Depois que os dados são enviados para Power BI, você pode continuar a usar Power BI para criar relatórios e painéis.

Para obter mais informações, consulte [este guia sobre como criar seu primeiro modelo de Power bi e relatório](/learn/modules/build-your-first-power-bi-report/).  

## <a name="excel-integration"></a>integração do Excel

Você pode usar a mesma integração M usada em Power BI para integrar com uma planilha do Excel. Para obter mais informações, consulte este [guia sobre como integrar com o Excel](https://support.microsoft.com/office/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a) e, em seguida, colar a consulta M exportada do log Analytics.

Informações adicionais podem ser encontradas em [integrar log Analytics e Excel](log-excel.md)

## <a name="next-steps"></a>Próximas etapas

Introdução às [consultas de log Analytics](log-query-overview.md).
