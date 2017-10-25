## <a name="create-an-iot-hub"></a>Crie um hub IoT
Crie um Hub IoT ao qual o aplicativo de dispositivo simulado poderá se conectar. As etapas a seguir mostram como concluir esta tarefa usando o portal do Azure.

1. Entre no [Portal do Azure][lnk-portal].
1. Na Barra de Navegação, clique em **Novo** > **Internet das Coisas** > **Hub IoT**.
   
    ![Barra de Navegação do portal do Azure][1]
1. Na janela **Hub IoT**, escolha a configuração para o Hub IoT.
   
    ![Janela do Hub IoT][2]
   
   1. Na caixa **Nome** , insira um nome para identificar seu hub IoT. Se o **Nome** for válido e estiver disponível, aparecerá uma marca de seleção verde na caixa **Nome**.
    [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]
   
   1. Selecione um [tipo de preço e de dimensionamento][lnk-pricing]. Este tutorial não requer uma camada específica. Para este tutorial, use a camada F1 gratuita.
   1. Em **Grupo de recursos**, crie um grupo de recursos ou selecione um existente. Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure][lnk-resource-groups].
   1. Em **Local**, selecione o local para hospedar o hub IoT. Para este tutorial, escolha o local mais próximo.
1. Quando você tiver escolhido as opções de configuração do hub IoT, clique em **Criar**.  O Azure poderá demorar alguns minutos para criar seu hub IoT. Para verificar o status, você pode monitorar o progresso no quadro inicial ou no painel Notificações.
   
1. Quando o Hub IoT tiver sido criado com êxito, clique no novo bloco para o Hub IoT no Portal do Azure para abrir a janela de propriedades do novo Hub IoT. Anote o **Nome do host** e clique em **Políticas de acesso compartilhado**.
   
    ![Nova janela do Hub IoT][4]
1. Em **Políticas de acesso compartilhado**, clique na política **iothubowner** e copie e anote a cadeia de conexão do Hub IoT na janela **iothubowner**. Para saber mais, confira [Controle de acesso][lnk-access-control] no "Guia do desenvolvedor do Hub IoT".
   
    ![Políticas de acesso compartilhado][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
