---
title: Guia de Início Rápido – Provisionar um dispositivo simulado X.509 no Hub IoT do Azure usando C
description: Este início rápido usa registros individuais. Neste início rápido, você criará e provisionará um dispositivo X.509 simulado usando o SDK do dispositivo em C para o DPS (Serviço de Provisionamento de Dispositivos) no Hub IoT do Azure.
author: wesmc7777
ms.author: wesmc
ms.date: 04/26/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 8f4c35a61edc970f5d4d3beb0c4255ccfe4e336a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968138"
---
# <a name="quickstart-provision-an-x509-simulated-device-using-the-azure-iot-c-sdk"></a>Início Rápido: Provisionar um dispositivo simulado X.509 usando o SDK de C do IoT do Azure

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Neste início rápido, você aprenderá a criar e executar um simulador de dispositivo X.509 em um computador de desenvolvimento Windows. Você configurará o dispositivo simulado para ser atribuído a um hub IoT usando um registro em uma instância do Serviço de Provisionamento de Dispositivos. Um código de exemplo do [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) será usado a fim de simular uma sequência de inicialização para o dispositivo. O dispositivo será reconhecido com base no registro do serviço de provisionamento e atribuído ao hub IoT.

Se você não estiver familiarizado com o processo de provisionamento automático, examine a visão geral de [provisionamento](about-iot-dps.md#provisioning-process). Não se esqueça de concluir as etapas em [Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT com o Portal do Azure](quick-setup-auto-provision.md) antes de continuar com este início rápido. 

O Serviço de Provisionamento de Dispositivos de IoT do Azure dá suporte a dois tipos de registros:

* [Grupos de registros](concepts-service.md#enrollment-group): usados para inscrever vários dispositivos relacionados.
* [Registros individuais](concepts-service.md#individual-enrollment): usados para inscrever um único dispositivo.

Este artigo irá demonstrar registros individuais.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos a seguir são para um ambiente de desenvolvimento do Windows. Para Linux ou macOS, confira a seção apropriada em [Preparar seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) na documentação do SDK.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) com a carga de trabalho ["Desenvolvimento para desktop com C++"](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) habilitada. Também há suporte para o Visual Studio 2015 e o Visual Studio 2017.

* Versão mais recente do [Git](https://git-scm.com/download/) instalada.

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Preparar um ambiente de desenvolvimento para o SDK de C do IoT do Azure

Nesta seção, você vai preparar um ambiente de desenvolvimento usado para criar o [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) que inclui o código de exemplo para a sequência de inicialização do X.509.

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

5. O exemplo de código usa um certificado X.509 para fornecer comprovação por meio da autenticação X.509. Execute o comando a seguir para criar uma versão do SDK específica para sua plataforma de desenvolvimento que inclui o cliente de provisionamento de dispositivos. Uma solução do Visual Studio para o dispositivo simulado é gerada no diretório `cmake`.

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```

    Se `cmake` não encontrar o compilador do C++, você poderá obter erros de build ao executar o comando acima. Se isso acontecer, tente executar esse comando no [prompt de comando do Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs).

    Após o sucesso do build, as últimas linhas de saída são semelhantes à seguinte saída:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: C:/code/azure-iot-sdk-c/cmake
    ```

## <a name="create-a-self-signed-x509-device-certificate"></a>Criar um certificado de dispositivo X.509 autoassinado

Nesta seção, você usará um certificado X.509 autoassinado. É importante ter em mente o seguinte:

* Os certificados autoassinados são somente para teste e não devem ser usados em produção.
* A data de validade padrão para um certificado autoassinado é de um ano.

Você usará o código de exemplo do SDK de C do IoT do Azure para criar o certificado a ser usado na entrada de registro individual do dispositivo simulado.

1. Inicie o Visual Studio e abra o arquivo da nova solução chamado `azure_iot_sdks.sln`. Esse arquivo de solução está localizado na pasta `cmake` que você criou anteriormente na raiz do repositório git azure-iot-sdk-c.

2. No menu do Visual Studio, selecione **Compilar** > **Compilar Solução** para compilar todos os projetos na solução.

3. Na janela *Gerenciador de Soluções* do Visual Studio, navegue até a pasta **Provisionar\_Ferramentas**. Clique com o botão direito do mouse no projeto **dice\_device\_enrollment** e selecione **Definir como Projeto de Inicialização**.

4. No menu do Visual Studio, selecione **Depurar** > **Iniciar sem depuração** para executar a solução. Na janela de saída, insira **i** para registro individual quando solicitado.

    A janela de saída exibe um certificado X.509 autoassinado gerado localmente para seu dispositivo simulado. Copie a saída para a área de transferência começando em **-----BEGIN CERTIFICATE-----** e terminando no primeiro **-----END CERTIFICATE-----** , não deixando de incluir essas duas linhas também. Você precisa apenas do primeiro certificado da janela de saída.

5. Usando um editor de texto, salve o certificado em um novo arquivo chamado **_X509testcert.pem_** .

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Criar uma entrada de registro de dispositivo no portal

1. Entre no portal do Azure, selecione o botão **Todos os recursos** no menu esquerdo e abra o serviço de Provisionamento de Dispositivos.

2. Selecione a guia **Gerenciar registros**, então selecione o botão **Adicionar registro individual** na parte superior.

3. No painel **Adicionar Registro**, insira as informações a seguir, então pressione o botão **Salvar**.

    * **Mecanismo:** Selecione **X.509** como o *Mecanismo* de atestado de identidade.
    * **Arquivo .pem ou .cer de certificado primário:** Escolha **Selecionar um arquivo** para selecionar o arquivo de certificado X509testcert.pem que você criou anteriormente.
    * **ID do Dispositivo de Hub IoT:** insira **test-docs-cert-device** para dar uma ID ao dispositivo.

      [![Adicionar um registro individual para atestado de X.509 no portal](./media/quick-create-simulated-device-x509/device-enrollment.png)](./media/quick-create-simulated-device-x509/device-enrollment.png#lightbox)

      No registro bem-sucedido, o dispositivo X.509 é exibido como **riot-device-cert** na coluna *ID do Registro* na guia o *Registros Individuais*. 

## <a name="simulate-first-boot-sequence-for-the-device"></a>Simular a primeira sequência de inicialização para o dispositivo

Nesta seção, atualize o código de exemplo para enviar a sequência de inicialização do dispositivo para a instância do Serviço de Provisionamento de Dispositivos. Essa sequência de inicialização fará com que o dispositivo seja reconhecido e atribuído a um hub IoT vinculado à instância do Serviço de Provisionamento de Dispositivo.

1. No portal do Azure, selecione a guia **Visão Geral** de seu serviço de Provisionamento de Dispositivos e anote o valor de **_Escopo da ID_** .

    ![Extrair informações do ponto de extremidade do Serviço de Provisionamento de Dispositivo na folha do portal](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Na janela *Gerenciador de Soluções* do Visual Studio, navegue até a pasta **Provisionar\_Exemplos**. Expanda o projeto de exemplo chamado **prov\_dev\_client\_sample**. Expanda **Arquivos de Origem** e abra **prov\_dev\_client\_sample.c**.

3. Localize a constante `id_scope` e substitua o valor pelo seu valor de **Escopo de ID** copiado anteriormente. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

4. Encontre a definição da função `main()` no mesmo arquivo. Verifique se a variável `hsm_type` está definida como `SECURE_DEVICE_TYPE_X509` em vez de `SECURE_DEVICE_TYPE_TPM`, conforme mostrado abaixo.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. Clique com botão direito do mouse no projeto **prov\_dev\_client\_sample** e selecione **Definir como Projeto de Inicialização**.

6. No menu do Visual Studio, selecione **Depurar** > **Iniciar sem depuração** para executar a solução. No prompt para recompilar o projeto, selecione **Sim** para recompilar o projeto antes da execução.

    A saída a seguir é um exemplo da inicialização bem-sucedida do exemplo de cliente de dispositivo provisionamento e da conexão com uma instância do Serviço de Provisionamento de Dispositivos para obter informações do hub IoT e registrá-lo:

    ```cmd
    Provisioning API Version: 1.2.7

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-cert-device
    ```

7. No portal, navegue até o Hub IoT vinculado ao seu serviço de provisionamento e selecione a guia **Dispositivos IoT**. No provisionamento bem-sucedido do dispositivo X.509 simulado para o hub, sua ID de dispositivo aparecerá na folha **Dispositivos IoT** com o *STATUS* como **habilitado**. Talvez seja necessário pressionar o botão **Atualizar** na parte superior. 

    ![Dispositivo é registrado no Hub IoT](./media/quick-create-simulated-device-x509/hub-registration.png) 

## <a name="clean-up-resources"></a>Limpar os recursos

Se planejar continuar a trabalhar e explorar o dispositivo cliente de exemplo, não limpe os recursos criados neste início rápido. Caso contrário, use as seguintes etapas para excluir todos os recursos criados por este início rápido.

1. Feche a janela de saída de exemplo de dispositivo cliente em seu computador.
1. No menu à esquerda no portal do Azure, selecione **Todos os recursos** e selecione o serviço de Provisionamento de Dispositivos. Abra **Gerenciar Registros** de seu serviço e selecione a guia **Registros Individuais**. Marque a caixa de seleção ao lado da *ID DE REGISTRO* do dispositivo registrado neste início rápido e pressione o botão **Excluir**, na parte superior do painel. 
1. No menu à esquerda no portal do Azure, selecione **Todos os recursos** e seu Hub IoT. Abra **Dispositivos IoT** do hub, marque a caixa de seleção ao lado da *ID DO DISPOSITIVO* registrado neste início rápido e pressione o botão **Excluir** na parte superior do painel.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um dispositivo simulado X.509 no seu computador Windows e o provisionou no Hub IoT usando o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure no portal. Para saber como registrar seu dispositivo X.509 programaticamente, continue com o início rápido do registro programático de dispositivos X.509. 

> [!div class="nextstepaction"]
> [Início rápido do Azure – Registrar dispositivos X.509 no Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](quick-enroll-device-x509-java.md)