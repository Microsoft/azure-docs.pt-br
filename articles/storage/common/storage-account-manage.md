---
title: Gerenciar configurações de conta de armazenamento no portal do Azure – Armazenamento do Azure | Microsoft Docs
description: Saiba como gerenciar as configurações de conta de armazenamento no portal do Azure, incluindo a configuração das definições do controle de acesso, a regeneração das chaves de acesso de conta, a alteração da camada de acesso ou a modificação do tipo de replicação usado pela conta. Saiba também como excluir uma conta de armazenamento no portal.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 66bdc4bd1e17347419a6eccd7c9532db17b33001
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303487"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Gerenciar configurações de conta de armazenamento no portal do Azure

Há uma variedade de configurações para sua conta de armazenamento disponíveis no [portal do Azure](https://portal.azure.com). Este artigo descreve algumas dessas configurações e como usá-las.

## <a name="access-control"></a>Controle de acesso

O armazenamento do Azure oferece suporte à autorização com o Azure Active Directory para o armazenamento de BLOBs e armazenamento de fila por meio do controle de acesso baseado em função (RBAC). Para obter mais informações sobre a autorização com o Azure AD, consulte [autorizar o acesso ao Azure blobs e filas usando o Azure Active Directory](storage-auth-aad.md).

As configurações de **Controle de acesso** no portal do Azure oferecem uma maneira simples de atribuir funções de RBAC a usuários, grupos, entidades de serviço e identidades gerenciadas. Para obter mais informações sobre como atribuir funções RBAC, consulte [gerenciar direitos de acesso a dados de blob e fila com RBAC](storage-auth-aad-rbac.md).

## <a name="tags"></a>tags

O Armazenamento do Azure é compatível com marcas do Azure Resource Manager para organizar seus recursos do Azure com uma taxonomia personalizada. Você pode aplicar marcas às suas contas de armazenamento, de modo que você pode agrupá-las dentro de sua assinatura de maneira lógica.

Para contas de armazenamento, o nome da marca é limitado a 128 caracteres e o valor da marca é limitado a 256 caracteres.

Para obter mais informações, veja [Usar marcas para organizar os recursos do Azure](../../azure-resource-manager/resource-group-using-tags.md).

## <a name="access-keys"></a>Chaves de acesso

Quando você cria uma conta de armazenamento, o Azure gera duas chaves de acesso de conta de armazenamento de 512 bits. Essas chaves podem ser usadas para autorizar o acesso à sua conta de armazenamento por meio da Chave Compartilhada. Você pode girar e regenerar as chaves sem interrupção para seus aplicativos e a Microsoft recomenda que isso seja feito regularmente.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

### <a name="view-account-keys-and-connection-string"></a>Exibir chaves de conta e a cadeia de caracteres de conexão

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

### <a name="regenerate-access-keys"></a>Regenerar chaves de acesso

A Microsoft recomenda que você regenere as chaves de acesso periodicamente para ajudar a proteger sua conta de armazenamento. Duas chaves de acesso são atribuídas para que você possa girar as chaves. Quando você gira as chaves, garante que seu aplicativo mantém o acesso ao Armazenamento do Azure em todo o processo. 

> [!WARNING]
> A regeneração das chaves de acesso pode afetar todos os aplicativos ou serviços do Azure que dependem da chave de conta de armazenamento. Todos os clientes que usam a chave de conta para acessar a conta de armazenamento devem ser atualizados para usar a nova chave, incluindo os serviços de mídia, nuvem, aplicativos para área de trabalho e móveis e aplicativos de interface gráfica do usuário para o Armazenamento do Azure, tais como o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

Siga este processo para girar as chaves da conta de armazenamento:

1. Atualize as cadeias de conexão no código do aplicativo para usar a chave secundária.
2. Regenere a chave de acesso primária de sua conta de armazenamento. Na folha **Chaves de Acesso** no portal do Azure, clique em **Regenerar Chave1** e clique em **Sim** para confirmar que você deseja gerar uma nova chave.
3. Atualize as cadeias de conexão em seu código para fazer referência à nova chave de acesso primária.
4. Regenere a chave de acesso secundária da mesma maneira.

## <a name="account-configuration"></a>Configuração da conta

Depois de criar uma conta de armazenamento, você pode modificar sua configuração. Por exemplo, você pode alterar como os dados são replicados ou alterar a camada de acesso da conta de Frequente para Esporádico. No [portal do Azure](https://portal.azure.com), navegue até a conta de armazenamento, encontre e clique em **Configuração** e clique em **Configurações** para exibir e/ou alterar a configuração da conta.

Alterar a configuração da conta de armazenamento pode resultar em custos adicionais. Para obter mais detalhes, confira a página [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="delete-a-storage-account"></a>Excluir uma conta de armazenamento

Para remover uma conta de armazenamento que você não está mais usando, navegue até a conta de armazenamento do [portal do Azure](https://portal.azure.com)e clique em **Excluir**. A exclusão de uma conta de armazenamento exclui a conta inteira, incluindo todos os dados na conta.

> [!WARNING]
> Não é possível restaurar uma conta de armazenamento excluída nem recuperar nenhuma parte de seu conteúdo antes da exclusão. Não se esqueça de fazer backup de todas as informações que você deseja salvar antes de excluir a conta. Isso também é verdadeiro para todos os recursos na conta – depois que você excluir um blob, tabela, fila ou arquivo, ele será excluído permanentemente.
> 

Se você tentar excluir uma conta de armazenamento associada a uma máquina virtual do Azure, poderá receber um erro indicando que a conta de armazenamento ainda está em uso. Para obter ajuda com a solução desse erro, confira [Solucionar erros ao excluir contas de armazenamento](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da conta de armazenamento do Azure](storage-account-overview.md)
- [Criar uma conta de armazenamento](storage-quickstart-create-account.md)
