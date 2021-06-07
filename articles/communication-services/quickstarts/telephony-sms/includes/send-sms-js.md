---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: bertong
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: bertong
ms.openlocfilehash: 8fe8b853fe07af40603950a61c0dd2a1df74d14e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644322"
---
Comece a usar os Serviços de Comunicação do Azure usando o SDK de SMS do JavaScript dos Serviços de Comunicação para enviar mensagens SMS.

A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua conta do Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-js-pr/tree/feature/communication/sdk/communication/communication-sms) | [Package (NPM)](https://www.npmjs.com/package/@azure/communication-sms) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/), versões Active LTS e Maintenance LTS (8.11.1 e 10.14.1 recomendadas).
- Um recurso e uma cadeia de conexão ativos dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).
- Um número de telefone habilitado para SMS. [Obter um número de telefone](../get-phone-number.md).

### <a name="prerequisite-check"></a>Verificação de pré-requisitos

- Em uma janela de terminal ou de comando, execute `node --version` para verificar se o Node.js está instalado.
- Para exibir os números de telefone associados ao seu recurso dos Serviços de Comunicação, entre no [portal do Azure](https://portal.azure.com/), localize o recurso em questão e abra a guia **números de telefone** no painel de navegação esquerdo.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-nodejs-application"></a>Criar um aplicativo Node.js

Primeiro, abra a janela de comando ou do terminal para criar um diretório para seu aplicativo e navegue até ele.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Execute `npm init -y` para criar um arquivo **package.json** com as configurações padrão.

```console
npm init -y
```

Use um editor de texto para criar um arquivo chamado **send-sms.js** no diretório raiz do projeto. Você adicionará todo o código-fonte deste guia de início rápido a esse arquivo nas seções a seguir.

### <a name="install-the-package"></a>Instalar o pacote

Use o comando `npm install` para instalar o SDK de SMS dos Serviços de Comunicação do Azure para JavaScript.

```console
npm install @azure/communication-sms --save
```

A opção `--save` lista a biblioteca como uma dependência no arquivo **package.json**.

## <a name="object-model"></a>Modelo de objeto

As classes e interfaces a seguir lidam com alguns dos principais recursos do SDK de SMS dos Serviços de Comunicação do Azure para Node.js.

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Essa classe é necessária para toda a funcionalidade de SMS. Crie uma instância dela com suas informações de assinatura e use-a para enviar mensagens SMS. |
| SmsSendRequest | Essa interface é o modelo para a criação da solicitação de SMS (por exemplo, configurar os números de telefone de envio e de destino e o conteúdo do SMS). |
| SmsSendOptions | Essa interface fornece opções para configurar o relatório de entrega. Se `enableDeliveryReport` for definido como `true`, um evento será emitido quando a entrega for bem-sucedida. |
| SmsSendResult               | Essa classe contém o resultado do serviço de SMS.                                          |

## <a name="authenticate-the-client"></a>Autenticar o cliente

Importe o **SmsClient** do SDK e crie uma instância dele com sua cadeia de conexão. O código abaixo recupera a cadeia de conexão do recurso de uma variável de ambiente chamada `COMMUNICATION_SERVICES_CONNECTION_STRING`. Saiba como [gerenciar a cadeia de conexão do seu recurso](../../create-communication-resource.md#store-your-connection-string).

Crie e abra um arquivo chamado **send-sms.js** e adicione o seguinte código:

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-a-1n-sms-message"></a>Enviar uma mensagem SMS 1:N

Para enviar uma mensagem SMS para uma lista de destinatários, chame a função `send` no SmsClient com uma lista de números de telefone de destinatários (se você quiser enviar uma mensagem para um só destinatário, inclua apenas um número na lista). Adicione este código ao final de **send-sms.js**:

```javascript
async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Hello World 👋🏻 via SMS"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```
Substitua `<from-phone-number>` por um número de telefone habilitado para SMS associado ao seu recurso dos Serviços de Comunicação e `<to-phone-number-1>` e `<to-phone-number-2>` pelos números de telefone para os quais deseja enviar uma mensagem.

> [!WARNING]
> Observe que os números de telefone devem ser fornecidos no formato padrão internacional E.164. (por exemplo: +14255550123).

## <a name="send-a-1n-sms-message-with-options"></a>Enviar uma mensagem SMS 1:N com opções

Você também pode transmitir um objeto de opções para especificar se o relatório de entrega deve ser habilitado e para definir marcas personalizadas.

```javascript

async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Weekly Promotion!"
  }, {
    //Optional parameters
    enableDeliveryReport: true,
    tag: "marketing"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```

Substitua `<from-phone-number>` pelo número de telefone habilitado para SMS associado ao seu recurso dos Serviços de Comunicação e `<to-phone-number-1>` e `<to-phone-number-2>` pelos números de telefones para os quais você deseja enviar uma mensagem.

> [!WARNING]
> Observe que os números de telefone devem ser fornecidos no formato padrão internacional E.164. (por exemplo: +14255550123).

O parâmetro `enableDeliveryReport` é um parâmetro opcional que você pode usar para configurar o Relatório de Entrega. Isso é útil para cenários em que você deseja emitir eventos quando mensagens SMS são entregues. Confira o guia de início rápido [Manipular eventos de SMS](../handle-sms-events.md) para configurar o Relatório de Entrega das suas mensagens SMS.
`tag` é um parâmetro opcional que pode ser usado para aplicar uma marca ao relatório de entrega.

## <a name="run-the-code"></a>Executar o código

Use o comando `node` para executar o código adicionado ao arquivo **send-sms.js**.

```console

node ./send-sms.js

```
