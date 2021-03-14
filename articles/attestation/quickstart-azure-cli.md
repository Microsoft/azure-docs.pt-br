---
title: Configurar o Atestado do Azure com a CLI do Azure
description: Como configurar um provedor de atestado usando a CLI do Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: ae283785b4d4dc80c6b9b6c3997aaf82c9ff0f2f
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178704"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Início Rápido: Configurar o Atestado do Azure com a CLI do Azure

Introdução ao [Atestado do Azure usando a CLI do Azure](/cli/azure/ext/attestation/attestation).

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="get-started"></a>Introdução

1. Instalar esta extensão usando o comando da CLI abaixo

   ```azurecli
   az extension add --name attestation
   ```
   
1. Verificar a versão

   ```azurecli
   az extension show --name attestation --query version
   ```

1. Use o seguinte comando para entrar no Azure:

   ```azurecli
   az login
   ```

1. Se necessário, alterne para a assinatura do Atestado do Azure:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Registre o provedor de recursos Microsoft.Attestation na assinatura com o comando [az provider register](/cli/azure/provider#az_provider_register):

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   Para obter mais informações sobre os provedores de recursos do Azure e como configurá-los e gerenciá-los, confira [Tipos e provedores de recursos do Azure](../azure-resource-manager/management/resource-providers-and-types.md).

   > [!NOTE]
   > Você só precisa registrar um provedor de recursos uma vez para uma assinatura.

1. Crie um grupo de recursos para o provedor de atestado. Coloque outros recursos do Azure no mesmo grupo de recursos, incluindo uma máquina virtual com uma instância do aplicativo cliente. Execute o comando [az group create](/cli/azure/group#az_group_create) para criar um grupo de recursos ou use um grupo de recursos existente:

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>Criar e gerenciar um provedor de atestado

Estes são os comandos que você pode usar para criar e gerenciar o provedor de atestado:

1. Execute o comando [az attestation create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) para criar um provedor de atestado sem o requisito de assinatura de política:

   ```azurecli
   az attestation create --name "myattestationprovider" --resource-group "MyResourceGroup" --location westus
   ```
   
1. Execute o comando [az attestation show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) para recuperar as propriedades do provedor de atestado, como status e AttestURI:

   ```azurecli
   az attestation show --name "myattestationprovider" --resource-group "MyResourceGroup"
   ```

   Esse comando exibe valores como a seguinte saída:

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

Exclua um provedor de atestado usando o comando [az attestation delete](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete):

```azurecli
az attestation delete --name "myattestationprovider" --resource-group "sample-resource-group"
```

## <a name="policy-management"></a>Gerenciamento de política

Use os comandos descritos aqui para fornecer o gerenciamento de política para um provedor de atestado, um tipo de atestado de cada vez.

O comando [az attestation policy show](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) retorna a política atual para o TEE especificado:

```azurecli
az attestation policy show --name "myattestationprovider" --resource-group "MyResourceGroup" --attestation-type SGX-IntelSDK
```

> [!NOTE]
> O comando exibe a política no formato de texto e JWT.

Estes são os tipos de TEE compatíveis:

- `SGX-IntelSDK`
- `SGX-OpenEnclaveSDK`
- `TPM`

Use o comando [az attestation policy set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) para definir uma nova política para o tipo de atestado especificado.

Para definir a política no formato de texto para determinado tipo de atestado usando o caminho do arquivo:

```azurecli
az attestation policy set --name testatt1 --resource-group testrg --attestation-type SGX-IntelSDK --new-attestation-policy-file "{file_path}"
```

Para definir a política no formato JWT para determinado tipo de atestado usando o caminho do arquivo:

```azurecli
az attestation policy set --name "myattestationprovider" --resource-group "MyResourceGroup" \
--attestation-type SGX-IntelSDK -f "{file_path}" --policy-format JWT
```

## <a name="next-steps"></a>Próximas etapas

- [Como criar e assinar uma política de atestado](author-sign-policy.md)
- [Implementar o atestado com um enclave do SGX usando exemplos de código](/samples/browse/?expanded=azure&terms=attestation)
