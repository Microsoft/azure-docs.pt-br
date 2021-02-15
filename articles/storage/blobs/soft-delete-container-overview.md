---
title: Exclusão reversível para contêineres (visualização)
titleSuffix: Azure Storage
description: A exclusão reversível para contêineres (visualização) protege seus dados para que você possa recuperar seus dados com mais facilidade quando eles forem modificados ou excluídos erroneamente por um aplicativo ou por outro usuário da conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 674a336e79f118d543590fb7514b6bebef72cf47
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390174"
---
# <a name="soft-delete-for-containers-preview"></a>Exclusão reversível para contêineres (visualização)

A exclusão reversível para contêineres (versão prévia) protege seus dados contra exclusão acidental ou maliciosa. Quando a exclusão reversível de contêiner está habilitada para uma conta de armazenamento, qualquer contêiner excluído e seu conteúdo são retidos no armazenamento do Azure para o período especificado. Durante o período de retenção, você pode restaurar os contêineres excluídos anteriormente. A restauração de um contêiner restaura todos os BLOBs dentro desse contêiner quando ele foi excluído.

Para proteção de ponta a ponta para seus dados de BLOB, a Microsoft recomenda habilitar os seguintes recursos de proteção de dados:

- Exclusão reversível de contêiner, para restaurar um contêiner que foi excluído. Para saber como habilitar a exclusão reversível de contêiner, consulte [habilitar e gerenciar a exclusão reversível para contêineres](soft-delete-container-enable.md).
- Controle de versão de BLOB, para manter automaticamente as versões anteriores de um blob. Quando o controle de versão de blob estiver habilitado, você poderá restaurar uma versão anterior de um blob para recuperar seus dados se eles forem modificados ou excluídos erroneamente. Para saber como habilitar o controle de versão de BLOB, consulte [habilitar e gerenciar o controle de versão de blob](versioning-enable.md).
- Exclusão reversível de BLOB, para restaurar um BLOB ou uma versão que foi excluída. Para saber como habilitar a exclusão reversível de BLOB, consulte [habilitar e gerenciar a exclusão reversível para BLOBs](soft-delete-blob-enable.md).

> [!WARNING]
> Não é possível desfazer a exclusão de uma conta de armazenamento. A exclusão reversível de contêiner não protege contra a exclusão de uma conta de armazenamento, mas somente contra a exclusão de contêineres nessa conta. Para proteger uma conta de armazenamento da exclusão, configure um bloqueio no recurso de conta de armazenamento. Para obter mais informações sobre como bloquear Azure Resource Manager recursos, consulte [Bloquear recursos para evitar alterações inesperadas](../../azure-resource-manager/management/lock-resources.md).

## <a name="how-container-soft-delete-works"></a>Como funciona a exclusão reversível de contêiner

Ao habilitar a exclusão reversível de contêiner, você pode especificar um período de retenção para contêineres excluídos entre 1 e 365 dias. O período de retenção padrão é de 7 dias. Durante o período de retenção, você pode recuperar um contêiner excluído chamando a operação **restaurar contêiner** .

Quando você restaura um contêiner, os BLOBs do contêiner e as versões de blob também são restaurados. No entanto, você só poderá usar a exclusão reversível de contêiner para restaurar BLOBs se o próprio contêiner tiver sido excluído. Para restaurar um blob excluído quando seu contêiner pai não foi excluído, você deve usar a exclusão reversível de BLOB ou o controle de versão de BLOB.

O diagrama a seguir mostra como um contêiner excluído pode ser restaurado quando a exclusão reversível do contêiner está habilitada:

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="Diagrama mostrando como um contêiner excluído por software pode ser restaurado":::

Ao restaurar um contêiner, você poderá restaurá-lo para seu nome original se esse nome não tiver sido reutilizado. Se o nome do contêiner original tiver sido usado, você poderá restaurar o contêiner com um novo nome.

Depois que o período de retenção tiver expirado, o contêiner será excluído permanentemente do armazenamento do Azure e não poderá ser recuperado. O relógio começa no período de retenção no ponto em que o contêiner é excluído. Você pode alterar o período de retenção a qualquer momento, mas tenha em mente que um período de retenção atualizado se aplica somente a contêineres excluídos recentemente. Os contêineres excluídos anteriormente serão excluídos permanentemente com base no período de retenção que estava em vigor no momento em que o contêiner foi excluído.

Desabilitar a exclusão reversível de contêiner não resulta na exclusão permanente de contêineres que foram excluídos anteriormente. Todos os contêineres excluídos por software serão excluídos permanentemente na expiração do período de retenção que estava em vigor no momento em que o contêiner foi excluído.

## <a name="about-the-preview"></a>Sobre a visualização

A exclusão reversível de contêiner está disponível na visualização em todas as regiões do Azure.

> [!IMPORTANT]
> A visualização de exclusão reversível de contêiner é destinada somente ao uso de não produção. SLAs (Contratos de Nível de Serviço) não estão disponíveis atualmente.

A versão 2019-12-12 e superior da API REST do armazenamento do Azure dá suporte à exclusão reversível de contêiner.

### <a name="storage-account-support"></a>Suporte da conta de armazenamento

A exclusão reversível de contêiner está disponível para os seguintes tipos de contas de armazenamento:

- Contas de armazenamento de uso geral V2 e v1
- Bloquear contas de armazenamento de BLOBs
- Contas de armazenamento de Blobs

Também há suporte para contas de armazenamento com um namespace hierárquico habilitado para uso com Azure Data Lake Storage Gen2.

### <a name="register-for-the-preview"></a>Registre-se para a versão prévia

Para se registrar na visualização para exclusão reversível de contêiner, use o PowerShell ou CLI do Azure para enviar uma solicitação para registrar o recurso com sua assinatura. Depois que a solicitação for aprovada, você poderá habilitar a exclusão reversível de contêiner com qualquer conta de armazenamento de BLOBs v2, de armazenamento de BLOB ou de uso geral nova ou existente.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para se registrar no PowerShell, chame o comando [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para se registrar com CLI do Azure, chame o comando [AZ Feature Register](/cli/azure/feature#az-feature-register) .

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>Verificar o status do seu registro

Para verificar o status do seu registro, use o PowerShell ou CLI do Azure.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para verificar o status do seu registro com o PowerShell, chame o comando [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para verificar o status do seu registro com CLI do Azure, chame o comando [AZ Feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>Preços e cobrança

Não há nenhum custo adicional para habilitar a exclusão reversível de contêiner. Os dados em contêineres com exclusão reversível são cobrados com a mesma taxa que os dados ativos.

## <a name="next-steps"></a>Próximas etapas

- [Configurar exclusão reversível de contêiner](soft-delete-container-enable.md)
- [Exclusão reversível para blobs](soft-delete-blob-overview.md)
- [Controle de versão de BLOB](versioning-overview.md)
