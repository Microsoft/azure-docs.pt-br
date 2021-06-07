---
title: Autenticar um aplicativo para acessar os recursos dos hubs de eventos do Azure
description: Este artigo fornece informações sobre como autenticar um aplicativo com Azure Active Directory para acessar os recursos dos hubs de eventos do Azure
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 25ec5f11ca7b5e801e18155f1a3da6474c8e66e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92913306"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Autenticar um aplicativo com Azure Active Directory para acessar recursos de hubs de eventos
O Microsoft Azure fornece gerenciamento de controle de acesso integrado para recursos e aplicativos com base no Azure Active Directory (Azure AD). Uma vantagem importante de usar o Azure AD com os hubs de eventos do Azure é que você não precisa mais armazenar suas credenciais no código. Em vez disso, você pode solicitar um token de acesso OAuth 2,0 da plataforma de identidade da Microsoft. O nome do recurso para solicitar um token é `https://eventhubs.azure.net/` , e é o mesmo para todas as nuvens/locatários (para clientes Kafka, o recurso para solicitar um token é `https://<namespace>.servicebus.windows.net` ). O Azure AD autentica a entidade de segurança (um usuário, grupo ou entidade de serviço) que executa o aplicativo. Se a autenticação for bem sucedido, o Azure AD retornará um token de acesso para o aplicativo e o aplicativo poderá usar o token de acesso para autorizar a solicitação aos recursos dos hubs de eventos do Azure.

Quando uma função é atribuída a uma entidade de segurança do Azure AD, o Azure concede acesso a esses recursos para essa entidade de segurança. O acesso pode ser definido para o nível de assinatura, o grupo de recursos, o namespace de hubs de eventos ou qualquer recurso sob ele. Uma segurança do Azure AD pode atribuir funções a um usuário, a um grupo, a uma entidade de serviço de aplicativo ou a uma [identidade gerenciada para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md). 

> [!NOTE]
> Uma definição de função é uma coleção de permissões. O Azure RBAC (controle de acesso baseado em função) do Azure controla como essas permissões são impostas por meio da atribuição de função. Uma atribuição de função consiste em três elementos: entidade de segurança, definição de função e escopo. Para obter mais informações, consulte [noções básicas sobre as diferentes funções](../role-based-access-control/overview.md).

## <a name="built-in-roles-for-azure-event-hubs"></a>Funções internas para hubs de eventos do Azure
O Azure fornece as seguintes funções internas do Azure para autorizar o acesso aos dados de hubs de eventos usando o Azure AD e o OAuth:

- [Proprietário de dados dos hubs de eventos do Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Use essa função para fornecer acesso completo aos recursos dos hubs de eventos.
- [Remetente de dados dos hubs de eventos do Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): Use essa função para fornecer acesso de envio aos recursos dos hubs de eventos.
- [Receptor de dados dos hubs de eventos do Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): Use essa função para dar acesso de recebimento aos recursos dos hubs de eventos.   

Para funções internas do registro de esquema, consulte [funções de registro de esquema](schema-registry-overview.md#azure-role-based-access-control).

> [!IMPORTANT]
> Nossa versão de visualização suportava a adição de privilégios de acesso a dados dos hubs de eventos à função de proprietário ou colaborador. No entanto, os privilégios de acesso a dados para a função de proprietário e colaborador não são mais respeitados. Se você estiver usando a função de proprietário ou colaborador, mude para usando a função de proprietário de dados dos hubs de eventos do Azure.


## <a name="authenticate-from-an-application"></a>Autenticar a partir de um aplicativo
Uma vantagem importante de usar o Azure AD com os hubs de eventos é que suas credenciais não precisam mais ser armazenadas em seu código. Em vez disso, você pode solicitar um token de acesso OAuth 2,0 da plataforma de identidade da Microsoft. O Azure AD autentica a entidade de segurança (um usuário, um grupo ou uma entidade de serviço) que executa o aplicativo. Se a autenticação for bem sucedido, o Azure AD retornará o token de acesso para o aplicativo e o aplicativo poderá usar o token de acesso para autorizar solicitações para os hubs de eventos do Azure.

As seções a seguir mostram como configurar seu aplicativo nativo ou aplicativo Web para autenticação com a plataforma de identidade da Microsoft 2,0. Para obter mais informações sobre a plataforma de identidade da Microsoft 2,0, consulte [visão geral da plataforma Microsoft Identity (v 2.0)](../active-directory/develop/v2-overview.md).

Para obter uma visão geral do fluxo de concessão do código do OAuth 2.0, consulte [Autorizar acesso a aplicativos Web do Azure Active Directory usando o fluxo de concessão do código do OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registre o aplicativo com um locatário do Azure AD
A primeira etapa no uso do Azure AD para autorizar recursos de hubs de eventos é registrar seu aplicativo cliente com um locatário do Azure AD do [portal do Azure](https://portal.azure.com/). Ao registrar seu aplicativo cliente, você fornece informações sobre o aplicativo para o AD. Em seguida, o Azure AD fornece uma ID do cliente (também chamada de ID do aplicativo) que você pode usar para associar seu aplicativo ao tempo de execução do Azure AD. Para saber mais sobre a ID do cliente, consulte [Objetos de entidade de serviço e aplicativo no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

As imagens a seguir mostram as etapas para registrar um aplicativo Web:

![Registrar um aplicativo](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Se você registrar seu aplicativo como um aplicativo nativo, poderá especificar qualquer URI válido para o URI de redirecionamento. Para aplicativos nativos, esse valor não precisa ser uma URL real. Para aplicativos Web, o URI de redirecionamento deve ser um URI válido, pois ele especifica a URL para a qual os tokens são fornecidos.

Depois de registrar seu aplicativo, você verá a ID do **aplicativo (cliente)** em **configurações**:

![ID do aplicativo registrado](./media/authenticate-application/application-id.png)

Para obter mais informações sobre como registrar um aplicativo no Azure AD, consulte [Integrando aplicativos com o Azure Active Directory](../active-directory/develop/quickstart-register-app.md).


### <a name="create-a-client-secret"></a>Criar um segredo do cliente   
O aplicativo precisa de um segredo do cliente para provar sua identidade ao solicitar um token. Para adicionar o segredo do cliente, siga estas etapas.

1. Navegue até o registro do aplicativo no portal do Azure.
1. Selecione a configuração **certificados & segredos** .
1. Em **segredos do cliente**, selecione **novo segredo do cliente** para criar um novo segredo.
1. Forneça uma descrição para o segredo e escolha o intervalo de expiração desejado.
1. Copie imediatamente o valor do novo segredo para um local seguro. O valor de preenchimento é exibido apenas uma vez.

    ![Segredo do cliente](./media/authenticate-application/client-secret.png)


## <a name="assign-azure-roles-using-the-azure-portal"></a>Atribuir funções do Azure usando o portal do Azure  
Depois de registrar o aplicativo, atribua a entidade de serviço do aplicativo a uma função do Azure AD dos hubs de eventos descrita na seção [funções de compilação para hubs de eventos do Azure](#built-in-roles-for-azure-event-hubs) . 

1. No [Portal do Azure](https://portal.azure.com/), navegue até o seu namespace de Hubs de Eventos.
2. Na página **visão geral** , selecione o Hub de eventos para o qual você deseja atribuir uma função.

    ![Selecione seu hub de eventos](./media/authenticate-application/select-event-hub.png)
1. Selecione **controle de acesso (iam)** para exibir as configurações de controle de acesso para o Hub de eventos. 
1. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais. Selecione o botão **Adicionar** na barra de ferramentas e, em seguida, selecione **Adicionar atribuição de função**. 

    ![Botão Adicionar na barra de ferramentas](./media/authenticate-application/role-assignments-add-button.png)
1. Na página **Adicionar atribuição de função** , execute as seguintes etapas:
    1. Selecione a **função de hubs de eventos** que você deseja atribuir. 
    1. Pesquise para localizar a **entidade de segurança** (usuário, grupo, entidade de serviço) à qual você deseja atribuir a função. Selecione o **aplicativo registrado** na lista. 
    1. Selecione **salvar** para salvar a atribuição de função. 

        ![Atribuir função a um usuário](./media/authenticate-application/assign-role-to-user.png)
    4. Alterne para a guia **atribuições de função** e confirme a atribuição de função. Por exemplo, a imagem a seguir mostra que **myWebApp** está na função de **remetente de dados dos hubs de eventos do Azure** . 
        
        ![Usuário na lista](./media/authenticate-application/user-in-list.png)

Você pode seguir etapas semelhantes para atribuir um escopo de função ao namespace de hubs de eventos, ao grupo de recursos ou à assinatura. Depois de definir a função e seu escopo, você pode testar esse comportamento com exemplos [neste local do GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). Para saber mais sobre como gerenciar o acesso aos recursos do Azure usando o RBAC do Azure e o portal do Azure, consulte [Este artigo](..//role-based-access-control/role-assignments-portal.md). 


### <a name="client-libraries-for-token-acquisition"></a>Bibliotecas de cliente para aquisição de token  
Depois de registrar seu aplicativo e conceder permissões de ti para enviar/receber dados nos hubs de eventos do Azure, você pode adicionar código ao seu aplicativo para autenticar uma entidade de segurança e adquirir o token 2,0 do OAuth. Para autenticar e adquirir o token, você pode usar uma das [bibliotecas de autenticação da plataforma de identidade da Microsoft](../active-directory/develop/reference-v2-libraries.md) ou outra biblioteca de software livre que dê suporte a OpenID ou Connect 1,0. Seu aplicativo pode, então, usar o token de acesso para autorizar uma solicitação nos hubs de eventos do Azure.

Para obter uma lista de cenários para os quais há suporte para tokens de aquisição, consulte a seção [cenários](https://aka.ms/msal-net-scenarios) do repositório do GITHUB do [MSAL (biblioteca de autenticação da Microsoft) para .net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) .

## <a name="samples"></a>Exemplos
- [Exemplos de Microsoft. Azure. EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Esses exemplos usam a biblioteca antiga **Microsoft.Azure.EventHubs**, mas você pode atualizá-los com facilidade para que usem a biblioteca **Azure.Messaging.EventHubs** mais recente. Para migrar o exemplo da biblioteca antiga para a nova, confira o [Guia de migração de Microsoft.Azure.EventHubs para Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Exemplos do Azure. Messaging. EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Este exemplo foi atualizado para usar a biblioteca **Azure. Messaging. EventHubs** mais recente.

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre o RBAC do Azure, confira [o que é o Azure RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md)?
- Para saber como atribuir e gerenciar atribuições de função do Azure com Azure PowerShell, CLI do Azure ou a API REST, consulte estes artigos:
    - [Adicionar ou remover atribuições de função do Azure usando o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Adicionar ou remover atribuições de função do Azure usando a CLI do Azure](../role-based-access-control/role-assignments-cli.md)
    - [Adicionar ou remover atribuições de função do Azure usando a API REST](../role-based-access-control/role-assignments-rest.md)
    - [Adicionar atribuições de função do Azure usando modelos do Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Consulte os seguintes artigos relacionados:
- [Autenticar uma identidade gerenciada com Azure Active Directory para acessar recursos de hubs de eventos](authenticate-managed-identity.md)
- [Autenticar solicitações para hubs de eventos do Azure usando assinaturas de acesso compartilhado](authenticate-shared-access-signature.md)
- [Autorizar o acesso aos recursos de hubs de eventos usando Azure Active Directory](authorize-access-azure-active-directory.md)
- [Autorizar o acesso a recursos de hubs de eventos usando assinaturas de acesso compartilhado](authorize-access-shared-access-signature.md)
