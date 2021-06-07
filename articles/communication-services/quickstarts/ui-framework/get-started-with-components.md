---
title: Introdução aos componentes base da estrutura da IU dos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services quickstart
description: Neste guia de início rápido, você aprenderá a começar a usar os componentes base da estrutura da IU
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 336059007cfca40a74ad5a4395c6f9a59215bb58
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105825774"
---
# <a name="quickstart-get-started-with-ui-framework-base-components"></a>Início rápido: Introdução aos componentes base da estrutura da IU

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Comece a usar os Serviços de Comunicação do Azure usando a estrutura da IU para integrar rapidamente as experiências de comunicação aos seus aplicativos. Neste guia de início rápido, você aprenderá a integrar componentes base da estrutura da IU ao seu aplicativo para criar experiências de comunicação. 

Os componentes da estrutura da IU são fornecidos em dois tipos: Base e composição.

- Os **componentes base** representam recursos de comunicação discretos; eles são os blocos de construção básicos que podem ser usados para criar experiências de comunicação complexas. 
- Os **componentes de composição** são experiências prontas para uso para cenários comuns de comunicação que foram criados por meio de **componentes base** como blocos de construção e empacotados para serem integrados com facilidade aos aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/), versões de LTS Ativo e de LTS de Manutenção (Node 12 recomendado).
- Um recurso ativo dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](./../create-communication-resource.md).
- Um Token de Acesso do Usuário para criar uma instância do cliente de chamada. Saiba como [criar e gerenciar os tokens de acesso do usuário](./../access-tokens.md).

## <a name="setting-up"></a>Configurando

A estrutura da IU exige a configuração de um ambiente do React. Em seguida, faremos isso. Caso você já tenha um aplicativo React, ignore esta seção.

### <a name="set-up-react-app"></a>Configurar o aplicativo React

Usaremos o modelo create-react-app neste guia de início rápido. Para obter mais informações, consulte: [Introdução ao React](https://reactjs.org/docs/create-a-new-react-app.html)

```console

npx create-react-app my-app

cd my-app

```

No final deste processo, você deve ter um aplicativo completo dentro da pasta `my-app`. Neste guia de início rápido, modificaremos os arquivos da pasta `src`.

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
| CallingProvider| O provedor de chamadas que cria uma instância de uma chamada. Necessário para adicionar componentes extras|
| ChatProvider | O provedor de chat para criar uma instância de um thread de chat. Necessário para adicionar componentes extras|
| MediaGallery | Componente base que mostra os participantes da chamada e os respectivos fluxos de vídeo remotos |
| MediaControls | Componente base para controle da chamada, incluindo mudo, vídeo e compartilhamento de tela |
| ChatThread | Componente base que renderiza um thread de chat com indicadores de digitação, confirmações de leitura etc. |
| SendBox | Componente base que permite ao usuário inserir mensagens que serão enviadas para o thread ingressado|

## <a name="initialize-calling-and-chat-providers-using-azure-communication-services-credentials"></a>Inicializar Provedores de Chamada e de Chat usando as credenciais dos Serviços de Comunicação do Azure

Acesse a pasta `src` dentro de `my-app` e procure o arquivo `app.js`. Aqui, removeremos o código a seguir para inicializar os provedores de Chamada e de Chat. Esses provedores são responsáveis por manter o contexto das experiências de chamada e de chat. Você pode escolher qual deles usar dependendo do tipo de experiência de comunicação que está criando. Se necessário, use os dois ao mesmo tempo. Para inicializar os componentes, você precisará de um token de acesso recuperado dos Serviços de Comunicação do Azure. Para obter detalhes sobre como obter tokens de acesso, confira [Criar e gerenciar tokens de acesso](./../access-tokens.md).

> [!NOTE]
> Os componentes não geram tokens de acesso, IDs de grupo nem IDs de thread. Esses elementos são provenientes de serviços que passam pelas etapas apropriadas para gerar essas IDs e transmiti-las para o aplicativo cliente. Para obter mais informações, consulte: [Arquitetura do servidor cliente](./../../concepts/client-and-server-architecture.md).
> 
> Por exemplo: O Provedor de Chat espera que a `userId` associada ao `token` que está sendo usada para inicializá-lo já tenha ingressado na `threadId` fornecida. Se o token não tiver ingressado na ID do thread, o Provedor de Chat falhará. Para obter mais informações sobre o chat, confira: [Introdução ao chat](./../chat/get-started.md)

Usaremos um tema da interface do usuário do Fluent para aprimorar a aparência do aplicativo:

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import { mergeThemes, teamsTheme } from '@fluentui/react-northstar';
import { Provider } from '@fluentui/react-northstar/dist/commonjs/components/Provider/Provider';
import { svgIconStyles } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconStyles';
import { svgIconVariables } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconVariables';
import * as siteVariables from '@fluentui/react-northstar/dist/es/themes/teams/siteVariables';

const iconTheme = {
  componentStyles: {
    SvgIcon: svgIconStyles
  },
  componentVariables: {
    SvgIcon: svgIconVariables
  },
  siteVariables
};

function App(props) {

  return (
    <Provider theme={mergeThemes(iconTheme, teamsTheme)}>
        <CallingProvider
        displayName={/*Insert Display Name to be used for the user*/}
        groupId={/*Insert GUID for group call to be joined*/}
        token={/*Insert the Azure Communication Services access token*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            // Add Calling Components Here
        </CallingProvider>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}

        <ChatProvider
        token={/*Insert the Azure Communication Services access token*/}
        displayName={/*Insert Display Name to be used for the user*/}
        threadId={/*Insert id for group chat thread to be joined*/}
        endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            //  Add Chat Components Here
        </ChatProvider>
    </Provider>
  );
}

export default App;

```

Depois de inicializado, esse provedor permite que você crie seu layout usando componentes base da estrutura da IU e qualquer lógica de layout extra. O provedor cuida da inicialização de toda a lógica subjacente e da conexão adequada dos diferentes componentes. Em seguida, usaremos vários componentes base fornecidos pela estrutura da IU para criar experiências de comunicação. Você pode personalizar o layout desses componentes e adicionar outros componentes personalizados que deseja renderizar com eles. 

## <a name="build-ui-framework-calling-component-experiences"></a>Criar experiências de componentes de chamada da estrutura da IU

Para a chamada, usaremos os componentes `MediaGallery` e `MediaControls`. Para obter mais informações sobre eles, confira [Funcionalidades da estrutura da IU](./../../concepts/ui-framework/ui-sdk-features.md). Para começar, na pasta `src`, crie um arquivo chamado `CallingComponents.js`. Aqui, inicializaremos um componente de função que manterá nossos componentes base para, depois, importá-los no `app.js`. Adicione layout e estilo extras em todos os componentes. 

`CallingComponents.js`
```javascript

import {MediaGallery, MediaControls, MapToCallConfigurationProps, connectFuncsToContext} from "@azure/acs-ui-sdk"

function CallingComponents(props) {

  if (props.isCallInitialized) {props.joinCall()}

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <MediaGallery/>
        <MediaControls/>
    </div>
  );
}

export default connectFuncsToContext(CallingComponents, MapToCallConfigurationProps);

```

Na parte inferior desse arquivo, exportamos os componentes de chamada usando o método `connectFuncsToContext` da estrutura da IU para conectar os componentes da IU de chamada ao estado subjacente usando a função de mapeamento `MapToCallingSetupProps`. Esse método gera o componente que tem as propriedades preenchidas, que usaremos para verificar o estado e ingressar na chamada. Usando a propriedade `isCallInitialized` para verificar se o `CallAgent` está pronto, usaremos o método `joinCall` para ingressar. A estrutura da IU dá suporte a funções de mapeamento personalizadas a serem usadas em cenários nos quais os desenvolvedores desejam controlar como os dados são enviados aos componentes.

## <a name="build-ui-framework-chat-component-experiences"></a>Criar experiências de componente de chat da estrutura da IU

Para o chat, usaremos os componentes `ChatThread` e `SendBox`. Para obter mais informações sobre esses componentes, confira [Funcionalidades da estrutura da IU](./../../concepts/ui-framework/ui-sdk-features.md). Para começar, na pasta `src`, crie um arquivo chamado `ChatComponents.js`. Aqui, inicializaremos um componente de função que manterá nossos componentes base para, depois, importá-los no `app.js`.

`ChatComponents.js`
```javascript

import {ChatThread, SendBox} from '@azure/acs-ui-sdk'

function ChatComponents() {

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <ChatThread />
        <SendBox />
    </div >
  );
}

export default ChatComponents;

```

## <a name="add-calling-and-chat-components-to-the-main-application"></a>Adicionar componentes de chamada e de chat ao aplicativo principal

De volta ao arquivo `app.js`, agora adicionaremos os componentes ao `CallingProvider` e `ChatProvider`, conforme mostrado abaixo.

`App.js`
```javascript

import ChatComponents from './ChatComponents';
import CallingComponents from './CallingComponents';

<Provider ... >
    <CallingProvider .... >
        <CallingComponents/>
    </CallingProvider>

    <ChatProvider .... >
        <ChatComponents />
    </ChatProvider>
</Provider>

```

## <a name="run-quickstart"></a>Executar o guia de início rápido

Para executar o código acima, use o comando:

```console

npm run start   

```

Para testar totalmente as funcionalidades, você precisará de um segundo cliente com a funcionalidade de chat e de chamada para ingressar na chamada e no thread do chat. Confira o [Exemplo de chamada em destaque](./../../samples/calling-hero-sample.md) e o [Exemplo de chat em destaque](./../../samples/chat-hero-sample.md) como possíveis opções.

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços de Comunicação, exclua o recurso ou o grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele. Saiba mais sobre [como limpar recursos](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Experimentar os componentes de composição da estrutura da IU](./get-started-with-composites.md)

Para obter mais informações, consulte os seguintes recursos:
- [Visão geral da estrutura da IU](../../concepts/ui-framework/ui-sdk-overview.md)
- [Funcionalidades da estrutura da IU](./../../concepts/ui-framework/ui-sdk-features.md)
