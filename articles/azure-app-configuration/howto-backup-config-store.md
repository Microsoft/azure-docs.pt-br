---
title: Fazer backup automaticamente de valores de chave de repositórios de configuração Azure App
description: Saiba como configurar um backup automático de valores de chave entre repositórios de configuração de aplicativo.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: b3e0bcad7beccc31e1772fbb24ffad7f502b8140
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454236"
---
# <a name="back-up-app-configuration-stores-automatically"></a>Fazer backup de repositórios de configuração de aplicativo automaticamente

Neste artigo, você aprenderá a configurar um backup automático de valores-chave de um repositório de configuração de Azure App primário para um repositório secundário. O backup automático usa a integração da grade de eventos do Azure com a configuração do aplicativo. 

Depois de configurar o backup automático, a configuração de aplicativo publicará eventos na grade de eventos do Azure para qualquer alteração feita nos valores de chave em um repositório de configuração. A grade de eventos dá suporte a uma variedade de serviços do Azure, dos quais os usuários podem assinar os eventos emitidos sempre que os valores de chave são criados, atualizados ou excluídos.

## <a name="overview"></a>Visão geral

Neste artigo, você usará o armazenamento de filas do Azure para receber eventos da grade de eventos e usar um gatilho de temporizador de Azure Functions para processar eventos na fila em lotes. 

Quando uma função é disparada, com base nos eventos, ela buscará os valores mais recentes das chaves que foram alteradas do repositório de configuração do aplicativo primário e atualizará o repositório secundário adequadamente. Essa configuração ajuda a combinar várias alterações que ocorrem em um curto período em uma operação de backup, o que evita solicitações excessivas feitas em seus repositórios de configuração de aplicativo.

![Diagrama que mostra a arquitetura do backup do repositório de configurações do aplicativo.](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Provisionamento de recursos

A motivação por trás do backup de repositórios de configuração de aplicativo é usar vários repositórios de configuração em diferentes regiões do Azure para aumentar a resiliência geográfica do seu aplicativo. Para conseguir isso, os armazenamentos primários e secundários devem estar em diferentes regiões do Azure. Todos os outros recursos criados neste tutorial podem ser provisionados em qualquer região de sua escolha. Isso ocorre porque, se a região primária estiver inoperante, não haverá nada de novo para fazer backup até que a região primária possa ser acessada novamente.

Neste tutorial, você criará um armazenamento secundário na `centralus` região e todos os outros recursos na `westus` região.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)].

## <a name="prerequisites"></a>Pré-requisitos 

- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) com a carga de trabalho de desenvolvimento do Azure.

- [SDK do .Net Core](https://dotnet.microsoft.com/download).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este tutorial requer a versão 2.3.1 ou posterior do CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

O grupo de recursos do Azure é uma coleção lógica na qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos usando o comando [az group create](/cli/azure/group).

O exemplo a seguir cria um grupo de recursos chamado `<resource_group_name>` na localização `westus`.  Substitua `<resource_group_name>` por um nome exclusivo para o grupo de recursos.

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>Criar repositórios de configuração de aplicativo

Crie seus armazenamentos de configuração de aplicativo primários e secundários em regiões diferentes.
Substitua `<primary_appconfig_name>` e `<secondary_appconfig_name>` por nomes exclusivos para seus repositórios de configuração. Cada nome de repositório deve ser exclusivo porque é usado como um nome DNS.

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-a-queue"></a>Criar uma fila

Crie uma conta de armazenamento e uma fila para receber os eventos publicados pela grade de eventos.

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>Assinar seus eventos de repositório de configuração de aplicativo

Você assina esses dois eventos do repositório de configuração do aplicativo primário:

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

O comando a seguir cria uma assinatura de grade de eventos para os dois eventos enviados à sua fila. O tipo de ponto de extremidade é definido como `storagequeue` , e o ponto de extremidade é definido como a ID da fila. Substitua `<event_subscription_name>` pelo nome de sua escolha para a assinatura de evento.

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-functions-for-handling-events-from-queue-storage"></a>Criar funções para manipular eventos do armazenamento de filas

### <a name="set-up-with-ready-to-use-functions"></a>Configurar com funções prontas para uso

Neste artigo, você trabalhará com funções C# que têm as seguintes propriedades:
- Pilha de tempo de execução .NET Core 3,1
- Azure Functions Runtime versão 3. x
- Função disparada pelo temporizador a cada 10 minutos

Para facilitar a inicialização de seus dados, [testamos e publicamos uma função](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) que você pode usar sem fazer nenhuma alteração no código. Baixe os arquivos de projeto e [publique-os em seu próprio aplicativo de funções do Azure do Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

> [!IMPORTANT]
> Não faça nenhuma alteração nas variáveis de ambiente no código que você baixou. Você criará as configurações de aplicativo necessárias na próxima seção.
>

### <a name="build-your-own-function"></a>Crie sua própria função

Se o código de exemplo fornecido anteriormente não atender aos seus requisitos, você também poderá criar sua própria função. Sua função deve ser capaz de executar as seguintes tarefas para concluir o backup:
- Leia periodicamente o conteúdo da fila para ver se ele contém notificações da grade de eventos. Consulte o [SDK da fila de armazenamento](../storage/queues/storage-quickstart-queues-dotnet.md) para obter detalhes de implementação.
- Se sua fila contiver [notificações de eventos da grade de eventos](./concept-app-configuration-event.md#event-schema), extraia todas as informações exclusivas `<key, label>` das mensagens de evento. A combinação de chave e rótulo é o identificador exclusivo para alterações de chave-valor no repositório primário.
- Leia todas as configurações do repositório primário. Atualize somente as configurações no armazenamento secundário que têm um evento correspondente na fila. Exclua todas as configurações do armazenamento secundário que estavam presentes na fila, mas não no repositório primário. Você pode usar o [SDK de configuração de aplicativo](https://github.com/Azure/AppConfiguration#sdks) para acessar os armazenamentos de configuração de forma programática.
- Exclua mensagens da fila se não houver nenhuma exceção durante o processamento.
- Implemente o tratamento de erros de acordo com suas necessidades. Consulte o exemplo de código anterior para ver algumas exceções comuns que talvez você queira manipular.

Para saber mais sobre como criar uma função, consulte: [criar uma função no Azure que é disparada por um temporizador](../azure-functions/functions-create-scheduled-function.md) e [desenvolver Azure Functions usando o Visual Studio](../azure-functions/functions-develop-vs.md).


> [!IMPORTANT]
> Use o melhor Judgement para escolher a agenda do temporizador com base na frequência com que você faz alterações no repositório de configuração primário. A execução da função muitas vezes pode acabar reduzindo as solicitações para o armazenamento.
>


## <a name="create-function-app-settings"></a>Criar configurações do aplicativo de funções

Se você estiver usando uma função que fornecemos, precisará das seguintes configurações de aplicativo em seu aplicativo de funções:
- `PrimaryStoreEndpoint`: Ponto de extremidade para o repositório de configuração de aplicativo primário. Um exemplo é `https://{primary_appconfig_name}.azconfig.io`.
- `SecondaryStoreEndpoint`: Ponto de extremidade para o repositório de configurações do aplicativo secundário. Um exemplo é `https://{secondary_appconfig_name}.azconfig.io`.
- `StorageQueueUri`: URI da fila. Um exemplo é `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`.

O comando a seguir cria as configurações de aplicativo necessárias em seu aplicativo de funções. Substitua `<function_app_name>` pelo nome do aplicativo de funções.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-the-function-app"></a>Conceder acesso à identidade gerenciada do aplicativo de funções

Use o comando a seguir ou o [portal do Azure](../app-service/overview-managed-identity.md#add-a-system-assigned-identity) para adicionar uma identidade gerenciada atribuída pelo sistema para seu aplicativo de funções.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> Para executar a criação de recursos e o gerenciamento de função necessários, sua conta precisa de `Owner` permissões no escopo apropriado (sua assinatura ou grupo de recursos). Se você precisar de assistência com a atribuição de função, saiba [como atribuir funções do Azure usando o portal do Azure](../role-based-access-control/role-assignments-portal.md).

Use os comandos a seguir ou o [portal do Azure](./howto-integrate-azure-managed-service-identity.md#grant-access-to-app-configuration) para conceder a identidade gerenciada do acesso do aplicativo de funções aos seus repositórios de configuração de aplicativo. Use estas funções:
- Atribua a `App Configuration Data Reader` função no repositório de configuração do aplicativo primário.
- Atribua a `App Configuration Data Owner` função no repositório de configurações do aplicativo secundário.

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

Use o comando a seguir ou o [portal do Azure](../storage/common/storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal) para conceder a identidade gerenciada do acesso do aplicativo de funções à sua fila. Atribua a `Storage Queue Data Contributor` função na fila.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Disparar um evento da Configuração de Aplicativos

Para testar se tudo funciona, você pode criar, atualizar ou excluir um valor de chave do repositório primário. Você deve ver automaticamente essa alteração no armazenamento secundário alguns segundos depois que o temporizador for disparado Azure Functions.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

Você disparou o evento. Em alguns instantes, a grade de eventos enviará a notificação de eventos para sua fila. *Após a próxima execução agendada de sua função*, exiba as definições de configuração em seu armazenamento secundário para ver se ela contém o valor de chave atualizado do repositório primário.

> [!NOTE]
> Você pode [disparar a função manualmente](../azure-functions/functions-manually-run-non-http.md) durante os testes e a solução de problemas sem esperar pelo gatilho de temporizador agendado.

Depois de verificar se a função de backup foi executada com êxito, você pode ver que a chave agora está presente em seu armazenamento secundário.

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>Solução de problemas

Se você não vir a nova configuração em seu armazenamento secundário:

- Verifique se a função de backup foi disparada *depois* de criar a configuração em seu repositório primário.
- É possível que a grade de eventos não tenha enviado a notificação de eventos para a fila no tempo. Verifique se a fila ainda contém a notificação de eventos do seu repositório primário. Se tiver, dispare a função de backup novamente.
- Verifique se há erros ou avisos no [Azure Functions logs](../azure-functions/functions-create-scheduled-function.md#test-the-function) .
- Use o [portal do Azure](../azure-functions/functions-how-to-use-azure-function-app-settings.md#get-started-in-the-azure-portal) para garantir que o aplicativo de funções do Azure contenha valores corretos para as configurações do aplicativo que Azure Functions está tentando ler.
- Você também pode configurar o monitoramento e os alertas para Azure Functions usando o [aplicativo Azure insights](../azure-functions/functions-monitoring.md?tabs=cmd). 


## <a name="clean-up-resources"></a>Limpar os recursos
Se você planeja continuar trabalhando com essa configuração de aplicativo e assinatura de evento, não limpe os recursos criados neste artigo. Se você não planeja continuar, use o comando a seguir para excluir os recursos criados neste artigo.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como configurar o backup automático de seus valores de chave, saiba mais sobre como você pode aumentar a resiliência geográfica do seu aplicativo:

- [Resiliência e recuperação de desastre](concept-disaster-recovery.md)
