---
title: "Raspberry Pi para nuvem (Node.js) – Conectar o Raspberry Pi ao Hub IoT do Azure | Microsoft Docs"
description: "Neste tutorial, aprenda a configurar e conectar o Raspberry Pi ao Hub IoT do Azure para o Raspberry Pi enviar dados à plataforma de nuvem do Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: raspberry pi azure iot, hub iot raspberry pi, raspberry pi enviar dados para a nuvem, raspberry pi para nuvem
ms.assetid: b0e14bfa-8e64-440a-a6ec-e507ca0f76ba
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 9/14/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7bf423fd05d6651bf16693e6d6930fada8b5da70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Conectar o Raspberry Pi ao Hub IoT do Azure (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Neste tutorial, você começará aprendendo as noções básicas de como trabalhar com o Raspberry Pi que está executando o Raspbian. Em seguida, aprenderá a conectar seus dispositivos diretamente à nuvem usando o [Hub IoT do Azure](iot-hub-what-is-iot-hub.md). Para obter exemplos do Windows 10 IoT Core, acesse o [Centro de Desenvolvimento do Windows](http://www.windowsondevices.com/).

Não tem um dispositivo ainda? Experimente [Simulador online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). Ou compre um novo kit de [aqui](https://azure.microsoft.com/develop/iot/starter-kits).


## <a name="what-you-do"></a>O que fazer

* Crie um Hub IoT.
* Registre um dispositivo para o Pi em seu Hub IoT.
* Configure o Raspberry Pi.
* Execute um aplicativo de exemplo no Pi para enviar os dados do sensor para o Hub IoT.

## <a name="what-you-learn"></a>O que você aprenderá

* Como criar um Hub IoT do Azure e obter sua nova cadeia de conexão do dispositivo.
* Como conectar o Pi a um sensor BME280.
* Como coletar dados de sensor executando um aplicativo de exemplo no Pi.
* Como enviar dados de sensor ao Hub IoT.

## <a name="what-you-need"></a>O que você precisa

![O que você precisa](media/iot-hub-raspberry-pi-kit-node-get-started/0_starter_kit.jpg)

* Uma placa Raspberry Pi 2 ou Raspberry Pi 3.
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
Se você não tiver os itens opcionais, poderá usar dados de sensor simulados.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="set-up-raspberry-pi"></a>Configurar o Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instalar o sistema operacional Raspbian para o Pi

Preparar o cartão microSD para instalação da imagem do Raspbian.

1. Baixe o Raspbian.
   1. [Baixe o Raspbian Stretch](http://downloads.raspberrypi.org/raspbian/images/raspbian-2017-07-05/) (o arquivo .zip).

   > [!WARNING]
   > Use o link acima para baixar a imagem zip `raspbian-2017-07-5`. A versão mais recente das imagens Raspbian tem alguns problemas conhecidos com o nó Wiring-Pi, que podem causar falhas nas próximas etapas.
   1. Extraia a imagem do Raspbian em uma pasta no computador.

1. Instale o Raspbian no cartão microSD.
   1. [Baixe e instale o utilitário gravador de cartão SD Etcher](https://etcher.io/).
   1. Execute o Etcher e selecione a imagem do Raspbian extraída na etapa 1.
   1. Selecione a unidade de cartão microSD. O Etcher talvez já tenha selecionado a unidade correta.
   1. Clique em Flash para instalar o Raspbian no cartão microSD.
   1. Remova o cartão microSD do computador após a conclusão. É seguro remover o cartão microSD diretamente porque o Etcher ejeta ou desmonta automaticamente o cartão microSD após a conclusão.
   1. Insira o cartão microSD no Pi.

### <a name="enable-ssh-and-i2c"></a>Habilitar SSH e I2C

1. Conecte o Pi ao monitor, ao teclado e ao mouse. 
1. Inicie o Pi e, em seguida, faça logon no Raspbian usando `pi` como o nome de usuário e `raspberry` como a senha.
1. Clique no ícone do Raspberry > **Preferências** > **Configuração do Raspberry Pi**.

   ![O menu de Preferências do Raspbian](media/iot-hub-raspberry-pi-kit-node-get-started/1_raspbian-preferences-menu.png)

1. Na guia **Interfaces**, defina **I2C** e **SSH** como **Habilitar** e, em seguida, clique em **OK**. Se você não tiver sensores físicos e quiser usar dados de sensor simulados, esta etapa será opcional.

   ![Habilitar I2C e SSH no Raspberry Pi](media/iot-hub-raspberry-pi-kit-node-get-started/2_enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE] 
Para habilitar o SSH e o I2C, você pode encontrar mais documentos de referência em [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) e [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Conectar o sensor ao Pi

Use a placa universal e os cabos de jumper para conectar um LED e um BME280 ao Pi, da seguinte maneira. Se você não tiver o sensor, [ignore esta seção](#connect-pi-to-the-network).

![A conexão do Raspberry Pi e do sensor](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

O sensor BME280 pode coletar dados de temperatura e umidade. O LED pisca quando o dispositivo envia uma mensagem para a nuvem. 

Use a seguinte fiação para os pinos do sensor:

| Início (Sensor e LED)     | End (quadro)            | Cor de cabo   |
| -----------------------  | ---------------------- | ------------: |
| VDD (pino 5G)             | 3,3 v PWR (pino 1)       | Cabo branco   |
| GND (pino 7G)             | GND (pino 6)            | Cabo marrom   |
| SDI (pino 10G)            | I2C1 SDA (pino 3)       | Cabo vermelho     |
| SCK (pino 8G)             | I2C1 SCL (pino 5)       | Cabo laranja  |
| LED VDD (pino 18F)        | GPIO 24 (pino 18)       | Cabo branco   |
| LED GND (pino 17F)        | GND (pino 20)           | Cabo preto   |

Clique para exibir os [mapeamentos de pinos do Raspberry Pi 2 e 3](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) para referência.

Depois de conectar com êxito o BME280 ao Raspberry Pi, ele deve ficar semelhante à imagem abaixo.

![Pi e BME280 conectados](media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Conectar Pi à rede

Ligue o Pi usando o cabo micro USB e a fonte de alimentação. Use o cabo Ethernet para conectar o Pi à sua rede com fio ou siga as [instruções da Raspberry Pi Foundation](https://www.raspberrypi.org/learning/software-guide/wifi/) para conectar o Pi à sua rede sem fio. Depois que o Pi tiver se conectado com êxito à rede, você precisará observar o [endereço IP do Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Conectado à rede com fio](media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)

> [!NOTE]
> Verifique se o Pi está conectado à mesma rede que o computador. Por exemplo, se o computador estiver conectado a uma rede sem fio enquanto o Pi estiver conectado a uma rede com fio, talvez você não veja o endereço IP na saída devdisco.

## <a name="run-a-sample-application-on-pi"></a>Executar um aplicativo de exemplo no Pi

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Clonar o aplicativo de exemplo e instalar os pacotes de pré-requisito

1. Conecte-se ao Raspberry Pi com um dos seguintes clientes SSH do seu computador host:
   
   **Usuários do Windows**
   1. Baixe e instale o [PuTTY](http://www.putty.org/) para Windows. 
   1. Copie o endereço IP do seu Pi para a seção Nome do host (ou Endereço IP) e selecione SSH como o tipo de conexão.
   
   ![PuTTy](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Usuários de Mac e do Ubuntu**
   
   Use o cliente SSH interno no Ubuntu ou macOS. Talvez você precise executar `ssh pi@<ip address of pi>` para conectar o Pi via SSH.
   > [!NOTE] 
   O nome de usuário padrão é `pi` e a senha é `raspberry`.

1. Instale o Node.js e o NPM em seu Pi.
   
   Primeiro, verifique a versão do Node.js. 
   
   ```bash
   node -v
   ```

   Se a versão for inferior a 4.x ou se não houver nenhum Node.js no seu Pi, instale a versão mais recente.

   ```bash
   curl -sL http://deb.nodesource.com/setup_4.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

1. Clone o aplicativo de exemplo.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

1. Instale todos os pacotes para o exemplo. A instalação inclui o SDK do dispositivo IoT do Azure, a biblioteca do Sensor BME280 e a biblioteca de fiação do Pi.

   ```bash
   cd iot-hub-node-raspberrypi-client-app
   sudo npm install
   ```
   > [!NOTE] 
   O processo de instalação poderá levar alguns minutos para ser concluído, dependendo da sua conexão de rede.

### <a name="configure-the-sample-application"></a>Configurar o aplicativo de exemplo

1. Abra o arquivo de configuração executando os seguintes comandos:

   ```bash
   nano config.json
   ```

   ![Arquivo de configuração](media/iot-hub-raspberry-pi-kit-node-get-started/6_config-file.png)

   Há dois itens que podem ser configurados nesse arquivo. O primeiro é `interval`, que define o intervalo de tempo (em milissegundos) entre mensagens enviadas para a nuvem. O segundo, o `simulatedData`, é um valor booliano para definir se os dados simulados de sensor serão usados ou não.

   Se você **não tiver o sensor**, defina o valor `simulatedData` como `true` para fazer com que o aplicativo de exemplo crie e use dados simulados de sensor.

1. Salve e saia digitando Control-O > Enter > Control-X.

### <a name="run-the-sample-application"></a>Executar o aplicativo de exemplo

Execute o aplicativo de exemplo com seguinte comando:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   Verifique se você copiou e colou a cadeia de conexão do dispositivo em aspas simples.


Você deverá ver a seguinte saída, mostrando os dados do sensor e as mensagens que são enviadas ao seu Hub IoT.

![Saída – dados de sensor enviados do Raspberry Pi para o seu Hub IoT](media/iot-hub-raspberry-pi-kit-node-get-started/8_run-output.png)

## <a name="next-steps"></a>Próximas etapas

Você executou um aplicativo de exemplo para coletar dados de sensor e enviá-los ao seu Hub IoT. Para ver as mensagens que o seu Raspberry Pi enviou ao seu Hub IoT ou enviar mensagens para o Raspberry Pi em uma interface de linha de comando, consulte o [tutorial Gerenciar mensagens em dispositivo de nuvem com o gerenciador de Hubs IoT](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
