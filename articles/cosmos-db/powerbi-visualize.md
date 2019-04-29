---
title: Tutorial do Power BI para o conector do Azure Cosmos DB
description: Use este tutorial do Power BI para importar o JSON, criar relatórios interessantes e visualizar dados usando o conector do Azure Cosmos DB e do Power BI.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: sngun
ms.openlocfilehash: 2c58b982e596c95aa47442c1897410fe9ab6b99a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60929552"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Visualizar dados do Azure Cosmos DB usando o conector do Power BI

O [Power BI](https://powerbi.microsoft.com/) é um serviço online no qual você pode criar e compartilhar painéis e relatórios. O Power BI Desktop é uma ferramenta de criação de relatórios que permite recuperar dados de várias fontes de dados. O Azure Cosmos DB é uma das fontes de dados que você pode usar com o Power BI Desktop. É possível conectar a conta do Power BI Desktop à conta do Azure Cosmos DB com o conector do Azure Cosmos DB para Power BI.  Após importar dados do Azure Cosmos DB para Power BI, você poderá transformá-los, criar relatórios e publicar os relatórios no Power BI.   

Este artigo descreve as etapas necessárias para conectar a conta do Azure Cosmos DB ao Power BI Desktop. Após a conexão, você navega para uma coleção, extrai os dados, transforma os dados JSON em formato tabular e publica um relatório no Power BI.

> [!NOTE]
> O conector do Power BI para Azure Cosmos DB conecta-se ao Power BI Desktop. Relatórios criados no Power BI Desktop podem ser publicados no PowerBI.com. A extração direta dos dados do Azure Cosmos DB não pode ser executada no PowerBI.com. 

> [!NOTE]
> No momento, há suporte à conexão do Azure Cosmos DB com o conector do Power BI apenas em contas da API do SQL do Azure Cosmos DB e da API do Gremlin.

## <a name="prerequisites"></a>Pré-requisitos
Antes de seguir as instruções neste tutorial do Power BI, certifique-se de ter acesso aos recursos seguintes:

* [Baixe a última versão do Power BI Desktop](https://powerbi.microsoft.com/desktop).

* Baixe os [dados do vulcão de exemplo](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/master/SampleData/VolcanoData.json) do GitHub.

* [Crie uma conta do banco de dados do Azure Cosmos DB](https://azure.microsoft.com/documentation/articles/create-account/) e importe os dados do vulcão usando a [ferramenta de migração de dados do Azure Cosmos DB](import-data.md). Ao importar dados, considere as seguintes configurações para a origem e os destinos na ferramenta de migração de dados:

   * **Parâmetros da origem** 

       * **Importar de:** Arquivo(s) JSON

   * **Parâmetros de destino** 

      * **Cadeia de conexão:** `AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Chave de partição:** /Country 

      * **Taxa de transferência de coleção:** 1000 

Para compartilhar seus relatórios no PowerBI.com, você deve ter uma conta no PowerBI.com.  Para saber mais sobre o Power BI e Power BI Pro, consulte [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Vamos começar
Neste tutorial, vamos imaginar que você é um geólogo que estuda os vulcões em todo o mundo. Os dados do vulcão são armazenados em uma conta do Azure Cosmos DB e o formato do documento JSON é o seguinte:

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

Você recuperará os dados do vulcão da conta do Azure Cosmos DB e visualizará os dados em um relatório interativo do Power BI.

1. Execute o Power BI Desktop.

2. Você pode **Obter dados**, consultar **Fontes Recentes Sources** ou **Abrir outros relatórios** diretamente na tela de boas-vindas. Selecione o "X" no canto superior direito para fechar a tela. A exibição de **Relatório** do Power BI Desktop é exibida.
   
   ![Exibição de relatório do Power BI Desktop — conector do Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview.png)

3. Selecione a faixa de opções **Página Inicial** e clique em **Obter Dados**.  A janela **Obter Dados** deve ser exibida.

4. Clique em **Azure**, selecione **Azure Cosmos DB (Beta)** e clique em **Conectar**. 

    ![Obtenção de dados do Power BI Desktop — conector do Power BI](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   

5. Na página de **Visualizar Conector**, clique em **Continuar**. A janela do **Azure Cosmos DB** é exibida.

6. Especifique a URL do ponto de extremidade da conta do Azure Cosmos DB da qual você deseja recuperar os dados, conforme mostrado abaixo e clique em **OK**. Para usar sua própria conta, você pode recuperar a URL na caixa de URI na folha **Chaves** do portal do Azure. Opcionalmente, é possível fornecer o nome do banco de dados, o nome da coleção ou usar o navegador para selecionar o banco de dados e a coleção para identificar de onde os dados são provenientes.
   
7. Se estiver se conectando a este ponto de extremidade pela primeira vez, você será solicitado a fornecer a chave de conta. Para sua própria conta, recupere a chave na caixa **Chave Primária** na folha **Chaves Somente Leitura** do portal do Azure. Insira a chave apropriada e, em seguida, clique em **Conectar**.
   
   Recomendamos que você use a chave somente leitura ao criar relatórios. Isso evita a exposição desnecessária da chave mestra a potenciais riscos de segurança. A chave somente leitura está disponível na folha **Chaves** no portal do Azure. 
    
8. Quando a conta é conectada com êxito, o painel **Navegador** é exibido. O **Navegador** mostra uma lista de bancos de dados na conta.

9. Clique e expanda no banco de dados de origem os dados do relatório, selecione **volcanodb** (o nome do banco de dados pode ser diferente).   

10. Agora, selecione uma coleção que contenha os dados a serem recuperados, selecione **volcano1** (o nome da coleção pode ser diferente).
    
    O painel Visualização mostra uma lista dos itens de **Registro** .  Um Documento é representado como um tipo **Registro** no Power BI. Da mesma forma, um bloco JSON aninhado dentro de um documento é também um **Registro**.
    
    ![Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Janela Navegador](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Clique em **Editar** para iniciar o Editor de Consultas em uma nova janela para transformar os dados.

## <a name="flattening-and-transforming-json-documents"></a>Nivelando e transformando documentos JSON
1. Altere para a janela do Editor de Consultas do Power BI, onde a coluna **Documento** no painel central.
   ![Editor de Consultas do Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Clique no expansor no lado direito do cabeçalho da coluna **Documento** .  O menu de contexto com uma lista de campos será exibido.  Selecione os campos que você precisa para o relatório, por exemplo, Nome do Vulcão, País, Região, Localização, Elevação, Tipo, Status e Última Erupção. Desmarque a caixa **Usar nome original da coluna como prefixo** e, em seguida, clique em **OK**.
   
    ![Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Expandir documentos](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. O painel central exibe uma visualização do resultado com os campos selecionados.
   
    ![Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Nivelar resultados](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. Em nosso exemplo, a propriedade Location é um bloco GeoJSON em um documento.  Como você pode ver, o Local é representado como um tipo **Registro** no Power BI Desktop.  
5. Clique no expansor no lado direito do cabeçalho da coluna Document.Location.  O menu de contexto com os campos tipo e coordenadas é exibido.  Vamos selecionar o campo de coordenadas, assegurar de que **Usar nome original da coluna como prefixo** não está selecionado e clicar em **OK**.
   
    ![Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Registro de localização](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. Agora, o painel central mostra uma coluna de coordenadas do tipo **Lista** .  Como mostrado no início do tutorial, os dados GeoJSON neste tutorial são do tipo Ponto com valores de Latitude e Longitude gravados na matriz de coordenadas.
   
    O elemento coordinates[0] representa a Longitude, enquanto que coordinates[1] representa a Latitude.
    ![Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Lista de coordenadas](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Para nivelar a matriz de coordenadas, crie uma **Coluna Personalizada** chamada LatLong.  Selecione a faixa de opções **Adicionar Coluna** e clique em **Coluna Personalizada**.  A janela **Coluna Personalizada** é exibida.
8. Forneça um nome para a nova coluna, por exemplo, LatLong.
9. Em seguida, especifique a fórmula personalizada para a nova coluna.  Para nosso exemplo, os valores de Latitude e Longitude, separados por uma vírgula, serão concatenados com a seguinte fórmula, como mostrado abaixo: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Clique em **OK**.
   
    Para obter mais informações sobre expressões DAX (Data Analysis) incluindo as funções DAX, visite [Noções básicas do DAX no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-learn-dax-basics).
   
    ![Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Adicionar Coluna Personalizada](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Agora, o painel central mostra as novas colunas LatLong preenchidas com os valores.
    
    ![Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Coluna LatLong personalizada](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Se você receber um erro na nova coluna, verifique as etapas aplicadas nas Configurações de Consulta correspondem à figura abaixo:
    
    ![As etapas aplicadas devem ser Origem, Navegação, Document.Location Expandido, Personalizado Adicionado](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Se as etapas são diferentes, exclua as etapas adicionais e tente adicionar a coluna personalizada novamente. 

11. Clique em **Fechar e Aplicar** para salvar o modelo de dados.
    
    ![Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Fechar e Aplicar](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Compilar os relatórios
A exibição de Relatório do Power BI Desktop é aquela em que você pode começar a criar relatórios para visualizar dados.  Você pode criar relatórios arrastando e soltando campos na tela **Relatório** .

![Exibição de relatório do Power BI Desktop — conector do Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

Na exibição de Relatório, você deve encontrar:

1. O painel **Campos** é onde você pode ver uma lista de modelos de dados com campos que podem ser utilizados para os relatórios.
2. O painel **Visualizações** . Um relatório pode conter uma ou várias visualizações.  Selecione os tipos de elementos visuais que atendem às suas necessidades no painel **Visualizações** .
3. A tela **Relatório** é onde você cria os elementos visuais do relatório.
4. A página **Relatório** . Você pode adicionar várias páginas do relatório no Power BI Desktop.

O exemplo a seguir mostra as etapas básicas da criação de um relatório interativo simples da exibição de Mapa.

1. Para nosso exemplo, criaremos uma exibição de mapa mostrando o local de cada vulcão.  No painel **Visualizações** , clique no tipo de elemento visual Mapa, como destacado na captura de tela acima.  Você deverá ver o tipo de elemento visual Mapa pintado na tela **Relatório** .  O painel **Visualização** também deve exibir um conjunto de propriedades relacionadas ao tipo de elemento visual Mapa.
2. Agora, arraste e solte o campo LatLong do painel **Campos** para a propriedade **Location** no painel **Visualizações**.
3. Em seguida, arraste e solte o campo Nome do Vulcão para a propriedade **Legend** .  
4. Em seguida, arraste e solte o campo Elevação para a propriedade **Size** .  
5. Agora você deverá ver o elemento visual Mapa mostrando um conjunto de bolhas que indica o local de cada vulcão com o tamanho da bolha correlacionando à elevação do vulcão.
6. Agora você criou um relatório básico.  É possível personalizar ainda mais o relatório adicionando mais visualizações.  Em nosso caso, adicionamos uma segmentação de dados do Tipo de Vulcão para tornar o relatório interativo.  
   
7. No menu Arquivo, clique em **Salvar** e salve o arquivo como PowerBITutorial.pbix.

## <a name="publish-and-share-your-report"></a>Publicar e compartilhar seu relatório
Para compartilhar seu relatório, você deve ter uma conta no PowerBI.com.

1. No Power BI Desktop, clique na faixa de opções **Página Inicial** .
2. Clique em **Publicar**.  Você será solicitado a inserir o nome de usuário e senha da conta do PowerBI.com.
3. Depois que a credencial é autenticada, o relatório é publicado no destino selecionado.
4. Clique em **Abrir 'PowerBITutorial.pbix' no Power BI** para ver e compartilhar seu relatório no PowerBI.com.
   
    ![Publicando no Power BI Sucesso! Abrir tutorial no Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Criar um painel no PowerBI.com
Agora que você tem um relatório, vamos compartilhá-lo no PowerBI.com

Quando você publica o relatório do Power BI Desktop no PowerBI.com, ele gera um **Relatório** e um **Conjunto de Dados** em seu locatário PowerBI.com. Por exemplo, após publicar um relatório chamado **PowerBITutorial** no PoweBI.com, você verá PowerBITutorial nas seções **Relatórios** e **Conjuntos de Dados** no PowerBI.com.

   ![Captura de tela dos novos Relatório e Conjunto de Dados no PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Para criar um painel compartilhável, clique no botão **Pin Live página** no seu relatório em PowerBI.com.

   ![Captura de tela dos novos Relatório e Conjunto de Dados no PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Siga as instruções em [Fixar um bloco de um relatório](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) para criar um novo painel. 

Você também pode fazer modificações ad hoc ao relatório antes de criar um painel. No entanto, é recomendável que você use o Power BI Desktop para executar as modificações e republicar o relatório no PowerBI.com.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

![Screenshot of Refresh Now in PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    ![Screenshot of the Schedule Refresh in PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
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

