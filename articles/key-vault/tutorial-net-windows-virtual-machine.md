---
title: Tutorial – Usar o Azure Key Vault com uma máquina virtual do Windows no .NET | Microsoft Docs
description: Neste tutorial, você vai configurar um aplicativo do ASP.NET Core para ler um segredo no cofre de chaves.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 3bb4647b39a276e2dd54260c17eca1d700d5ba16
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67874978"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Tutorial: Usar o Azure Key Vault com uma máquina virtual do Windows no .NET

O Azure Key Vault ajuda a proteger segredos como chaves de API, as cadeias de conexão de banco de dados que você precisa para acessar seus aplicativos, serviços e recursos de TI.

Neste tutorial, você aprenderá como fazer um aplicativo de console ler as informações no Azure Key Vault. Para isso, você usará identidades gerenciadas para recursos do Azure. 

Este tutorial mostra como:

> [!div class="checklist"]
> * Crie um grupos de recursos.
> * Crie um cofre da chave.
> * Adicionar um segredo ao cofre de chaves.
> * Recuperar um segredo do cofre de chaves.
> * Crie uma máquina virtual do Azure.
> * Habilitar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para a Máquina Virtual.
> * Atribuir permissões à identidade da VM.

Antes de começar, leia [Conceitos básicos do Key Vault](key-vault-whatis.md#basic-concepts). 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Pré-requisitos

Para Windows, Mac e Linux:
  * [Git](https://git-scm.com/downloads)
  * Este tutorial requer que você execute a CLI do Azure localmente. Você deve ter a CLI do Azure versão 2.0.4 ou posterior instalada. Execute `az --version` para encontrar a versão. Caso precise instalar ou atualizar a CLI, confira [Instalar a CLI 2.0 do Azure](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Sobre a Identidade de Serviço Gerenciada

O Azure Key Vault armazena credenciais com segurança para que elas não sejam exibidas no seu código. No entanto, você precisa se autenticar no Azure Key Vault para recuperar as chaves. Para fazer a autenticação no Key Vault, você precisa de uma credencial. Esse é um dilema clássico de inicialização. A MSI (Identidade de Serviço Gerenciada) resolve esse problema fornecendo uma _identidade de inicialização_ que simplifica o processo.

Ao habilitar a MSI para um serviço do Azure, como Máquinas Virtuais do Azure, Serviço de Aplicativo do Azure ou Azure Functions, o Azure cria um [serviço principal](key-vault-whatis.md#basic-concepts). A MSI faz isso para a instância do serviço no Azure AD (Azure Active Directory) e injeta as credenciais da entidade de serviço nessa instância. 

![MSI](media/MSI.png)

Em seguida, para obter um token de acesso, o código chama um serviço de metadados local disponível no recurso do Azure. Para se autenticar em um serviço do Azure Key Vault, o código usa o token de acesso obtido do ponto de extremidade da MSI local. 

## <a name="create-resources-and-assign-permissions"></a>Criar recursos e atribuir permissões

Antes de começar a codificar, você precisará criar alguns recursos, colocar um segredo no cofre de chaves e atribuir permissões.

### <a name="sign-in-to-azure"></a>Entrar no Azure

Para entrar no Azure usando a CLI do Azure, digite:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Crie um grupo de recursos usando o comando [az group create](/cli/azure/group#az-group-create). 

Este exemplo cria o grupo de recursos localizado no Oeste dos EUA:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Você usará o grupo de recursos recém criado neste tutorial.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Criar um cofre de chaves e preenchê-lo com um segredo

Crie um cofre de chaves em seu grupo de recursos, fornecendo o comando [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) com as seguintes informações:

* Nome do cofre de chaves: uma cadeia de caracteres de 3 a 24 caracteres contendo somente números (0 a 9), letras (a a z, A a Z) e hifens (-)
* Nome do grupo de recursos
* Localização: **Oeste dos EUA**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Nesse ponto, sua conta do Azure é a única autorizada a executar operações nesse novo cofre de chaves.

Agora, adicione um segredo ao cofre de chaves usando o comando [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)


Para criar um segredo no cofre de chaves chamado **AppSecret**, insira o seguinte comando:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Esse segredo armazena o valor **MySecret**.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Crie uma máquina virtual usando um dos seguintes métodos:

* [A CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli)
* [PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)
* [O portal do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

### <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade à VM
Crie uma identidade atribuída pelo sistema para a máquina virtual com o comando [az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign):

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Observe a identidade atribuída pelo sistema que é exibida no código a seguir. A saída do comando anterior seria: 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>Atribuir permissões à identidade da VM
Atribua as permissões de identidade criadas anteriormente ao cofre de chaves, com o comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy):

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Entrar na máquina virtual

Para entrar na máquina virtual, siga as instruções em [Conectar e entrar em uma máquina virtual do Azure executando o Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="set-up-the-console-app"></a>Configurar o aplicativo de console

Criar um aplicativo de console e instalar os pacotes necessários usando o comando `dotnet`.

### <a name="install-net-core"></a>Instalar o .NET Core

Para instalar o .NET Core, acesse a página [Downloads do .NET](https://www.microsoft.com/net/download).

### <a name="create-and-run-a-sample-net-app"></a>Criar e executar um aplicativo .NET de exemplo

Abra um prompt de comando.

Você pode exibir "Olá, Mundo" no console executando os comandos a seguir:

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

### <a name="install-the-packages"></a>Instalar os pacotes

 Na janela do console, instale os pacotes do .NET necessários para este início rápido:

 ```console
dotnet add package System.IO;
dotnet add package System.Net;
dotnet add package System.Text;
dotnet add package Newtonsoft.Json;
dotnet add package Newtonsoft.Json.Linq;
```

## <a name="edit-the-console-app"></a>Editar o aplicativo de console

Abra o arquivo *Program.cs* e adicione esses pacotes:

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Edite o arquivo de classe para conter o código no processo de duas etapas a seguir:

1. Efetue fetch de um token do ponto de extremidade da MSI local na VM. Isso também efetua fetch de um token no Azure AD.
1. Passe o token para o cofre de chaves e, em seguida, efetue fetch do segredo. 

```csharp
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from the local (URI) Managed Service Identity endpoint, which in turn fetches it from Azure AD
            var token = GetToken();

            // Step 2: Fetch the secret value from your key vault
            System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
        }

        static string GetToken()
        {
            WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
            request.Headers.Add("Metadata", "true");
            WebResponse response = request.GetResponse();
            return ParseWebResponse(response, "access_token");
        }

        static string FetchSecretValueFromKeyVault(string token)
        {
            WebRequest kvRequest = WebRequest.Create("https://<YourVaultName>.vault.azure.net/secrets/<YourSecretName>?api-version=2016-10-01");
            kvRequest.Headers.Add("Authorization", "Bearer "+  token);
            WebResponse kvResponse = kvRequest.GetResponse();
            return ParseWebResponse(kvResponse, "value");
        }

        private static string ParseWebResponse(WebResponse response, string tokenName)
        {
            string token = String.Empty;
            using (Stream stream = response.GetResponseStream())
            {
                StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                String responseString = reader.ReadToEnd();

                JObject joResponse = JObject.Parse(responseString);    
                JValue ojObject = (JValue)joResponse[tokenName];             
                token = ojObject.Value.ToString();
            }
            return token;
        }
    }
```

O código anterior mostra como realizar operações com o Azure Key Vault em uma máquina virtual do Windows.

## <a name="clean-up-resources"></a>Limpar recursos

Quando eles não forem mais necessários, exclua a máquina virtual e o cofre de chaves.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [API REST do Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
