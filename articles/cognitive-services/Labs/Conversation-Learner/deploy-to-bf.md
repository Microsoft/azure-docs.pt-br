---
title: Como implantar um bot Aprendiz de conversa – Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como implantar um bot Aprendiz de conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ae984cc2e0f43b81b8aa2f08b3944886733c9054
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848258"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Como implantar um bot Aprendiz de conversa

Este documento explica como implantar um bot Aprendiz de conversa localmente ou no Azure.

## <a name="prerequisite-determine-the-model-id"></a>Pré-requisito: determinar a ID do modelo 

Para executar um bot fora da interface do usuário de Aprendiz de Conversa, será necessário definir a ID do modelo de Aprendiz de Conversa que o bot utilizará -- ou seja, a ID do modelo de aprendizado de máquina na nuvem do Aprendiz de Conversa.  (Por contraste, ao executar o bot por meio da interface do usuário de Aprendiz de Conversa, a interface do usuário escolhe a ID do modelo.).  

A seguir, veja como obter a ID do modelo:

1. Inicie o bot e a interface de usuário do Aprendiz de conversa.  Consulte o guia de início rápido para obter instruções completas. Para resumir:

    Em uma janela de comando:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    Em outra janela de comando

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. Abra o navegador para `http://localhost:5050` 

3. Clique no modelo de Aprendiz de conversa para o qual você deseja obter a ID

4. Clique em “Configurações” na barra de navegação à esquerda.

5. O GUID da "ID do Modelo" é exibido na parte superior da página.

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>Opção 1: Implantar um bot Conversation Learner para ser executado localmente

Isso implanta um bot em seu computador local e mostra como acessá-lo usando o emulador do Framework do bot.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>Configurar o bot para acesso fora da interface do usuário do Aprendiz de conversa

Ao executar um bot localmente, adicione a ID do aplicativo ao arquivo `.env` do bot:

    ```
    CONVERSATION_LEARNER_MODEL_ID=<YOUR_MODEL_ID>
    ```

Em seguida, inicie o bot:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

Agora o bot está em execução localmente.  Você pode acessá-lo com o emulador do Framework de Bot.

### <a name="download-and-install-the-emulator"></a>Baixar e instalar o emulador

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Conectar o emulador ao seu bot

1. No canto superior esquerdo do emulador, na caixa “Inserir a URL do ponto de extremidade”, insira `http://127.0.0.1:3978/api/messages`.  Deixe os outros campos em branco e clique em “Conectar”.

2. Agora você está conversando com o bot.

## <a name="option-2-deploy-to-azure"></a>Opção 2: Implantar no Azure

Publica o bot do Aprendiz de conversa semelhante à forma como você publicaria qualquer outro bot. Em um nível alto, carregue seu código para um site hospedado, defina os valores de configuração apropriados e, em seguida, registre o bot com vários canais. Há instruções detalhadas neste vídeo, mostrando como publicar seu bot usando o Serviço de Bot do Azure.

Depois que o bot é implantado e em execução, você pode conectar diferentes canais a ele, como Facebook, Teams, Skype etc. usando um registro de canal de Bot do Azure. Para obter a documentação desse processo, consulte: https://docs.microsoft.com/bot-framework/bot-service-quickstart-registration

Abaixo estão instruções passo a passo para implantar um bot de Aprendiz de conversa do Azure.  Essas instruções pressupõem que a fonte de bot está disponível a partir de uma fonte baseada em nuvem, como Azure DevOps Services, GitHub, BitBucket ou OneDrive, e configurará o bot para implantação contínua.

1. Faça logon no portal do Azure em https://portal.azure.com

2. Criar um novo recurso “Bot do aplicativo Web” 

    1. Nomeie o bot
    2. Clique em “Modelo do Bot”, escolha “Node.js”, depois “Básico” e clique no botão “Selecionar”
    3. Clique em “Criar” para criar o bot do aplicativo Web.
    4. Aguarde até que o recurso do bot do aplicativo Web seja criado.

3. No portal do Azure, edite o recurso do bot do aplicativo Web que você acabou de criar.

   1. Clique no item de navegação “Configurações do Aplicativo” à esquerda
   1. Role até a seção “Configurações de Aplicativo”
   2. Adicione essas configurações:

       Variável de ambiente | value
       --- | --- 
       CONVERSATION_LEARNER_SERVICE_URI | "https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/"
       CONVERSATION_LEARNER_MODEL_ID      | GUID da ID do aplicativo, obtida da interface do usuário do Aprendiz de Conversa em “configurações” do modelo>
       LUIS_AUTHORING_KEY               | Chave de criação de LUIS para esse modelo
       LUIS_SUBSCRIPTION_KEY            | Não é necessário, mas é recomendado, que bots publicados evitem o uso de sua cota de Criação.
    
   4. Clique em “Salvar” na parte superior da página
   5. Abra o item de navegação “Build” à esquerda
   6. Clique em “Configurar implantação contínua” 
   7. Clique no ícone de “Instalação” em implantações
   8. Clique em “Configurações Necessárias”
   9. Selecione a fonte onde seu código bot está disponível e configure a origem.
