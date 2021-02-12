---
title: Integre o Log Analytics e o Excel
description: Obtenha uma consulta Log Analytics no Excel e atualize os resultados dentro do Excel.
ms.subservice: logs
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: 6fa181a35c46ed16e4e8c1884e66c54984c418ca
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2020
ms.locfileid: "97703442"
---
# <a name="integrate-log-analytics-and-excel"></a>Integre o Log Analytics e o Excel

Você pode integrar Azure Monitor Log Analytics e o Microsoft Excel usando a consulta M e a API Log Analytics. Essa integração permite que você envie até 500.000 registros para o Excel, contanto que o volume total dos resultados não exceda 61MiB.

> [!NOTE]
> Como o Excel é um aplicativo cliente local, as limitações de hardware local e software afetam o desempenho e a capacidade de processar grandes conjuntos de dados.

## <a name="create-your-m-query-in-log-analytics"></a>Criar sua consulta M no Log Analytics 

1. **Crie e execute sua consulta** no log Analytics como faria normalmente. Não se preocupe se você atingir a limitação de registros 10.000 na interface do usuário.  Recomendamos que você use as datas relativas, como a função ' atrás ' ou o seletor de tempo da interface do usuário, para que o Excel atualize o conjunto correto de dados.
  
2. **Exportar consulta** -quando estiver satisfeito com a consulta e seus resultados, exporte a consulta para M usando log Analytics **exportar para a opção de menu Power bi (consulta M)** no menu *Exportar* :

:::image type="content" source="media/log-excel/export-query.png" alt-text="Log Analytics consulta com os dados e a opção de exportação" border="true":::



A escolha dessa opção faz o download de um arquivo. txt contendo o código M que você pode usar no Excel.

A consulta mostrada acima exporta o código M a seguir. Aqui está um exemplo do código M exportado para a consulta em nosso exemplo:

```m
/*
The exported Power Query Formula Language (M Language ) can be used with Power Query in Excel
and Power BI Desktop.
For Power BI Desktop follow the instructions below: 
1) Download Power BI Desktop from https://powerbi.microsoft.com/desktop/
2) In Power BI Desktop select: 'Get Data' -> 'Blank Query'->'Advanced Query Editor'
3) Paste the M Language script into the Advanced Query Editor and select 'Done'
*/

let AnalyticsQuery =
let Source = Json.Document(Web.Contents("https://api.loganalytics.io/v1/workspaces/ddcfc599-cae0-48ee-9026-fffffffffffff/query", 
[Query=[#"query"="

Heartbeat 
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1h)    
| render timechart
",#"x-ms-app"="OmsAnalyticsPBI",#"timespan"="P1D",#"prefer"="ai.response-thinning=true"],Timeout=#duration(0,0,4,0)])),
TypeMap = #table(
{ "AnalyticsTypes", "Type" }, 
{ 
{ "string",   Text.Type },
{ "int",      Int32.Type },
{ "long",     Int64.Type },
{ "real",     Double.Type },
{ "timespan", Duration.Type },
{ "datetime", DateTimeZone.Type },
{ "bool",     Logical.Type },
{ "guid",     Text.Type },
{ "dynamic",  Text.Type }
}),
DataTable = Source[tables]{0},
Columns = Table.FromRecords(DataTable[columns]),
ColumnsWithType = Table.Join(Columns, {"type"}, TypeMap , {"AnalyticsTypes"}),
Rows = Table.FromRows(DataTable[rows], Columns[name]), 
Table = Table.TransformColumnTypes(Rows, Table.ToList(ColumnsWithType, (c) => { c{0}, c{3}}))
in
Table
in AnalyticsQuery
```

## <a name="connect-query-to-excel"></a>Conectar consulta ao Excel 

Para importar a consulta. 

1. Abra o Microsoft Excel. 
1. Na faixa de faixas, vá para o menu **dados** . Selecione **obter dados**. De **outras fontes**, selecione **consulta em branco**:
 
   :::image type="content" source="media/log-excel/excel-import-blank-query.png" alt-text="Opção importar de em branco no Excel" border="true":::

1. Na janela Power Query, selecione **Editor avançado**:

   :::image type="content" source="media/log-excel/advanced-editor.png" alt-text="Editor de consultas avançadas do Excel" border="true":::

 
1. Substitua o texto no editor avançado pela consulta exportada do Log Analytics:

   :::image type="content" source="media/log-excel/advanced-editor-2.png" alt-text="Criando uma consulta em branco" border="true":::
 
1. Selecione **concluído** e, em seguida, **carregar e fechar**. O Excel executa a consulta usando a API do log Analytics e o conjunto de resultados é mostrado.
 

   :::image type="content" source="media/log-excel/excel-query-result.png" alt-text="Resultados da consulta no Excel" border="true":::

> [!Note]
> Se o número de registros for menor que o esperado, o volume dos resultados poderá exceder o limite de 61MiB. Tente usar `project` `project-away` o ou em sua consulta para limitar as colunas para aquela que você precisa.

##  <a name="refreshing--data"></a>Atualizando dados

Você pode atualizar seus dados diretamente do Excel. No grupo de menus **dados** na faixa de opções do Excel, selecione o botão **Atualizar** .
 
## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as integrações do Excel com fontes de dados externas, consulte [importar dados de fontes de dados externas (Power Query)](https://support.office.com/article/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a)