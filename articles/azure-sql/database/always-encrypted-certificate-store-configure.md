---
title: Configurar Always Encrypted usando o repositório de certificados do Windows
description: Este artigo mostra como proteger dados confidenciais no banco de dados SQL do Azure com criptografia de banco de dado usando o assistente de Always Encrypted no SQL Server Management Studio (SSMS). Ele mostra como armazenar suas chaves de criptografia no repositório de certificados do Windows.
keywords: criptografar dados, criptografia do sql, criptografia de banco de dados, dados confidenciais, sempre criptografados
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviwer: ''
ms.date: 04/23/2020
ms.openlocfilehash: 60dea826a12ea475806adb6db88faa88e26463a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674839"
---
# <a name="configure-always-encrypted-by-using-the-windows-certificate-store"></a>Configurar Always Encrypted usando o repositório de certificados do Windows

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo mostra como proteger dados confidenciais no banco de dados SQL do Azure ou no Azure SQL Instância Gerenciada com criptografia de banco de dado usando o [Assistente de Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-wizard) em [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms). Ele mostra como armazenar suas chaves de criptografia no repositório de certificados do Windows.

O Always Encrypted é uma tecnologia de criptografia de dados que ajuda a proteger dados confidenciais em repouso no servidor, durante a movimentação entre o cliente e o servidor, e enquanto os dados estão em uso, garantindo que os dados confidenciais nunca apareçam como texto sem formatação no sistema de banco de dados. Depois de criptografar dados, somente aplicativos cliente ou servidores de aplicativos que têm acesso às chaves podem acessar dados de texto não criptografado. Para obter informações detalhadas, consulte [Always Encrypted (Mecanismo do Banco de Dados)](/sql/relational-databases/security/encryption/always-encrypted-database-engine).

Depois de configurar o banco de dados para usar o Always Encrypted, você criará um aplicativo cliente em C# com o Visual Studio para trabalhar com os dados criptografados.

Siga as etapas neste artigo para saber como configurar o Always Encrypted para o banco de dados SQL ou o SQL Instância Gerenciada. Neste artigo, você aprenderá como realizar as seguintes tarefas:

* Use o assistente de Always Encrypted no SSMS para criar [chaves de Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine#Anchor_3).
  * Crie uma [chave mestra de coluna (CMK)](/sql/t-sql/statements/create-column-master-key-transact-sql).
  * Crie uma [CEK (chave de criptografia de coluna)](/sql/t-sql/statements/create-column-encryption-key-transact-sql).
* Criar uma tabela de banco de dados e criptografar colunas.
* Crie um aplicativo que insira, selecione e exiba os dados das colunas criptografadas.

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, será necessário:

* Uma conta e uma assinatura do Azure. Se você não tiver uma, Inscreva-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Um banco de dados no [banco de dados SQL do Azure](single-database-create-quickstart.md) ou [Azure SQL instância gerenciada](../managed-instance/instance-create-quickstart.md).
* [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) versão 13.0.700.242 ou posterior.
* [.NET Framework 4.6](/dotnet/framework/) ou posterior (no computador cliente).
* [Do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="enable-client-application-access"></a>Habilitar o acesso do aplicativo cliente

Você deve habilitar seu aplicativo cliente para acessar o banco de dados SQL ou o SQL Instância Gerenciada Configurando um aplicativo Azure Active Directory (AAD) e copiando a ID e a *chave* do *aplicativo* que será necessário para autenticar seu aplicativo.

Para obter o *ID do Aplicativo* e *chave*, siga as etapas em [criar um aplicativo do Active Directory do Azure e entidade de serviço que podem acessar recursos](../../active-directory/develop/howto-create-service-principal-portal.md).



## <a name="connect-with-ssms"></a>Conectar-se ao SSMS

Abra o SQL Server Management Studio (SSMS) e conecte-se ao servidor ou gerenciado com seu banco de dados.

1. Abra o SSMS. (Clique em **conectar**  >  **Mecanismo de banco de dados** abrir a janela **conectar ao servidor** se ela não estiver aberta).
2. Insira o nome do servidor e credenciais.

    ![Copiar a cadeia de conexão](./media/always-encrypted-certificate-store-configure/ssms-connect.png)

Se a janela **Nova Regra de Firewall** for aberta, entre no Azure e deixe o SSMS criar uma nova regra de firewall para você.

## <a name="create-a-table"></a>Criar uma tabela

Nesta seção, você criará uma tabela para armazenar os dados dos pacientes. Inicialmente, essa será uma tabela normal; você vai configurar a criptografia na próxima seção.

1. Expanda os **Bancos de dados**.
2. Clique com o botão direito do mouse no banco de dados **Clínica** e clique em **Nova Consulta**.
3. Cole o T-SQL (Transact-SQL) a seguir na janela de nova consulta e o **execute** .
    
    ```tsql
    CREATE TABLE [dbo].[Patients](
    [PatientId] [int] IDENTITY(1,1),
    [SSN] [char](11) NOT NULL,
    [FirstName] [nvarchar](50) NULL,
    [LastName] [nvarchar](50) NULL,
    [MiddleName] [nvarchar](50) NULL,
    [StreetAddress] [nvarchar](50) NULL,
    [City] [nvarchar](50) NULL,
    [ZipCode] [char](5) NULL,
    [State] [char](2) NULL,
    [BirthDate] [date] NOT NULL
    PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
    GO
    ```

## <a name="encrypt-columns-configure-always-encrypted"></a>Criptografar colunas (configurar o Always Encrypted)

O SSMS fornece um assistente para configurar facilmente o Always Encrypted definindo as colunas criptografadas, CMK e CEK para você.

1. Expandir   >  **tabelas clínicas** de bancos de dados  >  .
2. Clique com o botão direito do mouse na tabela **Pacientes** e selecione **Criptografar Colunas** para abrir o Assistente Sempre Criptografado:

    ![Captura de tela que mostra a criptografia colunns... na tabela pacientes.](./media/always-encrypted-certificate-store-configure/encrypt-columns.png)

O assistente Always Encrypted inclui as seguintes seções: **Seleção de Coluna**, CMK (**Configuração da Chave Mestra**), **Validação** e **Resumo**.

### <a name="column-selection"></a>Seleção de coluna

Clique em **Avançar** na página **Introdução** para abrir a página **Seleção de Coluna**. Nessa página, você escolherá as colunas que quer criptografar, [o tipo de criptografia e quais CEK (Chave de Criptografia da Coluna)](/sql/relational-databases/security/encryption/always-encrypted-wizard#Anchor_2) usar.

Criptografe as informações de **SSN** e **BirthDate** de cada paciente. A coluna **SSN** usará criptografia determinística, que dá suporte a pesquisas de igualdade, junções e agrupamentos por categoria. A coluna **BirthDate** usará criptografia aleatória, que não permite operações.

Defina o **Tipo de Criptografia** para a coluna **SSN** como **Determinístico** e a coluna **BirthDate** como **Aleatório**. Clique em **Próximo**.

![Criptografar Colunas](./media/always-encrypted-certificate-store-configure/column-selection.png)

### <a name="master-key-configuration"></a>Configuração da Chave Mestra

Na página **Configuração da Chave Mestra** , é possível configurar a CMK e escolher o provedor do repositório de chaves em que a CMK será armazenada. No momento, é possível armazenar uma CMK no repositório de certificados do Windows, no Cofre de Chaves do Azure ou em um HSM (Módulo de Segurança de Hardware). Este tutorial mostra como armazenar suas chaves no repositório de certificados do Windows.

Verifique se **Repositório de certificados do Windows** está marcado e clique em **Avançar**.

![Configuração da Chave Mestra](./media/always-encrypted-certificate-store-configure/master-key-configuration.png)

### <a name="validation"></a>Validação

Você pode criptografar as colunas agora ou salvar um script do PowerShell para execução posterior. Para este tutorial, selecione **Seguir para a conclusão agora** e clique em **Avançar**.

### <a name="summary"></a>Resumo

Verifique se as configurações estão corretas e clique em **Concluir** para finalizar a configuração do Always Encrypted.

![Captura de tela mostra a página resultados com tarefas marcadas como aprovadas.](./media/always-encrypted-certificate-store-configure/summary.png)

### <a name="verify-the-wizards-actions"></a>Verificar as ações do assistente

Após a conclusão do assistente, seu banco de dados estará configurado para o Always Encrypted. O assistente executou as seguintes ações:

* Criou uma CMK.
* Criou uma CEK.
* Configuração das colunas selecionadas para criptografia. Sua tabela **Pacientes** não tem dados no momento, mas todos os dados existentes nas colunas selecionadas agora estão criptografadas.

Você pode verificar a criação das chaves no SSMS acessando **clínica**  >    >  **Always Encrypted chaves** de segurança. Agora, é possível ver as chaves novas que o assistente gerou para você.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Criar um aplicativo cliente que funcione com os dados criptografados

Agora que o Always Encrypted está configurado, você pode compilar um aplicativo que execute *inserções* e *seleções* nas colunas criptografadas. Para executar com sucesso o aplicativo de exemplo, você deve executá-lo no mesmo computador em que executou o assistente Always Encrypted. Para executar o aplicativo em outro computador, é preciso implantar os certificados do Always Encrypted no computador que executa o aplicativo cliente.  

> [!IMPORTANT]
> Seu aplicativo deve usar objetos [SqlParameter](/dotnet/api/system.data.sqlclient.sqlparameter) ao passar dados de texto sem formatação para o servidor com colunas do Sempre Criptografado. A passagem de valores literais sem usar objetos SqlParameter resultará em uma exceção.

1. Abra o Visual Studio e crie um novo aplicativo de console em C#. Verifique se seu projeto está definido como **.NET Framework 4.6** ou posterior.
2. Nomeie o projeto como **AlwaysEncryptedConsoleApp** e clique em **OK**.

![Captura de tela que mostra o projeto AlwaysEncryptedConsoleApp chamado recentemente.](./media/always-encrypted-certificate-store-configure/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modificar a cadeia de conexão para habilitar o Always Encrypted

Esta seção explica como habilitar o Always Encrypted na sua cadeia de conexão de banco de dados. Você modificará o aplicativo de console que acabou de criar na próxima seção, "Exemplo de aplicativo de console do Always Encrypted".

Para habilitar o Sempre Criptografado, você precisa adicionar a palavra-chave **Column Encryption Setting** na cadeia de conexão e defini-la como **Enabled**.

Isso pode ser definido diretamente na cadeia de conexão ou usando um [SqlConnectionStringBuilder](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder). O aplicativo de exemplo na próxima seção mostra como usar o **SqlConnectionStringBuilder**.

> [!NOTE]
> Essa é a única alteração necessária em um aplicativo cliente específico para o Always Encrypted. Se você tiver um aplicativo existente que armazene sua cadeia de conexão externamente (ou seja, em um arquivo de configuração), será possível habilitar o Always Encrypted sem alterar qualquer código.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Habilitar o Always Encrypted na cadeia de conexão

Adicione a palavra-chave a seguir na sua cadeia de conexão:

`Column Encryption Setting=Enabled`

### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Habilitar o Always Encrypted com um SqlConnectionStringBuilder

O código a seguir mostra como habilitar o Always Encrypted configurando o [SqlConnectionStringBuilder.ColumnEncryptionSetting](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting) como [Enabled](/dotnet/api/system.data.sqlclient.sqlconnectioncolumnencryptionsetting).

```csharp
// Instantiate a SqlConnectionStringBuilder.
SqlConnectionStringBuilder connStringBuilder =
    new SqlConnectionStringBuilder("replace with your connection string");

// Enable Always Encrypted.
connStringBuilder.ColumnEncryptionSetting =
    SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="always-encrypted-sample-console-application"></a>Aplicativo de console de exemplo do Always Encrypted

Este exemplo demonstra como:

* Modificar a cadeia de conexão para habilitar o Always Encrypted.
* Inserir dados nas colunas criptografadas.
* Selecionar um registro por filtragem para um valor específico em uma coluna criptografada.

Substitua o conteúdo de **Program.cs** pelo código a seguir. Substitua a cadeia de conexão pela variável global connectionString na linha diretamente acima do método Principal com a cadeia de conexão válida do Portal do Azure. Essa é a única alteração que você precisa fazer no código.

Execute o aplicativo para ver o Always Encrypted em ação.

```cs
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;
using System.Globalization;

namespace AlwaysEncryptedConsoleApp
{
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Data Source = SPE-T640-01.sys-sqlsvr.local; Initial Catalog = Clinic; Integrated Security = true";

        static void Main(string[] args)
        {
            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();

            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            CultureInfo culture = CultureInfo.CreateSpecificCulture("en-US");
            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993", culture)
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now let's locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
}
```

## <a name="verify-that-the-data-is-encrypted"></a>Verificar se os dados foram criptografados

Você pode verificar rapidamente se os dados reais no servidor estão criptografados, consultando os dados de **Pacientes** dados com o SSMS. (Use sua conexão atual em que a configuração de criptografia de coluna ainda não está habilitada.)

Execute a consulta a seguir no banco de dados Clínica.

```tsql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

É possível ver que as colunas criptografadas não contêm dados de texto não criptografado.

   ![Captura de tela que mostra dados criptografados nas colunas criptografadas.](./media/always-encrypted-certificate-store-configure/ssms-encrypted.png)

Para usar o SSMS para acessar os dados de texto não criptografado, você pode adicionar o parâmetro **Column Encryption Setting=enabled** à conexão.

1. No SSMS, clique com o botão direito do mouse no seu servidor em **Pesquisador de Objetos** e clique em **Desconectar**.
2. Clique em **conectar**  >  **mecanismo de banco de dados** para abrir a janela **conectar ao servidor** e clique em **Opções**.
3. Clique em **Parâmetros Adicionais de Conexão** e digite **Column Encryption Setting=enabled**.

    ![Captura de tela que mostra a guia parâmetros de conexão adicionais com configuração de criptografia de coluna = habilitado digitado na caixa.](./media/always-encrypted-certificate-store-configure/ssms-connection-parameter.png)
4. Execute a consulta a seguir no banco de dados **Clínica** .

    ```tsql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
    ```

     Agora você pode ver os dados de texto sem formatação em colunas criptografadas.

    ![Novo aplicativo de console](./media/always-encrypted-certificate-store-configure/ssms-plaintext.png)

> [!NOTE]
> Se você se conectar ao SSMS (ou qualquer cliente) em outro computador, ele não terá acesso às chaves de criptografia e não será capaz de descriptografar os dados.

## <a name="next-steps"></a>Próximas etapas

Depois de criar um banco de dados que usa o Always Encrypted, convém fazer o seguinte:

* Executar esse exemplo de um computador diferente. Ele não terá acesso às chaves de criptografia e, portanto, não terá acesso aos dados de texto não criptografado e não será executado com êxito.
* [Girar e limpar suas chaves](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio).
* [Migrar dados que já foram criptografados com o Always Encrypted](/sql/relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted).
* [Implantar certificados do Always Encrypted em outros computadores cliente](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted#Anchor_1) (veja a seção "Disponibilizando certificados para aplicativos e usuários").

## <a name="related-information"></a>Informações relacionadas

* [Always Encrypted (desenvolvimento de cliente)](/sql/relational-databases/security/encryption/always-encrypted-client-development)
* [Criptografia de Dados Transparente](/sql/relational-databases/security/encryption/transparent-data-encryption)
* [Criptografia do SQL Server](/sql/relational-databases/security/encryption/sql-server-encryption)
* [Assistente do Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-wizard)
* [Blog de Always Encrypted](/archive/blogs/sqlsecurity/always-encrypted-key-metadata)