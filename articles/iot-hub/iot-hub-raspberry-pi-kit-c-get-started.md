---
title: Conectar o Raspberry Pi ao Hub IoT do Azure usando C | Microsoft Docs
description: Saiba como configurar e conectar o Raspberry Pi ao Hub IoT do Azure para o Raspberry Pi enviar dados à plataforma de nuvem do Azure.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.openlocfilehash: 94ac75c4165b11e343ce5c31480a511ebf978a36
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838771"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Conectar o Raspberry Pi ao Hub IoT do Azure (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Neste tutorial, você começará aprendendo as noções básicas de como trabalhar com o Raspberry Pi que está executando o Raspbian. Em seguida, aprenderá a conectar seus dispositivos diretamente à nuvem usando o [Hub IoT do Azure](about-iot-hub.md). Para obter exemplos do Windows 10 IoT Core, acesse o [Centro de Desenvolvimento do Windows](https://www.windowsondevices.com/).

Não tem um dispositivo ainda? Experimente [Simulador online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). Ou compre um novo kit de [aqui](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>O que fazer

* Crie um Hub IoT.

* Registre um dispositivo para o Pi em seu Hub IoT.

* Instale o Raspberry Pi.

* Execute um aplicativo de exemplo no Pi para enviar os dados do sensor para o Hub IoT.

Conecte o Raspberry Pi a um Hub IoT criado por você. Em seguida, execute um aplicativo de exemplo no Pi para coletar dados de temperatura e umidade de um sensor BME280. Por fim, você envia os dados do sensor para o Hub IoT.

## <a name="what-you-learn"></a>O que você aprenderá

* Como criar um Hub IoT do Azure e obter sua nova cadeia de conexão do dispositivo.

* Como conectar o Pi a um sensor BME280.

* Como coletar dados de sensor executando um aplicativo de exemplo no Pi.

* Como enviar dados de sensor ao Hub IoT.

## <a name="what-you-need"></a>O que você precisa

![O que você precisa](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* Da placa do Raspberry Pi 2 ou do Raspberry Pi 3.

* Uma assinatura ativa do Azure. Se não tiver uma conta do Azure, [crie uma conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos.

* Um monitor, um teclado USB e mouse que se conectam ao Pi.

* Um Mac ou PC que esteja executando Windows ou Linux.

* Uma conexão com a Internet.

* Um cartão microSD de 16 GB ou superior.

* Um adaptador USB-SD ou um cartão microSD para gravar a imagem do sistema operacional no cartão microSD.

* Uma fonte de alimentação de 5 volts e 2 amperes com o cabo micro USB de 2,7 metros.

Os itens a seguir são opcionais:

* Um sensor de umidade, temperatura e pressão Adafruit BME280 montado.

* Uma placa universal.

* Cabos de jumper fêmea/macho de 15,2 cm.

* Um LED de 10 mm difuso.

> [!NOTE]
> Esses itens são opcionais, como o código de exemplo dá suporte a dados simulados de sensor.
>

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Configurar o Raspberry Pi

Agora, configure o Raspberry Pi.

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instalar o sistema operacional Raspbian para o Pi

Preparar o cartão microSD para instalação da imagem do Raspbian.

1. Baixe o Raspbian.

   1. [Baixe o Raspbian Stretch com área de trabalho](https://www.raspberrypi.org/downloads/raspbian/) (o arquivo. zip).

   2. Extraia a imagem do Raspbian em uma pasta no computador.

2. Instale o Raspbian no cartão microSD.

   1. [Baixe e instale o utilitário gravador de cartão SD Etcher](https://etcher.io/).

   2. Execute o Etcher e selecione a imagem do Raspbian extraída na etapa 1.

   3. Selecione a unidade de cartão microSD. Observação que o Etcher talvez já tenha selecionado a unidade correta.

   4. Clique em Flash para instalar o Raspbian no cartão microSD.

   5. Remova o cartão microSD do computador após a conclusão. É seguro remover o cartão microSD diretamente porque o Etcher ejeta ou desmonta automaticamente o cartão microSD após a conclusão.

   6. Insira o cartão microSD no Pi.

### <a name="enable-ssh-and-spi"></a>Habilitar SSH e SPI

1. Conectar o Pi ao monitor, teclado e mouse, inicie o Pi e, em seguida, entre no Raspbian usando `pi` como o nome de usuário e `raspberry` como a senha.
 
2. Clique no ícone do Raspberry > **Preferências** > **Configuração do Raspberry Pi**.

   ![O menu de Preferências do Raspbian](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. Na guia **Interfaces**, defina **SPI** e **SSH** como **Habilitar** e, em seguida, clique em **OK**. Se você não tiver sensores físicos e quiser usar dados de sensor simulados, esta etapa será opcional.

   ![Habilitar SPI e SSH no Raspberry Pi](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> Para habilitar o SSH e o SPI, você pode encontrar mais documentos de referência em [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) e [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).
>

### <a name="connect-the-sensor-to-pi"></a>Conectar o sensor ao Pi

Use a placa universal e os cabos de jumper para conectar um LED e um BME280 ao Pi, da seguinte maneira. Se você não tiver o sensor, [ignore esta seção](#connect-pi-to-the-network).

![A conexão do Raspberry Pi e do sensor](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

O sensor BME280 pode coletar dados de temperatura e umidade. E o LED piscará se houver uma comunicação entre o dispositivo e a nuvem.

Para os pinos do sensor, use a seguinte fiação:

| Início (Sensor e LED)     | End (quadro)            | Cor de cabo   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (pino 5G)         | GPIO 4 (pino 7)         | Cabo branco   |
| LED GND (pino 6G)         | GND (pino 6)            | Cabo preto   |
| VDD (pino 18F)            | 3,3 v PWR (pino 17)      | Cabo branco   |
| GND (pino 20F)            | GND (pino 20)           | Cabo preto   |
| SCK (pino 21F)            | SPI0 SCLK (pino 23)     | Cabo laranja  |
| SDO (pino 22F)            | SPI0 MISO (pino 21)     | Cabo amarelo  |
| SDI (pino 23F)            | SPI0 MOSI (pino 19)     | Cabo verde   |
| CS (pino 24F)             | SPI0 CS (pino 24)       | Cabo azul    |

Clique para exibir os [mapeamentos de pinos do Raspberry Pi 2 e 3](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) para referência.

Depois de conectar com êxito o BME280 ao Raspberry Pi, ele deve ficar semelhante à imagem abaixo.

![Pi e BME280 conectados](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Conectar Pi à rede

Ligue o Pi usando o cabo micro USB e a fonte de alimentação. Use o cabo Ethernet para conectar o Pi à sua rede com fio ou siga as [instruções da Raspberry Pi Foundation](https://www.raspberrypi.org/learning/software-guide/wifi/) para conectar o Pi à sua rede sem fio. Depois que o Pi tiver se conectado com êxito à rede, você precisará observar o [endereço IP do Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Conectado à rede com fio](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>Executar um aplicativo de exemplo no Pi

### <a name="sign-into-your-raspberry-pi"></a>Logon no seu Raspberry Pi

1. Use um dos seguintes clientes SSH do seu computador host para se conectar ao Raspberry Pi.
   
   **Usuários do Windows**
   1. Baixe e instale o [PuTTY](https://www.putty.org/) para Windows. 
   1. Copie o endereço IP do seu Pi para a seção Nome do host (ou Endereço IP) e selecione SSH como o tipo de conexão.
   
   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Usuários de Mac e do Ubuntu**

   Use o cliente SSH interno no Ubuntu ou macOS. Talvez você precise executar `ssh pi@<ip address of pi>` para conectar o Pi via SSH.
   > [!NOTE]
   > O nome de usuário padrão é `pi` e a senha é `raspberry`.


### <a name="configure-the-sample-application"></a>Configurar o aplicativo de exemplo

1. Clone o aplicativo de exemplo executando o seguinte comando:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Execute o script de instalação:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Se você **não tiver um BME280 físico**, poderá usar '--simulated-data' como um parâmetro de linha de comando para simular os dados de temperatura e umidade. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>Crie e execute o aplicativo de exemplo

1. Crie o aplicativo de exemplo, executando o seguinte comando:

   ```bash
   cmake . && make
   ```
   
   ![Saída do build](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. Execute o aplicativo de exemplo com seguinte comando:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Verifique se você copiou e colou a cadeia de conexão do dispositivo em aspas simples.
   >

Você deverá ver a seguinte saída, mostrando os dados do sensor e as mensagens que são enviadas ao seu Hub IoT.

![Saída – dados de sensor enviados do Raspberry Pi para o seu Hub IoT](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Ler as mensagens recebidas pelo seu hub

Uma maneira para monitorar mensagens recebidas pelo hub IoT do dispositivo é usar as ferramentas do IoT do Azure para Visual Studio Code. Para obter mais informações, consulte [usar as ferramentas de IoT do Azure para Visual Studio Code para enviar e receber mensagens entre o dispositivo e IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Para obter mais formas processar os dados enviados pelo seu dispositivo, continue na próxima seção.

## <a name="next-steps"></a>Próximas etapas

Você executou um aplicativo de exemplo para coletar dados de sensor e enviá-los ao seu Hub IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
