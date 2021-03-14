---
title: Armazenar credenciais de acesso com segurança
titleSuffix: Azure Data Science Virtual Machine
description: Saiba como armazenar com segurança as credenciais de acesso na Máquina Virtual de Ciência de Dados. Você aprenderá a usar identidades de serviço gerenciadas e Azure Key Vault para armazenar credenciais de acesso.
keywords: aprendizado profundo, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial, processo de ciência de dados da equipe
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: adc66fc794ac6cce3fc0f8fd1552f701a4968860
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519670"
---
# <a name="store-access-credentials-securely-on-an-azure-data-science-virtual-machine"></a>Armazenar credenciais de acesso com segurança em um Máquina Virtual de Ciência de Dados do Azure

É comum que o código em aplicativos de nuvem contenha credenciais para autenticação em serviços de nuvem. Como gerenciar e proteger essas credenciais é um desafio bem conhecido na criação de aplicativos em nuvem. O ideal é que as credenciais nunca apareçam em estações de trabalho do desenvolvedor ou obtenha check-in no controle do código-fonte.

O recurso [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md) facilita a solução desse problema, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). Você pode usar essa identidade para se autenticar em qualquer serviço que dá suporte à autenticação do Azure AD sem ter as credenciais no código.

Uma maneira de proteger as credenciais é usar Windows Installer (MSI) em combinação com [Azure Key Vault](../../key-vault/index.yml), um serviço gerenciado do Azure para armazenar segredos e chaves de criptografia com segurança. Você pode acessar um cofre de chaves usando a identidade gerenciada e, em seguida, recuperar os segredos autorizados e as chaves de criptografia do cofre de chaves.

A documentação sobre identidades gerenciadas para recursos do Azure e Key Vault consiste em um recurso abrangente para informações detalhadas sobre esses serviços. O restante deste artigo explica o uso básico do MSI e do Cofre de Chaves na DSVM (Máquina Virtual de Ciência de Dados) para acessar recursos do Azure. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>Criar uma identidade gerenciada no DSVM

```azurecli-interactive
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```

## <a name="assign-key-vault-access-permissions-to-a-vm-principal"></a>Atribuir Key Vault permissões de acesso a uma entidade de VM

```azurecli-interactive
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI.

# Assign only get and set permissions but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>Acessar um segredo no cofre de chaves da DSVM

```bash
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token.
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>Acessar chaves de armazenamento pela DSVM

```bash
# Prerequisite: You have granted your VMs MSI access to use storage account access keys based on instructions at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

# Now you can access the data in the storage account from the retrieved storage account keys.
```

## <a name="access-the-key-vault-from-python"></a>Acessar o cofre de chaves do Python

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials.
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a Key Vault client.
key_vault_client = KeyVaultClient(
    credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
    key_vault_uri,  # Your key vault URL.
    # The name of your secret that already exists in the key vault.
    "SQLPasswd",
    ""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>Acessar o cofre de chaves da CLI do Azure

```azurecli-interactive
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. The following commands enable access to the key vault from Azure CLI without requiring login to an Azure account.
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permissions, like accessing storage account keys, reading specific secrets, and writing new secrets, are provided to the MSI.

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```