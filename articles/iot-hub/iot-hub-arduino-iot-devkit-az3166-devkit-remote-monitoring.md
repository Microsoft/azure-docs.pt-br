---
title: Kit de Desenvolvimento de IoT para nuvem -- Conectar Kit de Desenvolvimento MXChip de IoT para Hub IoT | Microsoft Docs
description: Neste tutorial, saiba como enviar status de sensores em IoT DevKit AZ3166 para o acelerador de solução de monitoramento remoto do Azure IoT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: ae8dc263e08528c6e3b3bae8c779162c96d51f43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61324127"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Conectar o MXChip IoT DevKit ao acelerador de solução de Monitoramento Remoto do Azure IoT

Neste tutorial, você aprenderá como executar um aplicativo de exemplo em DevKit para enviar os dados do sensor para o seu acelerador de solução de Monitoramento Remoto do Azure IoT.

O [MXChip IoT DevKit](https://aka.ms/iot-devkit) é uma placa compatível com Arduino all-in-one periféricos e sensores avançados. É possível desenvolvê-lo usando a [extensão do Visual Studio Code para Arduino](https://aka.ms/arduino). E vem com um [catálogo de projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) crescente para guiá-lo no protótipo de soluções de IoT (Internet das Coisas) que aproveitam os serviços do Microsoft Azure.

## <a name="what-you-need"></a>O que você precisa

Siga o [Guia de Introdução](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) para:

* Ter seu DevKit conectado ao Wi-Fi
* Preparar o ambiente de desenvolvimento

Uma assinatura ativa do Azure. Se você não tiver uma, você pode registrar por meio de um desses dois métodos:

* Ative uma [conta do Microsoft Azure de avaliação por 30 dias](https://azure.microsoft.com/free/)

* Declare seu [crédito do Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se você for assinante do MSDN ou o Visual Studio

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Criar um acelerador de solução de Monitoramento Remoto do Azure IoT

1. Vá para o [site de aceleradores de solução do Azure IoT](https://www.azureiotsolutions.com/) e clique em **Criar uma nova solução**.

   ![Select Azure IoT solution accelerator type](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Por padrão, este exemplo cria um Hub IoT S2 depois que ele cria um acelerador de solução de Monitoramento Remoto IoT. Se este hub IoT não for usado com um grande número de dispositivos, é altamente recomendável fazer o downgrade do S2 para S1 e excluir o acelerador de solução de Monitoramento Remoto IoT para que o Hub IoT relacionado também possa ser excluído quando você não precisa mais dele. 

2. Selecione **Monitoramento remoto**.

3. Insira um nome de solução, selecione uma assinatura e uma região e, em seguida, clique em **Criar solução**. A solução pode demorar um pouco para ser provisionada.
  
   ![Criar solução](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Após o término do fornecimento, clique em **Launch**. Alguns dispositivos simulados são criados para a solução durante o processo de provisionamento. Clique em **DISPOSITIVOS** para fazer check-out.

   ![painel](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Console](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Clique em **ADICIONAR UM DISPOSITIVO**.

6. Clique em **Adicionar novo** para **Dispositivo Personalizado**.
  
   ![Adicionar novo dispositivo](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Clique em **Deixe-me definir minha própria ID de dispositivo**, digite `AZ3166`e, em seguida, clique em **Criar**.
  
   ![Criar dispositivo com a ID](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Anote **Hub IoT Hostname**e clique em **Pronto**.

## <a name="open-the-remotemonitoring-sample"></a>Abra a amostra RemoteMonitoring

1. Desconecte o DevKit de seu computador, se ele estiver conectado.

2. Iniciar o VS Code.

3. Conecte o DevKit ao computador. O Código VS detecta automaticamente o DevKit e abre as seguintes páginas:

   * Página de introdução do DevKit.
   * Exemplos do Arduino: Exemplos práticos de como começar a usar o DevKit.

4. Expanda a seção **EXEMPLOS DO ARDUINO** no canto esquerdo, vá até **Exemplos para MXCHIP AZ3166 > AzureIoT** e selecione **RemoteMonitoring**. Isso abrirá uma nova janela do VS Code com a pasta de projeto nela.

   > [!NOTE]
   > Se você fechar o painel, você poderá reabri-lo. Use `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, digite **Arduino** e, em seguida, localize e selecione **Arduino: Exemplos**.

## <a name="provision-required-azure-services"></a>Provisionar serviços necessários do Azure

Na janela de solução, execute sua tarefa em `Ctrl+P` (macOS: `Cmd+P`) inserindo `task cloud-provision` na caixa de texto fornecida.

No terminal do VS Code, uma linha de comando interativa orienta você durante o provisionamento dos serviços do Azure necessários.

![Provisionar recursos do Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Compilação e carregamento do código de dispositivo

1. Use `Ctrl+P` (macOS: `Cmd + P`) e o digite a **conexão dos dispositivos de configuração da tarefa**.

2. O terminal pergunta se você deseja usar uma string de conexão que ele recupera da etapa `task cloud-provision`. Você também pode inserir sua própria cadeia de caracteres de conexão do dispositivo clicando em 'Criar novo...'

3. O terminal solicita que você entre no modo de configuração. Para fazer isso, mantenha o botão A pressionado e, em seguida, solte o botão Reiniciar. A tela exibe a ID do Kit de Desenvolvimento e “Configuração”.

   ![Cadeia de conexão de entrada](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Depois que `task config-device-connection`terminar, clique em`F1` para carregar comandos do VS Code e selecione`Arduino: Upload`. VS Code inicia verificando e carregando o esboço do Arduino.
  
   ![Verificação e o upload do esboço do Arduino](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

O Kit de Desenvolvimento reinicia e começa a execução do código.

## <a name="test-the-project"></a>Teste do projeto

Quando o aplicativo de amostra é executado, o DevKit envia dados do sensor via Wi-Fi para o acelerador de solução de monitoramento remoto do IoT do Azure. Para ver o resultado, siga estas etapas:

1. Vá para o acelerador de solução de Monitoramento Remoto do Azure IoT e, em seguida, clique em **PAINEL**.

2. No console de solução de Monitoramento Remoto, você verá o status do sensor de DevKit.

   ![Dados de sensor no acelerador de solução de Monitoramento Remoto do Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Alterar ID do dispositivo

Se você quiser alterar o **AZ3166** embutido em código para uma ID do dispositivo personalizada no código, modifique a linha de código exibida no [exemplo de monitoramento remoto](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23).

## <a name="problems-and-feedback"></a>Comentários e problemas

Se você encontrar problemas, consulte [as perguntas frequentes do kit de desenvolvedores da IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou entre em contato conosco usando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como conectar a um dispositivo DevKit para o acelerador de solução de Monitoramento Remoto do Azure IoT e visualizar os dados de sensor, aqui estão as próximas etapas sugeridas:

* [Visão geral dos aceleradores de solução do Azure IoT](https://docs.microsoft.com/azure/iot-suite/)

* [Conectar um dispositivo MXChip IoT DevKit ao seu aplicativo Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)

* [Kit de desenvolvedor de IoT](https://microsoft.github.io/azure-iot-developer-kit/) 
