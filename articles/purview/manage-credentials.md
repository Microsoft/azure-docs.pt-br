---
title: Criar e gerenciar credenciais para verificações
description: Este artigo explica as etapas para criar e gerenciar credenciais no Azure alcance.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 4c964f3661e120026189a75d331e6db975b41c70
ms.sourcegitcommit: 90caa05809d85382c5a50a6804b9a4d8b39ee31e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97756068"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Credenciais para autenticação de origem no Azure alcance

Este artigo descreve como você pode criar credenciais no Azure alcance para reutilizar rapidamente e aplicar informações de autenticação salvas às verificações de fonte de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Um cofre de chaves do Azure. Para saber como criar um, consulte [início rápido: criar um cofre de chaves usando o portal do Azure](../key-vault/general/quick-create-portal.md).

## <a name="introduction"></a>Introdução
Uma credencial é uma informação de autenticação que o Azure alcance pode usar para se autenticar em suas fontes de dados registradas. Um objeto Credential pode ser criado para vários tipos de cenários de autenticação (como autenticação básica que requer nome de usuário/senha) e capturará as informações específicas necessárias com base no tipo escolhido de método de autenticação. As credenciais usam os segredos dos cofres de chaves do Azure existentes para recuperar informações de autenticação confidenciais durante o processo de criação de credenciais.

## <a name="using-purview-managed-identity-to-set-up-scans"></a>Usando a identidade gerenciada do alcance para configurar verificações
Se você estiver usando a identidade gerenciada do alcance para configurar verificações, não precisará criar explicitamente uma credencial e vincular seu cofre de chaves ao alcance para armazená-las. Para obter instruções detalhadas sobre como adicionar a identidade gerenciada do alcance para ter acesso para verificar suas fontes de dados, consulte as seções de autenticação específica da fonte de dados abaixo:

- [Armazenamento de Blobs do Azure](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Banco de Dados SQL do Azure](register-scan-azure-sql-database.md)
- [Instância Gerenciada do Banco de Dados SQL do Azure](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Criar conexões do Azure Key Vaults em sua conta do Azure alcance

Antes de criar uma credencial, primeiro você precisará associar uma ou mais das instâncias de Azure Key Vault existentes à sua conta do Azure alcance.

1. No menu de navegação do Azure alcance, navegue até o centro de gerenciamento e navegue até credenciais

2. Na barra de comandos de credenciais, selecione gerenciar conexões de Key Vault

    :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Gerenciar conexões AKV":::

3. Selecione + novo no painel gerenciar conexões de Key Vault 

4. Forneça as informações necessárias e, em seguida, selecione criar

5. Confirme se seu Key Vault foi associado com êxito à sua conta do Azure alcance

    :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Exibir conexões AKV":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Conceder ao alcance acesso de identidade gerenciada à sua Azure Key Vault

Navegue até o cofre de chaves-políticas de acesso de >-> adicionar política de acesso. Conceda permissão Get na lista suspensa permissões de segredos e selecione entidade de segurança para ser o alcance MSI. 

:::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="Adicionar alcance MSI ao AKV":::


:::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Adicionar política de acesso":::


:::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Salvar política de acesso":::

## <a name="create-a-new-credential"></a>Criar uma nova credencial

Tipo de credencial com suporte no alcance hoje:
* Autenticação básica: você adicionará a **senha** como um segredo no Key Vault
* Entidade de serviço: você adicionará a **chave da entidade de serviço** como um segredo no cofre de chaves 
* Autenticação do SQL: você adicionará a **senha** como um segredo no Key Vault
* Chave de conta: você adicionará a **chave de conta** como um segredo no Key Vault

Para obter mais informações, consulte [Adicionar um segredo a Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault).

Depois de armazenar seus segredos no cofre de chaves, crie sua nova credencial selecionando + novo na barra de comandos de credenciais. Forneça as informações necessárias, incluindo a seleção do método de autenticação e uma instância de Key Vault da qual selecionar um segredo. Depois que todos os detalhes tiverem sido preenchidos, clique em criar.

:::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Nova credencial":::

Verifique se a nova credencial é exibida na exibição de lista de credenciais e está pronta para uso

:::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Exibir credencial":::

## <a name="manage-your-key-vault-connections"></a>Gerenciar as conexões do cofre de chaves

1. Pesquisar/localizar Key Vault conexões por nome

    :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Pesquisar cofre de chaves":::

1. Excluir uma ou mais conexões de Key Vault
 
    :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Excluir cofre de chaves":::

## <a name="manage-your-credentials"></a>Gerenciar suas credenciais

1. Pesquisar/localizar credenciais por nome
  
2. Selecionar e fazer atualizações em uma credencial existente

3. Excluir uma ou mais credenciais
