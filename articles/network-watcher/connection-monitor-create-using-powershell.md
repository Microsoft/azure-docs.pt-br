---
title: Criar um monitor de conexão-PowerShell
titleSuffix: Azure Network Watcher
description: Saiba como criar um monitor de conexão usando o PowerShell.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 7f175d82b650871437a506ea4513f0ae28360f68
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99833024"
---
# <a name="create-a-connection-monitor-by-using-powershell"></a>Criar um monitor de conexão usando o PowerShell

> [!IMPORTANT]
> A partir de 1 de julho de 2021, você não poderá adicionar novos testes em um espaço de trabalho existente ou habilitar um novo espaço de trabalho no Monitor de Desempenho de Rede. Você também não poderá adicionar novos monitores de conexão no monitor de conexão (clássico). Você pode continuar a usar os testes e os monitores de conexão criados antes de 1 de julho de 2021. Para minimizar a interrupção do serviço para suas cargas de trabalho atuais, [migre seus testes de monitor de desempenho de rede ](migrate-to-connection-monitor-from-network-performance-monitor.md) ou  [migre do monitor de conexão (clássico)](migrate-to-connection-monitor-from-connection-monitor-classic.md) para o novo monitor de conexão no observador de rede do Azure antes de 29 de fevereiro de 2024.


Saiba como usar o recurso de monitor de conexão do observador de rede do Azure para monitorar a comunicação entre seus recursos.


## <a name="before-you-begin"></a>Antes de começar

Nos monitores de conexão criados com o monitor de conexão, você pode adicionar máquinas locais e VMs (máquinas virtuais) do Azure como fontes. Esses monitores de conexão também podem monitorar a conectividade com pontos de extremidade. Os pontos de extremidade podem estar no Azure ou qualquer outra URL ou IP.

Um monitor de conexão inclui as seguintes entidades:

* **Recurso de monitor de conexão**: um recurso do Azure específico à região. Todas as entidades a seguir são propriedades do recurso de monitor de conexão.
* **Ponto de extremidade**: uma origem ou um destino que participa de verificações de conectividade. Os exemplos de pontos de extremidade incluem VMs do Azure, agentes locais, URLs e IPs.
* **Configuração de teste**: uma configuração específica de protocolo para um teste. Com base no protocolo escolhido, você pode definir a porta, os limites, a frequência de teste e outros parâmetros.
* **Grupo de teste**: o grupo que contém pontos de extremidade de origem, pontos de extremidade de destino e configurações de teste. Um monitor de conexão pode conter mais de um grupo de teste.
* **Teste**: a combinação de um ponto de extremidade de origem, ponto de extremidade de destino e configuração de teste. Um teste é o nível mais granular no qual os dados de monitoramento estão disponíveis. Os dados de monitoramento incluem a porcentagem de verificações que falharam e o RTT (tempo de ida e volta).

    ![Diagrama mostrando um monitor de conexão, definindo a relação entre os grupos de teste e os testes.](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-a-connection-monitor"></a>Etapas para criar um monitor de conexão

Use os comandos a seguir para criar um monitor de conexão usando o PowerShell.

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>Descrição das propriedades

* **ConnectionMonitorName**: nome do recurso de monitor de conexão.

* **Sub**: ID da assinatura na qual você deseja criar um monitor de conexão.

* **NW**: ID de recurso do observador de rede no qual um monitor de conexão é criado.

* **Local**: região em que um monitor de conexão é criado.

* **Pontos de extremidade**
    * **Nome**: nome exclusivo para cada ponto de extremidade.
    * **ID do recurso**: para pontos de extremidade do Azure, a ID de recurso refere-se à ID de recurso Azure Resource Manager para VMs. Para pontos de extremidade não Azure, a ID de recurso refere-se à ID de recurso Azure Resource Manager para o espaço de trabalho Log Analytics vinculado a agentes não Azure.
    * **Endereço**: aplicável somente quando a ID do recurso não é especificada ou se a ID do recurso está no espaço de trabalho log Analytics. Se usado sem uma ID de recurso, pode ser a URL ou o IP de qualquer ponto de extremidade público. Se usado com uma ID de recurso Log Analytics, isso se refere ao FQDN do agente de monitoramento.
    * **Filtro**: para pontos de extremidade não Azure, use filtros para selecionar agentes de monitoramento no espaço de trabalho log Analytics no recurso de monitor de conexão. Se os filtros não estiverem definidos, todos os agentes pertencentes ao espaço de trabalho Log Analytics poderão ser usados para monitoramento.
        * **Tipo**: Defina como **endereço do agente**.
        * **Endereço**: definido como o FQDN do seu agente local.

* **Grupos de teste**
    * **Nome**: nomeie seu grupo de teste.
    * **Fontes**: escolha entre os pontos de extremidade que você criou anteriormente. Os pontos de extremidade de origem baseados no Azure precisam ter a extensão do observador de rede do Azure instalada; pontos de extremidade de origem não baseados no Azure precisam ter um agente de Log Analytics do Azure instalado. Para instalar um agente para sua origem, consulte [instalar agentes de monitoramento](./connection-monitor-overview.md#install-monitoring-agents).
    * **Destinos**: escolha entre os pontos de extremidade que você criou anteriormente. Você pode monitorar a conectividade com as VMs do Azure ou qualquer ponto de extremidade (um IP público, uma URL ou um FQDN) especificando-os como destinos. Em um único grupo de teste, você pode adicionar VMs do Azure, URLs do Office 365, URLs do Dynamics 365 e pontos de extremidade personalizados.
    * **Desabilitar**: desabilita o monitoramento de todas as origens e destinos que o grupo de teste especifica.

* **Configurações de teste**
    * **Nome**: nomear a configuração de teste.
    * **TestFrequencySec**: especifique com que frequência as fontes de ping são destinos no protocolo e na porta que você especificou. Você pode escolher 30 segundos, 1 minuto, 5 minutos, 15 minutos ou 30 minutos. As fontes testam a conectividade a destinos com base no valor escolhido. Por exemplo, se você selecionar 30 segundos, as origens verificarão a conectividade com o destino pelo menos uma vez em um período de 30 segundos.
    * **Protocolo**: escolha TCP, ICMP, http ou HTTPS. Dependendo do protocolo, você também pode selecionar as seguintes configurações específicas de protocolo:
        * **preferHTTPS**: especifique se deseja usar HTTPS sobre http.
        * **porta**: Especifique a porta de destino de sua escolha.
        * **disableTraceRoute**: impedir que as fontes descubram a topologia e o RTT de salto a salto. Isso se aplica a grupos de teste com TCP ou ICMP.
        * **método**: selecione o método de solicitação HTTP (get ou post). Isso se aplica a configurações de teste com HTTP.
        * **caminho**: Especifique os parâmetros de caminho a serem acrescentados à URL.
        * **validStatusCodes**: escolha os códigos de status aplicáveis. Se o código de resposta não corresponder, uma mensagem de diagnóstico será exibida.
        * **requestHeaders**: especifique cadeias de caracteres de cabeçalho de solicitação personalizadas que passem para o destino.
    * **Limite de êxito**: defina limites nos seguintes parâmetros de rede:
        * **checksFailedPercent**: defina a porcentagem de verificações que podem falhar quando as origens verificarem a conectividade com os destinos usando os critérios que você especificou. Para o protocolo TCP ou ICMP, a porcentagem de verificações com falha pode ser igual à porcentagem de perda de pacotes. Para o protocolo HTTP, esse campo representa o percentual de solicitações HTTP que não receberam resposta.
        * **roundTripTimeMs**: defina quanto tempo as fontes podem levar para se conectar ao destino pela configuração de teste em milissegundos.

## <a name="scale-limits"></a> Limites de escala

Os monitores de conexão têm os seguintes limites de escala:

* Máximo de monitores de conexão por assinatura por região: 100
* Máximo de grupos de teste por monitor de conexão: 20
* Máximo de origens e destinos por monitor de conexão: 100
* Configurações de teste máximas por monitor de conexão: 20

## <a name="next-steps"></a>Próximas etapas

* Saiba [como analisar dados de monitoramento e definir alertas](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts).
* Saiba [como diagnosticar problemas em sua rede](./connection-monitor-overview.md#diagnose-issues-in-your-network).
