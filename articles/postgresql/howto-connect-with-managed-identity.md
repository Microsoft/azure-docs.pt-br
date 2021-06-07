---
title: Conectar-se com identidade gerenciada – Banco de Dados do Azure para PostgreSQL – Servidor único
description: Saiba mais sobre como se conectar e autenticar usando a Identidade Gerenciada para autenticação com o Banco de Dados do Azure para PostgreSQL
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: beb5930e98a5c5498349dc3aa773423ee5d281bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792463"
---
# <a name="connect-with-managed-identity-to-azure-database-for-postgresql"></a>Conectar com a Identidade Gerenciada ao Banco de Dados do Azure para PostgreSQL

Este artigo mostra como usar uma identidade atribuída pelo usuário para uma VM (máquina virtual) para acessar o Banco de Dados do Azure para PostgreSQL. As Identidades de Serviço Gerenciadas são gerenciadas automaticamente pelo Azure e permitem a você autenticar os serviços que dão suporte à autenticação do Azure AD sem necessidade de inserir as credenciais em seu código. 

Você aprenderá como:
- Conceder à VM acesso a um Banco de Dados do Azure para PostgreSQL
- Criar um usuário no banco de dados que represente a identidade atribuída pelo usuário da VM
- Obter um token de acesso usando a identidade da VM e usá-lo para consultar um Banco de Dados do Azure para PostgreSQL
- Implementar a recuperação de token em um aplicativo de exemplo C#

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com as identidades gerenciadas para funcionalidades de recursos do Azure, veja esta [visão geral](../../articles/active-directory/managed-identities-azure-resources/overview.md). Caso você ainda não tenha uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar a criação de recursos e o gerenciamento de função necessários, sua conta precisa de permissões "Proprietário" no escopo apropriado (sua assinatura ou grupo de recursos). Caso você precise de ajuda com a atribuição de função, confira [Atribuir funções do Azure para gerenciar o acesso aos recursos de assinatura do Azure](../../articles/role-based-access-control/role-assignments-portal.md).
- É necessário uma VM do Azure (por exemplo, ao executar Ubuntu Linux) que você gostaria de usar para acessar o banco de dados usando a Identidade Gerenciada
- Você precisa de um servidor de Banco de Dados do Azure para PostgreSQL que tenha [Autenticação do Azure AD](howto-configure-sign-in-aad-authentication.md) configurada
- Para seguir o exemplo C#, primeiro conclua o guia de como [Conectar com C#](connect-csharp.md)

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>Criar uma identidade gerenciada atribuída pelo usuário para a VM

Crie uma identidade em sua assinatura usando o comando [az identity create](/cli/azure/identity#az_identity_create). Você pode usar o mesmo grupo de recursos em que a máquina virtual é executada ou um diferente.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myManagedIdentity
```

Para configurar a identidade nas etapas a seguir, use o comando [az identity show](/cli/azure/identity#az_identity_show) para armazenar a ID do recurso da entidade e a ID do cliente nas variáveis.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query id --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query clientId --output tsv)
```

Agora podemos conceder a identidade atribuída pelo usuário à VM com o comando [az vm identity assign](/cli/azure/vm/identity#az_vm_identity_assign):

```azurecli
az vm identity assign --resource-group myResourceGroup --name myVM --identities $resourceID
```

Para concluir a instalação, mostre o valor da ID do cliente, que será necessário nas próximas etapas:

```bash
echo $clientID
```

## <a name="creating-a-postgresql-user-for-your-managed-identity"></a>Criar um usuário do PostgreSQL para sua Identidade Gerenciada

Agora, conecte-se como o usuário administrador do Azure AD ao banco de dados PostgreSQL e execute as seguintes instruções SQL:

```sql
SET aad_validate_oids_in_tenant = off;
CREATE ROLE myuser WITH LOGIN PASSWORD 'CLIENT_ID' IN ROLE azure_ad_user;
```

A identidade gerenciada agora tem acesso ao autenticar com o nome de usuário `myuser` (substitua pelo nome de sua escolha).

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Recuperar o token de acesso do Serviço de metadados da instância do Azure

Seu aplicativo agora pode recuperar um token de acesso do Serviço de metadados da instância do Azure e usá-lo para autenticação com o banco de dados.

Essa recuperação de token é feita ao fazer uma solicitação HTTP para `http://169.254.169.254/metadata/identity/oauth2/token` e transmitir os seguintes parâmetros:

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID` (que você recuperou anteriormente)

Você obterá um resultado JSON que contém um campo `access_token` – esse valor de texto longo é o token de acesso da Identidade Gerenciada, que você deve usar como a senha ao se conectar ao banco de dados.

Para fins de teste, execute os seguintes comandos no shell. Observe que você precisa de `curl`, de `jq` e do cliente `psql` instalado.

```bash
# Retrieve the access token
export PGPASSWORD=`curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token`

# Connect to the database
psql -h SERVER --user USER@SERVER DBNAME
```

Agora você está conectado ao banco de dados configurado anteriormente.

## <a name="connecting-using-managed-identity-in-c"></a>Conectar usando a Identidade Gerenciada no C#

Esta seção mostra como obter um token de acesso usando a identidade gerenciada atribuída pelo usuário da VM e usá-lo para chamar o Banco de Dados do Azure para PostgreSQL. O Banco de Dados do Azure para PostgreSQL tem suporte nativo para autenticação do Azure AD, de modo que pode aceitar diretamente os tokens de acesso obtidos usando identidades gerenciadas para recursos do Azure. Ao criar uma conexão com o PostgreSQL, você transmite o token de acesso no campo senha.

Aqui está um exemplo de código .NET de abertura de uma conexão o PostgreSQL usando um token de acesso. Esse código deve ser executado na VM para acessar o ponto de extremidade da identidade gerenciada atribuída pelo usuário da VM. É necessário ter o .NET Framework 4.6 ou superior ou o .NET Core 2.2 ou superior para usar o método de token de acesso. Substitua os valores de HOST, USER, DATABASE e CLIENT_ID.

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using Npgsql;

namespace Driver
{
    class Script
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "HOST";
        private static string User = "USER";
        private static string Database = "DATABASE";
        private static string ClientId = "CLIENT_ID";

        static void Main(string[] args)
        {
            //
            // Get an access token for PostgreSQL.
            //
            Console.Out.WriteLine("Getting access token from Azure Instance Metadata service...");

            // Azure AD resource ID for Azure Database for PostgreSQL is https://ossrdbms-aad.database.windows.net/
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=" + ClientId);
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
                var list = JsonSerializer.Deserialize<Dictionary<string, string>>(stringResponse);
                accessToken = list["access_token"];
            }
            catch (Exception e)
            {
                Console.Out.WriteLine("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                System.Environment.Exit(1);
            }

            //
            // Open a connection to the PostgreSQL server using the access token.
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    Database,
                    5432,
                    accessToken);

            using (var conn = new NpgsqlConnection(connString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                conn.Open();

                using (var command = new NpgsqlCommand("SELECT version()", conn))
                {

                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        Console.WriteLine("\nConnected!\n\nPostgres version: {0}", reader.GetString(0));
                    }
                }
            }
        }
    }
}
```

Quando executado, esse comando fornecerá um resultado como este:

```
Getting access token from Azure Instance Metadata service...
Opening connection using access token...

Connected!

Postgres version: PostgreSQL 11.6, compiled by Visual C++ build 1800, 64-bit
```

## <a name="next-steps"></a>Próximas etapas

* Consulte os conceitos gerais da [Autenticação do Azure Active Directory com o Banco de Dados do Azure para PostgreSQL](concepts-aad-authentication.md)
