---
title: Gerenciar dados pessoais no Microsoft Azure | Microsoft Docs
description: diretrizes de como corrigir, atualizar, excluir e exportar dados pessoais no Azure Active Directory e no Banco de Dados SQL do Azure
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.openlocfilehash: 3b57c92bd744644ea81878712b4272ed3ece4e2e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-personal-data-in-microsoft-azure"></a>Gerenciar dados pessoais no Microsoft Azure

Este artigo fornece diretrizes de como corrigir, atualizar, excluir e exportar dados pessoais no Azure Active Directory e no Banco de Dados SQL do Azure.

## <a name="scenario"></a>Cenário

Uma empresa localizada em Dublin fornece um único lugar de compras para casamentos em destinos de classe alta na Irlanda e no mundo para uma base de clientes local e internacional. Ela tem escritórios, clientes, funcionários e fornecedores localizados no mundo todo para prestar serviços completos para os locais oferecidos.

Entre muitos outros itens, a empresa acompanha os RSVPs que incluem alergias a alimentos e preferências alimentares. Os convidados do casamento podem se registrar em várias atividades, como passeios a cavalo, surfe, passeios de barco, etc. e até mesmo interagir entre si em uma página da Web central durante os meses anteriores ao evento. A empresa coleta informações pessoais de funcionários, fornecedores, clientes e convidados do casamento. Devido à natureza internacional dos negócios, a empresa deve estar em conformidade com vários níveis de regulamentação.

## <a name="problem-statement"></a>Problema declarado

- Os administradores de dados devem conseguir corrigir informações pessoais incorretas e atualizar informações pessoais incompletas ou alteradas.

- Os administradores de dados devem conseguir excluir informações pessoais mediante a solicitação de uma entidade de dados.

- Os administradores de dados precisam exportar dados e fornecê-los a uma entidade de dados em um formato comum e estruturado mediante sua solicitação.

## <a name="company-goals"></a>Metas da empresa

- As informações pessoais incorretas ou incompletas de clientes, convidados do casamento, funcionários e fornecedores devem ser corrigidas ou atualizadas no Azure Active Directory e no Banco de Dados SQL do Azure.

- As informações pessoais devem ser excluídas do Azure Active Directory e do Banco de Dados SQL do Azure mediante a solicitação de uma entidade de dados.

- Os dados pessoais devem ser exportados em um formato comum e estruturado mediante a solicitação de uma entidade de dados.

## <a name="solutions"></a>Soluções

### <a name="azure-active-directory-rectifycorrect-inaccurate-or-incomplete-personal-data-and-erasedelete-personal-datauser-profiles"></a>Azure Active Directory: retificar/corrigir dados pessoais incorretos ou incompletos e apagar/excluir dados pessoais/perfis do usuário

O [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft.
É possível corrigir, atualizar ou excluir perfis do usuário de clientes e funcionários e informações de trabalho dos usuários que contêm dados pessoais, como o nome de um usuário, cargo, endereço ou número de telefone, no ambiente do [AAD](https://azure.microsoft.com/services/active-directory/) (Azure Active Directory) usando o [portal do Azure](https://portal.azure.com/).

Entre com uma conta que seja um administrador global do diretório.

#### <a name="how-do-i-correct-or-update-user-profile-and-work-information-in-azure-active-directory"></a>Como fazer para corrigir ou atualizar o perfil do usuário e as informações de trabalho no Azure Active Directory?

1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2. Escolha **Mais serviços**, insira **Usuários e grupos** na caixa de texto e selecione **Enter**.

    ![media/image1.png](media/manage-personal-data-azure/image001.png)

3. Na folha **Usuários e grupos**, selecione **Usuários**.

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. Na folha **Usuários e grupos – Usuários**, selecione um usuário na lista e, em seguida, na folha do usuário selecionado, selecione **Perfil** para exibir as informações de perfil do usuário que precisam ser corrigidas ou atualizadas.

    ![media/image3.png](media/manage-personal-data-azure/image005.png)

5. Corrija ou atualize as informações e, em seguida, na barra de comandos, selecione **Salvar.**

6.  Na folha do usuário selecionado, selecione **Informações de Trabalho** para exibir as informações de trabalho dos usuários que precisam ser corrigidas ou atualizadas.

    ![media/image4.png](media/manage-personal-data-azure/image007.png)

7. Corrija ou atualize as informações de trabalho dos usuários e, em seguida, na barra de comandos, selecione **Salvar.**

#### <a name="how-do-i-delete-a-user-profile-in-azure-active-directory"></a>Como fazer para excluir um perfil do usuário do Azure Active Directory?

1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2. Escolha **Mais serviços**, insira **Usuários e grupos** na caixa de texto e selecione **Enter**.

    ![](media/manage-personal-data-azure/image001.png)

3. Na folha **Usuários e grupos**, selecione **Usuários**.

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. Na folha **Usuários e grupos - Usuários** , escolha um usuário na lista.

    ![media/image3.png](media/manage-personal-data-azure/image007.png)

5. Na folha para o usuário selecionado, escolha **Visão geral** e, na barra de comandos, escolha **Excluir**.

    ![](media/manage-personal-data-azure/image013.png)

### <a name="sql-database-rectifycorrect-inaccurate-or-incomplete-personal-data-erasedelete-personal-data-export-personal-data"></a>Banco de Dados SQL: retificar/corrigir dados pessoais incorretos ou incompletos; apagar/excluir dados pessoais; exportar dados pessoais 

O [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/?v=16.50) é um banco de dados de nuvem que ajuda os desenvolvedores a criar e manter aplicativos.

Os dados pessoais podem ser atualizados no [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/?v=16.50) usando consultas SQL padrão e eles também podem ser excluídos. Além disso, os dados pessoais podem ser exportados do Banco de Dados SQL usando uma variedade de métodos, incluindo o assistente para importação e exportação do SQL Server do Azure, e em uma variedade de formatos, incluindo um arquivo BACPAC.

#### <a name="how-do-i-correct-update-or-erase-personal-data-in-sql-database"></a>Como fazer para corrigir, atualizar ou apagar dados pessoais do Banco de Dados SQL?

Para saber como corrigir ou atualizar dados pessoais no Banco de Dados SQL, visite a documentação [Atualizar (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql), [Atualizar Texto](https://docs.microsoft.com/sql/t-sql/queries/updatetext-transact-sql), [Atualizar com Expressão de Tabela Comum](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql) ou [Atualizar Texto de Gravação](https://docs.microsoft.com/sql/t-sql/queries/writetext-transact-sql).

Para saber como excluir dados pessoais do Banco de Dados SQL, visite a documentação [Excluir (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql).

#### <a name="how-do-i-export-personal-data-to-a-bacpac-file-in-sql-database"></a>Como fazer para exportar dados pessoais para um arquivo BACPAC no Banco de Dados SQL?

Um arquivo BACPAC inclui os dados e os metadados do Banco de Dados SQL e é um arquivo zip com uma extensão BACPAC. Faça isso usando o [portal do Azure](https://portal.azure.com/), o utilitário de linha de comando SQLPackage, o SSMS (SQL Server Management Studio) ou o PowerShell.

Para saber como exportar dados para um arquivo BACPAC, visite a página [Exportar um banco de dados SQL do Azure para um arquivo BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-export), que inclui instruções detalhadas para cada método listado acima.

Como fazer para exportar dados pessoais do Banco de Dados SQL com o Assistente para Importação e Exportação do SQL Server?

Esse assistente ajuda você a copiar dados de uma origem para um destino. Para obter uma introdução ao assistente, incluindo como obtê-lo, informações sobre permissões e como obter ajuda com a ferramenta, visite a página da Web [Importar e exportar dados com o Assistente para Importação e Exportação do SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard).

Para obter uma visão geral das etapas do assistente, visite a página da Web [Etapas do Assistente para Importação e Exportação do SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/steps-in-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Próximas etapas:

[Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/?v=16.50) 

[Active Directory do Azure](https://azure.microsoft.com/services/active-directory/)

