---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 59359d37fe347c8568c7944f75accdbc04cddb93
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967108"
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

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```