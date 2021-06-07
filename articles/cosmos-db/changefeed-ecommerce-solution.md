---
title: Usar feed de alterações do Azure Cosmos DB para visualizar análise de dados em tempo real
description: Este artigo descreve como o feed de alterações pode ser usado por uma empresa de varejo para entender os padrões do usuário, executar análise e visualização de dados em tempo real
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/28/2019
ms.author: sngun
ms.custom: devx-track-java
ms.openlocfilehash: e7b75c71d64054e38630677ecd38f8e3e2483c12
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606327"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Usar feed de alterações do Azure Cosmos DB para visualizar análise de dados em tempo real
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O feed de alteração Azure Cosmos DB é um mecanismo para obter um feed contínuo e incremental de registros de um contêiner Cosmos do Azure, pois esses registros estão sendo criados ou modificados. O suporte do feed de alterações funciona através de escuta de contêiner para quaisquer alterações. Ele gera a lista classificada de documentos que foram alterados na ordem em que eles foram modificados. Para saber mais sobre o feed de alterações, consulte o artigo [trabalhando com feed de alterações](change-feed.md). 

Este artigo descreve como o feed de alterações pode ser usado por uma empresa de comércio eletrônico para reconhecer padrões de usuário, executar análise de dados em tempo real e visualização. Você analisará eventos como um usuário visualizando um item, adicionando um item ao carrinho ou comprando um item. Quando um desses eventos ocorre, um novo registro é criado e o feed de alterações registra em log esse registro. O feed de alterações dispara uma série de etapas, resultando na visualização de métricas que analisam o desempenho e a atividade da empresa. As métricas de exemplo possíveis de serem visualizadas incluem receita, visitantes do site exclusivo, itens mais populares e preço médio dos itens que são visualizados versus adicionados a um carrinho versus comprados. Essas métricas de exemplo podem ajudar uma empresa de comércio eletrônico a avaliar a popularidade do site, desenvolver estratégias de preços e publicidade e tomar decisões sobre qual estoque investir.

Se estiver interessado em assistir a um vídeo sobre a solução antes de começar, assista ao seguinte vídeo:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Componentes da solução
O diagrama a seguir representa o fluxo de dados e os componentes envolvidos na solução:

:::image type="content" source="./media/changefeed-ecommerce-solution/project-visual.png" alt-text="Projeto visual" border="false":::
 
1. **Geração de dados:** o simulador de dados é usado para gerar dados de varejo que representam eventos como um usuário visualizando um item, adicionando um item ao carrinho e comprando um item. É possível gerar um grande conjunto de dados de exemplo usando o gerador de dados. Os dados de exemplo gerados contêm documentos no seguinte formato:
   
    ```json
    {
      "CartID": 2486,
      "Action": "Viewed",
      "Item": "Women's Denim Jacket",
      "Price": 31.99
    }
    ```

2. **Cosmos DB:** Os dados gerados são armazenados em um contêiner Cosmos do Azure.  

3. **Feed de alterações:** O feed de alterações escutará as alterações no contêiner Cosmos do Azure. Todas as vezes que um novo documento for adicionado à coleção (ou seja, quando um evento ocorrer como um usuário visualizando um item, adicionando um item ao carrinho ou comprando um item), o feed de alterações disparará um [Azure Functions](../azure-functions/functions-overview.md).  

4. **Azure Functions:** o Azure Functions processa os novos dados e envia-os para um [Hub de Eventos do Azure](../event-hubs/event-hubs-about.md).  

5. **Hub de Eventos:** o Hub de Eventos do Azure armazena esses eventos e envia-os para o [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) para executar análises adicionais.  

6. **Azure Stream Analytics:** o Azure Stream Analytics define consultas para processar os eventos e executar análise de dados em tempo real. Em seguida, esses dados são enviados para o [Microsoft Power BI](/power-bi/desktop-what-is-desktop).  

7. **Power BI:** o Power BI é usado para visualizar os dados enviados pelo Azure Stream Analytics. É possível criar um painel para ver como as métricas são alteradas em tempo real.  

## <a name="prerequisites"></a>Pré-requisitos

* Microsoft .NET Framework 4.7.1 ou superior

* Microsoft .NET Core 2.1 (ou superior)

* Visual Studio com desenvolvimento para Plataforma Universal do Windows, desenvolvimento para desktop com o .NET e cargas de trabalho de desenvolvimento para a Web e ASP.NET

* Assinatura do Microsoft Azure

* Conta do Microsoft Power BI

* Baixe o [Laboratório de feed de alterações do Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) pelo GitHub. 

## <a name="create-azure-resources"></a>Criar recursos do Azure 

Crie os recursos do Azure - Azure Cosmos DB, Conta de armazenamento, Hub de Eventos, Stream Analytics exigidos pela solução. Esses recursos serão implantados por meio de um modelo do Azure Resource Manager. Use as etapas a seguir para implantar esses recursos: 

1. Defina a política de execução do Windows PowerShell para **Irrestrito**. Para fazer isso, abra **o Windows PowerShell como um Administrador** e execute os comandos a seguir:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. No repositório GitHub do download da etapa anterior, navegue até a pasta **Azure Resource Manager** e abra o arquivo chamado **parameters.json**.  

3. Forneça valores para os parâmetros cosmosdbaccount_name, eventhubnamespace_name, storageaccount_name, conforme indicado no arquivo **parameters.json**. Posteriormente, você precisará usar os nomes que fornecer a cada um dos seus recursos.  

4. No **Windows PowerShell**, navegue até a pasta **Azure Resource Manager** e execute o comando a seguir:

   ```powershell
   .\deploy.ps1
   ```
5. Quando solicitado, insira sua ID de **Assinatura do Azure**, **changefeedlab** para o nome do grupo de recursos e **run1** para o nome da implantação. Após o início da implantação dos recursos, poderá demorar até 10 minutos até ser concluída.

## <a name="create-a-database-and-the-collection"></a>Criar um banco de dados e a coleção

Agora, você criará uma coleção para realizar eventos do site de comércio eletrônico. Quando um usuário visualiza um item, adiciona um item ao carrinho ou compra um item, a coleção recebe um registro que inclui a ação ("visualizado", "adicionado" ou "comprado"), o nome do item envolvido, o preço do item envolvido e o número de ID do carrinho de usuário envolvido.

1. Vá para [portal do Azure](https://portal.azure.com/) e localize a **conta de Azure Cosmos DB** que é criada pela implantação do modelo.  

2. No painel **Data Explorer**, selecione **Nova Coleção** e preencha o formulário com os seguintes detalhes:  

   * Para o campo **ID do Banco de Dados**, selecione **Criar novo** e, em seguida, insira **changefeedlabdatabase**. Deixe a caixa **Provisionar taxa de transferência do banco de dados** desmarcada.  
   * Para o campo de ID da **Coleção**, insira **changefeedlabcollection**.  
   * Para o campo **Chave de partição**, insira **/Item**. Esse campo diferencia maiúsculas e minúsculas, portanto, insira corretamente.  
   * Para o campo **Taxa de transferência**, insira **10000**.  
   * Selecione o botão **OK** .  

3. Em seguida, crie outra coleção nomeada **concessões** para o processamento de feed de alterações. A coleção de concessões coordena o processamento do feed de alterações em vários trabalhos. Uma coleção separada é usada para armazenar as concessões com uma concessão por partição.  

4. Retorne ao painel **Data Explorer** e selecione **Nova Coleção** e preencha o formulário com os seguintes detalhes:

   * Para o campo **ID do Banco de Dados**, selecione **Usar existente** e, em seguida, insira **changefeedlabdatabase**.  
   * Para o campo **ID da Coleção**, insira **concessões**.  
   * Para **Capacidade de armazenamento**, selecione **Fixo**.  
   * Deixe o campo **Taxa de transferência** configurado para o valor padrão.  
   * Selecione o botão **OK** .

## <a name="get-the-connection-string-and-keys"></a>Obter a cadeia de conexão e chaves

### <a name="get-the-azure-cosmos-db-connection-string"></a>Obter a cadeia de conexão do Azure Cosmos DB

1. Vá para [portal do Azure](https://portal.azure.com/) e localize a **conta de Azure Cosmos DB** que é criada pela implantação do modelo.  

2. Navegue até o painel **Chaves**, copie a CADEIA DE CONEXÃO PRIMÁRIA e copie-a para um bloco de notas ou outro documento que você terá acesso a todo o laboratório. É necessário rotulá-la como **Cadeia de Conexão do Cosmos DB**. Posteriormente, será necessário copiar a cadeia de caracteres no código, portanto, anote-a e lembre-se em que local está armazenando-a.

### <a name="get-the-storage-account-key-and-connection-string"></a>Obter a chave de conta de armazenamento e cadeia de conexão

Contas de Armazenamento do Microsoft Azure permitem que os usuários armazenem dados. Neste laboratório, você usará uma conta de armazenamento para armazenar dados usados pelo Azure Functions. O Azure Functions é disparado quando qualquer modificação é feita na coleção.

1. Retornar ao grupo de recursos e abrir a conta de armazenamento criada anteriormente  

2. Selecione **Chaves de acesso** no menu esquerdo.  

3. Copie os valores na **key 1** para um bloco de notas ou outro documento que você terá acesso a todo o laboratório. É necessário rotular a **Chave** como **Chave de armazenamento** e a **Cadeia de conexão** como **Cadeia de conexão de armazenamento**. Posteriormente, será necessário copiar essas cadeia de caracteres no código, portanto, anote-as e lembre-se em que local está armazenando-as.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Obter a cadeia de conexão para namespace do hub de eventos

Um Hub de Eventos do Azure recebe os dados do evento, armazena, processa e encaminha os dados. Neste laboratório, o Hub de Eventos do Azure receberá um documento sempre que um novo evento ocorrer (ou seja, um item for visualizado por um usuário, adicionado ao carrinho de um usuário ou adquirido por um usuário) e encaminhará esse documento ao Azure Stream Analytics.

1. Retorne ao grupo de recursos e abra o **Namespace do Hub de Eventos** criado e nomeado no prelab.  

2. Selecione **Políticas de acesso compartilhado** no menu esquerdo.  

3. Selecione **RootManageSharedAccessKey**. Copie a **Chave primária da cadeia de conexão** para um bloco de notas ou outro documento que você terá acesso a todo o laboratório. É necessário rotulá-la de cadeia de conexão do **Namespace do Hub de Eventos**. Posteriormente, será necessário copiar a cadeia de caracteres no código, portanto, anote-a e lembre-se em que local está armazenando-a.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Configurar o Azure Functions para ler o feed de alterações

Quando um novo documento é criado ou um documento atual é modificado em um contêiner Cosmos, o feed de alterações adiciona automaticamente esse documento modificado ao seu histórico de alterações de coleção. Agora, você criará e executará um Azure Functions que processará o feed de alterações. Quando um documento for criado ou modificado na coleção criada, o Azure Functions será disparado pelo feed de alterações. Em seguida, o Azure Functions enviará o documento modificado para o Hub de Eventos.

1. Retorne para o repositório que você clonou no dispositivo.  

2. Clique com o botão direito no arquivo nomeado **ChangeFeedLabSolution.sln** e selecione **Abrir com Visual Studio**.  

3. Navegue para **local.settings.json** no Visual Studio. Em seguida, use os valores registrados anteriormente para preencher os espaços em branco.  

4. Navegue para **ChangeFeedProcessor.cs**. Nos parâmetros para a função **Executar**, execute as seguintes ações:  

   * Substitua o texto **NOME DA SUA COLEÇÃO AQUI** com o nome da coleção. Se você seguiu as instruções anteriores, o nome da coleção será changefeedlabcollection.  
   * Substitua o texto **NOME DA SUA COLEÇÃO DE CONCESSÕES AQUI** com o nome da coleção de concessões. Se você seguiu instruções anteriores, o nome da coleção de concessões será **concessões**.  
   * Na parte superior do Visual Studio, verifique se a caixa Projeto de Inicialização à esquerda da seta verde indica **ChangeFeedFunction**.  
   * Selecione **Iniciar** na parte superior da página para executar o programa  
   * Você poderá confirmar que a função está sendo executada quando o aplicativo de console informar "Host de trabalho iniciado".

## <a name="insert-data-into-azure-cosmos-db"></a>Inserir dados no Azure Cosmos DB 

Para ver como o feed de alterações processa novas ações em um site de comércio eletrônico, é necessário simular dados que representem usuários visualizando itens do catálogo de produtos, adicionando esses itens aos carrinhos e comprando os itens nos carrinhos. Esses dados são arbitrários e com a finalidade de replicar quais dados em um site de comércio eletrônico seriam semelhantes.

1. Navegue de volta para o repositório no Explorador de Arquivos e clique com o botão direito em **ChangeFeedFunction.sln** para abri-lo novamente em uma nova janela do Visual Studio.  

2. Navegue até o arquivo **App.config**. No bloco `<appSettings>`, adicione o ponto de extremidade e a **CHAVE PRIMÁRIA** exclusiva da conta do Azure Cosmos DB recuperada anteriormente.  

3. Adicione os nomes da **coleção** e do **banco de dados**. (Esses nomes devem ser **changefeedlabcollection** e **changefeedlabdatabase**, a menos que você escolha nomear de modo diferente.)

   :::image type="content" source="./media/changefeed-ecommerce-solution/update-connection-string.png" alt-text="Atualizar cadeias de conexão":::
 
4. Salve as alterações em todos os arquivos editados.  

5. Na parte superior do Visual Studio, certifique-se de que a caixa **Projeto de Inicialização** à esquerda da seta verde indica **DataGenerator**. Em seguida, selecione **Iniciar** na parte superior da página para executar o programa.  
 
6. Aguarde o programa ser executado. As estrelas significam que os dados estão sendo coletados! Mantenha o programa em execução - é importante que muitos dados sejam coletados.  

7. Se você navegar até [portal do Azure](https://portal.azure.com/) e, em seguida, para a conta de Cosmos DB em seu grupo de recursos, para **Data Explorer**, você verá os dados aleatórios importados em seu **changefeedlabcollection** .
 
   :::image type="content" source="./media/changefeed-ecommerce-solution/data-generated-in-portal.png" alt-text="Dados gerados no portal":::

## <a name="set-up-a-stream-analytics-job"></a>Configurar um trabalho do Stream Analytics

O Azure Stream Analytics é um serviço de nuvem totalmente gerenciado para processamento de dados de streaming em tempo real. Neste laboratório, você usará o Stream Analytics para processar novos eventos do Hub de Eventos (isto é, quando um item for visualizado, adicionado a um carrinho ou comprado), incorporar esses eventos à análise de dados em tempo real e enviá-los para o Power BI para visualização.

1. No [portal do Azure](https://portal.azure.com/), navegue até o grupo de recursos e, em seguida, para **streamjob1** (o trabalho do Stream Analytics que você criou no prelab).  

2. Selecione **Entradas** como demonstrado abaixo.  

   :::image type="content" source="./media/changefeed-ecommerce-solution/create-input.png" alt-text="Criar entrada":::

3. Selecione **+ Adicionar entrada de fluxo**. Em seguida, selecione **Hub de Eventos** no menu suspenso.  

4. Preencha o novo formulário de entrada com os seguintes detalhes:

   * No campo alias **Entrada**, insira **entrada**.  
   * Selecione a opção para **Selecionar o Hub de Eventos de suas assinaturas**.  
   * Defina o campo **Assinatura** como a assinatura.  
   * No campo **Namespace do Hub de Eventos**, insira o nome do Namespace do Hub de Eventos que você criou durante o prelab.  
   * No campo **Nome do Hub de Eventos**, selecione a opção para **Usar existente** e escolha **event-hub1** no menu suspenso.  
   * Deixe o campo do nome da **política do Hub de Eventos** definido como o valor padrão.  
   * Deixe **Formato de serialização de evento** como **JSON**.  
   * Deixe o **campo de Codificação** definido para **UTF-8**.  
   * Deixe o campo **Tipo de compactação do evento** definido para **Nenhum**.  
   * Selecione o botão **Salvar**.

5. Navegue de volta para a página de trabalho do Stream Analytics e selecione **Saídas**.  

6. Selecione **+ Adicionar**. Em seguida, selecione **Power BI** no menu suspenso.  

7. Para criar uma nova saída do Power BI para visualizar o preço médio, execute as seguintes ações:

   * No campo **Alias de saída**, insira **averagePriceOutput**.  
   * Deixe o campo **Grupo de workspace** definido para **Autorizar conexão para carregar workspaces**.  
   * No campo **Nome do conjunto de dados**, insira **averagePrice**.  
   * No campo **Nome da tabela**, insira **averagePrice**.  
   * Selecione o botão **Autorizar** e, em seguida, siga as instruções para autorizar a conexão com o Power BI.  
   * Selecione o botão **Salvar**.  

8. Em seguida, volte para **streamjob1** e selecione **Editar consulta**.

   :::image type="content" source="./media/changefeed-ecommerce-solution/edit-query.png" alt-text="Editar consulta":::
 
9. Cole a seguinte consulta na janela de consulta. A consulta **PREÇO MÉDIO** calcula o preço médio de todos os itens visualizados pelos usuários, o preço médio de todos os itens adicionados aos carrinhos dos usuários e o preço médio de todos os itens comprados pelos usuários. Essa métrica pode ajudar as empresas de comércio eletrônico a decidir em quais preços vender itens e em qual estoque investir. Por exemplo, se o preço médio dos itens visualizados for muito maior que o preço médio dos itens comprados, uma empresa poderá optar por adicionar itens menos caros ao estoque.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Em seguida, selecione **Salvar** no canto superior esquerdo.  

11. Agora, retorne para **streamjob1** e selecione o botão **Iniciar** na parte superior da página. O Azure Stream Analytics pode demorar alguns minutos para inicializar, mas você verá a mudança de "Início" para "Executando".

## <a name="connect-to-power-bi"></a>Conectar o Power BI

O Power BI é um conjunto de ferramentas de análise de negócios para analisar dados e compartilhar insights. É um ótimo exemplo de como você pode visualizar estrategicamente os dados analisados.

1. Entre no Power BI e navegue até **Meu Workspace**, abrindo o menu no lado esquerdo da página.  

2. Selecione **+ Criar** no canto superior direito e, em seguida, selecione **Painel** para criar um painel.  

3. Selecione **+ Adicionar bloco** no canto superior direito.  

4. Selecione **Dados de Streaming Personalizados** e, em seguida, selecione o botão **Avançar**.  
 
5. Selecione **averagePrice** em **SEUS CONJUNTOS DE DADOS** e, em seguida, selecione **Avançar**.  

6. No campo **Tipo de Visualização**, escolha **Gráfico de barras clusterizado** no menu suspenso. Em **Eixo**, adicione ação. Ignore a **Legenda** sem adicionar nada. Em seguida, na próxima seção chamada **Value**, adicione **AVG**. Selecione **Avançar**, então, título do gráfico e selecione **aplicar**. Um novo gráfico no painel deverá ser exibido!  

7. Agora, se você quiser visualizar mais métricas, volte para **streamjob1** e crie mais três saídas com os seguintes campos.

   a. **Alias de saída:** incomingRevenueOutput, nome do Conjunto de Dados: incomingRevenue, nome da Tabela: incomingRevenue  
   b. **Alias de saída:** top5Output, nome do Conjunto de Dados: top5, nome da Tabela: top5  
   c. **Alias de saída:** uniqueVisitorCountOutput, nome do Conjunto de Dados: uniqueVisitorCount, nome da Tabela: uniqueVisitorCount

   Em seguida, selecione **Editar consulta** e cole as seguintes consultas **acima** da que você já gravou.

   ```sql
    /*TOP 5*/
    WITH Counter AS
    (
    SELECT Item, Price, Action, COUNT(*) AS countEvents
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY Item, Price, Action, TumblingWindow(second,30)
    ), 
    top5 AS
    (
    SELECT DISTINCT
    CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
    FROM Counter
    GROUP BY TumblingWindow(second,30)
    ), 
    arrayselect AS 
    (
    SELECT arrayElement.ArrayValue
    FROM top5
    CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
    ) 
    SELECT arrayvalue.value.item, arrayvalue.value.price,   arrayvalue.value.countEvents
    INTO top5Output
    FROM arrayselect

    /*REVENUE*/
    SELECT System.TimeStamp AS Time, SUM(Price)
    INTO incomingRevenueOutput
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY TumblingWindow(hour, 1)

    /*UNIQUE VISITORS*/
    SELECT System.TimeStamp AS Time, COUNT(DISTINCT CartID) as uniqueVisitors
    INTO uniqueVisitorCountOutput
    FROM input
    GROUP BY TumblingWindow(second, 5)
   ```
   
   A consulta TOP 5 calcula os 5 principais itens, classificados pelo número de vezes que foram comprados. Essa métrica pode ajudar as empresas de comércio eletrônico a avaliar quais itens são mais populares e podem influenciar as decisões de publicidade, preço e estoque da empresa.

   A consulta RECEITA calcula a receita, somando os preços de todos os itens comprados por minuto. Essa métrica pode ajudar as empresas de comércio eletrônico a avaliar o desempenho financeiro e também a reconhecer quais horas do dia contribuem para a maior parte da receita. Isso pode afetar a estratégia geral da empresa e de marketing, em particular.

   A consulta VISITANTES EXCLUSIVOS calcula quantos visitantes exclusivos estão no site a cada 5 segundos, detectando IDs de carrinho exclusivos. Essa métrica pode ajudar as empresas de comércio eletrônico a avaliar a atividade no site e criar estratégias para conquistar mais clientes.

8. Agora, você também pode adicionar blocos para esses conjuntos de dados.

   * Para Top 5, seria interessante elaborar um gráfico de colunas clusterizado com os itens como o eixo e a contagem como o valor.  
   * Para Receita, seria interessante elaborar um gráfico de linhas com o tempo como o eixo e a soma dos preços como o valor. A janela de tempo a ser exibida deve ser a maior possível para fornecer o máximo possível de informações.  
   * Para Visitantes Exclusivos, faria sentido fazer uma visualização de cartão com o número de visitantes exclusivos como o valor.

   Um painel de amostra é semelhante a estes gráficos:

   :::image type="content" source="./media/changefeed-ecommerce-solution/visualizations.png" alt-text="Captura de tela mostra um painel de exemplo com gráficos denominados preço médio de itens por ação, visitantes exclusivos, receita e 5 principais itens comprados.":::

## <a name="optional-visualize-with-an-e-commerce-site"></a>Opcional: visualizar com um site de comércio eletrônico

Agora, você observará como é possível usar a nova ferramenta de análise de dados para conectar um site de comércio eletrônico real. Para criar o site de comércio eletrônico, use um banco de dados Cosmos do Azure para armazenar a lista de categorias de produtos (mulheres, homens, unissex), o catálogo de produtos e uma lista dos itens mais populares.

1. Navegue de volta para o [portal do Azure](https://portal.azure.com/), em seguida, para sua **conta de Cosmos DB** e, em seguida, para **Data Explorer**.  

   Adicione duas coleções em   -  **produtos** e **categorias** do changefeedlabdatabase com capacidade de armazenamento fixo.

   Adicione outra coleção em **changefeedlabdatabase** chamada **topItems** e **/Item** como a chave de partição.

2. Selecione a coleção **topItems** e, em **Escala e configurações**, defina a **Vida Útil** como **30 segundos** para que a coleção topItems seja atualizada a cada 30 segundos.

   :::image type="content" source="./media/changefeed-ecommerce-solution/time-to-live.png" alt-text="Vida útil":::

3. Para preencher a coleção **topItems** com os itens comprados com mais frequência, navegue de volta para **streamjob1** e adicione uma nova **Saída**. Selecione **Cosmos DB**.

4. Preencha os campos obrigatórios, conforme a figura abaixo.

   :::image type="content" source="./media/changefeed-ecommerce-solution/cosmos-output.png" alt-text="Saída do Cosmos":::
 
5. Se você adicionou a consulta TOP 5 opcional na parte anterior do laboratório, prossiga para a parte 5a. Caso contrário, prossiga para a parte 5b.

   5a. Em **streamjob1**, selecione **Editar consulta** e cole a consulta a seguir no editor de consulta do Azure Stream Analytics abaixo da consulta TOP 5, mas acima do restante das consultas.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. Em **streamjob1**, selecione **Editar consulta** e cole a consulta a seguir no editor de consultas do Azure Stream Analytics acima de todas as outras consultas.

   ```sql
   /*TOP 5*/
   WITH Counter AS
   (
   SELECT Item, Price, Action, COUNT(*) AS countEvents
   FROM input
   WHERE Action = 'Purchased'
   GROUP BY Item, Price, Action, TumblingWindow(second,30)
   ), 
   top5 AS
   (
   SELECT DISTINCT
   CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
   FROM Counter
   GROUP BY TumblingWindow(second,30)
   ), 
   arrayselect AS 
   (
   SELECT arrayElement.ArrayValue
   FROM top5
   CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
   ) 
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```

6. Abra **EcommerceWebApp.sln** e navegue até o arquivo **Web.config** no **Gerenciador de Soluções**.  

7. No bloco `<appSettings>`, adicione o **URI** e a **CHAVE PRIMÁRIA** que você salvou anteriormente, onde informa **seu URI aqui** e **sua chave primária aqui**. Em seguida, adicione o **nome do banco de dados** e o **nome da coleção**, conforme indicado. (Esses nomes devem ser **changefeedlabdatabase** e **changefeedlabcollection**, a menos que você queira nomeá-los de maneira diferente.)

   Preencha o **nome da coleção de produtos**, **nome da coleção de categorias** e o **nome da coleção dos principais itens**, conforme indicado. (Esses nomes devem ser **produtos, categorias e topItems**, a menos que você queira nomeá-los de maneira diferente.)  

8. Navegue e abra a **Pasta de check-out** em **EcommerceWebApp.sln.** Em seguida, abra o arquivo **Web.config** dentro dessa pasta.  

9. No bloco `<appSettings>`, adicione o **URI** e a **CHAVE PRIMÁRIA** que você salvou anteriormente onde indicado. Em seguida, adicione o **nome do banco de dados** e o **nome da coleção**, conforme indicado. (Esses nomes devem ser **changefeedlabdatabase** e **changefeedlabcollection**, a menos que você queira nomeá-los de maneira diferente.)  

10. Pressione **Iniciar** na parte superior da página para executar o programa.  

11. Agora, é possível explorar o site de comércio eletrônico. Ao visualizar um item, adicionar um item ao carrinho, alterar a quantidade de um item no carrinho ou comprar um item, esses eventos serão transmitidos por meio do feed de alterações do Cosmos DB para o Hub de Eventos, o ASA e, em seguida, para o Power BI. É recomendável continuar executando o DataGenerator para gerar dados de tráfego da Web significativos e fornecer um conjunto realista de "Produtos Frequentes" no site de comércio eletrônico.

## <a name="delete-the-resources"></a>Excluir os recursos

Para excluir os recursos que você criou durante este laboratório, navegue até o grupo de recursos em [portal do Azure](https://portal.azure.com/), em seguida, selecione **excluir grupo de recursos** no menu na parte superior da página e siga as instruções fornecidas.

## <a name="next-steps"></a>Próximas etapas 
  
* Para saber mais sobre feed de alterações, consulte [como trabalhar com suporte de feed de alterações no Azure Cosmos DB](change-feed.md)