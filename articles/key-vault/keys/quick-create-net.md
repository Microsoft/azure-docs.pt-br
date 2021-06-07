---
title: Guia de Início Rápido – Biblioteca de clientes de chaves do Azure Key Vault para .NET (versão 4)
description: Saiba como criar, recuperar e excluir chaves de um cofre de chaves do Azure usando a biblioteca de clientes do .NET (versão 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: 52e33660b4076f8119ddb5d77e2dbbf7ee201913
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968451"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-net-sdk-v4"></a>Início rápido: Biblioteca de clientes de chaves do Azure Key Vault para .NET (SDK v4)

Introdução à biblioteca de clientes de chaves do Azure Key Vault para .NET. O [Azure Key Vault](../general/overview.md) é um serviço de nuvem que funciona como um repositório seguro de chaves de criptografia. Você pode armazenar chaves de criptografia, senhas, certificados e outros segredos com segurança. Os cofres de chaves do Azure podem ser criados e gerenciados por meio do portal do Azure. Neste guia de início rápido, você aprenderá a criar, recuperar e excluir chaves de um cofre de chaves do Azure usando a biblioteca de clientes de chaves do .NET

Recursos da biblioteca de clientes de chaves do Key Vault:

[Documentação de referência da API](/dotnet/api/azure.security.keyvault.keys) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.keys/)

Para obter mais informações sobre o Key Vault e as chaves, confira:
- [Visão Geral do Key Vault](../general/overview.md)
- [Visão geral das chaves](about-keys.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/dotnet)
* [SDK do .NET Core 3.1 ou posterior](https://dotnet.microsoft.com/download/dotnet-core)
* [CLI do Azure](/cli/azure/install-azure-cli)
* Um Key Vault: crie um usando o [portal do Azure](../general/quick-create-portal.md), a [CLI do Azure](../general/quick-create-cli.md) ou o [Azure PowerShell](../general/quick-create-powershell.md).

## <a name="setup"></a>Instalação

Este guia de início rápido usa a biblioteca de Identidade do Azure para autenticar o usuário nos Serviços do Azure. Os desenvolvedores também podem usar o Visual Studio ou o Visual Studio Code para autenticar as chamadas. Para saber mais, confira [Autenticar o cliente na biblioteca de clientes do Azure Idendity](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true).

### <a name="sign-in-to-azure"></a>Entrar no Azure

1. Execute o comando `login`.

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    ```azurecli-interactive
    az login
    ```
    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azurepowershell)
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
    ---

    Se a CLI do Azure ou o Azure PowerShell puder abrir o navegador padrão, ele o fará e carregará uma página de entrada do Azure.

    Caso contrário, abra uma página de navegador em [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e insira o código de autorização exibido no terminal.

2. Entre com suas credenciais de conta no navegador.

#### <a name="grant-access-to-your-key-vault"></a>Permitir acesso ao cofre de chaves

Crie uma política de acesso para o cofre de chaves que conceda permissões de chave à sua conta de usuário

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli-interactive
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
```
# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azurepowershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <your-key-vault-name> -UserPrincipalName user@domain.com -PermissionsToSecrets delete,get,list,set,purge
```
---

### <a name="create-new-net-console-app"></a>Criar um novo aplicativo de console do .NET

1. Em um shell de comando, execute o seguinte comando para criar um projeto chamado `key-vault-console-app`:

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Altere para o diretório *key-vault-console-app* recém-criado e execute o seguinte comando para compilar o projeto:

    ```dotnetcli
    dotnet build
    ```

    A saída de compilação não deve conter nenhum aviso ou erro.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>Instalar os pacotes

No shell de comando, instale a biblioteca de clientes de chaves do Azure Key Vault para .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Keys
```

Para este guia de início rápido, você também precisará instalar a biblioteca de clientes do SDK do Azure para a identidade do Azure:

```dotnetcli
dotnet add package Azure.Identity
```

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente

Este aplicativo usa o nome do cofre de chaves como uma variável de ambiente chamada `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```azurepowershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS ou Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Modelo de objeto

Com a biblioteca de clientes de chaves do Azure Key Vault para .NET, você pode gerenciar chaves. A seção [Exemplos de código](#code-examples) mostra como criar um cliente, definir, recuperar e excluir uma chave.

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas à parte superior de *Program.cs*:

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Keys;
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Neste guia de início rápido, o usuário conectado é usado para se autenticar no cofre de chaves, que é o método preferencial para o desenvolvimento local. Para os aplicativos implantados no Azure, a identidade gerenciada deve ser atribuída ao Serviço de Aplicativo ou à Máquina Virtual. Para obter mais informações, confira [Visão geral da identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

No exemplo abaixo, o nome do cofre de chaves é expandido para o URI do cofre de chaves, no formato "https://\<your-key-vault-name\>.vault.azure.net". Este exemplo usa a classe ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential) da [Biblioteca de Identidades do Azure](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme), que permite usar o mesmo código em diferentes ambientes com outras opções para fornecer a identidade. Para obter mais informações sobre como se autenticar no cofre de chaves, confira [Guia do Desenvolvedor](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

```csharp
var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-key"></a>Salvar uma chave

Para essa tarefa, use o método [CreateKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.createkeyasync). Os parâmetros do método aceitam um nome de chave e o [tipo de chave](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.keys.keytype).

```csharp
var key = await client.CreateKeyAsync("myKey", KeyType.Rsa);
```

> [!NOTE]
> Se o nome da chave existir, o código acima criará uma versão dessa chave.

### <a name="retrieve-a-key"></a>Recuperar uma chave

Agora você pode recuperar a chave criada anteriormente com o método [GetKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.getkeyasync).

```csharp
var key = await client.GetKeyAsync("myKey");
```

### <a name="delete-a-key"></a>Excluir uma chave

Por fim, vamos excluir e limpar a chave do cofre de chaves com os métodos [StartDeleteKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.startdeletekeyasync) e [PurgeDeletedKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.purgedeletedkeyasync).

```csharp
var operation = await client.StartDeleteKeyAsync("myKey");

// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

var key = operation.Value;
await client.PurgeDeletedKeyAsync("myKey");
```

## <a name="sample-code"></a>Código de exemplo

Modifique o aplicativo de console .NET Core para interagir com o Key Vault concluindo as seguintes etapas:

- Substitua o código em *Program.cs* pelo código a seguir:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Keys;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string keyName = "myKey";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a key in {keyVaultName} called '{keyName}' ...");
                var createdKey = await client.CreateKeyAsync(keyName, KeyType.Rsa);
                Console.WriteLine("done.");
    
                Console.WriteLine($"Retrieving your key from {keyVaultName}.");
                var key = await client.GetKeyAsync(keyName);
                Console.WriteLine($"Your key version is '{key.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your key from {keyVaultName} ...");
                var deleteOperation = await client.StartDeleteKeyAsync(keyName);
                // You only need to wait for completion if you want to purge or recover the key.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine("done.");

                Console.Write($"Purging your key from {keyVaultName} ...");
                await client.PurgeDeletedKeyAsync(keyName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testar e verificar

1.  Execute o comando a seguir para compilar o projeto

    ```dotnetcli
    dotnet build
    ```

1. Execute o comando a seguir para executar o aplicativo.

    ```dotnetcli
    dotnet run
    ```

1. Quando solicitado, insira um valor secreto. Por exemplo, mySecretPassword.

    Uma variação do seguinte resultado é exibida:

    ```console
    Creating a key in mykeyvault called 'myKey' ... done.
    Retrieving your key from mykeyvault.
    Your key version is '8532359bced24e4bb2525f2d2050738a'.
    Deleting your key from jl-kv ... done
    Purging your key from <your-unique-keyvault-name> ... done.   
    ```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um cofre de chaves, armazenou uma chave e a recuperou. 

Para saber mais sobre o Key Vault e como integrá-lo aos seus aplicativos, confira os seguintes artigos:

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Leia uma [Visão geral das chaves](about-keys.md)
- Confira um [Tutorial – Acessar o Key Vault no aplicativo do Serviço de Aplicativo](../general/tutorial-net-create-vault-azure-web-app.md)
- Confira um [Tutorial – Acessar o Key Vault na máquina virtual](../general/tutorial-net-virtual-machine.md)
- Confira o [Guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Examine a [Visão geral de segurança do Key Vault](../general/security-overview.md)
