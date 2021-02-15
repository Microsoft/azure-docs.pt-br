---
title: Autorizar o acesso com o Azure Active Directory
description: Este artigo fornece informações sobre como autorizar o acesso aos recursos do serviço de Signaler do Azure usando o Azure Active Directory.
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 37c2e41b5c81f941245b895ecd144baee3b9db6d
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797446"
---
# <a name="authorize-access-to-azure-signalr-service-resources-using-azure-active-directory"></a>Autorizar o acesso aos recursos do serviço de Signaler do Azure usando o Azure Active Directory
O serviço de Signaler do Azure dá suporte ao uso de Azure Active Directory (AD do Azure) para autorizar solicitações aos recursos do serviço Signaler do Azure. Com o Azure AD, você pode usar o RBAC (controle de acesso baseado em função) para conceder permissões a uma entidade de segurança, que pode ser um usuário ou uma entidade de serviço de aplicativo. Para saber mais sobre funções e atribuições de função, confira [noções básicas sobre as diferentes funções](../role-based-access-control/overview.md).

## <a name="overview"></a>Visão geral
Quando uma entidade de segurança (um aplicativo) tenta acessar um recurso do serviço de Signaler do Azure, a solicitação deve ser autorizada. Com o Azure AD, o acesso a um recurso é um processo de duas etapas. 

 1. Primeiro, a identidade da entidade de segurança é autenticada e um token OAuth 2,0 é retornado. O nome do recurso para solicitar um token é `https://signalr.azure.com/` .
 2. Em seguida, o token é passado como parte de uma solicitação para o serviço de Signaler do Azure para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que uma solicitação do aplicativo contenha um token de acesso OAuth 2.0 no runtime. Se o servidor de Hub estiver em execução em uma entidade do Azure, como uma VM do Azure, um conjunto de dimensionamento de máquinas virtuais ou um aplicativo de funções do Azure, ele poderá usar uma identidade gerenciada para acessar os recursos. Para saber como autenticar solicitações feitas por uma identidade gerenciada para o serviço de Signaler do Azure, confira [autenticar o acesso aos recursos do serviço de signaler do Azure com Azure Active Directory e identidades gerenciadas para recursos do Azure](authenticate-managed-identity.md). 

A etapa de autorização requer que uma ou mais funções RBAC sejam atribuídas à entidade de segurança. O serviço de Signaler do Azure fornece funções RBAC que abrangem conjuntos de permissões para recursos do Azure Signalr. As funções atribuídas a uma entidade de segurança determinam as permissões que o principal terá. Para obter mais informações sobre as funções RBAC, consulte [funções internas do Azure para o serviço de signaler do Azure](#azure-built-in-roles-for-azure-signalr-service). 

O servidor de Hub do signalr que não está em execução em uma entidade do Azure também pode autorizar com o Azure AD. Para saber como solicitar um token de acesso e usá-lo para autorizar solicitações para recursos do serviço de Signaler do Azure, confira [autenticar o acesso ao serviço de signaler do Azure com o Azure AD de um aplicativo](authenticate-application.md). 

## <a name="azure-built-in-roles-for-azure-signalr-service"></a>Funções internas do Azure para o serviço de Signaler do Azure

- [Servidor de aplicativos de sinalização]
- [Leitor de serviço do signalr]
- [Proprietário do serviço signalr]

## <a name="assign-rbac-roles-for-access-rights"></a>Atribuir funções RBAC para direitos de acesso
Azure Active Directory (Azure AD) autoriza os direitos de acesso aos recursos protegidos por meio do [controle de acesso baseado em função (RBAC)](../role-based-access-control/overview.md). O serviço de sinalizador do Azure define um conjunto de funções internas do Azure que abrangem conjuntos comuns de permissões usadas para acessar o serviço de Signaler do Azure e você também pode definir funções personalizadas para acessar o recurso.

Quando uma função RBAC é atribuída a uma entidade de segurança do Azure AD, o Azure concede acesso a esses recursos para essa entidade de segurança. O acesso pode ser definido para o nível de assinatura, o grupo de recursos ou qualquer recurso do serviço de Signaler do Azure. Uma entidade de segurança do Azure AD pode ser um usuário ou um aplicativo ou uma [identidade gerenciada para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-roles-for-azure-signalr-service"></a>Funções internas do serviço de Signaler do Azure
O Azure fornece as seguintes funções internas do Azure para autorizar o acesso ao recurso do serviço de Signaler do Azure usando o Azure AD e o OAuth:

### <a name="signalr-app-server"></a>Servidor de aplicativo signalr

Use essa função para conceder acesso para obter uma chave de acesso temporária para assinar tokens de cliente.

### <a name="signalr-serverless-contributor"></a>Colaborador sem servidor do signalr

Use essa função para conceder o acesso para obter um token de cliente diretamente do serviço de Signaler do Azure.

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos relacionados:

- [Autenticar um aplicativo com o Azure AD para acessar o serviço de Signaler do Azure](authenticate-application.md)
- [Autenticar uma identidade gerenciada com o Azure AD para acessar o serviço de Signaler do Azure](authenticate-managed-identity.md)