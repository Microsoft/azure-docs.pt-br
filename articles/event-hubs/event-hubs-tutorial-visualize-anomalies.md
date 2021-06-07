---
title: Hubs de Eventos do Azure – visualizar anomalias de dados nos eventos em tempo real
description: 'Tutorial: Visualizar anomalias de dados nos eventos em tempo real enviados aos Hubs de Eventos do Microsoft Azure'
ms.topic: tutorial
ms.date: 06/23/2020
ms.openlocfilehash: bb7d8da2498005b8b2e1183a836d9385f3d31e5c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783724"
---
# <a name="tutorial-visualize-data-anomalies-in-real-time-events-sent-to-azure-event-hubs"></a>Tutorial: Visualizar anomalias de dados nos eventos em tempo real enviados aos Hubs de Eventos do Azure

Com os Hubs de Eventos do Azure, você pode usar o Azure Stream Analytics para verificar os dados de entrada e remover as anomalias, que poderão ser visualizadas no Power BI. Digamos que você tenha milhares de dispositivos enviando dados em tempo real constantemente para um hub de eventos, totalizando milhões de eventos por segundo. Como verificar anomalias ou erros nessa quantidade de dados? Por exemplo, se os dispositivos estão enviando transações de cartão de crédito e você precisa capturar qualquer lugar em que tenha várias transações em vários países/regiões em um intervalo de tempo de cinco segundos? Isso pode ocorrer se alguém rouba cartões de crédito e os usa para comprar itens em todo o mundo ao mesmo tempo. 

Neste tutorial, você simulará esse exemplo. Você executa um aplicativo que cria e envia transações de cartão de crédito para um hub de eventos. Em seguida, você lerá o fluxo de dados em tempo real com o Azure Stream Analytics, que separa as transações válidas das transações inválidas e usará o Power BI para identificar visualmente as transações marcadas como inválidas.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar um namespace de Hubs de Eventos
> * Criar um Hub de Evento
> * Executar o aplicativo que envia transações de cartão de crédito
> * Configurar um trabalho do Stream Analytics para processar as transações
> * Configurar uma visualização do Power BI para mostrar os resultados

Para concluir este tutorial, você precisa de uma assinatura do Azure. Se você não tiver [uma conta gratuita][], crie uma antes de começar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Instalar o [Visual Studio](https://www.visualstudio.com/). 
- Você precisa de uma conta do Power BI para analisar a saída de um trabalho do Stream Analytics. Você pode [experimentar o Power BI gratuitamente](https://app.powerbi.com/signupredirect?pbi_source=web).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-resources"></a>Configurar recursos

Para este tutorial, você precisa de um namespace dos Hubs de Eventos e um hub de eventos. Você pode criar esses recursos usando a CLI do Azure ou o Azure PowerShell. Use o mesmo grupo de recursos e o local para todos os recursos. Em seguida, no final, você pode remover tudo em uma única etapa, excluindo o grupo de recursos.

As seções a seguir descrevem como executar essas etapas necessárias. Execute a CLI *ou* as instruções do PowerShell para executar as seguintes etapas:

1. Crie um [grupo de recursos](../azure-resource-manager/management/overview.md). 

2. Crie um namespace dos Hubs de Eventos. 

3. Crie um hub de eventos.

> [!NOTE]
> Em cada script, há variáveis definidas das quais você precisará posteriormente no tutorial. Isso inclui o nome do grupo de recursos ($resourceGroup), o namespace do hub de eventos ( **$eventHubNamespace**) e o nome do hub de eventos ( **$eventHubName**). Eles são chamados com prefixos de sinal de cifrão ($) neste artigo para que você saiba que eles foram definidos no script.

<!-- some day they will approve the tab control; 
  When that happens, put CLI and PSH in tabs. -->

### <a name="set-up-your-resources-using-azure-cli"></a>Configurar os recursos usando a CLI do Azure

Copie e cole o script no Cloud Shell. Supondo que você já esteja conectado, ele executa o script uma linha de cada vez.

As variáveis que devem ser globalmente exclusivas têm `$RANDOM` concatenados a elas. Quando o script é executado e as variáveis são definidas, uma cadeia de caracteres numérica aleatória é gerada e concatenada ao final da cadeia de caracteres fixa, tornando-a exclusiva.

```azurecli-interactive
# Set the values for location and resource group name.
location=westus
resourceGroup=ContosoResourcesEH

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
eventHubNamespace=ContosoEHNamespace$RANDOM
echo "Event Hub Namespace = " $eventHubNamespace

# Create the Event Hubs namespace.
az eventhubs namespace create --resource-group $resourceGroup \
   --name $eventHubNamespace \
   --location $location \
   --sku Standard

# The event hub name must be globally unique, so add a random number to the end.
eventHubName=ContosoEHhub$RANDOM
echo "event hub name = " $eventHubName

# Create the event hub.
az eventhubs eventhub create --resource-group $resourceGroup \
    --namespace-name $eventHubNamespace \
    --name $eventHubName \
    --message-retention 3 \
    --partition-count 2

# Get the connection string that authenticates the app with the Event Hubs service.
connectionString=$(az eventhubs namespace authorization-rule keys list \
   --resource-group $resourceGroup \
   --namespace-name $eventHubNamespace \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString \
   --output tsv)
echo "Connection string = " $connectionString 
```

### <a name="set-up-your-resources-using-azure-powershell"></a>Configurar os recursos usando o Azure PowerShell

Copie e cole o script no Cloud Shell. Supondo que você já esteja conectado, ele executa o script uma linha de cada vez.

As variáveis que devem ser globalmente exclusivas têm `$(Get-Random)` concatenados a elas. Quando o script é executado e as variáveis são definidas, uma cadeia de caracteres numérica aleatória é gerada e concatenada ao final da cadeia de caracteres fixa, tornando-a exclusiva.

```azurepowershell-interactive
# Log in to Azure account.
Login-AzAccount

# Set the values for the location and resource group.
$location = "West US"
$resourceGroup = "ContosoResourcesEH"

# Create the resource group to be used  
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
$eventHubNamespace = "contosoEHNamespace$(Get-Random)"
Write-Host "Event Hub Namespace is " $eventHubNamespace

# The event hub name must be globally unique, so add a random number to the end.
$eventHubName = "contosoEHhub$(Get-Random)"
Write-Host "Event hub Name is " $eventHubName

# Create the Event Hubs namespace.
New-AzEventHubNamespace -ResourceGroupName $resourceGroup `
     -NamespaceName $eventHubNamespace `
     -Location $location

# Create the event hub.
$yourEventHub = New-AzEventHub -ResourceGroupName $resourceGroup `
    -NamespaceName $eventHubNamespace `
    -Name $eventHubName `
    -MessageRetentionInDays 3 `
    -PartitionCount 2

# Get the event hub key, and retrieve the connection string from that object.
# You need this to run the app that sends test messages to the event hub.
$eventHubKey = Get-AzEventHubKey -ResourceGroupName $resourceGroup `
    -Namespace $eventHubNamespace `
    -AuthorizationRuleName RootManageSharedAccessKey

# Save this value somewhere local for later use.
Write-Host "Connection string is " $eventHubKey.PrimaryConnectionString
```

## <a name="run-app-to-produce-test-event-data"></a>Execute o aplicativo para gerar dados de evento de teste

Os [exemplos no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet) dos Hubs de Eventos incluem um aplicativo Detector de Anomalias que gera dados de teste para você. Ele simula o uso de cartões de crédito gravando transações de cartão de crédito no hub de eventos e isso inclui a gravação ocasional de várias transações do mesmo cartão de crédito em vários locais, de modo que elas sejam marcadas como anomalias. Para executar esse aplicativo, siga estas etapas: 

1. Baixe os [exemplos dos Hubs de Eventos](https://github.com/Azure/azure-event-hubs/archive/master.zip) do GitHub e descompacte-os localmente.
2. Navegue até a pasta **\azure-event-hubs-master\samples\DotNet\\** . 
3. Mude para a pasta **Azure.Messaging.EventHubs\AnomalyDetector\\** e clique duas vezes em **AnomalyDetector.sln** para abrir a solução no Visual Studio. 

    Para usar a versão antiga do exemplo que usa o pacote antigo Microsoft.Azure.EventHubs, abra a solução na pasta **Microsoft.Azure.EventHubs\AnomalyDetector**. 
3. Abra Program.cs e substitua a **cadeia de conexão dos Hubs de Eventos** pela cadeia de conexão salva ao executar o script. 
4. Substitua **Nome dos Hub de Eventos** pelo nome de seu hub de eventos. Clique em F5 para executar o aplicativo. Ele começará a enviar eventos ao hub de eventos e continuará até enviar os 1000 eventos. Em alguns casos, o aplicativo precisa estar em execução para que você possa recuperar dados. Esses casos são indicados nas instruções a seguir, onde for necessário.

## <a name="set-up-azure-stream-analytics"></a>Configurar o Azure Stream Analytics

Agora você pode transmitir dados para seu hub de eventos. Para usar esses dados em uma visualização do Power BI, comece configurando um trabalho do Stream Analytics para recuperar os dados que, em seguida, serão enviados para a visualização do Power BI.

### <a name="create-the-stream-analytics-job"></a>Criar o trabalho do Stream Analytics

1. No portal do Azure, clique em **Criar um recurso**. Digite **stream analytics** na caixa de pesquisa e pressione **Enter**. Selecione **Trabalho do Stream Analytics**. Clique em **Criar** no painel de trabalho do Stream Analytics. 

2. Insira as seguintes informações para o trabalho:

   **Nome do trabalho**: Use **contosoEHjob**. Esse campo é o nome do trabalho e deve ser globalmente exclusivo.

   **Assinatura**: Selecione sua assinatura.

   **Grupo de recursos**: use o mesmo grupo de recursos usado pelo hub de eventos (**ContosoResourcesEH**).

   **Localização**: use a mesma localização usado no script de instalação (**Oeste dos EUA**).

   ![Captura de tela mostrando como criar um novo trabalho do Azure Stream Analytics.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-add-job.png)

    Aceite os padrões para o restante dos campos. Clique em **Criar**. 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada ao trabalho do Stream Analytics

Se você não estiver no portal, no painel **Trabalho do Stream Analytics**, poderá voltar ao trabalho do Stream Analytics clicando em **Grupos de Recursos** no portal e selecionando seu grupo de recursos ( **ContosoResourcesEH**). Essa ação mostra todos os recursos no grupo; selecione o trabalho do stream analytics. 

As entradas para o trabalho do Stream Analytics são as transações de cartão de crédito do hub de eventos.

> [!NOTE]
> Os valores das variáveis começando com o sinal de cifrão ($) são definidos nos scripts de inicialização das seções anteriores. Você deve usar os mesmos valores aqui ao especificar esses campos, que são o nome do hub de eventos e do namespace dos Hubs de Eventos.

1. Em **Topologia do Trabalho**, clique em **Entradas**.

2. No painel de **Entradas**, clique em **Adicionar entrada de fluxo** e selecione os Hubs de Eventos. Na tela que aparece, preencha os seguintes campos:

   **Alias de entrada**: Use **contosoinputs**. Esse campo é o nome do fluxo de entrada usado ao definir a consulta para os dados.

   **Assinatura**: Selecione sua assinatura.

   **Namespace do Hubs de Eventos**: Selecione seu namespace do Hub de eventos ($**eventHubNamespace**). 

   **Nome do Hub de Eventos**: clique em **Usar existente** e selecione seu hub de eventos ($**eventHubName**).

   **Nome da política de Hubs de Eventos**: Selecione **RootManageSharedAccessKey**.

   **Grupo de consumidores de Hubs de Eventos**: Deixe esse campo em branco para usar o grupo de consumidores padrão.

   Aceite os padrões para o restante dos campos.

   ![Captura de tela mostrando como adicionar um fluxo de entrada ao trabalho do Stream Analytics.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-inputs.png)

5. Clique em **Save** (Salvar).

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída ao trabalho do Stream Analytics

1. Em **Topologia do Trabalho**, clique em **Saídas**. Esse campo é o nome do fluxo de saída usado ao definir a consulta para os dados.

2. No painel **Saídas**, clique em **Adicionar** e, em seguida, selecione **Power BI**. Na tela que aparece, preencha os seguintes campos:

   **Alias de saída**: Use **contosooutputs**. Esse campo é o alias exclusivo da saída. 

   **Nome do conjunto de dados**: Use **contosoehdataset**. Esse campo é o nome do conjunto de dados a ser usado no Power BI. 

   **Nome da tabela**: Use **contosoehtable**. Esse campo é o nome da tabela a ser usada no Power BI. 

   Aceite os padrões para o restante dos campos.

   ![Captura de tela mostrando como configurar as saídas para um trabalho do Stream Analytics.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-outputs.png)

3. Clique em **Autorizar** e entre em sua conta do Power BI.

4. Aceite os padrões para o restante dos campos.

5. Clique em **Save** (Salvar).

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta do trabalho do Stream Analytics

Essa consulta é usada para recuperar os dados que, por fim, são enviados para a visualização do Power BI. Ela usa **contosoinputs** e **contosooutputs**, que você definiu anteriormente ao configurar o trabalho. Essa consulta recupera as transações de cartão de crédito consideradas fraudulentas, que são as transações nas quais o mesmo número de cartão de crédito tem várias transações em locais diferentes no mesmo intervalo de cinco segundos.

1. Em **Topologia do Trabalho**, clique em **Consulta**.

2. Substitua a consulta por esta: 

   ```SQL
   /* criteria for fraud:
      credit card purchases with the same card
      in different locations within 5 seconds
   */
   SELECT System.Timestamp AS WindowEnd, 
     COUNT(*) as FraudulentUses      
   INTO contosooutputs
   FROM contosoinputs CS1 TIMESTAMP BY [Timestamp]
       JOIN contosoinputs CS2 TIMESTAMP BY [Timestamp]
       /* where the credit card # is the same */
       ON CS1.CreditCardId = CS2.CreditCardId
       /* and time between the two is between 0 and 5 seconds */
       AND DATEDIFF(second, CS1, CS2) BETWEEN 0 AND 5
       /* where the location is different */
   WHERE CS1.Location != CS2.Location
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. Clique em **Save** (Salvar).

### <a name="test-the-query-for-the-stream-analytics-job"></a>Testar a consulta do trabalho do Stream Analytics 

1. Execute o aplicativo Detector de Anomalias para enviar dados para o hub de eventos enquanto estiver configurando e executando o teste. 

2. No painel Consulta, clique nos pontos ao lado da entrada **contosoinputs** e selecione **Dados de exemplo da entrada**.

3. Especifique que você deseja dados equivalentes a três minutos e clique em **OK**. Aguarde até ser notificado de que a amostragem dos dados foi realizada.

4. Clique em **Teste** e verifique se você está obtendo resultados. Os resultados são exibidos na seção **Resultados** do painel inferior à direita, embaixo da consulta.

5. Feche o painel de consulta.

### <a name="run-the-stream-analytics-job"></a>Executar o trabalho do Stream Analytics

No trabalho do Stream Analytics, clique em **Iniciar**, **Agora**, **Iniciar**. Depois que o trabalho é iniciado com êxito, o status do trabalho muda de **parado** para **executando**.

## <a name="set-up-the-power-bi-visualizations"></a>Configurar as visualizações do Power BI

1. Execute o aplicativo Detector de Anomalias para enviar dados para o hub de eventos enquanto estiver configurando a visualização do Power BI. Você precisará executá-lo várias vezes, já que ele gera apenas 1000 transações sempre que é executado.

2. Entre na sua conta do [Power BI](https://powerbi.microsoft.com/).

3. Ir para **Meu Workspace**.

4. Clique em **Conjuntos de dados**.

   Você deve ver o conjunto de dados especificado quando criou a saída para o trabalho do Stream Analytics (**contosoehdataset**). Pode levar de 5 a 10 minutos para que o conjunto de dados apareça pela primeira vez.

5. Clique em **Painéis**, clique em **Criar** e selecione **Painel**.

   ![Captura de tela dos botões Painéis e Criar.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-dashboard.png)

6. Especifique o nome do painel, clique em **Criar**. Use **Anomalias de Cartão de Crédito**.

   ![Captura de tela da especificação do nome do painel.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-name.png)

7. Na página Painel, clique em **Adicionar bloco**, selecione **Dados de Streaming Personalizados** na seção **DADOS EM TEMPO REAL** e clique em **Avançar**.

   ![Captura de tela da especificação da origem do bloco.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-real-time-data.png)

8. Selecione o conjunto de dados (**contosoehdataset**) e clique em **Avançar**.

   ![Captura de tela da especificação do conjunto de dados.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-select-dataset.png)

9. Selecione **Cartão** como tipo de visualização. Em **Campos**, clique em **Adicionar valor** e selecione `fraudulentuses`.

   ![Captura de tela da especificação de campos e tipo de visualização.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-tile.png)

   Clique em **Próximo**.

10. Defina o título como **Usos fraudulentos** e o subtítulo como **Soma nos últimos minutos**. Clique em **Aplicar**. Ele salva o bloco em seu painel.

    ![Captura de tela da especificação do título e do subtítulo do bloco do painel.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-tile-details.png)

    > [!IMPORTANT]
    > Quando você executa o aplicativo de exemplo e transmite dados para o hub de eventos, o número nesse bloco muda rapidamente (a cada segundo). Isso ocorre porque a consulta do Stream Analytics, na verdade, atualiza o valor a **cada segundo**. Atualize a consulta para uma janela em cascata de 3 minutos para ver a soma dos últimos minutos. 
11. Adicione outra visualização. Repita as primeiras etapas novamente:

    * Clique em **Adicionar Bloco**.
    * Selecione **Dados de Streaming Personalizados**. 
    * Clique em **Próximo**.
    * Selecione seu conjunto de dados e clique em **Avançar**. 

12. Em **Tipo de Visualização**, selecione **Gráfico de linhas**.

13. Em **Eixo**, clique em **Adicionar Valor** e selecione `windowend`. 

14. Em **Valores**, clique em **Adicionar valor** e selecione `fraudulentuses`.

15. Em **Janela de tempo para exibir**, selecione os últimos cinco minutos. Clique em **Próximo**.

16. Especifique **Mostrar uso fraudulento ao longo do tempo** como título e deixe o subtítulo em branco do bloco; em seguida, clique em **Aplicar**. Você retornará ao seu painel.

17. Execute o aplicativo Detector de Anomalias novamente para enviar alguns dados ao hub de eventos. Você vê o bloco **Usos fraudulentos** mudar quando ele analisa os dados e o gráfico de linhas mostra os dados. 

    ![Captura de tela mostrando os resultados do Power BI](./media/event-hubs-tutorial-visualize-anomalies/power-bi-results.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se você quiser remover todos os recursos criados, remova os dados de visualização do Power BI e exclua o grupo de recursos. A exclusão do grupo de recursos exclui todos os recursos contidos nele. Nesse caso, ele remove o hub de eventos, o namespace dos Hubs de Eventos, o trabalho do Stream Analytics e o próprio grupo de recursos. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Limpar os recursos na visualização do Power BI

Faça logon na sua conta do Power BI. Ir para **Meu Workspace**. Na linha com o nome do painel, clique no ícone de Lixeira. Em seguida, vá para **DataSets** e clique no ícone de Lixeira para excluir o conjunto de dados (**contosoehdataset**).

### <a name="clean-up-resources-using-azure-cli"></a>Limpar recursos usando a CLI do Azure

Para remover o grupo de recursos, use o comando [excluir grupo az](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="clean-up-resources-using-powershell"></a>Limpar recursos usando o PowerShell

Para remover o grupo de recursos, use o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:
> [!div class="checklist"]
> * Criar um namespace de Hubs de Eventos
> * Criar um Hub de Evento
> * Executar o aplicativo que simula a eventos e os envia ao hub de eventos
> * Configurar um trabalho do Stream Analytics para processar eventos enviados ao hub
> * Configurar uma visualização do Power BI para mostrar os resultados

Avance para o próximo artigo para saber mais sobre os Hubs de Eventos do Azure.

> [!div class="nextstepaction"]
> [Introdução ao envio de mensagens para os Hubs de Eventos do Azure no .NET Standard](event-hubs-dotnet-standard-getstarted-send.md)

[uma conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
