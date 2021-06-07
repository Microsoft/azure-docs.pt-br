---
title: Elemento de interface do usuário KeyVaultCertificateSelector
description: Descreve o elemento de interface do usuário Microsoft. keyvault. KeyVaultCertificateSelector para portal do Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1ac6fd5a3f1e581fc9a676fe7f8f27b9742e1581
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93101214"
---
# <a name="microsoftkeyvaultkeyvaultcertificateselector-ui-element"></a>Elemento de interface do usuário Microsoft. keyvault. KeyVaultCertificateSelector

Um controle para selecionar um certificado de Key Vault.

## <a name="ui-sample"></a>Exemplo de interface do usuário

O usuário recebe a opção de selecionar um certificado disponível.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-select.png" alt-text="Microsoft. keyvault. KeyVaultCertificateSelector":::

Depois de selecionar **um certificado**, o usuário pode especificar um cofre de chaves e um certificado do cofre de chaves.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-certificate.png" alt-text="Certificado de seleção de Microsoft. keyvault. KeyVaultCertificateSelector":::

O controle é atualizado para exibir o cofre de chaves e o nome do certificado selecionados.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-result.png" alt-text="Microsoft. keyvault. KeyVaultCertificateSelector mostrar certificado selecionado":::

## <a name="schema"></a>Esquema

```json
{
  "name": "keyVaultCertificateSelection",
  "type": "Microsoft.KeyVault.KeyVaultCertificateSelector",
  "visible": true,
  "toolTip": "Select certificate",
  "label": "KeyVault certificates selection"
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
{
  "keyVaultName": "azuretestkeyvault1",
  "keyVaultId": "/subscriptions/{subscription-id}/resourceGroups/resourcegroup1/providers/Microsoft.KeyVault/vaults/azuretestkeyvault1",
  "certificateName": "certificate1",
  "certificateUrl": "https://azuretestkeyvault1.vault.azure.net/secrets/certificate1/{id}",
  "certificateThumbprint": "1B721E84DDDD1BB69282B4A54F18C6ADB1C174F2"
}
```

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
