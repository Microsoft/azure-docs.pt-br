---
title: Criptografar uma conta de armazenamento do Azure usada por um laboratório no Azure DevTest Labs
description: Saiba como configurar a criptografia de um armazenamento do Azure usado por um laboratório no Azure DevTest Labs
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: dcede89fb23c532742e41121688bcb51a5a73833
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92149305"
---
# <a name="encrypt-azure-storage-used-by-a-lab-in-azure-devtest-labs"></a>Criptografar o armazenamento do Azure usado por um laboratório no Azure DevTest Labs
Cada laboratório criado no Azure DevTest Labs é criado com uma conta de armazenamento do Azure associada. A conta de armazenamento é usada para as seguintes finalidades: 

- Armazenando documentos de [fórmula](devtest-lab-manage-formulas.md) que podem ser usados para criar máquinas virtuais.
- Armazenar resultados de artefatos que incluem logs de implantação e extensão gerados da aplicação de artefatos. 
- [Carregando VHDs (discos rígidos virtuais) para criar imagens personalizadas no laboratório.](devtest-lab-create-template.md)
- [Artefatos](add-artifact-vm.md) usados com frequência e [modelos de Azure Resource Manager](devtest-lab-create-environment-from-arm.md) para recuperação mais rápida durante a criação de máquina virtual/ambiente.

> [!NOTE]
> As informações acima são essenciais para que o laboratório opere. Ele é armazenado durante a vida útil do laboratório (e dos recursos do laboratório), a menos que seja explicitamente excluído. A exclusão manual desses recursos pode levar a erros na criação de VMs de laboratório e/ou fórmulas ficando corrompidas para uso futuro. 

## <a name="locate-the-storage-account-and-view-its-contents"></a>Localize a conta de armazenamento e exiba seu conteúdo

1. Na home page do laboratório, selecione o grupo de **recursos** na página **visão geral** . Você deve ver a página do **grupo de recursos** para o grupo de recursos que contém o laboratório. 

    :::image type="content" source="./media/encrypt-storage/overview-resource-group-link.png" alt-text="Selecione Grupo de recursos na página Visão geral":::
1. Selecione a conta de armazenamento do Azure do laboratório. A Convenção de nomenclatura para a conta de armazenamento do laboratório é: `a<labNameWithoutInvalidCharacters><4-digit number>` . Por exemplo, se o nome do laboratório for `contosolab` , o nome da conta de armazenamento poderá ser `acontosolab7576` . 

    :::image type="content" source="./media/encrypt-storage/select-storage-account.png" alt-text="Selecionar conta de armazenamento no grupo de recursos do laboratório":::
3. Na página **conta de armazenamento** , selecione **Gerenciador de armazenamento (versão prévia)** no menu à esquerda e, em seguida, selecione **contêineres de blob** para localizar conteúdo relevante relacionado ao laboratório. 

   :::image type="content" source="./media/encrypt-storage/storage-explorer.png" alt-text="Gerenciador de Armazenamento (Preview)" lightbox="./media/encrypt-storage/storage-explorer.png":::

## <a name="encrypt-the-lab-storage-account"></a>Criptografar a conta de armazenamento do laboratório
O armazenamento do Azure criptografa automaticamente os dados quando eles são persistidos na nuvem. A criptografia de armazenamento do Azure protege seus dados e ajuda você a atender aos compromissos de conformidade e segurança da organização. Para obter mais informações, consulte [Criptografia do Armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md).

Os dados na conta de armazenamento do laboratório são criptografados com uma **chave gerenciada pela Microsoft**. Você pode contar com chaves gerenciadas pela Microsoft para a criptografia de seus dados ou pode gerenciar a criptografia com suas próprias chaves. Se você optar por gerenciar a criptografia com suas próprias chaves para a conta de armazenamento do laboratório, poderá especificar uma **chave gerenciada pelo cliente** com Azure Key Vault a ser usada para criptografar/descriptografar dados no armazenamento de BLOBs e nos arquivos do Azure. Para obter mais informações sobre chaves gerenciadas pelo cliente, consulte [usar chaves gerenciadas pelo cliente com Azure Key Vault para gerenciar a criptografia de armazenamento do Azure](../storage/common/customer-managed-keys-overview.md).

Para saber como configurar chaves gerenciadas pelo cliente para a criptografia de armazenamento do Azure, consulte os seguintes artigos: 

- [Azure portal](../storage/common/customer-managed-keys-configure-key-vault.md)
- [PowerShell do Azure](../storage/common/customer-managed-keys-configure-key-vault.md)
- [CLI do Azure](../storage/common/customer-managed-keys-configure-key-vault.md)


## <a name="manage-the-azure-blob-storage-life-cycle"></a>Gerenciar o ciclo de vida do armazenamento de BLOBs do Azure
Conforme mencionado, as informações armazenadas na conta de armazenamento do laboratório são essenciais para que o laboratório opere sem erros. A menos que sejam excluídos explicitamente, esses dados continuarão a permanecer na conta de armazenamento do laboratório durante a vida útil do laboratório ou da vida útil de máquinas virtuais de laboratório específicas, dependendo do tipo de dados.

### <a name="uploaded-vhds"></a>VHDs carregados
Esses VHDs são usados para criar imagens personalizadas. Removê-los fará com que não seja mais possível criar imagens personalizadas a partir desses VHDs.

### <a name="artifacts-cache"></a>Cache de artefatos
Esses caches serão recriados quando os artefatos forem aplicados. Eles serão atualizados com o conteúdo mais recente dos respectivos repositórios referenciados. Portanto, se você excluir essas informações para salvar despesas relacionadas ao armazenamento, o alívio será temporário.

### <a name="azure-resource-manager-template-cache"></a>Cache de modelo de Azure Resource Manager
Esses caches serão recriados sempre que os repositórios de modelo baseados em Azure Resource Manager estiverem conectados e girados no laboratório. Eles serão atualizados com o conteúdo mais recente dos respectivos repositórios referenciados. Portanto, se você excluir essas informações para salvar despesas relacionadas ao armazenamento, o alívio será temporário.

### <a name="formulas"></a>Fórmulas
Esses documentos são usados para oferecer suporte à opção para criar fórmulas de VMs existentes e para criar VMs a partir de fórmulas. A exclusão desses documentos de fórmula pode levar a erros durante as seguintes operações:

- Criando uma fórmula de uma VM de laboratório existente
- Criando ou atualizando fórmulas 
- Criando uma VM com base em uma fórmula.

### <a name="artifact-results"></a>Resultados do artefato
Conforme os artefatos são aplicados, o tamanho dos respectivos resultados de artefato pode aumentar ao longo do tempo, dependendo do número e do tipo de artefatos sendo executados em VMs de laboratório. Portanto, como proprietário de um laboratório, convém controlar o ciclo de vida desses documentos. Para obter mais informações, confira [Gerenciar o ciclo de vida do armazenamento de Blobs do Azure](../storage/blobs/storage-lifecycle-management-concepts.md).

> [!IMPORTANT]
> Recomendamos que você faça essa etapa para reduzir as despesas associadas à conta de armazenamento do Azure. 

Por exemplo, a regra a seguir é usada para definir uma regra de expiração de 90 dias especificamente para resultados de artefato. Ele garante que os resultados mais antigos do artefato sejam reciclados da conta de armazenamento em uma cadência regular.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "artifacts/results" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 90 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas
Para saber como configurar chaves gerenciadas pelo cliente para a criptografia de armazenamento do Azure, consulte os seguintes artigos: 

- [Azure portal](../storage/common/customer-managed-keys-configure-key-vault.md)
- [PowerShell do Azure](../storage/common/customer-managed-keys-configure-key-vault.md)
- [CLI do Azure](../storage/common/customer-managed-keys-configure-key-vault.md)