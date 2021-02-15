---
title: Criar e gerenciar credenciais para verificações
description: Saiba mais sobre as etapas para criar e gerenciar credenciais no Azure alcance.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 9ffc450294f186b77cc7a6c44c10eecf266161d3
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526674"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Credenciais para autenticação de origem no Azure alcance

Este artigo descreve como você pode criar credenciais no Azure alcance. Essas credenciais salvas permitem que você reutilize rapidamente e aplique informações de autenticação salvas às verificações da fonte de dados.

## <a name="prerequisites"></a>Pré-requisitos

- Um cofre de chaves do Azure. Para saber como criar um, consulte [início rápido: criar um cofre de chaves usando o portal do Azure](../key-vault/general/quick-create-portal.md).

## <a name="introduction"></a>Introdução

Uma credencial é uma informação de autenticação que o Azure alcance pode usar para se autenticar em suas fontes de dados registradas. Um objeto Credential pode ser criado para vários tipos de cenários de autenticação, como a autenticação básica que exige nome de usuário/senha. Informações específicas de captura de credenciais necessárias para autenticação, com base no tipo escolhido de método de autenticação. As credenciais usam os segredos dos cofres de chaves do Azure existentes para recuperar informações de autenticação confidenciais durante o processo de criação de credenciais.

## <a name="use-purview-managed-identity-to-set-up-scans"></a>Usar a identidade gerenciada do alcance para configurar verificações

Se você estiver usando a identidade gerenciada do alcance para configurar verificações, não precisará criar explicitamente uma credencial e vincular seu cofre de chaves ao alcance para armazená-las. Para obter instruções detalhadas sobre como adicionar a identidade gerenciada do alcance para ter acesso para verificar suas fontes de dados, consulte as seções de autenticação específicas da fonte de dados abaixo:

- [Armazenamento de Blobs do Azure](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Banco de Dados SQL do Azure](register-scan-azure-sql-database.md)
- [Instância Gerenciada do Banco de Dados SQL do Azure](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Criar conexões do Azure Key Vaults em sua conta do Azure alcance

Antes de criar uma credencial, primeiro associe uma ou mais das instâncias de Azure Key Vault existentes à sua conta do Azure alcance.

1. Na [portal do Azure](https://portal.azure.com), selecione sua conta do Azure alcance. Navegue até o **centro de gerenciamento** e, em seguida, navegue até **credenciais**.

2. Na página **credenciais** , selecione **gerenciar conexões de Key Vault**.

   :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Gerenciar conexões de Azure Key Vault":::

3. Selecione **+ novo** na página Gerenciar conexões de Key Vault.

4. Forneça as informações necessárias e, em seguida, selecione **criar**.

5. Confirme se seu Key Vault foi associado com êxito à sua conta do Azure alcance, conforme mostrado neste exemplo:

   :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Exiba Azure Key Vault conexões a serem confirmadas.":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Conceder ao alcance acesso de identidade gerenciada à sua Azure Key Vault

1. Navegue até o Azure Key Vault.

2. Selecione a página **políticas de acesso** .

3. Selecione **Adicionar Política de Acesso**.

   :::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="Adicionar alcance MSI ao AKV":::

4. Na lista suspensa **permissões de segredos** , selecione as permissões **obter** e **listar** .

5. Para **selecionar entidade de segurança**, escolha a identidade gerenciada alcance. Você pode pesquisar o MSI do alcance usando o nome da instância do alcance **ou** a ID do aplicativo de identidade gerenciada. No momento, não há suporte para identidades compostas (nome da identidade gerenciada + ID do aplicativo).

   :::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Adicionar política de acesso":::

6. Selecione **Adicionar**.

7. Selecione **salvar** para salvar a política de acesso.

   :::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Salvar política de acesso":::

## <a name="create-a-new-credential"></a>Criar uma nova credencial

Esses tipos de credenciais têm suporte no alcance:

- Autenticação básica: você adiciona a **senha** como um segredo no cofre de chaves.
- Entidade de serviço: Adicione a **chave da entidade de serviço** como um segredo no cofre de chaves.
- Autenticação do SQL: você adiciona a **senha** como um segredo no cofre de chaves.
- Chave de conta: Adicione a **chave de conta** como um segredo no cofre de chaves.

Para obter mais informações, consulte [Adicionar um segredo a Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault).

Depois de armazenar seus segredos no cofre de chaves:

1. No Azure alcance, vá para a página credenciais.

2. Crie sua nova credencial selecionando **+ novo**.

3. Forneça as informações necessárias. Selecione o **método de autenticação** e uma **Key Vault conexão** da qual selecionar um segredo.

4. Depois que todos os detalhes tiverem sido preenchidos, selecione **criar**.

   :::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Nova credencial":::

5. Verifique se a nova credencial é exibida na exibição de lista e está pronta para uso.

   :::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Exibir credencial":::

## <a name="manage-your-key-vault-connections"></a>Gerenciar as conexões do cofre de chaves

1. Pesquisar/localizar Key Vault conexões por nome

   :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Pesquisar cofre de chaves":::

2. Excluir uma ou mais conexões de Key Vault

   :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Excluir cofre de chaves":::

## <a name="manage-your-credentials"></a>Gerenciar suas credenciais

1. Pesquisar/localizar credenciais por nome.
  
2. Selecione e faça atualizações em uma credencial existente.

3. Exclua uma ou mais credenciais.

## <a name="next-steps"></a>Próximas etapas

[Criar um conjunto de regras de exame](create-a-scan-rule-set.md)
