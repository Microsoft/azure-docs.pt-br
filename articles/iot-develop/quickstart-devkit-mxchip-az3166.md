---
title: 'Guia de início rápido: conectar um MXCHIP AZ3166 ao Azure IoT Central'
description: Use um software inserido no Azure RTOS para conectar um dispositivo MXCHIP AZ3166 à IoT do Azure e enviar dados telemétricos.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 03/17/2021
ms.openlocfilehash: 160797367e2daf0cb6fe708d626cbf217c9992c8
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448586"
---
# <a name="quickstart-connect-an-mxchip-az3166-devkit-to-iot-central"></a>Guia de início rápido: conectar um Kit de Desenvolvimento MXCHIP AZ3166 à IoT Central

**Aplica-se a**: [desenvolvimento de dispositivos inseridos](about-iot-develop.md#embedded-device-development)<br>
**Tempo total de conclusão**: 30 minutos

[![Procurar código](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)

Neste tutorial, você usará o Azure RTOS para conectar um Kit de Desenvolvimento de IoT MXCHIP AZ3166 (doravante chamado Kit de Desenvolvimento MXCHIP) à IoT do Azure. Este artigo faz parte da série [Introdução ao desenvolvimento de um dispositivo inserido na IoT do Azure](quickstart-device-development.md). A série apresenta os desenvolvedores de dispositivos ao Azure RTOS e mostra de que modo conectar vários kits de avaliação de dispositivos à IoT do Azure.

Você realizará as seguintes tarefas:

* Instalar um conjunto de ferramentas de desenvolvimento inseridas para programar um Kit de Desenvolvimento MXCHIP em C
* Criar e instalar uma imagem no Kit de Desenvolvimento MXCHIP
* Usar o Azure IoT Central para criar componentes de nuvem, exibir propriedades e a telemetria do dispositivo, bem como executar uma chamada aos comandos diretos

## <a name="prerequisites"></a>Pré-requisitos

* Um computador executando o Microsoft Windows 10
* Obter o [Git](https://git-scm.com/downloads) para executar a clonagem do repositório
* Hardware

    > * O [Kit de Desenvolvimento de IoT MXCHIP AZ3166](https://aka.ms/iot-devkit) (Kit de Desenvolvimento MXCHIP)
    > * Wi-Fi de 2.4 GHz
    > * Uma entrada USB 2.0 A macho para um cabo Micro USB macho

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Para configurar seu ambiente de desenvolvimento, primeiro clone um repositório GitHub que contenha todos os ativos necessários para o tutorial. Depois instale um conjunto de ferramentas de programação.

### <a name="clone-the-repo-for-the-tutorial"></a>Clonar um repositório para o tutorial

Clone o repositório a seguir para baixar todos os códigos de dispositivos de exemplo, scripts de configuração e versões offline da documentação. Caso tenha clonado este repositório anteriormente em outro tutorial, não será preciso fazer isso novamente.

Execute o seguinte comando para clonar o repositório:

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>Instalar as ferramentas

O repositório clonado contém um script de configuração que vai instalar e configurar as ferramentas necessárias. Caso tenha instalado essas ferramentas em outro tutorial no Guia de Introdução, não será preciso fazer isso novamente.

> [!NOTE]
> O script de configuração instalará as seguintes ferramentas:
> * [CMake](https://cmake.org): criar
> * [GCC do ARM](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm): compilar
> * [Termite](https://www.compuphase.com/software_termite.htm): monitorar a saída da porta serial para dispositivos conectados

Para instalar as ferramentas:

1. No Explorador de Arquivos, acesse o seguinte caminho no repositório e execute o script de configuração chamado *get-toolchain.bat*:

    > *getting-started\tools\get-toolchain.bat*

1. Após a instalação, abra uma janela do console para reconhecer as alterações de configuração executadas pelo script de configuração. Use este console para concluir as tarefas restantes de programação no tutorial. É possível usar o CMD do Windows, o PowerShell ou o Git Bash para Windows.
1. Execute o código a seguir para confirmar se o CMake versão 3.14 ou posterior está instalado.

    ```shell
    cmake --version
    ```

## <a name="create-the-cloud-components"></a>Criar componentes de nuvem

### <a name="create-the-iot-central-application"></a>Criar um aplicativo do Azure IoT Central

Há vários modos de conectar dispositivos à IoT do Azure. Nesta seção, você saberá como conectar um dispositivo usando o Azure IoT Central. A IoT Central é uma plataforma de aplicativos de IoT que reduz o custo e a complexidade da criação e do gerenciamento de soluções de IoT.

Para criar um aplicativo:
1. Em [Portal do Azure IoT Central](https://apps.azureiotcentral.com/), selecione **Meus aplicativos** no menu de navegação lateral.
1. Clique em **+ Novo aplicativo**.
1. Selecione **Aplicativos personalizados**.
1. Adicione um Nome e uma URL do Aplicativo.
1. Escolha o plano de preços **Gratuito** para ativar uma avaliação de sete dias.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-custom.png" alt-text="Criar um aplicativo personalizado no Azure IoT Central":::

1. Selecione **Criar**.

    Após provisionar o aplicativo, a IoT Central redirecionará você de modo automático para o painel do novo aplicativo.

    > [!NOTE]
    > Caso tenha um aplicativo existente da IoT Central, será possível usá-lo para concluir as etapas deste artigo em vez de criar um aplicativo.

### <a name="create-a-new-device"></a>Criar um novo dispositivo

Use o painel de aplicativos da IoT Central para criar um dispositivo nesta seção. Você usará as informações de conexão do dispositivo recém-criado para conectar o dispositivo físico com segurança em uma seção posterior.

Para criar um dispositivo:
1. No painel de aplicativos, selecione **Dispositivos** no menu de navegação lateral.
1. Clique em **+ Novo** para abrir a janela **Criar um dispositivo**.
1. Deixe o modelo de dispositivo definido como **Não Atribuído**.
1. Preencha o nome e a ID do dispositivo de acordo com sua preferência.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-device.png" alt-text="Criar um dispositivo no Azure IoT Central":::

1. Selecione o botão **Criar**.
1. O dispositivo recém-criado aparecerá na lista **Todos os dispositivos**.  Selecione o nome do dispositivo para mostrar detalhes.
1. Clique em **Conectar** na barra de menus superior direita para exibir as informações de conexão que serão usadas para configurar o dispositivo exibir na próxima seção.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-connection-info.png" alt-text="Exibir detalhes de conexão do dispositivo":::

1. Observe os valores de conexão dos parâmetros da cadeia de conexão abaixo exibidos na caixa de diálogo **Conectar**. Será necessário adicionar esses valores em um arquivo de configuração na próxima etapa:

    > * `ID scope`
    > * `Device ID`
    > * `Primary key`

## <a name="prepare-the-device"></a>Preparar o dispositivo

Para conectar o Kit de Desenvolvimento MXCHIP ao Azure, será necessário modificar um arquivo de configuração para executar as configurações de Wi-Fi e IoT do Azure, bem como recompilar e instalar a imagem no dispositivo.

### <a name="add-configuration"></a>Adicionar configuração

1. Abra o seguinte arquivo em um editor de texto:

    > *getting-started\MXChip\AZ3166\app\azure_config.h*

1. Defina as constantes de Wi-Fi para os valores abaixo de seu ambiente local.

    |Nome da constante|Valor|
    |-------------|-----|
    |`WIFI_SSID` |{*Seu SSID de Wi-Fi*}|
    |`WIFI_PASSWORD` |{*Sua senha de Wi-Fi*}|
    |`WIFI_MODE` |{*Um dos valores do modo Wi-Fi enumerados no arquivo*}|

1. Defina as constantes de informações do dispositivo de IoT do Azure para os valores salvos após a criação de recursos do Azure.

    |Nome da constante|Valor|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*Seu valor de escopo da ID*}|
    |`IOT_DPS_REGISTRATION_ID` |{*Seu valor da ID do dispositivo*}|
    |`IOT_DEVICE_SAS_KEY` |{*Seu valor da chave primária*}|

1. Salve e feche o arquivo.

### <a name="build-the-image"></a>Criar a imagem

No console ou no Explorador de Arquivos, execute o script *rebuild.bat* no seguinte caminho para criar a imagem:

> *getting-started\MXChip\AZ3166\tools\rebuild.bat*

Depois que a criação for concluída, confirme se o arquivo binário foi criado no seguinte caminho:

> *getting-started\MXChip\AZ3166\build\app\mxchip_azure_iot.bin*

### <a name="flash-the-image"></a>Instalar a imagem

1. No Kit de Desenvolvimento MXCHIP, localize o botão **Redefinir** e a micro porta USB. Use esses componentes nas etapas a seguir. Os dois estarão realçados na seguinte figura:

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/mxchip-iot-devkit.png" alt-text="Localizar os principais componentes da placa do Kit de Desenvolvimento MXChip":::

1. Conecte o micro cabo USB à micro porta USB no Kit de Desenvolvimento MXCHIP, depois conecte-o ao seu computador.
1. No Explorador de Arquivos, localize o arquivo binário criado na seção anterior.
1. Copie o arquivo binário *mxchip_azure_iot.bin*.
1. No Explorador de Arquivos, localize o dispositivo do Kit de Desenvolvimento MXCHIP conectado ao seu computador. O dispositivo será exibido como uma unidade no sistema com o rótulo da unidade **AZ3166**.
1. Cole o arquivo binário na pasta raiz do Kit de Desenvolvimento MXCHIP. A atualização da imagem será iniciada de modo automático e concluída em alguns segundos.

    > [!NOTE]
    > Durante o processo de atualização de imagem, um LED verde vai ligar/desligar no Kit de Desenvolvimento MXCHIP.

### <a name="confirm-device-connection-details"></a>Confirmar detalhes de conexão do dispositivo

É possível usar o aplicativo **Termite** para monitorar a comunicação e confirmar se o dispositivo foi configurado de modo adequado.

1. Inicie o **Termite**.
    > [!TIP]
    > Caso não seja possível conectar o Termite ao seu Kit de Desenvolvimento, instale o [driver ST-LINK](https://my.st.com/content/ccc/resource/technical/software/driver/files/stsw-link009.zip) e tente novamente. Confira a [Solução de problemas](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md) para obter etapas adicionais.
1. Selecione **Configurações**.
1. Na caixa de diálogo **Configurações da porta serial**, verifique as seguintes configurações e execute atualizações se necessário:
    * **Taxa de transmissão**: 115.200
    * **Porta**: a porta à qual o Kit de Desenvolvimento MXCHIP está conectado. Caso haja várias opções de portas na lista suspensa, será possível localizar a porta adequada para uso. Abra o **Gerenciador de Dispositivos** do Windows e exiba a opção **Portas** para identificar qual porta usar.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/termite-settings.png" alt-text="Confirmar as configurações no aplicativo Termite":::

1. Selecione OK.
1. Clique no botão **Reiniciar** do dispositivo. O botão está rotulado no dispositivo e próximo ao micro conector USB.
1. No aplicativo **Termite**, verifique os valores do ponto de verificação a seguir para confirmar se o dispositivo foi inicializado e conectado à IoT do Azure.

    ```output
    Starting Azure thread

    Initializing WiFi
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 10.0.0.123
        Mask: 255.255.255.0
        Gateway: 10.0.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 10.0.0.1
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 185.242.56.3
        SNTP time update: Nov 16, 2020 23:47:35.385 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: ***
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***
        Device id: ***
        Model id: dtmi:azurertos:devkit:gsgmxchip;1
    Connected to IoTHub
    SUCCESS: Azure IoT Hub client initialized

    Starting Main loop
    ```

Mantenha o Termite aberto para monitorar a saída do dispositivo nas etapas a seguir.

## <a name="verify-the-device-status"></a>Verificar o status do dispositivo

Para exibir o status do dispositivo no portal da IoT Central:
1. No painel de aplicativos, selecione **Dispositivos** no menu de navegação lateral.
1. Confirme se o **Status do dispositivo** foi atualizado como **Provisionado**.
1. Confirme se o **Modelo de dispositivo** foi atualizado como **Guia de Introdução**.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-view-status.png" alt-text="Exibir o status do dispositivo na IoT Central":::

## <a name="view-telemetry"></a>Exibir telemetria

É possível exibir o fluxo de telemetria do seu dispositivo para a nuvem usando a IoT Central.

Para exibir a telemetria no portal da IoT Central:

1. No painel de aplicativos, selecione **Dispositivos** no menu de navegação lateral.
1. Selecione o dispositivo na lista de dispositivos.
1. Na guia **Visão Geral**, exiba a telemetria conforme o dispositivo envia mensagens à nuvem.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-telemetry.png" alt-text="Exibir a telemetria do dispositivo na IoT Central":::

    > [!NOTE]
    > Também é possível monitorar a telemetria do dispositivo usando o aplicativo Termite.

## <a name="call-a-direct-method-on-the-device"></a>Chamar um método direto no dispositivo

Também é possível usar a IoT Central para executar uma chamada a um método direto implementado em seu dispositivo. Os métodos diretos têm um nome e, de modo opcional, podem ter um conteúdo JSON, uma conexão configurável e um tempo limite do método. Nesta seção, execute uma chamada a um método que permita ligar ou desligar um LED.

Para executar uma chamada a um método no portal da IoT Central:

1. Selecione a guia **Comando** na página do dispositivo.
1. Selecione **Estado**, depois clique em **Executar**.  A luz do LED será ligada.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-invoke-method.png" alt-text="Chama um método direto em um dispositivo":::

1. Desmarque o **Estado**, depois clique em **Executar**. A luz do LED será desligada.

## <a name="view-device-information"></a>Exibir informações do dispositivo

É possível exibir informações do dispositivo da IoT Central.

Selecione a guia **Sobre** na página do dispositivo.

:::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-about.png" alt-text="Exibir informações sobre o dispositivo na IoT Central":::

## <a name="debugging"></a>Depuração

Para executar a depuração do aplicativo, confira [Como executar uma depuração usando o Visual Studio Code](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md).

## <a name="clean-up-resources"></a>Limpar recursos

Caso não precise mais dos recursos do Azure criados neste tutorial, será possível excluí-los no portal da IoT Central. Como alternativa, caso prossiga para outro tutorial deste Guia de Introdução, será possível manter e reutilizar os recursos criados.

Para remover todo o aplicativo de exemplo do Azure IoT Central, bem como os respectivos dispositivos e recursos:
1. Selecione **Administração** > **Seu aplicativo**.
1. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma imagem personalizada que contém o código de exemplo do Azure RTOS. Depois atualizou a imagem no dispositivo do Kit de Desenvolvimento MXCHIP. Você também usou o portal da IoT Central para criar recursos do Azure, conectar o Kit de Desenvolvimento MXCHIP ao Azure com segurança, exibir dados telemétricos e enviar mensagens.

* Para os desenvolvedores de dispositivos, recomendamos conferir outros tutoriais da série [Introdução ao desenvolvimento de dispositivos inseridos na IoT do Azure](quickstart-device-development.md).
* Caso tenha problemas para inicializar ou conectar seu dispositivo após seguir as etapas deste guia, confira a [Solução de problemas](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md).
* Para saber mais sobre de que modo os componentes do Azure RTOS são usados no código de exemplo deste tutorial, confira [Como usar o Azure RTOS no Guia de Introdução](https://github.com/azure-rtos/getting-started/blob/master/docs/using-azure-rtos.md).

    > [!IMPORTANT]
    > O Azure RTOS fornece OEMs juntamente com componentes para proteger a comunicação e criar um isolamento de códigos e dados usando mecanismos subjacentes de MCU/MPU de proteção de hardware. No entanto, cada OEM é responsável por garantir que os dispositivos atendam aos requisitos de segurança em evolução.

