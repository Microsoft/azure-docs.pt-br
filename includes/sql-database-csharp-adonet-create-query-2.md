
<a name="cs_0_csharpprogramexample_h2"/>

## <a name="c-program-example"></a>Exemplo de programa em C#

As próximas seções deste artigo apresentam um programa C# que usa ADO.NET para enviar instruções Transact-SQL para o Banco de Dados SQL. O programa C# executa as seguintes ações:

1. [Conecta-se ao nosso Banco de Dados SQL usando o ADO.NET](#cs_1_connect).
2. [Cria tabelas](#cs_2_createtables).
3. [Preenche as tabelas com dados, emitindo instruções T-SQL INSERT](#cs_3_insert).
4. [Atualiza os dados pelo uso de uma união](#cs_4_updatejoin).
5. [Exclui os dados pelo uso de uma união](#cs_5_deletejoin).
6. [Seleciona as linhas de dados pelo uso de uma união](#cs_6_selectrows).
7. Fecha a conexão (que descarta todas as tabelas temporárias de tempdb).

O programa em C# contém:

- Código C# para se conectar ao banco de dados.
- Métodos que retornam o código-fonte T-SQL.
- Dois métodos que enviam o T-SQL para o banco de dados.

#### <a name="to-compile-and-run"></a>Para compilar e executar

Este programa C# é logicamente um arquivo .cs. Mas aqui o programa é dividido fisicamente em vários blocos de código, para facilitar a visualização e a compreensão de cada bloco. Para compilar e executar esse programa, faça o seguinte:

1. Crie um novo projeto em C# no Visual Studio.
    - O tipo de projeto deve ser um aplicativo de *console*, de algo parecido com a seguinte hierarquia: **Modelos** > **Visual C#** > **Área de Trabalho Clássica do Windows** > **Aplicativo de Console (.NET Framework)**.
3. No arquivo **Program.cs**, apague as linhas iniciais do código.
3. Em Program.cs, copie e cole cada um dos blocos a seguir, na mesma sequência em que são apresentados aqui.
4. Em Program.cs, edite os seguintes valores no método **principal**:

   - **cb.DataSource**
   - **cd.UserID**
   - **cb.Password**
   - **InitialCatalog**

5. Verifique se o assembly **System.Data.dll** é referenciado. Para verificar, expanda o nó **Referências** no painel **Gerenciador de Soluções**.
6. Para criar o programa no Visual Studio, clique no menu **Criar**.
7. Para executar o programa do Visual Studio, clique no botão **Iniciar**. A saída do relatório é exibida em uma janela cmd.exe.

> [!NOTE]
> Você tem a opção de editar a T-SQL para adicionar um líder  **#**  aos nomes de tabela, que cria como tabelas temporárias em **tempdb**. Isso pode ser útil para fins de demonstração, quando nenhum banco de dados de teste está disponível. As tabelas temporárias são excluídas automaticamente quando a conexão é fechada. Nenhuma REFERÊNCIA a chaves estrangeiras é imposta para tabelas temporárias.
>

<a name="cs_1_connect"/>
### <a name="c-block-1-connect-by-using-adonet"></a>Bloco C# 1: conectar-se usando o ADO.NET

- [Próximo](#cs_2_createtables)


```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll 
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
   class Program
   {
      static void Main(string[] args)
      {
         try
         {
            var cb = new SqlConnectionStringBuilder();
            cb.DataSource = "your_server.database.windows.net";
            cb.UserID = "your_user";
            cb.Password = "your_password";
            cb.InitialCatalog = "your_database";

            using (var connection = new SqlConnection(cb.ConnectionString))
            {
               connection.Open();

               Submit_Tsql_NonQuery(connection, "2 - Create-Tables",
                  Build_2_Tsql_CreateTables());

               Submit_Tsql_NonQuery(connection, "3 - Inserts",
                  Build_3_Tsql_Inserts());

               Submit_Tsql_NonQuery(connection, "4 - Update-Join",
                  Build_4_Tsql_UpdateJoin(),
                  "@csharpParmDepartmentName", "Accounting");

               Submit_Tsql_NonQuery(connection, "5 - Delete-Join",
                  Build_5_Tsql_DeleteJoin(),
                  "@csharpParmDepartmentName", "Legal");

               Submit_6_Tsql_SelectEmployees(connection);
            }
         }
         catch (SqlException e)
         {
            Console.WriteLine(e.ToString());
         }
         Console.WriteLine("View the report output here, then press any key to end the program...");
         Console.ReadKey();
      }
```


<a name="cs_2_createtables"/>
### <a name="c-block-2-t-sql-to-create-tables"></a>Bloco C# 2: T-SQL para criar tabelas

- [Anterior](#cs_1_connect) &nbsp; / &nbsp; [Avançar](#cs_3_insert)

```csharp
      static string Build_2_Tsql_CreateTables()
      {
         return @"
DROP TABLE IF EXISTS tabEmployee;
DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.


CREATE TABLE tabDepartment
(
   DepartmentCode  nchar(4)          not null
      PRIMARY KEY,
   DepartmentName  nvarchar(128)     not null
);

CREATE TABLE tabEmployee
(
   EmployeeGuid    uniqueIdentifier  not null  default NewId()
      PRIMARY KEY,
   EmployeeName    nvarchar(128)     not null,
   EmployeeLevel   int               not null,
   DepartmentCode  nchar(4)              null
      REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
);
";
      }
```

#### <a name="entity-relationship-diagram-erd"></a>Diagrama de relação de entidade (ERD)

As instruções CREATE TABLE precedentes envolvem a palavra-chave **REFERÊNCIAS** para criar uma relação *chave estrangeira* (FK) entre duas tabelas.  Se você estiver usando o tempdb, comente a palavra-chave `--REFERENCES` usando um par de traços à esquerda.

Em seguida, é um ERD que exibe a relação entre as duas tabelas. Os valores da coluna *filho* #tabEmployee.DepartmentCode são limitados aos valores presentes na coluna *pai* #tabDepartment.Department.

![ERD mostrando chave estrangeira](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)


<a name="cs_3_insert"/>
### <a name="c-block-3-t-sql-to-insert-data"></a>Bloco C# 3: T-SQL para inserir dados

- [Anterior](#cs_2_createtables) &nbsp; / &nbsp; [Avançar](#cs_4_updatejoin)


```csharp
      static string Build_3_Tsql_Inserts()
      {
         return @"
-- The company has these departments.
INSERT INTO tabDepartment
   (DepartmentCode, DepartmentName)
      VALUES
   ('acct', 'Accounting'),
   ('hres', 'Human Resources'),
   ('legl', 'Legal');

-- The company has these employees, each in one department.
INSERT INTO tabEmployee
   (EmployeeName, EmployeeLevel, DepartmentCode)
      VALUES
   ('Alison'  , 19, 'acct'),
   ('Barbara' , 17, 'hres'),
   ('Carol'   , 21, 'acct'),
   ('Deborah' , 24, 'legl'),
   ('Elle'    , 15, null);
";
      }
```


<a name="cs_4_updatejoin"/>
### <a name="c-block-4-t-sql-to-update-join"></a>Bloco C# 4: T-SQL para atualizar com união

- [Anterior](#cs_3_insert) &nbsp; / &nbsp; [Avançar](#cs_5_deletejoin)


```csharp
      static string Build_4_Tsql_UpdateJoin()
      {
         return @"
DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';


-- Promote everyone in one department (see @parm...).
UPDATE empl
   SET
      empl.EmployeeLevel += 1
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName1;
";
      }
```


<a name="cs_5_deletejoin"/>
### <a name="c-block-5-t-sql-to-delete-join"></a>Bloco C# 5: T-SQL para excluir com união

- [Anterior](#cs_4_updatejoin) &nbsp; / &nbsp; [Avançar](#cs_6_selectrows)


```csharp
      static string Build_5_Tsql_DeleteJoin()
      {
         return @"
DECLARE @DName2  nvarchar(128);
SET @DName2 = @csharpParmDepartmentName;  --'Legal';


-- Right size the Legal department.
DELETE empl
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName2

-- Disband the Legal department.
DELETE tabDepartment
   WHERE DepartmentName = @DName2;
";
      }
```



<a name="cs_6_selectrows"/>
### <a name="c-block-6-t-sql-to-select-rows"></a>Bloco C# 6: T-SQL para selecionar linhas

- [Anterior](#cs_5_deletejoin) &nbsp; / &nbsp; [Avançar](#cs_6b_datareader)


```csharp
      static string Build_6_Tsql_SelectEmployees()
      {
         return @"
-- Look at all the final Employees.
SELECT
      empl.EmployeeGuid,
      empl.EmployeeName,
      empl.EmployeeLevel,
      empl.DepartmentCode,
      dept.DepartmentName
   FROM
      tabEmployee   as empl
      LEFT OUTER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   ORDER BY
      EmployeeName;
";
      }
```


<a name="cs_6b_datareader"/>
### <a name="c-block-6b-executereader"></a>Bloco C# 6b: ExecuteReader

- [Anterior](#cs_6_selectrows) &nbsp; / &nbsp; [Avançar](#cs_7_executenonquery)

Esse método foi projetado para executar a instrução T-SQL SELECT que é criada com o método **Build_6_Tsql_SelectEmployees**.


```csharp
      static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("Now, SelectEmployees (6)...");

         string tsql = Build_6_Tsql_SelectEmployees();

         using (var command = new SqlCommand(tsql, connection))
         {
            using (SqlDataReader reader = command.ExecuteReader())
            {
               while (reader.Read())
               {
                  Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                     reader.GetGuid(0),
                     reader.GetString(1),
                     reader.GetInt32(2),
                     (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                     (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
               }
            }
         }
      }
```


<a name="cs_7_executenonquery"/>
### <a name="c-block-7-executenonquery"></a>Bloco C# 7: ExecuteNonQuery

- [Anterior](#cs_6b_datareader) &nbsp; / &nbsp; [Avançar](#cs_8_output)

Esse método é chamado para operações que modificam o conteúdo das tabelas de dados sem retornar nenhuma linha de dados.


```csharp
      static void Submit_Tsql_NonQuery(
         SqlConnection connection,
         string tsqlPurpose,
         string tsqlSourceCode,
         string parameterName = null,
         string parameterValue = null
         )
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

         using (var command = new SqlCommand(tsqlSourceCode, connection))
         {
            if (parameterName != null)
            {
               command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                  parameterName,
                  parameterValue);
            }
            int rowsAffected = command.ExecuteNonQuery();
            Console.WriteLine(rowsAffected + " = rows affected.");
         }
      }
   } // EndOfClass
}
```


<a name="cs_8_output"/>
### <a name="c-block-8-actual-test-output-to-the-console"></a>Bloco C# 8: saída de teste real para o console

- [Anterior](#cs_7_executenonquery)

Esta seção captura a saída que o programa enviou ao console. Somente os valores de guid variam entre as execuções de teste.


```text
[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>> csharp_db_test.exe

=================================
Now, CreateTables (10)...

=================================
Now, Inserts (20)...

=================================
Now, UpdateJoin (30)...
2 rows affected, by UpdateJoin.

=================================
Now, DeleteJoin (40)...

=================================
Now, SelectEmployees (50)...
0199be49-a2ed-4e35-94b7-e936acf1cd75 , Alison , 20 , acct , Accounting
f0d3d147-64cf-4420-b9f9-76e6e0a32567 , Barbara , 17 , hres , Human Resources
cf4caede-e237-42d2-b61d-72114c7e3afa , Carol , 22 , acct , Accounting
cdde7727-bcfd-4f72-a665-87199c415f8b , Elle , 15 , NULL , NULL

[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>>
```
