---
title: Solucionar problemas de conexões no Azure alcance
description: Este artigo explica as etapas para solucionar problemas de suas conexões no Azure alcance.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: f76a05757f86308785d4ca678675b87b3fa9d63e
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551515"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>Solucionar problemas de conexões no Azure alcance

Este artigo descreve como solucionar problemas de erros de conexão ao configurar verificações em fontes de dados no Azure alcance.

## <a name="permission-the-credential-on-the-data-source"></a>Permissão para a credencial na fonte de dados

Se você estiver usando uma identidade gerenciada ou uma entidade de serviço como um método de autenticação para verificações, terá que permitir que essas identidades tenham acesso à sua fonte de dados.

Há instruções específicas para cada tipo de fonte:

- [Armazenamento de Blobs do Azure](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Cosmos DB](register-scan-azure-cosmos-database.md#setting-up-authentication-for-a-scan)
- [Azure Data Explorer](register-scan-azure-data-explorer.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Banco de Dados SQL do Azure](register-scan-azure-sql-database.md)
- [Instância Gerenciada do Banco de Dados SQL do Azure](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
- [SQL Server](register-scan-on-premises-sql-server.md#setting-up-authentication-for-a-scan)
- [Power BI](register-scan-power-bi-tenant.md)

## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>Armazenando suas credenciais no cofre de chaves e usando o nome e a versão corretos do segredo

Você também deve armazenar suas credenciais em sua instância do Azure Key Vault e usar o nome e a versão corretos do segredo.

Verifique isso seguindo as etapas abaixo:

1. Navegue até o Key Vault.
1. Selecione **Configurações** > **Segredos**.
1. Selecione o segredo que você está usando para se autenticar em sua fonte de dados para verificações.
1. Selecione a versão que você pretende usar e verifique se a senha ou a chave de conta está correta clicando em **Mostrar valor secreto**. 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>Verifique as permissões para a identidade gerenciada do alcance no seu Azure Key Vault

Verifique se as permissões corretas foram configuradas para a identidade gerenciada do alcance para acessar seu Azure Key Vault.

Para verificar isso, execute as seguintes etapas:

1. Navegue até o cofre de chaves e para a seção **políticas de acesso**
1. Verifique se a identidade gerenciada do alcance é mostrada na seção *políticas de acesso atuais* , com permissões **Get** em segredos pelo menos

Se você não vir sua identidade gerenciada do alcance listada, siga as etapas em [criar e gerenciar credenciais para verificações](manage-credentials.md) para adicioná-la. 

## <a name="next-steps"></a>Próximas etapas

- [Procurar o catálogo de dados do Azure alcance](how-to-browse-catalog.md)
- [Pesquisar no catálogo de dados do Azure alcance](how-to-search-catalog.md)
