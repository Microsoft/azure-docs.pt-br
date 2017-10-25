## <a name="create-an-iot-hub"></a>Crie um hub IoT

1. No [portal do Azure](https://portal.azure.com/), clique em **Novo** > **Internet das Coisas** > **Hub IoT**.

   ![Criar um hub IoT no portal do Azure](../articles/iot-hub/media/iot-hub-create-hub-and-device/1_create-azure-iot-hub-portal.png)
2. No painel **Hub IoT**, insira as seguintes informações para o Hub IoT:

     **Nome**: insira o nome do hub IoT. Se o nome for válido, uma marca de seleção verde é exibida.

     **Tipo de escala e preço**: selecione a camada **F1 - Gratuita**. Essa opção é suficiente para esta demonstração. Para saber mais, confira [Tipo de escala e preço](https://azure.microsoft.com/pricing/details/iot-hub/).

     **Grupo de recursos**: criar um grupo de recursos para hospedar o hub IoT ou usar um existente. Para saber mais, veja [Usar os grupos de recursos para gerenciar seus recursos do Azure](../articles/azure-resource-manager/resource-group-portal.md).

     **Local**: selecione o local mais próximo a você onde o hub IoT é criado.

     **Fixar no painel**: selecione essa opção para ter fácil acesso ao Hub IoT no painel.

   ![Inserir informações para criar o hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/2_fill-in-fields-for-azure-iot-hub-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

3. Clique em **Criar**. A criação do seu hub IoT pode levar alguns minutos. Você pode ver o progresso no **notificações** painel.

   ![Ver as notificações de andamento para o hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/3_notification-azure-iot-hub-creation-progress-portal.png)

4. Depois de criar o hub IoT, clique nele no painel. Anote o **Nome do host** e clique em **Políticas de acesso compartilhado**.

   ![Obter nome de host do seu Hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

5. No painel **Políticas de acesso compartilhado**, clique na política **iothubowner**, em seguida, copie e anote a **Cadeia de caracteres de conexão** de seu Hub IoT. Para saber mais, veja [Controlar o acesso ao Hub IoT](../articles/iot-hub/iot-hub-devguide-security.md).

> [!NOTE] 
Você não precisará dessa cadeia de conexão iothubowner para este tutorial de configuração. No entanto, talvez você precise dela para alguns tutoriais em cenários de IoT diferentes após a conclusão desta instalação.

   ![Obter sua cadeia de conexão do Hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Registrar um dispositivo no hub IoT para o dispositivo

1. No [portal do Azure](https://portal.azure.com/), abra o hub IoT.

2. Clique em **Gerenciador de Dispositivos**.
3. No painel Gerenciador de Dispositivo, clique em **Adicionar** para adicionar um dispositivo ao Hub IoT. Faremos o seguinte:

   **ID do Dispositivo**: insira a ID do novo dispositivo. As IDs de Dispositivo diferenciam maiúsculas de minúsculas.

   **Tipo de Autenticação**: selecione a **Chave Simétrica**.

   **Gerar Chaves Automaticamente**: marque essa caixa de seleção.

   **Conectar o dispositivo ao Hub IoT**: clique em **Habilitar**.

   ![Adicionar um dispositivo no Device Explorer de seu hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-device-explorer-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Clique em **Salvar**.
5. Depois do dispositivo ser criado, abra-o no painel **Gerenciador de Dispositivos**.
6. Anote a chave primária da cadeia de conexão.

   ![Obter a cadeia de conexão do dispositivo](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_get-device-connection-string-in-device-explorer-portal.png)
