---
title: Início Rápido – Biblioteca de clientes de chave do Azure Key Vault para JavaScript (versão 4)
description: Saiba como criar, recuperar e excluir chaves de um Azure Key Vault usando a biblioteca de clientes do JavaScript
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: c0066409732f4492186ea0bf604261e1ab59ca9f
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750294"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-javascript-version-4"></a>Início rápido: Biblioteca de clientes de chave do Azure Key Vault para JavaScript (versão 4)

Introdução à biblioteca de clientes de chaves do Azure Key Vault para JavaScript. O [Azure Key Vault](../general/overview.md) é um serviço de nuvem que funciona como um repositório seguro de chaves de criptografia. Você pode armazenar chaves, senhas, certificados e outros segredos com segurança. Os cofres de chaves do Azure podem ser criados e gerenciados por meio do portal do Azure. Neste início rápido, você aprenderá a criar, recuperar e excluir chaves de um cofre de chaves do Azure usando a biblioteca de clientes de chaves do JavaScript

Recursos da biblioteca de clientes do Key Vault:

[Documentação de referência da API](/javascript/api/overview/azure/key-vault-index) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [Pacote (npm)](https://www.npmjs.com/package/@azure/keyvault-keys)

Para obter mais informações sobre o Key Vault e as chaves, confira:
- [Visão Geral do Key Vault](../general/overview.md)
- [Visão geral das chaves](about-keys.md).

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

```azurecli
mkdir key-vault-node-app
```

1. Altere para o diretório *key-vault-node-app* recém-criado e execute o comando "nit" para inicializar o projeto do Node:

```azurecli
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Instalar pacotes do Key Vault

Na janela do console, instale a [biblioteca de chaves](https://www.npmjs.com/package/@azure/keyvault-keys) do Azure Key Vault para Node.js.

```azurecli
npm install @azure/keyvault-keys
```

Instalar o pacote [azure.identity](https://www.npmjs.com/package/@azure/identity) para autenticar em um Key Vault

```azurecli
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

Crie uma política de acesso para o cofre de chaves que conceda permissões de chave à sua conta de usuário

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --key-permissions delete get list create purge
```

## <a name="code-examples"></a>Exemplos de código

Os exemplos de código abaixo mostrarão como criar um cliente, definir uma chave, recuperar uma chave e excluir uma chave. 

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
const { KeyClient } = require("@azure/keyvault-keys");
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Neste guia de início rápido, o usuário conectado é usado para se autenticar no cofre de chaves, que é o método preferencial para o desenvolvimento local. Para os aplicativos implantados no Azure, a identidade gerenciada deve ser atribuída ao Serviço de Aplicativo ou à Máquina Virtual. Para obter mais informações, confira [Visão geral da identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

No exemplo abaixo, o nome do cofre de chaves é expandido para o URI do cofre de chaves, no formato "https://\<your-key-vault-name\>.vault.azure.net". Este exemplo usa a classe ['DefaultAzureCredential()'](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) da [Biblioteca de Identidades do Azure](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme), que permite usar o mesmo código em diferentes ambientes com outras opções para fornecer a identidade. Para obter mais informações sobre como se autenticar no cofre de chaves, confira [Guia do Desenvolvedor](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Adicione o código a seguir à função 'main()'

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new KeyClient(KVUri, credential);
```

### <a name="save-a-key"></a>Salvar uma chave

Agora que o aplicativo foi autenticado, você pode colocar uma chave no cofre de chaves usando o [método createKey](/javascript/api/@azure/keyvault-keys/keyclient?#createKey_string__KeyType__CreateKeyOptions_). Os parâmetros do método aceitam um nome de chave e o [tipo de chave](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keytype)

```javascript
await client.createKey(keyName, keyType);
```

### <a name="retrieve-a-key"></a>Recuperar uma chave

Agora, você pode recuperar o valor definido anteriormente com o [método getKey](/javascript/api/@azure/keyvault-keys/keyclient?#getKey_string__GetKeyOptions_).

```javascript
const retrievedKey = await client.getKey(keyName);
 ```

### <a name="delete-a-key"></a>Excluir uma chave

Por fim, vamos excluir e limpar a chave do cofre de chaves com os métodos [beginDeleteKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#beginDeleteKey_string__BeginDeleteKeyOptions_) e [purgeDeletedKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#purgeDeletedKey_string__PurgeDeletedKeyOptions_).

```javascript
const deletePoller = await client.beginDeleteKey(keyName);
await deletePoller.pollUntilDone();
await client.purgeDeletedKey(keyName);
```

## <a name="sample-code"></a>Código de exemplo

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { KeyClient } = require("@azure/keyvault-keys");

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
  const client = new KeyClient(KVUri, credential);

  const keyName = "myKey";
  
  console.log("Creating a key in " + keyVaultName + " called '" + keyName + "` ...");
  await client.createKey(keyName, "RSA");

  console.log("Done.");

  console.log("Retrieving your key from " + keyVaultName + ".");

  const retrievedKey = await client.getKey(keyName);

  console.log("Your key version is '" + retrievedKey.properties.version + "'.");

  console.log("Deleting your key from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteKey(keyName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your key from {keyVaultName} ...");
  await client.purgeDeletedKey(keyName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Testar e verificar

Execute os comandos a seguir para executar o aplicativo.

```azurecli
npm install
npm index.js
```

Uma variação do seguinte resultado é exibida:

```azurecli
Creating a key in <your-unique-keyvault-name> called 'myKey' ... done.
Retrieving your key from mykeyvault.
Your key version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your key from <your-unique-keyvault-name> ... done.  
Purging your key from <your-unique-keyvault-name> ... done.   
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um cofre de chaves, armazenou uma chave e a recuperou. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Leia uma [Visão geral das chaves do Azure Key Vault](about-keys.md)
- Como [Proteger o acesso a um cofre de chaves](../general/security-overview.md)
- Confira o [Guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Examine a [Visão geral de segurança do Key Vault](../general/security-overview.md)
