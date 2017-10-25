---
title: ESP8266 para a nuvem - Conectar o Feather HUZZAH ESP8266 ao Hub IoT do Azure | Microsoft Docs
description: Saiba como configurar e conectar Adafruit Feather HUZZAH ESP8266 para Hub IoT do Azure para ele para enviar dados para a plataforma de nuvem do Azure neste tutorial.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.assetid: c505aacf-89a8-40ed-a853-493b75bec524
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2017
ms.author: xshi
ms.openlocfilehash: 6a450579c848fe6030a328ddf410f139baae2324
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Conectar-se o Adafruit Feather HUZZAH ESP8266 ao Hub IoT do Azure na nuvem

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Conexão entre o DHT22, o Feather HUZZAH ESP8266 e o Hub IoT](media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>O que fazer


Conecte o Adafruit Feather HUZZAH ESP8266 a um Hub IoT criado. Em seguida, você executa um aplicativo de exemplo no ESP8266 para coletar os dados de temperatura e de umidade de um sensor DHT22. Por fim, você envia os dados do sensor para o Hub IoT.

> [!NOTE]
> Se você estiver usando outras placas ESP8266, ainda poderá seguir estas etapas para conectá-las ao Hub IoT. Dependendo da placa ESP8266 utilizada, talvez seja necessário reconfigurar o `LED_PIN`. Por exemplo, se você estiver usando ESP8266 da AI-Thinker, poderá alterá-la de `0` para `2`. Não tem um dispositivo ainda? Obtenha-o no [site do Azure](http://azure.com/iotstarterkits).




## <a name="what-you-learn"></a>O que você aprenderá

* Como criar um Hub IoT e registrar um dispositivo no Feather HUZZAH ESP8266
* Como conectar o Feather HUZZAH ESP8266 ao sensor e ao computador
* Como coletar dados do sensor executando um aplicativo de exemplo no Feather HUZZAH ESP8266
* Como enviar os dados do sensor para o Hub IoT

## <a name="what-you-need"></a>O que você precisa

![Partes necessárias para o tutorial](media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

Para concluir esta operação, você precisará das seguintes partes do seu Kit de Início do Feather HUZZAH ESP8266:

* A placa Feather HUZZAH ESP8266
* Um cabo Micro USB para USB Tipo A

Você também precisa destes itens para seu ambiente de desenvolvimento:

* Uma assinatura ativa do Azure. Se não tiver uma conta do Azure, [crie uma conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos.
* Mac ou PC que esteja executando o Windows ou Ubuntu.
* Rede sem fio à qual Feather HUZZAH ESP8266 deve se conectar.
* Uma conexão com a Internet para baixar a ferramenta de configuração.
* [IDE do Arduino](https://www.arduino.cc/en/main/software) versão 1.6.8 ou posterior. As versões anteriores não funcionam com a biblioteca do AzureIoT.

Os itens a seguir são opcionais, caso você não tenha um sensor. Você também tem a opção de usar dados de sensor simulada.

* Um sensor de temperatura e umidade Adafruit DHT22
* Uma placa universal
* Cabos de jumper de M/M


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Difusão HUZZAH ESP8266 de conexão com o sensor e seu computador
Nesta seção, você conecta os sensores à sua placa. Em seguida, você conecta o dispositivo ao computador para uso posterior.
### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Conecte um sensor de temperatura e umidade DHT22 a Feather HUZZAH ESP8266

Use os cabos da placa universal e jumper para fazer a conexão da seguinte maneira. Se você não tiver um sensor, ignore esta seção porque você pode usar dados de sensor simulado em vez disso.

![Referência de conexões](media/iot-hub-arduino-huzzah-esp8266-get-started/15_connections_on_breadboard.png)


Para os pinos do sensor, use a seguinte fiação:


| Início (Sensor)           | End (quadro)           | Cor de cabo   |
| -----------------------  | ---------------------- | ------------: |
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

1. Adicione o usuário ao grupo, executando o seguinte comando:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` é o nome de proprietário de grupo que você obteve na etapa anterior. `<username>` é o nome de usuário do Ubuntu.

1. Saia do Ubuntu e entre novamente para que a alteração seja exibida.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Coletar dados de sensor e enviá-los para o hub IoT

Nesta seção, implante e execute um aplicativo de exemplo em Feather HUZZAH ESP8266. O aplicativo de exemplo pisca o LED do Feather HUZZAH ESP8266 e envia os dados de temperatura e umidade coletados do sensor DHT22 para o Hub IoT.

### <a name="get-the-sample-application-from-github"></a>Obter o aplicativo de exemplo do GitHub

O aplicativo de exemplo está hospedado no GitHub. Clone o repositório de exemplo que contém o aplicativo de exemplo do GitHub. Para clonar o repositório de exemplo, siga estas etapas:

1. Abra um prompt de comando ou uma janela de terminal.
1. Ir para uma pasta onde você deseja que o aplicativo de exemplo a ser armazenado.
1. Execute o comando a seguir:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

Instale o pacote do Feather HUZZAH ESP8266 no IDE do Arduino:

1. Abra a pasta onde o aplicativo de exemplo está armazenado.
1. Abra o arquivo app.ino na pasta do aplicativo no IDE do Arduino.

   ![Abrir o aplicativo de exemplo no IDE do Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/10_arduino-ide-open-sample-app.png)

1. No IDE Arduino, clique em **arquivo** > **preferências**.
1. Na caixa de diálogo **Preferências**, clique no ícone ao lado da caixa **URLs Adicionais do Gerenciador de Placas**.
1. Na janela pop-up, insira a URL a seguir e clique em **OK**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![Apontar para uma URL do pacote no IDE do Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/11_arduino-ide-package-url.png)

1. No **preferência** caixa de diálogo, clique em **OK**.
1. Clique em **Ferramentas** > **Placa** > **Gerenciador de Placas** e procure esp8266.

   O Gerenciador de Placas indica que ESP8266 com uma versão 2.2.0 ou posterior está instalado.

   ![O pacote do esp8266 é instalado](media/iot-hub-arduino-huzzah-esp8266-get-started/12_arduino-ide-esp8266-installed.png)

1. Clique em **ferramentas** > **placa** > **Adafruit HUZZAH ESP8266**.

### <a name="install-necessary-libraries"></a>Instalar as bibliotecas necessárias

1. No IDE Arduino, clique em **esboço** > **biblioteca incluem** > **gerenciar bibliotecas**.
1. Procure a seguinte biblioteca nomes individualmente. Para cada biblioteca que você localizar, clique em **Instalar**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Você não tem um sensor DHT22 real?

O aplicativo de exemplo pode simular a temperatura e umidade dados caso você não tenha um sensor DHT22 real. Para configurar o aplicativo de exemplo para usar dados simulados, siga estas etapas:

1. Abra o `config.h` arquivo o `app` pasta.
1. Localize a seguinte linha de código e altere o valor de `false` para `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Configurar o aplicativo de exemplo para usar dados simulados](media/iot-hub-arduino-huzzah-esp8266-get-started/13_arduino-ide-configure-app-use-simulated-data.png)

1. Salve o arquivo com `Control-s`.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Implantar o aplicativo de exemplo para Feather HUZZAH ESP8266

1. No IDE Arduino, clique em **Ferramenta** > **Porta** e clique na porta serial para Feather HUZZAH ESP8266.
1. Clique em **esboço** > **carregar** para criar e implantar o aplicativo de exemplo para Feather HUZZAH ESP8266.

### <a name="enter-your-credentials"></a>Inserir suas credenciais

Depois que o upload for concluído com êxito, siga estas etapas para inserir suas credenciais:

1. No IDE Arduino, clique em **ferramentas** > **Serial Monitor**.
1. Na janela do monitor serial, observe as duas listas suspensas no canto inferior direito.
1. Selecione **nenhuma final de linha** para obter a lista suspensa à esquerda.
1. Selecione **115200 baud** para obter a lista suspensa à direita.
1. Na caixa de entrada localizada na parte superior da janela do monitor serial, insira as seguintes informações se você for solicitado a fornecê-las e clique em **Enviar**.
   * SSID Wi-Fi
   * Senha de Wi-Fi
   * Cadeia de conexão de dispositivo

> [!Note]
> As informações de credencial são armazenadas no EEPROM do Feather HUZZAH ESP8266. Se você clicar no botão Redefinir da placa Feather HUZZAH ESP8266, o aplicativo de exemplo perguntará se você deseja apagar as informações. Insira `Y` para apagar as informações. Você será solicitado a fornecer as informações uma segunda vez.

### <a name="verify-the-sample-application-is-running-successfully"></a>Verificar se o aplicativo de exemplo está sendo executado com êxito

Se você vir a seguinte saída da janela serial monitor e o LED piscando no Feather HUZZAH ESP8266, o aplicativo de exemplo está sendo executado com êxito.

![Saída final no IDE do Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>Próximas etapas

Você conectou um Feather HUZZAH ESP8266 ao Hub IoT e enviou os dados capturados do sensor para o Hub IoT com êxito. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

