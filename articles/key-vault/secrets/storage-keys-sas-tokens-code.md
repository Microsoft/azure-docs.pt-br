---
title: Buscar tokens de Assinatura de Acesso Compartilhado no código | Azure Key Vault
description: O recurso de conta de armazenamento gerenciada fornece uma integração perfeita entre o Azure Key Vault e uma conta de armazenamento do Azure. Este exemplo usa o SDK do Azure para .NET para gerenciar tokens SAS.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 8c8a7b357efc2e30d1ca93d0b45fd2563c980045
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752742"
---
# <a name="create-sas-definition-and-fetch-shared-access-signature-tokens-in-code"></a>Criar definição SAS e buscar tokens de assinatura de acesso compartilhado no código

Você pode gerenciar sua conta de armazenamento com tokens SAS (Assinatura de Acesso Compartilhado) armazenados no cofre de chaves. Para obter mais informações, confira [Permitir acesso limitado aos recursos do Armazenamento do Azure usando a SAS](../../storage/common/storage-sas-overview.md).

> [!NOTE]
> É recomendável usar o [Azure RBAC (controle de acesso baseado em função)](../../storage/common/storage-auth-aad.md) para proteger a sua conta de armazenamento com segurança superior e com facilidade de uso da autorização de Chave Compartilhada.

Este artigo fornece exemplos de código .NET que cria uma definição de SAS e busca tokens SAS. Confira nosso exemplo de [ShareLink](/samples/azure/azure-sdk-for-net/share-link/) para obter detalhes completos, incluindo o cliente gerado para contas de armazenamento gerenciadas pelo Key Vault. Para obter informações sobre como criar e armazenar tokens SAS, confira [Gerenciar chaves de conta de armazenamento com o Key Vault e a CLI do Azure](overview-storage-keys.md) ou [Gerenciar chaves de conta de armazenamento com o Key Vault e o Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Exemplos de código

No seguinte exemplo, criaremos um modelo SAS:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="91-97":::

Usando esse modelo, podemos criar uma definição de SAS usando o 

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="137-156":::

Depois que a definição de SAS for criada, você poderá recuperar os tokens SAS como segredos usando um `SecretClient`. O nome do segredo precisa ser precedido pelo nome da conta de armazenamento seguido por um traço:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="52-58":::

Se o token de assinatura de acesso compartilhado estiver prestes a expirar, você poderá buscar o mesmo segredo novamente para gerar um novo.

Para ver um guia sobre como usar um token SAS recuperado do Key Vault para acessar os serviços do Armazenamento do Azure, confira [Usar uma SAS de conta para acessar o serviço Blob](../../storage/common/storage-account-sas-create-dotnet.md#use-an-account-sas-from-a-client)

> [!NOTE]
> Seu aplicativo precisará estar preparado para atualizar a SAS se receber um erro 403 do Armazenamento para você poder lidar com o caso em que uma chave ficou comprometida. Você precisará fazer a rotação delas mais rápido do que o período de rotação normal. 

## <a name="next-steps"></a>Próximas etapas
- Saiba como [Permitir acesso limitado aos recursos de armazenamento do Azure usando a SAS](../../storage/common/storage-sas-overview.md).
- Saiba como [Gerenciar chaves de conta de armazenamento com o Key Vault e a CLI do Azure](overview-storage-keys.md) ou o [Azure PowerShell](overview-storage-keys-powershell.md).
- Confira [Exemplos de chaves de conta de armazenamento gerenciadas](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)