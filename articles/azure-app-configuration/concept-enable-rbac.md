---
title: Autorizar o acesso à Configuração de Aplicativos do Azure usando o Azure Active Directory
description: Habilitar o RBAC do Azure para autorizar o acesso à sua instância de configuração do Azure App
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 05/26/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 4768dbe292b7c71770ded1e8ad27025bc9944608
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930255"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Autorizar o acesso à Configuração de Aplicativos do Azure usando o Azure Active Directory
Além de usar o HMAC (Message Authentication Code baseado em hash), a configuração do Azure App dá suporte ao uso do Azure Active Directory (AD do Azure) para autorizar solicitações às instâncias de configuração do aplicativo.  O Azure AD permite que você use o Azure RBAC (controle de acesso baseado em função) para conceder permissões a uma entidade de segurança.  Uma entidade de segurança pode ser um usuário, uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) ou uma [entidade de serviço de aplicativo](../active-directory/develop/app-objects-and-service-principals.md).  Para saber mais sobre as funções e atribuições de função, confira [Noções básicas sobre funções diferentes](../role-based-access-control/overview.md).

## <a name="overview"></a>Visão geral
As solicitações feitas por uma entidade de segurança para acessar um recurso de configuração de aplicativo devem ser autorizadas. Com o Azure AD, o acesso a um recurso é um processo de duas etapas:
1. A identidade da entidade de segurança é autenticada e um token OAuth 2.0 é retornado.  O nome do recurso para solicitar um token é `https://login.microsoftonline.com/{tenantID}`, onde `{tenantID}` corresponde à ID do locatário do Azure Active Directory ao qual a entidade de serviço pertence.
2. O token é passado como parte de uma solicitação para o serviço de Configuração de Aplicativos para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que uma solicitação do aplicativo contenha um token de acesso OAuth 2.0 no runtime.  Se um aplicativo estiver em execução em uma entidade do Azure, como um aplicativo Azure Functions, um aplicativo Web do Azure ou uma VM do Azure, ele pode usar uma identidade gerenciada para acessar os recursos.  Para saber como autenticar solicitações feitas por uma identidade gerenciada para a Configuração de Aplicativos do Azure, confira [Autenticar o acesso aos recursos da Configuração de Aplicativos do Azure com o Azure Active Directory e as identidades gerenciadas para Recursos do Azure](howto-integrate-azure-managed-service-identity.md).

A etapa de autorização requer que uma ou mais funções do Azure sejam atribuídas à entidade de segurança. Azure App configuração fornece funções do Azure que abrangem conjuntos de permissões para recursos de configuração de aplicativo. As funções atribuídas a uma entidade de segurança determinam as permissões fornecidas à entidade. Para obter mais informações sobre as funções do Azure, consulte [funções internas do Azure para Azure app configuração](#azure-built-in-roles-for-azure-app-configuration). 

## <a name="assign-azure-roles-for-access-rights"></a>Atribuir funções do Azure para direitos de acesso
O Azure Active Directory (AD do Azure) autoriza os direitos de acesso a recursos protegidos por meio do [controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md).

Quando uma função do Azure é atribuída a uma entidade de segurança do Azure AD, o Azure concede acesso a esses recursos para essa entidade de segurança. O acesso é definido para o recurso de Configuração de Aplicativos. Uma entidade de segurança do Azure AD pode ser um usuário ou uma entidade de serviço de aplicativo ou uma [identidade gerenciada para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="azure-built-in-roles-for-azure-app-configuration"></a>Funções internas do Azure para Azure App configuração
O Azure fornece as seguintes funções internas do Azure para autorizar o acesso aos dados de configuração do aplicativo usando o Azure AD e o OAuth:

- **Proprietário de Dados da Configuração de Aplicativos**: Use essa função para conceder acesso de leitura/gravação/exclusão aos dados da Configuração de Aplicativos. Isso não concede acesso ao recurso de Configuração de Aplicativos.
- **Leitor de Dados da Configuração de Aplicativos**: Use essa função para conceder acesso de leitura aos dados da Configuração de Aplicativos. Isso não concede acesso ao recurso de Configuração de Aplicativos.
- **Colaborador**: Use essa função para gerenciar o recurso de Configuração de Aplicativos. Embora os dados de configuração do aplicativo possam ser acessados usando chaves de acesso, essa função não concede acesso direto aos dados usando o Azure AD.
- **Leitor**: Use essa função para conceder acesso de leitura ao recurso de Configuração de Aplicativos. Isso não concede acesso às chaves de acesso do recurso nem aos dados armazenados na Configuração de Aplicativos.

> [!NOTE]
> Atualmente, o portal do Azure dá suporte apenas à autenticação HMAC para acessar os dados de configuração do aplicativo. Não há suporte para a autenticação do Azure AD. Portanto, os usuários do portal do Azure exigem que a função de *colaborador* recupere as chaves de acesso do recurso de configuração de aplicativo. A concessão de dados de *configuração de aplicativo* ou funções de proprietário de dados de *configuração de aplicativo* não tem impacto sobre o acesso por meio do Portal.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o uso de [identidades gerenciadas](howto-integrate-azure-managed-service-identity.md) para administrar o serviço de Configuração de Aplicativos.
