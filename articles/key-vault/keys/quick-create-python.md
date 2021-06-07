---
title: Início Rápido – Biblioteca de clientes Python do Azure Key Vault – gerenciar chaves
description: Saiba como criar, recuperar e excluir chaves de um Azure Key Vault usando a biblioteca de clientes do Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 2c15090551ad9d84282f65925ff9c2cfbab7e14f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773343"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Início Rápido: Biblioteca de clientes de chaves do Azure Key Vault para Python

Introdução à biblioteca de clientes do Azure Key Vault para Python. Siga as etapas abaixo para instalar o pacote e testar o código de exemplo para tarefas básicas. Ao usar o Key Vault para armazenar chaves criptográficas, você evita armazenar essas chaves em seu código, o que aumenta a segurança do seu aplicativo.

[Documentação de referência da API](/python/api/overview/azure/keyvault-keys-readme) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys) | [Pacote (Índice de Pacote do Python)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- O [Python 2.7+ ou 3.6+](/azure/developer/python/configure-local-development-environment)
- [CLI do Azure](/cli/azure/install-azure-cli)

Este início rápido pressupõe que você está executando a [CLI do Azure](/cli/azure/install-azure-cli) em uma janela de terminal do Linux.

## <a name="set-up-your-local-environment"></a>Configurar o ambiente local

Este guia de início rápido usa a biblioteca de identidades do Azure com a CLI do Azure para autenticar o usuário nos serviços do Azure. Os desenvolvedores também podem usar o Visual Studio ou o Visual Studio Code para autenticar as chamadas. Para saber mais, confira [Autenticar o cliente na biblioteca de clientes do Azure Idendity](/python/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Entrar no Azure

1. Execute o comando `login`.

    ```azurecli-interactive
    az login
    ```

    Se a CLI puder abrir o navegador padrão, ela o fará e carregará uma página de entrada do Azure.

    Caso contrário, abra uma página de navegador em [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e insira o código de autorização exibido no terminal.

2. Entre com suas credenciais de conta no navegador.

### <a name="install-the-packages"></a>Instalar os pacotes

1. Em um terminal ou prompt de comando, crie uma pasta de projeto adequada e depois crie e ative um ambiente virtual do Python conforme descrito em [Usar ambientes virtuais do Python](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments).

1. Instale a biblioteca de identidades do Azure Active Directory:

    ```terminal
    pip install azure.identity
    ```


1. Instale a biblioteca de clientes de chave do Key Vault:

    ```terminal
    pip install azure-keyvault-keys
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e um cofre de chaves

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Permitir acesso ao cofre de chaves

Crie uma política de acesso para o cofre de chaves que conceda a permissão de segredos à sua conta de usuário.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
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
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>Criar o código de exemplo

A biblioteca de clientes de chaves do Azure Key Vault para Python permite gerenciar chaves de criptografia. O exemplo de código a seguir demonstra como criar um cliente, definir uma chave, recuperar uma chave e excluir uma chave.

Crie um arquivo chamado *kv_keys.py* que contenha esse código.

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = input("Input a name for your key > ")

print(f"Creating a key in {keyVaultName} called '{keyName}' ...")

rsa_key = client.create_rsa_key(keyName, size=2048)

print(" done.")

print(f"Retrieving your key from {keyVaultName}.")

retrieved_key = client.get_key(keyName)

print(f"Key with name '{retrieved_key.name}' was found.")
print(f"Deleting your key from {keyVaultName} ...")

poller = client.begin_delete_key(keyName)
deleted_key = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Executar o código

Verifique se o código na seção anterior está em um arquivo chamado *kv_keys.py*. Execute o código com o seguinte comando:

```terminal
python kv_keys.py
```

- Se você encontrar erros de permissões, verifique se executou o [comando `az keyvault set-policy`](#grant-access-to-your-key-vault).
- Executar novamente o código com o mesmo nome de chave pode produzir este erro: "A Chave (Conflito) <name> está em um estado excluído, mas recuperável". Use um nome de chave diferente.

## <a name="code-details"></a>Detalhes do código

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Neste guia de início rápido, o usuário conectado é usado para se autenticar no cofre de chaves, que é o método preferencial para o desenvolvimento local. Para os aplicativos implantados no Azure, a identidade gerenciada deve ser atribuída ao Serviço de Aplicativo ou à Máquina Virtual. Para obter mais informações, confira [Visão geral da identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

No exemplo abaixo, o nome do cofre de chaves é expandido para o URI do cofre de chaves, no formato "https://\<your-key-vault-name\>.vault.azure.net". Este exemplo usa a classe ['DefaultAzureCredential()'](/python/api/azure-identity/azure.identity.defaultazurecredential), que permite usar o mesmo código em diferentes ambientes com outras opções para fornecer a identidade. Para obter mais informações, confira [Autenticação de credenciais do Azure padrão](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 


```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Salvar uma chave

Depois de obter o objeto de cliente para o cofre de chaves, você pode armazenar uma chave usando o método [create_rsa_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-rsa-key-name----kwargs-): 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

Você também pode usar [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-key-name--key-type----kwargs-) ou [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-ec-key-name----kwargs-).

Chamar um método `create` gera uma chamada à API REST do Azure para o cofre de chaves.

Ao processar a solicitação, o Azure autentica a identidade do chamador (a entidade de serviço) usando o objeto de credencial fornecido ao cliente.

## <a name="retrieve-a-key"></a>Recuperar uma chave

Para ler uma chave do Key Vault, use o método [get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#get-key-name--version-none----kwargs-):

```python
retrieved_key = client.get_key(keyName)
 ```

Você também pode verificar se a chave foi definida com o comando da CLI do Azure [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show).

### <a name="delete-a-key"></a>Excluir uma chave

Para excluir uma chave, use o método [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#begin-delete-key-name----kwargs-):

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

O método `begin_delete_key` é assíncrono e retorna um objeto do instrumento de sondagem. Chamar o método `result` do instrumento de sondagem aguarda sua conclusão.

Você pode verificar se a chave foi excluída com o comando da CLI do Azure [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show).

Depois de excluída, uma chave permanece em um estado excluído, mas recuperável, por algum tempo. Se você executar o código novamente, use um nome de chave diferente.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você também quiser experimentar [certificados](../certificates/quick-create-python.md) e [segredos](../secrets/quick-create-python.md), poderá reutilizar o Key Vault criado neste artigo.

Caso contrário, quando tiver concluído os recursos criados neste artigo, use o seguinte comando para excluir o grupo de recursos e todos os recursos que ele contém:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da o Cofre da Chave do Azure](../general/overview.md)
- [Proteger o acesso a um cofre de chaves](../general/security-overview.md)
- [Guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- [Visão geral da segurança do Key Vault](../general/security-overview.md)
- [Autenticar com o Key Vault](../general/authentication.md)
