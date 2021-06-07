---
title: Chaves gerenciadas pelo cliente para criptografia de conta
titleSuffix: Azure Storage
description: Você pode usar sua própria chave de criptografia para proteger os dados em sua conta de armazenamento. Quando você especifica uma chave gerenciada pelo cliente, essa chave é usada para proteger e controlar o acesso à chave que criptografa os dados. Chaves gerenciadas pelo cliente oferecem maior flexibilidade para gerenciar controles de acesso.
services: storage
author: tamram
ms.service: storage
ms.date: 03/23/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 72f7130aad12ee2b5d463195b0b5c7847022bdfd
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645166"
---
# <a name="customer-managed-keys-for-azure-storage-encryption"></a>Chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure

Você pode usar sua própria chave de criptografia para proteger os dados em sua conta de armazenamento. Quando você especifica uma chave gerenciada pelo cliente, essa chave é usada para proteger e controlar o acesso à chave que criptografa os dados. Chaves gerenciadas pelo cliente oferecem maior flexibilidade para gerenciar controles de acesso.

Você deve usar Azure Key Vault ou Azure Key Vault o HSM (módulo de segurança de hardware) gerenciado (versão prévia) para armazenar as chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las no cofre de chaves ou HSM gerenciado, ou pode usar as APIs de Azure Key Vault para gerar chaves. A conta de armazenamento e o cofre de chaves ou HSM gerenciado devem estar na mesma região e no mesmo locatário Azure Active Directory (Azure AD), mas podem estar em assinaturas diferentes.

Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault?](../../key-vault/general/overview.md).

> [!IMPORTANT]
>
> A criptografia com chaves gerenciadas pelo cliente armazenadas em Azure Key Vault HSM gerenciado está atualmente em versão **prévia**. Consulte os [termos de uso complementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.
>
> Azure Key Vault e Azure Key Vault o HSM gerenciado oferece suporte às mesmas APIs e interfaces de gerenciamento para configuração.

## <a name="about-customer-managed-keys"></a>Sobre as chaves gerenciadas pelo cliente

O diagrama a seguir mostra como o armazenamento do Azure usa Azure Active Directory e um cofre de chaves ou HSM gerenciado para fazer solicitações usando a chave gerenciada pelo cliente:

![Diagrama mostrando como as chaves gerenciadas pelo cliente funcionam no armazenamento do Azure](media/customer-managed-keys-overview/encryption-customer-managed-keys-diagram.png)

A seguinte lista explica as etapas enumeradas no diagrama:

1. Um administrador de Azure Key Vault concede permissões a chaves de criptografia para a identidade gerenciada associada à conta de armazenamento.
2. Um administrador de armazenamento do Azure configura a criptografia com uma chave gerenciada pelo cliente para a conta de armazenamento.
3. O armazenamento do Azure usa a identidade gerenciada associada à conta de armazenamento para autenticar o acesso a Azure Key Vault por meio de Azure Active Directory.
4. O armazenamento do Azure encapsula a chave de criptografia da conta com a chave do cliente em Azure Key Vault.
5. Para operações de leitura/gravação, o armazenamento do Azure envia solicitações para Azure Key Vault para desencapsular a chave de criptografia da conta para executar operações de criptografia e descriptografia.

## <a name="customer-managed-keys-for-queues-and-tables"></a>Chaves gerenciadas pelo cliente para filas e tabelas

Os dados armazenados na fila e no armazenamento de tabelas não são automaticamente protegidos por uma chave gerenciada pelo cliente quando as chaves gerenciadas pelo cliente são habilitadas para a conta de armazenamento. Opcionalmente, você pode configurar esses serviços para serem incluídos nessa proteção no momento em que você criar a conta de armazenamento.

Para obter mais informações sobre como criar uma conta de armazenamento que dá suporte a chaves gerenciadas pelo cliente para filas e tabelas, consulte [criar uma conta que dê suporte a chaves gerenciadas pelo cliente para tabelas e filas](account-encryption-key-create.md).

Os dados no armazenamento de BLOBs e arquivos do Azure são sempre protegidos por chaves gerenciadas pelo cliente quando as chaves gerenciadas pelo cliente são configuradas para a conta de armazenamento.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Habilitar chaves gerenciadas pelo cliente para uma conta de armazenamento

Quando você configura uma chave gerenciada pelo cliente, o armazenamento do Azure encapsula a chave de criptografia de dados raiz para a conta com a chave gerenciada pelo cliente no cofre de chaves associado ou HSM gerenciado. A habilitação de chaves gerenciadas pelo cliente não afeta o desempenho e entra em vigor imediatamente.

Quando você habilita ou desabilita as chaves gerenciadas pelo cliente, ou quando você modifica a chave ou a versão da chave, a proteção da chave de criptografia raiz é alterada, mas os dados em sua conta de armazenamento do Azure não precisam ser criptografados novamente.

As chaves gerenciadas pelo cliente podem ser habilitadas somente em contas de armazenamento existentes. O cofre de chaves ou HSM gerenciado deve ser configurado para conceder permissões para a identidade gerenciada associada à conta de armazenamento. A identidade gerenciada estará disponível somente depois que a conta de armazenamento for criada.

Você pode alternar entre chaves gerenciadas pelo cliente e chaves gerenciadas pela Microsoft a qualquer momento. Para obter mais informações sobre chaves gerenciadas pela Microsoft, consulte [sobre gerenciamento de chaves de criptografia](storage-service-encryption.md#about-encryption-key-management).

Para saber como configurar a criptografia de armazenamento do Azure com chaves gerenciadas pelo cliente em um cofre de chaves, consulte [Configurar a criptografia com chaves gerenciadas pelo cliente armazenadas em Azure Key Vault](customer-managed-keys-configure-key-vault.md). Para configurar chaves gerenciadas pelo cliente em um HSM gerenciado, consulte [Configurar a criptografia com chaves gerenciadas pelo cliente armazenadas em Azure Key Vault HSM gerenciado (versão prévia)](customer-managed-keys-configure-key-vault-hsm.md).

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente dependem de identidades gerenciadas para recursos do Azure, um recurso do Azure AD. Identidades gerenciadas não têm suporte a cenários entre diretórios. Quando você configura chaves gerenciadas pelo cliente no portal do Azure, uma identidade gerenciada é automaticamente atribuída à sua conta de armazenamento nos bastidores. Se, posteriormente, você mover a assinatura, o grupo de recursos ou a conta de armazenamento de um diretório do Azure AD para outro, a identidade gerenciada associada à conta de armazenamento não será transferida para o novo locatário, portanto, as chaves gerenciadas pelo cliente poderão deixar de funcionar. Para obter mais informações, consulte **transferindo uma assinatura entre diretórios do Azure ad** em [perguntas frequentes e problemas conhecidos com identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

A criptografia de armazenamento do Azure dá suporte às chaves RSA e RSA-HSM de tamanhos 2048, 3072 e 4096. Para obter mais informações sobre chaves, consulte [sobre chaves](../../key-vault/keys/about-keys.md).

O uso de um cofre de chaves ou HSM gerenciado tem custos associados. Para obter mais informações, consulte [preços de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="update-the-key-version"></a>Atualizar a versão de chave

Ao configurar a criptografia com chaves gerenciadas pelo cliente, você tem duas opções para atualizar a versão da chave:

- **Atualizar automaticamente a versão de chave:** Para atualizar automaticamente uma chave gerenciada pelo cliente quando uma nova versão estiver disponível, omita a versão da chave ao habilitar a criptografia com chaves gerenciadas pelo cliente para a conta de armazenamento. Se a versão da chave for omitida, o armazenamento do Azure verificará o cofre de chaves ou o HSM gerenciado diariamente para obter uma nova versão de uma chave gerenciada pelo cliente. O armazenamento do Azure usa automaticamente a versão mais recente da chave.
- **Atualizar manualmente a versão da chave:** Para usar uma versão específica de uma chave para a criptografia de armazenamento do Azure, especifique essa versão de chave ao habilitar a criptografia com chaves gerenciadas pelo cliente para a conta de armazenamento. Se você especificar a versão de chave, o armazenamento do Azure usará essa versão para criptografia até que você atualize manualmente a versão de chave.

    Quando a versão de chave é especificada explicitamente, você deve atualizar manualmente a conta de armazenamento para usar o novo URI de versão de chave quando uma nova versão é criada. Para saber como atualizar a conta de armazenamento para usar uma nova versão da chave, consulte [Configurar a criptografia com chaves gerenciadas pelo cliente armazenadas em Azure Key Vault](customer-managed-keys-configure-key-vault.md) ou [Configurar a criptografia com chaves gerenciadas pelo cliente armazenadas em Azure Key Vault HSM gerenciado (versão prévia)](customer-managed-keys-configure-key-vault-hsm.md).

Quando você atualiza a versão da chave, a proteção da chave de criptografia raiz é alterada, mas os dados em sua conta de armazenamento do Azure não são criptografados novamente. Não há nenhuma ação adicional necessária do usuário.

> [!NOTE]
> Para girar uma chave, crie uma nova versão da chave no cofre de chaves ou HSM gerenciado, de acordo com suas políticas de conformidade. Você pode girar a chave manualmente ou criar uma função para girá-la em um agendamento.

## <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso a chaves gerenciadas pelo cliente

Você pode revogar o acesso da conta de armazenamento para a chave gerenciada pelo cliente a qualquer momento. Depois que o acesso às chaves gerenciadas pelo cliente for revogado ou depois que a chave tiver sido desabilitada ou excluída, os clientes não poderão chamar operações que lidam ou gravam em um BLOB ou em seus metadados. As tentativas de chamar qualquer uma das seguintes operações falharão com o código de erro 403 (proibido) para todos os usuários:

- [Listar BLOBs](/rest/api/storageservices/list-blobs)quando chamado com o `include=metadata` parâmetro no URI de solicitação
- [Get Blob](/rest/api/storageservices/get-blob)
- [Obter propriedades do blob](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Blob de instantâneo](/rest/api/storageservices/snapshot-blob), quando chamado com o `x-ms-meta-name` cabeçalho de solicitação
- [Copiar blob](/rest/api/storageservices/copy-blob)
- [Copiar BLOB da URL](/rest/api/storageservices/copy-blob-from-url)
- [Definir camada do blob](/rest/api/storageservices/set-blob-tier)
- [Put Block](/rest/api/storageservices/put-block)
- [Colocar bloco da URL](/rest/api/storageservices/put-block-from-url)
- [Acrescentar Bloco](/rest/api/storageservices/append-block)
- [Anexar bloco da URL](/rest/api/storageservices/append-block-from-url)
- [Put Blob](/rest/api/storageservices/put-blob)
- [Colocar Página](/rest/api/storageservices/put-page)
- [Colocar página da URL](/rest/api/storageservices/put-page-from-url)
- [Blob de Cópia Incremental](/rest/api/storageservices/incremental-copy-blob)

Para chamar essas operações novamente, restaure o acesso à chave gerenciada pelo cliente.

Todas as operações de dados que não estão listadas nesta seção podem continuar depois que as chaves gerenciadas pelo cliente são revogadas ou uma chave é desabilitada ou excluída.

Para revogar o acesso às chaves gerenciadas pelo cliente, use o [PowerShell](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys) ou [CLI do Azure](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys).

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Chaves gerenciadas pelo cliente para Azure Managed disks

As chaves gerenciadas pelo cliente também estão disponíveis para gerenciar a criptografia de Azure Managed disks. Chaves gerenciadas pelo cliente se comportam de maneira diferente para discos gerenciados do que para recursos de armazenamento do Azure Para obter mais informações, consulte [criptografia no servidor de Azure Managed disks](../../virtual-machines/disk-encryption.md) para Windows ou [criptografia do lado do servidor de Azure Managed disks](../../virtual-machines/disk-encryption.md) para Linux.

## <a name="next-steps"></a>Próximas etapas

- [Criptografia do Armazenamento do Azure para dados em repouso](storage-service-encryption.md)
- [Configurar a criptografia com as chaves gerenciadas pelo cliente armazenadas no Azure Key Vault](customer-managed-keys-configure-key-vault.md)
- [Configurar a criptografia com chaves gerenciadas pelo cliente armazenadas em Azure Key Vault HSM gerenciado (versão prévia)](customer-managed-keys-configure-key-vault-hsm.md)
