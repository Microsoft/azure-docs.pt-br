---
title: Carregar dados de uso, métricas e logs para Azure Monitor
description: Carregar inventário de recursos, dados de uso, métricas e logs para Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: a522a650413be056ff64d26e90b6c15cf88d9a7d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101643483"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>Carregar dados de uso, métricas e logs para Azure Monitor

Periodicamente, você pode exportar informações de uso para fins de cobrança, métricas de monitoramento e logs e, em seguida, carregá-las no Azure. A exportação e o upload de qualquer um desses três tipos de dados também criarão e atualizarão o controlador de dados, a instância gerenciada do SQL e os recursos do grupo de servidores de hiperescala do PostgreSQL no Azure.

> [!NOTE] 
> Durante o período de versão prévia, não há nenhum custo para usar os serviços de dados habilitados para Arc do Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Antes de poder carregar dados de uso, métricas ou logs, você precisa:

* Instalar ferramentas 
* [Registrar o `Microsoft.AzureArcData` provedor de recursos](#register-the-resource-provider) 
* [Criar a entidade de serviço](#create-service-principal)

## <a name="install-tools"></a>Instalar ferramentas

As ferramentas necessárias incluem: 
* CLI do Azure (AZ) 
* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 

Consulte [instalar ferramentas](./install-client-tools.md).

## <a name="register-the-resource-provider"></a>Registre o provedor de recursos

Antes de carregar as métricas ou os dados do usuário no Azure, você precisa garantir que sua assinatura do Azure tenha o `Microsoft.AzureArcData` provedor de recursos registrado.

Para verificar o provedor de recursos, execute o seguinte comando:

```azurecli
az provider show -n Microsoft.AzureArcData -o table
```

Se o provedor de recursos não estiver registrado em sua assinatura no momento, você poderá registrá-lo. Para registrá-lo, execute o comando a seguir.  Este comando pode levar um minuto ou dois para ser concluído.

```azurecli
az provider register -n Microsoft.AzureArcData --wait
```

## <a name="create-service-principal"></a>Criar uma entidade de serviço

A entidade de serviço é usada para carregar dados de uso e métricas.

Siga estes comandos para criar sua entidade de serviço de carregamento de métricas:

> [!NOTE]
> A criação de uma entidade de serviço requer [determinadas permissões no Azure](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Para criar uma entidade de serviço, atualize o exemplo a seguir. Substitua `<ServicePrincipalName>` e `SubscriptionId` `resourcegroup` pelos seus valores e execute o comando:

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName> --role Contributor --scopes /subscriptions/{SubscriptionId}/resourceGroups/{resourcegroup}
```

Se você criou a entidade de serviço anteriormente e precisa apenas obter as credenciais atuais, execute o comando a seguir para redefinir a credencial.

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

Por exemplo, para criar uma entidade de serviço chamada `azure-arc-metrics` , execute o seguinte comando

```azurecli
az ad sp create-for-rbac --name azure-arc-metrics --role Contributor --scopes /subscriptions/a345c178a-845a-6a5g-56a9-ff1b456123z2/resourceGroups/myresourcegroup
```

Saída de exemplo:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Salve os `appId` `password` valores, e `tenant` em uma variável de ambiente para uso posterior. 

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
```

::: zone-end

Depois de criar a entidade de serviço, atribua a entidade de serviço à função apropriada. 

## <a name="assign-roles-to-the-service-principal"></a>Atribuir funções à entidade de serviço

Execute este comando para atribuir a entidade de serviço à `Monitoring Metrics Publisher` função na assinatura em que os recursos da instância do banco de dados estão localizados:

::: zone pivot="client-operating-system-windows-command"

> [!NOTE]
> Você precisa usar aspas duplas para nomes de função ao executar de um ambiente do Windows.

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/{SubscriptionID}/resourceGroups/{resourcegroup}

```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/{SubscriptionID}/resourceGroups/{resourcegroup}
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/{SubscriptionID}/resourceGroups/{resourcegroup}
```

::: zone-end

Saída de exemplo:

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

Com a entidade de serviço atribuída à função apropriada, você pode continuar a carregar as métricas ou os dados do usuário. 

## <a name="upload-logs-metrics-or-user-data"></a>Carregar logs, métricas ou dados do usuário

As etapas específicas para carregar logs, métricas ou dados de usuário variam de acordo com o tipo de informações que você está carregando. 

[Carregar logs para Azure Monitor](upload-logs.md)

[Carregar métricas para Azure Monitor](upload-metrics.md)

[Carregar dados de uso para Azure Monitor](upload-usage-data.md)

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Diretrizes gerais sobre como exportar e carregar o uso, métricas

As operações CRUD (criar, ler, atualizar e excluir) nos serviços de dados habilitados para o Arc do Azure são registradas para fins de cobrança e monitoramento. Há serviços em segundo plano que monitoram essas operações CRUD e calculam o consumo adequadamente. O cálculo real do uso ou do consumo ocorre de forma programada e é feito em segundo plano. 

Durante a visualização, esse processo ocorre à noite. As diretrizes gerais são carregar o uso apenas uma vez por dia. Quando as informações de uso são exportadas e carregadas várias vezes no mesmo período de 24 horas, somente o inventário de recursos é atualizado no portal do Azure, mas não no uso do recurso.

Para carregar métricas, o Azure monitor só aceita os últimos 30 minutos de dados ([saiba mais](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)). As diretrizes para carregar métricas são carregar as métricas imediatamente após criar o arquivo de exportação para que você possa exibir o conjunto de dados inteiro em portal do Azure. Por exemplo, se você exportou as métricas às 2:00 PM e executou o comando upload às 2:50 PM. Como Azure Monitor só aceita dados nos últimos 30 minutos, talvez você não veja nenhum dado no Portal. 

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre entidades de serviço](/powershell/azure/azurerm/create-azure-service-principal-azureps#what-is-a-service-principal)

[Carregar dados de cobrança no Azure e exibi-los no portal do Azure](view-billing-data-in-azure.md)

[Exibir recurso do controlador de dados de arco do Azure no portal do Azure](view-data-controller-in-azure-portal.md)
