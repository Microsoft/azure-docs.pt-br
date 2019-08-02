---
title: Tutorial – Usar uma máquina virtual do Linux e um aplicativo de console ASP.NET para armazenar segredos no Azure Key Vault | Microsoft Docs
description: Neste tutorial, você aprenderá a configurar um aplicativo ASP.NET Core para ler um segredo do Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 8c142b4c1663a7ccd462836e9aac4ee80accaae4
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67874827"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>Tutorial: Usar uma VM do Linux e um aplicativo .NET para armazenar segredos no Azure Key Vault

O Azure Key Vault ajuda você a proteger segredos, como chaves de API e cadeias de conexão de banco de dados necessárias para acessar seus aplicativos, serviços e recursos de TI.

Neste tutorial, você configurará um aplicativo de console .NET para ler informações do Azure Key Vault usando identidades gerenciadas para recursos do Azure. Você aprenderá como:

> [!div class="checklist"]
> * Criar um cofre de chave
> * Armazenar um segredo no Key Vault
> * Criar uma máquina virtual do Linux no Azure
> * Habilitar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para a máquina virtual
> * Conceder as permissões necessárias para o aplicativo de console ler dados do Key Vault
> * Recuperar um segredo do Key Vault

Antes de continuarmos, leia sobre [os conceitos básicos do Key Vault](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Pré-requisitos

* [Git](https://git-scm.com/downloads).
* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* [CLI 2.0 do Azure ou posterior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ou Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Entender a Identidade de Serviço Gerenciada

O Azure Key Vault pode armazenar credenciais com segurança para que elas não precisem estar em seu código, mas, para recuperá-las, você precisa fazer a autenticação no Azure Key Vault. Entanto, para fazer a autenticação no Key Vault, você precisa de uma credencial. Esse é um problema clássico de inicialização. Com o Azure e o Azure AD (Azure Active Directory), a MSI (Identidade de Serviço Gerenciada) fornece uma identidade de inicialização que simplifica tudo.

Quando você habilita a MSI para um serviço do Azure como Máquinas Virtuais, Serviço de Aplicativo ou Functions, o Azure cria uma entidade de serviço para a instância do serviço no Azure Active Directory. Ele injeta as credenciais da entidade de serviço na instância do serviço.

![MSI](media/MSI.png)

Em seguida, seu código chama um serviço de metadados local disponível no recurso do Azure para obter um token de acesso.
Seu código usa o token de acesso obtido do MSI_ENDPOINT local para fazer a autenticação em um serviço Azure Key Vault.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Para entrar no Azure usando a CLI do Azure, digite:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos usando o comando `az group create`. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos na localização Oeste dos EUA. Escolha um nome para o grupo de recursos e substitua `YourResourceGroupName` no seguinte exemplo:

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Você usará esse grupo de recursos durante este tutorial.

## <a name="create-a-key-vault"></a>Criar um cofre de chave

Em seguida, crie um cofre de chaves no grupo de recursos. Forneça as seguintes informações:

* Nome do cofre de chaves: uma cadeia de caracteres de 3 a 24 caracteres que pode conter somente números, letras e hifens (0-9, a-z, A-Z e \-).
* Nome do grupo de recursos
* Localização: **Oeste dos EUA**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Nesse ponto, somente sua conta do Azure é autorizada a executar qualquer operação nesse novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Agora, adicione um segredo. Em um cenário do mundo real, talvez você esteja armazenando uma cadeia de conexão SQL ou qualquer outra informação que você precise manter com segurança, mas disponibilizar para seu aplicativo.

Para este tutorial, digite os comandos a seguir para criar um segredo no cofre de chaves. O segredo é chamado **AppSecret** e seu valor é **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Criar uma máquina virtual Linux

Crie uma VM com o comando `az vm create`.

O exemplo a seguir cria uma VM chamada **myVM** e adiciona uma conta de usuário chamada **azureuser**. O parâmetro `--generate-ssh-keys` é usado para gerar uma chave SSH automaticamente e colocá-la na localização de chave padrão ( **~/.ssh**). Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A criação da VM e dos recursos de suporte demora alguns minutos. A saída de exemplo a seguir mostra que a operação de criação de VM foi bem-sucedida.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

Anote seu `publicIpAddress` na saída da VM. Você usará esse endereço para acessar a VM em etapas posteriores.

## <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade à VM

Crie uma identidade atribuída pelo sistema para a máquina virtual executando o seguinte comando:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

A saída do comando deverá ser:

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Anote `systemAssignedIdentity`. Você a usará na próxima etapa.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Conceder à identidade de VM permissão ao Key Vault

Agora você pode dar permissão ao Key Vault à identidade que você criou. Execute o comando a seguir:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Fazer logon na VM

Faça logon na máquina virtual usando um terminal.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>Instalar o .NET Core no Linux

Na VM do Linux:

Registre a chave do produto (Product Key) da Microsoft como confiável executando os seguintes comandos:

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

Configure o feed de pacote do host da versão desejada com base no sistema operacional:

```console
   # Ubuntu 17.10
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-artful-prod artful main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 17.04
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-zesty-prod zesty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 16.04 / Linux Mint 18
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 14.04 / Linux Mint 17
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
```

Instale o .NET e verifique a versão:

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>Criar e executar um aplicativo .NET de exemplo

Execute os seguintes comandos: Você deverá ver "Olá, Mundo" impresso no console.

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>Editar o aplicativo de console para buscar o segredo

Abra o arquivo Program.cs e adicione estes pacotes:

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

É um processo em duas etapas para alterar o arquivo de classe e permitir que o aplicativo acesse o segredo no cofre de chaves.

1. Busque um token no ponto de extremidade da MSI local na VM que, por sua vez, busca um token no Azure Active Directory.
1. Passe o token para o Key Vault e busque o segredo.

   Edite o arquivo de classe para conter o seguinte código:

   ```csharp
    class Program
       {
           static void Main(string[] args)
           {
               // Step 1: Get a token from local (URI) Managed Service Identity endpoint which in turn fetches it from Azure Active Directory
               var token = GetToken();

               // Step 2: Fetch the secret value from Key Vault
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
               WebRequest kvRequest = WebRequest.Create("https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01");
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

Agora você aprendeu como executar operações com o Azure Key Vault em um aplicativo .NET executado em uma máquina virtual do Linux no Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Exclua o grupo de recursos, a máquina virtual e todos os recursos relacionados quando não precisar mais deles. Para fazer isso, selecione o grupo de recursos da VM e selecione **Excluir**.

Exclua o cofre de chaves usando o comando `az keyvault delete`:

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [API REST do Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
