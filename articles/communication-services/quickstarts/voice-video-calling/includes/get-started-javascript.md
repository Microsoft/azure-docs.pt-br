---
title: Guia de início rápido – Adicionar uma chamada VOIP a um aplicativo Web usando os Serviços de Comunicação do Azure
description: Neste tutorial, você aprenderá a usar o SDK de Chamada dos Serviços de Comunicação do Azure para JavaScript
author: ddematheu
ms.author: nimag
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 7d7b62d6587a568b74d142a2ee6a93587941559d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645345"
---
Neste guia de início rápido, você aprenderá a iniciar uma chamada usando o SDK de Chamada dos Serviços de Comunicação do Azure para JavaScript.

> [!NOTE]
> Este documento usa a versão 1.0.0-beta.10 do SDK de Chamada.


## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/), versões Active LTS e Maintenance LTS (8.11.1 e 10.14.1 recomendadas).
- Um recurso ativo dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).
- Um Token de Acesso do Usuário para criar uma instância do cliente de chamada. Saiba como [criar e gerenciar os tokens de acesso do usuário](../../access-tokens.md).


[!INCLUDE [Calling with JavaScript](./get-started-javascript-setup.md)]

O código é o seguinte:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="token-input"
      type="text"
      placeholder="User access token"
      style="margin-bottom:1em; width: 200px;"
    />
    </div>
    <button id="token-submit" type="button">
        Submit
    </button>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px; display: block;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Crie um arquivo no diretório raiz do projeto chamado **client.js** para conter a lógica do aplicativo deste guia de início rápido. Adicione o código a seguir para importar o cliente de chamada e obter referências aos elementos DOM para que possamos anexar a nossa lógica de negócios. 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
let userTokenCredential = "";
const userToken = document.getElementById("token-input");
const calleeInput = document.getElementById("callee-id-input");
const submitToken = document.getElementById("token-submit");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces cuidam de alguns dos principais recursos do SDK de Chamada da Interface do Usuário dos Serviços de Comunicação do Azure:

| Nome                             | Descrição                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | O CallClient é o ponto de entrada principal para o SDK de Chamada.                                                                       |
| CallAgent                        | O CallAgent é usado para iniciar e gerenciar chamadas.                                                                                            |
| AzureCommunicationTokenCredential | A classe AzureCommunicationTokenCredential implementa a interface CommunicationTokenCredential, que é usada para criar uma instância do CallAgent. |


## <a name="authenticate-the-client"></a>Autenticar o cliente

Você precisa inserir um token de acesso de usuário válido para o recurso no campo de texto e clicar em 'Enviar'. Confira a documentação do [token de acesso do usuário](../../access-tokens.md) se você ainda não tiver um token disponível. Usando o `CallClient`, inicialize uma instância `CallAgent` com um `CommunicationTokenCredential` que nos permitirá realizar e receber chamadas. Adicione o seguinte código ao **client.js**:

```javascript
submitToken.addEventListener("click", async () => {
  const callClient = new CallClient(); 
  const userTokenCredential = userToken.value;
    try {
      tokenCredential = new AzureCommunicationTokenCredential(userTokenCredential);
      callAgent = await callClient.createCallAgent(tokenCredential);
      callButton.disabled = false;
      submitToken.disabled = true;
    } catch(error) {
      window.alert("Please submit a valid token!");
    }
})
```

## <a name="start-a-call"></a>Iniciar uma chamada

Adicione um manipulador de eventos para iniciar uma chamada quando o `callButton` for clicado:

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ id: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>Encerrar uma chamada

Adicione um ouvinte de eventos para encerrar a chamada atual quando o `hangUpButton` for clicado:

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
  submitToken.disabled = false;
});
```

A propriedade `forEveryone` encerra a chamada para todos os participantes da chamada.

## <a name="run-the-code"></a>Executar o código

Use `webpack-dev-server` para criar e executar o seu aplicativo. Execute o seguinte comando para agrupar o host de aplicativos em um servidor Web local:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Abra o navegador e navegue até http://localhost:8080/. Você deve ver o seguinte:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Captura de tela do Aplicativo JavaScript concluído.":::

Você pode fazer uma chamada VOIP de saída fornecendo uma ID de usuário no campo de texto e clicando no botão **Iniciar Chamada**. Chamar `8:echo123` conecta você a um bot de eco. Isso é ótimo para começar e verificar se os dispositivos de áudio estão funcionando.
