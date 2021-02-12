---
title: Carregar métricas para Azure Monitor
description: Carregar métricas de serviços de dados habilitados para o Azure ARC para Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f319f912520a69a0c68f89a3d4178f63cc45ca1f
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356541"
---
# <a name="upload-metrics-to-azure-monitor"></a>Carregar métricas para Azure Monitor

Periodicamente, você pode exportar as métricas de monitoramento e carregá-las no Azure. A exportação e o upload de dados também criam e atualizam os recursos do grupo de servidores, da instância gerenciada do SQL e do servidor de hiperescala do PostgreSQL no Azure.

> [!NOTE] 
> Durante o período de versão prévia, não há nenhum custo para usar os serviços de dados habilitados para Arc do Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir, verifique se você criou a entidade de serviço necessária e a atribuiu a uma função apropriada. Para obter detalhes, confira:
* [Criar entidade de serviço](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [Atribuir funções à entidade de serviço](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-metrics"></a>Carregar métricas

Com os serviços de dados de arco do Azure, você pode, opcionalmente, carregar suas métricas para Azure Monitor para que possa agregar e analisar métricas, gerar alertas, enviar notificações ou disparar ações automatizadas. 

Enviar seus dados para o Azure Monitor também permite que você armazene dados de métricas fora do local e em grande escala, permitindo o armazenamento de longo prazo dos dados para análise avançada.

Se você tiver vários sites que têm o Azure Arc Data Services, poderá usar Azure Monitor como um local central para coletar todos os seus logs e métricas em seus sites.

## <a name="set-final-environment-variables-and-confirm"></a>Definir variáveis de ambiente finais e confirmar

Defina a URL da autoridade de SPN em uma variável de ambiente:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

Certifique-se de que todas as variáveis de ambiente necessárias estejam definidas se você quiser:


::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

::: zone pivot="client-operating-system-windows-command"

```console
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

## <a name="upload-metrics-to-azure-monitor"></a>Carregar métricas para Azure Monitor

Para carregar as métricas para suas instâncias gerenciadas SQL habilitadas do Arc do Azure e os grupos de servidores de hiperescala PostgreSQL habilitados para o Azure Arc, os seguintes comandos da CLI:

1. Faça logon no controlador de dados com `azdata` .
 
1. Exportar todas as métricas para o arquivo especificado:

   ```console
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Carregar métricas no Azure monitor:

   ```console
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Aguarde pelo menos 30 minutos depois que as instâncias de dados habilitadas para o Arc do Azure forem criadas para o primeiro carregamento.
   >
   >Verifique se `upload` as métricas estão imediatamente após `export` as Azure monitor aceita apenas as métricas dos últimos 30 minutos. [Saiba mais](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting).


Se você vir algum erro indicando "falha ao obter métricas" durante a exportação, verifique se a coleta de dados está definida como `true` executando o seguinte comando:

```console
azdata arc dc config show
```

Procure "seção de segurança"

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

Verifique se as `allowNodeMetricsCollection` `allowPodMetricsCollection` Propriedades e estão definidas como `true` .

## <a name="view-the-metrics-in-the-portal"></a>Exibir as métricas no portal

Depois que suas métricas forem carregadas, você poderá exibi-las no portal do Azure.
> [!NOTE]
> Observe que pode levar alguns minutos para que os dados carregados sejam processados antes que você possa exibir as métricas no Portal.


Para exibir suas métricas no portal, use este link para abrir o portal: <https://portal.azure.com> em seguida, pesquise a instância do banco de dados por nome na barra de pesquisa:

Você pode exibir a utilização da CPU na página Visão geral ou se quiser métricas mais detalhadas, nas quais você pode clicar em métricas no painel de navegação à esquerda

Escolha o SQL Server como o namespace de métrica:

Selecione a métrica que você deseja visualizar (você também pode selecionar várias):

Altere a frequência para os últimos 30 minutos:

> [!NOTE]
> Só é possível carregar métricas para os últimos 30 minutos. Azure Monitor rejeita as métricas com mais de 30 minutos.

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

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Diretrizes gerais sobre como exportar e carregar o uso, métricas

As operações CRUD (criar, ler, atualizar e excluir) nos serviços de dados habilitados para o Arc do Azure são registradas para fins de cobrança e monitoramento. Há serviços em segundo plano que monitoram essas operações CRUD e calculam o consumo adequadamente. O cálculo real do uso ou do consumo ocorre de forma programada e é feito em segundo plano. 

Durante a visualização, esse processo ocorre à noite. As diretrizes gerais são carregar o uso apenas uma vez por dia. Quando as informações de uso são exportadas e carregadas várias vezes no mesmo período de 24 horas, somente o inventário de recursos é atualizado no portal do Azure, mas não no uso do recurso.

Para carregar métricas, o Azure monitor só aceita os últimos 30 minutos de dados ([saiba mais](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)). As diretrizes para carregar métricas são carregar as métricas imediatamente após criar o arquivo de exportação para que você possa exibir o conjunto de dados inteiro em portal do Azure. Por exemplo, se você exportou as métricas às 2:00 PM e executou o comando upload às 2:50 PM. Como Azure Monitor só aceita dados nos últimos 30 minutos, talvez você não veja nenhum dado no Portal. 

## <a name="next-steps"></a>Próximas etapas

[Carregar logs para Azure Monitor](upload-logs.md)

[Carregar dados de uso, métricas e logs para Azure Monitor](upload-usage-data.md)

[Carregar dados de cobrança no Azure e exibi-los no portal do Azure](view-billing-data-in-azure.md)

[Exibir recurso do controlador de dados de arco do Azure no portal do Azure](view-data-controller-in-azure-portal.md)
