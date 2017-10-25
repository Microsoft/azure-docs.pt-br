---
title: "Exemplos de início rápido da CLI 1.0 do Azure Monitor. | Microsoft Docs"
description: "Comandos de exemplo da CLI 1.0 para recursos do Azure Monitor. O Azure Monitor é um serviço do Microsoft Azure que permite enviar notificações de alerta ou chamar URLs da Web baseadas em valores dos dados de telemetria configurados, bem como dimensionar automaticamente Serviços de Nuvem, Máquinas Virtuais e Aplicativos Web."
author: kamathashwin
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1653aa81-0ee6-4622-9c65-d4801ed9006f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: ashwink
ms.openlocfilehash: ec4512500dc3c77a40d2ebd1e6b460d5bb005811
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-monitor--cross-platform-cli-10-quick-start-samples"></a>Exemplos de início rápido da CLI 1.0 de plataforma cruzada do Azure Monitor
Este artigo mostra um exemplo de CLI (interface de linha de comando) que ajudará você a acessar os recursos do Azure Monitor. O Azure Monitor permite que você dimensione automaticamente Serviços de Nuvem, Máquinas Virtuais e Aplicativos Web e envie notificações de alerta ou chame URLs da Web com base em valores de dados de telemetria configurados.

> [!NOTE]
> O Azure Monitor é o novo nome do que era chamado "Azure Insights" até 25 de setembro de 2016. No entanto, os namespaces e, portanto, os comandos a seguir ainda contêm os “insights”.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não tiver instalado a CLI do Azure, confira [Instalar a CLI do Azure](../cli-install-nodejs.md). Se não estiver familiarizado com a CLI do Azure, você poderá ler mais sobre ela em [Usar a CLI do Azure para Mac, Linux e Windows com o Azure Resource Manager](../xplat-cli-azure-resource-manager.md).

No Windows, instale o npm do [site do Node.js](https://nodejs.org/). Após concluir a instalação, usando o CMD.exe com os privilégios de Executar como Administrador, execute o seguinte na pasta em que o npm está instalado:

```console
npm install azure-cli --global
```

Em seguida, navegue até qualquer pasta/local desejado e digite na linha de comando:

```console
azure help
```

## <a name="log-in-to-azure"></a>Fazer logon no Azure
A primeira etapa é fazer logon na conta do Azure.

```console
azure login
```

Depois de executar esse comando, será necessário entrar usando as instruções na tela. Posteriormente, você verá sua Conta, sua TenantId e sua ID da Assinatura padrão. Todos os comandos funcionam no contexto de sua assinatura padrão.

Para listar os detalhes da sua assinatura atual, use o comando a seguir.

```console
azure account show
```

Para alterar o contexto de trabalho para uma assinatura diferente, use o comando a seguir.

```console
azure account set "subscription ID or subscription name"
```

Para usar comandos do Azure Resource Manager e do Azure Monitor, você precisa estar no modo do Azure Resource Manager.

```console
azure config mode arm
```

Para exibir uma lista de todos os comandos permitidos do Azure Monitor, execute o seguinte.

```console
azure insights
```

## <a name="view-activity-log-for-a-subscription"></a>Exibir o log de atividade para uma assinatura
Para exibir uma lista de eventos de log de atividade, execute o seguinte.

```console
azure insights logs list [options]
```

Tente o seguinte para exibir todas as opções disponíveis:

```console
azure insights logs list -help
```

Veja um exemplo para listar logs por resourceGroup

```console
azure insights logs list --resourceGroup "myrg1"
```

Exemplo para listar logs por chamador

```console
azure insights logs list --caller "myname@company.com"
```

Exemplo para listar logs por chamador em um tipo de recurso, entre datas de início e de término

```console
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Trabalhar com alertas
Você pode usar as informações na seção para trabalhar com alertas.

### <a name="get-alert-rules-in-a-resource-group"></a>Obter regras de alerta em um grupo de recursos
```console
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>Criar uma regra de alerta de métrica
```console
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-webtest-alert-rule"></a>Criar uma regra de alerta de teste na Web
```console
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>Excluir uma regra de alerta
```console
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>Perfis de log
Use as informações desta seção para trabalhar com perfis de log.

### <a name="get-a-log-profile"></a>Obter um perfil de log
```console
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>Adicionar um perfil de log sem retenção
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Remover um perfil de log
```console
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>Adicionar um perfil de log com retenção
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Adicionar um perfil de log com retenção e Hub de Eventos
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>Diagnostics
Use as informações desta seção para trabalhar com configurações de diagnóstico.

### <a name="get-a-diagnostic-setting"></a>Obter uma configuração de diagnóstico
```console
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>Desabilitar uma configuração de diagnóstico
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>Habilitar uma configuração de diagnóstico sem retenção
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>Autoscale
Use as informações desta seção para trabalhar com configurações de dimensionamento automático. Você precisa modificar estes exemplos.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Obter configurações de dimensionamento automático para um grupo de recursos
```console
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Obter configurações de dimensionamento automático por nome em um grupo de recursos
```console
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>Definir as configurações de dimensionamento automático
```console
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```
