---
title: Criar seu primeiro banco de dados relacional C#
description: Aprenda a criar seu primeiro banco de dados relacional em um Banco de Dados SQL do Azure com o C# usando o ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-csharp
ms.topic: tutorial
author: rothja
ms.author: jroth
ms.reviewer: sstein
ms.date: 07/29/2019
ms.openlocfilehash: 67d20a4f0f5d2dae769429c9c476899ea8099c43
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551673"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-cx23-and-adonet"></a>Tutorial: Criar um banco de dados relacional em um Banco de Dados SQL do Azure em C&#x23; e ADO.NET
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

O Banco de Dados SQL do Azure é um DBaaS (banco de dados como serviço) no Microsoft Cloud (Azure). Neste tutorial, você aprenderá a usar o portal do Azure e o ADO.NET com o Visual Studio para:

> [!div class="checklist"]
>
> * Criar um banco de dados usando o portal do Azure
> * Configurar uma regra de firewall de IP de nível de servidor usando o portal do Azure
> * Conecte-se ao banco de dados com o ADO.NET e o Visual Studio
> * Criar tabelas com o ADO.NET
> * Inserir, atualizar e excluir dados com o ADO.NET
> * Consultar ADO.NET de dados

*Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!TIP]
> O módulo do Microsoft Learn a seguir ajuda você a aprender gratuitamente como [Desenvolver e configurar um aplicativo ASP.NET que consulta um Banco de Dados SQL do Azure](/learn/modules/develop-app-that-queries-azure-sql/), incluindo a criação de um banco de dados simples.

## <a name="prerequisites"></a>Pré-requisitos

Uma instalação do [Visual Studio 2019](https://www.visualstudio.com/downloads/) ou posterior.

## <a name="create-a-blank-database-in-azure-sql-database"></a>Criar um banco de dados em branco no Banco de Dados SQL do Azure

Um banco de dados no Banco de Dados SQL do Azure é criado com um conjunto definido de recursos de armazenamento e de computação. O banco de dados é criado dentro de um [grupo de recursos do Azure](../../active-directory-b2c/overview.md) e é gerenciado usando um [servidor SQL lógico](logical-servers.md).

Siga estas etapas para criar um banco de dados em branco.

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Na página **Novo**, selecione **Bancos de Dados** na seção do Azure Marketplace e, em seguida, clique em **Banco de Dados SQL** na seção **Em Destaque**.

   ![criar banco de dados vazio](./media/design-first-database-csharp-tutorial/create-empty-database.png)

3. Preencha o formulário do **Banco de Dados SQL** com as informações abaixo, conforme mostrado na imagem anterior:

    | Configuração       | Valor sugerido | Descrição |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nome do banco de dados** | *yourDatabase* | Para ver os nomes do banco de dados válidos, confira [Identificadores do banco de dados](/sql/relational-databases/databases/database-identifiers). |
    | **Assinatura** | *yourSubscription*  | Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions). |
    | **Grupo de recursos** | *yourResourceGroup* | Para ver os nomes do grupo de recursos válidos, consulte [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming). |
    | **Selecionar fonte** | Banco de dados em branco | Especifica que um banco de dados em branco deve ser criado. |

4. Clique em **Servidor** para usar um servidor existente ou criar e configurar um novo servidor. Selecione um servidor existente ou clique em **Criar um servidor** e preencha o formulário **Novo servidor** com as seguintes informações:

    | Configuração       | Valor sugerido | Descrição |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nome do servidor** | Qualquer nome exclusivo globalmente | Para ver os nomes do servidor válidos, consulte [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming). |
    | **Logon de administrador do servidor** | Qualquer nome válido | Para ver os nomes de logon válidos, confira [Identificadores do banco de dados](/sql/relational-databases/databases/database-identifiers). |
    | **Senha** | Qualquer senha válida | Sua senha deve ter, pelo menos, oito caracteres e deve usar caracteres de três das seguintes categorias: caracteres com letras maiúsculas, letras minúsculas, números e caracteres não alfanuméricos. |
    | **Localidade** | Qualquer local válido | Para obter mais informações sobre as regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/). |

    ![criar database-server](./media/design-first-database-csharp-tutorial/create-database-server.png)

5. Clique em **Selecionar**.
6. Clique em **Tipo de preço** para especificar a camada de serviço, o número de DTUs ou vCores e a quantidade de armazenamento. Você pode explorar as opções para o número de DTUs/vCores e o armazenamento disponível em cada camada de serviço.

    Depois de selecionar a camada de serviço, o número de DTUs ou vCores e a quantidade de armazenamento, clique em **Aplicar**.

7. Insira uma **Ordenação** para o banco de dados em branco (para este tutorial, use o valor padrão). Para obter mais informações sobre ordenações, confira [Ordenações](/sql/t-sql/statements/collations)

8. Agora que você concluiu o formulário do **Banco de Dados SQL**, clique em **Criar** para provisionar o banco de dados. Esta etapa pode levar alguns minutos.

9. Na barra de ferramentas, clique em **Notificações** para monitorar o processo de implantação.

   ![A captura de tela mostra Notificações no portal do Azure com a Implantação em andamento.](./media/design-first-database-csharp-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Criar uma regra de firewall de IP no nível do servidor

O Banco de Dados SQL cria um firewall de IP no nível do servidor. Esse firewall impede que os aplicativos e ferramentas externos se conectem ao servidor e aos bancos de dados no servidor, a menos que uma regra de firewall permita seu IP por meio do firewall. Para habilitar a conectividade externa com seu banco de dados, primeiro adicione uma regra de firewall de IP ao seu endereço IP (ou intervalo de endereços IP). Siga estas etapas para criar uma [regra de firewall de IP no nível do servidor](firewall-configure.md).

> [!IMPORTANT]
> O Banco de Dados SQL se comunica pela porta 1433. Se você estiver tentando se conectar a esse serviço de dentro de uma rede corporativa, o tráfego de saída pela porta 1433 poderá não ser permitido pelo firewall da sua rede. Se isso acontecer, você não poderá conectar-se ao banco de dados, a menos que o administrador abra a porta 1433.

1. Depois da implantação ser concluída, clique em **Bancos de dados SQL** no menu à esquerda, depois, clique em *yourDatabase* na página **Bancos de dados SQL**. A página de visão geral de seu banco de dados é aberta, mostrando o **nome do servidor** totalmente qualificado (como *yourserver.database.windows.net*) e fornece opções para configurações adicionais.

2. Copie esse nome totalmente qualificado do servidor para usá-lo para se conectar ao seu servidor e bancos de dados do SQL Server Management Studio.

   ![nome do servidor](./media/design-first-database-csharp-tutorial/server-name.png)

3. Clique em **Definir firewall do servidor** na barra de ferramentas. A página **Configurações do firewall** do servidor será aberta.

   ![regra de firewall de IP no nível do servidor](./media/design-first-database-csharp-tutorial/server-firewall-rule.png)

4. Clique em **Adicionar IP do cliente** na barra de ferramentas para adicionar seu endereço IP atual a uma nova regra de firewall de IP. Uma regra de firewall de IP pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP.

5. Clique em **Save** (Salvar). Uma regra de firewall de IP no nível do servidor é criada para o endereço IP atual que abre a porta 1433 no servidor.

6. Clique em **OK**, em seguida, feche a página **Configurações do Firewall**.

Agora seu endereço IP pode passar pelo firewall de IP. Agora, é possível se conectar ao seu banco de dados usando o SQL Server Management Studio ou outra ferramenta de sua escolha. Use a conta do administrador do servidor criada anteriormente.

> [!IMPORTANT]
> Por padrão, o acesso por meio do firewall de IP do Banco de Dados SQL é habilitado para todos os serviços do Azure. Clique em **DESATIVAR** nesta página para desabilitar o acesso para todos os serviços do Azure.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu tarefas básicas de banco de dados, como criar um banco de dados e tabelas, conectar-se ao banco de dados, carregar os dados e executar consultas. Você aprendeu a:

> [!div class="checklist"]
>
> * Criar um banco de dados usando o portal do Azure
> * Configurar uma regra de firewall de IP de nível de servidor usando o portal do Azure
> * Conecte-se ao banco de dados com o ADO.NET e o Visual Studio
> * Criar tabelas com o ADO.NET
> * Inserir, atualizar e excluir dados com o ADO.NET
> * Consultar ADO.NET de dados

Avança para o próximo tutorial para saber mais sobre migração de dados.

> [!div class="nextstepaction"]
> [Migrar SQL Server para Banco de Dados SQL do Azure offline usando DMS](../../dms/tutorial-sql-server-to-azure-sql.md)