---
title: Intel Edison para nuvem (Node.js) - Conectar o Intel Edison ao Hub IoT do Azure | Microsoft Docs
description: Neste tutorial, aprenda a configurar e conectar o Intel Edison ao Hub IoT do Azure para o Intel Edison enviar dados para a plataforma de nuvem do Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: azure iot intel edison, intel edison iot hub, intel edison envia dados para a nuvem, intel edison para nuvem
ms.assetid: a7c9cf2d-c102-41b0-aa45-41285c6877eb
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/15/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5a31efba704045196b5563f7bc467c773bea7805
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-intel-edison-to-azure-iot-hub-nodejs"></a>Conectar o Intel Edison ao Hub IoT do Azure (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Neste tutorial, você começa aprendendo as noções básicas de como trabalhar com o Intel Edison. Em seguida, aprenderá a conectar seus dispositivos diretamente à nuvem usando o [Hub IoT do Azure](iot-hub-what-is-iot-hub.md).

Não tem um dispositivo ainda? Comece [aqui](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>O que fazer

* Configure os módulos do Intel Edison e do Grove.
* Crie um Hub IoT.
* Registre um dispositivo para o Edison em seu Hub IoT.
* Execute um aplicativo de exemplo no Edison para enviar os dados do sensor para o Hub IoT.

Conecte o Intel Edison a um Hub IoT criado por você. Em seguida, execute um aplicativo de exemplo no Edison para coletar dados de temperatura e umidade de um sensor de temperatura do Grove. Por fim, você envia os dados do sensor para o Hub IoT.

## <a name="what-you-learn"></a>O que você aprenderá

* Como criar um Hub IoT do Azure e obter sua nova cadeia de conexão do dispositivo.
* Como conectar o Edison a um sensor de temperatura do Grove.
* Como coletar dados de sensor executando um aplicativo de exemplo no Edison.
* Como enviar dados de sensor ao Hub IoT.

## <a name="what-you-need"></a>O que você precisa

![O que você precisa](media/iot-hub-intel-edison-kit-node-get-started/0_kit.png)

* A placa Intel Edison
* Placa de expansão Arduino
* Uma assinatura ativa do Azure. Se não tiver uma conta do Azure, [crie uma conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos.
* Um Mac ou PC que esteja executando Windows ou Linux.
* Uma conexão com a Internet.
* Um cabo USB do tipo Micro B para Tipo A
* Uma fonte de alimentação CC (corrente contínua). A fonte de alimentação deve ser classificada da seguinte maneira:
  - 7-15V CC
  - Pelo menos 1500 mA
  - O pino central/interno deve ser o polo positivo da fonte de alimentação

Os itens a seguir são opcionais:

* Grove Base Shield V2
* Grove - Sensor de temperatura
* Cabo do Grove
* As barras separadoras ou parafusos incluídos no pacote, incluindo dois parafusos para fixar o módulo à placa de expansão e quatro conjuntos de parafusos e espaçadores plásticos.

> [!NOTE] 
Esses itens são opcionais porque o exemplo de código dá suporte a dados simulados de sensor.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-intel-edison"></a>Configurar o Intel Edison

### <a name="assemble-your-board"></a>Montar sua placa

Esta seção contém etapas para anexar o módulo Intel® Edison à sua placa de expansão.

1. Coloque o módulo Intel® Edison dentro do contorno branco na placa de expansão, alinhando os orifícios no módulo aos parafusos na placa de expansão.

2. Pressione o módulo logo abaixo das palavras `What will you make?` até sentir um clique.

   ![montar a placa 2](media/iot-hub-intel-edison-kit-node-get-started/1_assemble_board2.jpg)

3. Use as duas porcas sextavadas (incluídas no pacote) para prender o módulo à placa de expansão.

   ![montar a placa 3](media/iot-hub-intel-edison-kit-node-get-started/2_assemble_board3.jpg)

4. Insira um parafuso em um dos quatro orifícios nos cantos na placa de expansão. Gire e aperte um dos espaçadores plásticos brancos no parafuso.

   ![montar a placa 4](media/iot-hub-intel-edison-kit-node-get-started/3_assemble_board4.jpg)

5. Repita para os espaçadores dos outros três cantos.

   ![montar a placa 5](media/iot-hub-intel-edison-kit-node-get-started/4_assemble_board5.jpg)

Agora, sua placa está montada.

   ![montar a placa](media/iot-hub-intel-edison-kit-node-get-started/5_assembled_board.jpg)

### <a name="connect-the-grove-base-shield-and-the-temperature-sensor"></a>Conectar o Grove Base Shield e o sensor de temperatura

1. Coloque o Grove Base Shield em sua placa. Verifique se todos os pinos estão totalmente conectados à sua placa.
   
   ![Grove Base Shield](media/iot-hub-intel-edison-kit-node-get-started/6_grove_base_sheild.jpg)

2. Use o Cabo do Grove para conectar o sensor de temperatura do Grove à porta **A0** do Grove Base Shield.

   ![Conectar ao sensor de temperatura](media/iot-hub-intel-edison-kit-node-get-started/7_temperature_sensor.jpg)

   ![Conexão do Edison e do sensor](media/iot-hub-intel-edison-kit-node-get-started/16_edion_sensor.png)

Agora seu sensor está pronto.

### <a name="power-up-edison"></a>Ligar o Edison

1. Conecte à fonte de alimentação.

   ![Conectar à fonte de alimentação](media/iot-hub-intel-edison-kit-node-get-started/8_plug_power.jpg)

2. Um LED verde (rotulado como DS1 na placa de expansão Arduino*) deve acender e permanecer aceso.

3. Aguarde um minuto para a placa concluir a inicialização.

   > [!NOTE]
   > Se não tiver uma fonte de alimentação CC, você ainda poderá ligar a placa usando uma porta USB. Confira a seção `Connect Edison to your computer` para obter detalhes. Ligar sua placa dessa maneira pode resultar em um comportamento imprevisível da placa, especialmente ao usar Wi-Fi ou motores de acionamento.

### <a name="connect-edison-to-your-computer"></a>Conectar o Edison ao computador

1. Mude a posição do microcomutador para baixo, na direção das duas portas micro USB, para que o Edison fique no modo de dispositivo. Veja [aqui](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode) as diferenças entre o modo de dispositivo e o modo de host.

   ![Mudar a posição do microcomutador para baixo](media/iot-hub-intel-edison-kit-node-get-started/9_toggle_down_microswitch.jpg)

2. Conecte o cabo micro USB à porta micro USB superior.

   ![Porta micro USB superior](media/iot-hub-intel-edison-kit-node-get-started/10_top_usbport.jpg)

3. Conecte a outra extremidade do cabo USB ao computador.

   ![USB do computador](media/iot-hub-intel-edison-kit-node-get-started/11_computer_usb.jpg)

4. Você saberá que a placa foi inicializada completamente quando o computador montar uma nova unidade (muito semelhante a inserir um cartão SD no computador).

## <a name="download-and-run-the-configuration-tool"></a>Baixe e execute a ferramenta de configuração
Obtenha a ferramenta de configuração mais recente [deste link](https://software.intel.com/en-us/iot/hardware/edison/downloads), listada sob o título `Installers`. Execute a ferramenta e siga as instruções na tela, clicando em Avançar quando necessário

### <a name="flash-firmware"></a>Atualizar o firmware
1. Na página `Set up options`, clique em `Flash Firmware`.
2. Selecione a imagem para atualizar sua placa seguindo um destes procedimentos:
   - Para baixar e atualizar sua placa com a imagem de firmware mais recente disponível da Intel, selecione `Download the latest image version xxxx`.
   - Para atualizar sua placa com uma imagem que você já salvou no computador, selecione `Select the local image`. Navegue e selecione a imagem com que você quer atualizar a placa.
3. A ferramenta de configuração tentará atualizar a placa. Todo o processo de atualização pode levar até 10 minutos.

### <a name="set-password"></a>Definir senha
1. Na página `Set up options`, clique em `Enable Security`.
2. É possível definir um nome personalizado para sua placa Intel® Edison. Isso é opcional.
3. Digite uma senha para a placa e clique em `Set password`.
4. Marque a senha, que será usada mais tarde.

### <a name="connect-wi-fi"></a>Conectar ao Wi-Fi
1. Na página `Set up options`, clique em `Connect Wi-Fi`. Aguarde até um minuto para que seu computador identifique as redes Wi-Fi disponíveis.
2. Na lista suspensa `Detected Networks`, selecione a rede.
3. Na lista suspensa `Security`, selecione o tipo de segurança da rede.
4. Forneça suas informações de logon e senha e clique em `Configure Wi-Fi`.
5. Marque o endereço IP, que será usado mais tarde.

> [!NOTE]
> Verifique se o Edison está conectado à mesma rede que o computador. Seu computador se conecta ao Edison usando o endereço IP.

   ![Conectar ao sensor de temperatura](media/iot-hub-intel-edison-kit-node-get-started/12_configuration_tool.png)

Parabéns! Você configurou o Edison com êxito.

## <a name="run-a-sample-application-on-intel-edison"></a>Executar um exemplo de aplicativo no Intel Edison

### <a name="prepare-the-azure-iot-device-sdk"></a>Preparar o SDK do dispositivo IoT do Azure

1. Use um dos seguintes clientes SSH do seu computador host para se conectar ao Intel Edison. O endereço IP é da ferramenta de configuração, e a senha é aquela que você definiu na ferramenta.
    - [PuTTY](http://www.putty.org/) para Windows.
    - O cliente SSH interno no Ubuntu ou macOS.

2. Clone o exemplo de aplicativo cliente com seu dispositivo. 
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-intel-edison-client-app
   ```

3. Em seguida, navegue até a pasta do repositório para executar o comando a seguir para instalar todos os pacotes. Isso levará alguns minutos para ser concluído.
   
   ```bash
   cd iot-hub-node-intel-edison-client-app
   npm install
   ```


### <a name="configure-and-run-the-sample-application"></a>Configurar e executar um aplicativo de exemplo

1. Abra o arquivo de configuração executando os seguintes comandos:

   ```bash
   nano config.json
   ```

   ![Arquivo de configuração](media/iot-hub-intel-edison-kit-node-get-started/13_configure_file.png)

   Há duas macros que você pode configurar nesse arquivo. A primeira é a `INTERVAL`, que define o intervalo de tempo entre duas mensagens que são enviadas para a nuvem. O segundo, o `simulatedData`, é um valor booliano para definir se os dados simulados de sensor serão usados ou não.

   Se você **não tiver o sensor**, defina o valor `simulatedData` como `true` para fazer com que o aplicativo de exemplo crie e use dados simulados de sensor.

1. Salve e saia pressionando CTRL+O > ENTER > CTRL+X.


1. Execute o aplicativo de exemplo com seguinte comando:

   ```bash
   sudo node index.js '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Verifique se você copiou e colou a cadeia de conexão do dispositivo em aspas simples.

Você deverá ver a seguinte saída, mostrando os dados do sensor e as mensagens que são enviadas ao seu Hub IoT.

![Saída – dados de sensor enviados do Intel Edison para o seu Hub IoT](media/iot-hub-intel-edison-kit-node-get-started/15_message_sent.png)

## <a name="next-steps"></a>Próximas etapas

Você executou um aplicativo de exemplo para coletar dados de sensor e enviá-los ao seu Hub IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
