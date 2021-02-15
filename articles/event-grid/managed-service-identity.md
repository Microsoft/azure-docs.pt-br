---
title: Entrega de eventos, identidade de serviço gerenciada e link privado
description: Este artigo descreve como habilitar a identidade do serviço gerenciado para um tópico da grade de eventos do Azure. Use-o para encaminhar eventos para destinos com suporte.
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 3e643465db7cc918499ca962c4697cb61cb4b594
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007764"
---
# <a name="event-delivery-with-a-managed-identity"></a>Entrega de eventos com uma identidade gerenciada
Este artigo descreve como habilitar uma [identidade de serviço gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para domínios ou tópicos personalizados da grade de eventos do Azure. Use-o para encaminhar eventos para destinos com suporte, como filas e tópicos de Barramento de Serviço, hubs de eventos e contas de armazenamento.

Aqui estão as etapas abordadas em detalhes neste artigo:
1. Crie um tópico ou domínio personalizado com uma identidade atribuída pelo sistema ou atualize um tópico ou domínio personalizado existente para habilitar a identidade. 
1. Adicione a identidade a uma função apropriada (por exemplo, remetente de dados do barramento de serviço) no destino (por exemplo, uma fila do barramento de serviço).
1. Ao criar assinaturas de evento, habilite o uso da identidade para entregar eventos ao destino. 

> [!NOTE]
> Atualmente, não é possível entregar eventos usando pontos de [extremidade privados](../private-link/private-endpoint-overview.md). Para obter mais informações, consulte a seção [pontos de extremidade particulares](#private-endpoints) no final deste artigo. 

## <a name="create-a-custom-topic-or-domain-with-an-identity"></a>Criar um tópico ou um domínio personalizado com uma identidade
Primeiro, vamos dar uma olhada em como criar um tópico ou domínio com uma identidade gerenciada pelo sistema.

### <a name="use-the-azure-portal"></a>Use o Portal do Azure
Você pode habilitar a identidade atribuída pelo sistema para um tópico ou domínio personalizado enquanto o cria no portal do Azure. A imagem a seguir mostra como habilitar uma identidade gerenciada pelo sistema para um tópico personalizado. Basicamente, você seleciona a opção **Habilitar identidade atribuída pelo sistema** na página **Avançado** do assistente de criação de tópico. Você também verá essa opção na página **avançado** do assistente de criação de domínio. 

![Habilitar identidade ao criar um tópico personalizado](./media/managed-service-identity/create-topic-identity.png)

### <a name="use-the-azure-cli"></a>Usar a CLI do Azure
Você também pode usar o CLI do Azure para criar um tópico ou domínio personalizado com uma identidade atribuída pelo sistema. Use o comando `az eventgrid topic create` com o parâmetro `--identity` definido como `systemassigned`. Se você não especificar um valor para este parâmetro, o valor padrão `noidentity` será usado. 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Da mesma forma, você pode usar o comando `az eventgrid domain create` para criar um domínio com uma identidade gerenciada pelo sistema.

## <a name="enable-an-identity-for-an-existing-custom-topic-or-domain"></a>Habilitar uma identidade para um tópico ou domínio personalizado existente
Na seção anterior, você aprendeu como habilitar uma identidade gerenciada pelo sistema enquanto você criou um tópico ou um domínio personalizado. Nesta seção, você aprenderá a habilitar uma identidade gerenciada pelo sistema para um domínio ou tópico personalizado existente. 

### <a name="use-the-azure-portal"></a>Use o Portal do Azure
O procedimento a seguir mostra como habilitar a identidade gerenciada pelo sistema para um tópico personalizado. As etapas para habilitar uma identidade para um domínio são semelhantes. 

1. Acesse o [portal do Azure](https://portal.azure.com).
2. Procure **Tópicos da grade de eventos** na barra de pesquisa na parte superior.
3. Selecione o **tópico personalizado** para o qual você deseja habilitar a identidade gerenciada. 
4. Alterne para a guia **Identidade**. 
5. Ative a opção para **habilitar a identidade** . 
1. Selecione **salvar** na barra de ferramentas para salvar a configuração. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Página de identidade para um tópico personalizado"::: 

Você pode usar etapas semelhantes para habilitar uma identidade para um domínio de grade de eventos.

### <a name="use-the-azure-cli"></a>Usar a CLI do Azure
Use o `az eventgrid topic update` comando com `--identity` definido como `systemassigned` para habilitar a identidade atribuída pelo sistema para um tópico personalizado existente. Se quiser desabilitar a identidade, especifique `noidentity` como o valor. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

O comando para atualizar um domínio existente é semelhante (`az eventgrid domain update`).

## <a name="supported-destinations-and-azure-roles"></a>Destinos com suporte e funções do Azure
Depois de habilitar a identidade para seu tópico ou domínio personalizado da grade de eventos, o Azure cria automaticamente uma identidade no Azure Active Directory. Adicione essa identidade às funções apropriadas do Azure para que o tópico ou o domínio personalizado possa encaminhar eventos para os destinos com suporte. Por exemplo, adicione a identidade à função de **remetente de dados dos hubs de eventos do Azure** para um namespace de hubs de eventos do Azure para que o tópico personalizado da grade de eventos possa encaminhar eventos para os hubs de eventos nesse namespace. 

Atualmente, a grade de eventos do Azure dá suporte a tópicos personalizados ou a domínios configurados com uma identidade gerenciada atribuída pelo sistema para encaminhar eventos para os seguintes destinos. Essa tabela também fornece as funções em que a identidade deve estar para que o tópico personalizado possa encaminhar os eventos.

| Destino | Função do Azure | 
| ----------- | --------- | 
| Filas e tópicos do Barramento de Serviço | [Remetente de dados do Barramento de Serviço do Azure](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Hubs de eventos do Azure | [Remetente de dados dos Hubs de Eventos do Azure](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Armazenamento de Blobs do Azure | [Colaborador de dados de blob de armazenamento](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Armazenamento de Filas do Azure |[Remetente da mensagem de dados da fila de armazenamento](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 

## <a name="add-an-identity-to-azure-roles-on-destinations"></a>Adicionar uma identidade às funções do Azure em destinos
Esta seção descreve como adicionar a identidade do seu tópico ou domínio personalizado a uma função do Azure. 

### <a name="use-the-azure-portal"></a>Use o Portal do Azure
Você pode usar a portal do Azure para atribuir o tópico personalizado ou a identidade do domínio a uma função apropriada para que o tópico ou domínio personalizado possa encaminhar eventos para o destino. 

O exemplo a seguir adiciona uma identidade gerenciada para um tópico personalizado da grade de eventos chamado **msitesttopic** à função de **remetente de dados do barramento de serviço do Azure** para um namespace do barramento de serviço que contém um recurso de fila ou tópico. Quando você adiciona à função no nível de namespace, o tópico personalizado da grade de eventos pode encaminhar eventos para todas as entidades no namespace. 

1. Vá para o **namespace do barramento de serviço** na [portal do Azure](https://portal.azure.com). 
1. Selecione **controle de acesso** no painel esquerdo. 
1. Selecione **Adicionar** na seção **Adicionar uma atribuição de função**. 
1. Na página **Adicionar uma atribuição de função**, siga os seguintes passos:
    1. Selecione a função. Neste caso, é **Remetente de dados do Barramento de Serviço do Azure**. 
    1. Selecione a **identidade** do seu tópico ou domínio personalizado da grade de eventos. 
    1. Selecione **salvar** para salvar a configuração.

Para adicionar uma identidade a outras funções mencionadas na tabela, as etapas são semelhantes. 

### <a name="use-the-azure-cli"></a>Usar a CLI do Azure
O exemplo nesta seção mostra como usar o CLI do Azure para adicionar uma identidade a uma função do Azure. Os comandos de exemplo são para tópicos personalizados de grade de eventos. Os comandos para domínios de grade de eventos são semelhantes. 

#### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>Obter a ID da entidade de segurança para a identidade do sistema do tópico personalizado 
Primeiro, obtenha a ID da entidade de segurança da identidade gerenciada pelo sistema do tópico personalizado e atribua a identidade às funções apropriadas.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Criar uma atribuição de função para hubs de eventos em vários escopos 
O exemplo de CLI a seguir mostra como adicionar a identidade de um tópico personalizado à função de **remetente de dados dos hubs de eventos do Azure** no nível do namespace ou no nível do hub de eventos. Se você criar a atribuição de função no nível de namespace, o tópico personalizado poderá encaminhar eventos para todos os hubs de eventos nesse namespace. Se você criar uma atribuição de função no nível do hub de eventos, o tópico personalizado poderá encaminhar eventos somente para esse Hub de eventos específico. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Criar uma atribuição de função para um tópico do barramento de serviço em vários escopos 
O exemplo de CLI a seguir mostra como adicionar uma identidade do tópico personalizado da grade de eventos à função de **remetente de dados do barramento de serviço do Azure** no nível do namespace ou no nível de tópico do barramento de serviço. Se você criar a atribuição de função no nível de namespace, o tópico da grade de eventos poderá encaminhar eventos para todas as entidades (filas ou tópicos do barramento de serviço) dentro desse namespace. Se você criar uma atribuição de função no nível de fila ou de tópico do barramento de serviço, o tópico personalizado da grade de eventos poderá encaminhar eventos somente para a fila ou tópico específico do barramento de serviço. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-an-identity"></a>Criar assinaturas de evento que usam uma identidade
Depois de ter um tópico personalizado da grade de eventos ou um domínio com uma identidade gerenciada pelo sistema e tiver adicionado a identidade à função apropriada no destino, você estará pronto para criar assinaturas que usam a identidade. 

### <a name="use-the-azure-portal"></a>Use o Portal do Azure
Ao criar uma assinatura de evento, você verá uma opção para habilitar o uso de uma identidade atribuída pelo sistema para um ponto de extremidade na seção **detalhes do ponto de extremidade** . 

![Habilitar identidade ao criar uma assinatura de evento para uma fila do barramento de serviço](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

Você também pode habilitar o uso de uma identidade atribuída pelo sistema para ser usada para mensagens mortas na guia **recursos adicionais** . 

![Habilitar identidade atribuída ao sistema para mensagens mortas](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="use-the-azure-cli---service-bus-queue"></a>Usar a fila do barramento de serviço CLI do Azure 
Nesta seção, você aprenderá a usar o CLI do Azure para habilitar o uso de uma identidade atribuída pelo sistema para entregar eventos a uma fila do barramento de serviço. A identidade deve ser um membro da função **Remetente de dados do Barramento de Serviço do Azure**. Ela também deve ser membro da função **Colaborador de dados de blobs de armazenamento** na conta de armazenamento usada para mensagens mortas. 

#### <a name="define-variables"></a>Definir variáveis
Primeiro, especifique valores para as variáveis a seguir a serem usadas no comando da CLI. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Criar uma assinatura de evento usando uma identidade gerenciada para entrega 
Este comando de exemplo cria uma assinatura de evento para um tópico personalizado da grade de eventos com um tipo de ponto de extremidade definido como **fila do barramento de serviço**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>Criar uma assinatura de evento usando uma identidade gerenciada para entrega e mensagens mortas
Este comando de exemplo cria uma assinatura de evento para um tópico personalizado da grade de eventos com um tipo de ponto de extremidade definido como **fila do barramento de serviço**. Ele também especifica que a identidade gerenciada pelo sistema deve ser usada para mensagens mortas. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="use-the-azure-cli---event-hubs"></a>Usar os hubs de eventos de CLI do Azure 
Nesta seção, você aprenderá a usar o CLI do Azure para habilitar o uso de uma identidade atribuída pelo sistema para entregar eventos a um hub de eventos. A identidade deve ser um membro da função **Remetente de dados dos Hubs de Eventos do Azure**. Ela também deve ser membro da função **Colaborador de dados de blobs de armazenamento** na conta de armazenamento usada para mensagens mortas. 

#### <a name="define-variables"></a>Definir variáveis
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Criar uma assinatura de evento usando uma identidade gerenciada para entrega 
Este comando de exemplo cria uma assinatura de evento para um tópico personalizado da grade de eventos com um tipo de ponto de extremidade definido como **hubs de eventos**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Criar uma assinatura de evento usando uma identidade gerenciada para entrega + mensagens mortas 
Este comando de exemplo cria uma assinatura de evento para um tópico personalizado da grade de eventos com um tipo de ponto de extremidade definido como **hubs de eventos**. Ele também especifica que a identidade gerenciada pelo sistema deve ser usada para mensagens mortas. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="use-the-azure-cli---azure-storage-queue"></a>Usar o CLI do Azure-fila de armazenamento do Azure 
Nesta seção, você aprenderá a usar o CLI do Azure para habilitar o uso de uma identidade atribuída pelo sistema para entregar eventos a uma fila de armazenamento do Azure. A identidade deve ser um membro da função de **remetente da mensagem de dados da fila de armazenamento** na conta de armazenamento. Ela também deve ser membro da função **Colaborador de dados de blobs de armazenamento** na conta de armazenamento usada para mensagens mortas.

#### <a name="define-variables"></a>Definir variáveis  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Criar uma assinatura de evento usando uma identidade gerenciada para entrega 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Criar uma assinatura de evento usando uma identidade gerenciada para entrega + mensagens mortas 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```

## <a name="private-endpoints"></a>Pontos de extremidade privados
Atualmente, não é possível entregar eventos usando pontos de [extremidade privados](../private-link/private-endpoint-overview.md). Ou seja, não haverá suporte se você tiver requisitos estritos de isolamento de rede em que o tráfego de eventos entregues não deve sair do espaço IP privado. 

No entanto, se seus requisitos chamarem uma maneira segura de enviar eventos usando um canal criptografado e uma identidade conhecida do remetente (nesse caso, a grade de eventos) usando o espaço IP público, você poderá entregar eventos aos hubs de eventos, ao barramento de serviço ou ao serviço de armazenamento do Azure usando um tópico personalizado da grade de eventos do Azure ou um domínio com identidade gerenciada pelo sistema Em seguida, você pode usar um link privado configurado em Azure Functions ou seu webhook implantado em sua rede virtual para efetuar pull de eventos. Consulte o exemplo: [conectar-se a pontos de extremidade privados com Azure Functions](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).

Nessa configuração, o tráfego passa pelo IP público/Internet da grade de eventos para os hubs de eventos, barramento de serviço ou armazenamento do Azure, mas o canal pode ser criptografado e uma identidade gerenciada da grade de eventos é usada. Se você configurar seu Azure Functions ou webhook implantado em sua rede virtual para usar os hubs de eventos, o barramento de serviço ou o armazenamento do Azure por meio do link privado, essa seção do tráfego permanecerá, de forma evidente, no Azure.


## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre identidades de serviço gerenciado, consulte [O que são identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md). 
