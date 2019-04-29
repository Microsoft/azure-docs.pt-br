---
title: Criptografia de armazenamento do Azure para dados em repouso | Microsoft Docs
description: O armazenamento do Azure protege seus dados criptografando-os automaticamente antes persisti-los para a nuvem. Todos os dados no armazenamento do Azure, em blobs, discos, arquivos, filas ou tabelas, são criptografados e descriptografados de maneira transparente usando criptografia AES de 256 bits e é compatível com o FIPS 140-2.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 211cfeb3aba29245e154f4a7db86fb4a3659c36f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730814"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Criptografia de armazenamento do Azure para dados em repouso

O armazenamento do Azure criptografa automaticamente seus dados quando persisti-los para a nuvem. A criptografia protege seus dados e para ajudar você a atender aos seus compromissos de conformidade e segurança organizacionais. Dados no armazenamento do Azure são criptografados e descriptografados de maneira transparente usando 256 bits [a criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), um dos codificadores de blocos potentes e é compatível com o FIPS 140-2. Criptografia de armazenamento do Azure é semelhante a criptografia do BitLocker no Windows.

Criptografia de armazenamento do Azure está habilitada para todas as contas de armazenamento novas e existentes e não pode ser desabilitada. Porque seus dados são protegidos por padrão, você não precisa modificar seu código ou aplicativos para tirar proveito da criptografia de armazenamento do Azure. Contas de armazenamento são criptografadas independentemente do seu nível de desempenho (standard ou premium) ou o modelo de implantação (Azure Resource Manager ou clássico). Todas as opções de redundância de armazenamento do Azure dá suporte à criptografia, e todas as cópias de uma conta de armazenamento são criptografadas. Todos os recursos de armazenamento do Azure são criptografados, incluindo blobs, discos, arquivos, filas e tabelas.

A criptografia não afeta o desempenho do armazenamento do Azure. Não há nenhum custo adicional para a criptografia de armazenamento do Azure.

Para obter mais informações sobre os módulos criptográficos subjacente de criptografia de armazenamento do Azure, consulte [Cryptography API: Próxima Geração](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="key-management"></a>Gerenciamento de chaves

Você pode contar com chaves gerenciadas pela Microsoft para a criptografia de sua conta de armazenamento, ou você pode gerenciar a criptografia com suas próprias chaves, junto com o Azure Key Vault.

### <a name="microsoft-managed-keys"></a>Chaves gerenciadas pela Microsoft

Por padrão, sua conta de armazenamento usa chaves de criptografia gerenciadas pela Microsoft. Você pode ver as configurações de criptografia para sua conta de armazenamento do **criptografia** seção o [portal do Azure](https://portal.azure.com), conforme mostrado na imagem a seguir.

![Exibir conta criptografada com chaves gerenciadas pela Microsoft](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>Chaves gerenciadas pelo cliente

Você pode gerenciar a criptografia de armazenamento do Azure com chaves gerenciadas pelo cliente. Chaves gerenciadas pelo cliente oferecem mais flexibilidade para criar, girar, desabilitar e revogar os controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados. 

Use o Azure Key Vault para gerenciar suas chaves e auditar o uso. Você pode criar suas próprias chaves e armazená-los em um cofre de chaves, ou você pode usar as APIs do Azure Key Vault para gerar chaves. A conta de armazenamento e o cofre de chaves devem estar na mesma região, mas podem estar em assinaturas diferentes. Para obter mais informações sobre o Azure Key Vault, consulte [o que é o Azure Key Vault?](../../key-vault/key-vault-overview.md).

Para revogar o acesso a chaves gerenciadas pelo cliente, consulte [PowerShell do Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) e [CLI do Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). Revogando o acesso efetivamente bloqueia o acesso a todos os dados na conta de armazenamento, pois a chave de criptografia não é acessível pelo armazenamento do Azure.

Para saber como usar chaves gerenciadas pelo cliente com o armazenamento do Azure, consulte um dos seguintes artigos:

- [Configurar chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure do portal do Azure](storage-encryption-keys-portal.md)
- [Configurar chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure do PowerShell](storage-encryption-keys-powershell.md)
- [Usar chaves gerenciadas pelo cliente com a criptografia de armazenamento do Azure da CLI do Azure](storage-encryption-keys-cli.md)

> [!NOTE]  
> Não há suporte para chaves gerenciadas pelo cliente para [discos gerenciados do Azure](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Criptografia de armazenamento do Azure em comparação com a criptografia de disco

Com a criptografia de armazenamento do Azure, todas as contas de armazenamento do Azure e os recursos que eles contêm são criptografados, incluindo os blobs de páginas que dão suporte a discos de máquina virtual do Azure. Além disso, os discos de máquina virtual do Azure podem ser criptografados com [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). O Azure Disk Encryption usa o padrão da indústria [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) no Windows e [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) no Linux para fornecer soluções de criptografia com base no sistema operacional que são integradas ao Azure Key Vault.

## <a name="next-steps"></a>Próximas etapas

- [O que é o Cofre da Chave do Azure?](../../key-vault/key-vault-overview.md)
