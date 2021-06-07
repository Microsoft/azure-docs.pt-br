---
title: 'Tutorial: Usar uma identidade gerenciada para acessar o Banco de Dados SQL do Azure – Windows – Azure AD'
description: Um tutorial que descreve o processo de usar uma identidade gerenciada atribuída ao sistema em uma VM do Windows para acessar o Banco de Dados SQL do Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4f56ce9fa86dc27b77ad6b463479d13c8e4e7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91856504"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>Tutorial: Usar uma identidade gerenciada atribuída pelo sistema da VM do Windows para acessar o SQL Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como usar uma identidade atribuída ao sistema em uma VM (máquina virtual) do Windows para acessar o Banco de Dados SQL do Azure. As Identidades de Serviço Gerenciadas são gerenciadas automaticamente pelo Azure e permitem a você autenticar os serviços que dão suporte à autenticação do Azure AD sem necessidade de inserir as credenciais em seu código. Você aprenderá como:

> [!div class="checklist"]
>
> * Permitir acesso à sua VM a um Banco de Dados SQL do Azure
> * Habilitar a autenticação do Azure AD
> * Crie um usuário contido no banco de dados que represente a identidade atribuída do sistema da VM
> * Obter um token de acesso usando a identidade da VM e usá-lo para consultar o Banco de Dados SQL do Azure

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="enable"></a>Habilitar

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]

## <a name="grant-access"></a>Conceder acesso

Para permitir acesso à VM a um banco de dados no Banco de Dados SQL do Azure, use um [servidor SQL lógico](../../azure-sql/database/logical-servers.md) existente ou crie um. Para criar um novo servidor e banco de dados usando o portal do Azure, siga este [início rápido do Azure SQL](../../azure-sql/database/single-database-create-quickstart.md). Também há inícios rápidos que usam a CLI do Azure e o Azure PowerShell na [documentação do SQL Azure](/azure/sql-database/).

Há duas etapas para conceder acesso da VM a um banco de dados:

1. Habilitar a autenticação do Azure AD para o servidor.
2. Crie um **usuário contido** no banco de dados que represente a identidade atribuída do sistema da VM.

### <a name="enable-azure-ad-authentication"></a>Habilitar a autenticação do Azure AD

**Para [configurar a autenticação do Azure AD](../../azure-sql/database/authentication-aad-configure.md):**

1. No portal do Azure, selecione **Servidores SQL** na navegação à esquerda.
2. Clique no SQL Server a ser habilitado para autenticação do Azure AD.
3. Na seção **Configurações** da folha, clique em **Administrador do Active Directory**.
4. Na barra de comandos, clique em **Definir administrador**.
5. Selecione uma conta de usuário do Azure AD para ser um administrador do servidor e clique em **Selecionar.**
6. Na barra de comandos, clique em **Salvar**.

### <a name="create-contained-user"></a>Criar um usuário contido

Esta seção mostra como criar um usuário contido no banco de dados que representa a identidade atribuída do sistema da VM. Para esta etapa, você precisará do SSMS ([Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)). Antes de começar, também pode ser útil examinar os seguintes artigos para obter informações sobre a integração do Azure AD:

- [Autenticação Universal com o Banco de Dados SQL e o Azure Synapse Analytics (suporte de SSMS para MFA)](../../azure-sql/database/authentication-mfa-ssms-overview.md)
- [Configurar e gerenciar o Azure Active Directory para autenticação com o Banco de Dados SQL ou o Azure Synapse Analytics](../../azure-sql/database/authentication-aad-configure.md)

O BD SQL requer nomes de exibição exclusivos do AAD. Com isso, contas do AAD como usuários, grupos e entidades de serviço (aplicativos) e nomes de VM habilitados para identidade gerenciada precisam ser definidos exclusivamente no AAD com relação aos respectivos nomes de exibição. O BD SQL verifica o nome de exibição do AAD durante a criação do T-SQL desses usuários e, se ele não for exclusivo, o comando falhará solicitando para fornecer um nome de exibição do AAD exclusivo para uma determinada conta.

**Para criar um usuário contido:**

1. Inicie o SQL Server Management Studio.
2. Na caixa de diálogo **Conectar-se ao Servidor**, insira o nome do servidor no campo **Nome do servidor**.
3. No campo **Autenticação**, selecione **Active Directory – Universal com suporte para MFA**.
4. No campo **Nome de usuário**, digite o nome da conta do Azure AD que você definir como o administrador do servidor, por exemplo, helen@woodgroveonline.com
5. Clique em **Opções**.
6. No campo **Conectar-se ao banco de dados**, digite o nome do banco de dados não são do sistema que você deseja configurar.
7. Clique em **Conectar**. Conclua o processo de conexão.
8. No **Pesquisador de Objetos**, expanda a pasta **Bancos de Dados**.
9. Clique com o botão direito do mouse em um banco de dados de usuário e clique em **Nova consulta**.
10. Na janela de consulta, insira a linha a seguir e clique em **Executar** na barra de ferramentas:

    > [!NOTE]
    > `VMName` no comando a seguir é o nome da VM para a qual você habilitou o sistema de identidade atribuído na seção de pré-requisitos.

    ```sql
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```

    O comando deve ser concluído com êxito, criando o usuário independente para a identidade atribuída ao sistema da VM.
11. Desmarque a janela de consulta, insira a linha a seguir e clique em **Executar** na barra de ferramentas:

    > [!NOTE]
    > `VMName` no comando a seguir é o nome da VM para a qual você habilitou o sistema de identidade atribuído na seção de pré-requisitos.

    ```sql
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    O comando deve ser concluído com êxito, concedendo ao usuário independente a capacidade de ler todo o banco de dados.

O código em execução na VM agora pode obter um token usando a identidade gerenciada atribuída ao sistema e usar o token para se autenticar no servidor.

## <a name="access-data"></a>Acessar dados

Esta seção mostra como obter um token de acesso usando a identidade gerenciada atribuída pelo sistema da VM e usá-lo para chamar o Azure SQL. O SQL Azure tem suporte nativo para autenticação do Azure AD, de modo que pode aceitar diretamente os tokens de acesso obtidos usando identidades gerenciadas para recursos do Azure. Você usa o método **token de acesso** para criar uma conexão para o SQL. Isso faz parte da integração do SQL Azure ao Azure AD e é diferente de fornecer as credenciais na cadeia de conexão.

Aqui está um exemplo de código .NET de abertura de uma conexão a SQL usando um token de acesso. O código precisa ser executado na VM para ser capaz de acessar o ponto de extremidade da identidade gerenciada atribuída pelo sistema da VM. É necessário ter o **.NET Framework 4.6** ou superior ou o **.NET Core 2.2** ou superior para usar o método de token de acesso. Substitua os valores de AZURE-SQL-SERVERNAME e DATABASE de acordo. Observe a ID de recurso para o SQL Azure é `https://database.windows.net/`.

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call managed identities for Azure resources endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream());
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Como alternativa, uma maneira rápida de testar a configuração de ponta a ponta sem necessidade de escrever e implantar um aplicativo na VM é usando o PowerShell.

1. No portal, navegue até **Máquinas Virtuais** e vá para a máquina virtual do Windows e em **Visão geral**, clique em **Conectar**.
2. Insira o seu **Nome de usuário** e **Senha** que você adicionou quando criou a VM do Windows.
3. Agora que você criou uma **Conexão de Área de Trabalho Remota** com a máquina virtual, abra o **PowerShell** na sessão remota.
4. Usando o `Invoke-WebRequest` do PowerShell, faça uma solicitação para o ponto de extremidade da identidade gerenciada local para obter um token de acesso ao SQL Azure.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```

    Converta a resposta de um objeto JSON para um objeto do PowerShell.

    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Extraia o token de acesso da resposta.

    ```powershell
    $AccessToken = $content.access_token
    ```

5. Abra uma conexão com o servidor. Lembre-se de substituir os valores para AZURE-SQL-SERVERNAME e DATABASE.

    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Em seguida, crie e envie uma consulta para o servidor. Lembre-se de substituir o valor para TABLE.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Examine o valor de `$DataSet.Tables[0]` para exibir os resultados da consulta.

## <a name="disable"></a>Desabilitar

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar uma identidade gerenciada atribuída ao sistema para acessar o Banco de Dados SQL do Azure. Para saber mais sobre o Banco de Dados SQL do Azure, confira:

> [!div class="nextstepaction"]
> [Banco de Dados SQL do Azure](../../azure-sql/database/sql-database-paas-overview.md)
