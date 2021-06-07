---
title: Registrar e examinar um Banco de Dados SQL do Azure
description: Este tutorial descreve como examinar um Banco de Dados SQL do Azure
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 74a8bc4209696682c294bccab450d25ae86e3645
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643935"
---
# <a name="register-and-scan-an-azure-sql-database"></a>Registrar e examinar um Banco de Dados SQL do Azure

Este artigo descreve como registrar uma fonte de dados do Banco de Dados SQL do Azure no Purview e configurar uma verificação nele.

## <a name="supported-capabilities"></a>Funcionalidades compatíveis

A fonte de dados do Banco de Dados SQL do Azure dá suporte às seguintes funcionalidades:

- **Verificações completas e incrementais** para capturar os metadados e a classificação no Banco de Dados SQL do Azure.

- **Linhagem** entre os ativos de dados para atividades de fluxo de dados e cópia do ADF.

### <a name="known-limitations"></a>Limitações conhecidas

O Azure Purview não dá suporte à verificação de [exibições](/sql/relational-databases/views/views?view=azuresqldb-current&preserve-view=true) no Banco de Dados SQL do Azure.

## <a name="prerequisites"></a>Pré-requisitos

1. Crie uma conta do Purview caso ainda não tenha uma.

1. Acesso à rede entre a conta do Purview e o Banco de Dados SQL do Azure.


### <a name="set-up-authentication-for-a-scan"></a>Configurar a autenticação para uma verificação

Autenticação para examinar um Banco de Dados SQL do Azure. Caso precise criar uma autenticação, [autorize o acesso de banco de dados ao Banco de Dados SQL](../azure-sql/database/logins-create-manage.md). Há três métodos de autenticação aos quais o Purview atualmente dá suporte:

- Autenticação SQL
- Entidade de Serviço
- Identidade Gerenciada

#### <a name="sql-authentication"></a>Autenticação SQL

> [!Note]
> Somente o logon da entidade de segurança no nível do servidor (criado pelo processo de provisionamento) ou membros da função de banco de dados `loginmanager` no banco de dados mestre podem criar novos logons. Isso leva cerca de **15 minutos** depois de conceder a permissão. A conta do Purview deverá ter as permissões apropriadas para examinar os recursos.

Siga as instruções descritas em [CRIAR LOGON](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) para criar um logon para o Banco de Dados SQL do Azure caso você não tenha isso disponível. Você precisará do **nome de usuário** e da **senha** nas próximas etapas.

1. Navegue até o seu cofre de chaves no portal do Azure
1. Selecione **Configurações > Segredos**
1. Selecione **+ Gerar/Importar** e insira o **Nome** e o **Valor** como a *senha* do Banco de Dados SQL do Azure
1. Selecione **Criar** para terminar
1. Se o cofre de chaves ainda não estiver conectado ao Purview, [crie uma conexão do cofre de chaves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Por fim, [crie uma credencial](manage-credentials.md#create-a-new-credential) usando o **nome de usuário** e a **senha** para configurar a verificação

#### <a name="service-principal-and-managed-identity"></a>Entidade de serviço e identidade gerenciada

Há várias etapas necessárias para permitir que o Purview use a entidade de serviço ou a **identidade gerenciada** do Purview para examinar o Banco de Dados SQL do Azure

   > [!Note]
   > O Purview precisará da **ID do Aplicativo (cliente)** e do **segredo do cliente** para fazer a verificação.

##### <a name="create-or-use-an-existing-service-principal"></a>Criar ou usar uma entidade de serviço existente

> [!Note]
> Ignore esta etapa se estiver usando a **identidade gerenciada** do Purview

Para usar uma entidade de serviço, use uma existente ou crie uma. 

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

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Configurar a autenticação do Azure AD na conta do banco de dados

A entidade de serviço ou a identidade gerenciada precisa ter permissão para obter metadados para o banco de dados, esquemas e tabelas. Ela também precisa conseguir consultar as tabelas para obter uma amostragem de classificação.

- [Configurar e gerenciar a autenticação do Azure AD com o Azure SQL](../azure-sql/database/authentication-aad-configure.md)
- Se você estiver usando a identidade gerenciada, sua conta do Purview terá uma identidade gerenciada própria, que é basicamente seu nome do Purview quando você o criou. Você precisará criar um usuário do Azure AD no Banco de Dados SQL do Azure com a identidade gerenciada exata do Purview ou sua entidade de serviço seguindo o tutorial descrito em [Criar o usuário da entidade de serviço no Banco de Dados SQL do Azure](../azure-sql/database/authentication-aad-service-principal-tutorial.md#create-the-service-principal-user-in-azure-sql-database). É necessário atribuir uma permissão apropriada (como `db_owner` ou `db_datareader`) à identidade. Exemplo de sintaxe SQL para criar um usuário e conceder a permissão:

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [Username]
    GO
    ```

    > [!Note]
    > O `Username` é a sua entidade de serviço ou identidade gerenciada do Purview. É possível ler mais sobre [funções de banco de dados fixas e respectivas funcionalidades](/sql/relational-databases/security/authentication-access/database-level-roles#fixed-database-roles).
    
##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Adicionar a entidade de serviço ao cofre de chaves e à credencial do Purview

> [!Note]
> Se estiver planejando usar a **identidade gerenciada** do Purview, ignore esta etapa, porque a identidade gerenciada padrão do Purview já está na credencial **Purview-MSI**.

É necessário obter a ID e o segredo do aplicativo da entidade de serviço:

1. Navegue até a sua entidade de serviço no [portal do Azure](https://portal.azure.com)
1. Copie os valores de **ID do Aplicativo (cliente)** em **Visão geral** e de **Segredo do cliente** em **Certificados e segredos**.
1. Navegue até o Key Vault
1. Selecione **Configurações > Segredos**
1. Selecione **+ Gerar/Importar** e insira o **Nome** de sua escolha e o **Valor** como o **Segredo do cliente** da entidade de serviço
1. Selecione **Criar** para terminar
1. Se o cofre de chaves ainda não estiver conectado ao Purview, [crie uma conexão do cofre de chaves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Por fim, [crie uma credencial](manage-credentials.md#create-a-new-credential) usando a entidade de serviço para configurar a verificação

### <a name="firewall-settings"></a>Configurações de firewall

O servidor de banco de dados precisará permitir que as conexões do Azure sejam habilitadas. Isso permitirá que o Azure acesse o servidor e se conecte a ele. Siga o guia de instruções de [Conexões dentro do Azure](../azure-sql/database/firewall-configure.md#connections-from-inside-azure).

1. Acesse a sua conta de banco de dados
1. Selecione o nome do servidor na página **Visão geral**
1. Escolha **Segurança > Firewalls e redes virtuais**
1. Escolha **Sim** na opção **Permitir que serviços e recursos do Azure acessem este servidor**

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="Permitir que serviços e recursos do Azure acessem este servidor." border="true":::
    
> [!Note]
> Atualmente, o Azure Purview não dá suporte à configuração da VNET. Portanto, não é possível fazer configurações de firewall baseadas em IP.

## <a name="register-an-azure-sql-database-data-source"></a>Registrar uma fonte de dados do Banco de Dados SQL do Azure

Para registrar um novo Banco de Dados SQL do Azure no seu catálogo de data, faça o seguinte:

1. Acesse sua conta do Purview

1. Selecionar **Fontes** no painel de navegação à esquerda

1. Escolha **Registrar**

1. Em **Registrar fontes**, escolha **Banco de Dados SQL do Azure**. Selecione **Continuar**.

:::image type="content" source="media/register-scan-azure-sql-database/register-new-data-source.png" alt-text="registrar uma nova fonte de dados" border="true":::

Na tela **Registrar fontes (Banco de Dados SQL do Azure)** , faça o seguinte:

1. Insira um **Nome** com a qual a fonte de dados será listada no Catálogo.
1. Selecione **Da assinatura do Azure**, escolha a assinatura apropriada na caixa suspensa **Assinatura do Azure** e o servidor apropriado na caixa suspensa **Nome do servidor**.
1. **Conclua** a etapa para registrar a fonte de dados.

:::image type="content" source="media/register-scan-azure-sql-database/add-azure-sql-database.png" alt-text="opções de registro de fontes" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> * A exclusão da verificação não exclui seus ativos das verificações anteriores do Banco de Dados SQL do Azure.
> * O ativo não será mais atualizado com alterações de esquema se a tabela de origem for alterada e examinar novamente a tabela de origem depois de editar a descrição na guia esquema do Purview.

## <a name="next-steps"></a>Próximas etapas

- [Navegar pelo Catálogo de Dados do Azure Purview](how-to-browse-catalog.md)
- [Pesquisar no Catálogo de Dados do Azure Purview](how-to-search-catalog.md)
