---
title: Configurar Always Encrypted usando Azure Key Vault
description: Este tutorial mostra como proteger dados confidenciais em um banco de dado no banco de dados SQL do Azure com a criptografia de dado usando o assistente de Always Encrypted no SQL Server Management Studio.
keywords: criptografia de dados, chave de criptografia, criptografia de nuvem
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 11/02/2020
ms.openlocfilehash: 257abf03994c7006b1c3789174f550515dcd309a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841454"
---
# <a name="configure-always-encrypted-by-using-azure-key-vault"></a>Configurar Always Encrypted usando Azure Key Vault 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]

Este artigo mostra como proteger dados confidenciais em um banco de dado no banco de dados SQL do Azure com a criptografia de dado usando o [Assistente de Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-wizard) no [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms). Ele também inclui instruções que mostram como armazenar cada chave de criptografia no Cofre de Chaves do Azure.

O Always Encrypted é uma tecnologia de criptografia de dados que ajuda a proteger dados confidenciais em repouso no servidor, durante a movimentação entre o cliente e o servidor, e enquanto os dados estão em uso. Always Encrypted garante que os dados confidenciais nunca sejam exibidos como texto sem formatação dentro do sistema de banco de dados. Depois de configurar a criptografia de dados, somente aplicativos clientes ou servidores de aplicativo que têm acesso às chaves poderão acessar dados de texto sem formatação. Para obter informações detalhadas, consulte [Always Encrypted (Mecanismo do Banco de Dados)](/sql/relational-databases/security/encryption/always-encrypted-database-engine).

Depois de configurar o banco de dados para usar o Always Encrypted, você criará um aplicativo cliente em C# com o Visual Studio para trabalhar com os dados criptografados.

Siga as etapas neste artigo e saiba como configurar Always Encrypted para seu banco de dados no banco de dados SQL do Azure ou SQL Instância Gerenciada. Neste artigo, você aprenderá como realizar as seguintes tarefas:

- Usar o assistente Always Encrypted no SSMS para criar [Chaves Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine#Anchor_3).
  - Crie uma [CMK (Chave Mestra da Coluna)](/sql/t-sql/statements/create-column-master-key-transact-sql).
  - Crie uma [CEK (Chave de Criptografia da Coluna)](/sql/t-sql/statements/create-column-encryption-key-transact-sql).
- Criar uma tabela de banco de dados e criptografar colunas.
- Crie um aplicativo que insira, selecione e exiba os dados das colunas criptografadas.

## <a name="prerequisites"></a>Pré-requisitos


- Uma conta e uma assinatura do Azure. Se você não tiver uma, Inscreva-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Um banco de dados no [banco de dados SQL do Azure](single-database-create-quickstart.md) ou [Azure SQL instância gerenciada](../managed-instance/instance-create-quickstart.md).
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) versão 13.0.700.242 ou posterior.
- [.NET Framework 4.6](/dotnet/framework/) ou posterior (no computador cliente).
- [Do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
- [Azure PowerShell](/powershell/azure/) ou [CLI do Azure](/cli/azure/install-azure-cli)

## <a name="enable-client-application-access"></a>Habilitar o acesso do aplicativo cliente

Você deve habilitar seu aplicativo cliente para acessar seu banco de dados no banco de dados SQL Configurando um aplicativo Azure Active Directory (Azure AD) e copiando a ID e a *chave* do *aplicativo* que você precisará para autenticar seu aplicativo.

Para obter o *ID do Aplicativo* e *chave*, siga as etapas em [criar um aplicativo do Active Directory do Azure e entidade de serviço que podem acessar recursos](../../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-key-vault-to-store-your-keys"></a>Criar um cofre de chaves para armazenar as chaves

Agora que seu aplicativo cliente está configurado e você tem a ID do aplicativo, é hora de criar um cofre de chaves e configurar a política de acesso para que você e seu aplicativo possam acessar os segredos do cofre (as chaves Always Encrypted). As permissões *create*, *get*, *list*, *sign*, *verify*, *wrapKey* e *unwrapKey* são necessárias para criar uma nova chave mestra de coluna e configurar a criptografia com o SQL Server Management Studio.

Você pode criar rapidamente um cofre de chaves executando o script a seguir. Para obter uma explicação detalhada desses comandos e obter mais informações sobre como criar e configurar um cofre de chaves, consulte [o que é Azure Key Vault?](../../key-vault/general/overview.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell (RM) ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. O módulo AzureRM continuará a receber as correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para saber mais sobre a compatibilidade entre eles, confira [Apresentação do novo módulo Az do Azure PowerShell](/powershell/azure/new-azureps-module-az).

```powershell
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

Connect-AzAccount
$subscriptionId = (Get-AzSubscription -SubscriptionName $subscriptionName).Id
Set-AzContext -SubscriptionId $subscriptionId

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

Set-AzKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
Set-AzKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your database in Azure SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

az login
az account set --subscription $subscriptionName

az group create --location $location --name $resourceGroupName

az keyvault create --name $vaultName --resource-group $resourceGroupName --location $location

az keyvault set-policy --name $vaultName --key-permissions create get list sign unwrapKey verify wrapKey --resource-group $resourceGroupName --upn $userPrincipalName
az keyvault set-policy --name $vaultName --key-permissions get list sign unwrapKey verify wrapKey --resource-group $resourceGroupName --spn $applicationId
```

---

## <a name="connect-with-ssms"></a>Conectar-se ao SSMS

Abra o SQL Server Management Studio (SSMS) e conecte-se ao servidor ou gerenciado com seu banco de dados.

1. Abra o SSMS. (Vá para **conectar**  >  **Mecanismo de banco de dados** abrir a janela **conectar ao servidor** se ela não estiver aberta.)

2. Insira o nome do servidor ou o nome da instância e as credenciais. 

    ![Copiar a cadeia de conexão](./media/always-encrypted-azure-key-vault-configure/ssms-connect.png)

Se a janela **Nova Regra de Firewall** for aberta, entre no Azure e deixe o SSMS criar uma nova regra de firewall para você.

## <a name="create-a-table"></a>Criar uma tabela

Nesta seção, você criará uma tabela para armazenar os dados dos pacientes. Inicialmente ela não está criptografada; você vai configurar a criptografia na próxima seção.

1. Expanda os **Bancos de dados**.
2. Clique com o botão direito do mouse no banco de dados e clique em **nova consulta**.
3. Cole o T-SQL (Transact-SQL) a seguir na janela de nova consulta e o **execute** .

```sql
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

O SSMS fornece um assistente que ajuda você a configurar facilmente o Always Encrypted ao configurar a chave mestra da coluna, a chave de criptografia de coluna e colunas criptografadas para você.

1. Expandir **Databases**  >  **tabelas clínicas** de bancos de dados  >  **Tables**.
2. Clique com o botão direito do mouse na tabela **Pacientes** e selecione **Criptografar Colunas** para abrir o Assistente Sempre Criptografado:

    ![Captura de tela que realça as colunas de criptografia... opção de menu.](./media/always-encrypted-azure-key-vault-configure/encrypt-columns.png)

O assistente Always Encrypted inclui as seguintes seções: **Seleção de Coluna**, **Configuração da Chave Mestra**, **Validação** e **Resumo**.

### <a name="column-selection"></a>Seleção de coluna

Clique em **Avançar** na página **Introdução** para abrir a página **Seleção de Coluna**. Nessa página, você escolherá as colunas que quer criptografar, [o tipo de criptografia e quais CEK (Chave de Criptografia da Coluna)](/sql/relational-databases/security/encryption/always-encrypted-wizard#Anchor_2) usar.

Criptografe as informações de **SSN** e **BirthDate** de cada paciente. A coluna SSN usará criptografia determinística, que oferece suporte a pesquisas de igualdade, junções e agrupar por. A coluna BirthDate usará criptografia aleatória, que não oferece suporte a operações.

Defina o **Tipo de Criptografia** para a coluna SSN como **Determinístico** e a coluna BirthDate como **Aleatório**. Clique em **Próximo**.

![Criptografar Colunas](./media/always-encrypted-azure-key-vault-configure/column-selection.png)

### <a name="master-key-configuration"></a>Configuração da Chave Mestra

Na página **Configuração da Chave Mestra** , é possível configurar a CMK e escolher o provedor do repositório de chaves em que a CMK será armazenada. No momento, é possível armazenar uma CMK no repositório de certificados do Windows, no Cofre de Chaves do Azure ou em um HSM (Módulo de Segurança de Hardware).

Este tutorial mostra como armazenar suas chaves no Cofre de Chaves do Azure.

1. Selecione **Cofre de Chaves do Azure**.
2. Selecione o cofre de chaves desejado na lista suspensa.
3. Clique em **Próximo**.

![Configuração da Chave Mestra](./media/always-encrypted-azure-key-vault-configure/master-key-configuration.png)

### <a name="validation"></a>Validação

Você pode criptografar as colunas agora ou salvar um script do PowerShell para execução posterior. Para este tutorial, selecione **Seguir para a conclusão agora** e clique em **Avançar**.

### <a name="summary"></a>Resumo

Verifique se as configurações estão corretas e clique em **Concluir** para finalizar a configuração do Always Encrypted.

![Captura de tela mostra a página resultados com tarefas marcadas como aprovadas.](./media/always-encrypted-azure-key-vault-configure/summary.png)

### <a name="verify-the-wizards-actions"></a>Verificar as ações do assistente

Após a conclusão do assistente, seu banco de dados estará configurado para o Always Encrypted. O assistente executou as seguintes ações:

- Criação de uma chave mestra de coluna e armazenamento no Cofre de Chaves do Azure.
- Criação de uma chave de criptografia de coluna e armazenamento no Cofre de Chaves do Azure.
- Configuração das colunas selecionadas para criptografia. A tabela Pacientes não tem dados no momento, mas todos os dados existentes nas colunas selecionadas agora estão criptografadas.

Você pode verificar a criação das chaves no SSMS expandindo a **clínica**  >  **Security**  >  **Always Encrypted Keys**.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Criar um aplicativo cliente que funcione com os dados criptografados

Agora que o Always Encrypted está configurado, você pode compilar um aplicativo que execute *inserções* e *seleções* nas colunas criptografadas.  

> [!IMPORTANT]
> Seu aplicativo deve usar objetos [SqlParameter](/dotnet/api/system.data.sqlclient.sqlparameter) ao passar dados de texto sem formatação para o servidor com colunas do Sempre Criptografado. A passagem de valores literais sem usar objetos SqlParameter resultará em uma exceção.

1. Abra o Visual Studio e crie um novo **Aplicativo de Console** do C# (Visual Studio 2015 e anterior) ou **Aplicativo de Console (.NET Framework)** (Visual Studio 2017 e posterior). Verifique se seu projeto está definido como **.NET Framework 4.6** ou posterior.
2. Nomeie o projeto como **AlwaysEncryptedConsoleAKVApp** e clique em **OK**.
3. Instale os seguintes pacotes NuGet acessando **ferramentas**  >  **Gerenciador de pacotes NuGet**  >  **console do Gerenciador de pacotes**.

Execute estas duas linhas de código no console do Gerenciador de pacotes:

   ```powershell
   Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modificar a cadeia de conexão para habilitar o Always Encrypted

Esta seção explica como habilitar o Always Encrypted na sua cadeia de conexão de banco de dados.

Para habilitar o Sempre Criptografado, você precisa adicionar a palavra-chave **Column Encryption Setting** na cadeia de conexão e defini-la como **Enabled**.

Isso pode ser definido diretamente na cadeia de conexão ou usando [SqlConnectionStringBuilder](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder). O aplicativo de exemplo na próxima seção mostra como usar o **SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Habilitar o Always Encrypted na cadeia de conexão

Adicione a palavra-chave a seguir na sua cadeia de conexão.

   `Column Encryption Setting=Enabled`

### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Habilitar o Always Encrypted com SqlConnectionStringBuilder

O código a seguir mostra como habilitar o Sempre Criptografado configurando [SqlConnectionStringBuilder.ColumnEncryptionSetting](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting) como [Enabled](/dotnet/api/system.data.sqlclient.sqlconnectioncolumnencryptionsetting).

```csharp
// Instantiate a SqlConnectionStringBuilder.
SqlConnectionStringBuilder connStringBuilder = new SqlConnectionStringBuilder("replace with your connection string");

// Enable Always Encrypted.
connStringBuilder.ColumnEncryptionSetting = SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>Registrar o provedor do Chave do Cofre do Azure
O código abaixo mostra como registrar o provedor do Cofre de Chaves do Azure no driver do ADO.NET.

```csharp
private static ClientCredential _clientCredential;

static void InitializeAzureKeyVaultProvider() {
    _clientCredential = new ClientCredential(applicationId, clientKey);

    SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider = new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

    Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers = new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

    providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
    SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
}
```

## <a name="always-encrypted-sample-console-application"></a>Aplicativo de console de exemplo do Always Encrypted

Este exemplo demonstra como:

- Modificar a cadeia de conexão para habilitar o Always Encrypted.
- Registre o Cofre de Chaves do Azure como o provedor de armazenamento de chaves do aplicativo.  
- Inserir dados nas colunas criptografadas.
- Selecionar um registro por filtragem para um valor específico em uma coluna criptografada.

Substitua o conteúdo de *Program.cs* pelo código a seguir. Substituir a cadeia de conexão pela variável global connectionString na linha que está diretamente antes do método Principal com a cadeia de conexão válida do portal do Azure. Essa é a única alteração que você precisa fazer no código.

Execute o aplicativo para ver o Always Encrypted em ação.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data;
using System.Data.SqlClient;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

namespace AlwaysEncryptedConsoleAKVApp {
    class Program {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string applicationId = @"<application ID from your AAD application>";
        static string clientKey = "<key from your AAD application>";

        static void Main(string[] args) {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

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

            InsertPatient(new Patient() {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });

            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients()) {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null) {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }

        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider() {
            _clientCredential = new ClientCredential(applicationId, clientKey);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope) {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient) {
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

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex) {
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


        static List<Patient> SelectAllPatients() {
            List<Patient> patients = new List<Patient>();

            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));

            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patients.Add(new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }

            return patients;
        }

        static Patient SelectPatientBySSN(string ssn) {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patient = new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else {
                        patient = null;
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }
            return patient;
        }

        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable() {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex) {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
}
```

## <a name="verify-that-the-data-is-encrypted"></a>Verificar se os dados foram criptografados

Você pode fazer uma verificação rápida de que os dados reais no servidor foram criptografados consultando os dados dos Pacientes com o SSMS (usando nossa conexão atual em que a **Configuração de Criptografia de Coluna** ainda não está habilitada).

Execute a consulta a seguir no banco de dados Clínica.

```sql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

É possível ver que as colunas criptografadas não contêm dados de texto não criptografado.

   ![Captura de tela que mostra que as colunas criptografadas não contêm nenhum dado de texto sem formatação.](./media/always-encrypted-azure-key-vault-configure/ssms-encrypted.png)

Para usar o SSMS para acessar os dados de texto sem formatação, primeiro você precisa garantir que o usuário tenha as permissões adequadas para o Azure Key Vault: *get*, *unwrapKey* e *verify*. Para obter informações detalhadas, consulte [Criar e armazenar chaves mestras de coluna (Always Encrypted)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted).

Em seguida, adicione o parâmetro *Column Encryption Setting=enabled* durante a sua conexão.

1. No SSMS, clique com o botão direito do mouse em seu servidor no **Pesquisador de Objetos** e escolha **Desconectar**.
2. Clique em **conectar**  >  **mecanismo de banco de dados** para abrir a janela **conectar ao servidor** e clique em **Opções**.
3. Clique em **Parâmetros Adicionais de Conexão** e digite **Column Encryption Setting=enabled**.

    ![Captura de tela que mostra a guia parâmetros adicionais de correção.](./media/always-encrypted-azure-key-vault-configure/ssms-connection-parameter.png)

4. Execute a consulta a seguir no banco de dados Clínica.

   ```sql
   SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

   Agora você pode ver os dados de texto sem formatação em colunas criptografadas.
   
   ![Novo aplicativo de console](./media/always-encrypted-azure-key-vault-configure/ssms-plaintext.png)

## <a name="next-steps"></a>Próximas etapas

Depois que o banco de dados estiver configurado para usar Always Encrypted, talvez você queira fazer o seguinte:

- [Girar e limpar suas chaves](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio).
- [Migrar dados que já foram criptografados com o Always Encrypted](/sql/relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted).

## <a name="related-information"></a>Informações relacionadas

- [Always Encrypted (desenvolvimento de cliente)](/sql/relational-databases/security/encryption/always-encrypted-client-development)
- [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption)
- [SQL Server criptografia](/sql/relational-databases/security/encryption/sql-server-encryption)
- [Assistente de Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-wizard)
- [Blog do Always Encrypted](/archive/blogs/sqlsecurity/always-encrypted-key-metadata)
