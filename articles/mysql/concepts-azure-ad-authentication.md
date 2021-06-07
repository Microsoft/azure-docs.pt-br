---
title: Autenticação do Active Directory - Banco de Dados do Azure para MySQL
description: Saiba mais sobre os conceitos do Azure Active Directory para a autenticação com o Banco de Dados do Azure para MySQL
author: sunilagarwal
ms.author: sunila
ms.service: mysql
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: e3afa7e7129c34aae8487affe979bd7704580a1e
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612442"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Usar o Azure Active Directory para autenticação com o MySQL

A autenticação do Microsoft Azure Active Directory (Azure AD) é um mecanismo de conexão ao Banco de Dados do Azure para MySQL usando identidades definidas no Azure AD.
Com a autenticação do Azure AD, é possível gerenciar as identidades de usuários do banco de dados e outros serviços da Microsoft em uma única localização central, o que simplifica o gerenciamento de permissões.

Os benefícios de usar o Azure AD incluem:

- Autenticação de usuários em Serviços do Azure de forma uniforme
- Gerenciamento de políticas de senha e rotação de senhas em um único local
- Várias formas de autenticação com suporte pelo Azure Active Directory, o que pode eliminar a necessidade de armazenar senhas
- Os clientes podem gerenciar permissões de banco de dados usando grupos (Azure AD) externos.
- A autenticação do Azure AD usa usuários de banco de dados MySQL para autenticar identidades no banco de dados
- Suporte de autenticação baseada em token em aplicativos que se conectam ao Banco de Dados do Azure para MySQL

Para configurar e usar a autenticação do Active Directory do Azure, use o seguinte processo:

1. Crie e popule o Azure Active Directory com identidades de usuário, conforme necessário.
2. Opcionalmente, associe ou altere o Active Directory que está associado atualmente à sua assinatura do Azure.
3. Crie um administrador do Azure AD para o Banco de Dados do Azure para MySQL.
4. Crie usuários de banco de dados em seu banco de dados, mapeados para identidades do Azure AD.
5. Conecte-se ao banco de dados recuperando um token para uma identidade do Azure AD e entrando.

> [!NOTE]
> Para saber como criar e popular o Azure AD e configurar o Azure AD com o Banco de Dados do Azure para MySQL, confira [Configurar e entrar com o Azure AD para Banco de Dados do Azure para MySQL](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="architecture"></a>Arquitetura

O diagrama de alto nível a seguir resume como a autenticação funciona usando a autenticação do Azure Active Directory com o Banco de Dados do Azure para MySQL. As setas indicam caminhos para comunicação.

![fluxo de autenticação][1]

## <a name="administrator-structure"></a>Estrutura do administrador

Quando você usar a autenticação do Azure AD, haverá duas contas de administrador para o servidor do MySQL: o administrador original do MySQL e o administrador do Azure AD. Somente o administrador com base em uma conta do AD do Azure pode criar o primeiro usuário de banco de dados do AD do Azure contido em um banco de dados de usuário. O logon de administrador do AD do Azure pode ser um usuário ou um grupo do AD do Azure. Quando o administrador é uma conta de grupo, ele pode ser usado por qualquer membro do grupo, permitindo múltiplos administradores do Azure AD para o servidor MySQL. Usar a conta de grupo como um administrador aprimora a capacidade de gerenciamento, permitindo que você adicione e remova membros do grupo no Azure AD centralmente, sem alterar os usuários ou as permissões no servidor MySQL. Somente um administrador do AD do Azure (um usuário ou grupo) pode ser configurado por vez, a qualquer momento.

![estrutura de administrador][2]

## <a name="permissions"></a>Permissões

Para criar novos usuários que podem se autenticar com o Azure AD, você deve ser o administrador do Azure Active Directory designado. Esse usuário é atribuído configurando a conta de Administrador do Azure AD para um servidor de Banco de Dados do Azure para MySQL.

Para criar um novo usuário de banco de dados do Azure AD, conecte-se como administrador do Azure AD. Isso é demonstrado em [Configurar e entrar com o Azure AD para o Banco de Dados do Azure para MySQL](howto-configure-sign-in-azure-ad-authentication.md).

Qualquer autenticação do Azure AD só será possível se o administrador do Azure AD tiver sido criado para o Banco de Dados do Azure para MySQL. Se o administrador do Azure Active Directory tiver sido removido do servidor, os usuários existentes do Azure Active Directory criados anteriormente não poderão mais se conectar ao banco de dados usando suas credenciais do Azure Active Directory.

## <a name="connecting-using-azure-ad-identities"></a>Conectar-se usando as identidades do Azure AD

A autenticação do Active Directory do Azure dá suporte aos seguintes métodos de conexão a um banco de dados usando identidades do AD do Azure:

- Senha do Azure Active Directory
- Integrada do Azure Active Directory
- Universal do Azure Active Directory com o MFA
- Usar certificados de aplicativos do Active Directory ou segredos de cliente
- [Identidade gerenciada](howto-connect-with-managed-identity.md)

Depois de estar autenticado no Active Directory, você recuperará um token. Esse token é sua senha para entrar.

Observe que as operações de gerenciamento, como adicionar novos usuários, têm suporte apenas para funções de usuário do Azure AD neste momento.

> [!NOTE]
> Para obter mais detalhes sobre como se conectar com um token do Active Directory, confira [Configurar e entrar com o Azure AD para o Banco de Dados do Azure para MySQL](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="additional-considerations"></a>Considerações adicionais

- Azure Active Directory autenticação só está disponível para MySQL 5,7 e mais recente.
- Somente um administrador do Azure AD pode ser configurado para um servidor do Banco de Dados do Azure para MySQL a qualquer momento.
- Somente um administrador do Azure AD para MySQL pode se conectar inicialmente ao Banco de Dados do Azure para MySQL usando uma conta do Azure Active Directory. O administrador do Active Directory pode configurar os próximos usuários do banco de dados do Azure AD.
- Se um usuário for excluído do Azure AD, esse usuário não poderá mais se autenticar com o Azure AD e, portanto, não será mais possível adquirir um token de acesso para esse usuário. Nesse caso, embora o usuário correspondente ainda esteja no banco de dados, não será possível se conectar ao servidor com esse usuário.
> [!NOTE]
> Ainda será possível entrar com o usuário do Azure AD excluído até que o token expire (até 60 minutos de emissão de tokens).  Se você também remover o usuário do Banco de Dados do Azure para MySQL, esse acesso será revogado imediatamente.
- Se o administrador do Azure AD for removido do servidor, o servidor não será mais associado a um locatário do Azure AD e, portanto, todos os logons do Azure AD serão desabilitados para o servidor. A adição de um novo administrador do Azure AD no mesmo locatário reabilitará logons do Azure AD.
- O Banco de Dados do Azure para MySQL corresponde aos tokens de acesso para o usuário do Banco de Dados do Azure para MySQL usando a ID de usuário do Azure AD exclusiva do usuário, em vez do nome de usuário. Isso significa que, caso um usuário do Azure AD seja excluído do Azure AD e um novo usuário seja criado com o mesmo nome, o Banco de Dados do Azure para MySQL o considera um usuário diferente. Portanto, se um usuário for excluído do Azure AD e um novo usuário com o mesmo nome for adicionado, o novo usuário não poderá se conectar com o usuário existente.

## <a name="next-steps"></a>Próximas etapas

- Para saber como criar e popular o Azure AD e configurar o Azure AD com o Banco de Dados do Azure para MySQL, confira [Configurar e entrar com o Azure AD para Banco de Dados do Azure para MySQL](howto-configure-sign-in-azure-ad-authentication.md).
- Para obter uma visão geral de logons e usuários do Banco de Dados do Azure para MySQL, confira [Criar usuários no Banco de Dados do Azure para MySQL](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
