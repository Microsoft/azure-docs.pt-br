---
title: Habilitar uma identidade gerenciada para eventos de roteamento (versão prévia) – CLI
titleSuffix: Azure Digital Twins
description: Consulte Como habilitar uma identidade atribuída pelo sistema para o gêmeos digital do Azure e usá-la para encaminhar eventos, usando o CLI do Azure.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 32cbe31f95c03f9b0b5eb1a31a28033dce18b112
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417024"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-cli"></a>Habilitar uma identidade gerenciada para roteamento de eventos de gêmeos digital do Azure (versão prévia): CLI do Azure

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

Este artigo descreve como habilitar uma [identidade atribuída pelo sistema para uma instância do gêmeos digital do Azure](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (atualmente em versão prévia) e usar a identidade ao encaminhar eventos para destinos com suporte, como [Hub de eventos](../event-hubs/event-hubs-about.md), destinos do [barramento de serviço](../service-bus-messaging/service-bus-messaging-overview.md)   e [contêiner de armazenamento do Azure](../storage/blobs/storage-blobs-introduction.md).

Este artigo orienta você pelo processo usando o [**CLI do Azure**](/cli/azure/what-is-azure-cli).

Aqui estão as etapas abordadas neste artigo: 

1. Crie uma instância do gêmeos digital do Azure com uma identidade atribuída pelo sistema ou habilite a identidade atribuída pelo sistema em uma instância existente do gêmeos do Azure digital. 
1. Adicione uma função ou funções apropriadas à identidade. Por exemplo, atribua a função de **remetente de dados do hub de eventos do Azure** à identidade se o ponto de extremidade for Hub de eventos ou **função de remetente de dados do barramento de serviço do Azure** se o ponto de extremidade for barramento de serviço.
1. Crie um ponto de extremidade no Azure digital gêmeos que possa usar identidades atribuídas pelo sistema para autenticação.

## <a name="enable-system-managed-identities-for-an-instance"></a>Habilitar identidades gerenciadas pelo sistema para uma instância 

Quando você habilita uma identidade atribuída pelo sistema em sua instância de gêmeos digital do Azure, o Azure cria automaticamente uma identidade para ela no [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Essa identidade pode então ser usada para autenticar os pontos de extremidade do Azure digital gêmeos para encaminhamento de eventos.

Você pode habilitar identidades gerenciadas pelo sistema para uma instância do gêmeos digital do Azure **como parte da configuração inicial da instância** ou **habilitá-la posteriormente em uma instância que já existe**.

Qualquer um desses métodos de criação fornecerá as mesmas opções de configuração e o mesmo resultado final para sua instância. Esta seção descreve como fazer as duas.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Adicionar uma identidade gerenciada pelo sistema durante a criação da instância

Nesta seção, você aprenderá a habilitar uma identidade gerenciada pelo sistema em uma instância do gêmeos digital do Azure que está sendo criada no momento. 

Isso é feito adicionando um `--assign-identity` parâmetro ao `az dt create` comando que é usado para criar a instância. (Para obter mais informações sobre esse comando, consulte sua [documentação de referência](/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_create) ou as [instruções gerais para configurar uma instância de gêmeos digital do Azure](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)).

Para criar uma instância com uma identidade gerenciada do sistema, adicione o  `--assign-identity` parâmetro da seguinte maneira:

```azurecli-interactive
az dt create -n {new_instance_name} -g {resource_group} --assign-identity
```

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Adicionar uma identidade gerenciada pelo sistema a uma instância existente

Nesta seção, você adicionará uma identidade gerenciada pelo sistema a uma instância do gêmeos digital do Azure que já existe.

Isso também é feito com o `az dt create` comando e o `--assign-identity` parâmetro. Em vez de fornecer um novo nome de uma instância para criar, você pode fornecer o nome de uma instância que já existe para atualizar o valor de `--assign-identity` para essa instância.

O comando para **habilitar** a identidade gerenciada é o mesmo que o comando para criar uma instância com uma identidade gerenciada do sistema. Todas as alterações são o valor do parâmetro nome da instância:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity
```

Para **desabilitar** a identidade gerenciada em uma instância em que ela está habilitada, use o seguinte comando semelhante para definir `--assign-identity` como `false` .

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity false
```

## <a name="assign-azure-roles-to-the-identity"></a>Atribuir funções do Azure à identidade 

Depois que uma identidade atribuída pelo sistema for criada para sua instância de gêmeos digital do Azure, você precisará atribuir as funções apropriadas para autenticar com diferentes tipos de [pontos de extremidade](concepts-route-events.md) para encaminhar eventos para destinos com suporte. Esta seção descreve as opções de função e como atribuí-las à identidade atribuída pelo sistema.

>[!NOTE]
> Essa é uma etapa importante — sem ele, a identidade não será capaz de acessar seus pontos de extremidade e eventos não serão entregues.

### <a name="supported-destinations-and-azure-roles"></a>Destinos com suporte e funções do Azure 

Aqui estão as funções mínimas que uma identidade precisa para acessar um ponto de extremidade, dependendo do tipo de destino. Funções com permissões mais altas (como funções de proprietário de dados) também funcionarão.

| Destino | Função do Azure |
| --- | --- |
| Hubs de eventos do Azure | Remetente de Dados dos Hubs de Eventos do Azure |
| Barramento de Serviço do Azure | Remetente de Dados do Barramento de Serviço do Azure |
| Contêiner de armazenamento do Azure | Colaborador de dados de blob de armazenamento |

Para obter mais informações sobre pontos de extremidade, rotas e os tipos de destinos com suporte para roteamento no Azure digital gêmeos, consulte [*conceitos: rotas de eventos*](concepts-route-events.md).

### <a name="assign-the-role"></a>Atribuir a função

>[!NOTE]
> Esta seção deve ser concluída por um usuário do Azure com permissões para gerenciar o acesso do usuário aos recursos do Azure (incluindo concessão e delegação de permissões). Funções comuns que atendem a esse requisito são *proprietário*, *administrador da conta* ou a combinação de administrador e *colaborador* de *acesso do usuário* . Para obter mais informações sobre requisitos de permissão para funções de gêmeos digitais do Azure, consulte [*como: configurar a instância e a autenticação*](how-to-set-up-instance-portal.md#prerequisites-permission-requirements).

Você pode adicionar o `--scopes` parâmetro `az dt create` no comando para atribuir a identidade a um ou mais escopos com uma função especificada. Isso pode ser usado ao criar pela primeira vez a instância, ou posteriormente, passando o nome de uma instância que já existe.

Aqui está um exemplo que cria uma instância com uma identidade gerenciada do sistema e atribui essa identidade a uma função personalizada chamada `MyCustomRole` em um hub de eventos.

```azurecli-interactive
az dt create -n {instance_name} -g {resource_group} --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource_group>/providers/Microsoft.EventHub/namespaces/<Event_Hubs_namespace>/eventhubs/<event_hub_name>" --role MyCustomRole
```

Para obter mais exemplos de atribuições de função com esse comando, consulte a [documentação **AZ DT Create** Reference](/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_create).

Como alternativa, você também pode usar o grupo de comandos [**AZ role Assignment**](/cli/azure/role/assignment?view=azure-cli-latest&preserve-view=true) para criar e gerenciar funções. Isso pode ser usado para dar suporte a cenários adicionais em que você não deseja agrupar a atribuição de função com o comando criar.

## <a name="create-an-endpoint-with-identity-based-authorization"></a>Criar um ponto de extremidade com autorização baseada em identidade

Depois de configurar uma identidade gerenciada pelo sistema para sua instância do gêmeos digital do Azure e atribuir a ela as funções apropriadas, você pode criar [pontos de extremidade](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) gêmeos do Azure digital que são capazes de usar a identidade para autenticação. Essa opção só está disponível para os pontos de extremidade do hub de eventos e do tipo de barramento de serviço (não há suporte para a grade de eventos).

>[!NOTE]
> Você não pode editar um ponto de extremidade que já foi criado com a identidade baseada em chave para mudar para a autenticação baseada em identidade. Você deve escolher o tipo de autenticação quando o ponto de extremidade é criado pela primeira vez.

Isso é feito adicionando um `--auth-type` parâmetro ao `az dt endpoint create` comando usado para criar o ponto de extremidade. (Para obter mais informações sobre esse comando, consulte sua [documentação de referência](/cli/azure/ext/azure-iot/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) ou as [instruções gerais para configurar um ponto de extremidade de gêmeos digital do Azure](how-to-manage-routes-apis-cli.md#create-the-endpoint)).

Para criar um ponto de extremidade que usa a autenticação baseada em identidade, especifique o `IdentityBased` tipo de autenticação com o  `--auth-type` parâmetro. O exemplo a seguir ilustra isso para um ponto de extremidade de hubs de eventos.

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name {endpoint_name} --eventhub-resource-group {eventhub_resource_group} --eventhub-namespace {eventhub_namespace} --eventhub {eventhub_name} --auth-type IdentityBased -n {instance_name}
```

## <a name="considerations-for-disabling-system-managed-identities"></a>Considerações sobre a desabilitação de identidades gerenciadas pelo sistema

Como uma identidade é gerenciada separadamente dos pontos de extremidade que o utilizam, é importante considerar os efeitos que qualquer alteração na identidade ou suas funções podem ter nos pontos de extremidade em sua instância de gêmeos digital do Azure. Se a identidade estiver desabilitada ou se uma função necessária para um ponto de extremidade for removida, o ponto de extremidade poderá se tornar inacessível e o fluxo de eventos será interrompido.

Para continuar usando um ponto de extremidade que foi configurado com uma identidade gerenciada que agora foi desabilitada, você precisará excluir o ponto de extremidade e [recriá-lo](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins) com um tipo de autenticação diferente. Pode levar até uma hora para que os eventos retomem a entrega para o ponto de extremidade após essa alteração.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre identidades gerenciadas no Azure AD: 
* [*Identidades gerenciadas dos recursos do Azure*](../active-directory/managed-identities-azure-resources/overview.md)