---
title: Use o Armazenamento do Azure como um back-end do Terraform
description: Uma introdução ao armazenamento do estado do Terraform no Armazenamento do Azure.
services: terraform
author: tomarchermsft
ms.service: azure
ms.topic: article
ms.date: 09/13/2018
ms.author: tarcher
ms.openlocfilehash: 7145a50bc53fd28afafd3de9c724b5e5f71624fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60905868"
---
# <a name="store-terraform-state-in-azure-storage"></a>Armazene o estado Terraform no armazenamento do Azure

O estado terraform é usado para reconciliar recursos implantados com configurações Terraform. Usando estado, o Terraform sabe quais recursos do Azure adicionar, atualizar ou excluir. Por padrão, o estado Terraform é armazenado localmente ao executar *Terraform apply*. Esta configuração não é ideal por alguns motivos:

- O estado local não funciona bem em um ambiente de equipe ou colaborativo
- O estado Terraform pode incluir informações confidenciais
- Armazenar estado localmente aumenta a chance de exclusão inadvertida

Terraform inclui o conceito de um backend de estado, que é o armazenamento remoto para o estado Terraform. Ao usar um backend de estado, o arquivo de estado é armazenado em um repositório de dados, como o Armazenamento do Azure. Este documento detalha como configurar e usar o Armazenamento do Azure como um back-end de estado do Terraform.

## <a name="configure-storage-account"></a>Configurar conta de armazenamento

Antes de usar o Armazenamento do Azure como back-end, uma conta de armazenamento deve ser criada. A conta de armazenamento pode ser criada com o portal do Azure, o PowerShell, o CLI do Azure ou o próprio Terraform. Use a amostra a seguir para configurar a conta de armazenamento com a CLI do Azure.

```azurecli-interactive
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

Anote o nome da conta de armazenamento, o nome do contêiner e a chave de acesso ao armazenamento. Esses valores são necessários ao configurar o estado remoto.

## <a name="configure-state-backend"></a>Configurar o back-end de estado

O back-end do Terraform estado é configurado durante a execução *Terraform init*. Para configurar o backend de estado, os seguintes dados são necessários.

- storage_account_name - O nome da conta do Armazenamento do Azure.
- container_name - O nome do contêiner de blob.
- Chave - O nome do arquivo de armazenamento de estado a ser criado.
- access_key - a chave de acesso de armazenamento.

Cada um desses valores pode ser especificado no arquivo de configuração do Terraform ou na linha de comando, no entanto, recomenda-se usar uma variável de ambiente para o `access_key`. Usar uma variável de ambiente impede que a chave seja gravada no disco.

Crie uma variável de ambiente denominada `ARM_ACCESS_KEY` com o valor da chave de acesso do Armazenamento do Azure.

```console
export ARM_ACCESS_KEY=<storage access key>
```

Para proteger ainda mais a chave de acesso da conta do Armazenamento do Azure, armazene-a no Cofre de Chaves do Azure. A variável de ambiente pode então ser definida usando um comando semelhante ao seguinte. Para obter mais informações sobre o Cofre de Chaves do Azure, consulte a [documentação do Azure Key Vault][azure-key-vault].

```console
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Para configurar o Terraform para usar o back-end, inclua uma *back-end* com um tipo de configuração do *azurerm* dentro da configuração de Terraform. Adicione os valores *storage_account_name*, *container_name* e *chave* ao bloco de configuração.

O exemplo a seguir configura um back-end e cria o Terraform e o grupo de recursos do Azure. Substitua os valores por valores do seu ambiente.

```json
terraform {
  backend "azurerm" {
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

Agora, inicialize a configuração com *Terraform init* e depois execute a configuração com *Terraform apply*. Quando concluído, você pode encontrar o arquivo de estado no Blob de armazenamento do Azure.

## <a name="state-locking"></a>Estado de bloqueio

Ao usar um Blob de Armazenamento do Azure para armazenamento de estado, o blob é bloqueado automaticamente antes de qualquer operação que grave o estado. Essa configuração impede várias operações de estado simultâneas, o que pode causar danos. Para obter mais informações, consulte [State Locking][terraform-state-lock] na documentação do Terraform.

O bloqueio pode ser visto ao examinar o blob no portal do Azure ou em outras ferramentas de gerenciamento do Azure.

![BLOBs do Azure com bloqueio](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Criptografia em repouso

Por padrão, os dados armazenados em um Blob do Azure são criptografados antes de serem persistidos na infraestrutura de armazenamento. Quando o Terraform precisa de estado, ele é recuperado do backend e armazenado na memória do sistema de desenvolvimento. Nesta configuração, o estado é protegido no Armazenamento do Azure e não gravado em seu disco local.

Para obter mais informações sobre a criptografia de armazenamento do Azure, consulte [criptografia do serviço de armazenamento do Azure para dados em repouso][azure-storage-encryption].

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a configuração de back-end do Terraform na [documentação de back-end do Terraform][terraform-backend].

<!-- LINKS - internal -->
[azure-key-vault]: ../key-vault/quick-create-cli.md
[azure-storage-encryption]: ../storage/common/storage-service-encryption.md

<!-- LINKS - external -->
[terraform-azurerm]: https://www.terraform.io/docs/backends/types/azurerm.html
[terraform-backend]: https://www.terraform.io/docs/backends/
[terraform-state-lock]: https://www.terraform.io/docs/state/locking.html
