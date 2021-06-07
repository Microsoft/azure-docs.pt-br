---
title: 'Tutorial: habilite seu bot por voz usando o SDK de Fala – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você criará um Echo Bot usando o Microsoft Bot Framework, implantará esse bot no Azure e o registrará com o canal Direct Line Speech do Bot Framework. Em seguida, você vai configurar um aplicativo cliente de exemplo para Windows que lhe permitirá falar com o bot e ouvir ele responder a você.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: fa449ad3d9a0e26bd0754a67581c8d63fa025e55
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552301"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Tutorial: habilite seu bot por voz usando o SDK de Fala

Você pode usar o serviço de Fala para habilitar um chatbot por voz.

Neste tutorial, você criará um bot que repete o que você diz a ele.
Você criará o seu bot usando o Microsoft Bot Framework, implantará o bot no Azure e o registrará com o canal Direct Line Speech do Bot Framework.
Em seguida, você vai configurar um aplicativo cliente de exemplo para Windows que lhe permitirá falar com o bot e ouvir ele responder a você.

Este tutorial foi criado para desenvolvedores que são novos no Azure, em bots do Bot Framework, na Direct Line Speech ou no SDK de Fala, e que desejam criar rapidamente um sistema funcional com codificação limitada. Não requer qualquer experiência ou familiaridade com esses serviços.

O chatbot habilitado por voz que você fará neste tutorial segue estas etapas:

1. O aplicativo cliente de exemplo está configurado para se conectar ao canal da Direct Line Speech e ao Echo Bot.
1. Quando o usuário pressiona um botão, os áudios de voz são transmitidos pelo microfone. (Ou o áudio é gravado continuamente quando uma palavra-chave personalizada é usada.)
1. Se uma palavra-chave personalizada for usada, a detecção de palavras-chave ocorrerá no dispositivo local, direcionando o streaming de áudio para a nuvem.
1. Usando o SDK de Fala, o aplicativo cliente de exemplo se conecta ao canal da Direct Line Speech e transmite áudio.
1. Opcionalmente, uma verificação de palavra-chave com precisão mais alta ocorre no serviço.
1. O áudio é passado para o serviço de reconhecimento de fala e transcrito para texto.
1. O texto reconhecido é passado para o Echo Bot como uma atividade do Bot Framework.
1. O texto de resposta é transformado em áudio pelo serviço de TTS (conversão de texto em fala) e transmitido de volta ao aplicativo cliente para reprodução.

<!-- svg src in User Story 1754106 -->
![diagram-tag](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "Fluxo do Canal de Fala")

> [!NOTE]
> As etapas deste tutorial não exigem um serviço pago. Como um novo usuário do Azure, você poderá usar créditos de sua assinatura de avaliação gratuita do Azure e a camada gratuita do serviço de Fala para concluir este tutorial.

Este tutorial abrange:
> [!div class="checklist"]
> * Criar novos recursos do Azure
> * Criar, testar e implantar o exemplo do Echo Bot em um Serviço de Aplicativo do Azure
> * Registrar seu bot com o canal da Direct Line Speech
> * Criar e executar o Cliente do Assistente de Voz do Windows para interagir com o seu Echo Bot
> * Adicionar ativação por palavra-chave personalizada
> * Aprender a alterar o idioma da linguagem reconhecida e falada

## <a name="prerequisites"></a>Pré-requisitos

Veja o que será necessário para concluir este tutorial:

- Um PC com Windows 10 com microfone e alto-falantes (ou fones de ouvido) funcionando
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) ou superior, com a carga de trabalho do **ASP.NET e de desenvolvimento Web** instalada
- [.NET Framework Runtime 4.6.1](https://dotnet.microsoft.com/download) ou superior
- Uma conta do Azure. [Inscreva-se gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
- Uma conta do [GitHub](https://github.com/)
- [Git para Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

O aplicativo cliente que você criará neste tutorial usa uma série de serviços do Azure. Para reduzir o tempo de ida e volta das respostas do bot, é preciso verificar se esses serviços estão localizados na mesma região do Azure. Nesta seção, você criará um grupo de recursos na região **Oeste dos EUA**. Esse grupo de recursos será usado ao criar recursos individuais para o Bot Framework, o canal da Direct Line Speech e o serviço de Fala.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">Criar um grupo de recursos</a>
1. Será solicitado que você forneça algumas informações:
   * Defina a **Assinatura** como **Avaliação Gratuita** (você também pode usar uma assinatura existente).
   * Insira um nome para o seu **Grupo de recursos**. Recomendamos **SpeechEchoBotTutorial-ResourceGroup**.
   * Na lista suspensa **Região**, selecione **Oeste dos EUA**.
1. Clique em **Examinar e criar**. Você verá uma faixa com o texto **Validação aprovada**.
1. Clique em **Criar**. Levará poucos minutos para criar o grupo de recursos.
1. Assim como para os outros recursos que você criará posteriormente neste tutorial, é uma boa ideia fixar esse grupo de recursos no seu painel para facilitar o acesso. Se você quiser fixar esse grupo de recursos, clique no ícone de fixação, à direita do nome do grupo de recursos.

### <a name="choosing-an-azure-region"></a>Escolhendo uma região do Azure

Se você quiser usar uma região diferente neste tutorial, estes fatores poderão limitar suas opções:

* Verifique se está usando uma [região do Azure com suporte](regions.md#voice-assistants).
* O canal da Direct Line Speech usa o serviço de conversão de texto em fala, que tem vozes neural e padrão. Vozes neural e padrão estão disponíveis nessas [regiões do Azure](regions.md#neural-and-standard-voices).

Para obter mais informações sobre as regiões, confira [localizações do Azure](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="create-resources"></a>Criar recursos

Agora que você tem um grupo de recursos em uma região com suporte, a próxima etapa é criar recursos individuais para cada serviço que você usará neste tutorial.

### <a name="create-a-speech-service-resource"></a>Criar um recurso do serviço de Fala

Siga estas instruções para criar um recurso de Fala:

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Criar um recurso do serviço de Fala</a>
4. Será solicitado que você forneça algumas informações:
   * Dê um **Nome** ao recurso. Recomendamos **SpeechEchoBotTutorial-Speech**
   * Para **Assinatura**, verifique se a **Avaliação Gratuita** está selecionada.
   * Para **Localização**, selecione **Oeste dos EUA**.
   * Para **Tipo de preço**, selecione **F0**. Essa é a camada gratuita.
   * Para **Grupo de recursos**, selecione **SpeechEchoBotTutorial-ResourceGroup**.
5. Depois de inserir todas as informações necessárias, clique em **Criar**. A criação do seu recurso pode levar alguns minutos.
6. Mais tarde, neste tutorial, você precisará de chaves de assinatura para esse serviço. Você pode acessar essas chaves a qualquer momento por meio da **Visão Geral** (gerenciar chaves) ou das **Chaves** do seu recurso.

Neste ponto, verifique se o seu grupo de recursos (**SpeechEchoBotTutorial-ResourceGroup**) tem um recurso de Fala:

| Nome | Tipo  | Location |
|------|-------|----------|
| SpeechEchoBotTutorial-Speech | Serviços Cognitivos | Oeste dos EUA |

### <a name="create-an-azure-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo do Azure

A próxima etapa é criar um Plano de Serviço de Aplicativo. Um plano de serviço de aplicativo define um conjunto de recursos de computação para um aplicativo Web ser executado.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">Criar um plano do Serviço de Aplicativo do Azure</a>
4. Será solicitado que você forneça algumas informações:
   * Defina a **Assinatura** como **Avaliação Gratuita** (você também pode usar uma assinatura existente).
   * Para **Grupo de recursos**, selecione **SpeechEchoBotTutorial-ResourceGroup**.
   * Dê um **Nome** ao recurso. Recomendamos **SpeechEchoBotTutorial-AppServicePlan**
   * Para **Sistema Operacional**, selecione **Windows**.
   * Para **Região**, selecione **Oeste dos EUA**.
   * Para **Tipo de preço**, verifique se **Padrão S1** está selecionado. Esse deve ser o valor padrão. Se não for, defina o **Sistema Operacional** como **Windows**, conforme descrito acima.
5. Clique em **Examinar e criar**. Você verá uma faixa com o texto **Validação aprovada**.
6. Clique em **Criar**. Levará poucos minutos para criar o grupo de recursos.

Neste ponto, verifique se o seu grupo de recursos (**SpeechEchoBotTutorial-ResourceGroup**) tem dois recursos:

| Nome | Tipo  | Location |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | Plano do Serviço de Aplicativo | Oeste dos EUA |
| SpeechEchoBotTutorial-Speech | Serviços Cognitivos | Oeste dos EUA |

## <a name="build-an-echo-bot"></a>Criar um Echo Bot

Agora que você já criou alguns recursos, vamos criar um bot. Vamos começar com o exemplo de Echo Bot, que como o nome indica, ecoa o texto que você inseriu como resposta. Não se preocupe, o código de exemplo está pronto para uso sem nenhuma alteração. Ele está configurado para funcionar com o canal da Direct Line Speech, com que vamos nos conectar após implantarmos o bot no Azure.

> [!NOTE]
> As instruções a seguir, bem como informações adicionais sobre o Echo Bot, estão disponíveis no [LEIAME do exemplo no GitHub](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md).

### <a name="run-the-bot-sample-on-your-machine"></a>Executar o exemplo de bot no seu computador

1. Clonar o repositório de exemplos:

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Inicie o Visual Studio.
3. Na barra de ferramentas, selecione **Arquivo** > **Abrir** > **Projeto/Solução** e abra a solução do Projeto de Echo Bot:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. Depois que o projeto for carregado, pressione <kbd>F5</kbd> para criar e executar o projeto.
5. Um navegador será iniciado, e você verá uma tela semelhante a esta.
    > [!div class="mx-imgBorder"]
    > [![Captura de tela mostrando a página do EchoBot com a mensagem Seu bot está pronto!](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "EchoBot em execução no localhost")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Testar o exemplo de bot com o Bot Framework Emulator

O [Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) é um aplicativo da área de trabalho que permite aos desenvolvedores de bot testar e depurar os bots deles localmente (ou remotamente, por meio de um túnel). O Emulator aceita texto digitado como entrada (não voz). O bot também responderá com texto. Siga estas etapas para usar o Bot Framework Emulator para testar seu Echo Bot em execução localmente, com entrada e saída de texto. Depois de implantar o bot no Azure, vamos testá-lo com entrada de voz e saída de voz.

1. Instale o [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) versão 4.3.0 ou superior
2. Inicie o Bot Framework Emulator e abra o seu bot:
   * **Arquivo** -> **Abrir bot**.
3. Insira a URL para o seu bot. Por exemplo:

   ```
   http://localhost:3978/api/messages
   ```
   e pressione "Conectar".
4. O bot deve lhe cumprimentar com a frase "Olá e seja bem-vindo!" . Digite qualquer mensagem de texto e confirme se você obtém uma resposta do bot.
5. Essa deve ser a aparência de uma troca de comunicação com uma instância do Echo Bot: [![Captura de tela mostrando o Bot Framework Emulator.](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Bot Framework Emulator")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Implantar seu bot em um Serviço de Aplicativo do Azure

A etapa seguinte é implantar o Echo Bot no Azure. Há algumas maneiras de implantar um bot, mas neste tutorial vamos nos concentrar na publicação diretamente por meio do Visual Studio.

> [!NOTE]
> Como alternativa, você pode implantar um bot usando a [CLI do Azure](/azure/bot-service/bot-builder-deploy-az-cli) e [modelos de implantação](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/adaptive-dialog/03.core-bot).

> [!NOTE]
> Se a opção **Publicar...** não aparecer à medida que você executar as etapas a seguir, use o instalador do Visual Studio para adicionar a carga de trabalho **ASP.NET e de desenvolvimento Web**.

1. No Visual Studio, abra o Echo Bot que foi configurado para uso com o canal da Direct Line Speech:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **EchoBot** e selecione **Publicar...**
1. Uma nova janela intitulada **Publicar** será aberta.
1. Selecione **Azure**, clique em **Avançar**, selecione **Serviço de Aplicativo do Azure (Windows)** , clique em **Avançar** e selecione **Criar um Serviço de Aplicativo do Azure...** ao lado do sinal de adição verde.
1. Quando a janela do **Serviço de Aplicativo (Windows)** for exibida:
   * Clique em **Adicionar uma conta** e entre com suas credenciais de conta do Azure. Se você já estiver conectado, selecione a conta que deseja na lista suspensa.
   * Em **Nome**, você precisará inserir um nome globalmente exclusivo para o seu bot. Esse nome será usado para criar uma URL exclusiva para o seu bot. Um valor padrão será preenchido, incluindo a data e a hora (por exemplo: "EchoBot20190805125647"). Neste tutorial, você pode usar o nome padrão.
   * Para **Assinatura**, defina-a como **Avaliação Gratuita**
   * Para **Grupo de Recursos**, selecione **SpeechEchoBotTutorial-ResourceGroup**
   * Para **Plano de Hospedagem**, selecione **SpeechEchoBotTutorial-AppServicePlan**
1. Clique em **Criar**. Na tela final do assistente, clique em **Concluir**.
1. Clique em **Publicar**, do lado direito da tela de publicação. O Visual Studio implanta o bot no Azure.
1. Você verá uma mensagem de sucesso na janela de saída do Visual Studio que tem esta aparência:

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. O navegador padrão deve abrir e exibir uma página com o texto: "Seu bot está pronto!".
1. Neste ponto, verifique o Grupo de Recursos **SpeechEchoBotTutorial-ResourceGroup** no portal do Azure e confirme esses três recursos:

| Nome | Tipo  | Location |
|------|-------|----------|
| EchoBot20190805125647 | Serviço de Aplicativo | Oeste dos EUA |
| SpeechEchoBotTutorial-AppServicePlan | Plano do Serviço de Aplicativo | Oeste dos EUA |
| SpeechEchoBotTutorial-Speech | Serviços Cognitivos | Oeste dos EUA |

## <a name="enable-web-sockets"></a>Habilitar soquetes Web

Você precisará fazer uma pequena alteração de configuração para que o bot possa se comunicar com o canal da Direct Line Speech usando os WebSockets. Siga estas etapas para habilitar os WebSockets:

1. Navegue até o [portal do Azure](https://portal.azure.com) e clique no seu Serviço de Aplicativo. O recurso deve ser nomeado de maneira semelhante a **EchoBot20190805125647** (seu nome de aplicativo exclusivo).
2. No painel de navegação esquerdo, em **Configurações**, clique em **Configuração**.
3. Selecione a guia **Configurações Gerais**.
4. Localize a alternância para **WebSockets** e defina-a como **Ativado**.
5. Clique em **Save** (Salvar).

> [!TIP]
> Você pode usar os controles da parte superior da página do Serviço de Aplicativo do Azure para interromper ou reiniciar o serviço. Isso pode ser útil ao solucionar problemas.

## <a name="create-a-channel-registration"></a>Criar um registro de canal

Agora que você criou um Serviço de Aplicativo do Azure para hospedar o seu bot, a próxima etapa é criar um **Registro de canais de bot**. A criação de um registro de canal é um pré-requisito para registrar seu bot com canais do Bot Framework, incluindo o canal da Direct Line Speech. Se você quiser saber mais sobre como os bots usam canais, confira [Conectar um bot a canais](/azure/bot-service/bot-service-manage-channels).

1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">Criar um registro de canais de bot do Azure </a>
2. Será solicitado que você forneça algumas informações:
   * Para o **identificador do bot**, insira **SpeechEchoBotTutorial-BotRegistration-####** e substitua **####** por um número de sua escolha. Observe que o identificador do bot deve ser globalmente exclusivo. Se você inserir um identificador do bot, mas receber a mensagem _A ID de bot solicitada não está disponível_, então selecione outro número. Nos exemplos abaixo, usamos 8726
   * Para **Assinatura**, selecione **Avaliação Gratuita**.
   * Para **Grupo de recursos**, selecione **SpeechEchoBotTutorial-ResourceGroup**.
   * Para **Localização**, selecione **Oeste dos EUA**.
     * Para **Tipo de preço**, selecione **F0**.
     * Para **Ponto de extremidade de mensagens**, insira a URL para seu aplicativo Web com o caminho `/api/messages` anexado no final. Por exemplo: se o nome globalmente exclusivo do aplicativo fosse **EchoBot20190805125647**, seu ponto de extremidade de mensagens seria: `https://EchoBot20190805125647.azurewebsites.net/api/messages/`.
     * Para o **Application Insights**, você pode definir isso como **Desativado**. Para obter mais informações, confira [Análise de bot](/azure/bot-service/bot-service-manage-analytics).
     * Ignore **Criar automaticamente a senha e a ID do Aplicativo**.
5. Na parte inferior da folha de **Registro de canais de bot**, clique em **Criar**.

Neste ponto, verifique seu grupo de recursos (**SpeechEchoBotTutorial-ResourceGroup**) no portal do Azure. Agora, ele deve mostrar pelo menos quatro recursos:

| Nome | Tipo  | Location |
|------|-------|----------|
| EchoBot20190805125647 | Serviço de Aplicativo | Oeste dos EUA |
| SpeechEchoBotTutorial-AppServicePlan | Plano do Serviço de Aplicativo | Oeste dos EUA |
| SpeechEchoBotTutorial-BotRegistration-8726 | Registro de canais do bot | Global |
| SpeechEchoBotTutorial-Speech | Serviços Cognitivos | Oeste dos EUA |

> [!IMPORTANT]
> O recurso de registro de canais de bot mostrará a região global, embora você tenha selecionado Oeste dos EUA. Isso é esperado.

## <a name="optional-test-in-web-chat"></a>Opcional: testar no webchat

A página de registro de canais de bot do Azure tem uma opção **Testar no webchat** em **Gerenciamento de bot**. Ele não funcionará por padrão com o bot, uma vez que o webchat precisa se autenticar em seu bot. Se você quiser testar o bot implantado com entrada de texto, siga as etapas abaixo. Observe que essas etapas são opcionais e não são necessárias para continuar com as próximas etapas do tutorial. 

1. Localizar e abrir seu recurso **EchoBotTutorial-BotRegistration-####** no [portal do Azure](https://portal.azure.com)
1. Na navegação de **Gerenciamento de bot**, selecione **Configurações**. Copie o valor em **ID do Aplicativo da Microsoft**
1. Abra a solução Visual Studio EchoBot. No Gerenciador de Soluções, localize e clique duas vezes em **appsettings.json**
1. Substitua a cadeia de caracteres vazia ao lado de **MicrosoftAppId** no arquivo JSON pelo valor de ID copiado
1. Retorne ao Portal do Azure, na navegação de **Gerenciamento de bot**, selecione **Configurações** e clique em **(gerenciar)** ao lado de **ID do Aplicativo da Microsoft**
1. Clique em **Novo segredo do cliente**. Adicione uma descrição (por exemplo, "webchat") e clique em **Adicionar**. Copiar o novo segredo
1. Substituir a cadeia de caracteres vazia ao lado de **MicrosoftAppPassword** no arquivo JSON com o valor de segredo copiado
1. Salve o arquivo JSON. Ele deverá ser semelhante a este:
```json
{
  "MicrosoftAppId": "3be0abc2-ca07-475e-b6c3-90c4476c4370",
  "MicrosoftAppPassword": "-zRhJZ~1cnc7ZIlj4Qozs_eKN.8Cq~U38G"
}
```
9. Publicar o aplicativo novamente (clique com o botão direito do mouse no projeto **EchoBot** no Gerenciador de Soluções do Visual Studio, selecione **Publicar...** e clique no botão **Publicar**)
10. Agora está tudo pronto para testar o bot no webchat.

## <a name="register-the-direct-line-speech-channel"></a>Registrar o canal da Direct Line Speech

Agora é hora de registrar seu bot com o canal da Direct Line Speech. Esse canal cria uma conexão entre o bot e um aplicativo cliente compilado com o SDK de fala.

1. Localize e abra seu recurso **SpeechEchoBotTutorial-BotRegistration-####** no [portal do Azure](https://portal.azure.com).
1. Na navegação de **Gerenciamento de bot**, selecione **Canais**.
   * Em **Mais canais**, clique em **Direct line Speech**.
   * Examine o texto na página intitulada **Configurar a Direct Line Speech** e, em seguida, expanda o menu suspenso **Conta do Serviço Cognitivo**.
   * Selecione o recurso de Fala que você criou anteriormente (por exemplo, **SpeechEchoBotTutorial-Speech**) no menu para associar o seu bot à chave de assinatura de fala.
   * Ignore o restante dos campos opcionais.
   * Clique em **Save** (Salvar).

1. Na navegação do **Gerenciamento de bot**, clique em **Configurações**.
   * Marque a caixa rotulada **Habilitar ponto de extremidade de streaming**. Isso é necessário para criar um protocolo de comunicação baseado em WebSockets entre o bot e o canal da Direct Line Speech.
   * Clique em **Save** (Salvar).

> [!TIP]
> Se você quiser saber mais, confira [Conectar um bot à Direct Line Speech](/azure/bot-service/bot-service-channel-connect-directlinespeech). Esta página inclui informações adicionais e problemas conhecidos.

## <a name="run-the-windows-voice-assistant-client"></a>Executar o cliente do assistente de voz do Windows

Nesta etapa, você executará o cliente do assistente de voz do Windows. O cliente é um aplicativo do WPF (Windows Presentation Foundation) em C# que usa o [SDK de Fala](./speech-sdk.md) para gerenciar a comunicação com o seu bot usando o canal da Direct Line Speech. Use-o para interagir com o seu bot e para testá-lo antes de gravar um aplicativo cliente personalizado. Ele é software livre, então você pode baixar o executável e usá-lo ou criá-lo por conta própria.

O cliente do assistente de voz do Windows tem uma interface do usuário simples que permite configurar a conexão com o bot, exibir a conversa de texto, exibir as atividades do Bot Framework no formato JSON e exibir cartões adaptáveis. Ele também dá suporte ao uso de palavras-chave personalizadas. Você usará esse cliente para falar com o bot e receber uma resposta de voz.

> [!NOTE]
> Neste ponto, confirme se o microfone e os alto-falantes estão habilitados e funcionando.

1. Navegue até o repositório do GitHub para o [Cliente do Assistente de Voz do Windows](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md).
1. Siga as instruções fornecidas ali para
   * baixar um executável predefinido em um pacote ZIP a fim de executá-lo ou
   * crie o executável por conta própria, clonando o repositório e criando o projeto.

1. Inicie o aplicativo cliente `VoiceAssistantClient.exe` e configure-o para se conectar ao bot, seguindo as instruções no repositório do GitHub.
1. Clique em **Reconectar** e verifique se você vê a mensagem **Nova conversa iniciada – Digite ou pressione o botão do microfone**.
1. Vamos testá-lo. Clique no botão do microfone e fale com algumas palavras em inglês. O texto reconhecido será exibido à medida que você falar. Quando você terminar de falar, o bot responderá com a voz dele, dizendo "Eco" e depois as palavras reconhecidas.
1. Você também pode usar texto para se comunicar com o bot. Basta digitar o texto na barra inferior. 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>Solucionando problemas de erros no Cliente do Assistente de Voz do Windows

Se você receber uma mensagem de erro na janela do aplicativo principal, use esta tabela para identificar e solucionar o erro:

| Erro | O que você deve fazer? |
|-------|----------------------|
|Erro (AuthenticationFailure): falha na atualização do WebSocket com um erro de autenticação (401). Verificar se a chave de assinatura (ou o token de autorização) e o nome da região estão corretos| Na página Configurações do aplicativo, verifique se você inseriu a chave de Assinatura de Fala e sua região corretamente.<br>Verifique se a sua Chave de Fala e a sua região principal foram inseridas corretamente. |
|Erro (ConnectionFailure): a conexão foi encerrada pelo host remoto. Código de erro: 1011. Detalhes do erro: não foi possível conectar ao bot antes de enviar uma mensagem | Verifique se você [marcou a caixa "Habilitar ponto de extremidade de streaming"](#register-the-direct-line-speech-channel) e/ou [alternou os **WebSockets**](#enable-web-sockets) para ativado.<br>Verifique se o seu Serviço de Aplicativo do Azure está em execução. Se estiver, tente reiniciar o Serviço de Aplicativo.|
|Erro (ConnectionFailure): a conexão foi encerrada pelo host remoto. Código de erro: 1002. Detalhes do erro: o servidor retornou o código de status '503' quando era esperado o código de status '101' | Verifique se você [marcou a caixa "Habilitar ponto de extremidade de streaming"](#register-the-direct-line-speech-channel) e/ou [alternou os **WebSockets**](#enable-web-sockets) para ativado.<br>Verifique se o seu Serviço de Aplicativo do Azure está em execução. Se estiver, tente reiniciar o Serviço de Aplicativo.|
|Erro (ConnectionFailure): a conexão foi encerrada pelo host remoto. Código de erro: 1011. Detalhes do erro: o código de status de resposta não indica sucesso: 500 (InternalServerError)| O bot especificou uma voz neural em seu campo [Speak](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) da atividade de saída, mas a região do Azure associada à sua chave de Assinatura de Fala não oferece suporte para vozes neurais. Confira [Vozes neural e padrão](./regions.md#neural-and-standard-voices).|

Se o problema não for abordado na tabela, confira [Assistentes de voz: perguntas frequentes](faq-voice-assistants.md). Se ainda não for possível resolver o problema depois de seguir todas as etapas neste tutorial, insira um novo problema na [página do GitHub do Assistente de Voz](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/issues).

#### <a name="a-note-on-connection-time-out"></a>Uma observação sobre atingir o tempo limite de conexão

Se você estiver conectado a um bot e nenhuma atividade acontecer nos últimos 5 minutos, o serviço encerrará automaticamente a conexão do WebSocket com o cliente e com o bot. Isso ocorre por design. Uma mensagem será exibida na barra inferior: *"A conexão ativa atingiu o tempo limite, mas está pronta para se reconectar sob demanda"* . Você não precisa pressionar o botão "Reconectar" – basta pressionar o botão do microfone e começar a falar, digitar uma mensagem de texto ou pronunciar uma palavra-chave (caso haja alguma habilitada). A conexão será restabelecida automaticamente.  
### <a name="view-bot-activities"></a>Exibir atividades de bot

Todo bot envia e recebe mensagens de **Atividade**. Na janela **Log de atividades** do Cliente do Assistente de Voz do Windows, você verá os logs com carimbo de data/hora para cada atividade que o cliente recebeu do bot. Você também pode ver as atividades que o cliente enviou para o bot usando o método [`DialogServiceConnector.SendActivityAsync`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync). Quando você seleciona um item de log, ele mostrará os detalhes da atividade associada como JSON.

Aqui está um exemplo de JSON de uma atividade recebida pelo cliente:

```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration-8726"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

Para saber mais sobre o que é retornado na saída do JSON, confira os [campos na Atividade](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md). Para fins deste tutorial, você pode se concentrar nos campos de [Texto](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) e de [Fala](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak).

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Exibir o código-fonte do cliente para chamadas ao SDK de Fala

O Cliente do Assistente de Voz do Windows usa o pacote NuGet [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/), que contém o SDK de Fala. Um bom lugar para começar a revisar o código de exemplo é o método InitSpeechConnector() no arquivo [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs), que cria esses dois objetos do SDK de Fala:
- [`DialogServiceConfig`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig) – Para definições de configuração (por exemplo, chave de Assinatura de Fala, região principal)
- [`DialogServiceConnector`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor) – Para gerenciar a conexão com o canal e os eventos de assinatura do cliente a fim de controlar as respostas reconhecidas de Fala e do bot.

## <a name="add-custom-keyword-activation"></a>Adicionar ativação por palavra-chave personalizada

O SDK de Fala dá suporte à ativação por palavra-chave personalizada. Semelhante à "Ei Cortana" para o Assistente da Microsoft, você pode escrever um aplicativo que escutará continuamente por uma palavra-chave de sua escolha. Tenha em mente que uma palavra-chave pode ser uma palavra única ou uma frase com várias palavras.

> [!NOTE]
> O termo *palavra-chave*, muitas vezes é usado de modo intercambiável com o termo *Wake Word* e você pode ver ambos empregados na documentação da Microsoft.

A detecção de palavras-chave é feita no aplicativo cliente. Se estiver usando uma palavra-chave, o áudio só será transmitido ao canal da Direct Line Speech se a palavra-chave configurada for detectada. O canal da Direct Line Speech inclui um componente chamado *KWV (verificação de palavra-chave)* , que realiza processamentos mais complexos na nuvem para verificar se a palavra-chave escolhida está no início do fluxo de áudio. Se a verificação da palavra-chave for realizada com sucesso, então o canal se comunicará com o bot.

Siga estas etapas para criar um modelo de palavra-chave, configurar o Cliente do Assistente de Voz do Windows para usar esse modelo e, por fim, testá-lo com o bot.

1. Siga estas instruções para [criar uma palavra-chave personalizada usando o serviço de Fala](./custom-keyword-basics.md).
2. Descompacte o arquivo de modelo que você baixou na etapa anterior. Ele deve ser nomeado conforme sua palavra-chave. Você está procurando um arquivo chamado `kws.table`.
3. No Cliente do Assistente de Voz do Windows, localize o menu **Configurações** (procure o ícone de engrenagem no canto superior direito). Localize o **caminho do arquivo de modelo** e insira o nome do caminho completo para o arquivo `kws.table` da etapa 2.
4. Marque a caixa rotulada **Habilitado**. Você deverá ver essa mensagem ao lado da caixa de seleção: "Escutará pela palavra-chave na próxima conexão". Se você tiver fornecido o arquivo errado ou um caminho inválido, será exibida uma mensagem de erro.
5. Insira sua **chave de assinatura** de Fala, região **principal da assinatura** e clique em **OK** para fechar o menu **Configurações**.
6. Clique em **Reconectar**. Você deverá ver uma mensagem que lê: "Nova conversa iniciada – Digite, pressione o botão do microfone ou pronuncie a palavra-chave". Agora, o aplicativo permanecerá escutando continuamente.
7. Fale qualquer frase que comece com a sua palavra-chave. Por exemplo: " **{Sua palavra-chave}** , que horas são?". Você não precisa fazer uma pausa após pronunciar a palavra-chave. Ao terminar, duas coisas acontecem:
   * Você verá uma transcrição daquilo que falou
   * Logo após, você ouvirá a resposta do bot
8. Faça novos experimentos com os três tipos de entrada aos quais o bot dá suporte:
   * Texto digitado na barra inferior
   * Pressionando o ícone do microfone e falando
   * Dizendo uma frase que começa com a sua palavra-chave

### <a name="view-the-source-code-that-enables-keyword"></a>Exibir o código-fonte que habilita a palavra-chave

No código-fonte do Cliente do Assistente de Voz do Windows, dê uma olhada nesses arquivos para examinar o código usado a fim de habilitar a detecção da palavra-chave:

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs) inclui uma chamada ao método [`KeywordRecognitionModel.fromFile()`](/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel#fromfile-string-) do SDK de Fala, que é usado para instanciar o modelo de um arquivo local no disco.
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) inclui uma chamada ao método [`DialogServiceConnector.StartKeywordRecognitionAsync()`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync) do SDK de Fala, que ativa a detecção de palavra-chave contínua.

## <a name="optional-change-the-language-and-bot-voice"></a>(Opcional) Alterar o idioma e a voz do bot

O bot que você criou escutará e responderá em inglês, com uma voz de conversão de texto em fala padrão em inglês dos EUA. No entanto, você não está limitado a usar o inglês ou uma voz padrão. Nesta seção, você aprenderá a alterar o idioma que o bot escutará e com o qual responderá. Você também aprenderá a selecionar uma voz diferente para esse idioma.

### <a name="change-the-language"></a>Alterar o idioma

Você pode escolher entre qualquer um dos idiomas mencionados na tabela de [conversão de fala em texto](language-support.md#speech-to-text). No exemplo abaixo, vamos alterar o idioma para alemão.

1. Abra o aplicativo Cliente do Assistente de Voz do Windows, clique no botão Configurações (ícone de engrenagem no canto superior direito) e insira `de-de` no campo idioma (esse é o valor da localidade mencionado na tabela de [conversão de fala em texto](language-support.md#speech-to-text)). Isso define o idioma falado a ser reconhecido, substituindo o padrão `en-us`. Isso também instrui o canal da Direct Line Speech a usar uma voz alemã padrão para a resposta do bot.
2. Feche a página Configurações e clique no botão Reconectar para estabelecer uma nova conexão com o seu Echo Bot.
3. Clique no botão do microfone e diga uma frase em alemão. Você verá o texto reconhecido e o Echo Bot respondendo com a voz alemã padrão.

### <a name="change-the-default-bot-voice"></a>Alterar a voz do bot padrão

A seleção da voz para conversão de texto em fala e o controle da pronúncia poderão ser feitos se o bot especificar a resposta na forma de uma [SSML (Linguagem de Marcação de Síntese de Fala)](speech-synthesis-markup.md), em vez de texto simples. O Echo Bot não usa SSML, mas podemos modificar o código com facilidade para fazer isso. No exemplo abaixo, adicionamos SSML à resposta do Echo Bot, de modo que a voz alemã Rodrigo Apollo (uma voz masculina) será usada em vez da voz feminina padrão. Consulte a lista de [vozes padrão](language-support.md#standard-voices) e [vozes neurais](language-support.md#neural-voices) com suporte para o seu idioma.

1. Vamos começar abrindo `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs`.
2. Localize estas duas linhas:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. Substitua elas por:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. Crie sua solução no Visual Studio e corrija eventuais erros de build.

O segundo argumento no método 'MessageFactory.Text' define o [campo de fala de atividade](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) na resposta do bot. Com a alteração acima, ele foi substituído de texto simples para SSML a fim de especificar uma voz alemã não padrão.

### <a name="redeploy-your-bot"></a>Reimplantar o seu bot

Agora que você fez a alteração necessária no bot, a próxima etapa é republicá-lo em seu Serviço de Aplicativo do Azure e experimentá-lo:

1. Na janela do Gerenciador de Soluções, clique com o botão direito do mouse no projeto **EchoBot** e selecione **Publicar**.
2. Sua configuração de implantação anterior já foi carregada como padrão. Basta clicar em **Publicar**, ao lado de **EchoBot20190805125647 – Implantação da Web**.
3. A mensagem **Publicado com sucesso** será exibida na janela de saída do Visual Studio e uma página da Web será iniciada com a mensagem "Seu bot está pronto!".
4. Abra o aplicativo Cliente do Assistente de Voz do Windows, clique no botão Configurações (ícone de engrenagem no canto superior direito) e verifique se você ainda tem `de-de` no campo idioma.
5. Siga as instruções em [Executar o Cliente do Assistente de Voz do Windows](#run-the-windows-voice-assistant-client) para se reconectar com o bot implantado recentemente, falar no novo idioma e ouvir o seu bot responder nesse idioma e com a nova voz.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não for continuar a usar o Echo Bot implantado neste tutorial, poderá removê-lo, juntamente com todos os recursos do Azure associados, simplesmente excluindo o Grupo de Recursos do Azure **SpeechEchoBotTutorial-ResourceGroup**.

1. No [portal do Azure](https://portal.azure.com), clique em **Grupos de Recursos** na navegação dos **serviços do Azure**.
2. Localize o grupo de recursos denominado: **SpeechEchoBotTutorial-ResourceGroup**. Clique nos três pontos (...).
3. Selecione **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Crie seu próprio aplicativo cliente com o SDK de Fala](./quickstarts/voice-assistants.md?pivots=programming-language-csharp)

## <a name="see-also"></a>Confira também

* Implantando em uma [região do Azure próxima de você](https://azure.microsoft.com/global-infrastructure/locations/) para ver o aprimoramento do tempo de resposta do bot
* Implantando em uma [região do Azure que dá suporte a vozes neurais de TTS de alta qualidade](./regions.md#neural-and-standard-voices)
* Preço associado ao canal da Direct Line Speech:
  * [Preços do Serviço de Bot](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Serviço de Fala](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Criando e implantando seu próprio bot habilitado por voz:
  * Crie um [bot do Bot Framework](https://dev.botframework.com/). Registre-o com o [canal da Direct Line Speech](/azure/bot-service/bot-service-channel-connect-directlinespeech) e [personalize a voz do seu bot](/azure/bot-service/directline-speech-bot)
  * Explore as [soluções existentes do Bot Framework](https://microsoft.github.io/botframework-solutions/index): crie um [assistente virtual](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) e [estenda-o para a Direct Line Speech](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
