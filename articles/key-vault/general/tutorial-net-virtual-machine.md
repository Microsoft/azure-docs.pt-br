---
title: Tutorial – Usar o Azure Key Vault com uma máquina virtual no .NET | Microsoft Docs
description: Neste tutorial, você vai configurar uma máquina virtual com um aplicativo do ASP.NET Core para ler um segredo do cofre de chaves.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: a56c08e5bf6054d24af3ade571ec625969286a77
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455637"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-net"></a>Tutorial: Usar o Azure Key Vault com uma máquina virtual no .NET

O Azure Key Vault ajuda a proteger segredos como chaves de API, as cadeias de conexão de banco de dados que você precisa para acessar seus aplicativos, serviços e recursos de TI.

Neste tutorial, você aprenderá como fazer um aplicativo de console ler as informações no Azure Key Vault. O aplicativo usará a identidade gerenciada da máquina virtual para se autenticar no Key Vault. 

Este tutorial mostra como:

> [!div class="checklist"]
> * Crie um grupos de recursos.
> * Crie um cofre da chave.
> * Adicionar um segredo ao cofre de chaves.
> * Recuperar um segredo do cofre de chaves.
> * Crie uma máquina virtual do Azure.
> * Habilitar uma [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) para a Máquina Virtual.
> * Atribuir permissões à identidade da VM.

Antes de começar, leia [Conceitos básicos do Key Vault](basic-concepts.md). 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Pré-requisitos

Para Windows, Mac e Linux:
  * [Git](https://git-scm.com/downloads)
  * O [SDK do .NET Core 3.1 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1).
  * [CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-resources-and-assign-permissions"></a>Criar recursos e atribuir permissões

Antes de começar a codificar, você precisará criar alguns recursos, colocar um segredo no cofre de chaves e atribuir permissões.

### <a name="sign-in-to-azure"></a>Entrar no Azure

Para entrar no Azure usando a CLI do Azure, digite:

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e um cofre de chaves

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Preencher seu cofre de chaves com um segredo

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Crie uma máquina virtual do Windows ou Linux usando um dos seguintes métodos:

| Windows | Linux |
|--|--|
| [CLI do Azure](../../virtual-machines/windows/quick-create-cli.md) | [CLI do Azure](../../virtual-machines/linux/quick-create-cli.md) |  
| [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) | [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) |
| [Azure portal](../../virtual-machines/windows/quick-create-portal.md) | [Azure portal](../../virtual-machines/linux/quick-create-portal.md) |

## <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade à VM
Crie uma identidade atribuída pelo sistema para a máquina virtual com o comando [az vm identity assign](/cli/azure/vm/identity#az-vm-identity-assign):

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Observe a identidade atribuída pelo sistema que é exibida no código a seguir. A saída do comando anterior seria: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Atribuir permissões à identidade da VM
Atribua as permissões de identidade criadas anteriormente ao cofre de chaves, com o comando [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy):

```azurecli
az keyvault set-policy --name '<your-unique-key-vault-name>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="sign-in-to-the-virtual-machine"></a>Entrar na máquina virtual

Para entrar na máquina virtual, siga as instruções em [Conectar-se e entrar em uma máquina virtual do Azure do Windows](../../virtual-machines/windows/connect-logon.md) ou [Conectar-se e fazer logon em uma máquina virtual do Azure do Linux](../../virtual-machines/linux/login-using-aad.md).

## <a name="set-up-the-console-app"></a>Configurar o aplicativo de console

Criar um aplicativo de console e instalar os pacotes necessários usando o comando `dotnet`.

### <a name="install-net-core"></a>Instalar o .NET Core

Para instalar o .NET Core, acesse a página [Downloads do .NET](https://www.microsoft.com/net/download).

### <a name="create-and-run-a-sample-net-app"></a>Criar e executar um aplicativo .NET de exemplo

Abra um prompt de comando.

Você pode exibir "Olá, Mundo" no console executando os comandos a seguir:

```console
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>Instalar o pacote

Na janela do console, instale a biblioteca de clientes de Segredos do Azure Key Vault para .NET:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

Para este guia de início rápido, você precisará instalar o seguinte pacote de identidade para autenticação no Azure Key Vault:

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>Editar o aplicativo de console

Abra o arquivo *Program.cs* e adicione esses pacotes:

```csharp
using System;
using Azure.Core;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Adicione essas linhas, atualizando o URI para refletir o `vaultUri` do cofre de chaves. O código abaixo está usando ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential) para autenticação no cofre de chaves, que está usando o token da identidade gerenciada do aplicativo para autenticar. Ele também está usando retirada exponencial para novas tentativas, caso o cofre de chaves esteja sendo limitado.

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            var kvUri = "https://<your-key-vault-name>.vault.azure.net";
            SecretClientOptions options = new SecretClientOptions()
            {
                Retry =
                {
                    Delay= TimeSpan.FromSeconds(2),
                    MaxDelay = TimeSpan.FromSeconds(16),
                    MaxRetries = 5,
                    Mode = RetryMode.Exponential
                 }
            };

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential(),options);

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando eles não forem mais necessários, exclua a máquina virtual e o cofre de chaves.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [API REST do Azure Key Vault](/rest/api/keyvault/)
