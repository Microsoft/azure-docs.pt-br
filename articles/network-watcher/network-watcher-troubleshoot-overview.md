---
title: Introdução à solução de problemas do recurso no Observador de Rede do Azure | Microsoft Docs
description: Essa página fornece uma visão geral das capacidades de solução de problemas do recurso do Observador de Rede
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: c1145cd6-d1cf-4770-b1cc-eaf0464cc315
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: kumud
ms.openlocfilehash: aa7fce21228d4413dc4964d6e828bf60478aee27
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60682913"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Introdução à solução de problemas do recurso no Observador de Rede do Azure

Os Gateways de Rede Virtual fornecem conectividade entre os recursos locais e outras redes virtuais no Azure. Para garantir que a comunicação não seja quebrada, é essencial monitorar os gateways e suas conexões. O Observador de Rede fornece a capacidade de solucionar problemas das conexões e dos gateways. Essa capacidade pode ser chamada pelo Portal, pelo PowerShell, pela CLI do Azure ou pela API REST. Quando chamado, o Observador de Rede diagnostica a integridade da conexão ou do gateway e retorna os resultados adequados. A solicitação é uma transação de longa execução. Os resultados são retornados quando o diagnóstico for concluído.

![portal][2]

## <a name="results"></a>Resultados

Os resultados preliminares retornados fornecem uma visão geral da integridade do recurso. Mais informações podem ser fornecidas sobre os recursos, como mostrado na seção a seguir:

A lista a seguir contém os valores retornados com a API de solução de problemas:

* **startTime** - esse valor indica a hora que a chamada da API de solução de problemas começou.
* **endTime** - esse valor indica a hora que a solução de problemas foi concluída.
* **cod** - esse valor não é apresentado como UnHealthy (não íntegro), se houver uma única falha de diagnóstico.
* **results** - é um conjunto de resultados retornados sobre a Conexão ou o gateway de rede virtual.
    * **id** - esse valor indica o tipo da falha.
    * **summary** - esse valor é um resumo da falha.
    * **detailed** - esse valor fornece uma descrição detalhada da falha.
    * **recommendedActions** - essa propriedade é um conjunto de ações recomendadas.
      * **actionText** - esse valor contém o texto que descreve a ação a ser executada.
      * **actionUri** - esse valor fornece o URI da documentação sobre como agir.
      * **actionUriText** - esse valor é uma breve descrição do texto de ação.

As tabelas a seguir mostram os diversos tipos de falha (id em resultados da lista anterior) que estão disponíveis e se a falha cria logs.

### <a name="gateway"></a>Gateway

| Tipo de Falha | Motivo | Registro|
|---|---|---|
| NoFault | Quando nenhum erro é detectado |Sim|
| GatewayNotFound | Não é possível localizar o gateway ou o gateway não está provisionado |Não |
| PlannedMaintenance |  A instância do gateway está em manutenção  |Não |
| UserDrivenUpdate | Essa falha ocorre quando uma atualização de um usuário está em andamento. A atualização pode ser uma operação de redimensionamento. | Não  |
| VipUnResponsive | Essa falha ocorre quando a instância primária do gateway não pode ser acessada devido a uma falha de investigação de integridade. | Não  |
| PlatformInActive | Há um problema com a plataforma. | Não |
| ServiceNotRunning | O serviço subjacente não está em execução. | Não |
| NoConnectionsFoundForGateway | Não existem conexões no gateway. Essa falha é apenas um aviso.| Não |
| ConnectionsNotConnected | As conexões não estão conectadas. Essa falha é apenas um aviso.| Sim|
| GatewayCPUUsageExceeded | O uso de CPU do gateway atual é > 95%. | Sim |

### <a name="connection"></a>Conexão

| Tipo de Falha | Motivo | Registro|
|---|---|---|
| NoFault | Quando nenhum erro é detectado |Sim|
| GatewayNotFound | Não é possível localizar o gateway ou o gateway não está provisionado |Não |
| PlannedMaintenance | A instância do gateway está em manutenção  |Não |
| UserDrivenUpdate | Essa falha ocorre quando uma atualização de um usuário está em andamento. A atualização pode ser uma operação de redimensionamento.  | Não  |
| VipUnResponsive | Essa falha ocorre quando a instância primária do gateway não pode ser acessada devido a uma falha de investigação de integridade. | Não  |
| ConnectionEntityNotFound | A configuração da conexão está ausente | Não  |
| ConnectionIsMarkedDisconnected | A conexão está marcada como "desconectada" |Não |
| ConnectionNotConfiguredOnGateway | O serviço subjacente não tem a conexão configurada. | Sim |
| ConnectionMarkedStandby | O serviço subjacente está marcado como em espera.| Sim|
| Authentication | Incompatibilidade de chave pré-compartilhada | Sim|
| PeerReachability | O gateway correspondente não está acessível. | Sim|
| IkePolicyMismatch | O gateway de mesmo nível tem diretivas IKE que não são suportadas pelo Azure. | Sim|
| WfpParse Error | Ocorreu um erro ao analisar o log WFP. |Sim|

## <a name="supported-gateway-types"></a>Tipos de gateway com suporte

A tabela a seguir lista quais gateways e conexões têm suporte com a solução de problemas do Observador de Rede:

|  |  |
|---------|---------|
|**Tipos de gateway**   |         |
|VPN      | Com suporte        |
|ExpressRoute | Sem suporte |
|**Tipos de VPN** | |
|Baseada em Rota | Com suporte|
|Baseada em Políticas | Sem suporte|
|**Tipos de conexão**||
|IPsec| Com suporte|
|VNet2Vnet| Com suporte|
|ExpressRoute| Sem suporte|
|VPNClient| Sem suporte|

## <a name="log-files"></a>Arquivos de log

Os arquivos de log para solução de problemas de recursos são armazenados em uma conta de armazenamento após a conclusão da solução de problemas de recursos. A imagem a seguir mostra o conteúdo de exemplo de uma chamada que resultou em um erro.

![arquivo zip][1]

> [!NOTE]
> Em alguns casos, somente um subconjunto dos arquivos de log é gravado no armazenamento.

Para obter instruções sobre como baixar os arquivos de contas de armazenamento do Azure, consulte [Introdução ao armazenamento de Blobs do Azure usando o .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que pode ser usada é o Gerenciador de armazenamento. Para saber mais sobre o Gerenciador de Armazenamento, acesse o link: [Gerenciador de Armazenamento](https://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

O arquivo **ConnectionStats.txt** contém estatísticas gerais da Conexão, incluindo bytes de entrada e saída, status da Conexão e a hora que a Conexão foi estabelecida.

> [!NOTE]
> Se a chamada para a API de solução de problemas retornar como íntegra, o único item retornado no arquivo zip será um arquivo **ConnectionStats.txt**.

O conteúdo desse arquivo é semelhante ao seguinte exemplo:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

O arquivo **CPUStats.txt** contém o uso da CPU e memória disponível no momento do teste.  O conteúdo desse arquivo é semelhante ao seguinte exemplo:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

O arquivo **IKEErrors.txt** contém erros de IKE que foram encontrados durante o monitoramento.

O exemplo a seguir mostra o conteúdo de um arquivo de IKEErrors.txt. Os erros podem ser diferentes dependendo do problema.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

O arquivo de log **Scrubbed-wfpdiag.txt** contém o log wfp. Esse log contém o registro de descarte do pacote e de falhas de IKE/AuthIP.

O exemplo a seguir mostra o conteúdo de um arquivo Scrubbed-wfpdiag.txt. Neste exemplo, a chave compartilhada de uma Conexão não está correta, como pode ser visto na terceira linha da parte inferior. O exemplo a seguir é apenas um snippet do log inteiro, já que, dependendo do problema, o log pode ser demorado.

```
...
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Deleted ICookie from the high priority thread pool list
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|IKE diagnostic event:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Event Header:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Timestamp: 1601-01-01T00:00:00.000Z
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Flags: 0x00000106
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Local address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Remote address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IP version field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP version: IPv4
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP protocol: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local address: 13.78.238.92
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote address: 52.161.24.36
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Application ID:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  User SID: <invalid>
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Failure type: IKE/Authip Main Mode Failure
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Type specific info:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure error code:0x000035e9
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IKE authentication credentials are unacceptable
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure point: Remote
...
```

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt.sum

O arquivo **wfpdiag.txt.sum** é um log que exibe buffers e eventos processados.

O exemplo a seguir é o conteúdo de um arquivo wfpdiag.txt.sum.
```
Files Processed:
    C:\Resources\directory\924336c47dd045d5a246c349b8ae57f2.GatewayTenantWorker.DiagnosticsStorage\2017-02-02T17-34-23\wfpdiag.etl
Total Buffers Processed 8
Total Events  Processed 2169
Total Events  Lost      0
Total Format  Errors    0
Total Formats Unknown   486
Elapsed Time            330 sec
+-----------------------------------------------------------------------------------+
|EventCount    EventName            EventType   TMF                                 |
+-----------------------------------------------------------------------------------+
|        36    ikeext               ike_addr_utils_c844  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        12    ikeext               ike_addr_utils_c857  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        96    ikeext               ike_addr_utils_c832  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|         6    ikeext               ike_bfe_callbacks_c133  1dc2d67f-8381-6303-e314-6c1452eeb529|
|         6    ikeext               ike_bfe_callbacks_c61  1dc2d67f-8381-6303-e314-6c1452eeb529|
|        12    ikeext               ike_sa_management_c5698  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c8447  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c494  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c642  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c3162  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c3307  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
```

## <a name="next-steps"></a>Próximos passos

Para saber como diagnosticar um problema com um gateway ou conexão de gateway, consulte [Diagnosticar problemas de comunicação entre redes](diagnose-communication-problem-between-networks.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
