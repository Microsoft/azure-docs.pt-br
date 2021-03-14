---
title: Autenticar um aplicativo para acessar o serviço de Signaler do Azure
description: Este artigo fornece informações sobre como autenticar um aplicativo com Azure Active Directory para acessar o serviço de Signaler do Azure
author: terencefan
ms.author: tefa
ms.service: signalr
ms.topic: conceptual
ms.date: 08/03/2020
ms.openlocfilehash: 597b69c1180ea1fb2a6812d648f8b8ad37707d66
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092603"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-azure-signalr-service"></a>Autenticar um aplicativo com Azure Active Directory para acessar o serviço de Signaler do Azure
O Microsoft Azure fornece gerenciamento de controle de acesso integrado para recursos e aplicativos com base no Azure Active Directory (Azure AD). Uma vantagem importante de usar o Azure AD com o serviço de Signalr do Azure é que você não precisa mais armazenar suas credenciais no código. Em vez disso, você pode solicitar um token de acesso OAuth 2,0 da plataforma de identidade da Microsoft. O nome do recurso para solicitar um token é `https://signalr.azure.com/` . O Azure AD autentica a entidade de segurança (um aplicativo, grupo de recursos ou entidade de serviço) que executa o aplicativo. Se a autenticação for bem sucedido, o Azure AD retornará um token de acesso para o aplicativo e o aplicativo poderá usar o token de acesso para autorizar a solicitação para os recursos do serviço Signaler do Azure.

Quando uma função é atribuída a uma entidade de segurança do Azure AD, o Azure concede acesso a esses recursos para essa entidade de segurança. O acesso pode ser definido para o nível de assinatura, o grupo de recursos ou o recurso de Signalr do Azure. Uma segurança do Azure AD pode atribuir funções a um usuário, a um grupo, a uma entidade de serviço de aplicativo ou a uma [identidade gerenciada para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md). 

> [!NOTE]
> Uma definição de função é uma coleção de permissões. O RBAC (controle de acesso baseado em função) controla como essas permissões são impostas por meio da atribuição de função. Uma atribuição de função consiste em três elementos: entidade de segurança, definição de função e escopo. Para obter mais informações, consulte [noções básicas sobre as diferentes funções](../role-based-access-control/overview.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registre o aplicativo com um locatário do Azure AD
A primeira etapa no uso do Azure AD para autorizar os recursos do serviço de Signaler do Azure é registrar seu aplicativo com um locatário do Azure AD da [portal do Azure](https://portal.azure.com/). Ao registrar seu aplicativo, você fornece informações sobre o aplicativo para o AD. Em seguida, o Azure AD fornece uma ID do cliente (também chamada de ID do aplicativo) que você pode usar para associar seu aplicativo ao tempo de execução do Azure AD. Para saber mais sobre a ID do cliente, consulte [Objetos de entidade de serviço e aplicativo no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

As imagens a seguir mostram as etapas para registrar um aplicativo Web:

![Registrar um aplicativo](./media/authenticate/app-registrations-register.png)

> [!Note]
> Se você registrar seu aplicativo como um aplicativo nativo, poderá especificar qualquer URI válido para o URI de redirecionamento. Para aplicativos nativos, esse valor não precisa ser uma URL real. Para aplicativos Web, o URI de redirecionamento deve ser um URI válido, pois ele especifica a URL para a qual os tokens são fornecidos.

Depois de registrar seu aplicativo, você verá a ID do **aplicativo (cliente)** em **configurações**:

![ID do aplicativo registrado](./media/authenticate/application-id.png)

Para obter mais informações sobre como registrar um aplicativo no Azure AD, consulte [Integrando aplicativos com o Azure Active Directory](../active-directory/develop/quickstart-register-app.md).


### <a name="create-a-client-secret"></a>Criar um segredo do cliente   
O aplicativo precisa de um segredo do cliente para provar sua identidade ao solicitar um token. Para adicionar o segredo do cliente, siga estas etapas.

1. Navegue até o registro do aplicativo no portal do Azure.
1. Selecione a configuração **certificados & segredos** .
1. Em **segredos do cliente**, selecione **novo segredo do cliente** para criar um novo segredo.
1. Forneça uma descrição para o segredo e escolha o intervalo de expiração desejado.
1. Copie imediatamente o valor do novo segredo para um local seguro. O valor de preenchimento é exibido apenas uma vez.

![Criar um segredo do cliente](./media/authenticate/client-secret.png)

### <a name="upload-a-certificate"></a>Carregar um certificado

Você também pode carregar uma certificação em vez de criar um segredo do cliente.

![Carregar uma certificação](./media/authenticate/certification.png)

## <a name="assign-azure-roles-using-the-azure-portal"></a>Atribuir funções do Azure usando o portal do Azure  
Para saber mais sobre como gerenciar o acesso aos recursos do Azure usando o RBAC do Azure e o portal do Azure, consulte [Este artigo](..//role-based-access-control/role-assignments-portal.md). 

Depois de determinar o escopo apropriado para uma atribuição de função, navegue até esse recurso na portal do Azure. Exiba as configurações de controle de acesso (IAM) para o recurso e siga estas instruções para gerenciar as atribuições de função:

1. Na [portal do Azure](https://portal.azure.com/), navegue até o recurso de sinalização.
1. Selecione **controle de acesso (iam)** para exibir as configurações de controle de acesso para o Signalr do Azure. 
1. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais. Selecione o botão **Adicionar** na barra de ferramentas e, em seguida, selecione **Adicionar atribuição de função**. 

    ![Botão Adicionar na barra de ferramentas](./media/authenticate/role-assignments-add-button.png)

1. Na página **Adicionar atribuição de função** , execute as seguintes etapas:
    1. Selecione a **função de signalr do Azure** que você deseja atribuir. 
    1. Pesquise para localizar a **entidade de segurança** (usuário, grupo, entidade de serviço) à qual você deseja atribuir a função.
    1. Selecione **salvar** para salvar a atribuição de função. 

        ![Atribuir função a um aplicativo](./media/authenticate/assign-role-to-application.png)

    1. A identidade à qual você atribuiu a função aparece listada sob essa função. Por exemplo, a imagem a seguir mostra esse aplicativo `signalr-dev` e `signalr-service` está na função de servidor de aplicativo signalr. 
        
        ![Lista de atribuição de função](./media/authenticate/role-assignment-list.png)

Você pode seguir etapas semelhantes para atribuir uma função com escopo ao grupo de recursos ou assinatura. Depois de definir a função e seu escopo, você pode testar esse comportamento com exemplos [neste local do GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac).

## <a name="sample-codes-while-configuring-your-app-server"></a>Códigos de exemplo ao configurar seu servidor de aplicativos.

Adicione as seguintes opções quando `AddAzureSignalR` :

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>";
});
```

Ou simplesmente configure o `ConnectionString` em seu `appsettings.json` arquivo.

```json
{
"Azure": {
    "SignalR": {
      "Enabled": true,
      "ConnectionString": "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>"
    }
  },
}
```

Ao usar `certificate` , altere o `clientSecret` para o `clientCert` seguinte:

```C#
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientCert=<clientCertFilepath>;tenantId=<tenantId>";
```

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre o RBAC, confira [o que é o Azure RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md)?
- Para saber como atribuir e gerenciar atribuições de função do Azure com Azure PowerShell, CLI do Azure ou a API REST, consulte estes artigos:
    - [Gerenciar o controle de acesso baseado em função (RBAC) com o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Gerenciar o controle de acesso baseado em função (RBAC) com a CLI do Azure](../role-based-access-control/role-assignments-cli.md)
    - [Gerenciar o controle de acesso baseado em função (RBAC) com a API REST](../role-based-access-control/role-assignments-rest.md)
    - [Gerenciar RBAC (controle de acesso baseado em função) com modelos de Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Consulte os seguintes artigos relacionados:
- [Autenticar uma identidade gerenciada com Azure Active Directory para acessar o serviço de Signaler do Azure](authenticate-managed-identity.md)
- [Autorizar o acesso ao serviço de Signaler do Azure usando o Azure Active Directory](authorize-access-azure-active-directory.md)