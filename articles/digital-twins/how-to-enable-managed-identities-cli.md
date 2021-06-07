---
title: Habilitar uma identidade gerenciada nos eventos de roteamento (versão prévia) – CLI
titleSuffix: Azure Digital Twins
description: Veja como habilitar uma identidade atribuída pelo sistema para os Gêmeos Digitais do Azure e usá-la para encaminhar eventos com a CLI do Azure.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e9eb5950b5cf7d4e7d0270deed72866ee28e3962
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106897"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-cli"></a>Habilitar uma identidade gerenciada para rotear eventos dos Gêmeos Digitais do Azure (versão prévia): CLI do Azure

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

Este artigo descreve como habilitar uma [identidade atribuída pelo sistema para uma instância dos Gêmeos Digitais do Azure](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (no momento em versão prévia) e usar a identidade ao encaminhar eventos para destinos com suporte, como [Hub de Eventos](../event-hubs/event-hubs-about.md), destinos do [Barramento de Serviço](../service-bus-messaging/service-bus-messaging-overview.md)  e [Contêiner de Armazenamento do Azure](../storage/blobs/storage-blobs-introduction.md).

Esse artigo explica o processo usando a [**CLI do Azure**](/cli/azure/what-is-azure-cli).

Aqui estão as etapas abordadas em detalhes nesse artigo: 

1. Crie uma instância dos Gêmeos Digitais do Azure com uma identidade atribuída pelo sistema ou habilite a identidade atribuída pelo sistema em uma instância existente do Gêmeos Digitais do Azure. 
1. Adicione uma função ou funções apropriadas à identidade. Por exemplo, atribua a função de **Remetente de Dados do Hub de Eventos do Azure** à identidade se o ponto de extremidade for Hub de Eventos ou a **função de Remetente de Dados do Barramento de Serviço do Azure** se o ponto de extremidade for Barramento de Serviço.
1. Crie um ponto de extremidade nos Gêmeos Digitais do Azure que possa usar identidades atribuídas pelo sistema para autenticação.

## <a name="enable-system-managed-identities-for-an-instance"></a>Habilitar identidades gerenciadas pelo sistema para uma instância 

Quando você habilita uma identidade atribuída pelo sistema em sua instância dos Gêmeos Digitais do Azure, o Azure cria automaticamente uma identidade para ela no [Azure AD (Azure Active Directory)](../active-directory/fundamentals/active-directory-whatis.md). Essa identidade pode então ser usada para autenticar os pontos de extremidade dos Gêmeos Digitais do Azure para o encaminhamento de eventos.

Você pode habilitar identidades gerenciadas pelo sistema para uma instância dos Gêmeos Digitais do Azure **como parte da configuração inicial da instância** ou **habilitá-la posteriormente em uma instância que já existe**.

Qualquer um desses métodos de criação fornecerá as mesmas opções de configuração e o mesmo resultado final para sua instância. Esta seção descreve como fazer os dois.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Adicionar uma identidade gerenciada pelo sistema durante a criação da instância

Nessa seção, você aprenderá a habilitar uma identidade gerenciada pelo sistema em uma instância dos Gêmeos Digitais do Azure que está sendo criada no momento. 

Isso é feito adicionando um parâmetro `--assign-identity` ao comando `az dt create` que é usado para criar a instância. (Para obter mais informações sobre esse comando, confira a [documentação de referência](/cli/azure/dt#az_dt_create) ou as [instruções gerais para configurar uma instância dos Gêmeos Digitais do Azure](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)).

Para criar uma instância com uma identidade gerenciada pelo sistema, adicione o parâmetro `--assign-identity` da seguinte maneira:

```azurecli-interactive
az dt create -n {new_instance_name} -g {resource_group} --assign-identity
```

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Adicionar uma identidade gerenciada pelo sistema a uma instância existente

Nesta seção, você adicionará uma identidade gerenciada pelo sistema a uma instância dos Gêmeos Digitais do Azure que já existem.

Isso também é feito com o comando `az dt create` e o parâmetro `--assign-identity`. Em vez de fornecer um novo nome de uma instância para criar, você pode fornecer o nome de uma instância que já existe para atualizar o valor de `--assign-identity` para essa instância.

O comando para **habilitar** a identidade gerenciada é o mesmo que o para criar uma instância com uma identidade gerenciada pelo sistema. A única alteração é o valor do parâmetro de nome da instância:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity
```

Para **desabilitar** a identidade gerenciada em uma instância em que ela está habilitada no momento, use o comando semelhante a seguir para definir `--assign-identity` como `false`.

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity false
```

## <a name="assign-azure-roles-to-the-identity"></a>Atribuir funções do Azure à identidade 

Depois que uma identidade atribuída pelo sistema for criada para a sua instância dos Gêmeos Digitais do Azure, você precisará atribuir a ela as funções apropriadas para autenticar com diferentes tipos de [pontos de extremidade](concepts-route-events.md) para encaminhar eventos para destinos com suporte. Esta seção descreve as opções de função e como atribuí-las à identidade atribuída pelo sistema.

>[!NOTE]
> Essa é uma etapa importante: sem ela, a identidade não será capaz de acessar seus pontos de extremidade e os eventos não serão entregues.

### <a name="supported-destinations-and-azure-roles"></a>Destinos com suporte e funções do Azure 

Aqui estão as funções mínimas que uma identidade precisa para acessar um ponto de extremidade, dependendo do tipo de destino. Funções com permissões mais altas (como funções de proprietário de dados) também funcionarão.

| Destino | Função do Azure |
| --- | --- |
| Hubs de Eventos do Azure | Remetente de Dados dos Hubs de Eventos do Azure |
| Barramento de Serviço do Azure | Remetente de Dados do Barramento de Serviço do Azure |
| Contêiner de armazenamento do Azure | Colaborador de dados de blob de armazenamento |

Para obter mais informações sobre pontos de extremidade, rotas e os tipos de destinos com suporte para roteamento nos Gêmeos Digitais do Azure, confira [*Conceitos: Rotas de eventos*](concepts-route-events.md).

### <a name="assign-the-role"></a>Atribuir a função

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Você pode adicionar o parâmetro `--scopes` no comando `az dt create` para atribuir a identidade a um ou mais escopos com uma função especificada. Isso pode ser usado ao criar a instância pela primeira vez ou posteriormente, passando o nome de uma instância que já existe.

Aqui está um exemplo que cria uma instância com uma identidade gerenciada pelo sistema e atribui essa identidade a uma função personalizada chamada `MyCustomRole` em um hub de eventos.

```azurecli-interactive
az dt create -n {instance_name} -g {resource_group} --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource_group>/providers/Microsoft.EventHub/namespaces/<Event_Hubs_namespace>/eventhubs/<event_hub_name>" --role MyCustomRole
```

Para obter mais exemplos de atribuições de função com esse comando, confira a [Documentação de referência de **az dt create**](/cli/azure/dt#az_dt_create).

Como alternativa, você também pode usar o grupo de comandos [**az role assignment**](/cli/azure/role/assignment) para criar e gerenciar funções. Isso pode ser usado para dar suporte a cenários adicionais em que você não deseja agrupar a atribuição de função com o comando create.

## <a name="create-an-endpoint-with-identity-based-authentication"></a>Criar um ponto de extremidade com autenticação baseada em identidade

Depois de configurar uma identidade gerenciada pelo sistema para sua instância dos Gêmeos Digitais do Azure e atribuir a ela as funções apropriadas, você pode criar [pontos de extremidade](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) dos Gêmeos Digitais do Azure que podem usar a identidade para autenticação. Essa opção está disponível apenas para os pontos de extremidade do Hub de Eventos e do tipo de Barramento de Serviço (não tem suporte pela Grade de Eventos).

>[!NOTE]
> Você não pode editar um ponto de extremidade que já foi criado com a identidade baseada em chave para mudar para a autenticação baseada em identidade. Você deve escolher o tipo de autenticação quando o ponto de extremidade é criado pela primeira vez.

Isso é feito adicionando um parâmetro `--auth-type` ao comando `az dt endpoint create` que é usado para criar o ponto de extremidade. (Para obter mais informações sobre esse comando, confira a [documentação de referência](/cli/azure/dt/endpoint/create) ou as [instruções gerais para configurar um ponto de extremidade dos Gêmeos Digitais do Azure](how-to-manage-routes-apis-cli.md#create-the-endpoint)).

Para criar um ponto de extremidade que usa a autenticação baseada em identidade, especifique o tipo de autenticação `IdentityBased` com o parâmetro `--auth-type`. O exemplo a seguir ilustra isso para um ponto de extremidade de Hubs de Eventos.

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name {endpoint_name} --eventhub-resource-group {eventhub_resource_group} --eventhub-namespace {eventhub_namespace} --eventhub {eventhub_name} --auth-type IdentityBased -n {instance_name}
```

## <a name="considerations-for-disabling-system-managed-identities"></a>Considerações sobre a desabilitação de identidades gerenciadas pelo sistema

Como uma identidade é gerenciada separadamente dos pontos de extremidade que a usam, é importante considerar os efeitos que qualquer alteração na identidade ou suas funções podem ter nos pontos de extremidade em sua instância de Gêmeos Digitais do Azure. Se a identidade estiver desabilitada ou se uma função necessária para um ponto de extremidade for removida dele, o ponto de extremidade poderá se tornar inacessível e o fluxo de eventos será interrompido.

Para continuar usando um ponto de extremidade que foi configurado com uma identidade gerenciada que agora foi desabilitada, você precisará excluir o ponto de extremidade e [recriá-lo](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins) com um tipo de autenticação diferente. Pode levar até uma hora para que os eventos retomem a entrega para o ponto de extremidade após essa alteração.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as identidades gerenciadas no Azure AD: 
* [*Identidades gerenciadas dos recursos do Azure*](../active-directory/managed-identities-azure-resources/overview.md)