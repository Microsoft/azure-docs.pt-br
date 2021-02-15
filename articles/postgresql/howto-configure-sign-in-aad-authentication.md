---
title: Usar o Azure Active Directory – Banco de Dados do Azure para PostgreSQL – Servidor único
description: Saiba mais sobre como configurar o AAD (Azure Active Directory) para a autenticação com o Banco de Dados do Azure para PostgreSQL – Servidor único
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: 178c339f6f47569160a9a748794678c610f35734
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87171637"
---
# <a name="use-azure-active-directory-for-authentication-with-postgresql"></a>Usar Azure Active Directory para autenticação com PostgreSQL

Este artigo explicará as etapas de como configurar o acesso do Azure Active Directory com o Banco de Dados do Azure para PostgreSQL e como se conectar usando um token do Azure Active Directory.

## <a name="setting-the-azure-ad-admin-user"></a>Configurar o usuário administrador do Azure AD

Somente usuários administradores do Azure Active Directory podem criar/habilitar usuários para a autenticação baseada no Azure Active Directory. Recomendamos não usar o administrador do Azure Active Directory para operações de banco de dados regulares, pois ele tem permissões de usuário elevadas (por exemplo, CREATEDB).

Para definir o administrador do Azure Active Directory (você pode usar um usuário ou um grupo), siga as etapas a seguir

1. No portal do Azure, selecione a instância do Banco de Dados do Azure para PostgreSQL que você deseja habilitar para o Azure Active Directory.
2. Em Configurações, selecione Administrador do Active Directory:

![definir administrador do azure ad][2]

3. Selecione um usuário válido do Azure Active Directory no locatário do cliente para ser administrador do Azure Active Directory.

> [!IMPORTANT]
> Ao definir o administrador, um novo usuário é adicionado ao servidor do Banco de Dados do Azure para PostgreSQL com permissões de administrador completo. O usuário administrador do Azure Active Directory no Banco de Dados do Azure para PostgreSQL terá a função `azure_ad_admin`.

Somente um administrador do Azure Active Directory pode ser criado por servidor PostgreSQL, e a seleção de outro substituirá o administrador do Azure Active Directory existente configurado para o servidor. Você pode especificar um grupo do Azure Active Directory em vez de um usuário individual para ter vários administradores. Depois, tenha em mente que você entrará com o nome do grupo para fins de administração.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Conectar ao Banco de Dados do Azure para PostgreSQL usando o Azure Active Directory

O diagrama de alto nível a seguir resume o fluxo de trabalho de usar a autenticação do Azure Active Directory com o Banco de Dados do Azure para PostgreSQL:

![fluxo de autenticação][1]

Criamos a integração do Azure Active Directory para trabalhar com ferramentas comuns do PostgreSQL, como a psql, que não reconhecem o Azure Active Directory e só dão suporte à especificação de nome de usuário e senha ao se conectar ao PostgreSQL. Passamos o token do Azure Active Directory como a senha, conforme mostrado na imagem acima.

No momento, testamos os clientes a seguir:

- linha de comando da psql (utilize a variável PGPASSWORD para passar o token, consulte abaixo)
- Azure Data Studio (usando a extensão do PostgreSQL)
- Outros clientes baseados em libpq (por exemplo, estruturas do aplicativo comuns e ORMs)

> [!NOTE]
> Lembre-se de que, no momento, o uso do token do Azure Active Directory com o pgAdmin não tem suporte, pois ele tem uma limitação embutida em código de 256 caracteres para senhas (a qual o token excede).

Estas são as etapas que um usuário/aplicativo precisará fazer a autenticação no Azure Active Directory descrito abaixo:

### <a name="prerequisites"></a>Pré-requisitos

Você pode acompanhar em Azure Cloud Shell, uma VM do Azure ou em seu computador local. Garanta que você tem o [CLI do Azure instalado](/cli/azure/install-azure-cli).

### <a name="step-1-authenticate-with-azure-ad"></a>Etapa 1: Autenticar com o Azure AD

Comece Autenticando com o Azure AD usando a ferramenta de CLI do Azure. Esta etapa não é necessária no Azure Cloud Shell.

```
az login
```

O comando iniciará uma janela do navegador na página de autenticação do Azure AD. Ele exige que você forneça a ID de usuário e a senha do Azure Active Directory.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Etapa 2: Recuperar um token de acesso do Azure Active Directory

Invoque a ferramenta de CLI do Azure para adquirir um token de acesso para o usuário autenticado do Azure Active Directory da etapa 1 para acessar o Banco de Dados do Azure para PostgreSQL.

Exemplo (para nuvem pública):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

O valor do recurso acima deve ser especificado exatamente como o mostrado. Para outras nuvens, é possível pesquisar o valor do recurso usando:

```azurecli-interactive
az cloud show
```

Para a versão 2.0.71 do CLI do Azure e posteriores, o comando pode ser especificado na seguinte versão mais conveniente para todas as nuvens:

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

Depois que a autenticação for bem-sucedida, o Azure Active Directory retornará um token de acesso:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

O token é uma cadeia de caracteres Base 64 que codifica todas as informações sobre o usuário autenticado e que é direcionado para o serviço do Banco de Dados do Azure para PostgreSQL.

> [!NOTE]
> A validade do token de acesso está entre 5 e 60 minutos. Recomendamos que você obtenha o token de acesso logo antes de iniciar o logon no Banco de Dados do Azure para PostgreSQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>Etapa 3: Use o token como senha para fazer logon no PostgreSQL

Ao se conectar, você precisa usar o token de acesso como a senha de usuário do PostgreSQL.

Ao usar o cliente de linha de comando `psql`, o token de acesso precisa ser passado pela variável de ambiente `PGPASSWORD`, já que o token de acesso excede o comprimento da senha que `psql` pode aceitar diretamente:

Exemplo do Windows:

```cmd
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

```PowerShell
$env:PGPASSWORD='<copy/pasted TOKEN value from step 2>'
```

Exemplo do Linux/macOS:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Agora, você pode iniciar uma conexão com o Banco de Dados do Azure para PostgreSQL como faria normalmente:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

Considerações importantes ao se conectar:

* `user@tenant.onmicrosoft.com` é o nome do usuário ou grupo do Azure AD ao qual você está tentando se conectar
* Sempre acrescentar o nome do servidor após o nome de usuário/grupo do Azure AD (por exemplo, `@mydb` )
* Certifique-se de usar a maneira exata de que o nome de usuário ou grupo do Azure AD está escrito
* Os nomes de usuário e grupo do Azure AD diferenciam maiúsculas de minúsculas
* Ao conectar-se como um grupo, use apenas o nome do grupo (por exemplo, `GroupName@mydb` )
* Se o nome contiver espaços, use `\` antes de cada espaço para escapar

Você já está autenticado no servidor PostgreSQL usando a autenticação do Azure Active Directory.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Criar usuários do Azure Active Directory no Banco de Dados do Azure para PostgreSQL

Para adicionar um usuário do Azure Active Directory ao banco de dados do Banco de Dados do Azure para PostgreSQL, execute as etapas a seguir após a conexão (consulte a seção mais adiante sobre como se conectar):

1. Primeiro, garanta que o usuário do Azure Active Directory `<user>@yourtenant.onmicrosoft.com` seja um usuário válido no locatário do Azure Active Directory.
2. Entre na instância do Banco de Dados do Azure para PostgreSQL como o usuário administrador do Azure Active Directory.
3. Criar a função `<user>@yourtenant.onmicrosoft.com` no Banco de Dados do Azure para PostgreSQL.
4. Torne `<user>@yourtenant.onmicrosoft.com` um membro da função azure_ad_user. Isso só deve ser dado aos usuários do Azure Active Directory.

**Exemplo:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> A autenticação de um usuário por meio do Azure Active Directory não concede ao usuário nenhuma permissão para acessar objetos no banco de dados do Banco de Dados do Azure para PostgreSQL. Você precisa conceder ao usuário as permissões necessárias manualmente.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Criar grupos do Azure Active Directory no Banco de Dados do Azure para PostgreSQL

Para habilitar um grupo do Azure Active Directory para acesso ao seu banco de dados, use o mesmo mecanismo que o dos usuários, mas especifique o nome do grupo:

**Exemplo:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

Ao fazer logon, os membros do grupo usarão os tokens de acesso pessoal deles, mas assinarão com o nome do grupo especificado como o nome de usuário.

## <a name="token-validation"></a>Validação de token

A autenticação do Azure Active Directory no Banco de Dados do Azure para PostgreSQL garante que o usuário exista no servidor PostgreSQL e verifica a validade do token por meio da validação do conteúdo do token. As seguintes etapas de validação de token são executadas:

- O token é assinado pelo Azure Active Directory e não foi violado
- O token foi emitido pelo Azure Active Directory para o locatário associado ao servidor
- O token não expirou
- O token é para o recurso do Banco de Dados do Azure para PostgreSQL (e não outro recurso do Azure)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Migrando usuários existentes do PostgreSQL para a autenticação baseada no Azure Active Directory

Você pode habilitar a autenticação do Azure Active Directory para usuários existentes. Há dois casos a serem considerados:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Caso 1: O nome de usuário do PostgreSQL corresponde ao nome UPN do Azure Active Directory

No caso improvável de que os usuários existentes já correspondam aos nomes de usuário do Azure Active Directory, você pode conceder a função `azure_ad_user` a eles a fim de habilitá-los para a autenticação do Azure Active Directory:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Agora, eles poderão entrar com as credenciais do Azure Active Directory em vez de usar a senha de usuário do PostgreSQL configurada anteriormente.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Caso 2: Nome de usuário do PostgreSQL diferente do nome UPN do Azure Active Directory

Se um usuário do PostgreSQL não existir no Azure Active Directory ou tiver um nome de usuário diferente, você poderá usar os grupos do Azure Active Directory para se autenticar como este usuário do PostgreSQL. Você pode migrar os usuários existentes do Banco de Dados do Azure para PostgreSQL para o Azure Active Directory. Para isso, crie um grupo do Azure Active Directory com um nome que corresponda ao usuário do PostgreSQL e, em seguida, conceda a função azure_ad_user ao usuário existente do PostgreSQL:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Isso pressupõe que você criou um grupo "DBReadUser" no Azure Active Directory. Os usuários que pertencerem a esse grupo agora poderão entrar no banco de dados como esse usuário.

## <a name="next-steps"></a>Próximas etapas

* Consulte os conceitos gerais da [Autenticação do Azure Active Directory no Banco de Dados do Azure para PostgreSQL – Servidor único](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
