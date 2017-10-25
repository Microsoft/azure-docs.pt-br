---
title: "M0 para a nuvem – conectar o Feather M0 WiFi ao Hub IoT do Azure | Microsoft Docs"
description: "Saiba como configurar e conectar o Adafruit Feather M0 WiFi ao Hub IoT do Azure para enviar dados à plataforma de nuvem do Azure neste tutorial."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/16/2017
ms.author: xshi
ms.openlocfilehash: 9b278735ce3af9e6e61a85c5e95ea218622361c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>Conectar o Adafruit Feather M0 WiFi ao Hub IoT do Azure na nuvem
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Conexão entre BME280, Feather M0 WiFi e Hub IoT](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

Neste tutorial, você começa aprendendo as noções básicas de como trabalhar com sua placa Arduino. Em seguida, aprenderá a conectar seus dispositivos diretamente à nuvem usando o [Hub IoT do Azure](iot-hub-what-is-iot-hub.md).

## <a name="what-you-do"></a>O que fazer

Conecte o Adafruit Feather M0 WiFi a um Hub IoT criado por você. Em seguida, você executa um aplicativo de exemplo no M0 WiFi para coletar dados de temperatura e umidade de um BME280. Por fim, você envia os dados do sensor para o Hub IoT.


## <a name="what-you-learn"></a>O que você aprenderá

* Como criar um Hub IoT e registrar um dispositivo para o Feather M0 WiFi
* Como conectar o Feather M0 WiFi ao sensor e ao seu computador
* Como coletar dados de sensor executando um aplicativo de exemplo no Feather M0 WiFi
* Como enviar os dados do sensor para o Hub IoT

## <a name="what-you-need"></a>O que você precisa

![Partes necessárias para o tutorial](media/iot-hub-adafruit-feather-m0-wifi-get-started/2_parts-needed-for-the-tutorial.png)

Para concluir esta operação, você precisará das seguintes partes do seu Kit de Início do Feather M0 WiFi:

* A placa Feather M0 WiFi
* Um cabo Micro USB para USB Tipo A

Você também precisa destes itens para seu ambiente de desenvolvimento:

* Uma assinatura ativa do Azure. Se não tiver uma conta do Azure, [crie uma conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos.
* Um Mac ou PC que esteja executando Windows ou Ubuntu.
* Uma rede sem fio à qual o Feather M0 WiFi deve se conectar.
* Uma conexão com a Internet para baixar a ferramenta de configuração.
* [IDE do Arduino](https://www.arduino.cc/en/main/software) versão 1.6.8 ou posterior. Versões anteriores não funcionam com a biblioteca do Hub IoT do Azure.

Se você não tiver um sensor, os itens a seguir serão opcionais. Você também tem a opção de usar dados de sensor simulados:

* Um sensor de temperatura e umidade BME280
* Uma placa universal
* Cabos de jumper macho/macho

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>Conectar o Feather M0 WiFi ao sensor e ao seu computador
Nesta seção, você conecta os sensores à sua placa. Em seguida, você conecta o dispositivo ao seu computador para uso posterior.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>Conectar um sensor de temperatura e umidade DHT22 ao Feather M0 WiFi

Use os cabos da placa universal e jumper para fazer a conexão. Se você não tiver um sensor, ignore esta seção porque você pode usar dados de sensor simulado em vez disso.

![Referência de conexões](media/iot-hub-adafruit-feather-m0-wifi-get-started/3_connections_on_breadboard.png)


Use a seguinte fiação para os pinos do sensor:


| Início (sensor)           | Fim (placa)            | Cor do cabo   |
| -----------------------  | ---------------------- | ------------: |
| VDD (pino 27A)            | 3V (pino 3A)            | Cabo vermelho     |
| GND (pino 29A)            | GND (pino 6A)           | Cabo preto   |
| SCK (pino 30A)            | SCK (pino 12A)          | Cabo amarelo  |
| SDO (pino 31A)            | MI (pino 14A)           | Cabo branco   |
| SDI (pino 32A)            | M0 (pino 13A)           | Cabo azul    |
| CS (pino 33A)             | GPIO 5 (pino 15J)       | Cabo laranja  |

Para obter mais informações, consulte [Saídas do sensor de umidade, pressão barométrica e temperatura Adafruit BME280](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout/wiring-and-test?view=all) e [Pinagem do Adafruit Feather M0 WiFi](https://learn.adafruit.com/adafruit-feather-m0-wifi-atwinc1500/pinouts).



Agora seu Feather M0 WiFi deve estar conectado a um sensor ativo.

![Conectar o DHT22 com o Feather Huzzah](media/iot-hub-adafruit-feather-m0-wifi-get-started/4_connect-bme280-feather-m0-wifi.png)

### <a name="connect-feather-m0-wifi-to-your-computer"></a>Conectar o Feather M0 WiFi ao seu computador

Conforme mostrado a seguir, use o cabo Micro USB para USB Tipo A para conectar o Feather M0 WiFi ao seu computador, conforme mostrado:

![Conectar o Feather Huzzah ao seu computador](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Adicionar permissões de porta serial (somente Ubuntu)

Se você usa o Ubuntu, verifique se tem as permissões para operar na porta USB do Feather M0 WiFi. Para adicionar permissões de porta serial, siga estas etapas:


1. Em um terminal, execute os seguintes comandos:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Você recebe uma das seguintes saídas:

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   Na saída, observe se `uucp` ou `dialout` é o nome do proprietário do grupo da porta USB.

2. Para adicionar o usuário ao grupo, execute o seguinte comando:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   Na etapa anterior, você obteve o nome de proprietário do grupo `<group-owner-name>`. Seu nome de usuário do Ubuntu é `<username>`.

3. Saia do Ubuntu e entre novamente para que a alteração seja exibida.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Coletar dados de sensor e enviá-los para o hub IoT

Nesta seção, você implanta e executa um aplicativo de exemplo no Feather M0 WiFi. O aplicativo de exemplo faz o LED piscar no Feather M0 WiFi. Em seguida, ele envia os dados de temperatura e umidade coletados do sensor BME280 para o hub IoT.

### <a name="get-the-sample-application-from-github-and-prepare-the-arduino-ide"></a>Obtenha o aplicativo de exemplo do GitHub e prepare o IDE do Arduino

O aplicativo de exemplo está hospedado no GitHub. Clone o repositório de exemplo que contém o aplicativo de exemplo do GitHub. Para clonar o repositório de exemplo, siga estas etapas:

1. Abra um prompt de comando ou uma janela de terminal.

2. Ir para uma pasta onde você deseja que o aplicativo de exemplo a ser armazenado.
3. Execute o comando a seguir:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

### <a name="install-the-package-for-feather-m0-wifi-in-the-arduino-ide"></a>Instale o pacote para o Feather M0 WiFi no IDE do Arduino

1. Abra a pasta onde o aplicativo de exemplo está armazenado.

2. Abra o arquivo app.ino na pasta do aplicativo no IDE do Arduino.

   ![Abrir o aplicativo de exemplo no IDE do Arduino](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)


1. Clique em **Arquivo** > **Preferências** (Windows/Linux) ou **Arduino** > **Preferências** (Mac) e copie e cole o link abaixo para na opção **URLs adicionais do Gerenciador de Placas** nas preferências do IDE do Arduino.
   
   ```
   https://adafruit.github.io/arduino-board-index/package_adafruit_index.json
   ```

1. Clique em **Ferramentas** > **Placa** > **Gerenciador de Placas** e, em seguida, instale o `Arduino SAMD Boards` versão `1.6.2` ou posterior. 

1. Em seguida, na mesma janela, instale o pacote `Adafruit SAMD Boards` para adicionar as definições do arquivo de placa.

   ![O pacote esp8266 está instalado](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

4. Clique em **Ferramentas** > **Placa** > **Adafruit M0 WiFi**.

5. Instale os drivers (apenas para Windows). Quando você conecta o Feather M0 Wi-Fi, pode precisar instalar um driver. Clique no [link de download na página da Web](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe) para baixar o instalador do driver. Siga as etapas para instalar os drivers que deseja.

### <a name="install-necessary-libraries"></a>Instalar as bibliotecas necessárias

1. No IDE Arduino, clique em **esboço** > **biblioteca incluem** > **gerenciar bibliotecas**.

2. Procure a seguinte biblioteca nomes individualmente. Para cada biblioteca que você localizar, clique em **Instalar**:

   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`

3. Instale o `Adafruit_WINC1500` manualmente. Vá para [este site](https://github.com/adafruit/Adafruit_WINC1500) e clique em **Clonar ou baixar** > **Baixar ZIP**. Em seguida, no IDE do Arduino, acesse **Esboço** > **Incluir Biblioteca** > **Adicionar Biblioteca .zip** e adicione o arquivo zip.

### <a name="use-the-sample-application-if-you-dont-have-a-real-bme280-sensor"></a>Use o aplicativo de exemplo se você não tiver um sensor BME280 real

Caso você não tenha um sensor BME280 real, o aplicativo de exemplo poderá simular os dados de temperatura e umidade. Para configurar o aplicativo de exemplo para usar dados simulados, siga estas etapas:

1. Abra o `config.h` arquivo o `app` pasta.

2. Localize a seguinte linha de código e altere o valor de `false` para `true`:

   ```c
   define SIMULATED_DATA true
   ```
   ![Configurar o aplicativo de exemplo para usar dados simulados](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

3. Salve o arquivo com `Control-s`.

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>Implantar o aplicativo de exemplo para o Feather M0 WiFi

1. No IDE do Arduino, clique em **Ferramenta** > **Porta** e clique na porta serial do Feather M0 WiFi.

2. Clique em **Esboço** > **Carregar** para criar e implantar o aplicativo de exemplo do Feather M0 WiFi.

### <a name="enter-your-credentials"></a>Inserir suas credenciais

Depois que o upload for concluído com êxito, siga estas etapas para inserir suas credenciais:

1. No IDE Arduino, clique em **ferramentas** > **Serial Monitor**.

2. No canto inferior direito da janela do monitor serial, selecione **Nenhum final de linha** na lista suspensa à esquerda.
3. Selecione **115200 baud** na lista suspensa à direita.
4. Na caixa de entrada na parte superior, insira as seguintes informações se você for solicitado a fornecê-las e, em seguida, clique em **Enviar**:

   * SSID Wi-Fi
   * Senha de Wi-Fi
   * Cadeia de conexão de dispositivo

> [!Note]
> As informações de credencial são armazenadas na EEPROM do Feather M0 WiFi. Se você clicar no botão de redefinir na placa do Feather M0 WiFi, o aplicativo de exemplo perguntará se você deseja apagar as informações. Digite `Y` para apagar as informações. Você é solicitado a fornecer as informações uma segunda vez.

### <a name="verify-that-the-sample-application-is-running-successfully"></a>Verifique se o aplicativo de exemplo está sendo executado com êxito

Caso você veja a seguinte saída na janela do monitor serial e o LED piscando no Feather M0 WiFi, o aplicativo de exemplo estará sendo executado com êxito:

![Saída final no IDE do Arduino](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>Próximas etapas

Você conectou um Feather M0 WiFi ao hub IoT e enviou os dados capturados pelo sensor para o hub IoT com sucesso. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

