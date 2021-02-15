---
title: Usar o portal do Azure para configurar chaves gerenciadas pelo cliente
titleSuffix: Cognitive Services
description: Saiba como usar o portal do Azure para configurar chaves gerenciadas pelo cliente com Azure Key Vault. As chaves gerenciadas pelo cliente permitem criar, girar, desabilitar e revogar controles de acesso.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: c79846b0a5b675c34e4e7919e9ecd9d591bfefe5
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356351"
---
## <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves gerenciadas pelo cliente com o Azure Key Vault

Você deve usar Azure Key Vault para armazenar chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs do Azure Key Vault para gerar chaves. O recurso de serviços cognitivas e o cofre de chaves devem estar na mesma região e no mesmo locatário Azure Active Directory (Azure AD), mas podem estar em assinaturas diferentes. Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault?](../../key-vault/general/overview.md).

Quando um novo recurso de serviços cognitivas é criado, ele é sempre criptografado usando chaves gerenciadas pela Microsoft. Não é possível habilitar chaves gerenciadas pelo cliente no momento em que o recurso é criado. As chaves gerenciadas pelo cliente são armazenadas em Azure Key Vault, e o cofre de chaves deve ser provisionado com políticas de acesso que concedem permissões de chave para a identidade gerenciada associada ao recurso de serviços cognitivas. A identidade gerenciada está disponível somente depois que o recurso é criado usando o tipo de preço necessário para CMK.

Habilitar chaves gerenciadas pelo cliente também habilitará uma [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md)atribuída pelo sistema, um recurso do Azure AD. Depois que a identidade gerenciada atribuída pelo sistema estiver habilitada, esse recurso será registrado com Azure Active Directory. Depois de ser registrado, a identidade gerenciada receberá acesso ao Key Vault selecionado durante a configuração de chave gerenciada pelo cliente. 

> [!IMPORTANT]
> Se você desabilitar as identidades gerenciadas atribuídas pelo sistema, o acesso ao cofre de chaves será removido e todos os dados criptografados com as chaves do cliente não estarão mais acessíveis. Todos os recursos dependentes desses dados deixarão de funcionar.

> [!IMPORTANT]
> Identidades gerenciadas não têm suporte a cenários entre diretórios. Quando você configura chaves gerenciadas pelo cliente no portal do Azure, uma identidade gerenciada é automaticamente atribuída nos bastidores. Se, posteriormente, você mover a assinatura, o grupo de recursos ou o recurso de um diretório do Azure AD para outro, a identidade gerenciada associada ao recurso não será transferida para o novo locatário, portanto, as chaves gerenciadas pelo cliente poderão deixar de funcionar. Para obter mais informações, consulte **transferindo uma assinatura entre diretórios do Azure ad** em [perguntas frequentes e problemas conhecidos com identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="configure-azure-key-vault"></a>Configurar o Azure Key Vault

O uso de chaves gerenciadas pelo cliente requer que duas propriedades sejam definidas no cofre de chaves, **exclusão reversível** e **não sejam limpas**. Essas propriedades não são habilitadas por padrão, mas podem ser habilitadas usando o PowerShell ou CLI do Azure em um cofre de chaves novo ou existente.

> [!IMPORTANT]
> Se você não tiver a **exclusão reversível** e **não limpar** as propriedades habilitadas e excluir sua chave, não será possível recuperar os dados em seu recurso de serviço cognitiva.

Para saber como habilitar essas propriedades em um cofre de chaves existente, confira as seções intituladas **Como habilitar a exclusão temporária** e **Como habilitar a proteção contra a limpeza** em um dos seguintes artigos:

- [Como usar a exclusão reversível com o PowerShell](../../key-vault/general/key-vault-recovery.md).
- [Como usar a exclusão reversível com a CLI](../../key-vault/general/key-vault-recovery.md).

Somente as chaves RSA de tamanho 2048 têm suporte com a criptografia de armazenamento do Azure. Para obter mais informações sobre chaves, consulte **Key Vault chaves** em [sobre Azure Key Vault chaves, segredos e certificados](../../key-vault/general/about-keys-secrets-certificates.md).

## <a name="enable-customer-managed-keys-for-your-resource"></a>Habilitar chaves gerenciadas pelo cliente para seu recurso

Para habilitar as chaves gerenciadas pelo cliente no portal do Azure, siga estas etapas:

1. Navegue até o recurso de serviços cognitivas.
1. Na folha **configurações** do recurso serviços cognitivas, clique em **criptografia**. Selecione a opção **chaves gerenciadas pelo cliente** , conforme mostrado na figura a seguir.

    ![Captura de tela mostrando como selecionar chaves gerenciadas pelo cliente](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Especificar uma chave

Depois de habilitar as chaves gerenciadas pelo cliente, você terá a oportunidade de especificar uma chave a ser associada ao recurso de serviços cognitivas.

### <a name="specify-a-key-as-a-uri"></a>Especificar uma chave como URI

Para especificar uma chave como um URI, siga estas etapas:

1. Para localizar o URI de chave no portal do Azure, navegue até o cofre de chaves e selecione a configuração **chaves** . Selecione a chave desejada e clique na chave para exibir suas versões. Selecione uma versão de chave para exibir as configurações dessa versão.
1. Copie o valor do campo **identificador de chave** , que fornece o URI.

    ![Captura de tela mostrando o URI da chave do Key Vault](../media/cognitive-services-encryption/key-uri-portal.png)

1. Nas configurações de **criptografia** para sua conta de armazenamento, escolha a opção **inserir URI de chave** .
1. Cole o URI que você copiou no campo **URI da chave** .

   ![Captura de tela mostrando como inserir o URI da chave](../media/cognitive-services-encryption/ssecmk2.png)

1. Especifique a assinatura que contém o cofre de chaves.
1. Salve suas alterações.

### <a name="specify-a-key-from-a-key-vault"></a>Especificar uma chave de um cofre de chaves

Para especificar uma chave de um cofre de chaves, primeiro verifique se você tem um cofre de chaves que contém uma chave. Para especificar uma chave de um cofre de chaves, siga estas etapas:

1. Escolha a opção **Selecionar do Cofre de chaves**.
1. Selecione o cofre de chaves que contém a chave que você deseja usar.
1. Selecione a chave no cofre de chaves.

   ![Captura de tela mostrando a opção de chave gerenciada pelo cliente](../media/cognitive-services-encryption/ssecmk3.png)

1. Salve suas alterações.

## <a name="update-the-key-version"></a>Atualizar a versão de chave

Quando você cria uma nova versão de uma chave, atualize o recurso de serviços cognitivas para usar a nova versão. Siga estas etapas:

1. Navegue até o recurso de serviços cognitivas e exiba as configurações de **criptografia** .
1. Insira o URI para a nova versão de chave. Como alternativa, você pode selecionar o cofre de chaves e a chave novamente para atualizar a versão.
1. Salve suas alterações.

## <a name="use-a-different-key"></a>Usar uma chave diferente

Para alterar a chave usada para criptografia, siga estas etapas:

1. Navegue até o recurso de serviços cognitivas e exiba as configurações de **criptografia** .
1. Insira o URI para a nova chave. Como alternativa, você pode selecionar o cofre de chaves e escolher uma nova chave.
1. Salve suas alterações.

## <a name="rotate-customer-managed-keys"></a>Girar chaves gerenciadas pelo cliente

Você pode fazer a rotação de uma chave gerenciada pelo cliente no Azure Key Vault de acordo com suas políticas de conformidade. Quando a chave é girada, você deve atualizar o recurso de serviços cognitivas para usar o novo URI de chave. Para saber como atualizar o recurso para usar uma nova versão da chave no portal do Azure, consulte [atualizar a versão da chave](#update-the-key-version).

A rotação da chave não aciona a nova criptografia dos dados no recurso. Não há nenhuma ação adicional necessária do usuário.

## <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso a chaves gerenciadas pelo cliente

Para revogar o acesso às chaves gerenciadas pelo cliente, use o PowerShell ou CLI do Azure. Para obter mais informações, confira [PowerShell do Azure Key Vault](/powershell/module/az.keyvault//) ou [CLI do Azure Key Vault](/cli/azure/keyvault). Revogar o acesso efetivamente bloqueia o acesso a todos os dados no recurso de serviços cognitivas, pois a chave de criptografia é inacessível por serviços cognitivas.

## <a name="disable-customer-managed-keys"></a>Desabilitar chaves gerenciadas pelo cliente

Quando você desabilita chaves gerenciadas pelo cliente, seu recurso de serviços cognitivas é criptografado com chaves gerenciadas pela Microsoft. Para desabilitar as chaves gerenciadas pelo cliente, siga estas etapas:

1. Navegue até o recurso de serviços cognitivas e exiba as configurações de **criptografia** .
1. Desmarque a caixa de seleção ao lado da configuração **usar sua própria chave** .