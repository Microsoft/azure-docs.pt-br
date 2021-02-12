---
title: Como usar comandos de dispositivo em uma solução de IoT Central do Azure
description: Como usar comandos de dispositivo na solução de IoT Central do Azure. Este tutorial mostra como, como desenvolvedor do dispositivo, usar comandos de dispositivo no aplicativo cliente para o aplicativo de IoT Central do Azure.
author: dominicbetts
ms.author: dobett
ms.date: 01/07/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: e53bf377a7ef8f2293debd288ba25ef8f04ff4fc
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610991"
---
# <a name="how-to-use-commands-in-an-azure-iot-central-solution"></a>Como usar comandos em uma solução de IoT Central do Azure

Este guia de instruções mostra como, como um desenvolvedor de dispositivos, usar comandos que são definidos em um modelo de dispositivo.

Um operador pode usar a interface do usuário do IoT Central para chamar um comando em um dispositivo. Os comandos controlam o comportamento de um dispositivo. Por exemplo, um operador pode chamar um comando para reinicializar um dispositivo ou coletar dados de diagnóstico.

Um dispositivo pode:

* Responder a um comando imediatamente.
* Responda a IoT Central quando receber o comando e depois notificar IoT Central quando o *comando de longa execução* for concluído.

Por padrão, os comandos esperam que um dispositivo seja conectado e falhem se o dispositivo não puder ser acessado. Se você selecionar a opção **fila se estiver offline** na interface do usuário do modelo de dispositivo, um comando poderá ser colocado na fila até que um dispositivo fique online. Esses *comandos offline* são descritos em uma seção separada mais adiante neste artigo.

## <a name="define-your-commands"></a>Definir seus comandos

Os comandos padrão são enviados a um dispositivo para instruir o dispositivo a fazer algo. Um comando pode incluir parâmetros com informações adicionais. Por exemplo, um comando para abrir uma válvula em um dispositivo pode ter um parâmetro que especifica o quanto abrir a válvula. Os comandos também podem receber um valor de retorno quando o dispositivo conclui o comando. Por exemplo, um comando que solicita que um dispositivo execute alguns diagnósticos poderia receber um relatório de diagnóstico como um valor de retorno.

Os comandos são definidos como parte de um modelo de dispositivo. A captura de tela a seguir mostra a definição de comando **obter Max-Min de relatório** no modelo de dispositivo **termostato** . Este comando tem os parâmetros de solicitação e de resposta: 

:::image type="content" source="media/howto-use-commands/command-definition.png" alt-text="Captura de tela mostrando o comando Get Max min Report no modelo de dispositivo termostato":::

A seguinte tabela mostra as definições de configuração para uma funcionalidade de comando:

| Campo             |Descrição|
|-------------------|-----------|
|Nome de exibição       |O valor de comando usado em painéis e formulários.|
| Nome            | O nome do comando. O IoT Central gera um valor para esse campo com base no nome de exibição, mas você pode escolher seu próprio valor, se necessário. Este campo precisa ser alfanumérico. O código do dispositivo usa esse valor de **nome** .|
| Tipo de Funcionalidade | Comando.|
| Enfileirar se offline | Se deseja tornar este comando um comando *offline* . |
| Descrição     | Uma descrição da funcionalidade de comando.|
| Comentário     | Comentários sobre a funcionalidade de comando.|
| Solicitação     | A carga para o comando do dispositivo.|
| Resposta     | A carga da resposta do comando do dispositivo.|

O trecho a seguir mostra a representação JSON do comando no modelo do dispositivo. Neste exemplo, o valor da resposta é um tipo de **objeto** complexo com vários campos:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

> [!TIP]
> Você pode exportar um modelo de dispositivo da página de modelo do dispositivo.

Você pode relacionar essa definição de comando à captura de tela da interface do usuário usando os seguintes campos:

* `@type` para especificar o tipo de recurso: `Command`
* `name` para o valor do comando.

Campos opcionais, como nome de exibição e descrição, permitem que você adicione mais detalhes à interface e aos recursos.

## <a name="standard-commands"></a>Comandos padrão

Esta seção mostra como um dispositivo envia um valor de resposta assim que recebe o comando.

O trecho de código a seguir mostra como um dispositivo pode responder a um comando que envia imediatamente um código de êxito:

> [!NOTE]
> Este artigo usa Node.js para simplificar a simplicidade. Para obter outros exemplos de linguagem, consulte o tutorial [criar e conectar um aplicativo cliente ao aplicativo do Azure IOT central](tutorial-connect-device.md) .

```javascript
client.onDeviceMethod('getMaxMinReport', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'getMaxMinReport': {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

A chamada para `onDeviceMethod` configura o `commandHandler` método. Este manipulador de comando:

1. Verifica o nome do comando.
1. Para o `getMaxMinReport` comando, ele chama `getMaxMinReportObject` para recuperar os valores a serem incluídos no objeto de retorno.
1. Chamadas `sendCommandResponse` para enviar a resposta de volta para IOT central. Essa resposta inclui o `200` código de resposta para indicar êxito.

A captura de tela a seguir mostra como a resposta de comando bem-sucedida é exibida na interface do usuário do IoT Central:

:::image type="content" source="media/howto-use-commands/simple-command-ui.png" alt-text="Captura de tela mostrando como exibir a carga de comando para um comando padrão":::

## <a name="long-running-commands"></a>Comandos de execução longa

Esta seção mostra como um dispositivo pode atrasar o envio de uma confirmação de que o comando foi compete.

O trecho de código a seguir mostra como um dispositivo pode implementar um comando de longa execução:

> [!NOTE]
> Este artigo usa Node.js para simplificar a simplicidade. Para obter outros exemplos de linguagem, consulte o tutorial [criar e conectar um aplicativo cliente ao aplicativo do Azure IOT central](tutorial-connect-device.md) .

```javascript
client.onDeviceMethod('rundiagnostics', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'rundiagnostics': {
    console.log('Starting long-running diagnostics run ' + request.payload);
    await sendCommandResponse(request, response, 202, 'Diagnostics run started');

    // Long-running operation here
    // ...

    const patch = {
      rundiagnostics: {
        value: 'Diagnostics run complete at ' + new Date().toLocaleString()
      }
    };

    deviceTwin.properties.reported.update(patch, function (err) {
      if (err) throw err;
      console.log('Properties have been reported for component');
    });
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};
```

A chamada para `onDeviceMethod` configura o `commandHandler` método. Este manipulador de comando:

1. Verifica o nome do comando.
1. Chamadas `sendCommandResponse` para enviar a resposta de volta para IOT central. Essa resposta inclui o `202` código de resposta para indicar resultados pendentes.
1. Conclui a operação de execução longa.
1. Usa uma propriedade relatada com o mesmo nome que o comando para informar IoT Central que o comando foi concluído.

A captura de tela a seguir mostra como a resposta do comando é exibida na interface do usuário do IoT Central quando recebe o código de resposta 202:

:::image type="content" source="media/howto-use-commands/long-running-start.png" alt-text="Captura de tela que mostra a resposta imediata do dispositivo":::

A captura de tela a seguir mostra a interface do usuário do IoT Central quando recebe a atualização de propriedade que indica que o comando foi concluído:

:::image type="content" source="media/howto-use-commands/long-running-finish.png" alt-text="Captura de tela que mostra o comando de longa execução concluído":::

## <a name="offline-commands"></a>Comandos offline

Esta seção mostra como um dispositivo lida com um comando offline. Se um dispositivo estiver online, ele poderá manipular o comando offline assim que ele for recebido. Se um dispositivo estiver offline, ele manipulará o comando offline quando ele se conectar ao IoT Central. Os dispositivos não podem enviar um valor de retorno em resposta a um comando offline.

> [!NOTE]
> Este artigo usa Node.js para simplificar a simplicidade.

A captura de tela a seguir mostra um comando offline chamado **GenerateDiagnostics**. O parâmetro de solicitação é um objeto com a propriedade DateTime chamada **StartTime** e uma propriedade de enumeração de inteiro chamada **Bank**:

:::image type="content" source="media/howto-use-commands/offline-command.png" alt-text="Captura de tela que mostra a interface do usuário para um comando offline":::

O trecho de código a seguir mostra como um cliente pode escutar comandos offline e exibir o conteúdo da mensagem:

```javascript
client.on('message', function (msg) {
  console.log('Body: ' + msg.data);
  console.log('Properties: ' + JSON.stringify(msg.properties));
  client.complete(msg, function (err) {
    if (err) {
      console.error('complete error: ' + err.toString());
    } else {
      console.log('complete sent');
    }
  });
});
```

A saída do trecho de código anterior mostra a carga com os valores **inicial** e de **banco** . A lista de propriedades inclui o nome do comando no item de lista **nome-do-método** :

```output
Body: {"StartTime":"2021-01-06T06:00:00.000Z","Bank":2}
Properties: {"propertyList":[{"key":"iothub-ack","value":"none"},{"key":"method-name","value":"GenerateDiagnostics"}]}
```

> [!NOTE]
> O tempo de vida padrão para comandos offline é de 24 horas, após o qual a mensagem expira.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar comandos em seu aplicativo de IoT Central do Azure, consulte [cargas](concepts-telemetry-properties-commands.md) para saber mais sobre parâmetros de comando e [criar e conectar um aplicativo cliente ao aplicativo de IOT central do Azure](tutorial-connect-device.md) para ver exemplos de código completos em idiomas diferentes.
