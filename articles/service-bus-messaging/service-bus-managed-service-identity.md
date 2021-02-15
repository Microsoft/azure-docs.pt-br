---
title: Identidades gerenciadas para recursos do Azure com Barramento de Serviço
description: Este artigo descreve como usar identidades gerenciadas para acessar as entidades do barramento de serviço do Azure (filas, tópicos e assinaturas).
ms.topic: article
ms.date: 01/21/2021
ms.openlocfilehash: bd985acd9b775d6baef0abf488952e28c17aef2a
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954303"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Autenticar uma identidade gerenciada com Azure Active Directory para acessar recursos do barramento de serviço do Azure
[Identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) é um recurso do Azure que permite criar uma identidade segura associada à implantação na qual o código do aplicativo é executado. Em seguida, você pode associar essa identidade a funções de controle de acesso que concedem permissões personalizadas para acessar os recursos do Azure específicos que seu aplicativo precisa.

Com identidades gerenciadas, a plataforma do Azure gerencia essa identidade de runtime. Você não precisa armazenar e proteger as chaves de acesso no código ou na configuração do aplicativo, seja para a identidade ou para os recursos que você precisa acessar. Um aplicativo cliente do Barramento de Serviço em execução dentro de um aplicativo de Serviço de Aplicativo do Azure ou em uma máquina virtual com entidades gerenciadas habilitadas para suporte de recursos do Azure não precisa lidar com regras e chaves de SAS nem com nenhum outro token de acesso. O aplicativo cliente precisa apenas do endereço do ponto de extremidade do namespace das Mensagens do Barramento de Serviço. Quando o aplicativo se conecta, o Barramento de Serviço associa o contexto de entidade gerenciada ao cliente em uma operação que será mostrada em um exemplo mais adiante neste artigo. Depois que está associado com uma identidade gerenciada, o cliente do Barramento de Serviço pode realizar todas as operações autorizadas. A autorização é concedida ao associar uma entidade gerenciada com as funções do Barramento de Serviço. 

## <a name="overview"></a>Visão geral
Quando uma entidade de segurança (um usuário, grupo ou aplicativo) tenta acessar uma entidade do barramento de serviço, a solicitação deve ser autorizada. Com o Azure AD, o acesso a um recurso é um processo de duas etapas. 

 1. Primeiro, a identidade da entidade de segurança é autenticada e um token OAuth 2,0 é retornado. O nome do recurso para solicitar um token é `https://servicebus.azure.net` .
 1. Em seguida, o token é passado como parte de uma solicitação para o serviço do barramento de serviço para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que uma solicitação do aplicativo contenha um token de acesso OAuth 2.0 no runtime. Se um aplicativo estiver em execução em uma entidade do Azure, como uma VM do Azure, um conjunto de dimensionamento de máquinas virtuais ou um aplicativo de funções do Azure, ele poderá usar uma identidade gerenciada para acessar os recursos. 

A etapa de autorização requer que uma ou mais funções do Azure sejam atribuídas à entidade de segurança. O barramento de serviço do Azure fornece funções do Azure que abrangem conjuntos de permissões para recursos do barramento de serviço. As funções atribuídas a uma entidade de segurança determinam as permissões que o principal terá. Para saber mais sobre como atribuir funções do Azure ao barramento de serviço do Azure, confira [funções internas do Azure para o barramento de serviço do Azure](#azure-built-in-roles-for-azure-service-bus). 

Aplicativos nativos e aplicativos Web que fazem solicitações para o barramento de serviço também podem autorizar com o Azure AD. Este artigo mostra como solicitar um token de acesso e usá-lo para autorizar solicitações para recursos do barramento de serviço. 


## <a name="assigning-azure-roles-for-access-rights"></a>Atribuindo funções do Azure para direitos de acesso
O Azure Active Directory (AD do Azure) autoriza os direitos de acesso a recursos protegidos por meio do [controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md). O barramento de serviço do Azure define um conjunto de funções internas do Azure que abrangem conjuntos comuns de permissões usadas para acessar entidades do barramento de serviço e você também pode definir funções personalizadas para acessar os dados.

Quando uma função do Azure é atribuída a uma entidade de segurança do Azure AD, o Azure concede acesso a esses recursos para essa entidade de segurança. O acesso pode ser definido para o nível de assinatura, o grupo de recursos ou o namespace do barramento de serviço. Uma entidade de segurança do Azure AD pode ser um usuário, um grupo, uma entidade de serviço de aplicativo ou uma identidade gerenciada para recursos do Azure.

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Funções internas do Azure para o barramento de serviço do Azure
Para o barramento de serviço do Azure, o gerenciamento de namespaces e todos os recursos relacionados por meio do portal do Azure e a API de gerenciamento de recursos do Azure já estão protegidos usando o modelo RBAC do Azure. O Azure fornece as funções internas do Azure abaixo para autorizar o acesso a um namespace do barramento de serviço:

- [Proprietário de dados do barramento de serviço do Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): habilita o acesso a dados ao namespace do barramento de serviço e suas entidades (filas, tópicos, assinaturas e filtros)
- [Remetente de dados do barramento de serviço do Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Use essa função para fornecer acesso de envio ao namespace do barramento de serviço e suas entidades.
- [Receptor de dados do barramento de serviço do Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Use essa função para conceder acesso de recebimento ao namespace do barramento de serviço e suas entidades. 

## <a name="resource-scope"></a>Escopo do recurso 
Antes de atribuir uma função do Azure a uma entidade de segurança, determine o escopo do acesso que essa entidade de segurança deve ter. De acordo com as melhores práticas, sempre é melhor conceder o escopo mais estreito possível.

A lista a seguir descreve os níveis nos quais você pode fazer o escopo de acesso aos recursos do barramento de serviço, começando com o escopo mais estreito:

- **Fila**, **tópico** ou **assinatura**: a atribuição de função se aplica à entidade de barramento de serviço específica. Atualmente, o portal do Azure não dá suporte à atribuição de usuários/grupos/identidades gerenciadas às funções do Azure do barramento de serviço no nível da assinatura. Veja um exemplo de como usar o comando CLI do Azure: [AZ-role-Assignment-Create](/cli/azure/role/assignment?#az-role-assignment-create) para atribuir uma identidade a uma função do Azure do barramento de serviço: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Namespace do barramento de serviço**: a atribuição de função abrange toda a topologia do barramento de serviço no namespace e no grupo de consumidores associado a ela.
- **Grupo de recursos**: a atribuição de função se aplica a todos os recursos do barramento de serviço no grupo de recursos.
- **Assinatura**: a atribuição de função se aplica a todos os recursos do barramento de serviço em todos os grupos de recursos na assinatura.

> [!NOTE]
> Tenha em mente que as atribuições de função do Azure podem levar até cinco minutos para serem propagadas. 

Para obter mais informações sobre como as funções internas são definidas, consulte [entender as definições de função](../role-based-access-control/role-definitions.md#management-and-data-operations). Para obter informações sobre como criar funções personalizadas do Azure, consulte [funções personalizadas do Azure](../role-based-access-control/custom-roles.md).

## <a name="enable-managed-identities-on-a-vm"></a>Habilitar identidades gerenciadas em uma VM
Antes de usar identidades gerenciadas para recursos do Azure para autorizar recursos de barramento de serviço de sua VM, você deve primeiro habilitar identidades gerenciadas para recursos do Azure na VM. Para saber como habilitar identidades gerenciadas para Recursos do Azure, confira um dos seguintes artigos:

- [Azure portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [PowerShell do Azure](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo do Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de cliente Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Conceder permissões a uma identidade gerenciada no Azure AD
Para autorizar uma solicitação ao serviço de barramento de serviço de uma identidade gerenciada em seu aplicativo, primeiro configure as configurações do Azure RBAC (controle de acesso baseado em função) para essa identidade gerenciada. O barramento de serviço do Azure define funções do Azure que abrangem permissões para enviar e ler do barramento de serviço. Quando a função do Azure é atribuída a uma identidade gerenciada, a identidade gerenciada recebe acesso às entidades do barramento de serviço no escopo apropriado.

Para obter mais informações sobre como atribuir funções do Azure, consulte [autenticar e autorizar com Azure Active Directory para acessar os recursos do barramento de serviço](authenticate-application.md#azure-built-in-roles-for-azure-service-bus).

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Usar Barramento de Serviço com identidades gerenciadas para recursos do Azure
Para usar o barramento de serviço com identidades gerenciadas, você precisa atribuir a identidade a função e o escopo apropriado. O procedimento nesta seção usa um aplicativo simples que é executado em uma identidade gerenciada e acessa os recursos do barramento de serviço.

Aqui, estamos usando um aplicativo Web de exemplo hospedado no [serviço Azure app](https://azure.microsoft.com/services/app-service/). Para obter as instruções passo a passo para criar um aplicativo Web, consulte [criar um ASP.NET Core aplicativo Web no Azure](../app-service/quickstart-dotnetcore.md)

Depois que o aplicativo for criado, siga estas etapas: 

1. Vá para **configurações** e selecione **identidade**. 
1. Selecione o **status** a ser **ativado**. 
1. Clique em **Salvar** para salvar a configuração. 

    ![Identidade gerenciada para um aplicativo Web](./media/service-bus-managed-service-identity/identity-web-app.png)

Depois de habilitar essa configuração, uma nova identidade de serviço será criada em seu Azure Active Directory (AD do Azure) e configurada no host do serviço de aplicativo.

> [!NOTE]
> Quando você usa uma identidade gerenciada, a cadeia de conexão deve estar no formato: `Endpoint=sb://<NAMESPACE NAME>.servicebus.windows.net/;Authentication=Managed Identity` .

Agora, atribua essa identidade de serviço a uma função no escopo necessário nos recursos do barramento de serviço.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Para atribuir funções do Azure usando o portal do Azure
Para atribuir uma função a um namespace do barramento de serviço, navegue até o namespace na portal do Azure. Exiba as configurações de controle de acesso (IAM) para o recurso e siga estas instruções para gerenciar as atribuições de função:

> [!NOTE]
> As etapas a seguir atribui uma função de identidade de serviço aos namespaces do barramento de serviço. Você pode seguir as mesmas etapas para atribuir uma função em outros escopos com suporte (grupo de recursos e assinatura). 
> 
> [Crie um namespace de mensagens do barramento de serviço](service-bus-create-namespace-portal.md) se você não tiver um. 

1. Na portal do Azure, navegue até o namespace do barramento de serviço e exiba a **visão geral** do namespace. 
1. Selecione **controle de acesso (iam)** no menu à esquerda para exibir as configurações de controle de acesso para o namespace do barramento de serviço.
1.  Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.
3.  Selecione **Adicionar** e, em seguida, selecione **Adicionar atribuição de função**.
4.  Na página **Adicionar atribuição de função** , siga estas etapas:
    1. Para **função**, selecione a função de barramento de serviço que você deseja atribuir. Neste exemplo, é o **proprietário dos dados do barramento de serviço do Azure**.
    1. Para o campo **atribuir acesso a** , selecione **serviço de aplicativo** em **identidade gerenciada atribuída pelo sistema**. 
    1. Selecione a **assinatura** na qual a identidade gerenciada para o aplicativo Web foi criada.
    1. Selecione a **identidade gerenciada** para o aplicativo Web que você criou. O nome padrão da identidade é o mesmo que o nome do aplicativo Web. 
    1. Em seguida, selecione **Salvar**.
        
        ![Página Adicionar atribuição de função](./media/service-bus-managed-service-identity/add-role-assignment-page.png)

    Depois de atribuir a função, o aplicativo Web terá acesso às entidades do barramento de serviço sob o escopo definido. 

    > [!NOTE]
    > Para obter uma lista de serviços que dão suporte a identidades gerenciadas, consulte [serviços que dão suporte a identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

### <a name="run-the-app"></a>Execute o aplicativo
Agora, modifique a página padrão do aplicativo ASP.NET que você criou. Também é possível usar o código do aplicativo Web [deste repositório GitHub](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

A página Default.aspx é sua página de aterrissagem. O código pode ser encontrado no arquivo Default.aspx.cs. O resultado é um aplicativo Web mínimo com alguns campos de entrada e os botões **enviar** e **receber** que se conectam ao Barramento de Serviço para enviar ou receber mensagens.

Observe como o objeto [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) é inicializado. Em vez de usar o provedor de token SAS (Token de Acesso Compartilhado), o código cria um provedor de token para a identidade gerenciada com a chamada `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();`. Dessa forma, não há nenhum segredo a ser retido e uso. O fluxo do contexto de identidade gerenciada para o Barramento de Serviço e o handshake de autorização são manipulados automaticamente pelo provedor de token. É um modelo mais simples que usar SAS.

Depois de fazer essas alterações, publique e execute o aplicativo. Você pode facilmente obter os dados de publicação corretos baixando e, em seguida, importando um perfil de publicação no Visual Studio:

![Obter perfil de publicação](./media/service-bus-managed-service-identity/msi3.png)
 
Para enviar ou receber mensagens, insira o nome do namespace e o nome da entidade que você criou. Em seguida, clique em **envie** ou **receber**.


> [!NOTE]
> - A identidade gerenciada funciona somente dentro do ambiente do Azure, em serviços de aplicativos, VMs do Azure e nos conjuntos de dimensionamento. Para aplicativos .NET, a biblioteca Microsoft.Azure.Services.AppAuthentication, que é usada pelo pacote NuGet do Barramento de Serviço, fornece uma abstração sobre esse protocolo e dá suporte a uma experiência de desenvolvimento local. Essa biblioteca também permite testar o código localmente no computador de desenvolvimento, usando a conta de usuário do Visual Studio, a CLI 2.0 do Azure ou a Autenticação Integrada do Active Directory. Para obter mais informações sobre as opções de desenvolvimento local com essa biblioteca, consulte [Autenticação de serviço a serviço para o Azure Key Vault usando .NET](/dotnet/api/overview/azure/service-to-service-authentication).  

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes tópicos:

* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)
