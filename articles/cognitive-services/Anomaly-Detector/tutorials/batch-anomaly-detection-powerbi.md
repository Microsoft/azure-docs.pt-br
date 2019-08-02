---
title: Visualizar anomalias usando a detecção em lotes e o Power BI
titleSuffix: Azure Cognitive Services
description: Use a API do Detector de Anomalias e o Power BI para visualizar anomalias em seus dados de série temporal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 04/30/2019
ms.author: aahi
ms.openlocfilehash: 74b51d04f2706d890475c500e1e730cff75397c5
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721493"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Tutorial: Visualizar anomalias usando a detecção em lotes e o Power BI

Use este tutorial para encontrar anomalias em um conjunto de dados de série temporal como um lote. Usando o Power BI Desktop, você usará um arquivo do Excel, preparará os dados para a API do Detector de Anomalias e visualizará as anomalias estatísticas nele.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Usar o Power BI Desktop para importar e transformar um conjunto de dados de série temporal
> * Integrar o Power BI Desktop à API do Detector de Anomalias para a detecção de anomalias em lotes
> * Visualize as anomalias encontradas em seus dados, incluindo os valores esperados e vistos e os limites da detecção de anomalias.

## <a name="prerequisites"></a>Pré-requisitos

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/), disponível gratuitamente.
* Um arquivo do Excel (.xlsx) contendo pontos de dados de série temporal. Os dados de exemplo deste início rápido podem ser encontrados no [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Carregar e formatar os dados de série temporal

Para começar, abra o Power BI Desktop e carregue os dados de série temporal que você baixou nos pré-requisitos. Este arquivo do Excel contém uma série de pares de valor e carimbo de data/hora UTC (Tempo Universal Coordenado).  

> [!NOTE]
> O Power BI pode usar dados de uma ampla variedade de fontes, como arquivos .csv, bancos de dados SQL, Armazenamento de Blobs do Azure, entre outros.  

Na janela principal do Power BI Desktop, clique na faixa de opções **Página Inicial**. No grupo **Dados externos** da faixa de opções, abra o menu suspenso **Obter Dados** e clique em **Excel**.

![Uma imagem do botão "Obter Dados" no Power BI](../media/tutorials/power-bi-get-data-button.png)

Depois que a caixa de diálogo for exibida, navegue até a pasta em que você baixou o arquivo .xlsx de exemplo e selecione-o. Depois que a caixa de diálogo **Navegador** for exibida, clique em **Sheet1** e, em seguida, em **Editar**.

![Uma imagem da tela "Navegador" da fonte de dados no Power BI](../media/tutorials/navigator-dialog-box.png)

O Power BI converterá os carimbos de data/hora na primeira coluna em um tipo de dados `Date/Time`. Esses carimbos de data/hora precisam ser convertidos em texto para serem enviados à API do Detector de Anomalias. Se o editor do Power Query não for aberto automaticamente, clique em **Editar Consultas** na guia Página Inicial. 

Clique na faixa de opções **Transformar** no Editor do Power Query. No grupo **Qualquer Coluna**, abra o menu suspenso **Tipo de Dados:** e selecione **Texto**.

![Uma imagem da tela "Navegador" da fonte de dados no Power BI](../media/tutorials/data-type-drop-down.png)

Quando você receber um aviso sobre a alteração do tipo de coluna, clique em **Substituir Atual**. Em seguida, clique em **Fechar e Aplicar** ou **Aplicar** na faixa de opções **Página Inicial**. 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Criar uma função para enviar os dados e formatar a resposta

Para formatar e enviar o arquivo de dados para a API do Detector de Anomalias, invoque uma consulta na tabela criada acima. No Editor do Power Query, na faixa de opções **Página Inicial**, abra o menu suspenso **Nova Fonte** e clique em **Consulta em Branco**.

Verifique se a nova consulta está selecionada e, em seguida, clique em **Editor Avançado**. 

![Uma imagem do botão "Editor Avançado" no Power BI](../media/tutorials/advanced-editor-screen.png)

No Editor Avançado, use o snippet do Power Query M a seguir para extrair as colunas da tabela e enviá-la à API. Depois disso, a consulta criará uma tabela com base na resposta JSON e a retornará. Substitua a variável `apiKey` pela chave de API válida do Detector de Anomalias e `endpoint` pelo ponto de extremidade. Depois de inserir a consulta no Editor Avançado, clique em **Concluído**.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({
                    
                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),
    
    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

Invoque a consulta na folha de dados selecionando `Sheet1` abaixo de **Inserir Parâmetro** e clique em **Invocar**. 

![Uma imagem do botão "Editor Avançado"](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Privacidade e autenticação da fonte de dados

> [!NOTE]
> Esteja ciente das políticas de sua organização referentes ao acesso a dados e à privacidade de dados. Confira [Níveis de privacidade do Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-privacy-levels) para obter mais informações.

Talvez você receba uma mensagem de aviso quando tentar executar a consulta, pois ela utiliza uma fonte de dados externa. 

![Uma imagem mostrando um aviso criado pelo Power BI](../media/tutorials/blocked-function.png)

Para corrigir isso, clique em **Arquivo** e **Opções e configurações**. Em seguida, clique em **Opções**. Abaixo de **Arquivo Atual**, selecione **Privacidade** e **Ignorar os Níveis de Privacidade e melhorar potencialmente o desempenho**. 

Além disso, talvez você receba uma mensagem solicitando que especifique como deseja se conectar à API.

![Uma imagem mostrando uma solicitação para especificar as credenciais de acesso](../media/tutorials/edit-credentials-message.png)

Para corrigir isso, clique em **Editar Credenciais** na mensagem. Depois que a caixa de diálogo for exibida, selecione **Anônimo** para se conectar anonimamente à API. E clique em **Conectar**. 

Depois disso, clique em **Fechar e Aplicar** na faixa de opções **Página Inicial** para aplicar as alterações.

## <a name="visualize-the-anomaly-detector-api-response"></a>Visualizar a resposta da API do Detector de Anomalias

Na tela principal do Power BI, comece a usar as consultas criadas acima para visualizar os dados. Primeiro selecione **Gráfico de Linhas** em **Visualizações**. Em seguida, adicione o carimbo de data/hora da função invocada ao **Eixo** do gráfico de linhas. Clique com o botão direito do mouse nele e selecione **Carimbo de data/hora**. 

![Clique com o botão direito do mouse no valor do Carimbo de data/hora](../media/tutorials/timestamp-right-click.png)

Adicione os campos a seguir da **Função Invocada** ao campo **Valores** do gráfico. Use a captura de tela abaixo para ajudar a criar o gráfico.

    * Valor
    * UpperMargins
    * LowerMargins
    * ExpectedValues

![Uma imagem da tela da nova medida rápida](../media/tutorials/chart-settings.png)

Depois de adicionar os campos, clique no gráfico e redimensione-o para mostrar todos os pontos de dados. O gráfico será semelhante à captura de tela abaixo:

![Uma imagem da tela da nova medida rápida](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Exibir pontos de dados de anomalias

No lado direito da janela do Power BI, abaixo do painel **CAMPOS**, clique com o botão direito do mouse em **Valor** abaixo da **Consulta de Função Invocada** e clique em **Nova medida rápida**.

![Uma imagem da tela da nova medida rápida](../media/tutorials/new-quick-measure.png)

Na tela exibida, selecione **Valor filtrado** como o cálculo. Defina **Valor Base** como `Sum of Value`. Em seguida, arraste `IsAnomaly` dos campos **Função Invocada** para **Filtro**. Selecione `True` no menu suspenso **Filtro**.

![Uma imagem da tela da nova medida rápida](../media/tutorials/new-quick-measure-2.png)

Depois de clicar em **OK**, você terá um campo `Value for True` na parte inferior da lista de campos. Clique com o botão direito do mouse nele e renomeie-o para **Anomalias**. Adicione-o aos **Valores** do gráfico. Em seguida, selecione a ferramenta **Formato** e defina o tipo do eixo X como **Categórico**.

![Uma imagem da tela da nova medida rápida](../media/tutorials/format-x-axis.png)

Aplique cores ao gráfico clicando na ferramenta **Formato** e **Cores de dados**. O gráfico deverá ter uma aparência semelhante à seguinte:

![Uma imagem da tela da nova medida rápida](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Detecção de anomalias de streaming com o Azure Databricks](anomaly-detection-streaming-databricks.md)
