---
title: Tutorial – Usar o Azure Key Vault com uma máquina virtual no Python | Microsoft Docs
description: Neste tutorial, você configurará uma máquina virtual com um aplicativo Python para que ele leia um segredo do cofre de chaves.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 25182105db831724565c6bf3dbbbb79832b677f7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772052"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Tutorial: Usar o Azure Key Vault com uma máquina virtual no Python

O Azure Key Vault ajuda a proteger chaves, segredos e certificados, como chaves de API e cadeias de conexão de banco de dados.

Neste tutorial, você vai configurar um aplicativo do Python para ler informações do Azure Key Vault usando identidades gerenciadas para recursos do Azure. Você aprenderá como:

> [!div class="checklist"]
> * Criar um cofre de chave
> * Armazenar um segredo no Key Vault
> * Criar uma máquina virtual do Linux no Azure
> * Habilitar uma [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) para a máquina virtual
> * Conceder as permissões necessárias para o aplicativo de console ler dados do Key Vault
> * Recuperar um segredo do Key Vault

Antes de começar, leia [Conceitos básicos do Key Vault](basic-concepts.md). 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Pré-requisitos

Para Windows, Mac e Linux:
  * [Git](https://git-scm.com/downloads)
  * Este tutorial requer que você execute a CLI do Azure localmente. Você deve ter a CLI do Azure versão 2.0.4 ou posterior instalada. Execute `az --version` para encontrar a versão. Caso precise instalar ou atualizar a CLI, confira [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Para fazer logon no Azure usando a CLI do Azure, digite:

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e um cofre de chaves

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Preencher seu cofre de chaves com um segredo

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM chamada **myVM** usando um dos seguintes métodos:

| Linux | Windows |
|--|--|
| [CLI do Azure](../../virtual-machines/linux/quick-create-cli.md) | [CLI do Azure](../../virtual-machines/windows/quick-create-cli.md) |
| [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) | [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) |
| [Azure portal](../../virtual-machines/linux/quick-create-portal.md) | [O portal do Azure](../../virtual-machines/windows/quick-create-portal.md) |

Para criar uma VM Linux usando a CLI do Azure, use o comando [az vm create](/cli/azure/vm).  O exemplo a seguir cria uma conta de usuário chamada *azureuser*. O parâmetro `--generate-ssh-keys` é usado para gerar automaticamente uma chave SSH e colocá-la no local de chave padrão ( *~/.ssh*). 

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Observe o valor de `publicIpAddress` na saída.

## <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade à VM

Crie uma identidade atribuída pelo sistema para a máquina virtual usando o comando [az vm identity assign](/cli/azure/vm/identity#az_vm_identity_assign) da CLI do Azure:

```azurecli
az vm identity assign --name "myVM" --resource-group "myResourceGroup"
```

Observe a identidade atribuída pelo sistema que é exibida no código a seguir. A saída do comando anterior seria: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Atribuir permissões à identidade da VM

Agora você pode atribuir as permissões de identidade criadas anteriormente para o cofre de chaves, executando o seguinte comando:

```azurecli
az keyvault set-policy --name "<your-unique-keyvault-name>" --object-id "<systemAssignedIdentity>" --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Fazer logon na VM

Para entrar na máquina virtual, siga as instruções em [Conectar-se e fazer logon em uma máquina virtual do Azure executando o Linux](../../virtual-machines/linux/login-using-aad.md) ou [Conectar-se e fazer logon em uma máquina virtual do Azure executando o Windows](../../virtual-machines/windows/connect-logon.md).


Para fazer logon em uma VM do Linux, você pode usar o comando ssh com o "<publicIpAddress>" fornecido na etapa [Criar uma máquina virtual](#create-a-virtual-machine):

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-libraries-on-the-vm"></a>Instalar as bibliotecas do Python na VM

Na máquina virtual, instale as duas bibliotecas do Python que usaremos em nosso script Python: `azure-keyvault-secrets` e `azure.identity`.  

Em uma VM do Linux, por exemplo, você pode instalá-las usando `pip3`:

```bash
pip3 install azure-keyvault-secrets

pip3 install azure.identity
```

## <a name="create-and-edit-the-sample-python-script"></a>Criar e editar o script de exemplo do Python

Na máquina virtual, crie um arquivo Python chamado **sample.py**. Edite o arquivo para que ele contenha o seguinte código, substituindo <seu-nome-de-cofre-de-chaves-exclusivo> pelo nome do seu cofre de chaves:

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = "<your-unique-keyvault-name>"
KVUri = f"https://{keyVaultName}.vault.azure.net"
secretName = "mySecret"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
retrieved_secret = client.get_secret(secretName)

print(f"The value of secret '{secretName}' in '{keyVaultName}' is: '{retrieved_secret.value}'")
```

## <a name="run-the-sample-python-app"></a>Executar o aplicativo Python de exemplo

Execute **sample.py**. Se tudo correr bem, ele retornará o valor de seu segredo:

```bash
python3 sample.py

The value of secret 'mySecret' in '<your-unique-keyvault-name>' is: 'Success!'
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando eles não forem mais necessários, exclua a máquina virtual e o cofre de chaves.  Você pode fazer isso rapidamente apenas excluindo o grupo de recursos ao qual eles pertencem:

```azurecli
az group delete -g myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

[API REST do Azure Key Vault](/rest/api/keyvault/)