---
title: "Introdução à verificação de conectividade no Observador de Rede do Azure | Microsoft Docs"
description: "Esta página apresenta uma visão geral da capacidade de conectividade do Observador de Rede"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: 16ceef9c923b6a933a5caf752991b466346e0ebc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-connectivity-check-in-azure-network-watcher"></a>Introdução à verificação de conectividade no Observador de Rede do Azure

O recurso de conectividade do Observador de Rede fornece a capacidade de verificar uma conexão TCP direta de uma máquina virtual a uma VM (máquina virtual), FQDN (nome de domínio totalmente qualificado), URI ou endereço IPv4. Os cenários de rede são complexos, são implementados usando Grupos de Segurança de Rede, firewalls, rotas definidas pelo usuário e recursos fornecidos pelo Azure. Configurações complexas tornam a solução de problemas de conectividade desafiadora. O Observador de Rede ajuda a reduzir a quantidade de tempo para localizar e detectar problemas de conectividade. Os resultados retornados podem fornecer informações sobre se um problema de conectividade é devido a uma plataforma ou um problema de configuração do usuário. A conectividade pode ser verificada com o [PowerShell](network-watcher-connectivity-powershell.md), a [CLI do Azure](network-watcher-connectivity-cli.md) e a [API REST](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> A verificação de conectividade requer uma extensão de máquina virtual `AzureNetworkWatcherExtension`. Para instalar a extensão em uma VM do Windows, visite [Extensão da máquina virtual do Agente do Observador de Rede do Azure para Windows](../virtual-machines/windows/extensions-nwa.md) e para a VM do Linux, visite [Extensão da máquina virtual do Agente do Observador de Rede do Azure para Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="response"></a>Resposta

A tabela a seguir mostra as propriedades retornadas quando uma verificação de conectividade conclui a execução.

|Propriedade  |Descrição  |
|---------|---------|
|ConnectionStatus     | O status da verificação de conectividade. Os resultados possíveis são **Acessível** e **Inacessível**.        |
|AvgLatencyInMs     | Latência média durante a verificação de conectividade em milissegundos. (Exibido somente se a verificação de status estiver acessível)        |
|MinLatencyInMs     | Latência mínima durante a verificação de conectividade em milissegundos. (Exibido somente se a verificação de status estiver acessível)        |
|MaxLatencyInMs     | Latência máxima durante a verificação de conectividade em milissegundos. (Exibido somente se a verificação de status estiver acessível)        |
|ProbesSent     | Número de investigações enviadas durante a verificação. O valor máximo é de 100.        |
|ProbesFailed     | Número de investigações que falharam durante a verificação. O valor máximo é de 100.        |
|Hops     | Caminho salto a salto da origem ao destino.        |
|Hops[].Type     | Tipo de recurso. Os valores possíveis são **Source**, **VirtualAppliance**, **VnetLocal** e **Internet**.        |
|Hops[].Id | Identificador exclusivo do salto.|
|Hops[].Address | Endereço IP do salto.|
|Hops[].ResourceId | ResourceID do salto se o salto for um recurso do Azure. Se for um recurso de Internet, ResourceID será **Internet**. |
|Hops[].NextHopIds | O identificador exclusivo do próximo salto dado.|
|Hops[].Issues | Uma coleção dos problemas encontrados durante a verificação do salto. Se não houver problemas, o valor ficará em branco.|
|Hops[].Issues[].Origin | No salto atual, o ponto em que o problema ocorreu. Os valores possíveis são:<br/> **Entrada** – o problema está no link do salto anterior para o salto atual<br/>**Saída** – o problema está no link do salto atual para o próximo salto<br/>**Local** – problema está no salto atual.|
|Hops[].Issues[].Severity | A gravidade do problema detectado. Os valores possíveis são **Erro** e **Aviso**. |
|Hops[].Issues[].Type |O tipo de problema encontrado. Os valores possíveis são: <br/>**CPU**<br/>**Memória**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Hops[].Issues[].Context |Detalhes sobre o problema encontrado.|
|Hops[].Issues[].Context[].key |Chave do par chave-valor retornado.|
|Hops[].Issues[].Context[].value |Valor do par chave-valor retornado.|

O seguinte é um exemplo de um problema encontrado em um salto.

```json
"Issues": [
    {
        "Origin": "Outbound",
        "Severity": "Error",
        "Type": "NetworkSecurityRule",
        "Context": [
            {
                "key": "RuleName",
                "value": "UserRule_Port80"
            }
        ]
    }
]
```
## <a name="fault-types"></a>Tipos de Falha

A verificação de conectividade retorna tipos de falha sobre a conexão. A tabela a seguir fornece uma lista dos tipos de falhas atuais retornados.

|Tipo  |Descrição  |
|---------|---------|
|CPU     | Alta utilização da CPU.       |
|Memória     | Alta utilização de memória.       |
|GuestFirewall     | O tráfego é bloqueado devido a uma configuração de firewall da máquina virtual.        |
|DNSResolution     | Falha na resolução DNS para o endereço de destino.        |
|NetworkSecurityRule    | O tráfego é bloqueado por uma Regra NSG (a Regra é retornada)        |
|UserDefinedRoute|O tráfego é descartado devido a uma rota do sistema ou definida pelo usuário. |

### <a name="next-steps"></a>Próximas etapas

Saiba como verificar a conectividade a um recurso visitando: [verificar a conectividade com o Observador de Rede do Azure](network-watcher-connectivity-powershell.md).

<!--Image references-->
[1]: ./media/network-watcher-next-hop-overview/figure1.png

