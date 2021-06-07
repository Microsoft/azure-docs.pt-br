---
title: 'Tutorial: Solucionar problemas com o Azure Monitor'
description: Saiba como o Azure Monitor e o Log Analytics ajudam a monitorar seu aplicativo Web do Serviço de Aplicativo. O Azure Monitor maximiza a disponibilidade entregando uma solução abrangente para monitorar seus ambientes.
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 06/20/2020
ms.openlocfilehash: d45a8b8f426df32b9f5ac6f64237107083e0f9ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586282"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>Tutorial: Solucionar problemas de um aplicativo do Serviço de Aplicativo com o Azure Monitor

> [!NOTE]
> A integração do Azure Monitor ao Serviço de Aplicativo está em [versão prévia](https://aka.ms/appsvcblog-azmon).
>

Este tutorial mostra como solucionar problemas de um aplicativo do [Serviço de Aplicativo](overview.md) usando o [Azure Monitor](../azure-monitor/overview.md). O aplicativo de exemplo inclui o código destinado a esgotar a memória e causar erros HTTP 500 para que você possa diagnosticar e corrigir o problema usando o Azure Monitor. Quando tiver terminado, você terá um aplicativo de exemplo em execução no Serviço de Aplicativo no Linux integrado ao [Azure Monitor](../azure-monitor/overview.md).

O [Azure Monitor](../azure-monitor/overview.md) maximiza a disponibilidade e o desempenho de seus aplicativos e serviços fornecendo uma solução abrangente para coletar, analisar e agir em relação a dados telemétricos de seus ambientes locais e de nuvem.

Neste tutorial, você aprende a:

> [!div class="checklist"]
> * Configurar um aplicativo Web com o Azure Monitor
> * Enviar logs do console para o Log Analytics
> * Usar consultas de log para identificar e solucionar problemas de erros do aplicativo Web

Você pode seguir as etapas deste tutorial no macOS, no Linux e no Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará de:

- [Assinatura do Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [CLI do Azure](/cli/azure/install-azure-cli)
- [Git](https://git-scm.com/)

## <a name="create-azure-resources"></a>Criar recursos do Azure

Primeiro, você executa vários comandos localmente para configurar um aplicativo de exemplo para usar com este tutorial. Os comandos clonam um aplicativo de exemplo, criam recursos do Azure, criam um usuário de implantação e implantam o aplicativo no Azure. Você será solicitado pela senha que foi fornecida como parte da criação do usuário de implantação. 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure main
```

## <a name="configure-azure-monitor-preview"></a>Configurar o Azure Monitor (versão prévia)

### <a name="create-a-log-analytics-workspace"></a>Criar um workspace do Log Analytics

Agora que já implantou o aplicativo de exemplo no Serviço de Aplicativo do Azure, você configurará a funcionalidade de monitoramento para solucionar problemas do aplicativo, quando surgirem. O Azure Monitor armazena dados de log em um workspace do Log Analytics. Um workspace é um contêiner que inclui dados e informações de configuração.

Nesta etapa, você criará um workspace do Log Analytics para configurar o Azure Monitor com seu aplicativo.

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [No Log Analytics do Azure Monitor, você paga pela ingestão e pela retenção de dados.](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>Criar uma configuração de diagnóstico

As configurações de diagnóstico podem ser usadas para coletar métricas de determinados serviços do Azure em logs do Azure Monitor para análise com outros dados de monitoramento usando consultas de log. Neste tutorial, você habilitará o servidor Web e os logs de saída/erro padrão. Confira [tipos de log compatíveis](./troubleshoot-diagnostic-logs.md#supported-log-types) para obter uma lista completa de tipos de log e descrições.

Execute os seguintes comandos para criar configurações de diagnóstico para AppServiceConsoleLogs (saída/erro padrão) e AppServiceHTTPLogs (logs de servidor Web). Substitua _\<app-name>_ e _\<workspace-name>_ por seus valores. 

> [!NOTE]
> Os dois primeiros comandos, `resourceID` e `workspaceID`, são variáveis a serem usadas no comando `az monitor diagnostic-settings create`. Confira [Criar configurações de diagnóstico usando a CLI do Azure](../azure-monitor/essentials/diagnostic-settings.md#create-using-azure-cli) para obter mais informações sobre este comando.
>

```bash
resourceID=$(az webapp show -g myResourceGroup -n <app-name> --query id --output tsv)

workspaceID=$(az monitor log-analytics workspace show -g myResourceGroup --workspace-name <workspace-name> --query id --output tsv)

az monitor diagnostic-settings create --resource $resourceID \
 --workspace $workspaceID \
 -n myMonitorLogs \
 --logs '[{"category": "AppServiceConsoleLogs", "enabled": true},
  {"category": "AppServiceHTTPLogs", "enabled": true}]'
```

## <a name="troubleshoot-the-app"></a>Solucionar problemas do aplicativo

Navegue até `http://<app-name>.azurewebsites.net`.

O aplicativo de exemplo, ImageConverter, converte as imagens incluídas de `JPG` para `PNG`. Um bug foi intencionalmente colocado no código para este tutorial. Se você selecionar imagens suficientes, o aplicativo produzirá um erro HTTP 500 durante a conversão de imagem. Imagine que esse cenário não foi considerado durante a fase de desenvolvimento. Você usará Azure Monitor para solucionar o erro.

### <a name="verify-the-app-is-works"></a>Verificar se o aplicativo está funcionando

Para converter imagens, clique em `Tools` e selecione `Convert to PNG`.

![Clique em "Ferramentas" e selecione "Converter para PNG"](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

Selecione as duas primeiras imagens e clique em `convert`. Elas serão convertidas com êxito.

![Selecionar as duas primeiras imagens](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>Interromper o aplicativo

Agora que você verificou o aplicativo convertendo duas imagens com êxito, tentaremos converter as primeiras cinco imagens.

![Converter as primeiras cinco imagens](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

Essa ação falha e produz um erro `HTTP 500` que não foi testado durante o desenvolvimento.

![A conversão resultará em um erro HTTP 500](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>Usar a consulta de logs para exibir logs do Azure Monitor

Vejamos quais logs estão disponíveis no workspace do Log Analytics. 

Clique neste [link do workspace do Log Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) para acessar seu workspace no portal do Azure.

No Portal do Azure, selecione o espaço de trabalho do Log Analytics.

### <a name="log-queries"></a>Consultas de logs

As consultas de log ajudam você a aproveitar tudo o que os dados coletados nos logs do Azure Monitor têm a oferecer. Use as consultas de log para identificar os logs em AppServiceHTTPLogs e AppServiceConsoleLogs. Confira a [visão geral da consulta de log](../azure-monitor/logs/log-query-overview.md) para obter mais informações sobre consultas de log.

### <a name="view-appservicehttplogs-with-log-query"></a>Exibir AppServiceHTTPLogs com a consulta de log

Agora que acessamos o aplicativo, vamos exibir os dados associados às solicitações HTTP, encontrados no `AppServiceHTTPLogs`.

1. Clique em `Logs` na navegação à esquerda.

![Logs do workspace do Log Anlytics](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. Procure `appservice` e clique duas vezes em `AppServiceHTTPLogs`.

![Tabelas do workspace do Log Analytics](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. Clique em `Run`.

![Logs em HTTP do Serviço de Aplicativo do workspace do Log Analytics](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

A consulta `AppServiceHTTPLogs` retorna todas as solicitações nas últimas 24 horas. A coluna `ScStatus` contém o status HTTP. Para diagnosticar os erros `HTTP 500`, limite o `ScStatus` a 500 e execute a consulta, conforme mostrado abaixo:

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>Exibir AppServiceConsoleLogs com a consulta de log

Agora que você confirmou os erros 500 em HTTP, vamos dar uma olhada na saída/erros padrão do aplicativo. Esses logs são encontrados em "AppServiceConsoleLogs".

(1) Clique em `+` para criar uma consulta. 

(2) Clique duas vezes na tabela `AppServiceConsoleLogs` e clique em `Run`. 

Como a conversão de cinco imagens resulta em erros de servidor, você pode ver se o aplicativo também está gravando erros filtrando `ResultDescription` para ver se há erros, como mostrado abaixo:

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

Na coluna `ResultDescription`, você verá o seguinte erro:

```output
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>Unir AppServiceHTTPLogs e AppServiceConsoleLogs

Agora que você identificou os erros HTTP 500 e padrão, precisa confirmar se há uma correlação entre essas mensagens. Em seguida, você une as tabelas com base no carimbo de data/hora, `TimeGenerated`.

> [!NOTE]
> Uma consulta que faz o seguinte foi preparada para você:
>
> - Filtra HTTPLogs quanto a erros 500
> - Consulta logs do console
> - Une as tabelas em `TimeGenerated`
>

Execute a seguinte consulta:

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

Na coluna `ResultDescription`, você verá o seguinte erro ao mesmo tempo que erros do servidor Web:

```output
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

A mensagem declara que a memória foi esgotada na linha 20 de `process.php`. Agora você confirmou que o aplicativo produziu um erro durante o erro HTTP 500. Vamos dar uma olhada no código para identificar o problema.

## <a name="identify-the-error"></a>Identificar o erro

No diretório local, abra o `process.php` e examine a linha 20. 

```php
imagepng($imgArray[$x], $filename);
```

O primeiro argumento, `$imgArray[$x]`, é uma variável que mantém todas as JPGs (na memória) que precisam de conversão. No entanto, `imagepng` só precisa que a imagem seja convertida e não todas as imagens. O pré-carregamento das imagens não é necessário e podem estar causando o esgotamento de memória, levando aos erros HTTP 500. Vamos atualizar o código para carregar imagens sob demanda para ver se ele resolve o problema. Em seguida, você aprimorará o código para resolver o problema de memória.

## <a name="fix-the-app"></a>Corrigir o aplicativo

### <a name="update-locally-and-redeploy-the-code"></a>Atualizar localmente e reimplantar o código

Você faz as seguintes alterações em `process.php` para lidar com o esgotamento de memória:

```php
<?php

//Retrieve query parameters
$maxImages = $_GET['images'];
$imgNames  = explode(",",$_GET['imgNames']);

//Load JPEGs into an array (in memory)
for ($x=0; $x<$maxImages; $x++){
    $filename = './images/converted_' . substr($imgNames[$x],0,-4) . '.png';
    imagepng(imagecreatefromjpeg("./images/" . $imgNames[$x]), $filename);
}
```

Confirme suas alterações no Git e, em seguida, envie as alterações de código por push para o Azure.

```bash
git commit -am "Load images on-demand in process.php"
git push azure main
```

### <a name="browse-to-the-azure-app"></a>Navegar até o aplicativo do Azure

Navegue até `http://<app-name>.azurewebsites.net`. 

A conversão de imagens já não deve mais produzir os erros HTTP 500.

![Aplicativo PHP em execução no Serviço de Aplicativo do Azure](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

Exclua a configuração de diagnóstico com o seguinte comando:

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
O que você aprendeu:

> [!div class="checklist"]
> * Configurou um aplicativo Web com Azure Monitor
> * Enviou logs ao Log Analytics
> * Usou consultas de log para identificar e solucionar problemas de erros do aplicativo Web

## <a name="next-steps"></a><a name="nextsteps"></a> Próximas etapas
* [Consultar logs com o Azure Monitor](../azure-monitor/logs/log-query-overview.md)
* [Solucionar problemas de Serviço de Aplicativo do Azure no Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analisar logs de aplicativos no HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)