---
title: Tutorial – usar uma máquina virtual do Linux e um aplicativo do Python para armazenar segredos no Azure Key Vault | Microsoft Docs
description: Neste tutorial, você aprenderá como configurar um aplicativo do Python para ler um segredo do Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: f002a4d67ee782b11cd8f39784eb48b136328c89
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876699"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>Tutorial: Usar uma VM do Linux e um aplicativo do Python para armazenar segredos no Azure Key Vault

O Azure Key Vault ajuda a proteger segredos, como as chaves de API e as cadeias de conexão de banco de dados necessárias para acessar aplicativos, serviços e recursos de TI.

Neste tutorial, você configura um aplicativo Web do Azure para ler informações do Azure Key Vault usando identidades gerenciadas de recursos do Azure. Você aprenderá como:

> [!div class="checklist"]
> * Criar um cofre de chave
> * Armazenar um segredo em seu cofre de chaves
> * Criar uma máquina virtual Linux
> * Habilitar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para a máquina virtual
> * Conceder as permissões necessárias para o aplicativo de console ler dados do cofre de chaves
> * Recuperar um segredo do seu cofre de chaves

Antes de prosseguir, verifique se você entendeu os [conceitos básicos sobre o Key Vault](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Pré-requisitos

* [Git](https://git-scm.com/downloads).
* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* [CLI do Azure versão 2.0.4 ou posterior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ou o Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Entender a Identidade de Serviço Gerenciada

O Azure Key Vault pode armazenar credenciais com segurança para que elas não fiquem no código. Para recuperá-las, você precisará se autenticar no Azure Key Vault. Entanto, para fazer a autenticação no Key Vault, você precisa de uma credencial. Esse é um problema clássico de inicialização. Por meio do Azure e do Azure AD (Azure Active Directory), a MSI (Identidade de Serviço Gerenciada) fornece uma identidade de inicialização que simplifica tudo.

Quando você habilita a MSI para um serviço do Azure como Máquinas Virtuais, Serviço de Aplicativo ou Functions, o Azure cria uma entidade de serviço para a instância do serviço no Azure AD. Ele injeta as credenciais da entidade de serviço na instância do serviço.

![MSI](media/MSI.png)

Em seguida, seu código chama um serviço de metadados local disponível no recurso do Azure para obter um token de acesso. O código usa o token de acesso obtido do ponto de extremidade da MSI local para se autenticar em um serviço do Azure Key Vault.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Para entrar no Azure usando a CLI do Azure, digite:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos usando o comando `az group create` no local Oeste dos EUA com o código a seguir. Substitua `YourResourceGroupName` por um nome de sua escolha.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Você usará esse grupo de recursos durante este tutorial.

## <a name="create-a-key-vault"></a>Criar um cofre de chave

Em seguida, você cria um cofre de chaves no grupo de recursos criado na etapa anterior. Forneça as seguintes informações:

* Nome do cofre de chaves: O nome precisa ser uma cadeia de 3 a 24 caracteres e conter apenas 0-9, a-z, A-Z e hifens (-).
* Nome do grupo de recursos.
* Localização: **Oeste dos EUA**.

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Nesse ponto, sua conta do Azure é a única autorizada a executar qualquer operação nesse novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Estamos adicionando um segredo para ajudar a ilustrar como isso funciona. Pode ser interessante armazenar uma cadeia de conexão do SQL ou qualquer outra informação que precise ser mantida segura e, ao mesmo tempo, disponível para seu aplicativo.

Digite os comandos a seguir para criar um segredo no cofre de chaves chamado *AppSecret*. Esse segredo armazenará o valor **MySecret**.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Criar uma máquina virtual Linux

Crie uma VM usando o comando `az vm create`.

O exemplo a seguir cria uma VM chamada **myVM** e adiciona uma conta de usuário chamada **azureuser**. O parâmetro `--generate-ssh-keys` gera automaticamente uma chave SSH e coloca-a na localização de chave padrão ( **~/.ssh**). Para criar um conjunto específico de chaves, use a opção `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A criação da VM e dos recursos de suporte demora alguns minutos. A seguinte saída de exemplo mostra que a criação de VM foi bem-sucedida:

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

Anote a sua própria `publicIpAddress` na saída da VM. Você usará esse endereço para acessar a VM em etapas posteriores.

## <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade à VM

Crie uma identidade atribuída pelo sistema para a máquina virtual executando o seguinte comando:

```azurecli-interactive
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

A saída do comando é mostrada a seguir.

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Anote `systemAssignedIdentity`. Você usará na próxima etapa.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Conceder à identidade de VM permissão ao Key Vault

Agora você pode dar permissão ao Key Vault à identidade que você criou. Execute o comando a seguir:

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Fazer logon na VM

Faça logon na máquina virtual usando um terminal.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>Instalar a biblioteca do Python na VM

Baixe e instale a biblioteca do Python de [solicitações](https://pypi.org/project/requests/2.7.0/) para fazer chamadas GET HTTP.

## <a name="create-edit-and-run-the-sample-python-app"></a>Criar, editar e executar o aplicativo de exemplo do Python

Crie um arquivo de Python chamado **Sample.py**.

Abra o Sample.py e edite-o para conter o seguinte código:

```python
# importing the requests library
  import requests
  
# Step 1: Fetch an access token from an MSI-enabled Azure resource      
  # Note that the resource here is https://vault.azure.net for the public cloud, and api-version is 2018-02-01
  MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
  r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"})

# Extracting data in JSON format 
  # This request gets an access token from Azure Active Directory by using the local MSI endpoint
  data = r.json()

# Step 2: Pass the access token received from the previous HTTP GET call to the key vault
  KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
  kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})

print(kvSecret.json()["value"])
```

O código anterior executa um processo em duas etapas:

   1. Efetua fetch de um token do ponto de extremidade da MSI local na VM. Em seguida, o ponto de extremidade efetuará fetch de um token do Azure Active Directory.
   1. Passa o token para o cofre de chaves e efetua fetch do segredo.

Execute o comando a seguir. Você deve ver o valor do segredo.

```console
python Sample.py
```

Neste tutorial, você aprendeu a usar o Azure Key Vault com um aplicativo do Python em execução em uma máquina virtual do Linux.

## <a name="clean-up-resources"></a>Limpar recursos

Exclua o grupo de recursos, a máquina virtual e todos os recursos relacionados quando não precisar mais deles. Para fazer isso, selecione o grupo de recursos da VM e selecione **Excluir**.

Exclua o cofre de chaves usando o comando `az keyvault delete`:

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [API REST do Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
