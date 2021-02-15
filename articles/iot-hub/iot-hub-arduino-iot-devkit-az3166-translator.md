---
title: Tornar o tradutor w/Azure Functions, serviços cognitivas, IoT DevKit
description: Use o microfone no Kit de Desenvolvimento da IoT para receber uma mensagem de voz e os Serviços Cognitivos do Azure para processá-la em um texto traduzido em inglês
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.custom: devx-track-csharp
ms.openlocfilehash: be26c6fe03dac9b9ff9dbff4a2bdce391ec0837e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024156"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Usar o Kit de Desenvolvimento da IoT AZ3166 com um Azure Functions e os Serviços Cognitivos para criar um tradutor de idiomas

Neste artigo, você aprende a tornar o Kit de Desenvolvimento da IoT um tradutor de idiomas usando os [Serviços Cognitivos do Azure](https://azure.microsoft.com/services/cognitive-services/). Ele registra sua voz e a traduz para o texto em inglês mostrado na tela do Kit de Desenvolvimento.

O [MXChip IoT DevKit](https://aka.ms/iot-devkit) é uma placa compatível com Arduino all-in-one periféricos e sensores avançados. É possível desenvolvê-lo usando o [Workbench do dispositivo de IoT do Azure](https://aka.ms/iot-workbench) ou pacote de extensão com as ferramentas do [ Azure IoT](https://aka.ms/azure-iot-tools) no Visual Studio Code. O [catálogo de projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) contém aplicativos de exemplo para ajudá-lo a criar protótipos de soluções de IoT.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste tutorial, faça primeiro as tarefas a seguir:

* Prepare o seu Kit de Desenvolvimento, seguindo as etapas em [Conectar Kit de Desenvolvimento da IoT AZ3166 ao Hub IoT do Azure na nuvem](./iot-hub-arduino-iot-devkit-az3166-get-started.md).

## <a name="create-azure-cognitive-service"></a>Criar Serviços Cognitivos do Azure

1. No portal do Azure, clique em **criar um recurso** e pesquise por **fala**. Preencha o formulário para criar o Serviço de Fala.
  ![Serviço de Fala](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Vá para o Serviço de Fala que acabou de criar, clique na seção de **Teclas** para copiar e anote o **Key1** para acessar o Kit de desenvolvimento através dele.
  ![Copiar chaves](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Abra um projeto de exemplo

1. Verifique se o kit de desenvolvimento de IoT **não está conectado** ao computador. Inicie o VS Code primeiro e, em seguida, conecte o kit de desenvolvimento ao computador.

1. Clique em `F1` para abrir a paleta de comandos, digite e selecione **Workbench dos Dispositivos de IoT do Azure: Abrir Exemplos...** . Em seguida, selecione **Kit de Desenvolvimento de IoT** como placa.

1. Na página exemplos do IoT Workbench, encontre o **Tradutor devkit** e clique em **abrir exemplo**. Em seguida, selecione o caminho padrão para baixar o código de exemplo.
  ![Abrir exemplo](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Usar o Serviço de Fala com o Azure Functions

1. Em VS Code, clique em `F1` , digite e selecione **Azure IOT Device Workbench: provisionar serviços do Azure...**. ![ Provisionar serviços do Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Siga as etapas para concluir o provisionamento do Hub IoT e do Azure Functions.
   ![Provisionar passos](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   Anote o nome do dispositivo do Hub IoT do Azure que você criou.

1. Abra `Functions\DevKitTranslatorFunction.cs` e atualize as linhas de código a seguir com o nome do dispositivo e a chave do serviço de fala que você anotou.
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. Clique em `F1` , digite e selecione **Azure IOT Device Workbench: implantar no Azure...**. Se VS Code solicitar confirmação para reimplantação, clique em **Sim**.
   ![Implantar o aviso](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Verifique se a implantação foi bem-sucedida.
   ![Implantar o sucesso](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. No portal do Azure, acesse a sessão **Aplicativos de funções** e localize o aplicativo de funções do Azure que acabou de criar. Clique em `devkit_translator`, depois clique em **</> Obter URL de função** para copiar a URL.
   ![Copiar URL de função](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Cole a URL no arquivo `azure_config.h`.
   ![Configuração do Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > Se o aplicativo de funções não funcionar corretamente, confira esta seção de [Perguntas frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) para resolver o problema.

## <a name="build-and-upload-device-code"></a>Compilação e carregamento do código de dispositivo

1. Alternar o DevKit no **modo de configuração** para:
   * Mantenha pressionado o botão **A**.
   * Pressione e solte a o botão **Redefinir**.

   A tela exibe a ID do Kit de Desenvolvimento e **Configuração**.

   ![Modo de configuração do Kit de Desenvolvimento](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Clique em `F1` , digite e selecione **Azure IOT Device Workbench: definir configurações de dispositivo... > cadeia de conexão do dispositivo de configuração**. Selecione **Selecione a Cadeia de conexão do dispositivo Hub IoT** para configurá-lo para o Kit de desenvolvimento.
   ![Configurar cadeia de conexão](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Ao completar com sucesso, uma notificação aparecerá.
   ![Configurar cadeias de conexão com sucesso](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Clique em `F1` novamente, digite e selecione **Workbench dos Dispositivos de IoT do Azure: Carregar o código de dispositivo**. Ele começa a compilar e carregar o código para o Kit de desenvolvimento.
   ![Upload de dispositivo](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>Testar o projeto

Após a inicialização do aplicativo, siga as instruções na tela do DevKit. O idioma de origem padrão é o chinês.

Para selecionar outro idioma para tradução:

1. Selecione o botão A para entrar no modo de instalação.

2. Selecione o botão B para rolar a página e ver todos os idiomas de origem compatíveis.

3. Pressione o botão A para confirmar sua escolha de idioma de origem.

4. Selecione e segure o botão B enquanto fala e solte o botão B para iniciar a tradução.

5. O texto traduzido em inglês será mostrado na tela.

![Percorrer a lista para selecionar o idioma](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Resultado da tradução](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

Na tela do resultado da tradução, você poderá:

- Selecionar os botões A e B para rolar a página e selecionar o idioma de origem.

- Selecionar o botão B para falar. Para enviar a voz e obter o texto de tradução, solte o botão B.

## <a name="how-it-works"></a>Como ele funciona

![mini-solution-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

O Kit de desenvolvimento do IoT grava sua voz e, em seguida, envia uma solicitação HTTP para engatilhar o Azure Functions. O Azure Functions chama a API de tradução de fala do serviço cognitivo para fazer a tradução. Depois que o Azure Functions obtém o texto de tradução, ele envia uma mensagem C2D para o dispositivo. Em seguida, a tradução é exibida na tela.

## <a name="problems-and-feedback"></a>Comentários e problemas

Se você encontrar problemas, consulte [as perguntas frequentes do kit de desenvolvedores da IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou entre em contato conosco usando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como usar o DevKit IoT como tradutor usando Azure Functions e Serviços Cognitivos. Neste tópico, você aprendeu a:

> [!div class="checklist"]
> * Usar a tarefa do Visual Studio Code para automatizar os provisionamentos de nuvem
> * Configurar a cadeia de conexão do dispositivo IoT do Azure
> * Implantar a Azure Function
> * Testar a tradução da mensagem de voz

Avance para os outros tutoriais para aprender:

> [!div class="nextstepaction"]
> [Conectar o IoT DevKit AZ3166 ao Azure acelerador de solução de Monitoramento Remoto](./iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring.md)