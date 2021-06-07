---
title: Início Rápido – Biblioteca de clientes de segredo do Azure Key Vault para JavaScript (versão 4)
description: Saiba como criar, recuperar e excluir segredos de um Azure Key Vault usando a biblioteca de clientes do JavaScript
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 769a63819925caa50ff364869b8d9a14c3258a2e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102214534"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-javascript-version-4"></a>Início rápido: Biblioteca de clientes de segredo do Azure Key Vault para JavaScript (versão 4)

Introdução à biblioteca de clientes do segredo do Azure Key Vault para JavaScript. O [Azure Key Vault](../general/overview.md) é um serviço de nuvem que funciona como um repositório seguro de segredos. Você pode armazenar chaves, senhas, certificados e outros segredos com segurança. Os cofres de chaves do Azure podem ser criados e gerenciados por meio do portal do Azure. Neste início rápido, você aprende a criar, recuperar e excluir segredos de um cofre de chaves do Azure usando a biblioteca de clientes do JavaScript

Recursos da biblioteca de clientes do Key Vault:

[Documentação de referência da API](/javascript/api/overview/azure/key-vault-index) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [Pacote (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

Para obter mais informações sobre o Key Vault e os segredos, confira:
- [Visão Geral do Key Vault](../general/overview.md)
- [Visão Geral dos Segredos](about-secrets.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org) atual para o seu sistema operacional.
- [CLI do Azure](/cli/azure/install-azure-cli)
- Um Key Vault – crie um usando o [portal do Azure](../general/quick-create-portal.md), a [CLI do Azure](../general/quick-create-cli.md) ou o [Azure PowerShell](../general/quick-create-powershell.md)

Este início rápido pressupõe que você esteja executando a [CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Entrar no Azure

1. Execute o comando `login`.

    ```azurecli-interactive
    az login
    ```

    Se a CLI puder abrir o navegador padrão, ela o fará e carregará uma página de entrada do Azure.

    Caso contrário, abra uma página de navegador em [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e insira o código de autorização exibido no terminal.

2. Entre com suas credenciais de conta no navegador.

## <a name="create-new-nodejs-application"></a>Criar um aplicativo Node.js

Em seguida, crie um aplicativo Node.js que possa ser implantado na nuvem. 

1. Em um shell de comando, crie uma pasta chamada `key-vault-node-app`:

```terminal
mkdir key-vault-node-app
```

1. Altere para o diretório *key-vault-node-app* recém-criado e execute o comando "nit" para inicializar o projeto do Node:

```terminal
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Instalar pacotes do Key Vault

Na janela do console, instale a [biblioteca de segredos](https://www.npmjs.com/package/@azure/keyvault-secrets) do Azure Key Vault para Node.js.

```terminal
npm install @azure/keyvault-secrets
```

Instalar o pacote [azure.identity](https://www.npmjs.com/package/@azure/identity) para autenticar em um Key Vault

```terminal
npm install @azure/identity
```

## <a name="set-environment-variables"></a>Definir variáveis de ambiente

Este aplicativo usa o nome do cofre de chaves como uma variável de ambiente chamada `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS ou Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="grant-access-to-your-key-vault"></a>Permitir acesso ao cofre de chaves

Crie uma política de acesso para o cofre de chaves que conceda permissões de segredos à sua conta de usuário

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

## <a name="code-examples"></a>Exemplos de código

Os exemplos de código abaixo mostrarão como criar um cliente, definir, recuperar e excluir um segredo. 

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

1. Crie um arquivo de texto e salve-o como "index.js"

1. Adicione chamadas necessárias para carregar os módulos do Azure e do Node.js

1. Crie a estrutura do programa, incluindo um tratamento de exceções básico

```javascript
const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {
    
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
```

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas à parte superior de seu código:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Neste guia de início rápido, o usuário conectado é usado para se autenticar no cofre de chaves, que é o método preferencial para o desenvolvimento local. Para os aplicativos implantados no Azure, a identidade gerenciada deve ser atribuída ao Serviço de Aplicativo ou à Máquina Virtual. Para obter mais informações, confira [Visão geral da identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

No exemplo abaixo, o nome do cofre de chaves é expandido para o URI do cofre de chaves, no formato "https://\<your-key-vault-name\>.vault.azure.net". Este exemplo usa a classe ['DefaultAzureCredential()'](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) da [Biblioteca de Identidades do Azure](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme), que permite usar o mesmo código em diferentes ambientes com outras opções para fornecer a identidade. Para obter mais informações sobre como se autenticar no cofre de chaves, confira [Guia do Desenvolvedor](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Adicione o código a seguir à função 'main()'

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Salvar um segredo

Agora que o aplicativo foi autenticado, é possível colocar um segredo no cofre de chaves usando o [método setSecret](/javascript/api/@azure/keyvault-secrets/secretclient#setSecret_string__string__SetSecretOptions_). Isso requer um nome para o segredo, estamos usando "mySecret" neste exemplo.  

```javascript
await client.setSecret(secretName, secretValue);
```

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Agora, você pode recuperar o valor definido anteriormente com o [método getSecret](/javascript/api/@azure/keyvault-secrets/secretclient#getSecret_string__GetSecretOptions_).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

Agora, seu segredo está salvo como `retrievedSecret.value`.

### <a name="delete-a-secret"></a>Excluir um segredo

Por fim, vamos excluir e limpar o segredo do cofre de chaves com os métodos [beginDeleteSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#beginDeleteSecret_string__BeginDeleteSecretOptions_) e [purgeDeletedSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#purgeDeletedSecret_string__PurgeDeletedSecretOptions_).

```javascript
const deletePoller = await client.beginDeleteSecret(secretName);
await deletePoller.pollUntilDone();
await client.purgeDeletedSecret(secretName);
```

## <a name="sample-code"></a>Código de exemplo

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");

  console.log("Deleting your secret from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteSecret(secretName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your secret from {keyVaultName} ...");
  await client.purgeDeletedSecret(secretName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Testar e verificar

1. Execute os comandos a seguir para executar o aplicativo.

    ```terminal
    npm install
    node index.js
    ```

1. Quando solicitado, insira um valor secreto. Por exemplo, mySecretPassword.

    Uma variação do seguinte resultado é exibida:

    ```azurecli
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.  
    Purging your secret from <your-unique-keyvault-name> ... done.   
    ```


## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um cofre de chaves, armazenou um segredo e o recuperou. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Leia uma [Visão geral dos Segredos do Azure Key Vault](about-secrets.md)
- Como [Proteger o acesso a um cofre de chaves](../general/secure-your-key-vault.md)
- Confira o [Guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Examine a [Visão geral de segurança do Key Vault](../general/security-overview.md)
