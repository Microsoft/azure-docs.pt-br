---
title: Habilitar a criptografia dupla em discos gerenciados por CLI do Azure em repouso
description: Habilite a criptografia dupla em repouso para os dados do disco gerenciado usando o CLI do Azure.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 7fabf5afa60590e92f7c0ca10197c436c1c0b8a0
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2020
ms.locfileid: "97702252"
---
# <a name="use-the-azure-cli-to-enable-double-encryption-at-rest-for-managed-disks"></a>Usar o CLI do Azure para habilitar a criptografia dupla em repouso para discos gerenciados

O Armazenamento em Disco do Azure dá suporte à criptografia dupla em repouso para discos gerenciados. Para obter informações conceituais sobre a criptografia dupla em repouso, bem como outros tipos de criptografia de disco gerenciado, consulte a seção [criptografia dupla em repouso](../disk-encryption.md#double-encryption-at-rest) do nosso artigo sobre criptografia de disco.

## <a name="prerequisites"></a>Pré-requisitos

Instale o [CLI do Azure](/cli/azure/install-az-cli2) mais recente e faça logon em uma conta do Azure com [AZ login](/cli/azure/reference-index).

## <a name="getting-started"></a>Introdução

1. Crie uma instância do Azure Key Vault e a chave de criptografia.

    Ao criar a instância do Key Vault, habilite a exclusão temporária e a proteção de limpeza. A exclusão temporária garante que a Key Vault mantenha uma chave excluída para determinado período de retenção (padrão de 90 dias). A proteção de limpeza garante que uma chave excluída não seja excluída permanentemente até que o período de retenção termine. Essas configurações protegem você contra a perda de dados devido à exclusão acidental. Essas configurações são obrigatórias ao usar um Key Vault para criptografar discos gerenciados.

    
    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    Crie um DiskEncryptionSet com EncryptionType definido como EncryptionAtRestWithPlatformAndCustomerKeys. Use a versão de API **2020-05-01** no modelo de Azure Resource Manager (ARM). 
    
        ```azurecli
        az deployment group create -g $rgName \
       --template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" \
        --parameters "diskEncryptionSetName=$diskEncryptionSetName" "encryptionType=EncryptionAtRestWithPlatformAndCustomerKeys" "keyVaultId=$keyVaultId" "keyVaultKeyUrl=$keyVaultKeyUrl" "region=$location"
        ```

1.    Conceda o acesso ao recurso DiskEncryptionSet para o Key Vault. 

        > [!NOTE]
        > Pode levar alguns minutos para o Azure criar a identidade do DiskEncryptionSet no Azure Active Directory. Se você receber um erro como "não é possível encontrar o objeto Active Directory" ao executar o comando a seguir, aguarde alguns minutos e tente novamente.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)

        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```

## <a name="next-steps"></a>Próximas etapas

Agora que você criou e configurou esses recursos, você pode usá-los para proteger seus discos gerenciados. Os links a seguir contêm scripts de exemplo, cada um com um cenário respectivo, que você pode usar para proteger seus discos gerenciados.

- [Exemplos de modelo do Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [Habilitar chaves gerenciadas pelo cliente com criptografia do lado do servidor-exemplos](disks-enable-customer-managed-keys-cli.md#examples)
