---
title: Como conectar o Kit de Desenvolvimento da IoT AZ3166 a um Hub IoT
description: Neste tutorial, aprenda a configurar e conectar o Kit de Desenvolvimento da IoT AZ3166 ao Hub IoT do Azure para enviar dados para a plataforma de nuvem do Azure.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.custom:
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.openlocfilehash: 3e5ab1667ee0cda459785efa624bd7f4fc6818b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97562949"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Como conectar o IoT DevKit AZ3166 ao Hub IoT do Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Você pode usar o [Kit de Desenvolvimento da IoT MXChip](https://microsoft.github.io/azure-iot-developer-kit/) para desenvolver e criar protótipos de soluções de Internet das Coisas (IoT) que usufruem dos serviços do Microsoft Azure. Ele inclui uma placa compatível com Arduino com periféricos e sensores avançados, um pacote de placa open-source e uma [galeria de amostras](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-learn"></a>O que você aprenderá

* Como criar um Hub IoT e registrar um dispositivo para o Kit de Desenvolvimento da IoT MXChip.
* Como conectar o Kit de Desenvolvimento da IoT ao Wi-Fi e configurar a cadeia de conexão do Hub IoT.
* Como enviar os dados de telemetria do sensor do Kit de Desenvolvimento do hub IoT.
* Como preparar o ambiente de desenvolvimento e desenvolver o aplicativo para o Kit de Desenvolvimento da IoT.

Você ainda não tem um DevKit? Experimente o [simulador DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) ou [compre um kit de desenvolvimento](https://aka.ms/iot-devkit-purchase).

Você pode encontrar o código-fonte de todos os tutoriais do Kit de Desenvolvimento na [galeria de exemplos de código](/samples/browse/?term=mxchip).

## <a name="what-you-need"></a>O que você precisa

- Uma placa do Kit de Desenvolvimento da IoT da MXChip com um cabo micro-USB. [Obtenha agora](https://aka.ms/iot-devkit-purchase).
- Um computador executando o Windows 10, o macOS 10.10+ ou o Ubuntu 18.04+.
- Uma assinatura ativa do Azure. [Ative uma conta de avaliação do Microsoft Azure por 30 dias](https://azureinfo.microsoft.com/us-freetrial.html).
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
  
## <a name="prepare-your-hardware"></a>Prepare seu hardware

Conecte o hardware a seguir no seu computador:

* Placa DevKit
* Cabo micro USB

![Requisitos de hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Conecte o Kit de Desenvolvimento ao computador, siga essas etapas:

1. Conecte a extremidade USB ao computador.

2. Conecte a extremidade micro USB ao Kit de Desenvolvimento.

3. O LED verde perto do botão liga/desliga confirma a conexão.

   ![Conexões de hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Início Rápido: Enviar telemetria do Kit de Desenvolvimento para um Hub IoT

O início rápido usa o firmware pré-compilado do Kit de Desenvolvimento para enviar a telemetria ao Hub IoT. Antes de executá-lo, crie um hub IoT e registre um dispositivo com o hub.

### <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Registrar um dispositivo

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa o Azure Cloud Shell para registrar um dispositivo simulado.

1. Execute o comando a seguir no Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

   **MyNodeDevice**: O nome do dispositivo que está sendo registrado. Use **MyNodeDevice** conforme mostrado. Se você escolher um nome diferente para seu dispositivo, será necessário usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Se você receber um erro ao executar `device-identity`, instale a [Extensão IoT do Azure para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md).
   > Execute o comando a seguir para adicionar a Extensão do Microsoft Azure IoT para a CLI do Azure à instância do Cloud Shell. A Extensão de IoT adiciona comandos específicos do Hub IoT, do IoT Edge e do DPS de IoT à CLI do Azure.
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. Execute os seguintes comandos no Azure Cloud Shell para obter a _cadeia de conexão de dispositivo_ referente ao dispositivo que você acabou de registrar:

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Tome nota da cadeia de conexão do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente no início rápido.

### <a name="send-devkit-telemetry"></a>Enviar telemetria do Kit de Desenvolvimento

O Kit de Desenvolvimento se conecta a um ponto de extremidade específico do dispositivo em seu hub IoT e envia telemetria de temperatura e umidade.

1. Baixe a versão mais recente do [firmware GetStarted](https://aka.ms/devkit/prod/getstarted/latest) para o Kit de Desenvolvimento da IoT.

1. Use o USB para conectar a Kit de Desenvolvimento de IoT ao seu computador. Abra o Explorador de Arquivos. Há um dispositivo de armazenamento em massa USB chamado **AZ3166**.

    ![Abra o Windows Explorer](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Arraste e solte o firmware que acabou de ser baixado no dispositivo de armazenamento em massa e ele será atualizado automaticamente.

    ![Copiar firmware](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. No Kit de Desenvolvimento, mantenha pressionado o botão **B**, pressione solte o botão **Redefinir** e, em seguida, solte o botão **B**. Seu Kit de Desenvolvimento entra no modo AP. Para confirmar, a tela exibirá o identificador SSID do Kit de Desenvolvimento e o endereço IP do portal de configuração.

    ![Botão de reinicialização, botão B e SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Definir o modo AP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Use um navegador da Web em um dispositivo habilitado para Wi-Fi (computador ou telefone celular) para se conectar ao SSID do Kit de Desenvolvimento da IoT mostrado na etapa anterior. Se ele solicitar uma senha, não preencha.

    ![Conectar SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Abra **192.168.0.1** no navegador. Selecione o Wi-Fi ao qual você deseja que o Kit de Desenvolvimento da IoT se conecte, digite a senha do Wi-Fi e cole a cadeia de conexão do dispositivo anotada anteriormente. Em seguida, clique em Salvar.

    ![Interface do usuário de configuração](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > O Kit de Desenvolvimento da IoT só dá suporte à rede de 2,4 GHz. Confira as [Perguntas frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) para obter mais detalhes.

1. As informações do Wi-Fi e a cadeia de conexão do dispositivo serão armazenadas no Kit de Desenvolvimento de IoT quando você vir a página de resultados.

    ![Resultado da configuração](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Após a configuração do Wi-Fi, suas credenciais persistirão no dispositivo para essa conexão, mesmo que o dispositivo esteja desconectado.

1. O Kit de Desenvolvimento da IoT reinicia em alguns segundos. Na tela do Kit de Desenvolvimento, você vê que o endereço IP do Kit de Desenvolvimento segue os dados telemétricos, incluindo o valor de temperatura e de umidade com contagem de mensagens enviadas para o Hub IoT.

    ![IP do Wi-Fi](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Enviar dados](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Para verificar os dados telemétricos enviados ao Azure, execute o seguinte comando no Azure Cloud Shell:

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Siga estas etapas para preparar o ambiente de desenvolvimento do Kit de Desenvolvimento:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Instale o Visual Studio Code com o pacote de extensão Azure IoT Tools

1. Instalar o [Arduino IDE](https://www.arduino.cc/en/Main/Software). Ele fornece a cadeia de ferramentas necessária para compilar e carregar o código do Arduino.
    * **Windows**: use a versão do Windows Installer. Não instale do App Store.
    * **macOS**: arraste e solte o **Arduino.app** extraído na pasta `/Applications`.
    * **Ubuntu**: descompacte-o na pasta, como `$HOME/Downloads/arduino-1.8.8`

2. Instale o [Visual Studio Code](https://code.visualstudio.com/), um editor de código-fonte multiplataforma com IntelliSense, conclusão de código e suporte a depuração poderosos, além de extensões avançadas que podem ser instaladas do Marketplace.

3. Inicie o VS Code, procure o **Arduino** no marketplace de extensões e instale-o. Essa extensão fornece experiências aprimoradas para desenvolvimento na plataforma do Arduino.

    ![Instalar Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Procure as [Ferramentas do Azure IoT](https://aka.ms/azure-iot-tools) no marketplace de extensões e instale-as.

    ![Captura de tela que mostra as ferramentas de IoT do Azure no Marketplace de extensão.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Ou copie e cole esta URL em uma janela do navegador: `vscode:extension/vsciot-vscode.azure-iot-tools`

    > [!NOTE]
    > O pacote de extensão do Azure IoT Tools contém o [Azure IoT Device Workbench](https://aka.ms/iot-workbench) que é usado para desenvolvimento e depuração em vários dispositivos do Kit de Ferramentas da IoT. A [extensão do Hub IoT](https://aka.ms/iot-toolkit), também incluída com o pacote de extensão do Azure IoT Tools, é usada para gerenciar e interagir com os hubs IoT do Azure.

5. Configurar o VS Code com configurações do Arduino.

    No Visual Studio Code, clique em **Arquivo > Preferências > Configurações** (no macOS, **Código > Preferências > Configurações**). Depois, clique no ícone **Abrir configurações (JSON)** no canto superior direito da página *Configurações*.

    ![Instalar as Ferramentas do Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Adicione as seguintes linhas para configurar o Arduino, dependendo de sua plataforma: 

    * **Windows**:

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:

        Substitua o espaço reservado **{username}** abaixo por seu nome de usuário.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Clique em `F1` para abrir a paleta de comandos, digite e selecione **Arduino: Board Manager**. Pesquise **AZ3166** e instale a versão mais recente.

    ![Instalar o SDK do Kit de Desenvolvimento](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Instalar drivers do ST-Link

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) é a interface USB que o kit de desenvolvimento de IoT usa para comunicar-se com seu computador de desenvolvimento. Você precisa instalá-lo no Windows para atualizar o código do dispositivo compilado para o Kit de Desenvolvimento. Siga as etapas específicas do sistema operacional para permitir o acesso do computador ao dispositivo.

* **Windows**: baixe e instale o driver USB do [site STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**: não é necessário nenhum driver para o macOS.
* **Ubuntu**: Execute os comandos no terminal e saia e entre de novo para que a alteração no grupo entre em vigor:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Agora a preparação e a configuração do ambiente de desenvolvimento estão concluídas. Vamos criar o exemplo Getstarted que você acabou de executar.

## <a name="build-your-first-project"></a>Criar seu primeiro projeto

### <a name="open-sample-code-from-sample-gallery"></a>Abrir exemplo de código da galeria de amostras

O Kit de Desenvolvimento da IoT contém uma galeria avançada de amostras que você pode usar para aprender a conectar o Kit de Desenvolvimento a vários serviços do Azure.

1. Verifique se o kit de desenvolvimento de IoT **não está conectado** ao computador. Inicie o VS Code primeiro e, em seguida, conecte o kit de desenvolvimento ao computador.

1. Clique em `F1` para abrir a paleta de comandos, digite e selecione **Workbench dos Dispositivos de IoT do Azure: Abrir Exemplos...** . Em seguida, selecione **Kit de Desenvolvimento de IoT** como placa.

1. Na página de Exemplos do IoT Workbench, localize **Introdução** e clique em **Abrir Exemplo**. Em seguida, selecione o caminho padrão para baixar o código de exemplo.

    ![Abrir exemplo](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Provisionar o dispositivo e o Hub IoT

Em vez de provisionar o Hub IoT e o dispositivo a partir do portal do Azure, você pode fazer isso no VS Code sem sair do ambiente de desenvolvimento.

1. Na nova janela de projeto aberta, clique em `F1` para abrir a paleta de comandos, digite e selecione **Workbench dos Dispositivos de IoT do Azure: Provisionar Serviços do Azure...** . Siga o guia passo a passo para concluir o provisionamento do Hub IoT do Azure e criar o dispositivo no Hub IoT.

    ![Comando de provisionamento](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Se você não tiver entrado no Azure. Siga a notificação pop-up para entrar.

1. Selecione a assinatura que deseja usar.

    ![Selecionar sub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Depois, selecione ou crie um novo [grupo de recursos](../azure-resource-manager/management/overview.md#terminology).

    ![Escolha o grupo de recursos](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. No grupo de recursos que você especificou, siga o guia para selecionar ou criar um novo Hub IoT.

    ![Selecionar as etapas do Hub IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Selecionar Hub IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Hub IoT selecionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. Na janela saída, você verá o Hub IoT provisionado.

    ![Hub IoT provisionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Selecione ou crie um novo dispositivo no Hub IoT que você provisionou.

    ![Selecionar etapas do dispositivo IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Selecionar dispositivo IoT provisionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Agora você tem o Hub IoT provisionado com um dispositivo criado nele. Além disso, a cadeia de conexão do dispositivo será salva no VS Code para configurar o Kit de Desenvolvimento da IoT mais tarde.

    ![Provisionamento concluído](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Configurar e compilar o código do dispositivo

1. Na barra de status inferior direita, verifique se **MXCHIP AZ3166** é mostrado como a placa selecionada e se a porta serial com **STMicroelectronics** está sendo usada.

    ![Selecionar a placa e o COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Clique em `F1` para abrir a paleta de comandos, digite e selecione **Workbench dos Dispositivos de IoT do Azure: Definir as Configurações do Dispositivo...**  e, em seguida, selecione **Definir Configurações do Dispositivo> Configurar Cadeia de Conexão do Dispositivo > Selecionar Cadeia de Conexão do Dispositivo do Hub IoT**.

1. No kit de desenvolvimento, mantenha pressionado o **botão A**, pressione e solte o botão **redefinir** e, em seguida, solte o **botão A**. O kit de desenvolvimento entra no modo de configuração e salva a cadeia de conexão.

    ![Cadeia de conexão](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Clique em `F1` novamente, digite e selecione **Workbench dos Dispositivos de IoT do Azure: Carregar o código de dispositivo**. Ele começa a compilar e carregar o código para o Kit de desenvolvimento.

    ![Upload do Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

O Kit de Desenvolvimento reinicia e começa a execução do código.

> [!NOTE]
> Sempre que há erros ou interrupções, é possível recuperar executando o comando novamente.

## <a name="test-the-project"></a>Teste do projeto

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Exibir a telemetria enviada ao Hub IoT do Azure

Clique no ícone de tomada na barra de status para abrir o Monitor Serial:

![Monitor serial](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

O aplicativo de exemplo é executado com êxito quando você vê os seguintes resultados:

* O Monitor de Serial exibe a mensagem enviada ao Hub IoT.
* O LED no Kit de Desenvolvimento de IoT MXChip está piscando.

![Saída do monitor de serial](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

> [!NOTE]
> Você pode encontrar um erro durante o teste em que o LED não piscando, o portal do Azure não mostra os dados que recebe do dispositivo, mas a tela OLED do dispositivo exibe **Executando…** . Para resolver o problema, no portal do Azure, acesse o dispositivo no hub IoT e envie uma mensagem para o dispositivo. Se você vir a seguinte resposta no monitor serial em VS Code, é possível que a comunicação direta do dispositivo esteja bloqueada no roteador. Verifique as regras de firewall e do roteador configuradas para os dispositivos em conexão. Além disso, veja se a porta de saída 1833 está aberta.
> 
> ERROR: mqtt_client.c (ln 454): Error: failure opening connection to endpoint  
> INFO: >>>Connection status: disconnected  
> ERROR: tlsio_mbedtls.c (ln 604): Underlying IO open failed  
> ERROR: mqtt_client.c (ln 1042): Error: io_open failed  
> ERROR: iothubtransport_mqtt_common.c (ln 2283): failure connecting to address atcsliothub.azure-devices.net.  
> INFO: >>>Re-connect.  
> INFO: IoThub Version: 1.3.6  

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Exibir a telemetria recebida pelo Hub IoT do Azure

Você pode usar as [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para monitorar mensagens D2C (do dispositivo para a nuvem) no Hub IoT.

1. Entre no [portal do Azure](https://portal.azure.com/) e encontre o Hub IoT que você criou.

    ![Portal do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. No painel **Políticas de acesso compartilhado**, clique na **política iothubowner** e anote a cadeia de conexão do Hub IoT.

    ![Cadeia de conexão do Hub IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. No VS Code, clique em `F1`, digite e selecione **Hub IoT do Azure: Definir cadeia de conexão do Hub IoT**. Copie a cadeia de conexão.

    ![Defina a cadeia de conexão do Hub IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Expanda o painel **DISPOSITIVOS DO HUB IOT** à esquerda, clique com botão direito no nome do dispositivo que você criou e selecione **Iniciar o Monitoramento do Ponto de Extremidade de Evento Interno**.

    ![Monitorar mensagens D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. No painel **SAÍDA**, veja as mensagens D2C de entrada do Hub IoT.

    ![Captura de tela que mostra as mensagens D2C de entrada para o Hub IoT.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Examine o código

O `GetStarted.ino` é o principal arquivo de esboço do Arduino.

![Mensagem D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Para ver como a telemetria do dispositivo é enviada ao Hub IoT, abra o arquivo `utility.cpp` na mesma pasta. Consulte [Referência de API](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) para saber como usar sensores e periféricos no Kit de Desenvolvimento da IoT.

O `DevKitMQTTClient` usado é um wrapper de **iothub_client** dos [SDKs de IoT dos SDKs do Microsoft Azure e bibliotecas para C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) para interagir com o Hub IoT.

## <a name="problems-and-feedback"></a>Comentários e problemas

Se houver problemas, procure uma solução nas [Perguntas frequentes sobre o kit de desenvolvimento de IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou fale conosco por meio do [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Você também pode nos fornecer comentários fazendo-os nesta página.

## <a name="next-steps"></a>Próximas etapas

Você conectou um Kit de Desenvolvimento de IoT MXChip ao seu Hub IoT e enviou os dados do sensor de captura ao seu Kit de Desenvolvimento de IoT.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
