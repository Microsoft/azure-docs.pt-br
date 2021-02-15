---
title: API REST de configuração de Azure App-Azure Active Directory autorização
description: Usar Azure Active Directory para autorização em relação à configuração de Azure App usando a API REST
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: f144c1f09f87e0b915daf86ba0391c2934e60095
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932669"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Autorização de Azure Active Directory – referência da API REST

Quando você usa a autenticação do Azure Active Directory (Azure AD), a autorização é manipulada pelo RBAC (controle de acesso baseado em função). O RBAC requer que os usuários sejam atribuídos a funções para conceder acesso aos recursos. Cada função contém um conjunto de ações que os usuários atribuídos à função podem executar.

## <a name="roles"></a>Funções

As seguintes funções estão disponíveis em assinaturas do Azure por padrão:

- **Proprietário do Azure app de dados de configuração**: essa função fornece acesso completo a todas as operações.
- **Azure app o leitor de dados de configuração**: essa função habilita operações de leitura.

## <a name="actions"></a>Ações

As funções contêm uma lista de ações que os usuários atribuídos a essa função podem executar. Azure App configuração dá suporte às seguintes ações:

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: Essa ação permite o acesso de leitura aos recursos de chave de configuração de aplicativo, como/kV e/Labels.
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: Essa ação permite o acesso de gravação aos recursos chave de configuração de aplicativo-valor.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: Essa ação permite que os recursos de chave de configuração de aplicativo sejam excluídos. Observe que a exclusão de um recurso retorna o valor-chave que foi excluído.

## <a name="error"></a>Erro

```http
HTTP/1.1 403 Forbidden
```

**Motivo:** A entidade de segurança que faz a solicitação não tem as permissões necessárias para executar a operação solicitada.
**Solução:** Atribua a função necessária para executar a operação solicitada para a entidade de segurança que faz a solicitação.

## <a name="managing-role-assignments"></a>Gerenciando atribuições de função

Você pode gerenciar atribuições de função usando [procedimentos RBAC](../role-based-access-control/overview.md) que são padrão em todos os serviços do Azure. Você pode fazer isso por meio do CLI do Azure, do PowerShell e do portal do Azure. Para obter mais informações, consulte [Adicionar ou remover atribuições de função do Azure usando o portal do Azure](../role-based-access-control/role-assignments-portal.md).