---
title: Usar investigações de integridade do Azure Load Balancer para dimensionar e fornecer alta disponibilidade para seu serviço
titlesuffix: Azure Load Balancer
description: Saiba como usar as investigações de integridade para monitorar instâncias atrás do Load Balancer
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2018
ms.author: kumud
ms.openlocfilehash: 913693e684ba8640a93f50d21dd3df6a6295e1c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60884751"
---
# <a name="load-balancer-health-probes"></a>Investigações de integridade do Load Balancer

O Azure Load Balancer fornece investigações de integridade para uso com regras de balanceamento de carga.  As respostas da investigação e da configuração da investigação de integridade determinam quais instâncias do pool de back-end receberão novos fluxos. É possível usar investigações de integridade para detectar a falha de um aplicativo em uma instância de back-end. Você também pode gerar uma resposta personalizada para uma investigação de integridade e usar a investigação de integridade para controle de fluxo, a fim de gerenciar o tempo de inatividade planejado ou de carga. Quando uma investigação de integridade falha, o Load Balancer para de enviar novos fluxos à respectiva instância não íntegra.

As investigações de integridade dão suporte a vários protocolos. A disponibilidade de um tipo específico de investigação de integridade para dar suporte a um protocolo específico varia de acordo com o SKU do Load Balancer.  Além disso, o comportamento do serviço varia de acordo com o SKU do Load Balancer.

| | SKU Standard | SKU Básico |
| --- | --- | --- |
| [Tipos de investigações](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Comportamento de investigação inoperante](#probedown) | Todas as investigações inoperantes, todos os fluxos TCP continuam. | Todas as investigações inoperantes, todos os fluxos TCP terminam. | 

> [!IMPORTANT]
> As investigações de integridade do Load Balancer originam-se no endereço IP 168.63.129.16 e não devem ser bloqueadas para que as investigações marquem a instância como operante.  Revisar o [endereço IP de origem da investigação](#probesource) para obter detalhes.

## <a name="types"></a>Tipos de investigações

A investigação de integridade pode ser configurada para ouvintes usando os três seguintes protocolos:

- [Ouvintes TCP](#tcpprobe)
- [Pontos de extremidade HTTP](#httpprobe)
- [Pontos de Extremidade HTTPS](#httpsprobe)

Os tipos de investigações de integridade disponíveis variam dependendo do SKU do Load Balancer selecionado:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| SKU Standard |    &#9989; |   &#9989; |   &#9989; |
| SKU Básico |   &#9989; |   &#9989; | &#10060; |

Seja qual for o tipo de investigação escolhido, as investigações de integridade podem observar qualquer porta em uma instância de back-end, incluindo a porta na qual o serviço real é fornecido.

### <a name="tcpprobe"></a>Investigação TCP

As investigações TCP iniciam uma conexão executando um handshake TCP aberto de três vias com a porta definida.  As investigações TCP encerram uma conexão com um handshake TCP fechado de quatro vias.

O intervalo mínimo de investigação é de 5 segundos e o número mínimo de respostas não íntegras é 2.  A duração total de todos os intervalos não pode exceder 120 segundos.

Uma investigação TCP falha quando:
* O ouvinte TCP na instância não responde durante o período de tempo limite.  Uma investigação é marcada como inoperante dependendo do número de solicitações de investigação com falha, as quais foram configuradas para passar sem resposta antes da marcação da investigação como inoperante.
* A investigação recebe uma redefinição de TCP da instância.

#### <a name="resource-manager-template"></a>Modelo do Resource Manager

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="httpprobe"></a> <a name="httpsprobe"></a> Investigação HTTP / HTTPS

> [!NOTE]
> Investigação HTTPS está disponível somente para [Standard Load Balancer](load-balancer-standard-overview.md).

As investigações HTTP e HTTPS se baseiam na investigação TCP e emitem um HTTP GET com o caminho especificado. Ambas essas investigações dão suporte a caminhos relativos para o HTTP GET. Investigações HTTPS são iguais às investigações HTTP com a adição de um wrapper de TLS (Transport Layer Security), anteriormente conhecido como SSL. A investigação de integridade é marcada como operante quando a instância responde com um status HTTP 200 dentro do período de tempo limite.  A investigação de integridade tenta verificar a porta de investigação de integridade configurada a cada 15 segundos, por padrão. O intervalo mínimo de investigação é de 5 segundos. A duração total de todos os intervalos não pode exceder 120 segundos.

As investigações HTTP/HTTPS também podem ser úteis se você deseja expressar a investigação de integridade.  implemente sua própria lógica para remover instâncias da rotação do balanceador de carga se a porta de investigação também é o ouvinte para o próprio serviço. Por exemplo, é recomendável remover uma instância caso ela esteja usando mais de 90% da CPU e retorne um status HTTP diferente de 200. 

Se você usar o Serviços de Nuvem e tiver funções web que usem w3wp.exe, também é possível obter o monitoramento automático do site. Falhas no código do site retornam um status não 200 para a investigação do balanceador de carga.

Uma investigação HTTP / HTTPS falha quando:
* O ponto de extremidade da investigação retorna um código de resposta HTTP diferente de 200 (por exemplo, 403, 404 ou 500). Isso marcará imediatamente a investigação de integridade como inoperante. 
* O ponto de extremidade de investigação não responde de modo algum durante um período de tempo limite de 31 segundos. Várias solicitações de investigação podem ficar sem resposta antes que a investigação seja marcada como não estando em execução e até que a soma de todos os intervalos de tempo limite seja atingido.
* O ponto de extremidade de investigação fecha a conexão por meio de uma redefinição de TCP.

#### <a name="resource-manager-templates"></a>Modelos do Gerenciador de Recursos

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guestagent"></a>Investigação de agente convidado (somente clássico)

Por padrão, as funções do serviço de nuvem (funções de trabalho e funções web) usam um agente convidado para o monitoramento de investigação.  Uma investigação de agente convidado é uma configuração de último recurso.  Sempre use uma investigação de integridade explicitamente com uma investigação TCP ou HTTP. Uma investigação de agente convidado não é tão eficiente quanto a investigações definidas explicitamente para a maioria dos cenários de aplicativo.

Uma investigação de agente convidado é uma verificação do agente convidado dentro da VM. Em seguida, ele escuta e responde com uma resposta HTTP 200 OK somente quando a instância está no estado Pronto. (Outros estados são Ocupado, Reciclando ou Parando.)

Para obter mais informações, consulte [Configurar o arquivo de definição de serviço (csdef) para investigações de integridade](https://msdn.microsoft.com/library/azure/ee758710.aspx) ou [Introdução à criação de um balanceador de carga público para serviços de nuvem](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Se o agente convidado não responder com HTTP 200 OK, o balanceador de carga marcará a instância como sem resposta. Em seguida, ele para de enviar fluxos a essa instância. O balanceador de carga continua a verificar a instância. 

Se o agente convidado responder com um HTTP 200, o balanceador de carga enviará o tráfego para essa instância novamente.

Quando você usa uma função Web, o código do site geralmente é executado em w3wp.exe, que não é monitorado nem pela malha do Azure nem pelo agente convidado. Falhas em w3wp.exe (por exemplo, respostas HTTP 500) não são relatadas para o agente convidado. Consequentemente, o balanceador de carga não perde essa instância.

<a name="health"></a>
## <a name="probehealth"></a>Integridade da investigação

As investigações de integridade TCP, HTTP e HTTPS são consideradas íntegras e marcam a instância de função como íntegra quando:

* A investigação de integridade é bem-sucedida uma vez após a inicialização da VM.
* O número especificado de investigações necessárias para marcar a instância de função como íntegra foi alcançado.

> [!NOTE]
> Se a investigação de integridade for flutuante, o balanceador de carga aguardará um período maior até recolocar a instância de função no estado íntegro. Esse tempo de espera extra protege o usuário e a infraestrutura, e é uma política intencional.

## <a name="probe-count-and-timeout"></a>Contagem e tempo limite de investigação

O comportamento de investigação depende do seguinte:

* Número de investigações bem-sucedidas que permitem a uma instância ser marcada como operante.
* Número de investigações com falha que fazem com que uma instância seja marcada como inoperante.

Os valores de tempo limite e de intervalo especificados determinam se uma instância é marcada como operante ou inoperante.

## <a name="probedown"></a>Comportamento de investigação inoperante

### <a name="tcp-connections"></a>Conexões TCP

As novas conexões TCP terão êxito nas instâncias de back-end íntegras restantes.

Se a investigação de integridade de uma instância de back-end falhar, as conexões TCP estabelecidas para essa instância de back-end continuarão.

Se todas as investigações para todas as instâncias em um pool de back-end falharem, nenhum novo fluxo será enviado ao pool de back-end. O Load Balancer Standard permitirá a continuação dos fluxos TCP estabelecidos.  O Basic Load Balancer terminará todos os fluxos TCP existentes para o pool de back-end.
 
O Load Balancer é um serviço de passagem (não encerra conexões TCP) e o fluxo ocorre sempre entre o cliente e o sistema operacional convidado e o aplicativo da VM. Um pool com todas as investigações inoperantes fará com que um front-end não responda às tentativas abertas de conexão TCP (SYN), pois não há nenhuma instância de back-end íntegra para receber o fluxo e responder com um SYN-ACK.

### <a name="udp-datagrams"></a>Datagramas UDP

Datagramas UDP serão entregues para as instâncias de back-end íntegras.

UDP é sem conexão e não há estado de fluxo controlado para UDP. Se a investigação de integridade de qualquer instância de back-end falhar, os fluxos UDP existentes podem ser movidos para outra instância íntegra no pool de back-end.

Se todas as investigações para todas as instâncias em um pool de back-end falharem, os fluxos UDP existentes serão encerrados para os Basic e Standard Load Balancers.

<a name="source"></a>
## <a name="probesource"></a>Endereço IP de origem da investigação

O Load Balancer usa um serviço de investigação distribuído para seu modelo de integridade interno. O serviço de investigação reside em cada host no qual as VMs e podem ser programadas sob demanda para gerar investigações de integridade de acordo com a configuração do cliente. O tráfego da investigação de integridade está diretamente entre o serviço de investigação que gera a investigação de integridade e a VM do cliente. Todas as investigações de integridade do Load Balancer originam-se do endereço IP 168.63.129.16 como sua fonte.  Use o espaço de endereços IP em uma VNET que não seja o espaço RFC1918.  O uso de um endereço IP globalmente reservado e de propriedade da Microsoft reduz a possibilidade de um conflito de endereços IP com o espaço de endereços IP utilizado na VNET.  Esse endereço IP é o mesmo em todas as regiões e não é alterado nem representa um risco à segurança porque apenas o componente interno da plataforma Azure pode obter um pacote desse endereço IP. 

A marca de serviço AzureLoadBalancer identifica esse endereço IP de origem nos [grupos de segurança de rede](../virtual-network/security-overview.md) e permite o tráfego da investigação de integridade por padrão.

Além de investigações de integridade do Load Balancer, as operações a seguir usam este endereço IP:

- Permite que o Agente de VM se comunique com a plataforma para sinalizar que ele está em um estado "Pronto"
- Permite a comunicação com o servidor virtual de DNS para fornecer resolução de nome filtrado aos clientes que não definem servidores DNS personalizados.  Essa filtragem garante que cada cliente só possa resolver os nomes de host de sua própria implantação.
- Permite que a VM obtenha um endereço IP dinâmico do serviço DHCP no Azure.

## <a name="design"></a> Diretrizes de design

As investigações de integridade são usadas para manter seu serviço resiliente e permitir que ele seja dimensionado. Uma configuração incorreta ou um padrão de design inadequado pode afetar a disponibilidade e a escalabilidade do serviço. Leia este documento na íntegra e considere qual é o impacto no seu cenário quando a resposta dessa investigação é marcada como inoperante ou operante e como isso afeta a disponibilidade do cenário de aplicativo.

Ao projetar o modelo de integridade para seu aplicativo, você deverá investigar uma porta em uma instância de back-end que reflita a integridade dessa instância __e__ o serviço de aplicativo que você está fornecendo.  A porta do aplicativo e a porta de investigação não precisam ser as mesmas.  Em alguns cenários, pode ser desejável que a porta de investigação seja diferente da porta na qual o aplicativo fornece o serviço.  

Às vezes, pode ser útil para seu aplicativo gerar uma resposta de investigação de integridade para não apenas detectar a integridade do aplicativo, mas também sinalizar diretamente para o Load Balancer se a instância deve receber ou não novos fluxos.  Você pode manipular a resposta de investigação para permitir que seu aplicativo crie a pressão de retorno e limite a entrega de novos fluxos para uma instância que falhou na investigação de integridade ou preparar a manutenção do aplicativo e iniciar a drenagem do cenário.  Ao usar o Standard Load Balancer, um sinal de [investigação inoperante](#probedown) sempre permitirá a continuidade dos fluxos TCP até o tempo limite ocioso ou o fechamento da conexão. 

Para o balanceamento de carga UDP, é necessário gerar um sinal de investigação de integridade personalizado da instância de back-end e usar uma investigação de integridade TCP, HTTP ou HTTPS direcionada ao ouvinte correspondente para refletir a integridade do aplicativo UDP.

Ao usar as [regras de balanceamento de carga das Portas HA](load-balancer-ha-ports-overview.md) com o [Standard Load Balancer](load-balancer-standard-overview.md), as cargas de todas as portas serão balanceadas e uma única resposta da investigação de integridade deverá refletir o status de toda a instância.

Não converta uma investigação de integridade nem use um proxy para ela por meio da instância que recebe a investigação de integridade para outra instância na VNET, pois essa configuração pode resultar em falhas em cascata no cenário.  Considere o seguinte cenário: um conjunto de dispositivos de terceiros é implantado no pool de back-end de um recurso do Load Balancer para fornecer escala e redundância para os dispositivos e a investigação de integridade está configurada para investigar uma porta de investigação que o dispositivo de terceiros usa como proxy ou converte em outras máquinas virtuais por trás do dispositivo.  Se você investigar a mesma porta que está usando para converter as solicitações ou usar um proxy para elas para as outras máquinas virtuais por trás do dispositivo, qualquer resposta de investigação de uma única máquina virtual por trás do dispositivo marcará o dispositivo em si como inativo. Essa configuração pode resultar em uma falha em cascata de todo o cenário de aplicativo, como resultado de uma única instância de back-end por trás do dispositivo.  O gatilho pode ser uma falha de investigação intermitente que fará com que o Load Balancer marque o destino original como inoperante (a instância do dispositivo) e, por sua vez, pode desabilitar todo o cenário de aplicativo. Em vez disso, investigue a integridade do dispositivo em si. A seleção da investigação para determinar o sinal de integridade é uma consideração importante para cenários de NVA (soluções de virtualização de rede) e é necessário consultar o fornecedor do aplicativo para descobrir qual é o sinal de integridade apropriado para esses cenários.

Se você não permitir o [IP de origem](#probesource) da investigação nas políticas de firewall, a investigação de integridade falhará, pois não poderá acessar a instância.  Por sua vez, o Load Balancer marcará para a instância como inoperante devido à falha da investigação de integridade.  Essa configuração incorreta poderá causar uma falha no cenário de aplicativo com balanceamento de carga.

Para que a investigação de integridade do Load Balancer marque a instância como operante, é **necessário** permitir esse endereço IP em todos os [grupos de segurança de rede](../virtual-network/security-overview.md) do Azure e nas políticas de firewall local.  Por padrão, cada grupo de segurança de rede inclui a [marca de serviço](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer para permitir o tráfego de investigação de integridade.

Caso deseje testar uma falha de investigação de integridade ou marcar uma instância individual como inoperante, use um [grupo de segurança de rede](../virtual-network/security-overview.md) para bloquear explicitamente a investigação de integridade (porta de destino ou [IP de origem](#probesource)) e simular a falha de uma investigação.

Não configure a VNET com o intervalo de endereços IP de propriedade da Microsoft que contém 168.63.129.16.  Essas configurações entrarão em conflito com o endereço IP da investigação de integridade e poderão resultar na falha do cenário.

Se você tiver várias interfaces na VM, será necessário certificar-se de responder à investigação na interface em que a investigação foi recebida.  Talvez seja necessário obter a conversão do endereço de rede de origem desse endereço na VM por interface.

Não habilite [carimbos de data/hora TCP](https://tools.ietf.org/html/rfc1323).  A habilitação de carimbos de data/hora TCP resultará na falha das investigações de integridade devido à remoção dos pacotes TCP pela pilha TCP do sistema operacional convidado da VM, o que faz com que o Load Balancer marque o respectivo ponto de extremidade como inoperante.  Os carimbos de data/hora TCP são rotineiramente habilitados por padrão em imagens de VM protegidas pela segurança e precisam ser desabilitados.

## <a name="monitoring"></a>Monitoramento

O [Standard Load Balancer](load-balancer-standard-overview.md) público e interno expõe o status da investigação de integridade por ponto de extremidade e por instância de back-end como métricas multidimensionais por meio do Azure Monitor. Essas métricas podem ser consumidas por outros serviços ou aplicativos de parceiros. 

Balanceador de carga público básico expõe o status de investigação de integridade resumidos por pool de back-end por meio dos logs do Azure Monitor.  Os logs do Azure Monitor não estão disponíveis para balanceadores de carga básico internos.  Você pode usar [registra em log do Azure Monitor](load-balancer-monitor-log.md) para verificar o status de integridade de investigação de Balanceador de carga público e contagem de investigação. O registro em log pode ser usado com o Power BI ou com o Azure Operational Insights para fornecer estatísticas sobre o status da integridade do balanceador de carga.

## <a name="limitations"></a>Limitações

- Investigações HTTPS não dão suporte à autenticação mútua com um certificado de cliente.
- As investigações de integridade falharão quando os carimbos de data/hora TCP estiverem habilitados.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Load Balancer Standard](load-balancer-standard-overview.md)
- [Introdução à criação de um balanceador de carga público no Gerenciador de Recursos usando PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [API REST para investigações de integridade](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Solicitar novas habilidades de investigação de integridade com [Uservoice do Load Balancer](https://aka.ms/lbuservoice)
