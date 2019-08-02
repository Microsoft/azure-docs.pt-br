---
title: Exibir resultados de roteamento de mensagens do Hub IoT do Azure (.NET) | Microsoft Docs
description: Exibir resultados de roteamento de mensagens do Hub IoT do Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: f34799bbf2142ba07c29915deae5b5dbe590c9fc
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330553"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Tutorial: Parte 2 – Exibir as mensagens roteadas

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Regras para roteamento de mensagens

Estas são as regras para o roteamento de mensagens; elas foram configuradas na parte 1 deste tutorial e você as vê funcionar nesta segunda parte.

|value |Result|
|------|------|
|level="storage" |Grave no Armazenamento do Azure.|
|level="critical" |Grave em uma fila do Barramento de Serviço. Um aplicativo lógico recupera a mensagem da fila e usa o Office 365 para enviar a mensagem por e-mail.|
|padrão |Exiba esses dados usando o Power BI.|

Agora você pode criar os recursos para os quais as mensagens serão roteadas, executar um aplicativo para enviar mensagens ao hub e ver o roteamento em ação.

## <a name="create-a-logic-app"></a>Criar um aplicativo lógico  

É a fila do Barramento de Serviço a ser usado para receber mensagens designadas como críticas. Configure um aplicativo lógico para monitorar a fila do Barramento de Serviço e enviar um e-mail quando uma mensagem é adicionada à fila.

1. No [portal do Azure](https://portal.azure.com), selecione **+ Criar um recurso**. Coloque o **aplicativo lógico** na caixa de pesquisa e clique em Enter. Nos resultados da pesquisa exibidos, selecione o aplicativo lógico e selecione **Criar** para continuar para o painel **Criar aplicativo lógico**. Preencha os campos.

   **Nome**: este campo é o nome do aplicativo lógico. Este tutorial usa **ContosoLogicApp**.

   **Assinatura**: Selecione sua assinatura do Azure.

   **Grupo de recursos**: Selecione **Usar existente** e selecione seu grupo de recursos. Este tutorial usa **ContosoResources**.

   **Localização**: use sua localização. Este tutorial usa **Oeste dos EUA**.

   **Log Analytics**: este controle de alternância deve ser desativado.

   ![A tela Criar Aplicativo Lógico](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Selecione **Criar**.

2. Agora vá para o aplicativo lógico. A maneira mais fácil de obter o aplicativo lógico é selecionar **Grupos de recursos**, selecionar o grupo de recursos (este tutorial usa **ContosoResources**) e selecionar o Aplicativo Lógico da lista de recursos. É exibida a página do Designer de Aplicativos Lógicos (talvez seja necessário rolar para a direita para ver a página inteira). Na página Designer de Aplicativos Lógicos, role para baixo até ver o bloco que diz **Aplicativo Lógico em Branco +** e selecione-o. A guia padrão é "Para Você". Se esse painel estiver em branco, selecione **Todos** para ver todos os conectores e gatilhos disponíveis.

3. Selecione **Barramento de Serviço** na lista de conectores.

   ![A lista de conectores](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. Uma lista de gatilhos é exibida. Selecione **Quando uma mensagem é recebida em uma fila (preenchimento automático)/Barramento de Serviço**.

   ![A lista de gatilhos para o Barramento de Serviço](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. Na próxima tela, preencha o nome de Conexão. Este tutorial usa **ContosoConnection**.

   ![Configurando a conexão para a fila do Barramento de Serviço](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Selecione o namespace do Barramento de Serviço. Este tutorial usa **ContosoSBNamespace**. Quando você seleciona o namespace, o portal consulta o namespace do Barramento de Serviço para recuperar as chaves. Selecione **RootManageSharedAccessKey** e selecione **Criar**.

   ![Concluindo a configuração de conexão](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. Na próxima tela, selecione o nome da fila (este tutorial usa **contososbqueue**) na lista suspensa. Você pode usar os padrões para o restante dos campos.

   ![As opções de fila](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Configure agora a ação para enviar um e-mail quando uma mensagem é recebida na fila. No Designer de Aplicativos Lógicos, selecione **+ Nova Etapa** para adicionar uma etapa e, em seguida, selecione **Todos** para ver todas as opções disponíveis. No painel **Escolher uma ação**, localize e selecione **Office 365 Outlook**. Na tela de gatilhos, selecione **Enviar um email/Office 365 Outlook**.  

   ![As opções do Office365](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Entre em sua conta do Office 365 para configurar a conexão. Se atingir o tempo limite, tente novamente. Especifique os endereços de e-mail para os destinatários dos e-mails. Também especifique o assunto e digite a mensagem de que você gostaria que o destinatário visse no corpo da mensagem. Para testar, preencha seu próprio endereço de e-mail como o destinatário.

   Selecione **Adicionar conteúdo dinâmico** para mostrar o conteúdo da mensagem que você pode incluir. Selecione **Conteúdo** – ele incluirá a mensagem no e-mail.

   ![As opções de email para o aplicativo lógico](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Clique em **Salvar**. Em seguida, feche o Designer de Aplicativo Lógico.

## <a name="set-up-azure-stream-analytics"></a>Configurar o Azure Stream Analytics

Para ver os dados em uma visualização do Power BI, primeiro configure um trabalho do Stream Analytics para recuperar os dados. Lembre-se de que apenas as mensagens onde o **nível** é **normal** são enviadas ao ponto de extremidade padrão e serão recuperadas pelo trabalho do Stream Analytics para a visualização do Power BI.

### <a name="create-the-stream-analytics-job"></a>Criar o trabalho do Stream Analytics

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Internet das Coisas** > **Trabalho do Stream Analytics**.

2. Insira as seguintes informações para o trabalho.

   **Nome do trabalho**: O nome do trabalho. O nome deve ser globalmente exclusivo. Este tutorial usa **contosoJob**.

   **Assinatura**: A assinatura do Azure que você está usando para o tutorial.

   **Grupo de recursos**: use o mesmo grupo de recursos usado pelo seu Hub IoT. Este tutorial usa **ContosoResources**.

   **Localização**: use a mesma localização usada no script de instalação. Este tutorial usa **Oeste dos EUA**.

   ![Criar o trabalho do Stream Analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. Selecione **Criar** para criar o trabalho. Para voltar ao trabalho, selecione **Grupos de recursos**. Este tutorial usa **ContosoResources**. Selecione o grupo de recursos, depois selecione o trabalho do Stream Analytics na lista de recursos.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada ao trabalho do Stream Analytics

4. Em **Topologia do Trabalho**, selecione **Entradas**.

5. No painel **Entradas**, selecione **Adicionar entrada de fluxo** e selecione o Hub IoT. Na tela que aparece, preencha os seguintes campos:

   **Alias de entrada**: Este tutorial usa **contosoinputs**.

   **Selecione o Hub IoT de sua assinatura**: Selecione essa opção de botão de opção.

   **Assinatura**: Selecione a assinatura do Azure que você está usando para este tutorial.

   **Hub IoT**: selecione o Hub IoT. Este tutorial usa **ContosoTestHub**.

   **Ponto de extremidade**: selecione **Mensagens**. (Se você selecionar Monitoramento de Operações, você obtém dados de telemetria sobre o Hub IoT em lugar dos dados que você está enviando.) 

   **Nome da política de acesso compartilhado**: Selecione **serviço**. O portal preenche a chave de política de acesso compartilhado para você.

   **Grupo de consumidores**: Selecione o grupo de consumidores configurado na etapa 1 deste tutorial. Este tutorial usa **contosoconsumers**.
   
   Para o restante dos campos, aceite os padrões. 

   ![Configurar as entradas para o trabalho do Stream Analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

6. Clique em **Salvar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída ao trabalho do Stream Analytics

1. Em **Topologia do Trabalho**, selecione **Saídas**.

2. No painel **Saídas**, selecione **Adicionar** e, em seguida, **Power BI**. Na tela que aparece, preencha os seguintes campos:

   **Alias de saída**: o alias exclusivo para a saída. Este tutorial usa **contosooutputs**. 

   **Nome do conjunto de dados**: nome do conjunto de dados a ser usado no Power BI. Este tutorial usa **contosodataset**. 

   **Nome da tabela**: nome da tabela a ser usada no Power BI. Este tutorial usa **contosotable**.

   Aceite os padrões para o restante dos campos.

3. Selecione **Autorizar** e entre em sua conta do Power BI. (Isso pode exigir mais de uma tentativa.)

   ![Configurar as saídas para o trabalho do Stream Analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Clique em **Salvar**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta do trabalho do Stream Analytics

1. Em **Topologia do trabalho**, selecione **Consulta**.

2. Substitua `[YourInputAlias]` pelo alias de entrada do trabalho. Este tutorial usa **contosoinputs**.

3. Substitua `[YourOutputAlias]` pelo alias de saída do trabalho. Este tutorial usa **contosooutputs**.

   ![Configurar a consulta para o trabalho do Stream Analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Clique em **Salvar**.

5. Feche o painel de consulta. Você retorna para a exibição dos recursos no Grupo de Recursos. Selecione o trabalho do Stream Analytics. Este tutorial o chama de **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Executar o trabalho do Stream Analytics

No trabalho do Stream Analytics, selecione **Iniciar** > **Agora** > **Iniciar**. Depois que o trabalho é iniciado com êxito, o status do trabalho muda de **parado** para **executando**.

Para configurar o relatório do Power BI, você precisa de dados, portanto você configurará o Power BI depois de criar o dispositivo e executar o aplicativo de simulação do dispositivo.

## <a name="run-simulated-device-app"></a>Executar aplicativo de dispositivo simulado

Na parte 1 deste tutorial, você configurou um dispositivo para simular o uso de um dispositivo de IoT. Nesta seção, você baixa o aplicativo de console do .NET que simula aquele dispositivo enviando mensagens de dispositivo para nuvem para um hub IoT, supondo que você ainda não tenha baixado o aplicativo e os recursos na parte 1 deste tutorial.

Este aplicativo envia mensagens para cada um dos diferentes métodos de roteamentos de mensagens. Há também uma pasta no download que contém o arquivo de parâmetros e o modelo completo do Azure Resource Manager, bem como os scripts do PowerShell e da CLI do Azure.

Se você não baixou os arquivos do repositório na etapa 1 deste tutorial, vá em frente e baixe-os agora em [Simulação de Dispositivo IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Selecionar este link baixa um repositório contendo vários aplicativos; a solução que você está procurando está em iot-hub/Tutorials/Routing/IoT_SimulatedDevice.sln. 

Clique duas vezes no arquivo de solução (IoT_SimulatedDevice.sln) para abrir o código no Visual Studio, em seguida, abra o Program.cs. Substitua `{iot hub hostname}` pelo nome de host do Hub IoT. O formato de nome de host do hub IoT é **{iot-hub-name}.azure-devices.net**. Para este tutorial, o nome de host do hub é **ContosoTestHub.azure-devices.net**. Em seguida, substitua `{device key}` com a chave do dispositivo salvo anteriormente durante a configuração do dispositivo simulado. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Executar e testar

Execute o aplicativo de console. Aguarde alguns minutos. Você pode ver as mensagens sendo enviadas na tela do aplicativo de console.

Esse aplicativo envia uma nova mensagem de dispositivo para a nuvem ao Hub IoT a cada segundo. A mensagem contém um objeto serializado para JSON com a ID do dispositivo, temperatura, umidade e nível de mensagem, cujo padrão é `normal`. Atribui aleatoriamente um nível de `critical` ou `storage`, fazendo com que a mensagem deve ser roteada para a conta de armazenamento ou para a fila do Barramento de Serviço (que dispara seu Aplicativo Lógico para enviar um e-mail). As leituras (`normal`) padrão serão exibidas no relatório de BI que você configurar a seguir.

Se tudo estiver configurado corretamente, agora você verá os seguintes resultados:

1. Você começa a obter e-mails sobre mensagens críticas.

   ![Os emails resultantes](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   Esse resultado significa que as instruções a seguir são verdadeiras. 

   * O roteamento para a fila do Barramento de Serviço está funcionando corretamente.
   * O Aplicativo Lógico recuperar a mensagem da fila de Barramento de Serviço está funcionando corretamente.
   * O conector de Aplicativo Lógico para o Outlook está funcionando corretamente. 

2. No [portal do Azure](https://portal.azure.com), selecione **Grupos de recursos** e selecione seu Grupo de Recursos. Este tutorial usa **ContosoResources**. Selecione a conta de armazenamento, clique em **Blobs** e selecione o Contêiner. Este tutorial usa **contosoresults**. Você deve ver uma pasta, e você pode fazer drill down em diretórios até que você veja um ou mais arquivos. Abrir um desses arquivos; elas contêm as entradas roteadas para a conta de armazenamento. 

   ![Os arquivos de resultados no armazenamento](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Esse resultado significa que a instrução a seguir é verdadeira.

   * O roteamento para a conta de armazenamento está funcionando corretamente.

Agora, com o aplicativo ainda em execução, configure a visualização do Power BI para ver as mensagens recebidas por meio de roteamento padrão.

## <a name="set-up-the-power-bi-visualizations"></a>Configurar as visualizações do Power BI

1. Entre na sua conta do [Power BI](https://powerbi.microsoft.com/).

2. Vá para os **Workspaces** e selecione o workspace que você definiu quando criou a saída para o trabalho do Stream Analytics. Este tutorial usa **Meu Workspace**. 

3. Selecione **Conjuntos de dados**. Se você não tiver um conjunto de dados, aguarde alguns minutos e verifique novamente.

   Você deve ver o conjunto de dados relacionado que você especificou quando criou a saída para o trabalho do Stream Analytics. Este tutorial usa **contosodataset**. (Ele pode levar de 5 a 10 minutos para aparecer o conjunto de dados pela primeira vez.)

4. Em **AÇÕES**, selecione o primeiro ícone para criar um relatório.

   ![Workspace do Power BI com ícone de Ações e relatório realçado](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Crie um gráfico de linhas para mostrar a temperatura em tempo real ao longo do tempo.

   * Na página de criação de relatório, adicione um gráfico de linha selecionando o ícone de gráfico de linha.

     ![As visualizações e os campos](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * Sobre o **campos** painel, expanda a tabela que você especificou quando criou a saída para o trabalho do Stream Analytics. Este tutorial usa **contosotable**.

   * Arraste **EventEnqueuedUtcTime** para **eixo** sobre o **visualizações** painel.

   * Arraste **temperatura** para **Valores**.

   Um gráfico de linhas é criado. O eixo x exibe a data e a hora no fuso horário UTC. O eixo y mostra a temperatura do sensor.

6. Crie outro gráfico de linhas para mostrar umidade em tempo real ao longo do tempo. Para configurar o segundo gráfico, siga as mesmas etapas acima e coloque **EventEnqueuedUtcTime** no eixo x e **umidade** no eixo y.

   ![O relatório final do Power BI com os dois gráficos](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Selecione **Salvar** para salvar o relatório.

Você deve ser capaz de visualizar dados em ambos os gráficos. Esse resultado significa que as instruções a seguir são verdadeiras:

   * O roteamento para o ponto de extremidade padrão está funcionando corretamente.
   * O trabalho do Azure Stream Analytics está transmitindo corretamente.
   * A visualização do Power BI está configurada corretamente.

Você pode atualizar os gráficos para ver os dados mais recentes, selecionando o botão Atualizar na parte superior da janela do Power BI. 

## <a name="clean-up-resources"></a>Limpar recursos 

Se você quiser remover todos os recursos criados por meio de ambas as partes deste tutorial, exclua o grupo de recursos. Essa ação também exclui todos os recursos contidos no grupo. Nesse caso, ele remove o Hub IoT, a fila e namespace do Barramento de Serviço, o aplicativo lógico, a conta de armazenamento e o próprio grupo de recursos. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Limpar os recursos na visualização do Power BI

Entre na sua conta do [Power BI](https://powerbi.microsoft.com/). Vá até seu workspace. Este tutorial usa **Meu Workspace**. Para remover a visualização do Power BI, acesse Conjuntos de Dados e selecione o ícone de Lixeira para excluir o conjunto de dados. Este tutorial usa **contosodataset**. Quando você remove o conjunto de dados, o relatório também será removido.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Usar a CLI do Azure para limpar os recursos

Para remover o grupo de recursos, use o comando [excluir grupo az](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` foi definido como **ContosoResources** novamente no início deste tutorial.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Usar o PowerShell para limpar os recursos

Para remover o grupo de recursos, use o comando [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup). `$resourceGroup` foi definido como **ContosoResources** novamente no início deste tutorial.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial de duas partes, você aprendeu a usar o roteamento de mensagens para rotear mensagens do Hub IoT para diferentes destinos executando as seguintes tarefas.  

**Parte I: Criar recursos, configurar o roteamento de mensagens**
> [!div class="checklist"]
> * Crie os recursos – um hub IoT, uma conta de armazenamento, uma fila do Barramento de Serviço e um dispositivo simulado.
> * Configure os pontos de extremidade e as rotas de mensagem no Hub IoT para a conta de armazenamento e a fila do Barramento de Serviço.

**Parte II: Enviar mensagens ao hub, exibir resultados roteados**
> [!div class="checklist"]
> * Crie um aplicativo lógico que é disparado e envia e-mail quando uma mensagem é adicionada à fila de Barramento de Serviço.
> * Baixe e execute um aplicativo que simula um dispositivo IoT enviando mensagens para o hub para as diferentes opções de roteamentos.
> * Crie uma visualização do Power BI para os dados enviados para o ponto de extremidade padrão.
> * Exiba os resultados...
> * ... na fila do Barramento de Serviço e emails.
> * ... na conta de armazenamento.
> * ...na visualização do Power BI.

Avance para o próximo tutorial para aprender a gerenciar o estado de um dispositivo IoT. 

> [!div class="nextstepaction"]
> [Configurar e usar métricas e diagnósticos com um Hub IoT](tutorial-use-metrics-and-diags.md)
