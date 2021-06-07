---
title: Recuperar uma mensagem do Twitter com o Azure Functions | Microsoft Docs
description: Use o sensor de movimento para detectar agitando e usar o Azure Functions para localizar um tweet aleatório com um hashtag que você especificar
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.custom: devx-track-csharp
ms.openlocfilehash: af1685f6455c0642800cba7dd604fcc836bcd7a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147898"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Shake, Shake para um tweet -- Recuperar uma mensagem do Twitter com o Azure Functions

Neste projeto, você aprenderá como usar o sensor de movimento para disparar um evento usando o Azure Functions. O aplicativo recupera um tweet aleatório com um #hashtag que você configura no seu esboço de Arduino. O tweet exibe na tela Kit de Desenvolvimento.

## <a name="what-you-need"></a>O que você precisa

Siga o [Guia de Introdução](./iot-hub-arduino-iot-devkit-az3166-get-started.md) para:

* Ter seu Kit de Desenvolvimento conectado ao Wi-Fi.
* Preparar o ambiente de desenvolvimento.

Uma assinatura ativa do Azure. Se você não tiver uma, você pode registrar por meio de um desses métodos:

* Ative uma [conta do Microsoft Azure de avaliação por 30 dias](https://azure.microsoft.com/free/)
* Solicite seu [crédito do Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se você for um assinante do MSDN ou do Visual Studio

## <a name="open-the-project-folder"></a>Abrir a pasta do projeto

Comece abrindo a pasta do projeto. 

### <a name="start-vs-code"></a>Iniciar o VS Code

* Verifique se o Kit de Desenvolvimento está conectado ao computador.

* Iniciar o VS Code.

* Conecte o DevKit ao computador.

   > [!NOTE]
   > Ao iniciar o VS Code, você receberá uma mensagem de erro que o Arduino IDE ou o pacote de quadro relacionado não pode ser encontrado. Se esse erro ocorrer, feche o VS Code e reinicie o Arduino IDE. O VS Code agora deve localizar o caminho do Arduino IDE corretamente.

### <a name="open-the-arduino-examples-folder"></a>Abra a pasta de exemplos de Arduino

Expanda a seção **EXEMPLOS DO ARDUINO** no canto esquerdo, vá até **Exemplos para MXCHIP AZ3166 > AzureIoT** e selecione **ShakeShake**. É aberta uma nova janela do VS Code com a pasta de projeto nela. Se você não consegue ver os a seção MXCHIP AZ3166, verifique se o seu dispositivo está devidamente conectado e reinicie o Visual Studio Code.  
os ![exemplos de minisolução](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

Você também pode abrir o exemplo da paleta de comandos. Clique em `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, digite **Arduino** e, em seguida, localize e selecione **Arduino: Exemplos**.

## <a name="provision-azure-services"></a>Provisionamento dos serviços do Azure

Na janela de solução, execute a tarefa por meio de `Ctrl+P` (macOS: `Cmd+P`) digitando `task cloud-provision`:.

No terminal do VS Code, uma linha de comando interativa orienta você durante o provisionamento dos serviços do Azure necessários:

![Captura de tela mostra a linha de comando interativa do terminal Visual Studio Code.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Se a página trava no status de carregamento ao tentar entrar no Azure, consulte este a etapa ["login page hangs" na etapa de perguntas frequentes do Kit de Desenvolvimento do IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>Modificar o #hashtag

Abra `ShakeShake.ino` e procure por esta linha de código:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Substitua a cadeia de caracteres `iot` dentro de chaves com seu hashtag preferencial. O Kit de Desenvolvimento posteriormente recupera um tweet aleatório que inclui o hashtag que você especificar nesta etapa.

## <a name="deploy-azure-functions"></a>Implantar o Azure Functions

Use `Ctrl+P` (macOS: `Cmd+P`) para executar `task cloud-deploy` para começar a implantar o código do Azure Functions:

![A captura de tela mostra Visual Studio Code onde você pode executar a tarefa implantação de nuvem para implantar o código Azure Functions.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Ocasionalmente, o Azure Function pode não funcionar corretamente. Para resolver esse problema quando ele ocorre, verifique a [seção de "Erro de compilação" das perguntas frequentes sobre o Kit de Desenvolvimento do IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Compilação e carregamento do código de dispositivo

Compilação e carregamento do código de dispositivo.

### <a name="windows"></a>Windows

1. Use `Ctrl+P` para executar `task device-upload`.

2. O terminal solicita que você entre no modo de configuração. Para fazer isso:

   * Mantenha pressionado o botão A

   * Pressione e solte o botão Reiniciar.

3. A tela exibe a ID do Kit de Desenvolvimento e “Configuração”.

### <a name="macos"></a>macOS

1. Coloque o Kit de Desenvolvimento no modo de configuração:

   Mantenha pressionado o botão A, em seguida, enviar por push e solte o botão reiniciar. A tela exibe “Configuração”.

2. Use `Cmd+P` para executar `task device-upload` para definir a cadeia de conexão que é recuperada da etapa `task cloud-provision`.

### <a name="verify-upload-and-run"></a>Verificar, carregar e executar

Agora a cadeia de caracteres de conexão está definida, ele verifica e carrega o aplicativo e o executa. 

1. O VS Code inicia verificando e carregando o esboço do DevKit:

   ![A captura de tela mostra Visual Studio Code verificar e carregar o esboço Arduino.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. O DevKit reinicia e inicia a execução do código.

Você pode obter uma mensagem de erro "Erro: AZ3166: pacote desconhecido". Esse erro ocorre quando o índice do pacote de quadro não é atualizado corretamente. Para resolver esse problema, verifique o [erro "pacote desconhecido" nas perguntas Frequentes do Kit de desenvolvimento de IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testar o projeto

Após a inicialização do aplicativo, clique e solte o botão A e, em seguida, cuidadosamente agite o quadro Kit de Desenvolvimento. Essa ação recupera um tweet aleatório, que contém o hashtag especificado anteriormente. Em alguns segundos, um tweet é exibe na tela do Kit de Desenvolvimento:

### <a name="arduino-application-initializing"></a>Inicialização do aplicativo Arduino...

![Inicialização-do-aplicativo-Arduino](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Pressione A para agitar...

![Pressione-A-para-agitar](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Pronto para agitar...

![Pronto-para-agitar](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Processando...

![Processing](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Pressione B para leitura...

![Pressione-B-para-leitura](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Exiba um tweet aleatório...

![Exiba-um-tweet-aleatório](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Pressione o botão A novamente e agite para um novo tweet.
- Pressione o botão B para rolar pelo restante do tweet.

## <a name="how-it-works"></a>Como ele funciona

![O diagrama mostra um dispositivo móvel enviando um evento para o Hub T do Azure que dispara um aplicativo de funções do Azure para solicitar um tweet, que é enviado de volta para o aplicativo e encaminhado para o Hub e para o dispositivo móvel.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

O esboço de Arduino envia um evento para o Hub IoT do Azure. Este evento dispara o aplicativo Azure Functions. O aplicativo Azure Functions contém a lógica para se conectar à API do Twitter e recuperar um tweet. Ele envolve, em seguida, o texto escrito em uma mensagem C2D (nuvem para dispositivo) e envia-o novamente para o dispositivo.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Opcional: Usar seu próprio token de portador do Twitter

Para fins de teste, este projeto de exemplo usa um token de portador pré-configurado do Twitter. No entanto, há um [limite de taxa](https://dev.twitter.com/rest/reference/get/search/tweets) para cada conta do Twitter. Se você quiser usar seu próprio token, siga estas etapas:

1. Vá para [portal do Desenvolvedor do Twitter](https://dev.twitter.com/) para registrar um novo aplicativo do Twitter.

2. [Obtenha a Chave do consumidor e Segredos do consumidor](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) do seu aplicativo.

3. Use [algum utilitário](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) para gerar um token de portador do Twitter dessas duas chaves.

4. No [portal do Azure](https://portal.azure.com/){: destino = blank"}, vá para o **Grupo de Recursos** e localize o Azure Function (tipo: serviço de aplicativo) para o projeto"Shake, Shake". O nome contém sempre a cadeia de caracteres 'shake...'.

   ![A captura de tela da portal do Azure mostra um serviço de aplicativo para o projeto.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Atualize o código para `run.csx` em **Funções > shakeshake-cs** com seu próprio token:

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
   ```
  
   ![Captura de tela mostra o código C# para a função em que você pode inserir seu token.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Salve o arquivo e clique em **Executar**.

## <a name="problems-and-feedback"></a>Comentários e problemas

Como solucionar problemas ou fornecer comentários. 

### <a name="problems"></a>Problemas

Um problema que você ver se a tela exibe “Nenhum Tweet”, enquanto cada etapa for executada com êxito. Normalmente, essa condição ocorre pela primeira vez que você implanta e executa o exemplo porque o aplicativo de função requer em qualquer lugar de alguns segundos até um minuto para inicialização a frio do aplicativo. 

Ou, ao executar o código, há alguns blips que causam uma reinicialização do aplicativo. Quando essa condição ocorre, o aplicativo de dispositivo pode obter um tempo limite para buscar o tweet. Nesse caso, você pode tentar um ou ambos os métodos para resolver esse problema:

1. Clique no botão Reiniciar no Kit de Desenvolvimento para executar o aplicativo de dispositivo novamente.

2. No [portal do Azure](https://portal.azure.com/), localize o aplicativo Azure Functions que você criou e reinicie-o:

   ![Captura de tela mostra a portal do Azure com um aplicativo Azure Functions e o botão reiniciar.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

Se você encontrar outros problemas, consulte [as perguntas frequentes do Kit de Desenvolvimento do IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou entre em contato conosco usando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a conectar um dispositivo DevKit ao seu acelerador de solução de Monitoramento Remoto do Azure IoT e recuperar um tweet, aqui estão as próximas etapas sugeridas:

* [Visão geral do acelerador de solução de Monitoramento Remoto do Azure IoT](/azure/iot-suite/)