## <a name="view-the-solution-dashboard"></a>Exibir o painel de solução

O painel de solução permite que você gerencie a solução implantada. Por exemplo, você pode exibir telemetria, adicionar dispositivos e invocar métodos.

1. Quando o provisionamento for concluído e o bloco da solução pré-configurada indicar **Pronto**, escolha **Iniciar** para abrir o portal de solução de monitoramento remoto em uma nova guia.

    ![Iniciar a solução pré-configurada][img-launch-solution]

1. Por padrão, o portal de solução mostra o *painel*. Você pode navegar para outras áreas do portal de solução usando o menu ao lado esquerdo da página.

    ![Painel de solução pré-configurada de monitoramento remoto][img-menu]

## <a name="add-a-device"></a>Adicionar um dispositivo

Para um dispositivo conectar-se à solução pré-configurada, ele deve identificar-se no Hub IoT usando credenciais válidas. Você pode recuperar as credenciais do dispositivo no painel da solução. Você pode incluir as credenciais do dispositivo em seu aplicativo cliente posteriormente neste tutorial.

Se você ainda não fez isso, adicione um dispositivo personalizado à sua solução de monitoramento remota. Conclua as etapas a seguir no painel de solução:

1. No canto inferior esquerdo do painel, clique em **Adicionar um dispositivo**.

   ![Adicionar um dispositivo][1]

1. No painel **Dispositivo Personalizado**, clique em **Adicionar novo**.

   ![Adicionar um dispositivo personalizado][2]

1. Escolha **Deixe-me definir minha própria ID de Dispositivo**. Insira uma ID do Dispositivo como **rasppi**, clique em **Verificar ID** para verificar se você já não usou o nome na sua solução, em seguida, clique em **Criar** para provisionar o dispositivo.

   ![Adicionar ID do dispositivo][3]

1. Anote as credenciais do dispositivo (**ID do dispositivo**, **Nome do host do Hub IoT** e **Chave do Dispositivo**). Seu aplicativo cliente no Raspberry Pi precisa desses valores para se conectar à solução de monitoramento remoto. Em seguida, clique em **Concluído**.

    ![Exibir credenciais do dispositivo][4]

1. Selecione seu dispositivo na lista de dispositivos do painel da solução. Em seguida, no painel **Detalhes do Dispositivo**, clique em **Habilitar Dispositivo**. O status do seu dispositivo agora é **Executando**. Agora a solução de monitoramento remoto poderá receber telemetria do seu dispositivo e invocar métodos nele.

[img-launch-solution]: media/iot-suite-raspberry-pi-kit-view-solution/launch.png
[img-menu]: media/iot-suite-raspberry-pi-kit-view-solution/menu.png
[1]: media/iot-suite-raspberry-pi-kit-view-solution/suite0.png
[2]: media/iot-suite-raspberry-pi-kit-view-solution/suite1.png
[3]: media/iot-suite-raspberry-pi-kit-view-solution/suite2.png
[4]: media/iot-suite-raspberry-pi-kit-view-solution/suite3.png