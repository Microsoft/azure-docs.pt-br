---
title: Criar seu primeiro banco de dados relacional – C# – Banco de Dados SQL do Azure | Microsoft Docs
description: Aprenda a criar seu primeiro banco de dados relacional em um banco de dados individual no Banco de Dados SQL do Azure com o C# usando o ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.openlocfilehash: f6f0160e06ac10a6f1e4e51c93bfa133818117c8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568973"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-cx23-and-adonet"></a>Tutorial: Criar um banco de dados relacionam em um banco de dados individual dentro de C&#x23; e ADO.NET do Banco de Dados SQL do Azure

O Banco de Dados SQL do Azure é um DBaaS (banco de dados como serviço) no Microsoft Cloud (Azure). Neste tutorial, você aprenderá a usar o portal do Azure e o ADO.NET com o Visual Studio para:

> [!div class="checklist"]
> * Criar um banco de dados individual usando o portal do Azure*
> * Configurar uma regra de firewall de IP de nível de servidor usando o portal do Azure
> * Conecte-se ao banco de dados com o ADO.NET e o Visual Studio
> * Criar tabelas com o ADO.NET
> * Inserir, atualizar e excluir dados com o ADO.NET
> * Consultar ADO.NET de dados

*Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Uma instalação do [Visual Studio 2019](https://www.visualstudio.com/downloads/) ou posterior.

## <a name="create-a-blank-single-database"></a>Criar um banco de dados em branco

Um banco de dados individual no Banco de Dados SQL do Azure é criado com um conjunto definido de recursos de armazenamento e de computação. O banco de dados é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) e é gerenciado usando um [servidor de banco de dados](sql-database-servers.md).

Siga estas etapas para criar um banco de dados individual em branco.

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Na página **Novo**, selecione **Bancos de Dados** na seção do Azure Marketplace e, em seguida, clique em **Banco de Dados SQL** na seção **Em Destaque**.

   ![criar banco de dados vazio](./media/sql-database-design-first-database/create-empty-database.png)

3. Preencha o formulário do **Banco de Dados SQL** com as informações abaixo, conforme mostrado na imagem anterior:

    | Configuração       | Valor sugerido | DESCRIÇÃO |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nome do banco de dados** | *yourDatabase* | Para ver os nomes do banco de dados válidos, confira [Identificadores do banco de dados](/sql/relational-databases/databases/database-identifiers). |
    | **Assinatura** | *yourSubscription*  | Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions). |
    | **Grupo de recursos** | *yourResourceGroup* | Para ver os nomes do grupo de recursos válidos, consulte [Regras e restrições de nomenclatura](/azure/architecture/best-practices/naming-conventions). |
    | **Selecionar fonte** | Banco de dados em branco | Especifica que um banco de dados em branco deve ser criado. |

4. Clique em **Servidor** para usar um servidor de banco de dados existente ou criar e configurar um novo servidor de banco de dados. Selecione um servidor existente ou clique em **Criar um servidor** e preencha o formulário **Novo servidor** com as seguintes informações:

    | Configuração       | Valor sugerido | DESCRIÇÃO |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nome do servidor** | Qualquer nome exclusivo globalmente | Para ver os nomes do servidor válidos, consulte [Regras e restrições de nomenclatura](/azure/architecture/best-practices/naming-conventions). |
    | **Logon de administrador do servidor** | Qualquer nome válido | Para ver os nomes de logon válidos, confira [Identificadores do banco de dados](/sql/relational-databases/databases/database-identifiers). |
    | **Senha** | Qualquer senha válida | Sua senha deve ter, pelo menos, oito caracteres e deve usar caracteres de três das seguintes categorias: caracteres com letras maiúsculas, letras minúsculas, números e caracteres não alfanuméricos. |
    | **Localidade** | Qualquer local válido | Para obter mais informações sobre as regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/). |

    ![criar database-server](./media/sql-database-design-first-database/create-database-server.png)

5. Clique em **Selecionar**.
6. Clique em **Tipo de preço** para especificar a camada de serviço, o número de DTUs ou vCores e a quantidade de armazenamento. Você pode explorar as opções para o número de DTUs/vCores e o armazenamento disponível em cada camada de serviço.

    Depois de selecionar a camada de serviço, o número de DTUs ou vCores e a quantidade de armazenamento, clique em **Aplicar**.

7. Insira uma **Ordenação** para o banco de dados em branco (para este tutorial, use o valor padrão). Para obter mais informações sobre ordenações, confira [Ordenações](/sql/t-sql/statements/collations)

8. Agora que você concluiu o formulário do **Banco de Dados SQL**, clique em **Criar** para provisionar o banco de dados individual. Esta etapa pode levar alguns minutos.

9. Na barra de ferramentas, clique em **Notificações** para monitorar o processo de implantação.

   ![notificação](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Criar uma regra de firewall de IP no nível do servidor

O serviço do Banco de Dados SQL cria um firewall de IP no nível do servidor. Esse firewall impede que os aplicativos e ferramentas externos se conectem ao servidor e aos bancos de dados no servidor, a menos que uma regra de firewall permita seu IP por meio do firewall. Para habilitar a conectividade externa com seu banco de dados individual, primeiro adicione uma regra de firewall de IP ao seu endereço IP (ou intervalo de endereços IP). Siga estas etapas para criar uma [Regra de firewall de IP no nível do servidor de Banco de Dados SQL](sql-database-firewall-configure.md).

> [!IMPORTANT]
> O serviço de Banco de Dados SQL se comunica pela porta 1433. Se você estiver tentando se conectar a esse serviço de dentro de uma rede corporativa, o tráfego de saída pela porta 1433 poderá não ser permitido pelo firewall da sua rede. Se isso acontecer, você não poderá conectar-se ao banco de dados individual, a menos que o administrador abra a porta 1433.

1. Depois da implantação ser concluída, clique em **Bancos de dados SQL** no menu à esquerda, depois, clique em *yourDatabase* na página **Bancos de dados SQL**. A página de visão geral de seu banco de dados é aberta, mostrando o **nome do servidor** totalmente qualificado (como *yourserver.database.windows.net*) e fornece opções para configurações adicionais.

2. Copie esse nome totalmente qualificado do servidor para usá-lo para se conectar ao seu servidor e bancos de dados do SQL Server Management Studio.

   ![nome do servidor](./media/sql-database-design-first-database/server-name.png)

3. Clique em **Definir firewall do servidor** na barra de ferramentas. A página **Configurações do firewall** do servidor do Banco de Dados SQL é aberta.

   ![regra de firewall de IP no nível do servidor](./media/sql-database-design-first-database/server-firewall-rule.png)

4. Clique em **Adicionar IP do cliente** na barra de ferramentas para adicionar seu endereço IP atual a uma nova regra de firewall de IP. Uma regra de firewall de IP pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP.

5. Clique em **Save** (Salvar). Uma regra de firewall de IP no nível do servidor é criada para o endereço IP atual que abre a porta 1433 no servidor do Banco de Dados SQL.

6. Clique em **OK**, em seguida, feche a página **Configurações do Firewall**.

Agora seu endereço IP pode passar pelo firewall de IP. Agora é possível se conectar ao seu banco de dados individual usando o SQL Server Management Studio ou outra ferramenta de sua escolha. Use a conta do administrador do servidor criada anteriormente.

> [!IMPORTANT]
> Por padrão, o acesso por meio do firewall de IP do Banco de Dados SQL é habilitado para todos os serviços do Azure. Clique em **DESATIVAR** nesta página para desabilitar todos os serviços do Azure.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu tarefas básicas de banco de dados, como criar um banco de dados e tabelas, conectar-se ao banco de dados, carregar os dados e executar consultas. Você aprendeu como:

> [!div class="checklist"]
> * Criar um banco de dados
> * Configurar uma regra de firewall
> * Conectar-se ao banco de dados com o [Visual Studio e C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Criar tabelas
> * Inserir, atualizar, excluir e consultar dados

Avança para o próximo tutorial para saber mais sobre migração de dados.

> [!div class="nextstepaction"]
> [Migrar SQL Server para Banco de Dados SQL do Azure offline usando DMS](../dms/tutorial-sql-server-to-azure-sql.md)
