---
title: "Criar um módulo do Azure IoT Edge com o Node.js | Microsoft Docs"
description: "Este tutorial mostra como escrever um módulo de conversor de dados BLE usando os últimos pacotes NPM do Azure IoT Edge e o gerador Yeoman."
services: iot-hub
author: sushi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: article
ms.date: 06/28/2017
ms.author: sushi
ms.openlocfilehash: e23c4aa7bb3eb4fab18d5a13cbad28e07c18d8f2
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-iot-edge-module-with-nodejs"></a>Criar um módulo do Azure IoT Edge com o Node.js

Este tutorial mostra como criar um módulo para o Azure IoT Edge em JS.

Neste tutorial, explicamos a configuração do ambiente e como escrever um módulo de conversor de dados [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) usando os últimos pacotes NPM do Azure IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

Nesta seção, você configura o ambiente para o desenvolvimento de módulos do IoT Edge. Ele se aplica aos sistemas operacionais *Windows de 64 bits* e *Linux de 64 bits (Ubuntu 14+)*.

O seguinte software é necessário:
* [Cliente Git](https://git-scm.com/downloads).
* [Node LTS](https://nodejs.org).
* `npm install -g yo`.
* `npm install -g generator-az-iot-gw-module`

## <a name="architecture"></a>Arquitetura

A plataforma Azure IoT Edge adota intensamente a [arquitetura Von Neumann](https://en.wikipedia.org/wiki/Von_Neumann_architecture). Isso significa que a arquitetura do Azure IoT Edge é um sistema que processa uma entrada e gera uma saída e que cada módulo individual também é um pequeno subsistema de entrada-saída. Neste tutorial, apresentamos os dois seguintes módulos:

1. Um módulo que recebe um sinal [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) simulado e converte-o em uma mensagem [JSON](https://en.wikipedia.org/wiki/JSON) formatada.
2. Um módulo que imprime a mensagem [JSON](https://en.wikipedia.org/wiki/JSON) recebida.

A seguinte imagem exibe o fluxo de dados típico de ponta a ponta para este projeto:

![Fluxo de dados entre três módulos](media/iot-hub-iot-edge-create-module/dataflow.png "Entrada: Módulo BLE Simulado; Processador: Módulo de Conversor; Saída: Módulo de Impressora")

## <a name="set-up-the-environment"></a>Configurar o ambiente
Abaixo, mostramos como configurar o ambiente rapidamente para começar a escrever seu primeiro módulo de conversor BLE com o JS.

### <a name="create-module-project"></a>Criar projeto de módulo
1. Abra uma janela de linha de comando e execute `yo az-iot-gw-module`.
2. Siga as etapas na tela para concluir a inicialização do projeto de módulo.

### <a name="project-structure"></a>Estrutura do projeto
Um projeto de módulo JS consiste nos seguintes componentes:

`modules` – Os arquivos de origem do módulo JS personalizados. Substitua o `sensor.js` e `printer.js` padrão pelos seus próprios arquivos de módulo.

`app.js` – O arquivo de entrada para iniciar a instância do Edge.

`gw.config.json` – O arquivo de configuração para personalizar os módulos a serem carregados pelo Edge.

`package.json` – As informações de metadados do projeto de módulo.

`README.md` – A documentação básica do projeto de módulo.


### <a name="package-file"></a>Arquivo de pacote

Este `package.json` declara todas as informações de metadados necessárias para um projeto de módulo, que inclui as dependências de nome, versão, entrada, scripts, tempo de execução e desenvolvimento.

O trecho de código a seguir mostra como configurar o projeto de exemplo de conversor BLE.
```json
{
  "name": "converter",
  "version": "1.0.0",
  "description": "BLE data converter sample for Azure IoT Edge.",
  "repository": {
    "type": "git",
    "url": "https://github.com/Azure-Samples/iot-edge-samples"
  },
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "author": "Microsoft Corporation",
  "license": "MIT",
  "dependencies": {
  },
  "devDependencies": {
    "azure-iot-gateway": "~1.1.3"
  }
}
```


### <a name="entry-file"></a>Arquivo de entrada
O `app.js` define a forma de inicializar a instância do Edge. Aqui, não precisamos fazer nenhuma alteração.

```javascript
(function() {
  'use strict';

  const Gateway = require('azure-iot-gateway');
  let config_path = './gw.config.json';

  // node app.js
  if (process.argv.length < 2) {
    throw 'Calling pattern should be node app.js.';
  }

  const gw = new Gateway(config_path);
  gw.run();
})();
```

### <a name="interface-of-module"></a>Interface do módulo
É possível tratar um módulo do Azure IoT Edge como um processador de dados cujo trabalho é: receber uma entrada, processá-la e produzir uma saída.

A entrada pode ser dados de hardware (como um detector de movimento), uma mensagem de outros módulos e outros como, por exemplo, um número aleatório gerado periodicamente por um timer.

A saída é semelhante à entrada: ela pode disparar o comportamento de hardware (como o LED intermitente), uma mensagem para outros módulos ou qualquer outra coisa (como a impressão para o console).

Os módulos se comunicam usando o objeto `message`. O **conteúdo** de uma `message` é uma matriz de bytes que pode representar qualquer tipo de dados desejado. As **propriedades** também estão disponíveis no `message` e são simplesmente um mapeamento de cadeia de caracteres. Você pode pensar em **Propriedades** como sendo os cabeçalhos em uma solicitação HTTPS, ou os metadados de um arquivo.

Para desenvolver um módulo do Azure IoT Edge no JS, você precisa criar um novo objeto de módulo que implementa os métodos necessários `receive()`. Neste ponto, você também pode optar por implementar os métodos `create()`, `start()` ou `destroy()` opcionais também. O trecho de código a seguir mostra o scaffolding do objeto de módulo JS.

```javascript
'use strict';

module.exports = {
  broker: null,
  configuration: null,

  create: function (broker, configuration) {
    // Default implementation.
    this.broker = broker;
    this.configuration = configuration;

    return true;
  },

  start: function () {
    // Produce
  },

  receive: function (message) {
    // Consume
  },

  destroy: function () {
  }
};
```

### <a name="converter-module"></a>Módulo de conversor
| Entrada                    | Processador                              | Saída                 | Arquivo de código-fonte            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| Mensagem de dados de temperatura | Análise e criação de uma nova mensagem JSON | Mensagem de JSON de estrutura | `converter.js` |

Este é um módulo típico do Azure IoT Edge. Ele aceita mensagens de temperatura de outros módulos (um módulo de hardware, ou, nesse caso, nosso módulo BLE simulado), e, em seguida, normaliza a mensagem de temperatura em uma mensagem de JSON estruturada (incluindo o ID da mensagem, definindo a propriedade do que for necessário para disparar o alerta de temperatura e assim por diante).

```javascript
receive: function (message) {
  // Initialize the messageCount in global object at first time.
  if (!global.messageCount) {
    global.messageCount = 0;
  }

  // Read the content and properties objects from message.
  let rawContent = JSON.parse(Buffer.from(message.content).toString('utf8'));
  let rawProperties = message.properties;

  // Generate new properties object.
  let newProperties = {
    source: rawProperties.source,
    macAddress: rawProperties.macAddress,
    temperatureAlert: rawContent.temperature > 30 ? 'true' : 'false'
  };

  // Generate new content object.
  let newContent = {
    deviceId: 'Intel NUC Gateway',
    messageId: ++global.messageCount,
    temperature: rawContent.temperature
  };

  // Publish the new message to broker.
  this.broker.publish(
    {
      properties: newProperties,
      content: new Uint8Array(Buffer.from(JSON.stringify(newContent), 'utf8'))
    }
  );
},
```

### <a name="printer-module"></a>Módulo de impressão
| Entrada                          | Processador | Saída                     | Arquivo de código-fonte          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| Qualquer mensagem de outros módulos | N/D       | Registrar a mensagem no console | `printer.js` |

Esse módulo é simples e autoexplicativo, que gera as mensagens recebidas (propriedade, conteúdo) na janela do terminal.

```javascript
receive: function (message) {
  let properties = JSON.stringify(message.properties);
  let content = Buffer.from(message.content).toString('utf8');

  console.log(`printer.receive.properties - ${properties}`);
  console.log(`printer.receive.content - ${content}\n`);
}
```

### <a name="configuration"></a>Configuração
A etapa final antes da execução dos módulos é configurar ao Azure IoT Edge e estabelecer as conexões entre os módulos.

Primeiro, precisamos declarar nosso carregador `node` (já que o Azure IoT Edge dá suporte a carregadores de linguagens diferentes), que pode ser referenciado por seu `name` nas próximas seções.

```json
"loaders": [
  {
    "type": "node",
    "name": "node"
  }
]
```

Depois de declarar nossos carregadores, precisamos declarar nossos módulos também. De forma semelhante à declaração dos carregadores, eles também podem ser referenciados por seu atributo `name`. Ao declarar um módulo, precisamos especificar o carregador que ele deve usar (que deve aquele que definimos anteriormente) e o ponto de entrada (deve ser o nome de classe normalizado de nosso módulo) para cada módulo. O módulo `simulated_device` é um módulo nativo incluído no pacote de tempo de execução básico do Azure IoT Edge. Inclua `args` no arquivo JSON mesmo se ele for `null`.

```json
"modules": [
  {
    "name": "simulated_device",
    "loader": {
      "name": "native",
      "entrypoint": {
        "module.path": "simulated_device"
      }
    },
    "args": {
      "macAddress": "01:02:03:03:02:01",
      "messagePeriod": 500
    }
  },
  {
    "name": "converter",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/converter.js"
      }
    },
    "args": null
  },
  {
    "name": "printer",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/printer.js"
      }
    },
    "args": null
  }
]
```

Ao final da configuração, estabelecemos as conexões. Cada conexão é expressa por `source` e `sink`. Ambas devem referenciar um módulo predefinido. A mensagem de saída do módulo `source` é encaminhada para a entrada do módulo `sink`.

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "printer"
  }
]
```

## <a name="running-the-modules"></a>Executando os módulos
1. `npm install`
2. `npm start`

Se desejar encerrar o aplicativo, pressione a tecla `<Enter>`.

> [!IMPORTANT]
> Não é recomendável usar Ctrl + C para encerrar o aplicativo IoT Edge. Isso poderá fazer com que o processo seja terminado de maneira anormal.
