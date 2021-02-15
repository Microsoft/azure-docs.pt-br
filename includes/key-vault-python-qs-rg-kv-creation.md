---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 5e912b76c2ef68aa01dae57d1b42abc386a8e67b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "89482108"
---
1. Use o comando `az group create` para criar um grupo de recursos:

    ```azurecli
    az group create --name KeyVault-PythonQS-rg --location eastus
    ```

    Você poderá alterar "eastus" para um local mais próximo de você, se preferir.

1. Use `az keyvault create` para criar o cofre de chaves:

    ```azurecli
    az keyvault create --name <your-unique-keyvault-name> --resource-group KeyVault-PythonQS-rg
    ```

    Substitua `<your-unique-keyvault-name>` por um nome que seja exclusivo em todo o Azure. Normalmente, você usa o nome pessoal ou da empresa junto com outros números e identificadores. 

1. Crie uma variável de ambiente que forneça o nome do Key Vault para o código:

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    ---
