---
title: Carregar dados de uso para Azure Monitor
description: Carregar uso dados dos serviços de dados habilitados para o Azure ARC para Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 0c72eda59f375c70274b17796ca53614ef95505b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669501"
---
# <a name="upload-usage-data-to-azure-monitor"></a>Carregar dados de uso para Azure Monitor

Periodicamente, você pode exportar informações de uso. A exportação e o upload dessas informações criam e atualizam os recursos do grupo de servidores, instância gerenciada do SQL e servidor de hiperescala do PostgreSQL no Azure.

> [!NOTE] 
> Durante o período de versão prévia, não há nenhum custo para usar os serviços de dados habilitados para Arc do Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


> [!NOTE]
> Aguarde pelo menos 24 horas depois de criar o controlador de dados de arco do Azure antes de carregar dados de uso.

## <a name="create-service-principal-and-assign-roles"></a>Criar entidade de serviço e atribuir funções

Antes de prosseguir, verifique se você criou a entidade de serviço necessária e a atribuiu a uma função apropriada. Para obter detalhes, confira:
* [Criar entidade de serviço](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [Atribuir funções à entidade de serviço](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>Carregar dados de uso

Informações de uso, como inventário e uso de recursos, podem ser carregadas no Azure na seguinte maneira de duas etapas:

1. Faça logon no controlador de dados. Insira os valores no prompt. 

   ```console
   azdata login
   ```

1. Exporte os dados de uso usando o `azdata arc dc export` comando, da seguinte maneira:

   ```console
   azdata arc dc export --type usage --path usage.json
   ```
 
   Este comando cria um `usage.json` arquivo com todos os recursos de dados habilitados para o Azure Arc, como instâncias gerenciadas do SQL e instâncias de hiperescala do PostgreSQL, etc. que são criados no controlador de dados.

2. Carregar os dados de uso usando o ```azdata upload``` comando

   ```console
   azdata arc dc upload --path usage.json
   ```

## <a name="automating-uploads-optional"></a>Automatizando carregamentos (opcional)

Se você quiser carregar as métricas e os logs de forma agendada, poderá criar um script e executá-lo em um temporizador a cada poucos minutos. Veja abaixo um exemplo de como automatizar os carregamentos usando um script de shell do Linux.

Em seu editor de texto/código favorito, adicione o script a seguir ao arquivo e salve como um arquivo executável de script, como `.sh` (Linux/Mac) ou `.cmd` , `.bat` ou `.ps1` .

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Tornar o arquivo de script executável

```console
chmod +x myuploadscript.sh
```

Executar o script a cada 20 minutos:

```console
watch -n 1200 ./myuploadscript.sh
```

Você também pode usar um Agendador de trabalho como cron ou Windows Agendador de Tarefas ou um orquestrador como Ansible, Puppet ou chefe.

## <a name="next-steps"></a>Próximas etapas

[Carregar métricas e logs para Azure Monitor](upload-metrics.md)

[Carregar logs para Azure Monitor](upload-logs.md)

[Carregar dados de cobrança no Azure e exibi-los no portal do Azure](view-billing-data-in-azure.md)

[Exibir recurso do controlador de dados de arco do Azure no portal do Azure](view-data-controller-in-azure-portal.md)
