---
title: Exemplos de início rápido da CLI do Azure Monitor
description: Comandos de exemplo da CLI para recursos do Azure Monitor. O Azure Monitor é um serviço do Microsoft Azure que permite enviar notificações de alerta ou chamar URLs da Web baseadas em valores dos dados de telemetria configurados, bem como dimensionar automaticamente Serviços de Nuvem, Máquinas Virtuais e Aplicativos Web.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: robb
ms.component: ''
ms.openlocfilehash: fa3293346fee6f6666db01dab5587dd760df84b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60740876"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>Exemplos de início rápido da CLI do Azure Monitor
Este artigo mostra um exemplo de CLI (interface de linha de comando) que ajudará você a acessar os recursos do Azure Monitor. O Azure Monitor permite que você dimensione automaticamente Serviços de Nuvem, Máquinas Virtuais e Aplicativos Web e envie notificações de alerta ou chame URLs da Web com base em valores de dados de telemetria configurados.

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não instalou a CLI do Azure, siga as instruções para [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Você também pode usar o [Azure Cloud Shell](/azure/cloud-shell) para executar a CLI como uma experiência interativa no navegador. Veja uma referência completa de todos os comandos disponíveis na [referência da CLI do Azure Monitor](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Fazer logon no Azure
A primeira etapa é fazer logon na conta do Azure.

```azurecli
az login
```

Depois de executar esse comando, será necessário entrar usando as instruções na tela. Todos os comandos funcionam no contexto de sua assinatura padrão.

Para listar os detalhes da sua assinatura atual, use o comando a seguir.

```azurecli
az account show
```

Para alterar o contexto de trabalho para uma assinatura diferente, use o comando a seguir.

```azurecli
az account set -s <Subscription ID or name>
```

Para exibir uma lista de todos os comandos permitidos do Azure Monitor, execute o seguinte.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Exibir o log de atividade para uma assinatura

Para exibir uma lista de eventos de log de atividade, execute o seguinte.

```azurecli
az monitor activity-log list
```

Tente o seguinte para exibir todas as opções disponíveis:

```azurecli
az monitor activity-log list -h
```

Veja um exemplo para listar logs por resourceGroup

```azurecli
az monitor activity-log list --resource-group <group name>
```

Exemplo para listar logs por chamador

```azurecli
az monitor activity-log list --caller myname@company.com
```

Exemplo para listar logs por chamador em um tipo de recurso, dentro de um intervalo de datas

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Trabalhar com alertas 
> [!NOTE]
> Apenas alertas (clássico) são suportados na CLI no momento. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Obter regras de alerta (clássico) em um grupo de recursos

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Criar uma regra de alerta (clássico) de métrica

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Deletar uma regra de alerta (clássico)

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Perfis de log

Use as informações desta seção para trabalhar com perfis de log.

### <a name="get-a-log-profile"></a>Obter um perfil de log

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Adicionar um perfil de log com retenção

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Adicionar um perfil de log com retenção e Hub de Eventos

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Remover um perfil de log

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnósticos

Use as informações desta seção para trabalhar com configurações de diagnóstico.

### <a name="get-a-diagnostic-setting"></a>Obter uma configuração de diagnóstico

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-log-setting"></a>Criar uma configuração de log de diagnóstico 

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <storage account ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

### <a name="delete-a-diagnostic-setting"></a>Excluir uma configuração de diagnóstico

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Autoscale

Use as informações desta seção para trabalhar com configurações de dimensionamento automático. Você precisa modificar estes exemplos.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Obter configurações de dimensionamento automático para um grupo de recursos

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Obter configurações de dimensionamento automático por nome em um grupo de recursos

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Definir configurações de autoescala

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```
