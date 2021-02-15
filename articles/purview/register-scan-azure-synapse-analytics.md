---
title: Como verificar a análise de Synapse do Azure
description: Este guia de instruções descreve os detalhes de como examinar a análise de Synapse do Azure.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: 62ca32ab4e348e1488fbb87672e582436b91d05d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98875002"
---
# <a name="register-and-scan-azure-synapse-analytics"></a>Registrar e verificar o Azure Synapse Analytics

Este artigo discute como registrar e verificar uma instância do Azure Synapse Analytics (anteriormente conhecido como SQL DW) no alcance.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

O Azure Synapse Analytics (anteriormente conhecido como SQL DW) dá suporte a verificações completas e incrementais para capturar os metadados e o esquema. As verificações também classificam os dados automaticamente com base em regras de classificação personalizada e do sistema.

### <a name="known-limitations"></a>Limitações conhecidas

O Azure alcance não dá suporte à verificação de [exibições](/sql/relational-databases/views/views?view=azure-sqldw-latest&preserve-view=true) na análise de Synapse do Azure

## <a name="prerequisites"></a>Pré-requisitos

- Antes de registrar as fontes de dados, crie uma conta do Azure alcance. Para obter mais informações sobre como criar uma conta do alcance, consulte [início rápido: criar uma conta do Azure alcance](create-catalog-portal.md).
- Você precisa ser um administrador de fonte de dados do Azure alcance
- Acesso à rede entre a conta do alcance e o Azure Synapse Analytics.
 
## <a name="setting-up-authentication-for-a-scan"></a>Configurar a autenticação para uma verificação

Há três maneiras de configurar a autenticação para o Azure Synapse Analytics:

- Identidade Gerenciada
- Autenticação do SQL
- Entidade de Serviço

    > [!Note]
    > Somente o logon da entidade de segurança no nível do servidor (criado pelo processo de provisionamento) ou membros da função de banco de dados `loginmanager` no banco de dados mestre podem criar novos logons. Isso leva cerca de 15 minutos depois de conceder a permissão. A conta do Purview deverá ter as permissões apropriadas para examinar os recursos.

### <a name="managed-identity-recommended"></a>Identidade gerenciada (recomendado) 
   
Sua conta do alcance tem sua própria identidade gerenciada, que é basicamente seu nome de alcance quando você a criou. Você deve criar um usuário do Azure AD no Azure Synapse Analytics (anteriormente conhecido como SQL DW) com o nome de identidade gerenciado exato do alcance seguindo os pré-requisitos e o tutorial sobre como [criar usuários do Azure ad usando aplicativos do Azure ad](../azure-sql/database/authentication-aad-service-principal-tutorial.md).

Exemplo de sintaxe SQL para criar um usuário e conceder a permissão:

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [PurviewManagedIdentity]
GO
```

A autenticação deve ter permissão para obter metadados para o banco de dados, esquemas e tabelas. Ela também precisa conseguir consultar as tabelas para obter uma amostragem de classificação. A recomendação é atribuir `db_owner` permissão à identidade.

### <a name="service-principal"></a>Entidade de Serviço

Para usar a autenticação de entidade de serviço para verificações, você pode usar uma existente ou criar uma nova. 

> [!Note]
> Caso precise criar uma entidade de serviço, siga estas etapas:
> 1. Navegue até o [Portal do Azure](https://portal.azure.com).
> 1. Selecione **Azure Active Directory** no menu do lado esquerdo.
> 1. Selecione **Registros do Aplicativo**.
> 1. Selecione **+Novo registro de aplicativo**.
> 1. Insira um nome para o **aplicativo** (o nome da entidade de serviço).
> 1. Escolha **Somente contas neste diretório organizacional**.
> 1. Em URI de Redirecionamento, selecione **Web** e insira qualquer URL desejada; ela não precisa ser real nem funcionar.
> 1. Em seguida, selecione **Registrar**.

É necessário obter a ID e o segredo do aplicativo da entidade de serviço:

1. Navegue até a sua entidade de serviço no [portal do Azure](https://portal.azure.com)
1. Copie os valores de **ID do Aplicativo (cliente)** em **Visão geral** e de **Segredo do cliente** em **Certificados e segredos**.
1. Navegue até o Key Vault
1. Selecione **Configurações > Segredos**
1. Selecione **+ Gerar/Importar** e insira o **Nome** de sua escolha e o **Valor** como o **Segredo do cliente** da entidade de serviço
1. Selecione **Criar** para terminar
1. Se o cofre de chaves ainda não estiver conectado ao Purview, [crie uma conexão do cofre de chaves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Por fim, [crie uma credencial](manage-credentials.md#create-a-new-credential) usando a entidade de serviço para configurar a verificação 

#### <a name="granting-the-service-principal-access-to-your-azure-synapse-analytics-formerly-sql-dw"></a>Concedendo acesso à entidade de serviço ao Azure Synapse Analytics (anteriormente conhecido como SQL DW)

Além disso, você também deve criar um usuário do Azure AD no Azure Synapse Analytics seguindo os pré-requisitos e o tutorial sobre como [criar usuários do Azure ad usando aplicativos do Azure ad](../azure-sql/database/authentication-aad-service-principal-tutorial.md). Exemplo de sintaxe SQL para criar um usuário e conceder a permissão:

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [ServicePrincipalName]
GO
```

> [!Note]
> O Purview precisará da **ID do Aplicativo (cliente)** e do **segredo do cliente** para fazer a verificação.

### <a name="sql-authentication"></a>Autenticação SQL

Você pode seguir as instruções em [criar logon](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-1) para criar um logon para o Azure Synapse Analytics (anteriormente conhecido como SQL DW), se você ainda não tiver um.

Quando o método de autenticação selecionado for **SQL Authentication**, você precisará obter sua senha e armazená-la no cofre de chaves:

1. Obter a senha para seu logon do SQL
1. Navegue até o Key Vault
1. Selecione **Configurações > Segredos**
1. Selecione **+ gerar/importar** e insira o **nome** e o **valor** como a *senha* para o logon do SQL
1. Selecione **Criar** para terminar
1. Se o cofre de chaves ainda não estiver conectado ao Purview, [crie uma conexão do cofre de chaves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Por fim, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) usando a chave para configurar sua verificação

## <a name="register-an-azure-synapse-analytics-instance-formerly-sql-dw"></a>Registrar uma instância do Azure Synapse Analytics (anteriormente conhecido como SQL DW)

Para registrar um novo servidor do Azure Synapse Analytics em seu catálogo de dados, faça o seguinte:

1. Acesse sua conta do Purview
1. Selecionar **Fontes** no painel de navegação à esquerda
1. Escolha **Registrar**
1. Em **registrar fontes**, selecione **Azure Synapse Analytics (anteriormente conhecido como SQL DW)**
1. Selecione **Continuar**

Na tela **registrar fontes (Azure Synapse Analytics)** , faça o seguinte:

1. Insira um **Nome** com a qual a fonte de dados será listada no Catálogo.
1. Escolha como deseja apontá-la para a conta de armazenamento desejada:
   1. Selecione **da assinatura do Azure**, selecione a assinatura apropriada na caixa suspensa **assinatura do Azure** e o servidor apropriado na caixa suspensa **nome do servidor** .
   1. Ou, então, escolha **Inserir manualmente** e insira um **Nome do servidor**.
1. **Conclua** a etapa para registrar a fonte de dados.

:::image type="content" source="media/register-scan-azure-synapse-analytics/register-sources.png" alt-text="opções de registro de fontes" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Próximas etapas

- [Navegar pelo Catálogo de Dados do Azure Purview](how-to-browse-catalog.md)
- [Pesquisar no Catálogo de Dados do Azure Purview](how-to-search-catalog.md)