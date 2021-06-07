---
title: Criar seu próprio componente de Estrutura da IU
titleSuffix: An Azure Communication Services quickstart
description: Neste guia de início rápido, você aprenderá a criar um componente personalizado compatível com a estrutura da IU
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 798b898f6e2367e3096bcf985715fd48de3d394f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105930210"
---
# <a name="quickstart-create-your-own-ui-framework-component"></a>Início rápido: Criar o seu componente da estrutura da IU

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Comece a usar os Serviços de Comunicação do Azure usando a estrutura da IU para integrar rapidamente as experiências de comunicação aos seus aplicativos.

Neste guia de início rápido, você aprenderá a criar os próprios componentes usando a interface de estado predefinida oferecida pela estrutura da IU. Essa abordagem é ideal para os desenvolvedores que precisam de mais personalização e desejam usar os próprios ativos de design na experiência. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/), versões de LTS Ativo e de LTS de Manutenção (Node 12 recomendado).
- Um recurso ativo dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](./../create-communication-resource.md).
- Um Token de Acesso do Usuário para criar uma instância do cliente de chamada. Saiba como [criar e gerenciar os tokens de acesso do usuário](./../access-tokens.md).

A estrutura da IU requer que um ambiente React seja configurado. Em seguida, faremos isso. Caso você já tenha um aplicativo React, ignore esta seção.

### <a name="set-up-react-app"></a>Configurar o aplicativo React

Usaremos o modelo create-react-app neste guia de início rápido. Para obter mais informações, consulte: [Introdução ao React](https://reactjs.org/docs/create-a-new-react-app.html)

```console

npx create-react-app my-app

cd my-app

```

No final desse processo, você deve ter um aplicativo completo dentro da pasta `my-app`. Neste guia de início rápido, modificaremos os arquivos da pasta `src`.

### <a name="install-the-package"></a>Instalar o pacote

Use o comando `npm install` para instalar o SDK de Chamada dos Serviços de Comunicação do Azure para JavaScript. Mova o tarball fornecido (versão prévia privada) para o diretório my-app.

```console

//For Private Preview install tarball

npm install --save ./{path for tarball}

```

A opção `--save` lista a biblioteca como uma dependência no arquivo **package.json**.

### <a name="run-create-react-app"></a>Executar Create React App

Vamos testar a instalação do Create React App executando:

```console

npm run start   

```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces cuidam de alguns dos principais recursos do SDK da Interface do Usuário dos Serviços de Comunicação do Azure:

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| Provedor| Provedor da interface do usuário do Fluent que permite que os desenvolvedores modifiquem os componentes subjacentes da interface do usuário do Fluent|
| CallingProvider| O provedor de chamadas que cria uma instância de uma chamada. Necessário para adicionar componentes base|
| ChatProvider | O provedor de chat para criar uma instância de um thread de chat. Necessário para adicionar componentes base|
| connectFuncsToContext | Método para conectar componentes da estrutura da IU com provedores subjacentes usando mapeadores |
| MapToChatMessageProps | O mapeador de camada de dados da mensagem de chat que fornece componentes com objetos de mensagens de chat |


## <a name="initialize-chat-providers-using-azure-communication-services-credentials"></a>Inicializar Provedores de Chat usando as credenciais dos Serviços de Comunicação do Azure

Neste guia de início rápido, usaremos o chat como um exemplo, para obter mais informações sobre chamadas, confira [Guia de início rápido dos componentes base](./get-started-with-components.md) e [Guia de início rápido dos componentes de composição](./get-started-with-composites.md).

Acesse a pasta `src` dentro de `my-app` e procure o arquivo `app.js`. Aqui, soltaremos o código a seguir para inicializar o Provedor de Chamada. Esse provedor é responsável por manter o contexto das experiências de chamada e de chat. Para inicializar os componentes, você precisará de um token de acesso recuperado dos Serviços de Comunicação do Azure. Para obter detalhes sobre como obter um token de acesso, confira [Criar e gerenciar tokens de acesso](./../access-tokens.md).

Os componentes da estrutura da IU seguem a mesma arquitetura geral no restante do serviço. Os componentes não geram tokens de acesso, IDs de grupo nem IDs de thread. Esses elementos são provenientes de serviços que passam pelas etapas apropriadas para gerar essas IDs e transmiti-las para o aplicativo cliente. Para saber mais, confira [Arquitetura do servidor cliente](./../../concepts/client-and-server-architecture.md).

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"

function App(props) {

  return (
    <ChatProvider
    token={/*Insert the Azure Communication Services access token*/}
    userId={/*Insert the Azure Communication Services user id*/}
    displayName={/*Insert Display Name to be used for the user*/}
    threadId={/*Insert id for group chat thread to be joined*/}
    endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
    refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
    >
        //  Add Chat Components Here
    </ChatProvider>
  );
}

export default App;

```

Depois de inicializado, esse provedor permite que você crie o próprio layout usando o componente da estrutura da IU e qualquer lógica de layout adicional. O provedor cuida da inicialização de toda a lógica subjacente e da conexão adequada dos diferentes componentes. Em seguida, criaremos um componente personalizado usando mapeadores da estrutura da IU para se conectar ao nosso provedor de chat.


## <a name="create-a-custom-component-using-mappers"></a>Criar um componente personalizado usando mapeadores

Vamos começar criando um arquivo chamado `SimpleChatThread.js`, no qual criaremos o componente. Vamos começar importando os componentes da estrutura da IU que precisaremos. Aqui, usaremos o HTML pronto para uso e o React para criar um componente totalmente personalizado para um thread de chat simples. Usando o método `connectFuncsToContext`, usaremos o mapeador `MapToChatMessageProps` para mapear os objetos para os componentes personalizados `SimpleChatThread`. Esses objetos fornecerão acesso às mensagens de chat que estão sendo enviadas e recebidas para que elas sejam preenchidas no nosso thread simples.

`SimpleChatThread.js`
```javascript

import {connectFuncsToContext, MapToChatMessageProps} from "@azure/acs-ui-sdk"

function SimpleChatThread(props) {

    return (
        <div>
            {props.chatMessages?.map((message) => (
                <div key={message.id ?? message.clientMessageId}> {`${message.senderDisplayName}: ${message.content}`}</div>
            ))}
        </div>
    );
}

export default connectFuncsToContext(SimpleChatThread, MapToChatMessageProps);

```

## <a name="add-your-custom-component-to-your-application"></a>Adicionar o seu componente personalizado ao seu aplicativo

Agora que temos nosso componente personalizado pronto, vamos importá-lo e adicioná-lo ao nosso layout.

```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import SimpleChatThread from "./SimpleChatThread"

function App(props) {

  return (
        <ChatProvider ... >
            <SimpleChatThread />
        </ChatProvider>
  );
}

export default App;

```

## <a name="run-quickstart"></a>Executar o guia de início rápido

Para executar o código acima, use o comando:

```console

npm run start   

```

Para testar totalmente as funcionalidades, você precisará de um segundo cliente com a funcionalidade de chat para enviar as mensagens que serão recebidas pelo nosso Thread de Chat Simples. Confira o [Exemplo de chamada em destaque](./../../samples/calling-hero-sample.md) e o [Exemplo de chat em destaque](./../../samples/chat-hero-sample.md) como possíveis opções.

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços de Comunicação, exclua o recurso ou o grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele. Saiba mais sobre [como limpar recursos](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Experimentar os componentes de composição da estrutura da IU](./get-started-with-composites.md)

Para obter mais informações, consulte os seguintes recursos:
- [Visão geral da estrutura da IU](../../concepts/ui-framework/ui-sdk-overview.md)
- [Funcionalidades da estrutura da IU](./../../concepts/ui-framework/ui-sdk-features.md)
- [Guia de início rápido dos componentes base da estrutura da IU](./get-started-with-components.md)
