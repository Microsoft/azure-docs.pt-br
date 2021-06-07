---
title: Investigações de integridade para dimensionar e fornecer HA para seu serviço
titleSuffix: Azure Load Balancer
description: Neste artigo, saiba como usar investigações de integridade para monitorar instâncias por trás Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: a008d7b26738b9552a7a43ab026391bd9afe0aa8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96780936"
---
# <a name="load-balancer-health-probes"></a>Investigações de integridade do Load Balancer

Ao usar regras de balanceamento de carga com Azure Load Balancer, você precisa especificar investigações de integridade para permitir que Load Balancer detecte o status do ponto de extremidade de back-end.  A configuração das respostas de investigação e investigação de integridade determinam quais instâncias de pool de back-end receberão novos fluxos. Você pode usar investigações de integridade para detectar a falha de um aplicativo em um ponto de extremidade de back-end. Você também pode gerar uma resposta personalizada para uma investigação de integridade e usar a investigação de integridade para controle de fluxo, a fim de gerenciar o tempo de inatividade planejado ou de carga. Quando uma investigação de integridade falhar, Load Balancer deixará de enviar novos fluxos para a respectiva instância não íntegra. A conectividade de saída não é afetada, somente a conectividade de entrada é afetada.

As investigações de integridade dão suporte a vários protocolos. A disponibilidade de um protocolo de investigação de integridade específico varia de acordo com Load Balancer SKU.  Além disso, o comportamento do serviço varia de acordo com Load Balancer SKU, conforme mostrado nesta tabela:

| | SKU Standard | SKU Básico |
| --- | --- | --- |
| **[Tipos de investigação](#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[Comportamento de investigação](#probedown)** | Todas as investigações inoperantes, todos os fluxos TCP continuam. | Todas as investigações, todos os fluxos de TCP expiram. | 


>[!IMPORTANT]
>Examine este documento em sua totalidade, incluindo [diretrizes de design](#design) importantes abaixo para criar um serviço confiável.

>[!IMPORTANT]
>As investigações de integridade do Load Balancer originam-se no endereço IP 168.63.129.16 e não devem ser bloqueadas para que as investigações marquem a instância como operante.  Revisar o [endereço IP de origem da investigação](#probesource) para obter detalhes.

>[!IMPORTANT]
>Independentemente do limite de tempo limite configurado, HTTP (S) Load Balancer as investigações de integridade irão investigar automaticamente uma instância se o servidor retornar qualquer código de status que não seja HTTP 200 OK ou se a conexão for encerrada via redefinição de TCP.

## <a name="probe-configuration"></a><a name="probes"></a>Configuração de investigação

A configuração de investigação de integridade consiste nos seguintes elementos:

- Duração do intervalo entre investigações individuais
- Número de respostas de investigação que precisam ser observadas antes da transição da investigação para um estado diferente
- Protocolo da investigação
- Porta da investigação
- Caminho HTTP a ser usado para HTTP GET ao usar investigações HTTP (S)

>[!NOTE]
>Uma definição de investigação não é obrigatória ou verificada ao usar Azure PowerShell, CLI do Azure, modelos ou API. Testes de validação de investigação só são feitos ao usar o portal do Azure.

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Compreendendo o sinal do aplicativo, a detecção do sinal e a reação da plataforma

O número de respostas de investigação se aplica a ambos

- o número de investigações bem-sucedidas que permitem que uma instância seja marcada como ativa e
- o número de investigações com tempo limite que fazem com que uma instância seja marcada como inativa.

Os valores de tempo limite e intervalo especificados determinam se uma instância será marcada como up ou down.  A duração do intervalo multiplicado pelo número de respostas de investigação determina a duração durante a qual as respostas de investigação precisam ser detectadas.  E o serviço reagirá depois que as investigações necessárias tiverem sido atingidas.

Podemos ilustrar o comportamento mais detalhadamente com um exemplo. Se você tiver definido o número de respostas de investigação como 2 e o intervalo como 5 segundos, isso significará que 2 falhas de tempo limite de investigação devem ser observadas em um intervalo de 10 segundos.  Como a hora em que uma investigação é enviada não é sincronizada quando seu aplicativo pode mudar de estado, podemos associar o tempo para detectar por dois cenários:

1. Se seu aplicativo começar a produzir uma resposta de investigação de tempo limite imediatamente antes da chegada da primeira investigação, a detecção desses eventos levará 10 segundos (intervalos de 2 x 5 segundos) mais a duração do aplicativo começando a sinalizar um tempo limite para quando a primeira investigação chegar.  Você pode assumir que essa detecção leva um pouco mais de 10 segundos.
2. Se seu aplicativo começar a produzir uma resposta de investigação de tempo limite logo após a chegada da primeira investigação, a detecção desses eventos não começará até que a próxima investigação chegue (e expire) mais 10 segundos (intervalos de 2 x 5 segundos).  Você pode assumir que essa detecção leva menos de 15 segundos.

Para este exemplo, depois que a detecção tiver ocorrido, a plataforma levará um pouco de tempo para reagir a essa alteração.  Isso significa um dependendo de 

1. Quando o aplicativo começa a mudar o estado e
2. Quando essa alteração é detectada e atende aos critérios necessários (número de investigações enviadas no intervalo especificado) e
3. Quando a detecção foi comunicada na plataforma 

Você pode assumir que a reação a uma resposta de investigação de tempo limite demorará entre um mínimo de 10 segundos e um número um pouco mais de 15 segundos para reagir a uma alteração no sinal do aplicativo.  Este exemplo é fornecido para ilustrar o que está ocorrendo, no entanto, não é possível prever uma duração exata além das diretrizes aproximadas acima ilustradas neste exemplo.

>[!NOTE]
>A investigação de integridade investigará todas as instâncias em execução no pool de back-end. Se uma instância for interrompida, ela não será investigada até que tenha sido iniciada novamente.

## <a name="probe-types"></a><a name="types"></a>Tipos de investigações

O protocolo usado pela investigação de integridade pode ser configurado para um dos seguintes:

- [Ouvintes TCP](#tcpprobe)
- [Pontos de extremidade HTTP](#httpprobe)
- [Pontos de Extremidade HTTPS](#httpsprobe)

Os protocolos disponíveis dependem do Load Balancer SKU usado:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| **SKU Standard** |    &#9989; |   &#9989; |   &#9989; |
| **SKU Básico** |   &#9989; |   &#9989; | &#10060; |

### <a name="tcp-probe"></a><a name="tcpprobe"></a>Investigação TCP

As investigações TCP iniciam uma conexão executando um handshake TCP aberto de três vias com a porta definida.  As investigações TCP encerram uma conexão com um handshake TCP fechado de quatro vias.

O intervalo mínimo de investigação é de 5 segundos e o número mínimo de respostas não íntegras é 2.  A duração total de todos os intervalos não pode exceder 120 segundos.

Uma investigação TCP falha quando:
* O ouvinte TCP na instância não responde durante o período de tempo limite.  Uma investigação é marcada com base no número de solicitações de investigação de tempo limite que foram configuradas para não responder antes de marcar a investigação.
* A investigação recebe uma redefinição de TCP da instância.

O seguinte ilustra como você pode expressar esse tipo de configuração de investigação em um modelo do Resource Manager:

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

### <a name="http--https-probe"></a><a name="httpprobe"></a> <a name="httpsprobe"></a> Investigação HTTP / HTTPS

>[!NOTE]
>Investigação HTTPS está disponível somente para [Standard Load Balancer](./load-balancer-overview.md).

As investigações HTTP e HTTPS se baseiam na investigação TCP e emitem um HTTP GET com o caminho especificado. Ambas essas investigações dão suporte a caminhos relativos para o HTTP GET. Investigações HTTPS são iguais às investigações HTTP com a adição de um wrapper de TLS (Transport Layer Security), anteriormente conhecido como SSL. A investigação de integridade é marcada como operante quando a instância responde com um status HTTP 200 dentro do período de tempo limite.  A investigação de integridade tenta verificar a porta de investigação de integridade configurada a cada 15 segundos, por padrão. O intervalo mínimo de investigação é de 5 segundos. A duração total de todos os intervalos não pode exceder 120 segundos.

As investigações HTTP/HTTPS também podem ser úteis para implementar sua própria lógica para remover instâncias da rotação do balanceador de carga se a porta de investigação também for o ouvinte para o próprio serviço. Por exemplo, é recomendável remover uma instância caso ela esteja usando mais de 90% da CPU e retorne um status HTTP diferente de 200. 

> [!NOTE] 
> A investigação de HTTPS requer o uso de certificados com base que tenham um hash de assinatura mínimo de SHA256 em toda a cadeia.

Se você usar o Serviços de Nuvem e tiver funções web que usem w3wp.exe, também é possível obter o monitoramento automático do site. Falhas no código do site retornam um status não 200 para a investigação do balanceador de carga.

Uma investigação HTTP / HTTPS falha quando:
* O ponto de extremidade da investigação retorna um código de resposta HTTP diferente de 200 (por exemplo, 403, 404 ou 500). Isso marcará imediatamente a investigação de integridade como inoperante. 
* O ponto de extremidade de investigação não responde durante o mínimo do intervalo de investigação e do período de tempo limite de 30 segundos. Várias solicitações de investigação podem ficar sem resposta antes que a investigação seja marcada como não estando em execução e até que a soma de todos os intervalos de tempo limite seja atingido.
* O ponto de extremidade de investigação fecha a conexão por meio de uma redefinição de TCP.

O seguinte ilustra como você pode expressar esse tipo de configuração de investigação em um modelo do Resource Manager:

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

### <a name="guest-agent-probe-classic-only"></a><a name="guestagent"></a>Investigação de agente convidado (somente clássico)

Por padrão, as funções do serviço de nuvem (funções de trabalho e funções web) usam um agente convidado para o monitoramento de investigação.  Uma investigação de agente convidado é uma configuração de último recurso.  Sempre use uma investigação de integridade explicitamente com uma investigação TCP ou HTTP. Uma investigação de agente convidado não é tão eficiente quanto a investigações definidas explicitamente para a maioria dos cenários de aplicativo.

Uma investigação de agente convidado é uma verificação do agente convidado dentro da VM. Em seguida, ele escuta e responde com uma resposta HTTP 200 OK somente quando a instância está no estado Pronto. (Outros estados são Ocupado, Reciclando ou Parando.)

Para obter mais informações, consulte [Configurar o arquivo de definição de serviço (csdef) para investigações de integridade](/previous-versions/azure/reference/ee758710(v=azure.100)) ou [Introdução à criação de um balanceador de carga público para serviços de nuvem](/previous-versions/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services).

Se o agente convidado não responder com HTTP 200 OK, o balanceador de carga marcará a instância como sem resposta. Em seguida, ele para de enviar fluxos a essa instância. O balanceador de carga continua a verificar a instância. 

Se o agente convidado responder com um HTTP 200, o balanceador de carga enviará o tráfego para essa instância novamente.

Quando você usa uma função Web, o código do site geralmente é executado em w3wp.exe, que não é monitorado nem pela malha do Azure nem pelo agente convidado. Falhas em w3wp.exe (por exemplo, respostas HTTP 500) não são relatadas para o agente convidado. Consequentemente, o balanceador de carga não perde essa instância.

<a name="health"></a>
## <a name="probe-up-behavior"></a><a name="probehealth"></a>Comportamento de investigação

As investigações de integridade TCP, HTTP e HTTPS são consideradas íntegras e marcam o ponto de extremidade back-end como íntegro quando:

* A investigação de integridade é bem-sucedida uma vez após a inicialização da VM.
* O número especificado de investigações necessárias para marcar o ponto de extremidade de back-end como íntegro foi atingido.

Qualquer ponto de extremidade de back-end que tenha atingido um estado íntegro é qualificado para receber novos fluxos.  

> [!NOTE]
> Se a investigação de integridade flutuar, o balanceador de carga aguarda mais tempo antes de colocar o ponto de extremidade de back-end no estado íntegro. Esse tempo de espera extra protege o usuário e a infraestrutura, e é uma política intencional.

## <a name="probe-down-behavior"></a><a name="probedown"></a>Comportamento de investigação inoperante

### <a name="tcp-connections"></a>Conexões TCP

As novas conexões TCP terão sucesso no ponto de extremidade de back-end íntegro.

Se uma investigação de integridade do ponto de extremidade de back-end falhar, as conexões TCP estabelecidas com esse ponto de extremidade de back-end

Se todas as investigações para todas as instâncias em um pool de back-end falharem, nenhum novo fluxo será enviado ao pool de back-end. O Load Balancer Standard permitirá a continuação dos fluxos TCP estabelecidos.  O Basic Load Balancer terminará todos os fluxos TCP existentes para o pool de back-end.
 
O Load Balancer é um serviço de passagem (não encerra conexões TCP) e o fluxo ocorre sempre entre o cliente e o sistema operacional convidado e o aplicativo da VM. Um pool com todas as investigações fará com que um front-end não responda a SYN (tentativas de abertura de conexão TCP), pois não há nenhum ponto de extremidade de back-end íntegro para receber o fluxo e responder com um SYN-ACK.

### <a name="udp-datagrams"></a>Datagramas UDP

Os datagramas UDP serão entregues aos pontos de extremidade de back-end íntegros.

UDP é sem conexão e não há estado de fluxo controlado para UDP. Se qualquer investigação de integridade do ponto de extremidade de back-end falhar, os fluxos UDP existentes serão movidos para outra instância íntegra no pool de back-end.

Se todas as investigações para todas as instâncias em um pool de back-end falharem, os fluxos UDP existentes serão encerrados para os Basic e Standard Load Balancers.

<a name="source"></a>
## <a name="probe-source-ip-address"></a><a name="probesource"></a>Endereço IP de origem da investigação

O Load Balancer usa um serviço de investigação distribuído para seu modelo de integridade interno. O serviço de investigação reside em cada host no qual as VMs e podem ser programadas sob demanda para gerar investigações de integridade de acordo com a configuração do cliente. O tráfego da investigação de integridade está diretamente entre o serviço de investigação que gera a investigação de integridade e a VM do cliente. Todas as investigações de integridade do Load Balancer originam-se do endereço IP 168.63.129.16 como sua fonte.  Use o espaço de endereços IP em uma VNET que não seja o espaço RFC1918.  O uso de um endereço IP globalmente reservado e de propriedade da Microsoft reduz a possibilidade de um conflito de endereços IP com o espaço de endereços IP utilizado na VNET.  Esse endereço IP é o mesmo em todas as regiões e não é alterado nem representa um risco à segurança porque apenas o componente interno da plataforma Azure pode obter um pacote desse endereço IP. 

A marca de serviço AzureLoadBalancer identifica esse endereço IP de origem nos [grupos de segurança de rede](../virtual-network/network-security-groups-overview.md) e permite o tráfego da investigação de integridade por padrão.

Além de Load Balancer investigações de integridade, as [seguintes operações usam esse endereço IP](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Permite que o Agente de VM se comunique com a plataforma para sinalizar que ele está em um estado "Pronto"
- Permite a comunicação com o servidor virtual de DNS para fornecer resolução de nome filtrado aos clientes que não definem servidores DNS personalizados.  Essa filtragem garante que cada cliente só possa resolver os nomes de host de sua própria implantação.
- Permite que a VM obtenha um endereço IP dinâmico do serviço DHCP no Azure.

## <a name="design-guidance"></a><a name="design"></a> Diretrizes de design

As investigações de integridade são usadas para manter seu serviço resiliente e permitir que ele seja dimensionado. Uma configuração incorreta ou um padrão de design inadequado pode afetar a disponibilidade e a escalabilidade do serviço. Leia este documento na íntegra e considere qual é o impacto no seu cenário quando a resposta dessa investigação é marcada como inoperante ou operante e como isso afeta a disponibilidade do cenário de aplicativo.

Quando você cria o modelo de integridade para seu aplicativo, você deve investigar uma porta em um ponto de extremidade de back-end que reflita a integridade dessa instância __e__ o serviço de aplicativo que você está fornecendo.  A porta do aplicativo e a porta de investigação não precisam ser as mesmas.  Em alguns cenários, pode ser desejável que a porta de investigação seja diferente da porta na qual o aplicativo fornece o serviço.  

Às vezes, pode ser útil para seu aplicativo gerar uma resposta de investigação de integridade para não apenas detectar a integridade do aplicativo, mas também sinalizar diretamente para o Load Balancer se a instância deve receber ou não novos fluxos.  Você pode manipular a resposta de investigação para permitir que seu aplicativo crie a pressão de retorno e limite a entrega de novos fluxos para uma instância que falhou na investigação de integridade ou preparar a manutenção do aplicativo e iniciar a drenagem do cenário.  Ao usar o Standard Load Balancer, um sinal de [investigação inoperante](#probedown) sempre permitirá a continuidade dos fluxos TCP até o tempo limite ocioso ou o fechamento da conexão. 

Para o balanceamento de carga de UDP, você deve gerar um sinal de investigação de integridade personalizado do ponto de extremidade de back-end e usar uma investigação de integridade TCP, HTTP ou HTTPS direcionando o ouvinte correspondente para refletir a integridade do aplicativo UDP.

Ao usar as [regras de balanceamento de carga das Portas HA](load-balancer-ha-ports-overview.md) com o [Standard Load Balancer](./load-balancer-overview.md), as cargas de todas as portas serão balanceadas e uma única resposta da investigação de integridade deverá refletir o status de toda a instância.

Não converta uma investigação de integridade nem use um proxy para ela por meio da instância que recebe a investigação de integridade para outra instância na VNET, pois essa configuração pode resultar em falhas em cascata no cenário.  Considere o seguinte cenário: um conjunto de dispositivos de terceiros é implantado no pool de back-end de um recurso do Load Balancer para fornecer escala e redundância para os dispositivos e a investigação de integridade está configurada para investigar uma porta de investigação que o dispositivo de terceiros usa como proxy ou converte em outras máquinas virtuais por trás do dispositivo.  Se você investigar a mesma porta que está usando para converter as solicitações ou usar um proxy para elas para as outras máquinas virtuais por trás do dispositivo, qualquer resposta de investigação de uma única máquina virtual por trás do dispositivo marcará o dispositivo em si como inativo. Essa configuração pode levar a uma falha em cascata de todo o cenário do aplicativo como resultado de um único ponto de extremidade de back-end por trás do dispositivo.  O gatilho pode ser uma falha de investigação intermitente que fará com que o Load Balancer marque o destino original como inoperante (a instância do dispositivo) e, por sua vez, pode desabilitar todo o cenário de aplicativo. Em vez disso, investigue a integridade do dispositivo em si. A seleção da investigação para determinar o sinal de integridade é uma consideração importante para cenários de NVA (soluções de virtualização de rede) e é necessário consultar o fornecedor do aplicativo para descobrir qual é o sinal de integridade apropriado para esses cenários.

Se você não permitir o [IP de origem](#probesource) da investigação nas políticas de firewall, a investigação de integridade falhará, pois não poderá acessar a instância.  Por sua vez, o Load Balancer marcará para a instância como inoperante devido à falha da investigação de integridade.  Essa configuração incorreta poderá causar uma falha no cenário de aplicativo com balanceamento de carga.

Para que a investigação de integridade do Load Balancer marque a instância como operante, é **necessário** permitir esse endereço IP em todos os [grupos de segurança de rede](../virtual-network/network-security-groups-overview.md) do Azure e nas políticas de firewall local.  Por padrão, cada grupo de segurança de rede inclui a [marca de serviço](../virtual-network/network-security-groups-overview.md#service-tags) AzureLoadBalancer para permitir o tráfego de investigação de integridade.

Caso deseje testar uma falha de investigação de integridade ou marcar uma instância individual como inoperante, use um [grupo de segurança de rede](../virtual-network/network-security-groups-overview.md) para bloquear explicitamente a investigação de integridade (porta de destino ou [IP de origem](#probesource)) e simular a falha de uma investigação.

Não configure a VNET com o intervalo de endereços IP de propriedade da Microsoft que contém 168.63.129.16.  Essas configurações entrarão em conflito com o endereço IP da investigação de integridade e poderão resultar na falha do cenário.

Se você tiver várias interfaces na VM, será necessário certificar-se de responder à investigação na interface em que a investigação foi recebida.  Talvez seja necessário obter a conversão do endereço de rede de origem desse endereço na VM por interface.

Não habilite [carimbos de data/hora TCP](https://tools.ietf.org/html/rfc1323).  Habilitar carimbos de data/hora TCP pode causar falha em investigações de integridade devido a pacotes TCP sendo descartados pela pilha TCP do sistema operacional convidado da VM, o que resulta em Load Balancer marcando o respectivo ponto de extremidade.  Os carimbos de data/hora TCP são rotineiramente habilitados por padrão em imagens de VM protegidas pela segurança e precisam ser desabilitados.

## <a name="monitoring"></a>Monitoramento

Os [Standard Load Balancer](./load-balancer-overview.md) públicos e internos expõem por ponto de extremidade e status de investigação de integridade do ponto de extremidade de back-end como métricas multidimensionais através de Azure monitor Essas métricas podem ser consumidas por outros serviços do Azure ou aplicativos de parceiro. 

O Load Balancer público básico expõe o status de investigação de integridade resumido por pool de back-end por meio de logs Azure Monitor.  Os logs de Azure Monitor não estão disponíveis para balanceadores de carga básicos internos.  Você pode usar [os logs de Azure monitor](load-balancer-monitor-log.md) para verificar o status de integridade da investigação do balanceador de carga público e a contagem de investigação. O registro em log pode ser usado com o Power BI ou com o Azure Operational Insights para fornecer estatísticas sobre o status da integridade do balanceador de carga.

## <a name="limitations"></a>Limitações

- Investigações HTTPS não dão suporte à autenticação mútua com um certificado de cliente.
- Você deve assumir que as investigações de integridade falharão quando os carimbos de data/hora TCP estiverem habilitados.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Standard Load Balancer](./load-balancer-overview.md)
- [Introdução à criação de um balanceador de carga público no Gerenciador de Recursos usando PowerShell](quickstart-load-balancer-standard-public-powershell.md)
- [API REST para investigações de integridade](/rest/api/load-balancer/loadbalancerprobes/)
- Solicitar novas habilidades de investigação de integridade com [Uservoice do Load Balancer](https://aka.ms/lbuservoice)
