---
title: Autenticar um aplicativo para acessar entidades do barramento de serviço do Azure
description: Este artigo fornece informações sobre como autenticar um aplicativo com Azure Active Directory para acessar entidades do barramento de serviço do Azure (filas, tópicos, etc.)
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: c4e19c0ab26d491ba0b95159e274383431aefaee
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518221"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Autenticar e autorizar um aplicativo com Azure Active Directory para acessar entidades do barramento de serviço do Azure
O barramento de serviço do Azure dá suporte ao uso de Azure Active Directory (AD do Azure) para autorizar solicitações para entidades do barramento de serviço (filas, tópicos, assinaturas ou filtros). Com o Azure AD, você pode usar o Azure RBAC (controle de acesso baseado em função) para conceder permissões a uma entidade de segurança, que pode ser um usuário, grupo ou entidade de serviço de aplicativo. Para saber mais sobre funções e atribuições de função, confira [noções básicas sobre as diferentes funções](../role-based-access-control/overview.md).

## <a name="overview"></a>Visão geral
Quando uma entidade de segurança (um usuário, grupo ou aplicativo) tenta acessar uma entidade do barramento de serviço, a solicitação deve ser autorizada. Com o Azure AD, o acesso a um recurso é um processo de duas etapas. 

 1. Primeiro, a identidade da entidade de segurança é autenticada e um token OAuth 2,0 é retornado. O nome do recurso para solicitar um token é `https://servicebus.azure.net` .
 1. Em seguida, o token é passado como parte de uma solicitação para o serviço do barramento de serviço para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que uma solicitação do aplicativo contenha um token de acesso OAuth 2.0 no runtime. Se um aplicativo estiver em execução em uma entidade do Azure, como uma VM do Azure, um conjunto de dimensionamento de máquinas virtuais ou um aplicativo de funções do Azure, ele poderá usar uma identidade gerenciada para acessar os recursos. Para saber como autenticar solicitações feitas por uma identidade gerenciada para o serviço do barramento de serviço, consulte [autenticar o acesso aos recursos do barramento de serviço do Azure com Azure Active Directory e identidades gerenciadas para recursos do Azure](service-bus-managed-service-identity.md). 

A etapa de autorização requer que uma ou mais funções do Azure sejam atribuídas à entidade de segurança. O barramento de serviço do Azure fornece funções do Azure que abrangem conjuntos de permissões para recursos do barramento de serviço. As funções atribuídas a uma entidade de segurança determinam as permissões que o principal terá. Para saber mais sobre como atribuir funções do Azure ao barramento de serviço do Azure, confira [funções internas do Azure para o barramento de serviço do Azure](#azure-built-in-roles-for-azure-service-bus). 

Aplicativos nativos e aplicativos Web que fazem solicitações para o barramento de serviço também podem autorizar com o Azure AD. Este artigo mostra como solicitar um token de acesso e usá-lo para autorizar solicitações para recursos do barramento de serviço. 


## <a name="assigning-azure-roles-for-access-rights"></a>Atribuindo funções do Azure para direitos de acesso
O Azure Active Directory (AD do Azure) autoriza os direitos de acesso a recursos protegidos por meio [do RBAC do Azure](../role-based-access-control/overview.md). O barramento de serviço do Azure define um conjunto de funções internas do Azure que abrangem conjuntos comuns de permissões usadas para acessar entidades do barramento de serviço e você também pode definir funções personalizadas para acessar os dados.

Quando uma função do Azure é atribuída a uma entidade de segurança do Azure AD, o Azure concede acesso a esses recursos para essa entidade de segurança. O acesso pode ser definido para o nível de assinatura, o grupo de recursos ou o namespace do barramento de serviço. Uma entidade de segurança do Azure AD pode ser um usuário, um grupo, uma entidade de serviço de aplicativo ou uma [identidade gerenciada para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Funções internas do Azure para o barramento de serviço do Azure
Para o barramento de serviço do Azure, o gerenciamento de namespaces e todos os recursos relacionados por meio do portal do Azure e a API de gerenciamento de recursos do Azure já estão protegidos usando o modelo RBAC do Azure. O Azure fornece as funções internas do Azure abaixo para autorizar o acesso a um namespace do barramento de serviço:

- [Proprietário de dados do barramento de serviço do Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): habilita o acesso a dados ao namespace do barramento de serviço e suas entidades (filas, tópicos, assinaturas e filtros)
- [Remetente de dados do barramento de serviço do Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Use essa função para fornecer acesso de envio ao namespace do barramento de serviço e suas entidades.
- [Receptor de dados do barramento de serviço do Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Use essa função para conceder acesso de recebimento ao namespace do barramento de serviço e suas entidades. 

## <a name="resource-scope"></a>Escopo do recurso 
Antes de atribuir uma função do Azure a uma entidade de segurança, determine o escopo do acesso que essa entidade de segurança deve ter. De acordo com as melhores práticas, sempre é melhor conceder o escopo mais estreito possível.

A lista a seguir descreve os níveis nos quais você pode fazer o escopo de acesso aos recursos do barramento de serviço, começando com o escopo mais estreito:

- **Fila**, **tópico**ou **assinatura**: a atribuição de função se aplica à entidade de barramento de serviço específica. Atualmente, o portal do Azure não dá suporte à atribuição de usuários/grupos/identidades gerenciadas às funções do Azure do barramento de serviço no nível da assinatura. 
- **Namespace do barramento de serviço**: a atribuição de função abrange toda a topologia do barramento de serviço no namespace e no grupo de consumidores associado a ela.
- **Grupo de recursos**: a atribuição de função se aplica a todos os recursos do barramento de serviço no grupo de recursos.
- **Assinatura**: a atribuição de função se aplica a todos os recursos do barramento de serviço em todos os grupos de recursos na assinatura.

> [!NOTE]
> Tenha em mente que as atribuições de função do Azure podem levar até cinco minutos para serem propagadas. 

Para obter mais informações sobre como as funções internas são definidas, consulte [entender as definições de função](../role-based-access-control/role-definitions.md#management-and-data-operations). Para obter informações sobre como criar funções personalizadas do Azure, consulte [funções personalizadas do Azure](../role-based-access-control/custom-roles.md).


## <a name="assign-azure-roles-using-the-azure-portal"></a>Atribuir funções do Azure usando o portal do Azure  
Para saber mais sobre como gerenciar o acesso aos recursos do Azure usando o RBAC do Azure e o portal do Azure, consulte [Este artigo](..//role-based-access-control/role-assignments-portal.md). 

Depois de determinar o escopo apropriado para uma atribuição de função, navegue até esse recurso na portal do Azure. Exiba as configurações de controle de acesso (IAM) para o recurso e siga estas instruções para gerenciar as atribuições de função:

> [!NOTE]
> As etapas descritas abaixo atribuim uma função ao namespace do barramento de serviço. Você pode seguir as mesmas etapas para atribuir uma função a outros escopos com suporte (grupo de recursos, assinatura, etc.).

1. Na [portal do Azure](https://portal.azure.com/), navegue até o namespace do barramento de serviço. Selecione **controle de acesso (iam)** no menu à esquerda para exibir as configurações de controle de acesso para o namespace. Se você precisar criar um namespace do barramento de serviço, siga as instruções deste artigo: [criar um namespace de mensagens do barramento de serviço](service-bus-create-namespace-portal.md).

    ![Selecione controle de acesso no menu à esquerda](./media/authenticate-application/select-access-control-menu.png)
1. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais. Selecione o botão **Adicionar** na barra de ferramentas e, em seguida, selecione **Adicionar atribuição de função**. 

    ![Botão Adicionar na barra de ferramentas](./media/authenticate-application/role-assignments-add-button.png)
1. Na página **Adicionar atribuição de função** , execute as seguintes etapas:
    1. Selecione a **função de barramento de serviço** que você deseja atribuir. 
    1. Pesquise para localizar a **entidade de segurança** (usuário, grupo, entidade de serviço) à qual você deseja atribuir a função.
    1. Selecione **salvar** para salvar a atribuição de função. 

        ![Atribuir função a um usuário](./media/authenticate-application/assign-role-to-user.png)
    4. A identidade à qual você atribuiu a função aparece listada sob essa função. Por exemplo, a imagem a seguir mostra que os usuários do Azure estão na função de proprietário de dados do barramento de serviço do Azure. 
        
        ![Usuário na lista](./media/authenticate-application/user-in-list.png)

Você pode seguir etapas semelhantes para atribuir uma função com escopo a um grupo de recursos ou uma assinatura. Depois de definir a função e seu escopo, você pode testar esse comportamento com os [exemplos no GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).


## <a name="authenticate-from-an-application"></a>Autenticar a partir de um aplicativo
Uma vantagem importante de usar o Azure AD com o barramento de serviço é que suas credenciais não precisam mais ser armazenadas em seu código. Em vez disso, você pode solicitar um token de acesso OAuth 2,0 da plataforma de identidade da Microsoft. O Azure AD autentica a entidade de segurança (um usuário, um grupo ou uma entidade de serviço) que executa o aplicativo. Se a autenticação for bem sucedido, o Azure AD retornará o token de acesso para o aplicativo e o aplicativo poderá usar o token de acesso para autorizar solicitações ao barramento de serviço do Azure.

As seções a seguir mostram como configurar seu aplicativo nativo ou aplicativo Web para autenticação com a plataforma de identidade da Microsoft 2,0. Para obter mais informações sobre a plataforma de identidade da Microsoft 2,0, consulte [visão geral da plataforma Microsoft Identity (v 2.0)](../active-directory/develop/v2-overview.md).

Para obter uma visão geral do fluxo de concessão do código do OAuth 2.0, consulte [Autorizar acesso a aplicativos Web do Azure Active Directory usando o fluxo de concessão do código do OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registre o aplicativo com um locatário do Azure AD
A primeira etapa no uso do Azure AD para autorizar entidades do barramento de serviço é registrar seu aplicativo cliente com um locatário do Azure AD do [portal do Azure](https://portal.azure.com/). Ao registrar seu aplicativo cliente, você fornece informações sobre o aplicativo para o AD. Em seguida, o Azure AD fornece uma ID do cliente (também chamada de ID do aplicativo) que você pode usar para associar seu aplicativo ao tempo de execução do Azure AD. Para saber mais sobre a ID do cliente, consulte [Objetos de entidade de serviço e aplicativo no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

As imagens a seguir mostram as etapas para registrar um aplicativo Web:

![Registrar um aplicativo](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Se você registrar seu aplicativo como um aplicativo nativo, poderá especificar qualquer URI válido para o URI de redirecionamento. Para aplicativos nativos, esse valor não precisa ser uma URL real. Para aplicativos Web, o URI de redirecionamento deve ser um URI válido, pois ele especifica a URL para a qual os tokens são fornecidos.

Depois de registrar seu aplicativo, você verá a ID do **aplicativo (cliente)** em **configurações**:

![ID do aplicativo registrado](./media/authenticate-application/application-id.png)

Para obter mais informações sobre como registrar um aplicativo no Azure AD, consulte [Integrando aplicativos com o Azure Active Directory](../active-directory/develop/quickstart-register-app.md).

> [!IMPORTANT]
> Anote a **tenantid** e a **ApplicationId**. Você precisará desses valores para executar o aplicativo.

### <a name="create-a-client-secret"></a>Criar um segredo do cliente   
O aplicativo precisa de um segredo do cliente para provar sua identidade ao solicitar um token. Para adicionar o segredo do cliente, siga estas etapas.

1. Navegue até o registro do aplicativo no portal do Azure se você ainda não estiver na página.
1. Selecione **certificados & segredos** no menu à esquerda.
1. Em **segredos do cliente**, selecione **novo segredo do cliente** para criar um novo segredo.

    ![Novo botão de segredo do cliente](./media/authenticate-application/new-client-secret-button.png)
1. Forneça uma descrição para o segredo e escolha o intervalo de expiração desejado e, em seguida, selecione **Adicionar**.

    ![Adicionar página de segredo do cliente](./media/authenticate-application/add-client-secret-page.png)
1. Copie imediatamente o valor do novo segredo para um local seguro. O valor de preenchimento é exibido apenas uma vez.

    ![Segredo do cliente](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Permissões para a API do barramento de serviço
Se seu aplicativo for um aplicativo de console, você deverá registrar um aplicativo nativo e adicionar permissões de API para o **Microsoft. ServiceBus** ao conjunto de **permissões necessárias** . Aplicativos nativos também precisam de um **redirecionador-URI** no Azure AD, que serve como um identificador; o URI não precisa ser um destino de rede. Use `https://servicebus.microsoft.com` para este exemplo, porque o exemplo de código já usa esse URI.

### <a name="client-libraries-for-token-acquisition"></a>Bibliotecas de cliente para aquisição de token  
Depois de registrar seu aplicativo e conceder permissões de ti para enviar/receber dados no barramento de serviço do Azure, você pode adicionar código ao seu aplicativo para autenticar uma entidade de segurança e adquirir o token 2,0 do OAuth. Para autenticar e adquirir o token, você pode usar uma das [bibliotecas de autenticação da plataforma de identidade da Microsoft](../active-directory/develop/reference-v2-libraries.md) ou outra biblioteca de software livre que dê suporte a OpenID ou Connect 1,0. Seu aplicativo pode, então, usar o token de acesso para autorizar uma solicitação no barramento de serviço do Azure.

Para obter uma lista de cenários para os quais há suporte para tokens de aquisição, consulte a seção [cenários](https://aka.ms/msal-net-scenarios) do repositório do GITHUB do [MSAL (biblioteca de autenticação da Microsoft) para .net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) .

## <a name="sample-on-github"></a>Exemplo no GitHub
Consulte o exemplo a seguir no GitHub: [controle de acesso baseado em função do Azure para o barramento de serviço](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl). 

Use a opção de **logon de segredo do cliente** , não a opção de logon de **usuário interativo** . Ao usar a opção de segredo do cliente, você não verá uma janela pop-up. O aplicativo utiliza a ID do locatário e a ID do aplicativo para autenticação. 

### <a name="run-the-sample"></a>Execute o exemplo

Antes de executar o exemplo, edite o arquivo **app.config** e, dependendo do seu cenário, defina os seguintes valores:

- `tenantId`: defina com o valor **TenantId**.
- `clientId`: defina com o valor **ApplicationId**.
- `clientSecret`: se você quiser entrar usando o segredo do cliente, crie-o no Azure AD. Além disso, use um aplicativo Web ou uma API em vez de um aplicativo nativo. E também adicione o aplicativo no **Controle de Acesso (IAM)** no namespace que você criou anteriormente.
- `serviceBusNamespaceFQDN`: defina o nome DNS completo do namespace do Barramento de Serviço recém-criado, por exemplo, `example.servicebus.windows.net`.
- `queueName`: defina como o nome da fila criada.
- O URI de redirecionamento que você especificou no aplicativo nas etapas anteriores.

Ao executar o aplicativo de console, você será solicitado a selecionar um cenário. Selecione **logon de usuário interativo** digitando seu número e pressionando ENTER. O aplicativo exibe uma janela de logon, solicita seu consentimento para acessar o Barramento de Serviço e, em seguida, usa o serviço para executar o cenário de envio/recebimento usando a identidade de logon.


## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre o RBAC do Azure, confira [o que é o Azure RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md)?
- Para saber como atribuir e gerenciar atribuições de função do Azure com Azure PowerShell, CLI do Azure ou a API REST, consulte estes artigos:
    - [Adicionar ou remover atribuições de função do Azure usando o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Adicionar ou remover atribuições de função do Azure usando a CLI do Azure](../role-based-access-control/role-assignments-cli.md)
    - [Adicionar ou remover atribuições de função do Azure usando a API REST](../role-based-access-control/role-assignments-rest.md)
    - [Adicionar ou remover atribuições de função do Azure usando modelos de Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Para saber mais sobre as mensagens do Barramento de Serviço, confira os tópicos a seguir.

- [Exemplos de RBAC do Azure do barramento de serviço](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
- [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
- [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)