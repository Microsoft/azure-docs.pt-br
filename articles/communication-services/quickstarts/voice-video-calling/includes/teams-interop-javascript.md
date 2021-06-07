---
title: Início Rápido – Participar de uma reunião no Teams usando um aplicativo Web
description: Neste tutorial, você aprende a participar de uma reunião no Teams usando o SDK de Chamada dos Serviços de Comunicação do Azure para JavaScript
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 6747d1d3cfba1c9e2bee7a8a7a48d67d6bed9f8e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564524"
---
Neste guia de início rápido, você aprenderá a participar de uma reunião no Teams usando o SDK de Chamada dos Serviços de Comunicação do Azure para JavaScript.

## <a name="prerequisites"></a>Pré-requisitos

- Um [aplicativo Web de chamadas dos Serviços de Comunicação](../getting-started-with-calling.md) funcional.
- Uma [implantação do Teams](/deployoffice/teams-install).


## <a name="add-the-teams-ui-controls"></a>Adicionar os controles de interface do usuário do Teams

Substitua o código em index.html pelo snippet a seguir.
A caixa de texto será usada para inserir o contexto da reunião do Teams, e o botão será usado para o usuário participar da reunião especificada:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - Calling Sample</title>
</head>
<body>
    <h4>Azure Communication Services</h4>
    <h1>Teams meeting join quickstart</h1>
    <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
        <p><span style="font-weight: bold" id="recording-state"></span></p>
    <div>
        <button id="join-meeting-button" type="button" disabled="false">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
    </div>
    <script src="./bundle.js"></script>
</body>

</html>
```

## <a name="enable-the-teams-ui-controls"></a>Habilitar os controles de interface do usuário do Teams

Substitua o conteúdo do arquivo client.js pelo snippet a seguir.

```javascript
import { CallClient } from "@azure/communication-calling";
import { Features } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');
const recordingStateElement = document.getElementById('recording-state');

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'ACS user'});
    teamsMeetingJoinButton.disabled = false;
}
init();

hangUpButton.addEventListener("click", async () => {
    // end the current call
    await call.hangUp();
  
    // toggle button states
    hangUpButton.disabled = true;
    teamsMeetingJoinButton.disabled = false;
    callStateElement.innerText = '-';
  });

teamsMeetingJoinButton.addEventListener("click", () => {    
    // join with meeting link
    call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
    call.on('stateChanged', () => {
        callStateElement.innerText = call.state;
    })

    call.api(Features.Recording).on('isRecordingActiveChanged', () => {
        if (call.api(Features.Recording).isRecordingActive) {
            recordingStateElement.innerText = "This call is being recorded";
        }
        else {
            recordingStateElement.innerText = "";
        }
    });
    // toggle button states
    hangUpButton.disabled = false;
    teamsMeetingJoinButton.disabled = true;
});
```

## <a name="get-the-teams-meeting-link"></a>Obter o link de reunião do Teams

O link de reunião do Teams pode ser recuperado usando APIs do Graph. Isso é detalhado na [documentação do Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
O SDK de Chamada de Serviços de Comunicação aceita um link de reunião completo do Teams. Esse link é retornado como parte do recurso `onlineMeeting`, acessível na propriedade [`joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) Você também pode obter as informações da reunião necessárias da URL **Ingressar na Reunião** no próprio convite da reunião do Teams.

## <a name="run-the-code"></a>Executar o código

Os usuários do webpack podem usar o `webpack-dev-server` para compilar e executar seu aplicativo. Execute o seguinte comando para empacotar o host de aplicativos em um servidor Web local:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Abra o navegador e navegue até http://localhost:8080/. Você deve ver o seguinte:

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="Captura de tela do Aplicativo JavaScript concluído.":::

Insira o contexto do Teams na caixa de texto e clique em *Participar da Reunião do Teams* para participar da reunião do Teams no seu aplicativo dos Serviços de Comunicação.
