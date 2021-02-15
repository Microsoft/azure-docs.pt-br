---
title: 'Portal do Azure: máscara de dados dinâmicos'
description: Como começar a usar o mascaramento de dados dinâmicos do Azure SQL Database no portal do Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/28/2020
ms.openlocfilehash: e551925bbd1426256a81ac2ca3f245af7697245b
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98027988"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Introdução à máscara de dados dinâmicos do Banco de Dados SQL com o portal do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo mostra como implementar a [máscara de dados dinâmicos](dynamic-data-masking-overview.md) com o Portal do Azure. Você também pode implementar a máscara de dados dinâmicos usando [cmdlets de Banco de Dados SQL do Azure](/powershell/module/az.sql/) ou a [API REST](/rest/api/sql/).

> [!NOTE]
> Este recurso não pode ser definido usando o portal para SQL Instância Gerenciada (use o PowerShell ou a API REST). Para obter mais informações, consulte [Dynamic Data Masking](/sql/relational-databases/security/dynamic-data-masking).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Configurar o mascaramento de dados dinâmicos para o banco de dados usando o portal do Azure

1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com) .
2. Navegue até a página de configurações do banco de dados que contém os dados confidenciais que você deseja mascarar.
3. Clique na folha **máscara de dados dinâmicos** na seção **segurança** do banco de dados.

   ![Captura de tela que mostra a seção de segurança com Máscara de Dados Dinâmicos realçado.](./media/dynamic-data-masking-configure-portal/4_ddm_settings_tile.png)

4. Na página de configuração **Máscara de Dados Dinâmicos**, você poderá ver algumas colunas de banco de dados que o mecanismo de recomendações sinalizou para mascaramento. Para aceitar as recomendações, basta clicar em **Adicionar Máscara** para uma ou mais colunas e uma máscara será criada com base no tipo padrão para essa coluna. Você pode alterar a função de mascaramento clicando na regra de mascaramento e editando o formato do campo de mascaramento para um formato diferente à sua escolha. Clique em **Salvar** para salvar suas configurações.

    ![Captura de tela que mostra a página de configuração Máscara de Dados Dinâmicos.](./media/dynamic-data-masking-configure-portal/5_ddm_recommendations.png)

5. Para adicionar uma máscara a uma coluna do banco de dados, na parte superior da página de configuração **Máscara de Dados Dinâmicos**, clique em **Adicionar Máscara** para abrir a página de configuração **Adicionar Regra de Mascaramento**.

    ![Captura de tela que mostra a página de configuração Adicionar regra de mascaramento.](./media/dynamic-data-masking-configure-portal/6_ddm_add_mask.png)

6. Selecione o **Esquema**, a **Tabela** e a **Coluna** para definir o campo designado para o mascaramento.
7. **Selecione como mascarar** na lista de categorias de mascaramento de dados confidenciais.

    ![Captura de tela que mostra as categorias de máscara de dados confidenciais na seção selecionar como mascarar.](./media/dynamic-data-masking-configure-portal/7_ddm_mask_field_format.png)

8. Clique em **Adicionar** na página regra de mascaramento de dados para atualizar o conjunto de regras de mascaramento na política de mascaramento de dados dinâmicos.
9. Digite as identidades dos usuários do SQL ou do Azure Active Directory (Azure AD) que devem ser excluídas do mascaramento e tenha acesso aos dados confidenciais sem máscara. Deve ser uma lista de usuários separada por vírgulas. Os usuários com privilégios de administrador sempre terão acesso aos dados sem máscara originais.

    ![Painel de navegação](./media/dynamic-data-masking-configure-portal/8_ddm_excluded_users.png)

    > [!TIP]
    > Para fazer isso, a camada de aplicativo pode exibir dados confidenciais para usuários com privilégios de aplicativo, adicionar a identidade do usuário do SQL ou do Azure AD que o aplicativo usa para consultar o banco de dados. É altamente recomendável que essa lista contenha um número mínimo de usuários privilegiados para minimizar a exposição de dados confidenciais.

10. Clique em **Salvar** na página de configuração de mascaramento de dados para salvar a política de mascaramento nova ou atualizada.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral da máscara de dados dinâmicos, consulte [máscara de dados dinâmicos](dynamic-data-masking-overview.md).
- Você também pode implementar a máscara de dados dinâmicos usando [cmdlets de Banco de Dados SQL do Azure](/powershell/module/az.sql/) ou a [API REST](/rest/api/sql/).
