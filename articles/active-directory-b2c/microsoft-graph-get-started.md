---
title: Registrar um aplicativo Microsoft Graph
titleSuffix: Azure AD B2C
description: Prepare-se para gerenciar recursos do Azure AD B2C com o Microsoft Graph registrando um aplicativo que recebe as permissões de API do Graph necessárias.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0dcb959184e12ffa22ae25443087684123598e47
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382447"
---
# <a name="register-a-microsoft-graph-application"></a>Registrar um aplicativo Microsoft Graph

O [Microsoft Graph][ms-graph] permite que você gerencie muitos dos recursos em seu locatário do Azure AD B2C, incluindo contas de usuário do cliente e políticas personalizadas. Ao gravar scripts ou aplicativos que chamam a [API do Microsoft Graph][ms-graph-api], você pode automatizar tarefas de gerenciamento de locatários, como:

* Migrar o repositório de usuários existente para um locatário do Azure AD B2C
* Implantar políticas personalizadas com um Pipeline do Azure no Azure DevOps e gerenciar chaves de política personalizadas
* Hospedar o registro de usuário em sua página e criar contas de usuário no seu diretório do Azure AD B2C nos bastidores
* Automatizar o registro de aplicativo
* Obter logs de auditoria

As seções a seguir ajudam você a se preparar para usar a API do Microsoft Graph para automatizar o gerenciamento de recursos em seu diretório do Azure AD B2C.

## <a name="microsoft-graph-api-interaction-modes"></a>Modos de interação da API do Microsoft Graph

Há dois modos de comunicação que você pode usar ao trabalhar com a API do Microsoft Graph para gerenciar recursos em seu locatário do Azure AD B2C:

* **Interativo**: apropriado para tarefas de execução única, você usa uma conta do administrador no locatário do B2C para executar as tarefas de gerenciamento. Esse modo requer que um administrador entre usando suas credenciais antes de chamar a API do Microsoft Graph.

* **Automatizado**: para tarefas de execução agendadas ou contínuas, esse método usa uma conta de serviço que você configura com as permissões necessárias para executar tarefas de gerenciamento. Você cria a "conta de serviço" no Azure AD B2C registrando um aplicativo que seus aplicativos e scripts usam para autenticação usando sua *ID de aplicativo (cliente)* e a concessão de **credenciais de cliente do OAuth 2.0**. Nesse caso, o aplicativo atua como ele próprio para chamar a API do Microsoft Graph, não o usuário administrador como no método interativo descrito anteriormente.

Você habilita o cenário de interação **automatizada** criando um registro de aplicativo mostrado nas seções a seguir.

Embora o fluxo de concessão de credenciais de cliente do OAuth 2.0 no momento não tenha suporte direto no serviço de autenticação Azure AD B2C, você pode configurar o fluxo de credenciais do cliente usando o Azure AD e o ponto de extremidade/token da plataforma de identidade da Microsoft para um aplicativo em seu locatário do Azure AD B2C. Um locatário do Azure AD B2C compartilha algumas funcionalidades com os locatários corporativos do Azure AD.

## <a name="register-management-application"></a>Registrar aplicativo de gerenciamento

Antes que seus scripts e aplicativos possam interagir com a [API do Microsoft Graph][ms-graph-api] para gerenciar os recursos do Azure AD B2C, você precisa criar um registro de aplicativo no seu locatário do Azure AD B2C que concede as permissões de API necessárias.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Escolha **Registros de aplicativo** e **Novo registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *managementapp1*.
1. Escolha **Somente contas neste diretório organizacional**.
1. Em **Permissões**, desmarque a caixa de seleção *Dar consentimento do administrador às permissões OpenID e offline_access*.
1. Selecione **Registrar**.
1. Registre a **ID do Aplicativo (cliente)** mostrada na página de visão geral do aplicativo. Você usa esse valor em uma etapa posterior.

## <a name="grant-api-access"></a>Conceder acesso à API

Para que seu aplicativo acesse dados no Microsoft Graph, conceda ao aplicativo registrado as [permissões de aplicativo](https://docs.microsoft.com/graph/permissions-reference) relevantes. As permissões efetivas do seu aplicativo são o nível completo de privilégios implícitos pela permissão. Por exemplo, para *criar*, *ler*, *atualizar* e *excluir* todos os usuários em seu locatário do Azure AD B2C, adicione a permissão **User.ReadWrite.All**. 

> [!NOTE]
> A permissão **User.ReadWrite.All** não inclui a capacidade de atualizar senhas de conta de usuário. Se o seu aplicativo precisar atualizar senhas da conta de usuário, [conceda a função de administrador de usuários](#optional-grant-user-administrator-role). Ao conceder a função de [administrador de usuários](../active-directory/roles/permissions-reference.md#user-administrator), o **User.ReadWrite.All** não é necessário. A função de administrador de usuários inclui tudo o que é necessário para gerenciar usuários.

Você pode conceder ao aplicativo várias permissões de aplicativo. Por exemplo, se o seu aplicativo também precisar gerenciar grupos em seu locatário do Azure AD B2C, adicione também a permissão **Group.ReadWrite.All**. 

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]


## <a name="optional-grant-user-administrator-role"></a>[Opcional] Conceder a função de administrador de usuários

Se o seu aplicativo ou script precisar atualizar as senhas dos usuários, você precisará atribuir a função de *Administrador de usuários* ao seu aplicativo. A função de [administrador de usuários](../active-directory/roles/permissions-reference.md#user-administrator) tem um conjunto fixo de permissões que você concede ao seu aplicativo. 

Para adicionar a função de *administrador de usuários*, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com) e use o filtro **Diretório + Assinatura** para alternar para o locatário do Azure AD B2C.
1. Pesquise e selecione **Azure AD B2C**.
1. Em **Gerenciar**, selecione **Funções e administradores**.
1. Selecione a função de **Administrador de usuários**. 
1. Selecione **Adicionar atribuições**.
1. Na caixa de texto **Selecionar**, insira o nome ou a ID do aplicativo que você registrou anteriormente, por exemplo, *managementapp1*. Quando ele aparecer nos resultados da pesquisa, selecione seu aplicativo.
1. Selecione **Adicionar**. Pode levar alguns minutos para que as permissões sejam totalmente propagadas.

## <a name="create-client-secret"></a>Criar o segredo do cliente

Seu aplicativo precisa de um segredo do cliente para provar sua identidade ao solicitar um token. Para adicionar o segredo do cliente, siga estas etapas:

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]


## <a name="next-steps"></a>Próximas etapas

Agora que você registrou seu aplicativo de gerenciamento e concedeu a ele as permissões necessárias, seus aplicativos e serviços (por exemplo, Azure Pipelines) podem usar suas credenciais e permissões para interagir com a API do Microsoft Graph. 

* [Obter um token de acesso do Azure AD](/graph/auth-v2-service#4-get-an-access-token)
* [Usar o token de acesso para chamar o Microsoft Graph](/graph/auth-v2-service#4-get-an-access-token)
* [Operações do B2C com suporte pelo Microsoft Graph](microsoft-graph-operations.md)
* [Gerenciar contas de usuário do Azure AD B2C com Microsoft Graph](microsoft-graph-operations.md)
* [Integrar os logs de auditoria com a API de relatório do Azure AD](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: /graph/
[ms-graph-api]: /graph/api/overview
