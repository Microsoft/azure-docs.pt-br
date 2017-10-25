> [!div class="op_single_selector"]
> * [C em Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [C em Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [Node.js](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> 
> 

## <a name="scenario-overview"></a>Visão geral do cenário
Nesse cenário, você cria um dispositivo que envia a telemetria a seguir para a [solução pré-configurada][lnk-what-are-preconfig-solutions] de monitoramento remoto:

* Temperatura externa
* Temperatura interna
* Umidade

Para simplificar, o código no dispositivo gera valores de exemplo, mas é recomendável estender o exemplo conectando sensores reais aos dispositivos e enviando telemetria real.

O dispositivo também é capaz de responder aos métodos invocados no painel da solução e aos conjuntos de valores de propriedade desejada no painel da solução.

Para concluir este tutorial, você precisa de uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk-free-trial].

## <a name="before-you-start"></a>Antes de começar
Antes de escrever qualquer código para o seu dispositivo, você precisa provisionar a solução pré-configurada de monitoramento remoto e provisionar um novo dispositivo personalizado nessa solução.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Provisionar sua solução pré-configurada de monitoramento remoto
O dispositivo que você criar neste tutorial enviará dados a uma instância da solução pré-configurada de [monitoramento remoto][lnk-remote-monitoring]. Se você ainda não provisionou a solução pré-configurada de monitoramento remoto em sua conta do Azure, use as seguintes etapas:

1. Na página <https://www.azureiotsuite.com/>, clique em **+** para criar uma solução.
2. Clique em **Selecionar** no painel **Monitoramento remoto** para criar a solução.
3. Na página **Criar solução de Monitoramento remoto**, insira um **Nome de solução** de sua escolha, selecione a **Região** na qual você deseja implantar e selecione a assinatura do Azure a ser usada. Clique em **Criar solução**.
4. Aguarde até que o processo de provisionamento seja concluído.

> [!WARNING]
> As soluções pré-configuradas usam serviços faturáveis do Azure. Certifique-se de remover a solução pré-configurada da sua assinatura quando tiver terminado com ela para evitar encargos desnecessários. Você pode remover completamente uma solução pré-configurada de sua assinatura visitando a página <https://www.azureiotsuite.com/>.
> 
> 

Quando o processo de provisionamento para a solução de monitoramento remoto for concluído, clique em **Iniciar** para abrir o painel da solução em seu navegador.

![Painel da solução][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Provisionar o dispositivo na solução de monitoramento remoto
> [!NOTE]
> Se você já configurou um dispositivo em sua solução, poderá ignorar esta etapa. Você precisa saber as credenciais do dispositivo ao criar o aplicativo cliente.
> 
> 

Para um dispositivo conectar-se à solução pré-configurada, ele deve identificar-se no Hub IoT usando credenciais válidas. Você pode recuperar as credenciais do dispositivo no painel da solução. Você pode incluir as credenciais do dispositivo em seu aplicativo cliente posteriormente neste tutorial.

Para adicionar um dispositivo à sua solução de monitoramento remoto, realize as etapas a seguir no painel da solução:

1. No canto inferior esquerdo do painel, clique em **Adicionar um dispositivo**.
   
   ![Adicionar um dispositivo][1]
2. No painel **Dispositivo Personalizado**, clique em **Adicionar novo**.
   
   ![Adicionar um dispositivo personalizado][2]
3. Escolha **Deixe-me definir minha própria ID de Dispositivo**. Insira uma ID do Dispositivo como **mydevice**, clique em **Verificar ID** para verificar se esse nome ainda não está em uso e, em seguida, clique em **Criar** para provisionar o dispositivo.
   
   ![Adicionar ID do dispositivo][3]
4. Anote as credenciais do dispositivo (ID do dispositivo, nome do host do Hub IoT e Chave do Dispositivo). Seu aplicativo cliente precisa desses valores para se conectar à solução de monitoramento remoto. Em seguida, clique em **Concluído**.
   
    ![Exibir credenciais do dispositivo][4]
5. Selecione seu dispositivo na lista de dispositivos do painel da solução. Em seguida, no painel **Detalhes do Dispositivo**, clique em **Habilitar Dispositivo**. O status do seu dispositivo agora é **Executando**. Agora a solução de monitoramento remoto poderá receber telemetria do seu dispositivo e invocar métodos nele.

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/