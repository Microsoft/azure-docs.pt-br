---
title: Endereços IP usados pelo Azure Monitor
description: Exceções de firewall de servidor exigidas pelo Application Insights
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 56ff33cc0a34cb254ca88f96d69a07bc131bebf4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714027"
---
# <a name="ip-addresses-used-by-azure-monitor"></a>Endereços IP usados pelo Azure Monitor

[Azure monitor](../overview.md) usa um número de endereços IP. O Azure Monitor é composto de métricas de plataforma principal e log, além de Log Analytics e Application Insights. Talvez você precise saber esses endereços se o aplicativo ou a infraestrutura que você está monitorando estiver hospedado atrás de um firewall.

> [!NOTE]
> Embora esses endereços sejam estáticos, é possível que seja necessário alterá-los de tempos em tempos. Todo o tráfego do Application Insights representa o tráfego de saída, com exceção do monitoramento da disponibilidade e webhooks, que requerem regras de firewall de entrada.

> [!TIP]
> Você poderá usar as [tags de serviço de rede](../../virtual-network/service-tags-overview.md) do Azure para gerenciar o acesso se estiver usando grupos de segurança de rede do Azure. Se você estiver gerenciando o acesso para recursos híbridos/locais, poderá baixar as listas de endereços IP equivalentes como [arquivos JSON](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files), que são atualizados a cada semana. Para abordar todas as exceções neste artigo, você precisaria usar as marcas de serviço: `ActionGroup` , `ApplicationInsightsAvailability` e `AzureMonitor` .

Como alternativa, você também pode assinar essa página como um feed RSS, adicionando https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/azure-monitor/app/ip-addresses.md.atom ao seu leitor RSS/ATOM favorito para obter uma notificação das alterações mais recentes.


## <a name="outgoing-ports"></a>Portas de saída

Você precisa abrir algumas portas de saída no firewall do servidor para permitir que o SDK do Application Insights e/ou o Monitor de Status envie dados para o portal:

| Finalidade | URL | IP | Portas |
| --- | --- | --- | --- |
| Telemetria |dc.applicationinsights.azure.com<br/>dc.applicationinsights.microsoft.com<br/>dc.services.visualstudio.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244<br/>40.85.218.175<br/>104.211.92.54<br/>52.175.198.74<br/>51.140.6.23<br/>40.71.12.231<br/>13.69.65.22<br/>13.78.108.165<br/>13.70.72.233<br/>20.44.8.7<br/>13.86.218.248<br/>40.79.138.41<br/>52.231.18.241<br/>13.75.38.7<br/>102.133.155.50<br/>52.162.110.67<br/>191.233.204.248<br/>13.69.66.140<br/>13.77.52.29<br/>51.107.59.180<br/>40.71.12.235<br/>20.44.8.10<br/>40.71.13.169<br/>13.66.141.156<br/>40.71.13.170<br/>13.69.65.23<br/>20.44.17.0<br/>20.36.114.207 <br/>51.116.155.246 <br/>51.107.155.178 <br/>51.140.212.64 <br/>13.86.218.255 <br/>20.37.74.240 <br/>65.52.250.236 <br/>13.69.229.240 <br/>52.236.186.210<br/>52.167.107.65<br/>40.71.12.237<br/>40.78.229.32<br/>40.78.229.33<br/>51.105.67.161<br/>40.124.64.192<br/>20.44.12.194<br/>20.189.172.0<br/>13.69.106.208<br/>40.78.253.199<br/>40.78.253.198<br/>40.78.243.19 | 443 |
| Live Metrics Stream | live.applicationinsights.azure.com<br/>rt.applicationinsights.microsoft.com<br/>rt.services.visualstudio.com|23.96.28.38<br/>13.92.40.198<br/>40.112.49.101<br/>40.117.80.207<br/>157.55.177.6<br/>104.44.140.84<br/>104.215.81.124<br/>23.100.122.113| 443 |

## <a name="status-monitor"></a>Monitor de status

Configuração do Monitor de Status - necessária somente ao fazer alterações.

| Finalidade | URL | IP | Portas |
| --- | --- | --- | --- |
| Configuração |`management.core.windows.net` | |`443` |
| Configuração |`management.azure.com` | |`443` |
| Configuração |`login.windows.net` | |`443` |
| Configuração |`login.microsoftonline.com` | |`443` |
| Configuração |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| Configuração |`auth.gfx.ms` | |`443` |
| Configuração |`login.live.com` | |`443` |
| Instalação | `globalcdn.nuget.org`, `packages.nuget.org` ,`api.nuget.org/v3/index.json` `nuget.org`, `api.nuget.org`, `dc.services.vsallin.net` | |`443` |

## <a name="availability-tests"></a>Testes de disponibilidade

Esta é a lista de endereços a partir dos quais [testes da web de disponibilidade](./monitor-web-app-availability.md) são executados. Se você deseja executar testes da Web em seu aplicativo, mas o servidor Web está restrito a servir clientes específicos, você precisa permitir o tráfego de entrada dos nossos servidores de teste de disponibilidade.


> [!NOTE]
> Para recursos localizados dentro de redes virtuais privadas que não podem permitir a comunicação direta de entrada com os agentes de teste de disponibilidade no Azure público, a única opção é [criar e hospedar seus próprios testes de disponibilidade personalizados](availability-azure-functions.md).

### <a name="service-tag"></a>Marca de serviço

Caso você esteja usando grupos de segurança de rede do Azure, basta adicionar uma **regra de porta de entrada** para permitir o tráfego de testes de disponibilidade do Application Insights, selecionando **Marca de Serviço** como a **Origem** e **ApplicationInsightsAvailability** como **Marca de serviço de origem**.

>[!div class="mx-imgBorder"]
>![Em Configurações, selecione Regras de segurança de entrada e, em seguida, selecione Adicionar na parte superior da guia ](./media/ip-addresses/add-inbound-security-rule.png)

>[!div class="mx-imgBorder"]
>![Guia Adicionar regra de segurança de entrada](./media/ip-addresses/add-inbound-security-rule2.png)

Abra as portas 80 (http) e 443 (https) para o tráfego de entrada destes endereços (endereços IP são agrupados por local):

### <a name="addresses-grouped-by-location"></a>Endereços agrupados por local

> [!NOTE]
> Esses endereços são listados usando a notação CIDR (Roteamento entre Domínios sem Classificação). Isso significa que uma entrada como `51.144.56.112/28` é equivalente a 16 IPs, começando em `51.144.56.112` e terminando em `51.144.56.127`.

```
Australia East
20.40.124.176/28
20.40.124.240/28
20.40.125.80/28

Brazil South
191.233.26.176/28
191.233.26.128/28
191.233.26.64/28

France Central (Formerly France South)
20.40.129.96/28
20.40.129.112/28
20.40.129.128/28
20.40.129.144/28

France Central
20.40.129.32/28
20.40.129.48/28
20.40.129.64/28
20.40.129.80/28

East Asia
52.229.216.48/28
52.229.216.64/28
52.229.216.80/28

North Europe
52.158.28.64/28
52.158.28.80/28
52.158.28.96/28
52.158.28.112/28

Japan East
52.140.232.160/28
52.140.232.176/28
52.140.232.192/28

West Europe
51.144.56.96/28
51.144.56.112/28
51.144.56.128/28
51.144.56.144/28
51.144.56.160/28
51.144.56.176/28

UK South
51.105.9.128/28
51.105.9.144/28
51.105.9.160/28

UK West
20.40.104.96/28
20.40.104.112/28
20.40.104.128/28
20.40.104.144/28

Southeast Asia
52.139.250.96/28
52.139.250.112/28
52.139.250.128/28
52.139.250.144/28

West US
40.91.82.48/28
40.91.82.64/28
40.91.82.80/28
40.91.82.96/28
40.91.82.112/28
40.91.82.128/28

Central US
13.86.97.224/28
13.86.97.240/28
13.86.98.48/28
13.86.98.0/28
13.86.98.16/28
13.86.98.64/28

North Central US
23.100.224.16/28
23.100.224.32/28
23.100.224.48/28
23.100.224.64/28
23.100.224.80/28
23.100.224.96/28
23.100.224.112/28
23.100.225.0/28

South Central US
20.45.5.160/28
20.45.5.176/28
20.45.5.192/28
20.45.5.208/28
20.45.5.224/28
20.45.5.240/28

East US
20.42.35.32/28
20.42.35.64/28
20.42.35.80/28
20.42.35.96/28
20.42.35.112/28
20.42.35.128/28

```  

#### <a name="azure-government"></a>Azure Government

Não é necessário se você for um cliente de nuvem pública do Azure.

```
USGov Virginia
52.227.229.80/31


USGov Arizona
52.244.35.112/31


USGov Texas
52.243.157.80/31


USDoD Central
52.182.23.96/31


USDoD East
52.181.33.96/31

```

## <a name="application-insights--log-analytics-apis"></a>APIs de Application Insights e Log Analytics

| Finalidade | URI |  IP | Portas |
| --- | --- | --- | --- |
| API |`api.applicationinsights.io`<br/>`api1.applicationinsights.io`<br/>`api2.applicationinsights.io`<br/>`api3.applicationinsights.io`<br/>`api4.applicationinsights.io`<br/>`api5.applicationinsights.io`<br/>`dev.applicationinsights.io`<br/>`dev.applicationinsights.microsoft.com`<br/>`dev.aisvc.visualstudio.com`<br/>`www.applicationinsights.io`<br/>`www.applicationinsights.microsoft.com`<br/>`www.aisvc.visualstudio.com`<br/>`api.loganalytics.io`<br/>`*.api.loganalytics.io`<br/>`dev.loganalytics.io`<br>`docs.loganalytics.io`<br/>`www.loganalytics.io` |20.37.52.188 <br/> 20.37.53.231 <br/> 20.36.47.130 <br/> 20.40.124.0 <br/> 20.43.99.158 <br/> 20.43.98.234 <br/> 13.70.127.61 <br/> 40.81.58.225 <br/> 20.40.160.120 <br/> 23.101.225.155 <br/> 52.139.8.32 <br/> 13.88.230.43 <br/> 52.230.224.237 <br/> 52.242.230.209 <br/> 52.173.249.138 <br/> 52.229.218.221 <br/> 52.229.225.6 <br/> 23.100.94.221 <br/> 52.188.179.229 <br/> 52.226.151.250 <br/> 52.150.36.187 <br/> 40.121.135.131 <br/> 20.44.73.196 <br/> 20.41.49.208 <br/> 40.70.23.205 <br/> 20.40.137.91 <br/> 20.40.140.212 <br/> 40.89.189.61 <br/> 52.155.118.97 <br/> 52.156.40.142 <br/> 23.102.66.132 <br/> 52.231.111.52 <br/> 52.231.108.46 <br/> 52.231.64.72 <br/> 52.162.87.50 <br/> 23.100.228.32 <br/> 40.127.144.141 <br/> 52.155.162.238 <br/> 137.116.226.81 <br/> 52.185.215.171 <br/> 40.119.4.128 <br/> 52.171.56.178 <br/> 20.43.152.45 <br/> 20.44.192.217 <br/> 13.67.77.233 <br/> 51.104.255.249 <br/> 51.104.252.13 <br/> 51.143.165.22 <br/> 13.78.151.158 <br/> 51.105.248.23 <br/> 40.74.36.208 <br/> 40.74.59.40 <br/> 13.93.233.49 <br/> 52.247.202.90 |80.443 |
| Extensão de anotações de pipeline do Azure | aigs1.aisvc.visualstudio.com |dinâmico|443 | 

## <a name="application-insights-analytics"></a>Análise do Application Insights

| Finalidade | URI | IP | Portas |
| --- | --- | --- | --- |
| Portal da análise | analytics.applicationinsights.io | dinâmico | 80.443 |
| CDN | applicationanalytics.azureedge.net | dinâmico | 80.443 |
| Mídia CDN | applicationanalyticsmedia.azureedge.net | dinâmico | 80.443 |

Observação: o domínio *.applicationinsights.io pertence à equipe do Application Insights.

## <a name="log-analytics-portal"></a>Portal do Log Analytics

| Finalidade | URI | IP | Portas |
| --- | --- | --- | --- |
| Portal | portal.loganalytics.io | dinâmico | 80.443 |
| CDN | applicationanalytics.azureedge.net | dinâmico | 80.443 |

Observação: o domínio *.loganalytics.io pertence à equipe do Log Analytics.

## <a name="application-insights-azure-portal-extension"></a>Extensão do Portal do Azure Application Insights

| Finalidade | URI | IP | Portas |
| --- | --- | --- | --- |
| Extensão do Application Insights | stamp2.app.insightsportal.visualstudio.com | dinâmico | 80.443 |
| Extensão do Application Insights CDN | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | dinâmico | 80.443 |

## <a name="application-insights-sdks"></a>SDKs do Application Insights

| Finalidade | URI | IP | Portas |
| --- | --- | --- | --- |
| SDK do Application Insights JS CDN | az416426.vo.msecnd.net<br/>js.monitor.azure.com | dinâmico | 80.443 |

## <a name="action-group-webhooks"></a>WebHooks do grupo de ações

Você pode consultar a lista de endereços IP usados por grupos de ação usando o [comando Get-AzNetworkServiceTag do PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag).

### <a name="action-groups-service-tag"></a>Marca de serviço de grupos de ação
O gerenciamento de alterações nos endereços IP de origem pode ser muito demorado. O uso de **marcas de serviço** elimina a necessidade de atualizar sua configuração. Uma marca de serviço representa um grupo de prefixos de endereço IP de um determinado serviço do Azure. A Microsoft gerencia os endereços IP e atualiza automaticamente a marca de serviço à medida que os endereços são alterados, eliminando a necessidade de atualizar as regras de segurança de rede para um grupo de ação.

1. Na portal do Azure em pesquisa de serviços do Azure para *grupo de segurança de rede*.
2. Clique em **Adicionar** e crie um grupo de segurança de rede.

   1. Adicione o nome do grupo de recursos e insira os *detalhes da instância*.
   1. Clique em **revisão + criar** e, em seguida, clique em *criar*.
   
   :::image type="content" source="../alerts/media/action-groups/action-group-create-security-group.png" alt-text="Exemplo de como criar um grupo de segurança de rede."border="true":::

3. Vá para o grupo de recursos e clique em *grupo de segurança de rede* que você criou.

    1. Selecione *regras de segurança de entrada*.
    1. Clique em **Adicionar**.
    
    :::image type="content" source="../alerts/media/action-groups/action-group-add-service-tag.png" alt-text="Exemplo de como adicionar uma marca de serviço." border="true":::

4. Uma nova janela será aberta no painel direito.
    1.  Selecionar origem: **marca de serviço**
    1.  Marca de serviço de **origem: resourcegroup**
    1.  Clique em **Adicionar**.
    
    :::image type="content" source="../alerts/media/action-groups/action-group-service-tag.png" alt-text="Exemplo de como adicionar a marca de serviço." border="true":::


## <a name="profiler"></a>Criador de perfil

| Finalidade | URI | IP | Portas |
| --- | --- | --- | --- |
| Agente | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| Portal | gateway.azureserviceprofiler.net | dinâmico | 443
| Armazenamento | *.core.windows.net | dinâmico | 443

## <a name="snapshot-debugger"></a>Depurador de instantâneo

> [!NOTE]
> O Application Insights Profiler e o Depurador de Instantâneos compartilham o mesmo conjunto de endereços IP.

| Finalidade | URI | IP | Portas |
| --- | --- | --- | --- |
| Agente | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| Portal | gateway.azureserviceprofiler.net | dinâmico | 443
| Armazenamento | *.core.windows.net | dinâmico | 443