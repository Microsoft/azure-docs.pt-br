---
title: Ajuste de desempenho de TCP/IP para VMs do Azure | Microsoft Docs
description: Aprenda várias TCP/IP desempenho ajustes técnicas comuns e suas relações com VMs do Azure.
services: virtual-network
documentationcenter: na
author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
ms.openlocfilehash: 1e8605a41cbe610c971b891309b2149d221b8b27
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032444"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>TCP/IP ajuste de desempenho para VMs do Azure

Este artigo discute técnicas comuns de ajuste de desempenho TCP/IP e algumas coisas a serem consideradas ao usá-las para máquinas virtuais em execução no Azure. Ele fornece uma visão geral básica das técnicas e explorar como eles podem ser ajustados.

## <a name="common-tcpip-tuning-techniques"></a>Técnicas comuns de ajuste de TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU, fragmentação e descarregamento de envio grande

#### <a name="mtu"></a>MTU

A unidade de transmissão máxima (MTU) é o maior tamanho quadro (pacote), especificado em bytes, que podem ser enviados por um adaptador de rede. A MTU é uma definição configurável. O padrão de MTU usado em VMs do Azure e a configuração padrão na maioria dos dispositivos de rede globalmente, é de 1.500 bytes.

#### <a name="fragmentation"></a>Fragmentação

A fragmentação ocorre quando um pacote é enviado, que excede a MTU de uma interface de rede. O TCP/IP stack interromperá o pacote em partes menores (fragmentos) que estão em conformidade com a MTU da interface. A fragmentação ocorre na camada de IP e é independente do protocolo subjacente (como TCP). Quando um pacote de 2.000 bytes é enviado por um adaptador de rede com uma MTU de 1.500, o pacote será ser dividido em um pacote de 1.500 bytes e um pacote de 500 bytes.

Dispositivos de rede no caminho entre a origem e destino podem qualquer um dos pacotes de soltar que excedem a MTU ou fragmentarem o pacote em partes menores.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>O fragmento não de bit em um pacote IP

O bit do não Fragmentar é um sinalizador no cabeçalho de protocolo IP. O bit DF indica que os dispositivos de rede no caminho entre o remetente e o receptor devem fragmenta o pacote. Esse bit foi definido por vários motivos. (Consulte a seção de "Descoberta de MTU de caminho" deste artigo para obter um exemplo). Quando um dispositivo de rede recebe um pacote com o conjunto de bits de fragmento não, e esse pacote ultrapassar a MTU da interface do dispositivo, o comportamento padrão é para o dispositivo remover o pacote. O dispositivo envia uma mensagem ICMP fragmentação necessária volta para a fonte original do pacote.

#### <a name="performance-implications-of-fragmentation"></a>Implicações de desempenho de fragmentação

Fragmentação pode ter implicações de desempenho negativo. Uma das principais razões para o efeito no desempenho é o impacto de CPU/memória de fragmentação e reagrupamento de pacotes. Quando um dispositivo de rede precisa de um pacote do fragmento, ele precisa alocar recursos de CPU/memória para executar a fragmentação.

A mesma coisa acontece quando o pacote é reorganizado. O dispositivo de rede precisa armazenar todos os fragmentos até que elas são recebidas para que ele pode remontá-las para o pacote original. Esse processo de fragmentação e reagrupamento também pode causar a latência.

A outra implicação de desempenho negativo possíveis de fragmentação é que os pacotes fragmentados podem chegar fora de ordem. Quando os pacotes são recebidos fora de ordem, alguns tipos de dispositivos de rede poderá removê-los. Quando isso acontece, todo o pacote deve ser retransmitido.

Fragmentos são normalmente descartados por dispositivos de segurança, como firewalls de rede ou quando um dispositivo de rede de recebimento do buffers são esgotados. Quando um dispositivo de rede de recebimento do buffers são esgotados, um dispositivo de rede está tentando remontar um pacote fragmentado, mas não tem os recursos para armazenar e reassume o pacote.

A fragmentação pode ser vista como uma operação negativa, mas o suporte para fragmentação é necessária quando você estiver se conectando a diversas redes pela internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Benefícios e as consequências de modificar a MTU

Em termos gerais, você pode criar uma rede mais eficiente, aumentando a MTU. Todos os pacotes que são transmitidos tem informações de cabeçalho que são adicionadas ao pacote original. Quando a fragmentação cria mais pacotes, não há cabeçalho mais sobrecarga e que faz com que a rede menos eficiente.

Aqui está um exemplo. O tamanho do cabeçalho Ethernet é 14 bytes mais de uma sequência de verificação de quadro de 4 bytes para garantir a consistência do quadro. Se um pacote de 2.000 bytes é enviado, 18 bytes de sobrecarga de Ethernet é adicionado na rede. Se o pacote é fragmentado em um pacote de 1.500 bytes e um pacote de 500 bytes, cada pacote terá de 18 bytes do cabeçalho de Ethernet, um total de 36 bytes.

Tenha em mente que a aumentar a MTU necessariamente não criará uma rede mais eficiente. Se um aplicativo envia somente pacotes de 500 bytes, a sobrecarga de cabeçalho mesmo existirá se o MTU é 1.500 bytes ou 9.000 bytes. A rede se tornará mais eficiente apenas se ele usa o maior tamanho dos pacotes que é afetados pela MTU.

#### <a name="azure-and-vm-mtu"></a>O Azure e MTU de VM

A MTU para VMs do Azure padrão é 1.500 bytes. A pilha de rede Virtual do Azure tentará um pacote em bytes 1.400 do fragmento. Mas a pilha de rede Virtual permitirá pacotes até 2,006 bytes quando o bit de fragmento não é definido no cabeçalho IP.

Observe que a pilha de rede Virtual não é inerentemente ineficiente porque ele pacotes em 1.400 bytes de fragmentos, embora as VMs têm uma MTU de 1.500. Uma grande porcentagem de pacotes de rede são muito menores do que 1.400 ou 1.500 bytes.

#### <a name="azure-and-fragmentation"></a>Azure e a fragmentação

Pilha de rede virtual é configurada para descartar "fragmentos fora de ordem", ou seja, os pacotes fragmentados que não chegam na ordem original fragmentado. Esses pacotes são descartados principalmente por causa de uma vulnerabilidade de segurança de rede anunciada em novembro de 2018 chamado FragmentSmack.

FragmentSmack é um defeito a maneira como o kernel do Linux manipulados reagrupamento de pacotes IPv4 e IPv6 fragmentados. Um invasor remoto pode usar essa falha para disparar fragmento caro reagrupamento operações, que pode levar a uma negação de serviço e de aumento de CPU no sistema de destino.

#### <a name="tune-the-mtu"></a>Ajustar a MTU

Você pode configurar uma MTU de VM do Azure, como faria em qualquer outro sistema operacional. Mas você deve considerar a fragmentação que ocorre no Azure, como descrito acima, quando você estiver configurando uma MTU.

Não Incentivamos os clientes a aumentar MTUs de VM. Esta discussão destina-se para explicar os detalhes de como o Azure implementa o MTU e executa a fragmentação.

> [!IMPORTANT]
>Aumentando a MTU não é conhecido para melhorar o desempenho e pode ter um efeito negativo no desempenho do aplicativo.
>
>

#### <a name="large-send-offload"></a>Descarregamento de envio grande

Descarregamento de envio grande (LSO) pode melhorar o desempenho de rede ao descarregar a segmentação de pacotes para o adaptador de Ethernet. Quando LSO está habilitado, o TCP/IP stack cria um pacote grande de TCP e envia para o adaptador de Ethernet para segmentação antes de encaminhá-lo. O benefício de LSO é que ele pode liberar a CPU da segmentação de pacotes em tamanhos que estão em conformidade com a MTU e que o processamento para a interface de Ethernet em que ele é executado em hardware de descarregamento. Para saber mais sobre os benefícios de LSO, consulte [que dão suporte a grande send offload](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Quando LSO estiver habilitada, os clientes do Azure poderá ver tamanhos grandes de quadro ao executar capturas de pacote. Esses tamanhos de quadro grande podem levar alguns clientes pensar fragmentação está ocorrendo ou que uma MTU grande está sendo usado quando não for. Com LSO, o adaptador Ethernet pode anunciar um tamanho maior de máximo de segmento (MSS) para a pilha de TCP/IP para criar um pacote maior de TCP. Todo esse quadro não segmentado é então encaminhado para o adaptador de Ethernet e deve ser visível em uma captura de pacotes executada na VM. Mas o pacote será ser dividido pelo adaptador de Ethernet, de acordo com a MTU do adaptador de Ethernet em menor número de quadros.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>Dimensionamento de janela de MSS TCP e PMTUD

#### <a name="tcp-maximum-segment-size"></a>Tamanho máximo de segmento TCP

Tamanho máximo de segmento TCP (MSS) é uma configuração que limita o tamanho de segmentos TCP, que evita a fragmentação de pacotes TCP. Sistemas operacionais geralmente usará esta fórmula para definir o MSS:

`MSS = MTU - (IP header size + TCP header size)`

O cabeçalho IP e o cabeçalho de TCP são 20 bytes ou o total de 40 bytes. Portanto, uma interface com uma MTU de 1.500 terá um MSS de 1,460. Mas o MSS é configurável.

Essa configuração foi acordada no handshake de três vias TCP quando uma sessão TCP é definida entre uma origem e um destino. Ambos os lados enviar um valor de MSS, e o menor dos dois é usado para a conexão TCP.

Tenha em mente que o MTUs de origem e destino não são os únicos fatores que determinam o valor de MSS. Dispositivos de rede intermediários, como gateways de VPN, incluindo o Gateway de VPN do Azure, podem ajustar a MTU independentemente de origem e destino para garantir um desempenho ideal de rede.

#### <a name="path-mtu-discovery"></a>Descoberta de MTU de caminho

MSS é negociada, mas ele não pode indicar o MSS real que pode ser usado. Isso ocorre porque os outros dispositivos de rede no caminho entre a origem e o destino podem ter um valor MTU menor do que a origem e destino. Nesse caso, o dispositivo cujo MTU é menor do que o pacote descartará o pacote. O dispositivo enviará de volta uma mensagem ICMP fragmentação necessária (tipo 3, 4 de código) que contém seu MTU. Esta mensagem ICMP permite que o host de origem reduzir seu MTU de caminho adequadamente. O processo é chamado de descoberta de MTU de caminho (PMTUD).

O processo PMTUD é ineficiente e afeta o desempenho de rede. Quando os pacotes são enviados que excedem o MTU do caminho de uma rede, os pacotes precisam ser retransmitida com um MSS inferior. Se o remetente não recebe a mensagem ICMP fragmentação necessária, talvez devido a um firewall de rede no caminho (normalmente conhecido como um *PMTUD blackhole*), o remetente não souber, ele precisa reduzir o MSS e será continuamente o pacote de retransmissão. É por isso não é recomendável aumentar o MTU de VM do Azure.

#### <a name="vpn-and-mtu"></a>VPN e MTU

Se você usar VMs que executam o encapsulamento (como VPNs IPsec), há algumas considerações adicionais sobre o tamanho do pacote e MTU. VPNs de adicionar mais cabeçalhos para pacotes, que aumenta o tamanho do pacote e requer um MSS menores.

Para o Azure, recomendamos que você defina a fixação de MSS TCP para 1.350 bytes e criar um túnel de MTU da interface para 1.400. Para obter mais informações, consulte o [VPN dispositivos e a página de parâmetros de IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Latência, tempo de ida e volta e dimensionamento de janela TCP

#### <a name="latency-and-round-trip-time"></a>Tempo de latência e de ida e volta

Latência de rede é regida pela velocidade da luz em uma rede de fibra óptica. Taxa de transferência de rede de TCP efetivamente também é regida pelo tempo de ida e volta (RTT) entre os dois dispositivos de rede.

| | | | |
|-|-|-|-|
|**Route**|**distância**|**Tempo unidirecional**|**RTT**|
|Nova Iorque para San Francisco|4,148 km|21 ms|42 ms|
|Nova York para Londres|5,585 km|28 ms|56 ms|
|Nova York para Sydney|15,993 km|80 ms|160 ms|

Esta tabela mostra a distância linear entre dois locais. Em redes, a distância é geralmente mais do que a distância em linha reta. Aqui está uma fórmula simples para calcular o mínimo RTT conforme regido pela velocidade da luz:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

Você pode usar a 200 para a velocidade de propagação. Essa é a distância, em metros, que a luz viaja em 1 milissegundo.

Vamos dar Nova Iorque para San Francisco como exemplo. A distância em linha reta é 4,148 km. Esse valor se conectando a equação, obtemos o seguinte:

`Minimum RTT = 2 * (4,148 / 200)`

A saída da equação é em milissegundos.

Se você quiser obter o melhor desempenho de rede, a opção lógica é selecionar destinos com a distância mais curta entre eles. Você também deve criar sua rede virtual para o caminho do tráfego de otimizar e reduzir a latência. Para obter mais informações, consulte a seção "Considerações de design de rede" deste artigo.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Efeitos de tempo de latência e de ida e volta sobre TCP

Tempo de ida e volta tem um efeito direto na taxa de transferência máxima de TCP. No protocolo TCP, *tamanho da janela* é a quantidade máxima de tráfego que pode ser enviado por uma conexão TCP antes que o remetente precisa receber a confirmação do receptor. Se o MSS TCP é definido como 1,460 e o tamanho da janela TCP é definido como 65.535, o remetente pode enviar 45 pacotes antes que ele deve receber a confirmação do receptor. Se o remetente não receber a confirmação, ele retransmitirá os dados. Aqui está a fórmula:

`TCP window size / TCP MSS = packets sent`

Neste exemplo, 65.535 / 1,460 é arredondado até 45.

Esse estado de "aguardando confirmação", um mecanismo para garantir a entrega confiável de dados, é o que faz com que o RTT afetar a taxa de transferência TCP. Quanto mais tempo o remetente aguarda confirmação, quanto mais tempo ele precisa aguardar antes de enviar mais dados.

Aqui está a fórmula para calcular a taxa de transferência máxima de uma única conexão de TCP:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Esta tabela mostra o máximo em megabytes por segundo taxa de transferência de uma única conexão de TCP. (Para facilitar a leitura, megabytes é usado para a unidade de medida).

| | | | |
|-|-|-|-|
|**Tamanho de janela TCP (bytes)**|**Latência RTT (ms)**|**Máximo em megabytes por segundo de taxa de transferência**|**Máximo megabits/segundo de taxa de transferência**|
|65.535|1|65.54|524.29|
|65.535|30|2.18|17.48|
|65.535|60|1.09|8.74|
|65.535|90|.73|5.83|
|65.535|120|.55|4.37|

Se os pacotes forem perdidos, a taxa de transferência máxima de uma conexão TCP será reduzida enquanto o remetente retransmite dados que já foram enviados.

#### <a name="tcp-window-scaling"></a>Dimensionamento de janela TCP

Dimensionamento de janela TCP é uma técnica que aumenta dinamicamente o tamanho da janela TCP para permitir que mais dados a serem enviados antes que uma confirmação é necessária. No exemplo anterior, os 45 pacotes seriam enviados antes de uma confirmação foi necessária. Se você aumentar o número de pacotes que podem ser enviados antes de uma confirmação é necessária, você está reduzindo o número de vezes que um remetente aguarda o reconhecimento, que aumenta a taxa de transferência máxima de TCP.

Esta tabela ilustra essas relações:

| | | | |
|-|-|-|-|
|**Tamanho de janela TCP (bytes)**|**Latência RTT (ms)**|**Máximo em megabytes por segundo de taxa de transferência**|**Máximo megabits/segundo de taxa de transferência**|
|65.535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

Mas o valor do cabeçalho TCP para o tamanho de janela TCP é apenas 2 bytes de comprimento, o que significa que o valor máximo para uma janela de recepção é de 65.535. Para aumentar o tamanho máximo da janela, um fator de dimensionamento de janela TCP foi introduzido.

O fator de escala também é uma configuração que você pode configurar em um sistema operacional. Aqui está a fórmula para calcular o tamanho da janela TCP por meio de fatores de dimensionamento:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Aqui está o cálculo para um fator de dimensionamento de janela de 3 e um tamanho de janela de 65.535:

`65,535 \* (2^3) = 262,140 bytes`

Um fator de escala de 14 resulta em um tamanho de janela TCP de 14 (o deslocamento máximo permitido). O tamanho da janela TCP será 1,073,725,440 bytes (8,5 gigabits).

#### <a name="support-for-tcp-window-scaling"></a>Suporte para dimensionamento de janela TCP

Windows podem definir diferente fatores de dimensionamento para tipos de conexão diferente. (As classes de conexões incluem datacenter, internet e assim por diante.) Você usa o `Get-NetTCPConnection` para exibir a tipo de conexão de dimensionamento da janela de comando do PowerShell:

```powershell
Get-NetTCPConnection
```

Você pode usar o `Get-NetTCPSetting` para exibir os valores de cada classe de comando do PowerShell:

```powershell
Get-NetTCPSetting
```

Você pode definir o tamanho de janela TCP inicial e o fator de escala de TCP no Windows usando o `Set-NetTCPSetting` comando do PowerShell. Para obter mais informações, consulte [NetTCPSetting conjunto](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Essas são as configurações de TCP em vigor para `AutoTuningLevel`:

| | | | |
|-|-|-|-|
|**AutoTuningLevel**|**Fator de escala**|**Multiplicador de escala**|**Fórmula para<br/>calcular o tamanho máximo da janela**|
|Desabilitado|Nenhum|Nenhum|Tamanho da janela|
|Restrito|4|2^4|Tamanho da janela * (2 ^ 4)|
|Altamente restrito|2|2^2|Tamanho da janela * (2 ^ 2)|
|Normal|8|2^8|Tamanho da janela * (2 ^ 8)|
|Experimental|14|2^14|Tamanho da janela * (2 ^ 14)|

Essas configurações são a maior probabilidade de afetar o desempenho de TCP, mas lembre-se de que muitos outros fatores através da internet, fora do controle do Azure, também podem afetar o desempenho de TCP.

#### <a name="increase-mtu-size"></a>Aumentar o tamanho MTU

Como uma MTU maior significa uma MSS maiores, você talvez esteja se perguntando se aumentar a MTU pode aumentar o desempenho de TCP. Provavelmente, não. Há prós e contras ao tamanho de pacote, além de apenas o tráfego de TCP. Conforme discutido anteriormente, os fatores mais importantes que afetam o desempenho de taxa de transferência TCP são RTT, perda de pacotes e tamanho de janela TCP.

> [!IMPORTANT]
> Não recomendamos que os clientes do Azure altere o valor MTU padrão em máquinas virtuais.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Rede acelerada e RSS

#### <a name="accelerated-networking"></a>Redes aceleradas

Funções de rede da máquina virtual foram historicamente intensivo na VM convidada e host do hipervisor/de CPU. Todos os pacotes que transite por meio do host é processado no software pelo host de CPU, incluindo todos os encapsulamento de rede virtual e desencapsulamento. Portanto, mais o tráfego que passa por host, maior será a CPU carregar. E se a CPU do host está ocupada com outras operações, que também afetará taxa de transferência de rede e latência. Azure resolve esse problema com a rede acelerada.

A rede acelerada fornece a latência de rede ultralow consistente por meio de hardware programável interno do Azure e tecnologias como SR-IOV. A rede acelerada passa grande parte do Azure definido pelo software pilha da rede em SmartNICs baseada em FPGA e as CPUs. Essa alteração permite que os aplicativos do usuário final recuperar ciclos de computação, que coloca menos carga na VM, diminuindo a tremulação e a inconsistência na latência. Em outras palavras, o desempenho pode ser mais determinista.

A rede acelerada melhora o desempenho, permitindo que a VM para ignorar o host e estabelecer um caminho de dados diretamente com um host SmartNIC convidada. Aqui estão alguns benefícios da rede acelerada:

- **Latência menor / maior pacotes por segundo (pps)**: Remover o comutador virtual do caminho de dados elimina o tempo pacotes gastam no host para processamento da política e aumenta o número de pacotes que podem ser processadas na VM.

- **Tremulação reduzida**: Processamento de comutador virtual depende da quantidade de política que precisa ser aplicada e a carga de trabalho da CPU que está fazendo o processamento. Descarregamento da imposição de política para o hardware remove essa variabilidade ao entregar pacotes diretamente à VM, eliminando a comunicação de VM de host e todas as interrupções de software e de alternâncias de contexto.

- **Menor utilização da CPU**: ignorar o comutador virtual no host resulta em menor utilização da CPU para processar o tráfego de rede.

Para usar a rede acelerada, você precisa habilitá-lo explicitamente em cada VM aplicável. Ver [criar uma máquina virtual Linux com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) para obter instruções.

#### <a name="receive-side-scaling"></a>O Receive side scaling

Colocação em escala (RSS) é uma tecnologia de driver de rede que distribui o recebimento do tráfego de rede com mais eficiência com a distribuição de processamento de recebimento entre várias CPUs em um sistema multiprocessador do lado de recebimento. Em termos simples, o RSS permite que um sistema processar mais tráfego recebido, pois ela usa todas as CPUs disponíveis, em vez de apenas um. Para obter uma discussão mais técnica sobre RSS, consulte [Introdução ao RSS](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

Para obter o melhor desempenho quando a rede acelerada está habilitada em uma VM, você precisa habilitar o RSS. RSS também pode fornecer a rede acelerada de benefícios em VMs que não usam. Para uma visão geral de como determinar se o RSS está habilitado e como habilitá-lo, consulte [otimizar a taxa de transferência de rede para máquinas virtuais do Azure](http://aka.ms/FastVM).

### <a name="tcp-timewait-and-timewait-assassination"></a>TCP TIME_WAIT e TIME_WAIT assassination

TCP TIME_WAIT é outra configuração comum que afeta o desempenho de rede e de aplicativo. Em VMs ocupadas que estão abrindo e fechando muitos soquetes, como clientes ou servidores (origem IP:Source porta + porta IP:Destination de destino), durante a operação normal do TCP, um soquete determinado pode terminar em um estado TIME_WAIT por um longo tempo. O estado TIME_WAIT destina-se para permitir que todos os dados adicionais a serem entregues em um soquete antes de fechá-lo. Portanto, as pilhas de TCP/IP geralmente impedir a reutilização de um soquete soltando silenciosamente um pacote do cliente TCP SYN.

A quantidade de tempo que um soquete está em TIME_WAIT é configurável. Ele pode variar de 30 segundos a 240 segundos. Soquetes são um recurso finito, e o número de soquetes que pode ser usado em um determinado momento é configurável. (O número de soquetes disponíveis normalmente é cerca de 30.000.) Se os soquetes disponíveis são consumidos, ou se os clientes e servidores têm configurações de TIME_WAIT incompatíveis e uma VM tentar reutilizar um soquete em um estado TIME_WAIT, novas conexões falharão como TCP SYN silenciosamente os pacotes são descartados.

O valor para o intervalo de portas para soquetes é geralmente configurável dentro da pilha de TCP/IP de um sistema operacional. O mesmo é verdadeiro para as configurações de TCP TIME_WAIT e reutilização de soquete. Alterar esses números pode melhorar a escalabilidade. Mas, dependendo da situação, essas alterações pode causar problemas de interoperabilidade. Você deve ter cuidado se você alterar esses valores.

Você pode usar assassination TIME_WAIT para resolver essa limitação de colocação em escala. Assassination TIME_WAIT permite que um soquete a ser reutilizado em determinadas situações, como quando o número de sequência no pacote de IP a nova conexão excede o número de sequência do último pacote da conexão anterior. Nesse caso, o sistema operacional permitirá que a nova conexão seja estabelecida (ele aceitará o nova SYN/ACK) e feche o força a conexão anterior que estava em um estado TIME_WAIT. Esse recurso tem suporte em VMs do Windows no Azure. Para saber mais sobre o suporte a outras VMs, verifique com o fornecedor do sistema operacional.

Para saber mais sobre como configurar as configurações de TCP TIME_WAIT e intervalo de porta de origem, consulte [as configurações que podem ser modificadas para melhorar o desempenho de rede](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Fatores de rede virtual que podem afetar o desempenho

### <a name="vm-maximum-outbound-throughput"></a>Produtividade de saída máxima de VM

O Azure fornece uma variedade de tamanhos de VM e tipos, cada um com uma combinação diferente de funcionalidades de desempenho. Um desses recursos é rede taxa de transferência (ou largura de banda) que é medida em megabits por segundo (Mbps). Como as máquinas virtuais são hospedadas no hardware compartilhado, a capacidade de rede precisa ser compartilhada adequadamente entre as máquinas virtuais usando o mesmo hardware. Máquinas virtuais maiores são alocadas mais largura de banda que máquinas virtuais menores.

A largura de banda de rede alocada a cada máquina virtual é limitada no tráfego de saída (saída) da máquina virtual. Todo o tráfego de rede que deixa a máquina virtual é contado para o limite alocado, independentemente do destino. Por exemplo, se uma máquina virtual tem um limite de 1.000 Mbps, esse limite se aplica se o tráfego de saída destina-se a outra máquina virtual na mesma rede virtual ou um fora do Azure.

A entrada não é limitada diretamente. Mas há outros fatores, como limites de CPU e armazenamento, que podem afetar a capacidade de uma máquina virtual de processar dados de entrada.

A rede acelerada é projetada para melhorar o desempenho de rede, incluindo latência, taxa de transferência e utilização da CPU. Rede acelerada possa melhorar a taxa de transferência de uma máquina virtual, mas ele pode fazer isso somente até a largura de banda alocada da máquina virtual.

Máquinas virtuais do Azure têm pelo menos um adaptador de rede anexado a eles. Elas podem ter várias. A largura de banda alocada a uma máquina virtual é a soma de todo o tráfego de saída de todas as interfaces de rede anexadas à máquina. Em outras palavras, a largura de banda é alocada em uma base por-virtual machine, independentemente de quantos adaptadores de rede estão anexados à máquina.

Taxa de transferência de saída esperada e o número de interfaces de rede com suporte em cada tamanho VM são detalhados em [máquinas virtuais de tamanhos para Windows no Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Para ver a taxa de transferência máxima, selecione um tipo, como **propósito geral**e, em seguida, localize a seção sobre a série de tamanho na página resultante (por exemplo, "série Dv2"). Para cada série, há uma tabela que fornece especificações de rede na última coluna, que é intitulada "máximo de NICs / esperado (Mbps) da largura de banda de rede."

O limite de taxa de transferência se aplica à máquina virtual. Taxa de transferência não é afetada por estes fatores:

- **Número de adaptadores de rede**: O limite de largura de banda se aplica à soma de todo o tráfego de saída da máquina virtual.

- **Rede acelerada**: Embora esse recurso pode ser útil para alcançar o limite publicado, ele não altera o limite.

- **Destino do tráfego**: Todos os destinos contam para o limite de saída.

- **Protocolo**: Todo o tráfego de saída em todos os protocolos conta para o limite.

Para obter mais informações, consulte [largura de banda de rede de máquina Virtual](http://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Considerações sobre o desempenho da Internet

Como discutido neste artigo, fatores na internet e fora do controle do Azure podem afetar o desempenho da rede. Aqui estão alguns desses fatores:

- **Latência**: O tempo de ida e volta entre dois destinos pode ser afetado por problemas em redes intermediários, o tráfego que não usa o caminho de distância "mais curto" e caminhos de emparelhamento abaixo do ideal.

- **Perda de pacotes**: Perda de pacotes pode ser causada pelo congestionamento de rede, problemas de caminho físico e com baixo desempenho de dispositivos de rede.

- **Fragmentação da tamanho MTU**: A fragmentação ao longo do caminho pode levar a atrasos na chegada de dados ou em pacotes que chegam fora de ordem, que podem afetar a entrega de pacotes.

Rastreamento de rotas é uma boa ferramenta para medir as características de desempenho de rede (como perda de pacotes e latência) ao longo de cada caminho de rede entre um dispositivo de origem e um dispositivo de destino.

### <a name="network-design-considerations"></a>Considerações de design de rede

Junto com as considerações discutidas neste artigo, a topologia de uma rede virtual pode afetar o desempenho da rede. Por exemplo, um design de hub e spoke que backhauls tráfego global para uma rede virtual de hub único apresentará a latência de rede, o que afetará o desempenho geral da rede.

O número de dispositivos de rede que passa do tráfego de rede também pode afetar a latência geral. Por exemplo, em um design de hub e spoke, se o tráfego passa por um dispositivo de rede virtual spoke e um dispositivo virtual de hub antes de transitar à internet, dispositivos de rede virtual podem introduzir latência.

### <a name="azure-regions-virtual-networks-and-latency"></a>Regiões do Azure, redes virtuais e latência

Regiões do Azure são compostas de vários data centers que existem dentro de uma área geográfica geral. Esses datacenters podem não estar fisicamente próximos uns dos outros. Em alguns casos, eles são separados por até 10 km. A rede virtual é uma sobreposição de lógica em cima da rede de datacenter físico do Azure. Uma rede virtual não implica em qualquer topologia de rede específico dentro do datacenter.

Por exemplo, duas VMs que estão na mesma rede virtual e sub-rede podem ser em diferentes racks, linhas ou até mesmo datacenters. Eles podem ser separados por metros de cabo de fibra ótica ou quilômetros do cabo de fibra óptica. Essa variação pode introduzir latência variável (diferença de alguns milissegundos) entre diferentes VMs.

O posicionamento geográfico de VMs e a latência resultante possíveis entre duas VMs, podem ser influenciados pela configuração de zonas de disponibilidade e conjuntos de disponibilidade. Mas a distância entre os datacenters em uma região específica da região e influenciou principalmente pela topologia de data center na região.

### <a name="source-nat-port-exhaustion"></a>Esgotamento de porta NAT de origem

Uma implantação no Azure pode se comunicar com pontos de extremidade fora do Azure na internet pública e/ou no espaço de IP público. Quando uma instância inicia uma conexão de saída, o Azure mapeia dinamicamente o endereço IP privado para um endereço IP público. Depois que o Azure cria esse mapeamento, o tráfego de retorno para o fluxo originado de saída também pode acessar o endereço IP privado onde o fluxo foi originado.

Para cada conexão de saída, o balanceador de carga do Azure precisa manter esse mapeamento para um determinado período de tempo. Com a natureza de vários locatários do Azure, manter esse mapeamento para cada fluxo de saída para cada VM pode ser recurso intensivo. Portanto, há limites que são definidos e com base na configuração da rede Virtual do Azure. Ou, digamos que, mais precisamente, uma VM do Azure só pode fazer um determinado número de conexões de saída em um determinado momento. Quando esses limites são atingidos, a VM não será capaz de fazer conexões de saída mais.

Mas esse comportamento é configurável. Para obter mais informações sobre o SNAT e SNAT a porta esgotamento, consulte [deste artigo](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Medir o desempenho de rede no Azure

Um número dos limites máximos de desempenho neste artigo está relacionado à latência de rede / ida e volta (RTT) de tempo entre duas VMs. Esta seção fornece algumas sugestões sobre como testar a latência/RTT e como testar o desempenho de TCP e o desempenho da rede VM. Você pode ajustar e os valores de TCP/IP e rede discutidos anteriormente, usando as técnicas descritas nesta seção de teste de desempenho. Você pode conectar valores de tamanho de janela, a MTU, MSS e latência em cálculos fornecidos anteriormente e comparar máximos teóricos com os valores reais que você observa durante o teste.

### <a name="measure-round-trip-time-and-packet-loss"></a>Medir o tempo de ida e volta e perda de pacotes

Desempenho de TCP depende intensamente RTT e a perda de pacotes. O utilitário PING disponível no Windows e Linux fornece a maneira mais fácil de medir RTT e perda de pacotes. A saída do PING mostrará a latência de mínimo/máximo/médio entre a origem e destino. Ela também mostrará a perda de pacotes. Por padrão, o PING usa o protocolo ICMP. Você pode usar PsPing para testar TCP RTT. Para obter mais informações, consulte [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Taxa de transferência real de uma conexão TCP medida

NTttcp é uma ferramenta de teste de desempenho de TCP de uma VM Linux ou Windows. Você pode alterar várias configurações de TCP e, em seguida, testar os benefícios ao usar o NTttcp. Para obter mais informações, consulte estes recursos:

- [Largura de banda/taxa de transferência (NTttcp) de teste](https://aka.ms/TestNetworkThroughput)

- [Utilitário NTttcp](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Medida largura de banda real de uma máquina virtual

Você pode testar o desempenho de diferentes tipos de VM, accelerated networking e assim por diante, usando uma ferramenta chamada iPerf. o iPerf também está disponível no Linux e Windows. o iPerf pode usar o TCP ou UDP para testar a taxa de transferência de rede geral. testes de taxa de transferência TCP iPerf são influenciados pelos fatores discutidos neste artigo (como latência e RTT). Portanto, o UDP pode produzem resultados melhores se você quiser apenas testar a taxa de transferência máxima.

Para obter mais informações, consulte estes artigos:

- [Solucionando problemas de desempenho de rede de Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Como validar a taxa de transferência VPN para uma rede virtual](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Detectar comportamentos TCP ineficientes

Na captura de pacote, os clientes do Azure poderá ver os pacotes TCP com sinalizadores TCP (SACK, DUP ACK, retransmissão e rápida de retransmissão) que podem indicar problemas de desempenho de rede. Esses pacotes indicam especificamente ineficiências de rede resultantes da perda de pacotes. Mas a perda de pacotes não é necessariamente causada por problemas de desempenho do Azure. Problemas de desempenho pode ser o resultado de problemas de aplicativos, problemas de sistema operacional ou outros problemas que podem não estar diretamente relacionados com a plataforma do Azure.

Além disso, tenha em mente que alguns retransmissão e ACKs duplicados são normais em uma rede. Protocolos TCP foram criados para ser confiável. Evidência desses pacotes de TCP em uma captura de pacote não indica necessariamente um problema de rede sistemática, a menos que sejam excessivas.

Ainda assim, esses tipos de pacote são indicações de que a taxa de transferência TCP não estiver obtendo o máximo desempenho, pelos motivos discutidos em outras seções deste artigo.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre ajuste de desempenho de TCP/IP para VMs do Azure, você talvez queira ler sobre outras considerações para o [planejamento de redes virtuais](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-vnet-plan-design-arm) ou [Saiba mais sobre como conectar e configurar as redes virtuais ](https://docs.microsoft.com/en-us/azure/virtual-network/).
