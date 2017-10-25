---
title: 'Kit de Desenvolvimento da IoT para a nuvem: conectar o Kit de Desenvolvimento da IoT AZ3166 ao Hub IoT do Azure | Microsoft Docs'
description: Neste tutorial, aprenda a configurar e conectar o Kit de Desenvolvimento da IoT AZ3166 ao Hub IoT do Azure para enviar dados para a plataforma de nuvem do Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: xshi
ms.openlocfilehash: e8abae4d523ad537563f2c2964a3585b68fda7c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>Como conectar o IoT DevKit AZ3166 ao Azure IoT Hub na nuvem

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Você pode usar o [Kit de Desenvolvimento da IoT MXChip](https://microsoft.github.io/azure-iot-developer-kit/) para desenvolver e criar protótipos de soluções de Internet das Coisas (IoT) que usufruem dos serviços do Microsoft Azure. Ele inclui uma placa compatível com Arduino com periféricos avançados e sensores, um pacote de placa de software livre e um [catálogo de projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) crescente.

## <a name="what-you-do"></a>O que fazer
Conecte o [Kit de Desenvolvimento](https://microsoft.github.io/azure-iot-developer-kit/) a um Hub IoT do Azure que você criou, colete os dados de temperatura e umidade de sensores e envie os dados para o Hub IoT.

Você ainda não tem um DevKit? [Obtenha um](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>O que você aprenderá

* Como conectar Kit de Desenvolvimento da IoT com o ponto de acesso sem fio e preparar o ambiente de desenvolvimento.
* Como criar um Hub IoT e registrar um dispositivo para o Kit de Desenvolvimento da IoT MXChip.
* Como coletar dados de sensor executando um aplicativo de exemplo no Kit de Desenvolvimento da IoT MXChip.
* Como enviar os dados do sensor para o hub IoT.

## <a name="what-you-need"></a>O que você precisa

* Uma placa MXChip IoT DevKit com um cabo micro USB. [Obtenha agora](https://aka.ms/iot-devkit-purchase).
* Um computador que executa o Windows 10 ou macOS 10.10+.
* Uma assinatura ativa do Azure. [Ative uma conta de avaliação do Microsoft Azure por 30 dias](https://azureinfo.microsoft.com/us-freetrial.html).
  

## <a name="prepare-your-hardware"></a>Prepare seu hardware

Conecte o hardware no seu computador.

Você precisa desse hardware:

* Placa DevKit
* Cabo micro USB

![Requisitos de hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Conecte o Kit de Desenvolvimento ao computador:

1. Conecte a extremidade USB ao computador.
2. Conecte a extremidade micro USB ao Kit de Desenvolvimento.
3. O LED verde perto do botão liga/desliga confirma a conexão.

![Conexões de hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Configurar o Wi-Fi

Os projetos de IoT requerem conectividade com a Internet. Use as instruções a seguir para configurar o Kit de Desenvolvimento e conectar-se ao Wi-Fi.

### <a name="enter-ap-mode"></a>Entrar no modo AP

Mantenha o botão B pressionado e solte o botão reiniciar; em seguida, solte o botão B. O seu Kit de Desenvolvimento entrará no modo AP para configurar o Wi-Fi. A tela exibirá o identificador SSID do Kit de Desenvolvimento e o endereço IP do portal de configuração.

![Botão de reinicialização, botão B e SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Conexão do PA do DevKit

Agora, use outro dispositivo habilitado para Wi-Fi (computador ou telefone celular) para se conectar ao SSID do Kit de Desenvolvimento (destacado na imagem anterior). Deixe a senha em branco.

![Informações de rede e o botão Conectar](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Configurar o Wi-Fi para o Kit de Desenvolvimento

Abra o endereço IP exibido na tela do DevKit no seu navegador do PC ou do celular, selecione a rede Wi-Fi com a qual deseja que o DevKit se conecte e digite a senha. Selecione **Conectar**.

![Caixa de senha e botão Conectar](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Quando a conexão for bem-sucedida, o DevKit reiniciará em alguns segundos. Você verá então o endereço IP e nome do Wi-Fi na tela:

![Nome do Wi-Fi e endereço IP](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> O endereço IP exibido na foto pode não corresponder ao endereço IP real atribuído e exibido na tela do Kit de Desenvolvimento. Isso é normal, já que o Wi-Fi usa o DHCP para atribuir IPs dinamicamente.

Após a configuração do Wi-Fi, suas credenciais persistirão no dispositivo para essa conexão, mesmo que o dispositivo esteja desconectado. Por exemplo, se você configurar o Kit de Desenvolvimento para o Wi-Fi em casa e, em seguida, levar o Kit de Desenvolvimento para o escritório, será necessário reconfigurar o modo AP (começando na etapa na seção "Entrar no modo AP") para conectar o Kit de Desenvolvimento ao Wi-Fi do seu escritório. 

## <a name="start-using-the-devkit"></a>Começar a usar o Kit de Desenvolvimento

O aplicativo padrão em execução no DevKit verifica a versão mais recente do firmware e exibe alguns dados de diagnóstico de sensor para você.

### <a name="upgrade-to-the-latest-firmware"></a>Como atualizar para o firmware mais recente

> [!NOTE] 
> Desde a v1.1, o Kit de desenvolvimento permite o ST-SAFE no carregador de inicialização. Será necessário atualizar o firmware se você estiver executando uma versão abaixo de v1.1 para que ele funcione provavelmente.

Se você precisa de uma atualização de firmware, a tela exibirá a versão de firmware atual e a mais recente. Siga o guia [Atualizar o firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/upgrading/) para atualizá-lo.

![Exibição das versões mais recente e atual do firmware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
> Essa é uma tarefa que só precisa ser executada uma vez. Depois de começar a desenvolver no Kit de Desenvolvimento e carregar seu aplicativo, ele já virá com a versão mais recente do firmware.

### <a name="test-various-sensors"></a>Teste de vários sensores

Pressione o botão B para testar os sensores. Continue pressionando e soltando o botão B para completar o ciclo por cada sensor.

![Botão B e exibição de sensor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Agora é hora de configurar o ambiente de desenvolvimento: ferramentas e pacotes para você compilar aplicativos impressionantes de IoT. Você pode escolher a versão do Windows ou macOS de acordo com seu sistema operacional.

### <a name="windows"></a>Windows

Recomendamos usar o pacote de instalação para preparar o ambiente de desenvolvimento. Se tiver alguma dificuldade, siga as [etapas manuais](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) para concluir sua tarefa.

#### <a name="download-the-latest-package"></a>Baixar o pacote mais recente

O arquivo .zip que você baixa contém todas as ferramentas e pacotes necessários para o desenvolvimento do Kit de Desenvolvimento.

> [!div class="button"]
[Baixar](https://aka.ms/devkit/prod/installpackage/latest)

O arquivo .zip contém as ferramentas e pacotes a seguir. Se você já tiver alguns componentes instalados, o script irá detectá-los e ignorá-los.

* Node.js e Yarn: tempo de execução para o script de instalação e as tarefas automatizadas.
* [MSI da CLI do Azure 2.0](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): experiência de linha de comando de multiplaforma para gerenciar recursos do Azure. O MSI contém Python e pip dependentes.
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code): editor de códigos simples para o desenvolvimento de Kit de Desenvolvimento.
* [Extensão do Visual Studio Code para Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): extensão que permite o desenvolvimento do Arduino no Visual Studio Code.
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): a extensão para Arduino depende dessa ferramenta.
* Pacote de placa do Kit de Desenvolvimento: cadeias de ferramentas, bibliotecas e projetos para o Kit de Desenvolvimento.
* Utilitário de Link-ST: ferramentas e drivers essenciais.

#### <a name="run-the-installation-script"></a>Execute o script de instalação

No Explorador de Arquivos do Windows, localize o arquivo .zip e extraia-o. Localize `install.cmd`, clique com o botão direito do mouse nele e selecione **Executar como administrador**.

![Explorador de Arquivos](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Durante a instalação, você verá o progresso de cada ferramenta ou pacote.

![Progresso da instalação](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

#### <a name="install-drivers"></a>Instalar drivers

O VS Code para extensão do Arduino depende do Arduino IDE. Se esta for a primeira vez que você estiver instalando o Arduino IDE, será solicitado que você instale os drivers relevantes:

![Guia de Introdução: driver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

A instalação deve levar cerca de 10 minutos para ser concluída, dependendo da velocidade da Internet. Depois que a instalação for concluída, você deverá ver os atalhos do Visual Studio Code e do Arduino IDE na área de trabalho.

> [!NOTE] 
> Eventualmente, ao iniciar o VS Code, aparecerá um erro notificando que ele não pôde encontrar o Arduino IDE nem o pacote de placa relacionado. Para resolver isso, feche o VS Code e reinicie o Arduino IDE. O VS Code deve então localizar o caminho do Arduino IDE corretamente.

### <a name="macos-preview"></a>macOS (versão prévia)

Siga estas etapas para preparar o ambiente de desenvolvimento no macOS.

#### <a name="install-azure-cli-20"></a>Instalar a CLI 2.0 do Azure

1. Instale a CLI do Azure 2.0 usando um comando `curl`:

   ```bash
   curl -L https://aka.ms/InstallAzureCli | bash
   ```

2. Reinicie o shell de comando para que as alterações entrem em vigor:

   ```bash
   exec -l $SHELL
   ```

Para obter mais informações sobre como instalar a CLI do Azure 2.0, consulte o [guia oficial](https://docs.microsoft.com//cli/azure/install-azure-cli).

#### <a name="install-the-arduino-ide"></a>Instalar o Arduino IDE

A extensão de Arduino de Visual Studio Code se baseia no Arduino IDE. Baixe e instale o [Arduino IDE para macOS](https://www.arduino.cc/en/Main/Software).

#### <a name="install-visual-studio-code"></a>Instalar o Visual Studio Code

Baixe e instale o [Visual Studio Code para macOS](https://code.visualstudio.com/). Essa é a ferramenta de desenvolvimento principal para criar aplicativos de Kit de Desenvolvimento de IoT.

####  <a name="download-the-latest-package"></a>Baixar o pacote mais recente

1. Instale o Node.js. Você pode usar o gerenciador de pacotes macOS popular [Homebrew](https://brew.sh/) ou um [instalador pré-compilado](https://nodejs.org/en/download/) para instalá-lo.

2. Baixe o arquivo .zip que contém scripts de tarefas necessárias para o desenvolvimento de Kit de Desenvolvimento no VS Code.

   > [!div class="button"]
   [Baixar](https://azureboard.azureedge.net/installpackage/devkit_tasks_1.0.2.zip)

3. Localize e extraia o arquivo .zip. Em seguida, inicie o aplicativo **Terminal** e execute os comandos a seguir:

   a. Mova a pasta extraída para sua pasta de usuário macOS:
      ```bash
      mv [.zip extracted folder]/azure-board-cli ~/. ; cd ~/azure-board-cli
      ```
  
   b. Instale os pacotes npm:
      ```
      npm install
      ```

#### <a name="install-the-vs-code-extension-for-arduino"></a>Instalar a extensão de VS Code para Arduino

Você pode instalar as extensões do Azure Marketplace diretamente no Visual Studio Code. Selecione o ícone de extensões no painel esquerdo, pesquise **Arduino** e, em seguida, selecione **Instalar**:

![Localizando uma extensão de Arduino](media/iot-hub-arduino-devkit-az3166-get-started/installation-extensions-mac.png)

#### <a name="install-the-devkit-board-package"></a>Instalar o pacote de placa do Kit de Desenvolvimento

Adicione a placa do Kit de Desenvolvimento usando o Gerenciador de Placas no Visual Studio Code.

1. Use Cmd + Shift + P para abrir a paleta de comandos e digite **Arduino**, depois localize e selecione **Arduino: Gerenciador de Placas**.

2. Selecione **URLs Adicionais** no canto inferior direito.
   ![Link de URLs adicionais](media/iot-hub-arduino-devkit-az3166-get-started/installation-additional-urls-mac.png)

3. No arquivo settings.json, adicione uma linha na parte inferior do painel **CONFIGURAÇÕES DO USUÁRIO** e salve.
   ```json
   "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
   ```
   ![Código adicionado ao painel CONFIGURAÇÕES DO USUÁRIO](media/iot-hub-arduino-devkit-az3166-get-started/installation-settings-json-mac.png)

4. No Gerenciador de Placas, pesquise por **az3166** e instale a versão mais recente.
   ![Instalando o az3166](media/iot-hub-arduino-devkit-az3166-get-started/installation-az3166-mac.png)

Agora você já instalou todas as ferramentas e os pacotes necessários para macOS.


## <a name="open-the-project-folder"></a>Abrir a pasta do projeto

### <a name="start-vs-code"></a>Iniciar o VS Code

Verifique se que o DevKit não está conectado. Inicie o VS Code primeiro e conecte o Kit de Desenvolvimento ao seu computador. O VS Code localiza o Visual Studio Code automaticamente e abre uma página de introdução:

![Página Introdução](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-vscode.png)

> [!NOTE] 
> Eventualmente, ao iniciar o VS Code, aparecerá um erro notificando que não foi possível encontrar o Arduino IDE nem o pacote de placa relacionado. Feche o VS Code e inicialize o Arduino IDE uma vez, assim o VS Code localizará o caminho do Arduino IDE corretamente.


### <a name="open-the-arduino-examples-folder"></a>Abra a pasta de exemplos de Arduino

Expanda a seção **EXEMPLOS DO ARDUINO** no canto esquerdo, vá até **Exemplos para MXCHIP AZ3166 > AzureIoT** e selecione **GetStarted**. Isso abrirá uma nova janela do VS Code com a pasta de projeto nela.

![Guia Exemplos do Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-examples.png)

Se você fechar o painel, você poderá reabri-lo. Use `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, digite **Arduino** e, em seguida, localize e selecione **Arduino: Exemplos**.

## <a name="provision-azure-services"></a>Provisionamento dos serviços do Azure

Na janela de solução, execute a tarefa por meio de `Ctrl+P` (macOS: `Cmd+P`) digitando `task cloud-provision`:

No terminal do VS Code, uma linha de comando interativa orienta você durante o provisionamento dos serviços do Azure necessários:

![Linha de comando interativa](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Compilar e carregar o esboço do Arduino

### <a name="install-the-required-library"></a>Instalar a biblioteca necessária

1. Pressione `F1` ou `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, digite **Arduino** e, em seguida, localize e selecione **Arduino: gerenciador de biblioteca**.

2. Pesquise pela biblioteca **ArduinoJson** e selecione **Instalar**: ![Instalar biblioteca Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/arduino-json.png)

### <a name="build-and-upload-the-device-code-windows"></a>Criar e carregar o código do dispositivo (Windows)
1. Use `Ctrl+P` para executar `task device-upload`.
2. O terminal solicita que você entre no modo de configuração. Para fazer isso, mantenha o botão A pressionado e, em seguida, solte o botão Reiniciar. A tela exibe a ID do Kit de Desenvolvimento e “Configuração”.

Isso serve para definir a cadeia de conexão que recupera da etapa `task cloud-provision`.

Em seguida, o terminal começa verificando e carregando o esboço do Arduino:

![Verificação e o upload do esboço do Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

O Kit de Desenvolvimento reinicia e começa a execução do código.

### <a name="build-and-upload-the-device-code-mac"></a>Criar e carregar o código de dispositivo (Mac)

1. Coloque o Kit de Desenvolvimento no modo de configuração: mantenha o botão A pressionado e, em seguida, solte o botão Reiniciar. A tela exibe “Configuração”.
2. Use `Cmd+P` para executar `task device-upload`.

Isso serve para definir a cadeia de conexão que recupera da etapa `task cloud-provision`.

Em seguida, o VS Code começa verificando e carregando o sketch do Arduino:

![device-upload](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

O Kit de Desenvolvimento reinicia e começa a execução do código.

## <a name="test-the-project"></a>Teste do projeto

No VS Code, siga estas etapas para abrir e configurar o Monitor Serial:

1. Clique na palavra `COM[X]` na barra de status para definir a porta COM certa com `STMicroelectronics`: ![com-port](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/com-port.png)

2. Clique no ícone de tomada na barra de status para abrir o Monitor Serial: ![serial-monitor](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/serial-monitor.png)

3. Na barra de status, clique no número que representa a Taxa de Baud e defina como `115200`: ![baud-rate](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/baud-rate.png)

O aplicativo de exemplo é executado com êxito quando você vê os seguintes resultados:

* O Monitor Serial exibe as mesmas informações como o conteúdo na captura de tela abaixo.
* O LED no MXChip IoT DevKit está piscando.

![Saída final no VS Code](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Comentários e problemas

Se você tiver problemas, você poderá encontrar [Perguntas frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/). Você também pode nos fornecer comentários fazendo-os nesta página.

## <a name="next-steps"></a>Próximas etapas

Você conectou um Kit de Desenvolvimento de IoT MXChip ao seu Hub IoT e enviou os dados do sensor de captura ao seu Kit de Desenvolvimento de IoT.

Para continuar a introdução ao Hub IoT do Azure e explorar outros cenários de IoT, confira:

- [Gerenciar mensagens do dispositivos de nuvem com o iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [Salvar mensagens do Hub IoT para o armazenamento de dados do Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Use o Power BI para visualizar dados do sensor do Hub IoT do Azure em tempo real](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Use Aplicativos Web para visualizar dados do sensor do Hub IoT do Azure em tempo real](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Previsão do tempo usando os dados de sensor do seu Hub IoT no Azure Machine Learning](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [Gerenciamento de dispositivos com o iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Monitoramento remoto e notificações com Aplicativos Lógicos](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
