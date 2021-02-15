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
ms.date: 01/21/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67870a458138101f3b8a009f7c96c74991396284
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675179"
---
# <a name="register-a-microsoft-graph-application"></a>Registrar um aplicativo Microsoft Graph

[Microsoft Graph][ms-graph] permite que você gerencie muitos dos recursos em seu locatário Azure ad B2C, incluindo contas de usuário do cliente e políticas personalizadas. Ao escrever scripts ou aplicativos que chamam a [API de Microsoft Graph][ms-graph-api], você pode automatizar tarefas de gerenciamento de locatários como:

* Migrar um repositório de usuários existente para um locatário Azure AD B2C
* Implantar políticas personalizadas com um pipeline do Azure no Azure DevOps e gerenciar chaves de política personalizadas
* O registro de usuário do host em sua própria página e a criação de contas de usuário em seu diretório Azure AD B2C em segundo plano
* Automatizar o registro de aplicativos
* Obter logs de auditoria

As seções a seguir ajudam você a se preparar para usar o Microsoft Graph API para automatizar o gerenciamento de recursos em seu diretório Azure AD B2C.

## <a name="microsoft-graph-api-interaction-modes"></a>Modos de interação do Microsoft Graph API

Há dois modos de comunicação que você pode usar ao trabalhar com a API de Microsoft Graph para gerenciar recursos em seu locatário Azure AD B2C:

* **Interativo** – apropriado para tarefas de execução única, você usa uma conta de administrador no locatário B2C para executar as tarefas de gerenciamento. Esse modo requer que um administrador entre usando suas credenciais antes de chamar a API de Microsoft Graph.

* **Automatizado** – para tarefas de execução agendadas ou contínuas, esse método usa uma conta de serviço que você configura com as permissões necessárias para executar tarefas de gerenciamento. Você cria a "conta de serviço" em Azure AD B2C registrando um aplicativo que seus aplicativos e scripts usam para autenticação usando sua *ID de aplicativo (cliente)* e a concessão de **credenciais de cliente do OAuth 2,0** . Nesse caso, o aplicativo atua como ele próprio para chamar a API de Microsoft Graph, não o usuário administrador como no método interativo descrito anteriormente.

Você habilita o cenário de interação **automatizada** criando um registro de aplicativo mostrado nas seções a seguir.

Embora o fluxo de concessão de credenciais de cliente do OAuth 2,0 não tenha suporte direto no serviço de autenticação Azure AD B2C, você pode configurar o fluxo de credenciais do cliente usando o Azure AD e o ponto de extremidade/token da plataforma de identidade da Microsoft para um aplicativo em seu locatário Azure AD B2C. Um locatário do Azure AD B2C compartilha algumas funcionalidades com os locatários corporativos do Azure AD.

## <a name="register-management-application"></a>Registrar aplicativo de gerenciamento

Antes que seus scripts e aplicativos possam interagir com a [API de Microsoft Graph][ms-graph-api] para gerenciar Azure ad B2C recursos, você precisa criar um registro de aplicativo em seu locatário Azure ad B2C que conceda as permissões de API necessárias.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Escolha **Registros de aplicativo** e **Novo registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *managementapp1*.
1. Escolha **Somente contas neste diretório organizacional**.
1. Em **permissões**, desmarque a caixa de seleção *conceder consentimento de administrador às permissões OpenID e offline_access* .
1. Selecione **Registrar**.
1. Registre a **ID do aplicativo (cliente)** que aparece na página Visão geral do aplicativo. Você usará esse valor em uma etapa posterior.

### <a name="grant-api-access"></a>Conceder acesso à API

Em seguida, conceda as permissões do aplicativo registrado para manipular os recursos de locatário por meio de chamadas para a API Microsoft Graph.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Criar segredo do cliente

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Agora você tem um aplicativo que tem permissão para *criar*, *ler*, *Atualizar* e *excluir* usuários em seu locatário Azure ad B2C. Continue na próxima seção para adicionar permissões de *atualização de senha* .

## <a name="enable-user-delete-and-password-update"></a>Habilitar exclusão de usuário e atualização de senha

A permissão *ler e gravar dados* de diretório **não inclui a** capacidade de excluir usuários ou atualizar senhas de conta de usuário.

Se seu aplicativo ou script precisar excluir usuários ou atualizar suas senhas, atribua a função de *administrador de usuários* ao seu aplicativo:

1. Entre no [portal do Azure](https://portal.azure.com) e use o filtro **diretório + assinatura** para alternar para o locatário Azure ad B2C.
1. Pesquise e selecione **Azure AD B2C**.
1. Em **gerenciar**, selecione **funções e administradores**.
1. Selecione a função de **administrador de usuários** .
1. Selecione **Adicionar atribuições**.
1. Na caixa de texto **selecionar** , digite o nome do aplicativo que você registrou anteriormente, por exemplo, *managementapp1*. Selecione seu aplicativo quando ele aparecer nos resultados da pesquisa.
1. Selecione **Adicionar**. Pode levar alguns minutos para que as permissões se propaguem totalmente.

## <a name="next-steps"></a>Próximas etapas

Agora que você registrou seu aplicativo de gerenciamento e concedeu a ele as permissões necessárias, seus aplicativos e serviços (por exemplo, Azure Pipelines) podem usar suas credenciais e permissões para interagir com a API de Microsoft Graph. 

* [Obter um token de acesso do Azure AD](/graph/auth-v2-service#4-get-an-access-token)
* [Usar o token de acesso para chamar Microsoft Graph](/graph/auth-v2-service#4-get-an-access-token)
* [Operações B2C com suporte pelo Microsoft Graph](microsoft-graph-operations.md)
* [Gerenciar Azure AD B2C contas de usuário com Microsoft Graph](microsoft-graph-operations.md)
* [Obter logs de auditoria com a API de relatórios do Azure AD](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: /graph/
[ms-graph-api]: /graph/api/overview
