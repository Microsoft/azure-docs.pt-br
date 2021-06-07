---
title: Início Rápido – Biblioteca de clientes de certificado do Azure Key Vault para JavaScript (versão 4)
description: Saiba como criar, recuperar e excluir certificados de um cofre de chaves do Azure usando a biblioteca de clientes do JavaScript
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 1064c6a1e2dddaae98e94ccceca7b1d550897393
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97930847"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-javascript-version-4"></a>Início rápido: Biblioteca de clientes de certificado do Azure Key Vault para JavaScript (versão 4)

Introdução à biblioteca de clientes de certificados do Azure Key Vault para JavaScript. O [Azure Key Vault](../general/overview.md) é um serviço de nuvem que funciona como um repositório seguro de certificados. Você pode armazenar chaves, senhas, certificados e outros segredos com segurança. Os cofres de chaves do Azure podem ser criados e gerenciados por meio do portal do Azure. Neste guia de início rápido, você aprenderá a criar, recuperar e excluir certificados de um cofre de chaves do Azure usando a biblioteca de clientes do JavaScript

Recursos da biblioteca de clientes do Key Vault:

[Documentação de referência da API](/javascript/api/overview/azure/key-vault-index) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [Pacote (npm)](https://www.npmjs.com/package/@azure/keyvault-certificates)

Para obter mais informações sobre o Key Vault e os certificados, confira:
- [Visão Geral do Key Vault](../general/overview.md)
- [Visão geral dos certificados](about-certificates.md).

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

Na janela do console, instale a [biblioteca de certificados](https://www.npmjs.com/package/@azure/keyvault-certificates) do Azure Key Vault para Node.js.

```azurecli
npm install @azure/keyvault-certificates
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

Crie uma política de acesso para o cofre de chaves que conceda permissões de certificado à sua conta de usuário

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create purge
```

## <a name="code-examples"></a>Exemplos de código

Os exemplos de código abaixo mostrarão como criar um cliente, definir um certificado, recuperar um certificado e excluir um certificado. 

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
const { CertificateClient } = require("@azure/keyvault-certificates");
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Neste guia de início rápido, o usuário conectado é usado para se autenticar no cofre de chaves, que é o método preferencial para o desenvolvimento local. Para os aplicativos implantados no Azure, a identidade gerenciada deve ser atribuída ao Serviço de Aplicativo ou à Máquina Virtual. Para obter mais informações, confira [Visão geral da identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

No exemplo abaixo, o nome do cofre de chaves é expandido para o URI do cofre de chaves, no formato "https://\<your-key-vault-name\>.vault.azure.net". Este exemplo usa a classe ['DefaultAzureCredential()'](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) da [Biblioteca de Identidades do Azure](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme), que permite usar o mesmo código em diferentes ambientes com outras opções para fornecer a identidade. Para obter mais informações sobre como se autenticar no cofre de chaves, confira [Guia do Desenvolvedor](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Adicione o código a seguir à função 'main()'

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new Certificate(KVUri, credential);
```

### <a name="save-a-certificate"></a>Salvar um certificado

Agora que o aplicativo foi autenticado, você pode colocar um certificado em seu cofre de chaves usando o [método beginCreateCertificate](/javascript/api/@azure/keyvault-certificates/certificateclient?#beginCreateCertificate_string__CertificatePolicy__BeginCreateCertificateOptions_). Isso requer um nome para o certificado e a política de certificação[política de certificação](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicy) com [propriedades de política de certificação](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicyproperties)

```javascript
const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
};
const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
const certificate = await poller.pollUntilDone();
```

> [!NOTE]
> Se o nome do certificado existir, o código acima criará uma versão desse certificado.
### <a name="retrieve-a-certificate"></a>Recuperar um certificado

Agora, você pode recuperar o valor definido anteriormente com o [método getCertificate](/javascript/api/@azure/keyvault-certificates/certificateclient?#getCertificate_string__GetCertificateOption).

```javascript
const retrievedCertificate = await client.getCertificate(certificateName);
 ```

### <a name="delete-a-certificate"></a>Excluir um certificado

Por fim, vamos excluir e limpar o certificado do cofre de chaves com os métodos [beginDeleteCertificate]https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#beginDeleteCertificate_string__BeginDeleteCertificateOptions_) e [purgeDeletedCertificate](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#purgeDeletedCertificate_string__PurgeDeletedCertificateOptions_).

```javascript
const deletePoller = await client.beginDeleteCertificate(certificateName);
await deletePoller.pollUntilDone();
await client.purgeDeletedCertificate(certificateName);
```

## <a name="sample-code"></a>Código de exemplo

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { CertificateClient } = require("@azure/keyvault-certificates");

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

  const string certificateName = "myCertificate";
  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new CertificateClient(KVUri, credential);

  console.log("Creating a certificate in " + keyVaultName + " called '" + certificateName +  "` ...");
  const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
  };
  const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
  const certificate = await poller.pollUntilDone();

  console.log("Done.");

  console.log("Retrieving your certificate from " + keyVaultName + ".");

  const retrievedCertificate = await client.getCertificate(certificateName);

  console.log("Your certificate version is '" + retrievedCertificate.properties.version + "'.");

  console.log("Deleting your certificate from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteCertificate(certificateName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your certificate from {keyVaultName} ...");
  await client.purgeDeletedCertificate(certificateName);
  
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
Creating a certificate in mykeyvault called 'myCertificate' ... done.
Retrieving your certificate from mykeyvault.
Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your certificate from mykeyvault ... done
Purging your certificate from mykeyvault ... done 
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um cofre de chaves e armazenou e recuperou um certificado. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Leia uma [Visão geral dos certificados](about-certificates.md)
- Confira um [Tutorial – Acessar o Key Vault no aplicativo do Serviço de Aplicativo](../general/tutorial-net-create-vault-azure-web-app.md)
- Confira um [Tutorial – Acessar o Key Vault na máquina virtual](../general/tutorial-net-virtual-machine.md)
- Confira o [Guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Examine a [Visão geral de segurança do Key Vault](../general/security-overview.md)
