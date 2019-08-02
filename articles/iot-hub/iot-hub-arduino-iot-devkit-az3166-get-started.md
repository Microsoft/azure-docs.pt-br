---
title: Kit de Desenvolvimento da IoT para a nuvem -- Conectar o Kit de Desenvolvimento da IoT AZ3166 ao Hub IoT do Azure | Microsoft Docs
description: Neste tutorial, aprenda a configurar e conectar o Kit de Desenvolvimento da IoT AZ3166 ao Hub IoT do Azure para enviar dados para a plataforma de nuvem do Azure.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.openlocfilehash: 4017a3be5e03e1a9b85b4002b8069a1adc3a6b83
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551580"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Como conectar o IoT DevKit AZ3166 ao Hub IoT do Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Você pode usar o [Kit de Desenvolvimento da IoT MXChip](https://microsoft.github.io/azure-iot-developer-kit/) para desenvolver e criar protótipos de soluções de Internet das Coisas (IoT) que usufruem dos serviços do Microsoft Azure. Ele inclui uma placa compatível com Arduino com periféricos avançados e sensores, um pacote de placa do código-fonte aberto e uma rica [Galeria de exemplos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-learn"></a>O que você aprenderá

* Como criar um Hub IoT e registrar um dispositivo para o Kit de Desenvolvimento da IoT MXChip.
* Como conectar o IoT DevKit ao Wi-Fi e configurar a cadeia de caracteres de conexão do IoT Hub.
* Como enviar os dados de telemetria do sensor de DevKit ao seu hub IoT.
* Como preparar o ambiente de desenvolvimento e desenvolver aplicativos para o DevKit de IoT.

Você ainda não tem um DevKit? Experimente o [simulador DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) ou [compre um kit de desenvolvimento](https://aka.ms/iot-devkit-purchase).

Você pode encontrar o código-fonte para todos os tutoriais do Kit de desenvolvimento na [IoTDevEnvExamples](https://github.com/IoTDevEnvExamples) repositório.

## <a name="what-you-need"></a>O que você precisa

* Uma placa MXChip IoT DevKit com um cabo Micro USB. [Obtenha agora](https://aka.ms/iot-devkit-purchase).
* Um computador executando o Windows 10, Ubuntu 18.04 + ou macOS 10.10 +.
* Uma assinatura ativa do Azure. [Ative uma conta de avaliação do Microsoft Azure por 30 dias](https://azureinfo.microsoft.com/us-freetrial.html).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
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

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Início Rápido: Enviar telemetria de kit de desenvolvimento para um IoT Hub

O guia de início rápido usa pré-compiladas firmware do Kit de desenvolvimento para enviar a telemetria ao IoT Hub. Antes de você executá-lo, você pode cria um hub IoT e registrar um dispositivo com o hub.

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
   > Se você receber um erro ao executar `device-identity`, instale o [extensão de IOT do Azure para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md) para obter mais detalhes.
  
1. Execute os seguintes comandos no Azure Cloud Shell para obter a _cadeia de conexão de dispositivo_ referente ao dispositivo que você acabou de registrar:

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Tome nota da cadeia de conexão do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente no início rápido.

### <a name="send-devkit-telemetry"></a>Enviar telemetria do Kit de desenvolvimento

O Kit de desenvolvimento se conecta a um ponto de extremidade específico do dispositivo no hub IoT e envia a telemetria de temperatura e umidade.

1. Baixe a versão mais recente do [GetStarted firmware](https://aka.ms/devkit/prod/getstarted/latest) para IoT DevKit.

1. Certifique-se de que o IoT DevKit se conectar ao computador via USB. Abra o Explorador de arquivos, há um dispositivo de armazenamento em massa USB chamado **AZ3166**.
    ![Abrir do Windows Explorer](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Arrastar e soltar o firmware baixado apenas para o dispositivo de armazenamento em massa e ele irá surgir automaticamente.
    ![Copy firmware](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. No Kit de desenvolvimento, mantenha pressionado o botão **B**, pressionado e solte o **redefinir** botão e, em seguida, solte o botão **B**. O Kit de desenvolvimento entrará no modo AP. Para confirmar, a tela exibe o identificador do conjunto de serviço (SSID) do Kit de desenvolvimento e o endereço IP do portal de configuração.
    ![Redefinição de botão, botão B e SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Definir o modo AP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Use um navegador da Web em um Wi-Fi diferente dispositivo habilitado (computador ou telefone celular) para se conectar ao SSID do DevKit de IoT exibido na etapa anterior. Se ele solicitar uma senha, deixe vazio.
    ![Conectar-se o SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Abra **192.168.0.1** no navegador. Selecione o Wi-Fi que você deseja se conectar IoT DevKit, digite a senha de Wi-Fi e, em seguida, cole a cadeia de conexão do dispositivo feitas observadas anteriormente. Em seguida, clique em Salvar.
    ![Configuração da interface do usuário](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > O IoT DevKit dá suporte apenas a rede de 2,4 GHz. Confira as [Perguntas frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) para obter mais detalhes.

1. A cadeia de conexão de dispositivo e informações de Wi-Fi será armazenada em IoT DevKit quando você vir a página de resultados.
    ![Resultado de configuração](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Após a configuração do Wi-Fi, suas credenciais persistirão no dispositivo para essa conexão, mesmo que o dispositivo esteja desconectado.

1. O IoT DevKit reiniciará em alguns segundos. Na tela do Kit de desenvolvimento, você pode ver o endereço IP para o Kit de desenvolvimento segue pelos dados de telemetria, incluindo temperatura e umidade valor com contagem de mensagens-send para o IoT Hub do Azure.
    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Enviar dados](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Para verificar se os dados de telemetria enviados para o Azure, execute o seguinte comando no Azure Cloud Shell:
    ```bash
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Siga estas etapas para preparar o ambiente de desenvolvimento para o Kit de desenvolvimento:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Instalar o Visual Studio Code com o pacote de extensão de ferramentas do Azure IoT

1. Instalar o [Arduino IDE](https://www.arduino.cc/en/Main/Software). Ele fornece a cadeia de ferramentas necessária para compilar e carregar o código do Arduino.
    * **Windows**: use a versão do Windows Installer. Não instale da Store do aplicativo.
    * **macOS**: arraste e solte o **Arduino.app** extraído na pasta `/Applications`.
    * **Ubuntu**: descompacte-o na pasta, como `$HOME/Downloads/arduino-1.8.8`

2. Instale [Visual Studio Code](https://code.visualstudio.com/), um editor de código de origem de plataforma cruzada com o poderoso intellisense, preenchimento de código e suporte, bem como extensões sofisticadas de depuração pode ser instalado do marketplace.

3. Inicie o VS Code, procure o **Arduino** no marketplace de extensões e instale-o. Essa extensão fornece experiências aprimoradas para desenvolvimento na plataforma do Arduino.
    ![Instalar Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Procure as [Ferramentas do Azure IoT](https://aka.ms/azure-iot-tools) no marketplace de extensões e instale-as.
    ![Instalar as Ferramentas do Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Ou use este link direto:
    > [!div class="nextstepaction"]
    > [Instalar o pacote de extensão de ferramentas do Azure IoT](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > O pacote de extensão de ferramentas do Azure IoT contém a [Bancada de trabalho do Azure IoT dispositivo](https://aka.ms/iot-workbench) que é usado para desenvolver e depurar em vários dispositivos do Kit de desenvolvimento de IoT. O [Kit de ferramentas do Azure IoT Hub](https://aka.ms/iot-toolkit), também é incluído com o pacote de extensão de ferramentas do IoT do Azure, que é usado para gerenciar e interagir com Hubs IoT do Azure.

5. Configurar o VS Code com configurações do Arduino.

    No Visual Studio Code, clique em **Arquivo > Preferências > Configurações**. Em seguida, clique em **...** e **abra settings.json**.
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

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) é a interface USB que o kit de desenvolvimento de IoT usa para comunicar-se com seu computador de desenvolvimento. Você precisará instalá-lo no Windows para exibir o código de dispositivo compilados para o Kit de desenvolvimento. Siga as etapas específicas do sistema operacional para permitir o acesso do computador ao dispositivo.

* **Windows**: Baixe e instale o driver USB do [site STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html) ou [link direto](https://aka.ms/stlink-v2-windows).
* **macOS**: não é necessário nenhum driver para o macOS.
* **Ubuntu**: Execute os comandos no terminal e sair e entrar para que a alteração de grupo entrem em vigor:
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Agora a preparação e a configuração do ambiente de desenvolvimento estão concluídas. Informe-nos crie o exemplo de GetStarted que você acabou de ser executado.

## <a name="build-your-first-project"></a>Criar seu primeiro projeto

### <a name="open-sample-code-from-sample-gallery"></a>Abrir o código de exemplo da Galeria de exemplo

O IoT DevKit contém uma sofisticada Galeria de exemplos que você pode usar para aprender conectem o Kit de desenvolvimento para vários serviços do Azure.

1. Verifique se o kit de desenvolvimento de IoT **não está conectado** ao computador. Inicie o VS Code primeiro e, em seguida, conecte o kit de desenvolvimento ao computador.

1. Clique em `F1` para abrir a paleta de comandos, digite e selecione **Workbench dos Dispositivos de IoT do Azure: Abrir Exemplos...** . Em seguida, selecione **Kit de Desenvolvimento de IoT** como placa.

1. Na página de Exemplos do IoT Workbench, localize **Introdução** e clique em **Abrir Exemplo**. Em seguida, selecione o caminho padrão para baixar o código de exemplo.
    ![Abrir exemplo](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Provisionamento do Azure IoT Hub e dispositivo

Em vez de provisionamento o IoT Hub do Azure e o dispositivo do portal do Azure, você pode fazer isso no VS Code sem deixar o ambiente de desenvolvimento.

1. Na nova janela de projeto aberta, clique em `F1` para abrir a paleta de comandos, digite e selecione **Workbench dos Dispositivos de IoT do Azure: Provisionar Serviços do Azure...** . Siga o guia passo a passo para concluir o provisionamento do Hub IoT do Azure e criar o dispositivo no Hub IoT.
    ![Comando de provisão](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Se você não tiver entrado no Azure. Siga a notificação de pop-up para entrar.

1. Selecione a assinatura que deseja usar.
    ![Selecione sub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Em seguida, selecione ou crie uma nova [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).
    ![Selecione o grupo de recursos](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. No grupo de recursos que você especificou, siga o guia para selecionar ou criar um novo IoT Hub do Azure.
    ![Selecione as etapas do IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Selecione o Hub IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Hub IoT selecionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. Na janela de saída, você verá o IoT Hub do Azure provisionado ![provisionado de Hub IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Selecione ou crie um novo dispositivo no Hub do IoT do Azure você provisionou.
    ![Selecione as etapas de dispositivo IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Selecione o dispositivo de IoT provisionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Agora você tem o IoT Hub do Azure provisionados e o dispositivo criado nele. Também a cadeia de caracteres de conexão do dispositivo será salvo no VS Code para configurar o IoT DevKit mais tarde.
    ![Provisionamento concluído](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Configurar e compilar o código de dispositivo

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

Clique no ícone de tomada na barra de status para abrir o Monitor Serial: ![Monitor serial](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

O aplicativo de exemplo é executado com êxito quando você vê os seguintes resultados:

* O Monitor de Serial exibe a mensagem enviada ao Hub IoT.
* O LED no Kit de Desenvolvimento de IoT MXChip está piscando.

![Saída do monitor de serial](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Exibir a telemetria recebida pelo Hub IoT do Azure

Você pode usar as [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para monitorar mensagens D2C (do dispositivo para a nuvem) no Hub IoT.

1. Entre no [portal do Azure](https://portal.azure.com/) e encontre o Hub IoT que você criou.
    ![Portal do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. No painel **Políticas de acesso compartilhado**, clique na **política iothubowner** e anote a cadeia de conexão do Hub IoT.
    ![Cadeia de conexão do Hub IoT do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. No VS Code, clique em `F1`, digite e selecione **Hub IoT do Azure: Definir cadeia de conexão do Hub IoT**. Copie a cadeia de conexão.
    ![Defina a cadeia de conexão do Hub IoT do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Expanda o **dispositivos de HUB IOT do AZURE** painel à direita, clique com botão direito no nome do dispositivo que você criou e selecione **Iniciar monitoramento de evento de ponto de extremidade interno**.
    ![Monitorar a mensagem D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. No painel **SAÍDA**, veja as mensagens D2C de entrada do Hub IoT.
    ![Mensagem D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Examine o código

O `GetStarted.ino` é o principal arquivo de esboço de Arduino.

![Mensagem D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Para ver como a telemetria do dispositivo será enviada ao IoT Hub do Azure, abra o `utility.cpp` arquivo na mesma pasta. Modo de exibição [referência da API](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) para aprender a usar periféricos e sensores em IoT DevKit.

O `DevKitMQTTClient` usado é um wrapper do **iothub_client** da [SDKs de IoT do Microsoft Azure e bibliotecas para C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) para interagir com o IoT Hub do Azure.

## <a name="problems-and-feedback"></a>Comentários e problemas

Se houver problemas, procure uma solução nas [Perguntas frequentes sobre o kit de desenvolvimento de IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou fale conosco por meio do [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Você também pode nos fornecer comentários fazendo-os nesta página.

## <a name="next-steps"></a>Próximas etapas

Você conectou um Kit de Desenvolvimento de IoT MXChip ao seu Hub IoT e enviou os dados do sensor de captura ao seu Kit de Desenvolvimento de IoT.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
