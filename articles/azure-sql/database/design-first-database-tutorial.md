---
title: 'Tutorial: Criar seu primeiro banco de dados relacional usando o SSMS'
description: Aprenda a criar seu primeiro banco de dados relacional em no Banco de Dados SQL do Azure usando o SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 07/29/2019
ms.custom: sqldbrb=1
ms.openlocfilehash: ae7baeac6cee2a692928642e3e38ce0adad17d1c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674882"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-using-ssms"></a>Tutorial: Criar um banco de dados relacional no Banco de Dados SQL do Azure usando o SSMS
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]


O Banco de Dados SQL do Azure é um DBaaS (banco de dados como serviço) no Microsoft Cloud (Azure). Neste tutorial, você aprenderá a usar o Portal do Azure e o SSMS ([SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)) para:

> [!div class="checklist"]
>
> - Criar um banco de dados usando o portal do Azure*
> - Configurar uma regra de firewall de IP de nível de servidor usando o portal do Azure
> - Conectar-se ao banco de dados com o SSMS
> - Criar tabelas com SSMS
> - Carregar dados em massa com o BCP
> - Consultar dados com SSMS

*Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!TIP]
> O módulo do Microsoft Learn a seguir ajuda você a aprender gratuitamente como [Desenvolver e configurar um aplicativo ASP.NET que consulta um Banco de Dados SQL do Azure](/learn/modules/develop-app-that-queries-azure-sql/), incluindo a criação de um banco de dados simples.
> [!NOTE]
> Para os fins deste tutorial, estamos usando o Banco de Dados SQL do Azure. Você também pode usar um banco de dados em pool em um pool elástico ou em uma Instância Gerenciada de SQL. Para conectividade com uma Instância Gerenciada de SQL, confirme esses inícios rápidos da Instância Gerenciada do SQL: [Início Rápido: Configurar a VM do Azure para conectar-se a uma Instância Gerenciada de SQL do Azure](../managed-instance/connect-vm-instance-configure.md) e [Início Rápido: Configurar uma conexão ponto a site a uma Instância Gerenciada de SQL do Azure do local](../managed-instance/point-to-site-p2s-configure.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se você instalou:

- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (versão mais recente)
- [BCP e SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (versão mais recente)

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-blank-database-in-azure-sql-database"></a>Criar um banco de dados em branco no Banco de Dados SQL do Azure

Um banco de dados no Banco de Dados SQL do Azure é criado com um conjunto definido de recursos de armazenamento e de computação. O banco de dados é criado dentro de um [grupo de recursos do Azure](../../active-directory-b2c/overview.md) e é gerenciado usando um [servidor SQL lógico](logical-servers.md).

Siga estas etapas para criar um banco de dados em branco.

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.
2. Na página **Novo**, selecione **Bancos de Dados** na seção do Azure Marketplace e, em seguida, clique em **Banco de Dados SQL** na seção **Em Destaque**.

   ![criar banco de dados vazio](./media/design-first-database-tutorial/create-empty-database.png)

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

    ![criar database-server](./media/design-first-database-tutorial/create-database-server.png)

5. Clique em **Selecionar**.
6. Clique em **Tipo de preço** para especificar a camada de serviço, o número de DTUs ou vCores e a quantidade de armazenamento. Você pode explorar as opções para o número de DTUs/vCores e o armazenamento disponível em cada camada de serviço.

    Depois de selecionar a camada de serviço, o número de DTUs ou vCores e a quantidade de armazenamento, clique em **Aplicar**.

7. Insira uma **Ordenação** para o banco de dados em branco (para este tutorial, use o valor padrão). Para obter mais informações sobre ordenações, confira [Ordenações](/sql/t-sql/statements/collations)

8. Agora que você concluiu o formulário do **Banco de Dados SQL**, clique em **Criar** para provisionar o banco de dados. Esta etapa pode levar alguns minutos.

9. Na barra de ferramentas, clique em **Notificações** para monitorar o processo de implantação.

   ![A captura de tela mostra o menu Notificações com a Implantação em andamento.](./media/design-first-database-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Criar uma regra de firewall de IP no nível do servidor

O Banco de Dados SQL do Azure cria um firewall de IP no nível do servidor. Esse firewall impede que os aplicativos e ferramentas externos se conectem ao servidor e aos bancos de dados no servidor, a menos que uma regra de firewall permita seu IP por meio do firewall. Para habilitar a conectividade externa com seu banco de dados, primeiro adicione uma regra de firewall de IP ao seu endereço IP (ou intervalo de endereços IP). Siga estas etapas para criar uma [regra de firewall de IP no nível do servidor](firewall-configure.md).

> [!IMPORTANT]
> O Banco de Dados SQL do Azure se comunica pela porta 1433. Se você estiver tentando se conectar a esse serviço de dentro de uma rede corporativa, o tráfego de saída pela porta 1433 poderá não ser permitido pelo firewall da sua rede. Se isso acontecer, você não poderá conectar-se ao banco de dados, a menos que o administrador abra a porta 1433.

1. Após a conclusão da implantação, selecione **bancos de dados SQL** no menu do portal do Azure ou pesquise e selecione *bancos de dados SQL* em qualquer página.  

1. Selecione *yourDatabase* na página **bancos de dados SQL**. A página de visão geral do seu banco de dados é aberta, mostrando o **nome do servidor** totalmente qualificado (tal como `contosodatabaseserver01.database.windows.net`) e fornece opções para configurações adicionais.

   ![nome do servidor](./media/design-first-database-tutorial/server-name.png)

1. Copie esse nome totalmente qualificado do servidor para usá-lo para se conectar ao seu servidor e bancos de dados do SQL Server Management Studio.

1. Clique em **Definir firewall do servidor** na barra de ferramentas. A página **Configurações do firewall** do servidor será aberta.

   ![regra de firewall de IP no nível do servidor](./media/design-first-database-tutorial/server-firewall-rule.png)

1. Clique em **Adicionar IP do cliente** na barra de ferramentas para adicionar seu endereço IP atual a uma nova regra de firewall de IP. Uma regra de firewall de IP pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP.

1. Clique em **Save** (Salvar). Uma regra de firewall de IP no nível do servidor é criada para o endereço IP atual que abre a porta 1433 no servidor.

1. Clique em **OK**, em seguida, feche a página **Configurações do Firewall**.

Agora seu endereço IP pode passar pelo firewall de IP. Agora, é possível se conectar ao seu banco de dados usando o SQL Server Management Studio ou outra ferramenta de sua escolha. Use a conta do administrador do servidor criada anteriormente.

> [!IMPORTANT]
> Por padrão, o acesso por meio do firewall de IP do Banco de Dados SQL é habilitado para todos os serviços do Azure. Clique em **DESATIVAR** nesta página para desabilitar todos os serviços do Azure.

## <a name="connect-to-the-database"></a>Conectar-se ao banco de dados

Use o [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) para estabelecer uma conexão com seu banco de dados.

1. Abra o SQL Server Management Studio.
2. Na caixa de diálogo **Conectar ao Servidor**, insira as informações a seguir:

   | Configuração       | Valor sugerido | Descrição |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Tipo de servidor** | Mecanismo de banco de dados | Esse valor é necessário. |
   | **Nome do servidor** | O nome do servidor totalmente qualificado | Por exemplo, *yourserver.database.windows.net*. |
   | **Autenticação** | Autenticação do SQL Server | A Autenticação do SQL é o único tipo de autenticação que configuramos neste tutorial. |
   | **Logon** | A conta do administrador do servidor | A conta que você especificou quando criou o servidor. |
   | **Senha** | A senha para sua conta do administrador do servidor | A senha que você especificou quando criou o servidor. |

   ![conectar-se ao servidor](./media/design-first-database-tutorial/connect.png)

3. Clique em **Opções** na caixa de diálogo **Conectar servidor**. Na seção **Conectar ao banco de dados**, digite *yourDatabase* para conectar-se a este banco de dados.

    ![conectar o banco de dados no servidor](./media/design-first-database-tutorial/options-connect-to-db.png)  

4. Clique em **Conectar**. A janela **Pesquisador de Objetos** abre no SSMS.

5. No **Pesquisador de Objetos**, expanda **Bancos de Dados** e expanda *yourDatabase* para exibir os objetos no banco de dados de exemplo.

   ![objetos de banco de dados](./media/design-first-database-tutorial/connected.png)  

## <a name="create-tables-in-your-database"></a>Criar tabelas no banco de dados

Criar um esquema de banco de dados com quatro tabelas que modelam um sistema de gerenciamento de aluno para universidades, usando o [Transact-SQL](/sql/t-sql/language-reference):

- Person
- Curso
- Aluno
- Crédito

O diagrama a seguir mostra como essas tabelas estão relacionadas. Algumas dessas tabelas fazem referência a colunas em outras tabelas. Por exemplo, a tabela *Student* faz referência à coluna *PersonId* da tabela *Person*. Estude o diagrama para entender como as tabelas neste tutorial estão relacionadas umas com as outras. Para obter uma visão detalhada de como criar tabelas de banco de dados eficientes, consulte [Criar tabelas de banco de dados eficientes](/previous-versions/tn-archive/cc505842(v=technet.10)). Para obter informações sobre como escolher tipos de dados, consulte [Tipos de dados](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Você também pode usar o [designer de tabela no SQL Server Management Studio](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools) para criar e projetar suas tabelas.

![Relações de tabela](./media/design-first-database-tutorial/tutorial-database-tables.png)

1. No **Pesquisador de Objetos**, clique com o botão direito do mouse em *yourDatabase* e selecione **Nova Consulta**. Uma janela de consulta em branco conectada ao seu banco de dados é aberta.

2. Na janela de consulta, execute a consulta a seguir para criar quatro tabelas em seu banco de dados:

   ```sql
   -- Create Person table
   CREATE TABLE Person
   (
       PersonId INT IDENTITY PRIMARY KEY,
       FirstName NVARCHAR(128) NOT NULL,
       MiddelInitial NVARCHAR(10),
       LastName NVARCHAR(128) NOT NULL,
       DateOfBirth DATE NOT NULL
   )

   -- Create Student table
   CREATE TABLE Student
   (
       StudentId INT IDENTITY PRIMARY KEY,
       PersonId INT REFERENCES Person (PersonId),
       Email NVARCHAR(256)
   )

   -- Create Course table
   CREATE TABLE Course
   (
       CourseId INT IDENTITY PRIMARY KEY,
       Name NVARCHAR(50) NOT NULL,
       Teacher NVARCHAR(256) NOT NULL
   )

   -- Create Credit table
   CREATE TABLE Credit
   (
       StudentId INT REFERENCES Student (StudentId),
       CourseId INT REFERENCES Course (CourseId),
       Grade DECIMAL(5,2) CHECK (Grade <= 100.00),
       Attempt TINYINT,
       CONSTRAINT [UQ_studentgrades] UNIQUE CLUSTERED
       (
           StudentId, CourseId, Grade, Attempt
       )
   )
   ```

   ![Criar tabelas](./media/design-first-database-tutorial/create-tables.png)

3. Expanda o nó **Tabelas** em *yourDatabase* no **Pesquisador de Objetos** para ver as tabelas que você criou.

   ![ssms tabelas criadas](./media/design-first-database-tutorial/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Carregar dados nas tabelas

1. Crie uma pasta chamada *sampleData* na pasta Downloads para armazenar os dados de exemplo de seu banco de dados.

2. Clique com o botão direito do mouse nos seguintes links e salve-os na pasta *sampleData*.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Abra uma janela de prompt de comando e navegue até a pasta *sampleData*.

4. Execute o comando a seguir para inserir dados de exemplo nas tabelas, substituindo os valores de *server*, *database*, *user* e *password* pelos valores do seu ambiente.

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Agora, você carregou dados de exemplo nas tabelas que criou anteriormente.

## <a name="query-data"></a>Consultar dados

Execute as seguintes consultas para recuperar as informações das tabelas do banco de dados. Confira [Escrever consultas SQL](/previous-versions/sql/sql-server-2005/express-administrator/bb264565(v=sql.90)) para saber mais sobre como escrever consultas SQL. A primeira consulta une todas as quatro tabelas para localizar os alunos ensinados por 'Dominick Pope' e que têm uma nota superior a 75%. A segunda consulta une todas as quatro tabelas e localiza os cursos em que 'Noe Coleman' já se registrou.

1. Em uma janela de consulta do SQL Server Management Studio, execute a seguinte consulta:

   ```sql
   -- Find the students taught by Dominick Pope who have a grade higher than 75%
   SELECT  person.FirstName, person.LastName, course.Name, credit.Grade
   FROM  Person AS person
       INNER JOIN Student AS student ON person.PersonId = student.PersonId
       INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
       INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope'
       AND Grade > 75
   ```

2. Em uma janela de consulta, execute a seguinte consulta:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled
   SELECT  course.Name, course.Teacher, credit.Grade
   FROM  Course AS course
       INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
       INNER JOIN Student AS student ON student.StudentId = credit.StudentId
       INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
       AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu muitas tarefas básicas de banco de dados. Você aprendeu a:

> [!div class="checklist"]
>
> - Criar um banco de dados usando o portal do Azure*
> - Configurar uma regra de firewall de IP de nível de servidor usando o portal do Azure
> - Conectar-se ao banco de dados com o SSMS
> - Criar tabelas com SSMS
> - Carregar dados em massa com o BCP
> - Consultar dados com SSMS

Avance para o próximo tutorial para saber mais sobre como criar um banco de dados usando Visual Studio e C#.

> [!div class="nextstepaction"]
> [Criar um banco de dados relacional em um Banco de Dados SQL do Azure em C# e no ADO.NET](design-first-database-csharp-tutorial.md)