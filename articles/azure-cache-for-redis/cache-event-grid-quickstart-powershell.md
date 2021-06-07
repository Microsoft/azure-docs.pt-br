---
title: 'Início rápido: Encaminhar eventos do Cache do Azure para Redis para o ponto de extremidade da Web com o PowerShell'
description: Use a Grade de Eventos do Azure para assinar eventos do Cache do Azure para Redis, enviar os eventos para um webhook e processar os eventos em um aplicativo Web.
ms.date: 1/5/2021
author: curib
ms.author: cauribeg
ms.topic: quickstart
ms.service: cache
ms.openlocfilehash: 6c3b433a8e433f39b723a7155bb6de116857efca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102508156"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-powershell"></a>Início rápido: Encaminhar eventos do Cache do Azure para Redis para o ponto de extremidade da Web com o PowerShell

A Grade de Eventos do Azure é um serviço de eventos para a nuvem. Neste guia de início rápido, você usará o Azure PowerShell para assinar eventos do Cache do Azure para Redis, disparar um evento e ver os resultados. 

Normalmente, você envia eventos para um ponto de extremidade que processa os dados de evento e realiza ações. No entanto, para simplificar este guia de início rápido, você enviará eventos para um aplicativo Web que coletará e exibirá as mensagens. Quando você concluir as etapas descritas neste guia de início rápido, verá que os dados do evento foram enviados ao aplicativo Web.

## <a name="setup"></a>Instalação

Este guia de início rápido exige que você esteja executando a última versão do Azure PowerShell. Se você precisa instalar ou atualizar, confira [Instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre em sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela para fazer a autenticação.

```powershell
Connect-AzAccount
```

Este exemplo usa **westus2** e armazena a seleção em uma variável para uso.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os tópicos da Grade de Eventos são implantados como recursos individuais do Azure e precisam ser provisionados em um grupo de recursos do Azure. Um grupo de recursos é uma coleção lógica na qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

O exemplo a seguir cria um grupo de recursos chamado **gridResourceGroup** no local **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-an-azure-cache-for-redis-instance"></a>Criar uma instância de Cache do Azure para Redis 

```powershell
New-AzRedisCache
   -ResourceGroupName <String>
   -Name <String>
   -Location <String>
   [-Size <String>]
   [-Sku <String>]
   [-RedisConfiguration <Hashtable>]
   [-EnableNonSslPort <Boolean>]
   [-TenantSettings <Hashtable>]
   [-ShardCount <Int32>]
   [-MinimumTlsVersion <String>]
   [-SubnetId <String>]
   [-StaticIP <String>]
   [-Tag <Hashtable>]
   [-Zone <String[]>]
   [-DefaultProfile <IAzureContextContainer>]
   [-WhatIf]
   [-Confirm]
   [<CommonParameters>]
```
Para obter mais informações sobre como criar uma instância de cache no PowerShell, confira a [referência do Azure PowerShell](/powershell/module/az.rediscache/new-azrediscache). 

## <a name="create-a-message-endpoint"></a>Criar um ponto de extremidade de mensagem

Antes de assinar o tópico, vamos criar o ponto de extremidade para a mensagem do evento. Normalmente, o ponto de extremidade executa ações com base nos dados de evento. Para simplificar este início rápido, você implanta um [aplicativo Web criado previamente](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens de eventos. A solução implantada inclui um plano do Serviço de Aplicativo, um aplicativo Web do Aplicativo do Serviço de e o código-fonte do GitHub.

Substitua `<your-site-name>` por um nome exclusivo para o aplicativo Web. O nome do aplicativo Web deve ser exclusivo, pois é parte de uma entrada DNS.

```powershell
$sitename="<your-site-name>"

New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

A implantação pode levar alguns minutos para ser concluída. Depois que a implantação for bem-sucedida, exiba seu aplicativo Web para garantir que ele esteja em execução. Em um navegador da Web, navegue até: `https://<your-site-name>.azurewebsites.net`

Você deve ver o site sem mensagens exibidas no momento.

:::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Site vazio do Visualizador da Grade de Eventos.":::

## <a name="subscribe-to-your-azure-cache-for-redis-event"></a>Assinar seu evento do Cache do Azure para Redis

Nesta etapa, você assinará um tópico para indicar à Grade de Eventos quais eventos deseja acompanhar. O exemplo a seguir assina a instância de cache que você criou e transmite a URL do seu aplicativo Web como o ponto de extremidade para notificação de eventos. O ponto de extremidade para seu aplicativo Web deve incluir o sufixo `/api/updates/`.

```powershell
$cacheId = (Get-AzRedisCache -ResourceGroupName $resourceGroup -Name $cacheName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpoint `
  -ResourceId $cacheId
```

Exiba novamente o seu aplicativo Web e observe que um evento de validação de assinatura foi enviado a ele. Selecione o ícone de olho para expandir os dados de evento. A Grade de Eventos envia o evento de validação de modo que o ponto de extremidade possa verificar se ele deseja receber os dados de evento. O aplicativo Web inclui o código para validar a assinatura.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Visualizador da Grade de Eventos do Azure.":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Disparar um evento do Cache do Azure para Redis

Agora, vamos disparar um evento para ver como a Grade de Eventos distribui a mensagem para o ponto de extremidade.

```powershell
Import-AzRedisCache
      [-ResourceGroupName <String>]
      -Name <String>
      -Files <String[]>
      [-Format <String>]
      [-Force]
      [-PassThru]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```
Para obter mais informações sobre como fazer a importação no PowerShell, confira a [referência do Azure PowerShell](/powershell/module/az.rediscache/import-azrediscache). 

Você disparou o evento, e a Grade de Eventos enviou a mensagem para o ponto de extremidade configurado durante a assinatura. Exiba seu aplicativo Web para ver o evento que você acabou de enviar.

```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ImportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ImportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ImportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>Limpar os recursos
Se você pretende continuar trabalhando com essa instância do Cache do Azure para Redis e a assinatura de evento, não limpe os recursos criados neste guia de início rápido. Caso contrário, use o comando a seguir para excluir os recursos criados neste guia de início rápido.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como criar tópicos e assinaturas de eventos, saiba mais sobre os eventos do Cache do Azure para Redis e como a Grade de Eventos pode ajudar você:

- [Como responder aos eventos do Cache do Azure para Redis](cache-event-grid.md)
- [Sobre a Grade de Eventos](../event-grid/overview.md)