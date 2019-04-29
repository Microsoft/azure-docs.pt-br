---
title: Como criar um modelo de Aprendiz de Conversa usando Node.js - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como criar um modelo de Aprendiz de Conversa usando Node.js.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 743b4d4888738579a023e879dc7b6e95cd23ccc7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708355"
---
# <a name="create-a-conversation-learner-model-using-nodejs"></a>Como criar um modelo de Aprendiz de Conversa usando Node.js

O Aprendiz de Conversa reduz a complexidade de compilar bots. Ele permite um fluxo de trabalho de desenvolvimento híbrido, permitindo que o código gravado à mão e o aprendizado de máquina reduzam a quantidade de código necessária para gravar bots. Determinadas partes fixas do modelo, como verificar se o usuário está conectado ou fazer uma solicitação de API para verificar o estoque da loja, ainda podem ser codificadas. No entanto, outras alterações na seleção de estado e ação podem ser aprendidas em diálogos de exemplo fornecidos pelo desenvolvedor ou especialista do domínio.

## <a name="invitation-required"></a>Convite necessário

*É necessário um convite para acessar o Aprendiz de Conversa do Projeto.*

O Aprendiz de Conversa do Projeto consiste em um SDK que você adiciona ao bot e um serviço de nuvem que o SDK acessa para aprendizado de máquina.  Atualmente, o acesso ao serviço de nuvem do Aprendiz de Conversa do Projeto exige um convite.  Se você ainda não foi convidado, [solicite um convite](https://aka.ms/conversation-learner-request-invite).  Se você não recebeu um convite, não poderá acessar a API de nuvem.

## <a name="prerequisites"></a>Pré-requisitos

- Nó 8.5.0 ou superior e npm 5.3.0 ou superior. Instalar de [https://nodejs.org](https://nodejs.org).
  
- Chave de criação do LUIS:

  1. Faça logon em [https://www.luis.ai](https://www.luis.ai).

  2. Clique no seu nome no canto superior direito e, em seguida, em "configurações"

  3. A chave de criação é mostrada na página de resultados

  (A chave de criação do LUIS atende 2 funções.  Primeiro, servirá como chave de criação do Aprendiz de Conversa.  Segundo, o Aprendiz de Conversa usa o LUIS para extração de entidade; a chave de criação do LUIS é usada para criar modelos LUIS em seu nome)

- Navegador da Web do Google Chrome. Instalar de [https://www.google.com/chrome/index.html](https://www.google.com/chrome/index.html).

- git. Instalar de [https://git-scm.com/downloads](https://git-scm.com/downloads).

- VSCode. Instalar de [https://code.visualstudio.com/](https://code.visualstudio.com/). Observe que isso é recomendável, não obrigatório.

## <a name="quick-start"></a>Início rápido 

1. Instalar e compilar:

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > Durante `npm install`, será possível ignorar esse erro, caso ocorra: `gyp ERR! stack Error: Can't find Python executable`

2. Configurar:

   Crie um arquivo chamado `.env` no diretório `cl-bot-01`.  O conteúdo do arquivo deve ser:

   ```
   NODE_ENV=development
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Iniciar bot:

    ```
    npm start
    ```

    Isso executa o bot vazio genérico em `cl-bot-01/src/app.ts`.

3. Abra o navegador para `http://localhost:3978`

Agora, você está usando o Aprendiz de Conversa e pode criar e ensinar um modelo de Aprendiz de Conversa.  

> [!NOTE]
> Na inicialização, o Aprendiz de Conversa do Projeto está disponível por convite.  Se `http://localhost:3978/ui` mostrar um erro HTTP `403`, significa que sua conta não foi convidada.  [Solicite um convite](https://aka.ms/conversation-learner-request-invite).

## <a name="tutorials-demos-and-switching-between-bots"></a>Tutoriais, demonstrações e alternância entre bots

As instruções acima iniciaram o bot vazio genérico.  Para executar um tutorial ou um bot de demonstração:

1. Se você tiver a interface do usuário da Web do Aprendiz de Conversa aberta, retorne à lista de modelos em `http://localhost:3978/ui/home`.
    
2. Se outro bot estiver em execução (como `npm start` ou `npm run demo-pizza`), pare-o.  Não é necessário parar o processo da interface do usuário ou fechar o navegador da Web.

3. Execute um bot de demonstração na linha de comando (etapa 2 acima).  Demonstrações incluem:

   ```bash
   npm run tutorial-general
   npm run tutorial-entity-detection
   npm run tutorial-session-callbacks
   npm run tutorial-api-calls
   npm run tutorial-hybrid
   npm run demo-password
   npm run demo-pizza
   npm run demo-storage
   ```

4. Caso ainda não esteja, alterne para a interface do usuário da Web do Aprendiz de Conversa no Chrome, carregando `http://localhost:3978/ui/home`. 

5. Clique em “Importar tutoriais” e selecione o modelo de demonstração na interface do usuário do Aprendiz de Conversa que corresponde à demonstração que você iniciou.

Os arquivos de origem das demonstrações estão em `cl-bot-01/src/demos`

## <a name="create-a-bot-which-includes-back-end-code"></a>Crie um bot que inclua código back-end

1. Se você tiver a interface do usuário da Web do Aprendiz de Conversa aberta, retorne à lista de modelos em `http://localhost:3978/ui/home`.
    
2. Se um bot estiver em execução( como `npm run demo-pizza`), pare-o.  Não é necessário parar o processo da interface do usuário ou fechar o navegador da Web.

3. Se desejar, edite o código em `cl-bot-01/src/app.ts`.

4. Recompilar e reiniciar o bot:

    ```bash    
    npm run build
    npm start
    ```

5. Caso ainda não esteja, alterne para a interface do usuário da Web do Aprendiz de Conversa no Chrome, carregando `http://localhost:3978/ui/home`. 

6. Crie um novo modelo de Aprendiz de Conversa na interface do usuário e comece a ensinar.

7. Para fazer alterações de código em `cl-bot-01/src/app.ts`, repita as etapas acima, iniciando pela etapa 2.

## <a name="vscode"></a>VSCode

No VSCode, há configurações de execução para cada demonstração e para o "Bot vazio" em `cl-bot-01/src/app.ts`.  Abra a pasta `cl-bot-01` em VSCode.

## <a name="advanced-configuration"></a>Configuração avançada

Há um arquivo de modelo `.env.example` que mostra quais variáveis de ambiente você pode definir para configurar os exemplos.

É possível ajustar essas portas para evitar conflitos entre outros serviços em execução no computador, adicionando um arquivo `.env` à raiz do projeto:

```bash
cp .env.example .env
```

Isso usa a configuração padrão, que permite executar o bot localmente e começar a usar o Aprendiz de Conversa.  (Posteriormente, para implantar o bot no Bot Framework, algumas edições nesse arquivo serão necessárias.)

## <a name="support"></a>Suporte

- Marcar perguntas no [Stack Overflow](https://stackoverflow.com) com "microsoft cognitive"
- Solicite um recurso em nossa [página Voz do Usuário](https://aka.ms/conversation-learner-uservoice)
- Abra um problema em nosso [repositório do GitHub](https://github.com/Microsoft/ConversationLearner-Samples)

## <a name="contributing"></a>Participante

Este projeto adotou o [Código de Conduta Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Para obter mais informações, confira as [Perguntas frequentes sobre o código de conduta](https://opensource.microsoft.com/codeofconduct/faq/) ou entre em contato com [opencode@microsoft.com](mailto:opencode@microsoft.com) para enviar outras perguntas ou comentários.

## <a name="source-repositories"></a>Repositórios de origem

- [conversationlearner-samples](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner-sdk](https://github.com/Microsoft/ConversationLearner-SDK)
- [conversationlearner-models](https://github.com/Microsoft/ConversationLearner-Models)
- [conversationlearner-ui](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner-webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Olá, Mundo](./tutorials/01-hello-world.md)
