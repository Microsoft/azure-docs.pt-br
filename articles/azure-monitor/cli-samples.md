---
title: Exemplos de CLI do Azure Monitor
description: Comandos de exemplo da CLI para recursos do Azure Monitor. O Azure Monitor é um serviço do Microsoft Azure que permite enviar notificações de alerta ou chamar URLs da Web baseadas em valores dos dados de telemetria configurados, bem como dimensionar automaticamente Serviços de Nuvem, Máquinas Virtuais e Aplicativos Web.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/16/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8eaf8c2e140f0b323db0c20a2e9946884c51df04
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039165"
---
# <a name="azure-monitor-cli-samples"></a>Exemplos de CLI do Azure Monitor
Este artigo mostra um exemplo de CLI (interface de linha de comando) que ajudará você a acessar os recursos do Azure Monitor. O Azure Monitor permite que você dimensione automaticamente Serviços de Nuvem, Máquinas Virtuais e Aplicativos Web e envie notificações de alerta ou chame URLs da Web com base em valores de dados de telemetria configurados.

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não instalou a CLI do Azure, siga as instruções para [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Você também pode usar o [Azure Cloud Shell](/azure/cloud-shell) para executar a CLI como uma experiência interativa no navegador. Veja uma referência completa de todos os comandos disponíveis na [referência da CLI do Azure Monitor](/cli/azure/monitor). 

## <a name="log-in-to-azure"></a>Fazer logon no Azure
A primeira etapa é fazer logon na conta do Azure.

```azurecli
az login
```

Depois de executar esse comando, será necessário entrar usando as instruções na tela. Todos os comandos funcionam no contexto de sua assinatura padrão.

Listar os detalhes da sua assinatura atual.

```azurecli
az account show
```

Alterar o contexto de trabalho para uma assinatura diferente.

```azurecli
az account set -s <Subscription ID or name>
```

Exibir uma lista de todos os comandos do Azure Monitor com suporte.

```azurecli
az monitor -h
```

## <a name="view-activity-log"></a>Exibir log de atividades

Exibir uma lista de eventos do log de atividades.

```azurecli
az monitor activity-log list
```

Veja todas as opções disponíveis.

```azurecli
az monitor activity-log list -h
```

Listar logs por um resourceGroup.

```azurecli
az monitor activity-log list --resource-group <group name>
```

Listar logs por chamador.

```azurecli
az monitor activity-log list --caller myname@company.com
```

Listar logs por chamador em um tipo de recurso, dentro de um intervalo de datas.

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

### <a name="create-a-diagnostic-setting"></a>Criar uma configuração de diagnóstico 

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
