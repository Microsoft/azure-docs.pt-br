---
title: Usar o Azure Active Directory – Banco de Dados do Azure para MySQL
description: Saiba mais sobre como configurar o Azure Active Directory para a autenticação com o Banco de Dados do Azure para MySQL
author: sunilagarwal
ms.author: sunila
ms.service: mysql
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: 808c3589ba5b51b035ccc8165489c4d11203dd66
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612221"
---
# <a name="use-azure-active-directory-for-authentication-with-mysql"></a>Usar Azure Active Directory para autenticação com MySQL

Este artigo explicará as etapas de como configurar o acesso do Azure Active Directory com o Banco de Dados do Azure para MySQL e como se conectar usando um token do Azure Active Directory.

> [!IMPORTANT]
> Azure Active Directory autenticação só está disponível para MySQL 5,7 e mais recente.

## <a name="setting-the-azure-ad-admin-user"></a>Configurar o usuário administrador do Azure Active Directory

Somente usuários administradores do Azure Active Directory podem criar/habilitar usuários para a autenticação baseada no Azure Active Directory. Para criar um usuário administrador do Azure AD, siga as etapas a seguir

1. No portal do Azure, escolha a instância do Banco de Dados do Azure para MySQL que você deseja habilitar para o Azure Active Directory.
2. Em Configurações, selecione Administrador do Active Directory:

![definir administrador do azure ad][2]

3. Selecione um usuário válido do Azure Active Directory no locatário do cliente para ser administrador do Azure Active Directory.

> [!IMPORTANT]
> Ao definir o administrador, um novo usuário é adicionado ao servidor do Banco de Dados do Azure para MySQL com permissões completas de administrador.

Somente um administrador do Azure Active Directory pode ser criado por servidor MySQL, e a seleção de outro substituirá o administrador do Azure Active Directory existente configurado para o servidor.

Depois de configurar o administrador, será possível entrar:

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Conectar ao Banco de Dados do Azure para MySQL usando o Azure Active Directory

O diagrama de alto nível a seguir resume o fluxo de trabalho de uso da autenticação do Azure Active Directory com o Banco de Dados do Azure para MySQL:

![fluxo de autenticação][1]

Criamos a integração do Azure Active Directory para trabalhar com ferramentas comuns da CLI do MySQL, como a mysql, que não reconhecem o Azure Active Directory e só permitem a especificação do nome de usuário e da senha ao se conectar ao MySQL. Passamos o token do Azure Active Directory como a senha, conforme mostrado na imagem acima.

No momento, testamos os clientes a seguir:

- MySQLWorkbench 
- CLI do MySQL

Também testamos os drivers de aplicativos mais comuns. Veja os detalhes no final desta página.

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

Invoque a ferramenta de CLI do Azure para adquirir um token de acesso para o usuário autenticado do Azure Active Directory da etapa 1 para acessar o Banco de Dados do Azure para MySQL.

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

O token é uma cadeia de caracteres Base 64 que codifica todas as informações sobre o usuário autenticado e que é direcionado para o serviço do Banco de Dados do Azure para MySQL.

> [!NOTE]
> A validade do token de acesso está entre 5 e 60 minutos. Recomendamos que você obtenha o token de acesso logo antes de iniciar o logon no Banco de Dados do Azure para MySQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>Etapa 3: Use o token como senha para fazer logon no MySQL

Ao se conectar, é preciso usar o token de acesso como a senha de usuário do MySQL. Ao usar clientes de GUI, como MySQLWorkbench, é possível usar o método acima para recuperar o token. 

Ao usar a CLI, é possível usar essa alternativa para se conectar: 

**Exemplo (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

Considerações importantes ao se conectar:

* `user@tenant.onmicrosoft.com` é o nome do usuário ou grupo do Azure AD ao qual você está tentando se conectar
* Sempre acrescentar o nome do servidor após o nome de usuário/grupo do Azure AD (por exemplo, `@mydb` )
* Certifique-se de usar a maneira exata de que o nome de usuário ou grupo do Azure AD está escrito
* Os nomes de usuário e grupo do Azure AD diferenciam maiúsculas de minúsculas
* Ao conectar-se como um grupo, use apenas o nome do grupo (por exemplo, `GroupName@mydb` )
* Se o nome contiver espaços, use `\` antes de cada espaço para escapar

Observe a configuração "enable-cleartext-plugin": é preciso usar uma configuração semelhante com outros clientes para garantir que o token seja enviado ao servidor sem que haja hash.

Você já está autenticado no servidor MySQL usando a autenticação do Azure Active Directory.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Criar usuários do Azure Active Directory no Banco de Dados do Azure para MySQL

Para adicionar um usuário do Azure Active Directory ao banco de dados do Banco de Dados do Azure para MySQL, execute as etapas a seguir após a conexão (consulte a seção mais adiante sobre como se conectar):

1. Primeiro, garanta que o usuário do Azure Active Directory `<user>@yourtenant.onmicrosoft.com` seja um usuário válido no locatário do Azure Active Directory.
2. Entre na instância do Banco de Dados do Azure para MySQL como o usuário administrador do Azure Active Directory.
3. Criar usuário `<user>@yourtenant.onmicrosoft.com` no Banco de Dados do Azure para MySQL.

**Exemplo:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

Para nomes de usuário que excedem 32 caracteres, é recomendável usar um alias como alternativa, para ser usado ao conectar: 

Exemplo:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> A autenticação de um usuário por meio do Azure Active Directory não concede ao usuário nenhuma permissão para acessar objetos no banco de dados do Banco de Dados do Azure para MySQL. É preciso conceder ao usuário as permissões necessárias manualmente.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Criar grupos do Azure Active Directory no Banco de Dados do Azure para MySQL

Para habilitar um grupo do Azure Active Directory para acesso ao seu banco de dados, use o mesmo mecanismo que o dos usuários, mas especifique o nome do grupo:

**Exemplo:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Ao fazer logon, os membros do grupo usarão os tokens de acesso pessoal deles, mas assinarão com o nome do grupo especificado como o nome de usuário.

## <a name="token-validation"></a>Validação de token

A autenticação do Azure Active Directory no Banco de Dados do Azure para MySQL garante que o usuário existe no servidor MySQL e verifica a validade do token por meio da validação do conteúdo do token. As seguintes etapas de validação de token são executadas:

-   O token é assinado pelo Azure Active Directory e não foi violado
-   O token foi emitido pelo Azure Active Directory para o locatário associado ao servidor
-   O token não expirou
-   O token é para o recurso do Banco de Dados do Azure para MySQL (não para outro recurso do Azure)

## <a name="compatibility-with-application-drivers"></a>Compatibilidade com drivers de aplicativo

A maioria dos drivers é compatível, no entanto, use as configurações para enviar a senha em texto não criptografado, para que o token seja enviado sem mudança.

* C/C++
  * libmysqlclient: Com suporte
  * mysql-connector-c++: Com suporte
* Java
  * Connector/J (mysql-connector-java): Compatível, é preciso utilizar a configuração `useSSL`
* Python
  * Conector/Python: Com suporte
* Ruby
  * mysql2: Com suporte
* .NET
  * mysql-connector-net: Compatível, é necessário adicionar o plug-in para mysql_clear_password
  * mysql-net/MySqlConnector: Com suporte
* Node.js
  * mysqljs: Não compatível (não envia token em texto não criptografado sem patch)
  * node-mysql2: Com suporte
* Perl
  * DBD::mysql: Com suporte
  * Net::MySQL: Sem suporte
* Go
  * go-sql-driver: Compatível, adicione `?tls=true&allowCleartextPasswords=true` à cadeia de conexão

## <a name="next-steps"></a>Próximas etapas

* Consulte os conceitos gerais da [Autenticação do Azure Active Directory com o Banco de Dados do Azure para MySQL](concepts-azure-ad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
