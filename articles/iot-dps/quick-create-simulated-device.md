---
title: Início Rápido – Provisionar um dispositivo TPM simulado para o Hub IoT do Azure usando C#
description: Este início rápido usa registros individuais. Neste início rápido, você criará e provisionará um dispositivo TPM simulado usando o SDK do dispositivo C para o DPS (Serviço de Provisionamento de Dispositivos) no Hub IoT do Azure.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom:
- mvc
- amqp
- mqtt
ms.openlocfilehash: e2930a3ca2ecb9d8217fdfea1cbcb0e669f61775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960012"
---
# <a name="quickstart-provision-a-simulated-tpm-device-using-the-azure-iot-c-sdk"></a>Início Rápido: Provisionar um dispositivo TPM simulado usando o SDK de C do IoT do Azure

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Neste início rápido, você aprenderá a criar e a executar um simulador de dispositivo TPM (Trusted Platform Module) em um computador de desenvolvimento com Windows. Você conectará esse dispositivo simulado a um hub IoT usando uma instância do Serviço de Provisionamento de Dispositivos. Usaremos um código de exemplo do [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) para ajudar a inscrever o dispositivo com uma instância do Serviço de Provisionamento de Dispositivos e simular uma sequência de inicialização para o dispositivo.

Se você não estiver familiarizado com o processo de provisionamento automático, examine a visão geral de [provisionamento](about-iot-dps.md#provisioning-process). Não se esqueça de concluir as etapas em [Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT com o Portal do Azure](./quick-setup-auto-provision.md) antes de continuar com este início rápido. 

O Serviço de Provisionamento de Dispositivos de IoT do Azure dá suporte a dois tipos de registros:
- [Grupos de registros](concepts-service.md#enrollment-group): usados para inscrever vários dispositivos relacionados.
- [Registros individuais](concepts-service.md#individual-enrollment): usados para inscrever um único dispositivo.

Este artigo irá demonstrar registros individuais.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos a seguir são para um ambiente de desenvolvimento do Windows. Para Linux ou macOS, confira a seção apropriada em [Preparar seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) na documentação do SDK.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) com a carga de trabalho ["Desenvolvimento para desktop com C++"](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) habilitada. Também há suporte para o Visual Studio 2015 e o Visual Studio 2017.

* Versão mais recente do [Git](https://git-scm.com/download/) instalada.

<a id="setupdevbox"></a>

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Preparar um ambiente de desenvolvimento para o SDK de C do IoT do Azure

Nesta seção, você preparará um ambiente de desenvolvimento usado para compilar o [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) e o exemplo de simulador de dispositivo de [TPM](/windows/device-security/tpm/trusted-platform-module-overview).

1. Baixe o [sistema de build CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio (Visual Studio e a carga de trabalho de "Desenvolvimento para Desktop com C++") estejam instalados em seu computador, **antes** da instalação de `CMake`. Após a instalação dos pré-requisitos e verificação do download, instale o sistema de compilação CMake.

2. Localize o nome da tag para a [versão mais recente](https://github.com/Azure/azure-iot-sdk-c/releases/latest) do SDK.

3. Abra um prompt de comando ou o shell Bash do Git. Execute os comandos a seguir para clonar a versão mais recente do repositório do GitHub do [SDK do Azure IoT C](https://github.com/Azure/azure-iot-sdk-c). Use a tag que você encontrou na etapa anterior como o valor para o parâmetro `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Essa operação deve demorar alguns minutos.

4. Crie um subdiretório `cmake` no diretório raiz do repositório git e navegue até essa pasta. Execute os seguintes comandos do diretório `azure-iot-sdk-c`:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

## <a name="build-the-sdk-and-run-the-tpm-device-simulator"></a>Compilar o SDK e executar o simulador de dispositivo TPM

Nesta seção, você compilará o SDK de C do IoT do Azure, que inclui o código de exemplo de simulador de dispositivo TPM. Este exemplo fornece um [mecanismo de atestado](concepts-service.md#attestation-mechanism) de TPM por meio da autenticação de Token SAS (Assinatura de Acesso Compartilhado).

1. Do subdiretório `cmake` criado no repositório git azure-iot-sdk-c, execute o seguinte comando para criar o exemplo. Uma solução do Visual Studio para o dispositivo simulado também será gerada por esse comando de compilação.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

    Se `cmake` não encontrar o compilador do C++, você poderá obter erros de build ao executar o comando acima. Se isso acontecer, tente executar esse comando no [prompt de comando do Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Após o sucesso da compilação, as últimas linhas de saída serão semelhantes à seguinte saída:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

2. Navegue até a pasta raiz do repositório git clonado e execute o simulador [TPM](/windows/device-security/tpm/trusted-platform-module-overview) usando o caminho mostrado abaixo. Esse simulador escuta em um soquete nas portas 2321 e 2322. Não feche essa janela de comando; você precisará manter esse simulador em execução até o término deste início rápido. 

   Se você estiver na pasta *cmake*, execute os seguintes comandos:

    ```cmd/sh
    cd ..
    .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    Você não verá qualquer saída do simulador. Deixe-o continuar simulando um dispositivo TPM.

<a id="simulatetpm"></a>

## <a name="read-cryptographic-keys-from-the-tpm-device"></a>Ler as chaves de criptografia do dispositivo TPM

Nesta seção, você compilará e executará um exemplo que lerá a ID do registro e a chave de endosso do simulador de TPM que você deixou em execução e escutando nas portas 2321 e 2322. Esses valores serão usados para a inscrição do dispositivo com sua instância do Serviço de Provisionamento de Dispositivos.

1. Inicie o Visual Studio e abra o arquivo da nova solução chamado `azure_iot_sdks.sln`. Esse arquivo de solução está localizado na pasta `cmake` que você criou anteriormente na raiz do repositório git azure-iot-sdk-c.

2. No menu do Visual Studio, selecione **Compilar** > **Compilar Solução** para compilar todos os projetos na solução.

3. Na janela *Gerenciador de Soluções* do Visual Studio, navegue até a pasta **Provisionar\_Ferramentas**. Clique com botão direito do mouse no projeto **tpm_device_provision** e selecione **Definir como Projeto de Inicialização**. 

4. No menu do Visual Studio, selecione **Depurar** > **Iniciar sem depuração** para executar a solução. O aplicativo lê e exibe uma **_ID de Registro_** e uma **_Chave de endosso_**. Anote ou copie esses valores. Eles serão usados na próxima seção para inscrição do dispositivo. 


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Criar uma entrada de registro de dispositivo no portal

1. Entre no portal do Azure, selecione o botão **Todos os recursos** no menu esquerdo e abra o serviço de Provisionamento de Dispositivos.

1. Selecione a guia **Gerenciar registros** e selecione o botão **Adicionar registro individual** na parte superior. 

1. No painel **Adicionar Registro**, insira as seguintes informações:
   - Selecione **TPM** como o atestado de identidade *Mecanismo*.
   - Insira a *ID de Registro* e *Chave de Endosso* para seu dispositivo do TPM com os valores anotados anteriormente, conforme anotados anteriormente.
   - Selecione um hub IoT vinculado com o serviço de provisionamento.
   - Opcionalmente, você pode fornecer as seguintes informações:
       - Insira uma *ID de Dispositivo* exclusiva (você pode usar **test-docs-device** ou fornecer a sua). Evite dados confidenciais ao nomear seu dispositivo. Se você optar por não fornecer uma, a ID de registro será usada para identificar o dispositivo.
       - Atualize o **Estado inicial do dispositivo gêmeo** com a configuração inicial desejada para o dispositivo.
   - Uma vez concluído, pressione o botão **Salvar**. 

      ![Inserir informações de registro de dispositivo no portal](./media/quick-create-simulated-device/enter-device-enrollment.png)  

      Em caso de registro bem-sucedido, a *ID de Registro* do seu dispositivo será exibida na lista na guia *Registros Individuais*. 


<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Simular a primeira sequência de inicialização para o dispositivo

Nesta seção, você configurará o exemplo de código para usar o [AMQP (Advanced Message Queuing Protocol)](https://wikipedia.org/wiki/Advanced_Message_Queuing_Protocol) e enviar a sequência de inicialização do dispositivo para a instância do Serviço de Provisionamento de Dispositivo. Essa sequência de inicialização fará com que o dispositivo seja reconhecido e atribuído a um hub IoT vinculado à instância do Serviço de Provisionamento de Dispositivo.

1. No Portal do Azure, selecione a guia **Visão Geral** do Serviço de Provisionamento de Dispositivos e anote o valor de **_Escopo da ID_**.

    ![Extrair informações do ponto de extremidade do Serviço de Provisionamento de Dispositivo do portal](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

2. Na janela *Gerenciador de Soluções* do Visual Studio, navegue até a pasta **Provisionar\_Exemplos**. Expanda o projeto de exemplo chamado **prov\_dev\_client\_sample**. Expanda **Arquivos de Origem** e abra **prov\_dev\_client\_sample.c**.

3. Próximo à parte superior do arquivo, localize as instruções `#define` para cada protocolo de dispositivo, conforme mostrado abaixo. Remova a marca de comentário apenas de `SAMPLE_AMQP`.

    Atualmente, o [protocol MQTT não tem suporte para o Registro Individual de TPM](https://github.com/Azure/azure-iot-sdk-c#provisioning-client-sdk).

    ```c
    //
    // The protocol you wish to use should be uncommented
    //
    //#define SAMPLE_MQTT
    //#define SAMPLE_MQTT_OVER_WEBSOCKETS
    #define SAMPLE_AMQP
    //#define SAMPLE_AMQP_OVER_WEBSOCKETS
    //#define SAMPLE_HTTP
    ```

4. Localize a constante `id_scope` e substitua o valor pelo seu valor de **Escopo de ID** copiado anteriormente. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Encontre a definição da função `main()` no mesmo arquivo. Verifique se a variável `hsm_type` está definida como `SECURE_DEVICE_TYPE_TPM` em vez de `SECURE_DEVICE_TYPE_X509`, conforme mostrado abaixo.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. Clique com botão direito do mouse no projeto **prov\_dev\_client\_sample** e selecione **Definir como Projeto de Inicialização**. 

7. No menu do Visual Studio, selecione **Depurar** > **Iniciar sem depuração** para executar a solução. No prompt para recompilar o projeto, selecione **Sim** para recompilar o projeto antes da execução.

    A saída a seguir é um exemplo da inicialização bem-sucedida do exemplo de cliente de dispositivo provisionamento, e da conexão com uma instância de Serviço de Provisionamento de Dispositivos para obter informações e registrar o hub IoT:

    ```cmd
    Provisioning API Version: 1.2.7
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-device
    ```

8. Após o provisionamento do dispositivo simulado no Hub IoT por seu serviço de provisionamento, a ID do dispositivo aparece em **Dispositivos IOT** do hub. 

    ![Dispositivo é registrado no Hub IoT](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>Limpar os recursos

Se planejar continuar a trabalhar e explorar o dispositivo cliente de exemplo, não limpe os recursos criados neste início rápido. Caso contrário, use as seguintes etapas para excluir todos os recursos criados por este início rápido.

1. Feche a janela de saída de exemplo de dispositivo cliente em seu computador.
2. Feche a janela do simulador do TPM no seu computador.
3. No menu à esquerda no portal do Azure, selecione **Todos os recursos** e selecione o serviço de Provisionamento de Dispositivos. Abra **Gerenciar Registros** de seu serviço e selecione a guia **Registros Individuais**. Marque a caixa de seleção ao lado da *ID DE REGISTRO* do dispositivo registrado neste início rápido e pressione o botão **Excluir**, na parte superior do painel. 
4. No menu à esquerda no portal do Azure, selecione **Todos os recursos** e seu Hub IoT. Abra **Dispositivos IoT** do hub, marque a caixa de seleção ao lado da *ID DO DISPOSITIVO* registrado neste início rápido e pressione o botão **Excluir** na parte superior do painel.

## <a name="next-steps"></a>Próximas etapas

Neste Guia de Início Rápido, você criou um dispositivo simulado TPM no seu computador e o provisionou no Hub IoT usando o Serviço de Provisionamento de Dispositivos no Hub IoT. Para saber como registrar seu dispositivo TPM programaticamente, continue com o Guia de Início Rápido para registro programático de um dispositivo TPM. 

> [!div class="nextstepaction"]
> [Início rápido do Azure – Registrar dispositivo TPM no Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](quick-enroll-device-tpm-java.md)