---
title: Introdução aos componentes de composição do SDK da estrutura da IU dos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services quickstart
description: Neste guia de início rápido, você aprenderá a começar a usar os componentes de composição da estrutura da IU
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 7d6b0d01ec3de1cb4ce91b1c81cd8a0ccd73b1d1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105825791"
---
# <a name="quickstart-get-started-with-ui-framework-composite-components"></a>Início rápido: Introdução aos componentes de composição da estrutura da IU

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Comece a usar os Serviços de Comunicação do Azure usando a estrutura da IU para integrar rapidamente as experiências de comunicação aos seus aplicativos. Neste guia de início rápido, você aprenderá a integrar os componentes de composição da estrutura da IU ao seu aplicativo para criar experiências de comunicação.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/), versões de LTS Ativo e de LTS de Manutenção (Node 12 recomendado).
- Um recurso ativo dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](./../create-communication-resource.md).
- Um token de acesso do usuário para criar uma instância da composição de chamada. Saiba como [criar e gerenciar os tokens de acesso do usuário](./../access-tokens.md).

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

//Private Preview install tarball

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
| GroupCall | Componente de composição que renderiza uma experiência de chamada de grupo com controles e galeria de participantes. |
| GroupChat | Componente de composição que renderiza uma experiência de chat em grupo com o thread e a entrada do chat |


## <a name="initialize-group-call-and-group-chat-composite-components"></a>Inicializar componentes de composição de chat em grupo e chamada em grupo

Acesse a pasta `src` dentro de `my-app` e procure o arquivo `app.js`. Aqui, soltaremos o código a seguir para inicializar nossos componentes de composição da Chamada e do Chat em Grupo. Você pode escolher qual deles usar dependendo do tipo de experiência de comunicação que está criando. Se necessário, use os dois ao mesmo tempo. Para inicializar os componentes, você precisará de um token de acesso recuperado dos Serviços de Comunicação do Azure. Para obter detalhes sobre como obter tokens de acesso, confira: [Criar e gerenciar tokens de acesso de usuário](./../access-tokens.md).

> [!NOTE]
> Os componentes não geram tokens de acesso, IDs de grupo nem IDs de thread. Esses elementos são provenientes de serviços que passam pelas etapas apropriadas para gerar essas IDs e transmiti-las para o aplicativo cliente. Para obter mais informações, consulte: [Arquitetura do servidor cliente](./../../concepts/client-and-server-architecture.md).
> 
> Por exemplo: a composição do Chat em Grupo espera que o `userId` associado ao `token` que está sendo usado para inicializá-lo já tenha sido ingressado ao `threadId` que está sendo fornecido. Se o token não tiver ingressado na ID do thread, a composição do Chat em Grupo falhará. Para obter mais informações sobre o chat, confira: [Introdução ao chat](./../chat/get-started.md)


`App.js`
```javascript

import {GroupCall, GroupChat} from "@azure/acs-ui-sdk"

function App(){

    return(<>
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "50rem", float: "left"}}>
            <GroupCall
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                refreshTokenCallback={CALLBACK} //Optional, Callback to refresh the token in case it expires
                groupId={GROUPID} //Required, Id for group call that will be joined. (GUID)
                onEndCall = { () => {
                    //Optional, Action to be performed when the call ends
                }}
            />
        </div>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "30rem", float: "left"}}>
            <GroupChat 
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                threadId={THREADID} //Required, Id for group chat thread that will be joined.
                endpointUrl={ENDPOINT_URL} //Required, URL for Azure endpoint being used for Azure Communication Services
                onRenderAvatar = { (acsId) => {
                    //Optional, function to override the avatar image on the chat thread. Function receives one parameters for the Azure Communication Services Identity. Must return a React element.
                }}
                refreshToken = { () => {
                    //Optional, function to refresh the access token in case it expires
                }}
                options = {{
                    //Optional, options to define chat behavior
                    sendBoxMaxLength: number | undefined //Optional, Limit the max send box length based on viewport size change.
                }}
            />
        </div>
    </>);
}

export default App;

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
> [Experimentar componentes base da estrutura da IU](./get-started-with-components.md)

Para obter mais informações, consulte os seguintes recursos:
- [Visão geral da estrutura da IU](../../concepts/ui-framework/ui-sdk-overview.md)
- [Funcionalidades da estrutura da IU](./../../concepts/ui-framework/ui-sdk-features.md)
