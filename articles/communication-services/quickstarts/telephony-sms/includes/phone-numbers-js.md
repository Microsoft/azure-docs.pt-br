---
ms.openlocfilehash: 956c92c5c020f892b8148e9d43d403b1099fbdba
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113210"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/), versões Active LTS e Maintenance LTS (8.11.1 e 10.14.1 recomendadas).
- Um recurso e uma cadeia de conexão ativos dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>Verificação de pré-requisitos

- Em uma janela de terminal ou de comando, execute `node --version` para verificar se o Node.js está instalado.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-nodejs-application"></a>Criar um aplicativo Node.js

Primeiro, abra a janela de comando ou do terminal para criar um diretório para seu aplicativo e navegue até ele.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

Execute `npm init -y` para criar um arquivo **package.json** com as configurações padrão.

```console
npm init -y
```

Crie um arquivo chamado **phone-numbers-quickstart.js** na raiz do diretório que você acabou de criar. Adicione o seguinte snippet a ele:

```javascript
async function main() {
    // quickstart code will here
}

main();
```

### <a name="install-the-package"></a>Instalar o pacote

Use o comando `npm install` para instalar a biblioteca de clientes de Números de Telefone dos Serviços de Comunicação do Azure para JavaScript.

```console
npm install @azure/communication-phone-numbers --save
```

A opção `--save` adiciona a biblioteca como uma dependência no arquivo **package.json**.

## <a name="authenticate-the-client"></a>Autenticar o cliente

Importe o **PhoneNumbersClient** da biblioteca de clientes e crie uma instância dele com sua cadeia de conexão. O código abaixo recupera a cadeia de conexão do recurso de uma variável de ambiente chamada `COMMUNICATION_SERVICES_CONNECTION_STRING`. Saiba como [gerenciar a cadeia de conexão do seu recurso](../../create-communication-resource.md#store-your-connection-string).

Adicione o seguinte código à parte superior de **phone-numbers-quickstart.js**:

```javascript
const { PhoneNumbersClient } = require('@azure/communication-phone-numbers');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the phone numbers client
const phoneNumbersClient = new PhoneNumbersClient(connectionString);
```

## <a name="manage-phone-numbers"></a>Gerenciar números de telefone

### <a name="search-for-available-phone-numbers"></a>Pesquisar números de telefone disponíveis

Para comprar números de telefone, primeiro você precisa procurar números de telefone disponíveis. Para procurar números de telefone, forneça o código de área, o tipo de atribuição, as [funcionalidades do número de telefone](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), o [tipo de número de telefone](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services) e a quantidade. Observe que para números de telefone do tipo chamada gratuita, fornecer o código de área é opcional.

Adicione o snippet a seguir à sua função `main`:

```javascript
/**
 * Search for Available Phone Number
 */

// Create search request
const searchRequest = {
    countryCode: "US",
    phoneNumberType: "tollFree",
    assignmentType: "application",
    capabilities: {
      sms: "outbound",
      calling: "none"
    },
    areaCode: "833",
    quantity: 1
  };

const searchPoller = await phoneNumbersClient.beginSearchAvailablePhoneNumbers(searchRequest);

// The search is underway. Wait to receive searchId.
const { searchId, phoneNumbers } = searchPoller.pollUntilDone();
const phoneNumber = phoneNumbers[0];

console.log(`Found phone number: ${phoneNumber}`);
console.log(`searchId: ${searchId}`);
```

### <a name="purchase-phone-number"></a>Comprar número de telefone

O resultado da pesquisa por números de telefone é um `PhoneNumberSearchResult`. Ela contém um `searchId` que pode ser passado para a API de comprar números para adquirir os números na pesquisa. Observe que chamar a API de comprar números de telefone resultará em um encargo para sua conta do Azure.

Adicione o snippet a seguir à sua função `main`:

```javascript
/**
 * Purchase Phone Number
 */

const purchasePoller = await phoneNumbersClient.beginPurchasePhoneNumbers(searchId);

// Purchase is underway.
await purchasePoller.pollUntilDone();
console.log(`Successfully purchased ${phoneNumber}`);
```

### <a name="update-phone-number-capabilities"></a>Atualizar as funcionalidades do número de telefone

Com o número de telefone comprado, adicione o seguinte código para atualizar as funcionalidades dele:

```javascript
/**
 * Update Phone Number Capabilities
 */

// Create update request.
// This will update phone number to send and receive sms, but only send calls.
const updateRequest = {
  sms: "inbound+outbound",
  calling: "outbound"
};

const updatePoller = await phoneNumbersClient.beginUpdatePhoneNumberCapabilities(
  phoneNumber,
  updateRequest
);

// Update is underway.
await updatePoller.pollUntilDone();
console.log("Phone number updated successfully.");
```

### <a name="get-purchased-phone-numbers"></a>Obter números de telefone comprados

Depois de comprar um número, você poderá recuperá-lo do cliente. Adicione o seguinte código à sua função `main` para obter o número de telefone que você acabou de comprar:

```javascript
/**
 * Get Purchased Phone Number
 */

const { capabilities } = await phoneNumbersClient.getPurchasedPhoneNumber(phoneNumber);
console.log(`These capabilities: ${capabilities}, should be the same as these: ${updateRequest}.`);
```

Você também pode recuperar todos os números de telefone comprados.

```javascript
const phoneNumbers = await phoneNumbersClient.listPurchasedPhoneNumbers();

for await (const purchasedPhoneNumber of phoneNumbers) {
  console.log(`Phone number: ${purchasedPhoneNumber.phoneNumber}, country code: ${purchasedPhoneNumber.countryCode}.`);
}
```

### <a name="release-phone-number"></a>Liberar número de telefone

Você também pode liberar todos os números de telefone comprados. Adicione o snippet de código abaixo à sua função `main`:

```javascript
/**
 * Release Purchased Phone Number
 */

const releasePoller = await client.beginReleasePhoneNumber(phoneNumber);

// Release is underway.
await releasePoller.pollUntilDone();
console.log("Successfully release phone number.");
```

## <a name="run-the-code"></a>Executar o código

Use o comando `node` para executar o código adicionado ao arquivo **phone-numbers-quickstart.js**.

```console
node phone-numbers-quickstart.js
```