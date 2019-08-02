---
title: Ordenar eventos de conexão de dispositivo do Hub IoT usando o Azure Cosmos DB | Microsoft Docs
description: Este artigo descreve como ordenar e registrar eventos de conexão de dispositivo do Hub IoT usando o Azure Cosmos DB para manter o estado de conexão mais recente
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.openlocfilehash: a020221d841682d1e18d2b728a732ec4dfc35ef3
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67988295"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Ordenar eventos de conexão de dispositivo do Hub IoT usando o Azure Cosmos DB

A Grade de Eventos do Azure ajuda a criar aplicativos baseados em eventos e a integrar eventos de IoT facilmente nas suas soluções empresariais. Este artigo orienta você por uma configuração que pode ser usada para monitorar e armazenar o estado de conexão do dispositivo mais recente no Cosmos DB. Utilizaremos o número de sequência disponível nos eventos Dispositivo conectado e Dispositivo desconectado e armazenaremos o estado mais recente no Cosmos DB. Usaremos um procedimento armazenado, que é uma lógica de aplicativo executada em uma coleção no Cosmos DB.

O número de sequência é uma representação em cadeia de caracteres de um número hexadecimal. É possível usar a comparação de cadeia de caracteres para identificar o número maior. Se você estiver convertendo a cadeia de caracteres para hexadecimal, o número será um número de 256 bits. O número de sequência é estritamente crescente; portanto, o evento mais recente terá um número maior do que os outros eventos. Isso é útil caso você tenha conexões e desconexões de dispositivo frequentes e queira garantir que apenas o evento mais recente será usado para disparar uma ação downstream, pois a Grade de Eventos do Azure não oferece suporte à ordenação de eventos.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta ativa do Azure. Se você não tiver uma, poderá [criar uma conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

* Uma conta de API do SQL do Azure Cosmos DB. Se você ainda não criou uma, consulte [Criar uma conta de banco de dados](../cosmos-db/create-sql-api-java.md#create-a-database-account) para obter um passo a passo.

* Uma coleção no seu banco de dados. Consulte [Adicionar uma coleção](../cosmos-db/create-sql-api-java.md#add-a-container) para obter um passo a passo. Ao criar sua coleção, use `/id` para a chave de partição.

* Um Hub IoT no Azure. Se ainda não criou um, consulte a [Introdução ao Hub IoT](iot-hub-csharp-csharp-getstarted.md) para obter um passo a passo.

## <a name="create-a-stored-procedure"></a>Criar um procedimento armazenado

Primeiramente, crie um procedimento armazenado e o configure para executar uma lógica que compare os números de sequência dos próximos eventos e registre o evento mais recente por dispositivo no banco de dados.

1. Na sua API do SQL do Cosmos DB, selecione **Data Explorer** > **Itens** > **Novo procedimento armazenado**.

   ![Criar procedimento armazenado](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Insira **LatestDeviceConnectionState** para a ID do procedimento armazenado e cole o seguinte no **corpo do procedimento armazenado**. Esse código deve substituir o código existente no corpo do procedimento armazenado. Este código mantém uma linha por dispositivo e registra o estado de conexão mais recente dessa ID do dispositivo ao identificar o número de sequência mais elevado.

    ```javascript
    // SAMPLE STORED PROCEDURE
    function UpdateDevice(deviceId, moduleId, hubName, connectionState, connectionStateUpdatedTime, sequenceNumber) {
      var collection = getContext().getCollection();
      var response = {};

      var docLink = getDocumentLink(deviceId, moduleId);

      var isAccepted = collection.readDocument(docLink, function(err, doc) {
        if (err) {
          console.log('Cannot find device ' + docLink + ' - ');
          createDocument();
        } else {
          console.log('Document Found - ');
          replaceDocument(doc);
        }
      });

      function replaceDocument(document) {
        console.log(
          'Old Seq :' +
            document.sequenceNumber +
            ' New Seq: ' +
            sequenceNumber +
            ' - '
        );
        if (sequenceNumber > document.sequenceNumber) {
          document.connectionState = connectionState;
          document.connectionStateUpdatedTime = connectionStateUpdatedTime;
          document.sequenceNumber = sequenceNumber;

          console.log('replace doc - ');

          isAccepted = collection.replaceDocument(docLink, document, function(
            err,
            updated
          ) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(updated);
            }
          });
        } else {
          getContext()
            .getResponse()
            .setBody('Old Event - current: ' + document.sequenceNumber + ' Incoming: ' + sequenceNumber);
        }
      }
      function createDocument() {
        document = {
          id: deviceId + '-' + moduleId,
          deviceId: deviceId,
          moduleId: moduleId,
          hubName: hubName,
          connectionState: connectionState,
          connectionStateUpdatedTime: connectionStateUpdatedTime,
          sequenceNumber: sequenceNumber
        };
        console.log('Add new device - ' + collection.getAltLink());
        isAccepted = collection.createDocument(
          collection.getAltLink(),
          document,
          function(err, doc) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(doc);
            }
          }
        );
      }

      function getDocumentLink(deviceId, moduleId) {
        return collection.getAltLink() + '/docs/' + deviceId + '-' + moduleId;
      }
    }
    ```

3. Salve o procedimento armazenado:

    ![salvar procedimento armazenado](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Criar um aplicativo lógico

Primeiro, crie um aplicativo lógico e adicione um gatilho de Grade de eventos que monitora o grupo de recursos de sua máquina virtual.

### <a name="create-a-logic-app-resource"></a>Criar um recurso de aplicativo lógico

1. Na [portal do Azure](https://portal.azure.com), selecione **+ criar um recurso**, selecione **integração** e, em seguida, **aplicativo lógico**.

   ![Criar aplicativo lógico](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Nomeie seu aplicativo lógico que é exclusivo na sua assinatura e, em seguida, selecione a mesma assinatura, grupo de recursos e local como o Hub IoT.

   ![Novo aplicativo lógico](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Selecione **Criar** para criar o aplicativo lógico.

   Agora você criou um recurso do Azure para o seu aplicativo lógico. Depois que o Azure implanta o aplicativo lógico, o designer de aplicativos lógicos mostra modelos para padrões comuns para que você possa começar o quanto antes.

   > [!NOTE]
   > Para localizar e abrir seu aplicativo lógico novamente, selecione **grupos de recursos** e selecione o grupo de recursos que você está usando para este "como". Em seguida, selecione seu novo aplicativo lógico. Isso abre o designer do aplicativo lógico.

4. No designer do aplicativo lógico, role para a direita até ver os gatilhos comuns. Em **modelos**, escolha **aplicativo lógico em branco** para que você possa criar seu aplicativo lógico do zero.

### <a name="select-a-trigger"></a>Selecionar um gatilho

Um gatilho é um evento específico que inicia o aplicativo lógico. Para este tutorial, o gatilho que desencadeia o fluxo de trabalho está recebendo uma solicitação através HTTP.

1. Na barra de pesquisa conectores e gatilhos, digite **http** e pressione Enter.

2. Selecione **Solicitação - Quando uma solicitação HTTP é recebida** como o gatilho.

   ![Selecionar o gatilho de solicitação HTTP](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Selecione **Use o conteúdo de amostra para gerar o esquema**.

   ![Usar o payload de exemplo para gerar o esquema](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

4. Cole o código JSON de exemplo a seguir na caixa de texto e, em seguida, selecione **Concluído**:

   ```json
   [{
    "id": "fbfd8ee1-cf78-74c6-dbcf-e1c58638ccbd",
    "topic":
      "/SUBSCRIPTIONS/DEMO5CDD-8DAB-4CF4-9B2F-C22E8A755472/RESOURCEGROUPS/EGTESTRG/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/MYIOTHUB",
    "subject": "devices/Demo-Device-1",
    "eventType": "Microsoft.Devices.DeviceConnected",
    "eventTime": "2018-07-03T23:20:11.6921933+00:00",
    "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
      "hubName": "MYIOTHUB",
      "deviceId": "48e44e11-1437-4907-83b1-4a8d7e89859e",
      "moduleId": ""
    },
    "dataVersion": "1",
    "metadataVersion": "1"
   }]
   ```

   ![Colar conteúdo JSON de exemplo](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. É possível que você receba uma notificação pop-up informando: **Lembre-se de incluir um cabeçalho Content-Type definido como application/json na sua solicitação.** Você pode ignorar essa sugestão com segurança e passar para a próxima seção.

### <a name="create-a-condition"></a>Criar uma condição

No seu fluxo de trabalho de aplicativo lógico, as condições ajudam a executar ações específicas depois de passar uma condição específica. Após o cumprimento da condição, uma ação desejada pode ser definida. Para este tutorial, a condição é verificar se o eventType é dispositivo conectado ou dispositivo desconectado. A ação será executar o procedimento armazenado no seu banco de dados.

1. Selecione **+ nova etapa** e, em seguida, **interna**, localize e selecione **condição**. Clique em **escolher um valor** e uma caixa será exibida mostrando o conteúdo dinâmico – os campos que podem ser selecionados. Preencha os campos conforme mostrado abaixo para executá-los somente para eventos conectados ao dispositivo e dispositivos desconectados:

   * Escolha um valor: **EventType** – selecione-o nos campos no conteúdo dinâmico que aparecem quando você clica nesse campo.
   * Altere "é igual a" para **terminar com**.
   * Escolha um valor: **nected**.

     ![Preencher condição](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. Na caixa de diálogo **se verdadeiro** , clique em **Adicionar uma ação**.
  
   ![Adicionar ação se for verdadeira](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Procure Cosmos DB e selecione o **procedimento armazenado Azure Cosmos DB-execute**

   ![Pesquisar o CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Preencha **cosmosdb-Connection** para o **nome da conexão** e selecione a entrada na tabela e, em seguida, selecione **criar**. Você verá o painel **Executar procedimento armazenado** . Insira os valores para os campos:

   **ID do banco de dados**: ToDoList

   **ID da coleção**: Itens

   **ID do SPROC**: LatestDeviceConnectionState

5. Selecione **Adicionar novo parâmetro**. No menu suspenso que aparece, marque as caixas ao lado de **chave de partição** e **parâmetros para o procedimento armazenado**e clique em qualquer outro lugar na tela; Ele adiciona um campo para o valor de chave de partição e um campo para parâmetros para o procedimento armazenado.

   ![preencher ação do aplicativo lógico](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Agora, insira o valor e os parâmetros da chave de partição, conforme mostrado abaixo. Certifique-se de colocar entre colchetes e aspas duplas, conforme mostrado. Talvez seja necessário clicar em **adicionar conteúdo dinâmico** para obter os valores válidos que você pode usar aqui.

   ![preencher ação do aplicativo lógico](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. Na parte superior do painel em que ele diz **para cada**um, em **selecionar uma saída de etapas anteriores**, verifique se o **corpo** está selecionado.

   ![popular aplicativo lógico para-cada](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Salve seu aplicativo lógico.

### <a name="copy-the-http-url"></a>Copiar a URL HTTP

Antes de sair do Designer de Aplicativos Lógicos, copie a URL que o aplicativo lógico está ouvindo para um gatilho. Você usa essa URL para configurar a Grade de Eventos.

1. Expanda a caixa de configuração do gatilho **Quando uma solicitação HTTP for recebida**, clicando nela.

2. Copie o valor de **URL DE HTTP POST**, selecionando o botão de cópia ao lado dele.

   ![Copiar a URL DE HTTP POST](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Salve essa URL para que você possa referir-se a ela na próxima seção.

## <a name="configure-subscription-for-iot-hub-events"></a>Configurar a assinatura de eventos do Hub IoT

Nesta seção, você configura o Hub IoT para publicar eventos à medida que ocorrem.

1. No portal do Azure, navegue para o hub IoT.

2. Selecione **Eventos**.

   ![Abra os detalhes da Grade de Eventos](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Selecione **+ assinatura de evento**.

   ![Criar nova assinatura de evento](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Preencha os **detalhes da assinatura do evento**: Forneça um nome descritivo e selecione **Esquema da Grade de Eventos**.

5. Preencha os campos de **tipos de evento** . Na lista suspensa, selecione somente **dispositivo conectado** e **dispositivo desconectado** do menu. Clique em qualquer outro lugar na tela para fechar a lista e salvar suas seleções.

   ![Definir tipos de eventos a serem procurados](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. Para obter **detalhes do ponto de extremidade**, selecione tipo de ponto de extremidade como **gancho da Web** e clique em selecionar ponto de extremidade e cole a URL que você copiou do aplicativo lógico e confirme a seleção.

   ![Selecionar URL do ponto de extremidade](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. O formulário agora deve ser semelhante ao exemplo a seguir:

   ![Exemplo de formulário de inscrição de evento](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Selecione **Criar** para salvar a assinatura de evento.

## <a name="observe-events"></a>Observar eventos

Como a assinatura de evento já foi configurada, vamos testar conectando um dispositivo.

### <a name="register-a-device-in-iot-hub"></a>Registrar um dispositivo no Hub IoT

1. Do seu Hub IoT, selecione **Dispositivos IoT**.

2. Selecione **+ Adicionar** na parte superior do painel.

3. Para**IID do dispositivo**, insira `Demo-Device-1`.

4. Clique em **Salvar**.

5. É possível adicionar vários dispositivos com diferentes IDs de dispositivo.

   ![Dispositivos adicionados ao Hub](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Clique no dispositivo novamente; Agora, as cadeias de conexão e as chaves serão preenchidas. Copie a **Cadeia de conexão – chave primária** para uso futuro.

   ![ConnectionString para dispositivo](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Iniciar o simulador do Raspberry Pi

Vamos usar o simulador Web do Raspberry Pi para simular a conexão do dispositivo.

[Iniciar o simulador do Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Executar um aplicativo de exemplo no simulador Web do Raspberry Pi

Isso disparará um evento de dispositivo conectado.

1. Na área codificação, substitua o espaço reservado na linha 15 pela cadeia de conexão do dispositivo do Hub IoT do Azure que você salvou no final da seção anterior.

   ![Colar na cadeia de conexão do dispositivo](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Execute o aplicativo selecionando **executar**.

Você verá algo semelhante à saída a seguir que mostra os dados do sensor e as mensagens que são enviadas para o Hub IoT.

   ![Executando o aplicativo](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Clique em **Parar** para parar o simulador e disparar um evento de **Dispositivo desconectado**.

Você executou um aplicativo de exemplo para coletar dados de sensor e enviá-los ao seu Hub IoT.

### <a name="observe-events-in-cosmos-db"></a>Observar eventos no Cosmos DB

É possível ver os resultados do procedimento armazenado executado no seu documento do Cosmos DB. Eles são parecidos com isto. Cada linha contém o estado de conexão do dispositivo mais recente por dispositivo.

   ![Como produzir um resultado](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure

Em vez de usar o [portal do Azure](https://portal.azure.com), é possível realizar as etapas do Hub IoT usando a CLI do Azure. Para obter detalhes, consulte as páginas da CLI do Azure para [criar uma assinatura de evento](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) e [criar um dispositivo IoT](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create).

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial usou recursos que incorrem em encargos na sua assinatura do Azure. Quando você terminar de experimentar o tutorial e testar os resultados, desabilite ou exclua os recursos que não deseja manter.

Se você não quiser perder o trabalho no seu aplicativo lógico, desabilite-o em vez de excluí-lo.

1. Navegue até seu aplicativo lógico.

2. Na folha **Visão geral**, selecione **Excluir** ou **Desabilitar**.

    Cada assinatura pode ter um Hub IoT gratuito. Se você criou um Hub gratuito para este tutorial, não será necessário excluí-lo para evitar encargos.

3. Navegue até o seu Hub IoT.

4. Na folha **Visão geral**, selecione **Excluir**.

    Mesmo se você mantiver o Hub IoT, convém excluir a assinatura de evento que você criou.

5. No seu Hub IoT, selecione **Grade de Eventos**.

6. Selecione a assinatura de evento que deseja remover.

7. Selecione **Excluir**.

Para remover uma conta do Azure Cosmos DB no portal do Azure, clique com o botão direito do mouse no nome da conta e clique em **Excluir conta**. Consulte as instruções detalhadas para [excluir uma conta do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/manage-account).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Reagir aos eventos do Hub IoT usando a Grade de Eventos para disparar ações](../iot-hub/iot-hub-event-grid.md)

* [Tente o tutorial de eventos do Hub IoT](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Saiba mais sobre o que mais você pode fazer com [Grade de Eventos](../event-grid/overview.md)