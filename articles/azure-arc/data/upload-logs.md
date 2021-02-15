---
title: Carregar logs para o Azure Monitor
description: Carregar logs para os serviços de dados habilitados para o Azure ARC para Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f3f29ae1ab868a96e6f70ed964f79c47bc591c4d
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92374732"
---
# <a name="upload-logs-to-azure-monitor"></a>Carregar logs para o Azure Monitor

Periodicamente, você pode exportar logs e carregá-los no Azure. A exportação e o upload de logs também cria e atualiza os recursos do grupo de servidores, instância gerenciada do SQL e servidor de hiperescala do PostgreSQL no Azure.

> [!NOTE] 
> Durante o período de versão prévia, não há nenhum custo para usar os serviços de dados habilitados para Arc do Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>Antes de começar

Antes de carregar os logs, você precisa: 

1. [Criar um espaço de trabalho do log Analytics](#create-a-log-analytics-workspace)
1. [Atribuir ID e chave compartilhada a variáveis de ambiente](#assign-id-and-shared-key-to-environment-variables)

## <a name="create-a-log-analytics-workspace"></a>Criar um espaço de trabalho do log Analytics

Para criar um espaço de trabalho do log Analytics, execute estes comandos para criar um Log Analytics espaço de trabalho e definir as informações de acesso em variáveis de ambiente.

> [!NOTE]
> Ignore esta etapa se você já tiver um espaço de trabalho.

```azurecli
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>
```

Saída de exemplo:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>Atribuir ID e chave compartilhada a variáveis de ambiente

Salve a análise do espaço de trabalho de log `customerId` como uma variável de ambiente a ser usada posteriormente:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_ID=<customerId>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID='<customerId>'
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_ID='<customerId>'
```
::: zone-end

Esse comando retorna as chaves de acesso necessárias para se conectar ao seu espaço de trabalho do log Analytics:

```azurecli
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

Saída de exemplo:

```output
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Salve a chave primária em uma variável de ambiente a ser usada mais tarde:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_SHARED_KEY=<primarySharedKey>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:WORKSPACE_SHARED_KEY='<primarySharedKey>'
```
```
::: zone-end


::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_SHARED_KEY='<primarySharedKey>'
```

::: zone-end

## <a name="set-final-environment-variables-and-confirm"></a>Definir variáveis de ambiente finais e confirmar

Defina a URL da autoridade de SPN em uma variável de ambiente:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end


## <a name="verify-environment-variables"></a>Verifique as variáveis de ambiente

Certifique-se de que todas as variáveis de ambiente necessárias estejam definidas se você quiser:


::: zone pivot="client-operating-system-windows-command"

```console
echo %WORKSPACE_ID%
echo %WORKSPACE_SHARED_KEY%
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

Com as variáveis de ambiente definidas, você pode carregar logs no espaço de trabalho de log. 

## <a name="upload-logs-to-azure-monitor"></a>Carregar logs para o Azure Monitor

 Para carregar logs para suas instâncias gerenciadas SQL habilitadas do Arc do Azure e os grupos de servidores de hiperescala PostgreSQL habilitados para AzureArc, execute os seguintes comandos da CLI-

1. Faça logon no controlador de dados de arco do Azure com [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .

   ```console
   azdata login
   ```

   Siga os prompts para definir o namespace, o nome de usuário do administrador e a senha. 

1. Exportar todos os logs para o arquivo especificado:

   ```console
   azdata arc dc export --type logs --path logs.json
   ```

2. Carregar logs em um espaço de trabalho do Azure monitor log Analytics:

   ```console
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Exibir seus logs no portal do Azure

Depois que os logs forem carregados, você poderá consultá-los usando o gerenciador de consultas de log da seguinte maneira:

1. Abra o portal do Azure e, em seguida, pesquise seu espaço de trabalho por nome na barra de pesquisa na parte superior e, em seguida, selecione-o.
2. Selecione logs no painel esquerdo.
3. Selecione introdução (ou selecione os links na página Introdução para saber mais sobre Log Analytics se você for novo).
4. Siga o tutorial para saber mais sobre Log Analytics se esta for a primeira vez que você usa Log Analytics.
5. Expanda os Logs Personalizados na parte inferior da lista de tabelas e você verá uma tabela chamada "sql_instance_logs_CL".
6. Selecione o ícone ' olho ' ao lado do nome da tabela.
7. Selecione o botão ' Exibir no editor de consultas '.
8. Agora você terá uma consulta no editor de consultas que mostrará os 10 eventos mais recentes no log.
9. A partir daqui, você pode experimentar consultando os logs ao usar o editor de consultas, definir alertas etc.

## <a name="automating-uploads-optional"></a>Automatizando carregamentos (opcional)

Se você quiser carregar as métricas e os logs de forma agendada, poderá criar um script e executá-lo em um temporizador a cada poucos minutos. Veja abaixo um exemplo de como automatizar os carregamentos usando um script de shell do Linux.

Em seu editor de texto/código favorito, adicione o script a seguir ao arquivo e salve como um arquivo executável de script, como. sh (Linux/Mac) ou. cmd,. bat,. ps1.

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

[Carregar dados de uso, métricas e logs para Azure Monitor](upload-usage-data.md)

[Carregar dados de cobrança no Azure e exibi-los no portal do Azure](view-billing-data-in-azure.md)

[Exibir recurso do controlador de dados de arco do Azure no portal do Azure](view-data-controller-in-azure-portal.md)
