---
title: 'Tutorial: Criar seu primeiro banco de dados relacional no Banco de Dados SQL do Azure usando o SSMS | Microsoft Docs'
description: Aprenda a criar seu primeiro banco de dados relacional em um banco de dados individual no Banco de Dados SQL do Azure usando o SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 02/08/2019
ms.openlocfilehash: bbd009d127eecc4df357fc1073ba4055d13a8b2c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569011"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-using-ssms"></a>Tutorial: Criar um banco de dados relacional em um banco de dados individual dentro do Banco de Dados SQL do Azure usando o SSMS

O Banco de Dados SQL do Azure é um DBaaS (banco de dados como serviço) no Microsoft Cloud (Azure). Neste tutorial, você aprenderá a usar o Portal do Azure e o SSMS ([SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)) para:

> [!div class="checklist"]
> - Criar um banco de dados individual usando o portal do Azure*
> - Configurar uma regra de firewall de IP de nível de servidor usando o portal do Azure
> - Conectar-se ao banco de dados com o SSMS
> - Criar tabelas com SSMS
> - Carregar dados em massa com o BCP
> - Consultar dados com SSMS

*Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!NOTE]
> Para os fins deste tutorial, estamos usando um banco de dados individual. Você também pode usar um banco de dados em pool em um pool elástico ou um banco de dados de instância em uma instância gerenciada. Para a conectividade com uma instância gerenciada, confirme esses inícios rápidos de instância gerenciada: [Início Rápido: Configurar a VM do Azure para conectar a uma Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-configure-vm.md) e [Início Rápido: Configurar uma conexão ponto a site para uma Instância Gerenciada do Banco de Dados SQL do Azure do local](sql-database-managed-instance-configure-p2s.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se você instalou:

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (versão mais recente)
- [BCP e SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (versão mais recente)

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

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

## <a name="connect-to-the-database"></a>Conectar-se ao banco de dados

Use o [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) para estabelecer uma conexão com seu banco de dados individual.

1. Abra o SQL Server Management Studio.
2. Na caixa de diálogo **Conectar ao Servidor**, insira as informações a seguir:

   | Configuração       | Valor sugerido | DESCRIÇÃO |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Tipo de servidor** | Mecanismo de banco de dados | Esse valor é obrigatório. |
   | **Nome do servidor** | O nome do servidor totalmente qualificado | Por exemplo, *yourserver.database.windows.net*. |
   | **Autenticação** | Autenticação do SQL Server | A Autenticação do SQL é o único tipo de autenticação que configuramos neste tutorial. |
   | **Logon** | A conta do administrador do servidor | A conta que você especificou quando criou o servidor. |
   | **Senha** | A senha para sua conta do administrador do servidor | A senha que você especificou quando criou o servidor. |

   ![conectar-se ao servidor](./media/sql-database-design-first-database/connect.png)

3. Clique em **Opções** na caixa de diálogo **Conectar servidor**. Na seção **Conectar ao banco de dados**, digite *yourDatabase* para conectar-se a este banco de dados.

    ![conectar o banco de dados no servidor](./media/sql-database-design-first-database/options-connect-to-db.png)  

4. Clique em **Conectar**. A janela **Pesquisador de Objetos** abre no SSMS.

5. No **Pesquisador de Objetos**, expanda **Bancos de Dados** e expanda *yourDatabase* para exibir os objetos no banco de dados de exemplo.

   ![objetos de banco de dados](./media/sql-database-design-first-database/connected.png)  

## <a name="create-tables-in-your-database"></a>Criar tabelas no banco de dados

Criar um esquema de banco de dados com quatro tabelas que modelam um sistema de gerenciamento de aluno para universidades, usando o [Transact-SQL](/sql/t-sql/language-reference):

- Pessoa
- Curso
- Aluno
- Crédito

O diagrama a seguir mostra como essas tabelas estão relacionadas. Algumas dessas tabelas fazem referência a colunas em outras tabelas. Por exemplo, a tabela *Student* faz referência à coluna *PersonId* da tabela *Person*. Estude o diagrama para entender como as tabelas neste tutorial estão relacionadas umas com as outras. Para obter uma visão detalhada de como criar tabelas de banco de dados eficientes, consulte [Criar tabelas de banco de dados eficientes](https://msdn.microsoft.com/library/cc505842.aspx). Para obter informações sobre como escolher tipos de dados, consulte [Tipos de dados](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Você também pode usar o [designer de tabela no SQL Server Management Studio](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools) para criar e projetar suas tabelas.

![Relações de tabela](./media/sql-database-design-first-database/tutorial-database-tables.png)

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

   ![Criar tabelas](./media/sql-database-design-first-database/create-tables.png)

3. Expanda o nó **Tabelas** em *yourDatabase* no **Pesquisador de Objetos** para ver as tabelas que você criou.

   ![ssms tabelas criadas](./media/sql-database-design-first-database/ssms-tables-created.png)

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

Execute as seguintes consultas para recuperar as informações das tabelas do banco de dados. Confira [Escrever consultas SQL](https://technet.microsoft.com/library/bb264565.aspx) para saber mais sobre como escrever consultas SQL. A primeira consulta une todas as quatro tabelas para localizar os alunos ensinados por 'Dominick Pope' e que têm uma nota superior a 75%. A segunda consulta une todas as quatro tabelas e localiza os cursos em que 'Noe Coleman' já se registrou.

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

Neste tutorial, você aprendeu muitas tarefas básicas de banco de dados. Você aprendeu como:

> [!div class="checklist"]
> - Criar um banco de dados individual
> - Configurar uma regra de firewall de IP no nível do servidor
> - Conecte-se ao banco de dados com o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS.exe)
> - Criar tabelas
> - Carregar dados em massa
> - Consultar os dados

Avance para o próximo tutorial para saber mais sobre como criar um banco de dados usando Visual Studio e C#.

> [!div class="nextstepaction"]
> [Criar um banco de dados relacional em um banco de dados individual dentro de C# e ADO.NET do Banco de Dados SQL do Azure usando](sql-database-design-first-database-csharp.md)
