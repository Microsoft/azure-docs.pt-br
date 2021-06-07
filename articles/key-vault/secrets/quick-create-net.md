---
title: Guia de Início Rápido – Biblioteca de clientes de segredos do Azure Key Vault para .NET (versão 4)
description: Saiba como criar, recuperar e excluir segredos de um cofre de chaves do Azure usando a biblioteca de clientes do .NET (versão 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: a82c2cdf7084b31eb6ba861e48ecffb81e6d1363
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453692"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-net-sdk-v4"></a>Início Rápido: Biblioteca de clientes do segredo do Azure Key Vault para .NET (SDK v4)

Introdução à biblioteca de clientes do segredo do Azure Key Vault para .NET. O [Azure Key Vault](../general/overview.md) é um serviço de nuvem que funciona como um repositório seguro de segredos. Você pode armazenar chaves, senhas, certificados e outros segredos com segurança. Os cofres de chaves do Azure podem ser criados e gerenciados por meio do portal do Azure. Neste guia de início rápido, você aprenderá a criar, recuperar e excluir segredos de um cofre de chaves do Azure usando a biblioteca de clientes do .NET

Recursos da biblioteca de clientes do Key Vault:

[Documentação de referência da API](/dotnet/api/azure.security.keyvault.secrets) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

Para obter mais informações sobre o Key Vault e os segredos, confira:
- [Visão Geral do Key Vault](../general/overview.md)
- [Visão Geral dos Segredos](about-secrets.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/dotnet)
* [SDK do .NET Core 3.1 ou posterior](https://dotnet.microsoft.com/download/dotnet-core)
* [CLI do Azure](/cli/azure/install-azure-cli)
* Um Key Vault – crie um usando o [portal do Azure](../general/quick-create-portal.md), a [CLI do Azure](../general/quick-create-cli.md) ou o [Azure PowerShell](../general/quick-create-powershell.md)

Este guia de início rápido usa o `dotnet` e a CLI do Azure

## <a name="setup"></a>Instalação

Este guia de início rápido usa a biblioteca de identidades do Azure com a CLI do Azure para autenticar o usuário nos serviços do Azure. Os desenvolvedores também podem usar o Visual Studio ou o Visual Studio Code para autenticar as chamadas. Para saber mais, confira [Autenticar o cliente na biblioteca de clientes do Azure Idendity](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true).

### <a name="sign-in-to-azure"></a>Entrar no Azure

1. Execute o comando `login`.

    ```azurecli-interactive
    az login
    ```

    Se a CLI puder abrir o navegador padrão, ela o fará e carregará uma página de entrada do Azure.

    Caso contrário, abra uma página de navegador em [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e insira o código de autorização exibido no terminal.

2. Entre com suas credenciais de conta no navegador.

### <a name="grant-access-to-your-key-vault"></a>Permitir acesso ao cofre de chaves

Crie uma política de acesso para o cofre de chaves que conceda permissões de segredos à sua conta de usuário

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

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

No shell de comando, instale a biblioteca de clientes do segredo do Azure Key Vault para .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
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
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS ou Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Modelo de objeto

A biblioteca de clientes do segredo do Azure Key Vault para .NET permite que você gerencie segredos. A seção [Exemplos de código](#code-examples) mostra como criar um cliente, bem como definir, recuperar e excluir um segredo.

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas à parte superior de *Program.cs*:

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Neste guia de início rápido, o usuário conectado é usado para se autenticar no cofre de chaves, que é o método preferencial para o desenvolvimento local. Para os aplicativos implantados no Azure, a identidade gerenciada deve ser atribuída ao Serviço de Aplicativo ou à Máquina Virtual. Para obter mais informações, confira [Visão geral da identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

No exemplo abaixo, o nome do cofre de chaves é expandido para o URI do cofre de chaves, no formato "https://\<your-key-vault-name\>.vault.azure.net". Este exemplo usa a classe ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential) da [Biblioteca de Identidades do Azure](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme), que permite usar o mesmo código em diferentes ambientes com outras opções para fornecer a identidade. Para obter mais informações sobre como se autenticar no cofre de chaves, confira [Guia do Desenvolvedor](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Salvar um segredo

Agora que o aplicativo de console foi autenticado, adicione um segredo ao cofre de chaves. Para essa tarefa, use o método [SetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.setsecretasync). O primeiro parâmetro do método aceita um nome para o segredo "mySecret" neste exemplo.

```csharp
await client.SetSecretAsync(secretName, secretValue);
```

> [!NOTE]
> Se o nome do segredo existir, o código acima criará uma versão desse segredo.


### <a name="retrieve-a-secret"></a>Recuperar um segredo

Agora, você pode recuperar o valor definido anteriormente com o método [GetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.getsecretasync).

```csharp
var secret = await client.GetSecretAsync(secretName);
```

Agora, seu segredo está salvo como `secret.Value`.

### <a name="delete-a-secret"></a>Excluir um segredo

Por fim, vamos excluir o segredo do cofre de chaves com os métodos [StartDeleteSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.startdeletesecretasync) e [PurgeDeletedSecretAsync](/dotnet/api/azure.security.keyvault.keys.keyclient).

```csharp
var operation = await client.StartDeleteSecretAsync("mySecret");
// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

await client.PurgeDeletedKeyAsync("mySecret");
```

## <a name="sample-code"></a>Código de exemplo

Modifique o aplicativo de console .NET Core para interagir com o Key Vault concluindo as seguintes etapas:

1. Substitua o código em *Program.cs* pelo código a seguir:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                await client.SetSecretAsync(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                var secret = await client.GetSecretAsync(secretName);
                Console.WriteLine($"Your secret is '{secret.Value.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                DeleteSecretOperation operation = await client.StartDeleteSecretAsync(secretName);
                // You only need to wait for completion if you want to purge or recover the secret.
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
                
                Console.Write($"Purging your secret from {keyVaultName} ...");
                await client.PurgeDeletedSecretAsync(secretName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testar e verificar

1. Execute o comando a seguir para executar o aplicativo.

    ```dotnetcli
    dotnet run
    ```

1. Quando solicitado, insira um valor secreto. Por exemplo, mySecretPassword.

Uma variação do seguinte resultado é exibida:

```console
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

Para saber mais sobre o Key Vault e como integrá-lo aos seus aplicativos, confira os seguintes artigos:

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Confira um [Tutorial – Acessar o Key Vault no aplicativo do Serviço de Aplicativo](../general/tutorial-net-create-vault-azure-web-app.md)
- Confira um [Tutorial – Acessar o Key Vault na máquina virtual](../general/tutorial-net-virtual-machine.md)
- Confira o [Guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Examine a [Visão geral de segurança do Key Vault](../general/security-overview.md)
