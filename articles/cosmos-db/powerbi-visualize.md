---
title: Tutorial do Power BI para o conector do Azure Cosmos DB
description: Use este tutorial do Power BI para importar o JSON, criar relatórios interessantes e visualizar dados usando o conector do Azure Cosmos DB e do Power BI.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: b27bab9ea3029264143caaacf094f0a799894356
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359856"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Visualizar dados do Azure Cosmos DB usando o conector do Power BI
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O [Power BI](https://powerbi.microsoft.com/) é um serviço online no qual você pode criar e compartilhar painéis e relatórios. O Power BI Desktop é uma ferramenta de criação de relatórios que permite recuperar dados de várias fontes de dados. O Azure Cosmos DB é uma das fontes de dados que você pode usar com o Power BI Desktop. É possível conectar a conta do Power BI Desktop à conta do Azure Cosmos DB com o conector do Azure Cosmos DB para Power BI.  Após importar dados do Azure Cosmos DB para Power BI, você poderá transformá-los, criar relatórios e publicar os relatórios no Power BI.   

Este artigo descreve as etapas necessárias para conectar a conta do Azure Cosmos DB ao Power BI Desktop. Após a conexão, você navega para uma coleção, extrai os dados, transforma os dados JSON em formato tabular e publica um relatório no Power BI.

> [!NOTE]
> O conector do Power BI para Azure Cosmos DB conecta-se ao Power BI Desktop. Relatórios criados no Power BI Desktop podem ser publicados no PowerBI.com. A extração direta dos dados do Azure Cosmos DB não pode ser executada no PowerBI.com. 

> [!NOTE]
> No momento, há suporte à conexão do Azure Cosmos DB com o conector do Power BI apenas em contas da API do SQL do Azure Cosmos DB e da API do Gremlin.

## <a name="prerequisites"></a>Pré-requisitos
Antes de seguir as instruções neste tutorial do Power BI, certifique-se de ter acesso aos recursos seguintes:

* [Baixe a última versão do Power BI Desktop](https://powerbi.microsoft.com/desktop).

* Baixe os [dados do vulcão de exemplo](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/main/SampleData/VolcanoData.json) do GitHub.

* [Crie uma conta de banco de dados Cosmos do Azure](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) e importe os dados do vulcão usando a [ferramenta de migração de Azure Cosmos DB data](import-data.md). Ao importar dados, considere as seguintes configurações para a origem e os destinos na ferramenta de migração de dados:

   * **Parâmetros da origem** 

       * **Importar de:** Arquivo (s) JSON

   * **Parâmetros de destino** 

      * **Cadeia de conexão:**`AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Chave de partição:** /Country 

      * **Taxa de transferência de coleta:** 1000 

Para compartilhar seus relatórios no PowerBI.com, você deve ter uma conta no PowerBI.com.  Para saber mais sobre Power BI e Power BI Pro, consulte [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing) .

## <a name="lets-get-started"></a>Vamos começar
Neste tutorial, vamos imaginar que você é um geólogo que estuda os vulcões em todo o mundo. Os dados do vulcão são armazenados em uma conta do Azure Cosmos DB e o formato do documento JSON é o seguinte:

```json
{
    "Volcano Name": "Rainier",
        "Country": "United States",
        "Region": "US-Washington",
        "Location": {
          "type": "Point",
          "coordinates": [
            -121.758,
            46.87
          ]
        },
        "Elevation": 4392,
        "Type": "Stratovolcano",
        "Status": "Dendrochronology",
        "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

Você recuperará os dados do vulcão da conta do Azure Cosmos DB e visualizará os dados em um relatório interativo do Power BI.

1. Execute o Power BI Desktop.

2. Você pode **Obter dados**, consultar **Fontes Recentes Sources** ou **Abrir outros relatórios** diretamente na tela de boas-vindas. Selecione o "X" no canto superior direito para fechar a tela. A exibição de **Relatório** do Power BI Desktop é exibida.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview.png" alt-text="Exibição de relatório do Power BI Desktop — conector do Power BI":::

3. Selecione a faixa de opções **Página Inicial** e clique em **Obter Dados**.  A janela **Obter Dados** deve ser exibida.

4. Clique em **Azure**, selecione **Azure Cosmos DB (Beta)** e clique em **Conectar**. 

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbigetdata.png" alt-text="Obtenção de dados do Power BI Desktop — conector do Power BI":::

5. Na página de **Visualizar Conector**, clique em **Continuar**. A janela do **Azure Cosmos DB** é exibida.

6. Especifique a URL do ponto de extremidade da conta do Azure Cosmos DB da qual você deseja recuperar os dados, conforme mostrado abaixo e clique em **OK**. Para usar sua própria conta, você pode recuperar a URL na caixa de URI na folha **Chaves** do portal do Azure. Opcionalmente, é possível fornecer o nome do banco de dados, o nome da coleção ou usar o navegador para selecionar o banco de dados e a coleção para identificar de onde os dados são provenientes.
   
7. Se estiver se conectando a este ponto de extremidade pela primeira vez, você será solicitado a fornecer a chave de conta. Para sua própria conta, recupere a chave na caixa **Chave Primária** na folha **Chaves Somente Leitura** do portal do Azure. Insira a chave apropriada e, em seguida, clique em **Conectar**.
   
   Recomendamos que você use a chave somente leitura ao criar relatórios. Isso impede a exposição desnecessária da chave primária a possíveis riscos de segurança. A chave somente leitura está disponível na folha **Chaves** no portal do Azure. 
    
8. Quando a conta é conectada com êxito, o painel **Navegador** é exibido. O **Navegador** mostra uma lista de bancos de dados na conta.

9. Clique e expanda no banco de dados de origem os dados do relatório, selecione **volcanodb** (o nome do banco de dados pode ser diferente).   

10. Agora, selecione uma coleção que contenha os dados a serem recuperados, selecione **volcano1** (o nome da coleção pode ser diferente).
    
    O painel Visualização mostra uma lista dos itens de **Registro** .  Um Documento é representado como um tipo **Registro** no Power BI. Da mesma forma, um bloco JSON aninhado dentro de um documento é também um **Registro**.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbinavigator.png" alt-text="Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Janela Navegador":::

12. Clique em **Editar** para iniciar o Editor de Consultas em uma nova janela para transformar os dados.

## <a name="flattening-and-transforming-json-documents"></a>Nivelando e transformando documentos JSON
1. Altere para a janela do Editor de Consultas do Power BI, onde a coluna **Documento** no painel central.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png" alt-text="Editor de Consultas do Power BI Desktop":::

1. Clique no expansor no lado direito do cabeçalho da coluna **Documento** .  O menu de contexto com uma lista de campos será exibido.  Selecione os campos que você precisa para o relatório, por exemplo, Nome do Vulcão, País, Região, Localização, Elevação, Tipo, Status e Última Erupção. Desmarque a caixa **Usar nome original da coluna como prefixo** e, em seguida, clique em **OK**.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png" alt-text="Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Expandir documentos":::

1. O painel central exibe uma visualização do resultado com os campos selecionados.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png" alt-text="Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Nivelar resultados":::

1. Em nosso exemplo, a propriedade Location é um bloco GeoJSON em um documento.  Como você pode ver, o Local é representado como um tipo **Registro** no Power BI Desktop.  

1. Clique no expansor no lado direito do cabeçalho da coluna Document.Location.  O menu de contexto com os campos tipo e coordenadas é exibido.  Vamos selecionar o campo de coordenadas, assegurar de que **Usar nome original da coluna como prefixo** não está selecionado e clicar em **OK**.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png" alt-text="Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Registro de localização":::

1. Agora, o painel central mostra uma coluna de coordenadas do tipo **Lista** .  Como mostrado no início do tutorial, os dados GeoJSON neste tutorial são do tipo Ponto com valores de Latitude e Longitude gravados na matriz de coordenadas.
   
   O elemento coordinates[0] representa a Longitude, enquanto que coordinates[1] representa a Latitude.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png" alt-text="Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Lista de coordenadas":::

1. Para nivelar a matriz de coordenadas, crie uma **Coluna Personalizada** chamada LatLong.  Selecione a faixa de opções **Adicionar Coluna** e clique em **Coluna Personalizada**.  A janela **Coluna Personalizada** é exibida.

1. Forneça um nome para a nova coluna, por exemplo, LatLong.

1. Em seguida, especifique a fórmula personalizada para a nova coluna.  Para nosso exemplo, os valores de Latitude e Longitude, separados por uma vírgula, serão concatenados com a seguinte fórmula, como mostrado abaixo: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Clique em **OK**.
   
   Para obter mais informações sobre o DAX (Data Analysis Expressions) incluindo funções DAX, visite [noções básicas do Dax em Power bi desktop](/power-bi/desktop-quickstart-learn-dax-basics).
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png" alt-text="Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Adicionar Coluna Personalizada":::

1. Agora, o painel central mostra as novas colunas LatLong preenchidas com os valores.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png" alt-text="Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Coluna LatLong personalizada":::
    
    Se você receber um erro na nova coluna, verifique as etapas aplicadas nas Configurações de Consulta correspondem à figura abaixo:
    
    :::image type="content" source="./media/powerbi-visualize/power-bi-applied-steps.png" alt-text="As etapas aplicadas devem ser Origem, Navegação, Document.Location Expandido, Personalizado Adicionado":::
    
    Se as etapas são diferentes, exclua as etapas adicionais e tente adicionar a coluna personalizada novamente. 

1. Clique em **Fechar e Aplicar** para salvar o modelo de dados.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicloseapply.png" alt-text="Tutorial de Power BI do conector para Azure Cosmos DB Power BI-fechar & aplicar":::

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Compilar os relatórios

A exibição de Relatório do Power BI Desktop é aquela em que você pode começar a criar relatórios para visualizar dados.  Você pode criar relatórios arrastando e soltando campos na tela **Relatório** .

:::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview2.png" alt-text="Power BI Desktop exibição de relatório – arraste e solte os campos obrigatórios":::

Na exibição de Relatório, você deve encontrar:

1. O painel **Campos** é onde você pode ver uma lista de modelos de dados com campos que podem ser utilizados para os relatórios.
1. O painel **Visualizações** . Um relatório pode conter uma ou várias visualizações.  Selecione os tipos de elementos visuais que atendem às suas necessidades no painel **Visualizações** .
1. A tela **Relatório** é onde você cria os elementos visuais do relatório.
1. A página **Relatório** . Você pode adicionar várias páginas do relatório no Power BI Desktop.

O exemplo a seguir mostra as etapas básicas da criação de um relatório interativo simples da exibição de Mapa.

1. Para nosso exemplo, criaremos uma exibição de mapa mostrando o local de cada vulcão.  No painel **Visualizações** , clique no tipo de elemento visual Mapa, como destacado na captura de tela acima.  Você deverá ver o tipo de elemento visual Mapa pintado na tela **Relatório** .  O painel **Visualização** também deve exibir um conjunto de propriedades relacionadas ao tipo de elemento visual Mapa.
1. Agora, arraste e solte o campo LatLong do painel **Campos** para a propriedade **Location** no painel **Visualizações**.
1. Em seguida, arraste e solte o campo Nome do Vulcão para a propriedade **Legend** .  
1. Em seguida, arraste e solte o campo Elevação para a propriedade **Size** .  
1. Agora você deverá ver o elemento visual Mapa mostrando um conjunto de bolhas que indica o local de cada vulcão com o tamanho da bolha correlacionando à elevação do vulcão.
1. Agora você criou um relatório básico.  É possível personalizar ainda mais o relatório adicionando mais visualizações.  Em nosso caso, adicionamos uma segmentação de dados do Tipo de Vulcão para tornar o relatório interativo.  
   
1. No menu Arquivo, clique em **Salvar** e salve o arquivo como PowerBITutorial.pbix.

## <a name="publish-and-share-your-report"></a>Publicar e compartilhar seu relatório
Para compartilhar seu relatório, você deve ter uma conta no PowerBI.com.

1. No Power BI Desktop, clique na faixa de opções **Página Inicial** .
1. Clique em **Publicar**.  Você será solicitado a inserir o nome de usuário e senha da conta do PowerBI.com.
1. Depois que a credencial é autenticada, o relatório é publicado no destino selecionado.
1. Clique em **Abrir 'PowerBITutorial.pbix' no Power BI** para ver e compartilhar seu relatório no PowerBI.com.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png" alt-text="Publicação em Power BI êxito! Abrir o tutorial no Power BI":::

## <a name="create-a-dashboard-in-powerbicom"></a>Criar um painel no PowerBI.com
Agora que você tem um relatório, vamos compartilhá-lo no PowerBI.com

Quando você publica o relatório do Power BI Desktop no PowerBI.com, ele gera um **Relatório** e um **Conjunto de Dados** em seu locatário PowerBI.com. Por exemplo, após publicar um relatório chamado **PowerBITutorial** no PoweBI.com, você verá PowerBITutorial nas seções **Relatórios** e **Conjuntos de Dados** no PowerBI.com.

   :::image type="content" source="./media/powerbi-visualize/powerbi-reports-datasets.png" alt-text="Captura de tela dos novos Relatório e Conjunto de Dados no PowerBI.com":::

Para criar um painel compartilhável, clique no botão **Pin Live página** no seu relatório em PowerBI.com.

   :::image type="content" source="./media/powerbi-visualize/power-bi-pin-live-tile.png" alt-text="Captura de tela de como fixar um relatório no PowerBI.com":::

Siga as instruções em [Fixar um bloco de um relatório](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) para criar um novo painel. 

Você também pode fazer modificações ad hoc ao relatório antes de criar um painel. No entanto, é recomendável que você use o Power BI Desktop para executar as modificações e republicar o relatório no PowerBI.com.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

:::image type="content" source="./media/powerbi-visualize/power-bi-refresh-now.png" alt-text="Screenshot of Refresh Now in PowerBI.com":::

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    :::image type="content" source="./media/powerbi-visualize/power-bi-schedule-refresh.png" alt-text="Screenshot of the Schedule Refresh in PowerBI.com":::
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre o Power BI, confira [Introdução ao Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Para saber mais sobre o Azure Cosmos DB, consulte a [página de aterrissagem de documentação do Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).
