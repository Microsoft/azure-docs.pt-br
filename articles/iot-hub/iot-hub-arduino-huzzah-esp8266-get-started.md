---
title: ESP8266 para a nuvem - Conectar o Feather HUZZAH ESP8266 ao Hub IoT do Azure | Microsoft Docs
description: Saiba como configurar e conectar Adafruit Feather HUZZAH ESP8266 para Hub IoT do Azure para ele para enviar dados para a plataforma de nuvem do Azure neste tutorial.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: 293901aca3fa1a94c9c6340d2e04f47914db0e07
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60783153"
---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Conectar-se o Adafruit Feather HUZZAH ESP8266 ao Hub IoT do Azure na nuvem

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Conexão entre o DHT22, o Feather HUZZAH ESP8266 e o Hub IoT](./media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>O que fazer

Conecte o Adafruit Feather HUZZAH ESP8266 a um Hub IoT criado. Em seguida, você executa um aplicativo de exemplo no ESP8266 para coletar os dados de temperatura e de umidade de um sensor DHT22. Por fim, você envia os dados do sensor para o Hub IoT.

> [!NOTE]
> Se você estiver usando outras placas ESP8266, ainda poderá seguir estas etapas para conectá-las ao Hub IoT. Dependendo da placa ESP8266 utilizada, talvez seja necessário reconfigurar o `LED_PIN`. Por exemplo, se você estiver usando ESP8266 da AI-Thinker, poderá alterá-la de `0` para `2`. Não tem um dispositivo ainda? Obtenha-o no [site do Azure](https://azure.com/iotstarterkits).

## <a name="what-you-learn"></a>O que você aprenderá

* Como criar um Hub IoT e registrar um dispositivo no Feather HUZZAH ESP8266
* Como conectar o Feather HUZZAH ESP8266 ao sensor e ao computador
* Como coletar dados do sensor executando um aplicativo de exemplo no Feather HUZZAH ESP8266
* Como enviar os dados do sensor para o Hub IoT

## <a name="what-you-need"></a>O que você precisa

![Partes necessárias para o tutorial](./media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

Para concluir esta operação, você precisará das seguintes partes do seu Kit de Início do Feather HUZZAH ESP8266:

* A placa Feather HUZZAH ESP8266
* Um cabo Micro USB para USB Tipo A

Você também precisa destes itens para seu ambiente de desenvolvimento:

* Uma assinatura ativa do Azure. Se não tiver uma conta do Azure, [crie uma conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos.
* Um Mac ou PC que esteja executando Windows ou Ubuntu.
* Uma rede sem fio para Feather HUZZAH ESP8266 para se conectar.
* Uma conexão com a Internet para baixar a ferramenta de configuração.
* [Extensão do Visual Studio Code para Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino).

> [!Note]
> A versão da IDE do Arduino usada pela extensão do Visual Studio Code para Arduino deve ser 1.6.8 ou posterior. As versões anteriores não funcionam com a biblioteca do AzureIoT.

Os itens a seguir são opcionais, caso você não tenha um sensor. Você também tem a opção de usar dados de sensor simulada.

* Um sensor de temperatura e umidade Adafruit DHT22
* Uma placa universal
* Cabos de jumper macho/macho

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Recuperar a cadeia conexão para o hub IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Difusão HUZZAH ESP8266 de conexão com o sensor e seu computador

Nesta seção, você conecta os sensores à sua placa. Em seguida, você conecta o dispositivo ao computador para uso posterior.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Conecte um sensor de temperatura e umidade DHT22 a Feather HUZZAH ESP8266

Use os cabos da placa universal e jumper para fazer a conexão da seguinte maneira. Se você não tiver um sensor, ignore esta seção porque você pode usar dados de sensor simulado em vez disso.

![Referência de conexões](./media/iot-hub-arduino-huzzah-esp8266-get-started/17_connections_on_breadboard.png)

Para os pinos do sensor, use a seguinte fiação:

| Início (Sensor)           | End (quadro)            | Cor de cabo   |
| -----------------------  | ---------------------- | ------------  |
| VDD (Pin 31F)            | 3V (Fixar 58H)           | Cabo vermelho     |
| DADOS de (Pin 32F)           | GPIO 2 (Pin 46A)       | Cabo azul    |
| GND (Pin 34F)            | GND (PIn 56I)          | Cabo preto   |

Para obter mais informações, consulte [Instalação do sensor Adafruit DHT22](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) e [Pinagem do Adafruit Feather HUZZAH Esp8266](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts).

Agora seu Feather Huzzah ESP8266 deve estar conectado a um sensor ativo.

![Conectar o DHT22 ao Feather Huzzah](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Conectar o Feather HUZZAH ESP8266 ao seu computador

Como mostrado a seguir, use o cabo Micro USB para USB Tipo A para conectar o Feather HUZZAH ESP8266 ao computador.

![Conectar o Feather Huzzah ao computador](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Adicionar permissões de porta serial (somente Ubuntu)

Se você usar o Ubuntu, verifique se tem as permissões para operar na porta USB do Feather HUZZAH ESP8266. Para adicionar permissões de porta serial, siga estas etapas:

1. Execute os seguintes comandos em um terminal:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Você recebe uma das seguintes saídas:

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   Na saída, observe se `uucp` ou `dialout` é o nome do proprietário do grupo da porta USB.

2. Adicione o usuário ao grupo, executando o seguinte comando:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` é o nome de proprietário de grupo que você obteve na etapa anterior. `<username>` é o nome de usuário do Ubuntu.

3. Saia do Ubuntu e entre novamente para que a alteração seja exibida.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Coletar dados de sensor e enviá-los para o hub IoT

Nesta seção, implante e execute um aplicativo de exemplo em Feather HUZZAH ESP8266. O aplicativo de exemplo pisca o LED do Feather HUZZAH ESP8266 e envia os dados de temperatura e umidade coletados do sensor DHT22 para o Hub IoT.

### <a name="get-the-sample-application-from-github"></a>Obter o aplicativo de exemplo do GitHub

O aplicativo de exemplo está hospedado no GitHub. Clone o repositório de exemplo que contém o aplicativo de exemplo do GitHub. Para clonar o repositório de exemplo, siga estas etapas:

1. Abra um prompt de comando ou uma janela de terminal.

2. Ir para uma pasta onde você deseja que o aplicativo de exemplo a ser armazenado.

3. Execute o comando a seguir:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

   Em seguida, instale o pacote para Feather HUZZAH ESP8266 no código do Visual Studio.

4. Abra a pasta onde o aplicativo de exemplo está armazenado.

5. Abra o arquivo app.ino na pasta do aplicativo no Visual Studio Code.

   ![Abrir o aplicativo de exemplo no Visual Studio Code](media/iot-hub-arduino-huzzah-esp8266-get-started/10_vscode-open-sample-app.png)

6. No Visual Studio Code, digite `F1`.

7. Tipo de **Arduino** e selecione **Arduino: Board Manager**.

8. Na guia **Gerenciador de Placas do Arduino**, clique em **URLs adicionais**.

   ![Gerenciador de Placas do Arduino do código do VS](media/iot-hub-arduino-huzzah-esp8266-get-started/11_vscode-arduino-board-manager.png)

9. Na janela **Configurações do Usuário**, copie e cole o seguinte no final do arquivo

   ```json
   "arduino.additionalUrls": "http://arduino.esp8266.com/stable/package_esp8266com_index.json"
   ```

   ![Configurar a URL do pacote do Arduino no código do VS](media/iot-hub-arduino-huzzah-esp8266-get-started/12_vscode-package-url.png)

10. Salve o arquivo e feche a guia **Configurações do Usuário**.

11. Clique em **Atualizar Índices do Pacote**. Depois que a atualização for concluída, procure **esp8266**.

12. Clique no botão **Instalar** para esp8266.

    O Gerenciador de Placas indica que ESP8266 com uma versão 2.2.0 ou posterior está instalado.

    ![O pacote do esp8266 é instalado](media/iot-hub-arduino-huzzah-esp8266-get-started/13_vscode-esp8266-installed.png)

13. Insira `F1`, em seguida, digite **Arduino** e selecione **Arduino: Configuração de placa**.

14. Clique na caixa para **Placa Selecionada:** e digite **esp8266**, em seguida, selecione **Adafruit HUZZAH ESP8266 (esp8266)**.

    ![Selecione a placa esp8266](media/iot-hub-arduino-huzzah-esp8266-get-started/14_vscode-select-esp8266.png)

### <a name="install-necessary-libraries"></a>Instalar as bibliotecas necessárias

1. No Visual Studio Code, digite `F1`, em seguida, digite **Arduino** e selecione **Arduino: Gerenciador de biblioteca**.

2. Procure a seguinte biblioteca nomes individualmente. Para cada biblioteca que você encontrar, clique em **Instalar**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Você não tem um sensor DHT22 real?

O aplicativo de exemplo pode simular a temperatura e umidade dados caso você não tenha um sensor DHT22 real. Para configurar o aplicativo de exemplo para usar dados simulados, siga estas etapas:

1. Abra o `config.h` arquivo o `app` pasta.

2. Localize a seguinte linha de código e altere o valor de `false` para `true`:

   ```c
   define SIMULATED_DATA true
   ```

   ![Configurar o aplicativo de exemplo para usar dados simulados](media/iot-hub-arduino-huzzah-esp8266-get-started/15_vscode-configure-app-use-simulated-data.png)

3. Salve o arquivo.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Implantar o aplicativo de exemplo para Feather HUZZAH ESP8266

1. No Visual Studio Code, clique em  **\<selecione Serial Port >** o status da barra e, em seguida, clique na porta serial para Feather HUZZAH ESP8266.

2. Insira `F1`, em seguida, digite **Arduino** e selecione **Arduino: Carregar** para compilar e implantar o aplicativo de exemplo para Feather HUZZAH ESP8266.

### <a name="enter-your-credentials"></a>Inserir suas credenciais

Depois que o upload for concluído com êxito, siga estas etapas para inserir suas credenciais:

1. Abra o IDE Arduino, clique em **Ferramentas** > **Serial Monitor**.

2. Na janela do monitor serial, observe as duas listas suspensas no canto inferior direito.

3. Selecione **nenhuma final de linha** para obter a lista suspensa à esquerda.

4. Selecione **115200 baud** para obter a lista suspensa à direita.

5. Na caixa de entrada localizada na parte superior da janela do monitor serial, insira as seguintes informações se você for solicitado a fornecê-las e clique em **Enviar**.

   * SSID Wi-Fi
   * Senha de Wi-Fi
   * Cadeia de conexão de dispositivo

> [!Note]
> As informações de credencial são armazenadas no EEPROM do Feather HUZZAH ESP8266. Se você clicar no botão Redefinir da placa Feather HUZZAH ESP8266, o aplicativo de exemplo perguntará se você deseja apagar as informações. Insira `Y` para apagar as informações. Você será solicitado a fornecer as informações uma segunda vez.

### <a name="verify-the-sample-application-is-running-successfully"></a>Verificar se o aplicativo de exemplo está sendo executado com êxito

Se você vir a seguinte saída da janela serial monitor e o LED piscando no Feather HUZZAH ESP8266, o aplicativo de exemplo está sendo executado com êxito.

![Saída final no IDE do Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/16_arduino-ide-final-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Ler as mensagens recebidas pelo seu hub

Uma maneira para monitorar mensagens recebidas pelo hub IoT do dispositivo é usar as ferramentas do IoT do Azure para Visual Studio Code. Para obter mais informações, consulte [usar as ferramentas de IoT do Azure para Visual Studio Code para enviar e receber mensagens entre o dispositivo e IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Para obter mais formas processar os dados enviados pelo seu dispositivo, continue na próxima seção.

## <a name="next-steps"></a>Próximas etapas

Você conectou um Feather HUZZAH ESP8266 ao Hub IoT e enviou os dados capturados do sensor para o Hub IoT com êxito.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]