---
title: Registrar e examinar uma Instância Gerenciada do Banco de Dados SQL do Azure
description: Este tutorial descreve como examinar uma Instância Gerenciada do Banco de Dados SQL do Azure
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: a30980ba61a1dfec918dce1a55e78f1be2a36dd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677848"
---
# <a name="register-and-scan-an-azure-sql-database-managed-instance"></a>Registrar e examinar uma Instância Gerenciada do Banco de Dados SQL do Azure

Este artigo descreve como registrar uma fonte de dados da Instância Gerenciada do Banco de Dados SQL do Azure no Purview e configurar uma verificação nele.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

A fonte de dados da Instância Gerenciada do Banco de Dados SQL do Azure dá suporte às seguintes funcionalidades:

- **Verificações completas e incrementais** para capturar os metadados e a classificação na Instância Gerenciada do Banco de Dados SQL do Azure.

- **Linhagem** entre os ativos de dados para atividades de fluxo de dados e cópia do ADF.

### <a name="known-limitations"></a>Limitações conhecidas

O Azure Purview não dá suporte à verificação de [exibições](/sql/relational-databases/views/views?view=azuresqldb-mi-current&preserve-view=true) na Instância Gerenciada de SQL do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Crie uma conta do Purview caso ainda não tenha uma.

- [Configurar um ponto de extremidade público na Instância Gerenciada de SQL do Azure](../azure-sql/managed-instance/public-endpoint-configure.md)
    > [!Note]
    > Sua organização precisa conseguir permitir o ponto de extremidade público, pois **ainda não há suporte ao ponto de extremidade privado** no Purview. Se você usar o ponto de extremidade privado, a verificação não será bem-sucedida.

### <a name="setting-up-authentication-for-a-scan"></a>Configurar a autenticação para uma verificação

Autenticação para examinar uma Instância Gerenciada do Banco de Dados SQL do Azure. Caso precise criar uma autenticação, [autorize o acesso de banco de dados à Instância Gerenciada do Banco de Dados SQL](../azure-sql/database/logins-create-manage.md). Há três métodos de autenticação aos quais o Purview atualmente dá suporte:

- Autenticação SQL
- Entidade de Serviço
- Identidade Gerenciada

#### <a name="sql-authentication"></a>Autenticação SQL

> [!Note]
> Somente o logon da entidade de segurança no nível do servidor (criado pelo processo de provisionamento) ou membros da função de banco de dados `loginmanager` no banco de dados mestre podem criar novos logons. Isso leva cerca de **15 minutos** depois de conceder a permissão. A conta do Purview deverá ter as permissões apropriadas para examinar os recursos.

Siga as instruções descritas em [CRIAR LOGON](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) para criar um logon para a Instância Gerenciada do Banco de Dados SQL do Azure caso você não tenha isso disponível. Você precisará do **nome de usuário** e da **senha** nas próximas etapas.

1. Navegue até o seu cofre de chaves no portal do Azure
1. Selecione **Configurações > Segredos**
1. Selecione **+ Gerar/Importar** e insira o **Nome** e o **Valor** como a *senha* da Instância Gerenciada do Banco de Dados SQL do Azure
1. Selecione **Criar** para terminar
1. Se o cofre de chaves ainda não estiver conectado ao Purview, [crie uma conexão do cofre de chaves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Por fim, [crie uma credencial](manage-credentials.md#create-a-new-credential) usando o **nome de usuário** e a **senha** para configurar a verificação

#### <a name="service-principal-and-managed-identity"></a>Entidade de serviço e identidade gerenciada

Há várias etapas necessárias para permitir que o Purview use a entidade de serviço para examinar a Instância Gerenciada do Banco de Dados SQL do Azure

##### <a name="create-or-use-an-existing-service-principal"></a>Criar ou usar uma entidade de serviço existente

> [!Note]
> Ignore esta etapa se estiver usando a **identidade gerenciada**

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
- Crie um usuário do Azure AD na Instância Gerenciada do Banco de Dados SQL do Azure seguindo os pré-requisitos e o tutorial descritos em [Criar usuários independentes mapeados para identidades do Azure AD](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities)
- Atribua a permissão `db_owner` (**recomendado**) à identidade

##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Adicionar a entidade de serviço ao cofre de chaves e à credencial do Purview

> [!Note]
> Se estiver planejando usar a **identidade gerenciada**, ignore esta etapa, porque a identidade padrão do Purview já está em **Purview-MSI**

É necessário obter a ID e o segredo do aplicativo da entidade de serviço:

1. Navegue até a sua entidade de serviço no [portal do Azure](https://portal.azure.com)
1. Copie os valores de **ID do Aplicativo (cliente)** em **Visão geral** e de **Segredo do cliente** em **Certificados e segredos**.
1. Navegue até o Key Vault
1. Selecione **Configurações > Segredos**
1. Selecione **+ Gerar/Importar** e insira o **Nome** de sua escolha e o **Valor** como o **Segredo do cliente** da entidade de serviço
1. Selecione **Criar** para terminar
1. Se o cofre de chaves ainda não estiver conectado ao Purview, [crie uma conexão do cofre de chaves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Por fim, [crie uma credencial](manage-credentials.md#create-a-new-credential) usando a entidade de serviço para configurar a verificação

## <a name="register-an-azure-sql-database-managed-instance-data-source"></a>Registrar uma fonte de dados da Instância Gerenciada do Banco de Dados SQL do Azure

1. Acesse sua conta do Purview

1. Selecionar **Fontes** no painel de navegação à esquerda

1. Escolha **Registrar**

1. Selecione **Instância Gerenciada do Banco de Dados SQL do Azure** e **Continuar**

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-the-sql-data-source.png" alt-text="Configurar a fonte de dados SQL":::

1. Selecione **Da assinatura do Azure**, escolha a assinatura apropriada na caixa suspensa **Assinatura do Azure** e o servidor apropriado na caixa suspensa **Nome do servidor**.

1. Forneça o **nome de domínio totalmente qualificado do ponto de extremidade público** e o **número da porta**. Em seguida, selecione **Concluir** para registrar a fonte de dados.

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/add-azure-sql-database-managed-instance.png" alt-text="Adicionar uma Instância Gerenciada do Banco de Dados SQL do Azure":::

    Por ex.: `foobar.public.123.database.windows.net,3342`

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> A exclusão da verificação não exclui seus ativos das verificações anteriores da Instância Gerenciada do Banco de Dados SQL do Azure.

## <a name="next-steps"></a>Próximas etapas

- [Navegar pelo Catálogo de Dados do Azure Purview](how-to-browse-catalog.md)
- [Pesquisar no Catálogo de Dados do Azure Purview](how-to-search-catalog.md)