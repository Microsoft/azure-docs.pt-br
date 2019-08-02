---
title: Ajuste de desempenho de TCP/IP para VMs do Azure | Microsoft Docs
description: Conheça várias técnicas comuns de ajuste de desempenho de TCP/IP e sua relação com as VMs do Azure.
services: virtual-network
documentationcenter: na
author: rimayber
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: rimayber
ms.reviewer: dgoddard, stegag, steveesp, minale, btalb, prachank
ms.openlocfilehash: bb23484903ac3ce129c6e7a7a27e0765c227fb1d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297780"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>Ajuste de desempenho de TCP/IP para VMs do Azure

Este artigo discute técnicas comuns de ajuste de desempenho de TCP/IP e algumas coisas a serem consideradas ao usá-las para máquinas virtuais em execução no Azure. Ele fornecerá uma visão geral básica das técnicas e explorará como elas podem ser ajustadas.

## <a name="common-tcpip-tuning-techniques"></a>Técnicas comuns de ajuste de TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU, fragmentação e descarregamento de envio grande

#### <a name="mtu"></a>MTU

A MTU (unidade máxima de transmissão) é o maior quadro de tamanho (pacote), especificado em bytes, que pode ser enviado por uma interface de rede. A MTU é uma configuração configurável. A MTU padrão usada nas VMs do Azure e a configuração padrão na maioria dos dispositivos de rede globalmente é de 1.500 bytes.

#### <a name="fragmentation"></a>Fragmentação

A fragmentação ocorre quando um pacote é enviado que excede o MTU de uma interface de rede. A pilha de TCP/IP interromperá o pacote em pedaços menores (fragmentos) que estão em conformidade com a MTU da interface. A fragmentação ocorre na camada de IP e é independente do protocolo subjacente (como TCP). Quando um pacote de 2.000 bytes é enviado por uma interface de rede com um MTU de 1.500, o pacote será dividido em pacote de 1 1.500 bytes e pacote de 1 500 bytes.

Os dispositivos de rede no caminho entre uma origem e um destino podem soltar os pacotes que excedem a MTU ou fragmentar o pacote em partes menores.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>O bit não fragmentar em um pacote IP

O bit não fragmentar (DF) é um sinalizador no cabeçalho do protocolo IP. O bit DF indica que os dispositivos de rede no caminho entre o remetente e o receptor não devem fragmentar o pacote. Esse bit pode ser definido por vários motivos. (Consulte a seção "descoberta de MTU de caminho" deste artigo para ver um exemplo.) Quando um dispositivo de rede recebe um pacote com o conjunto de bits não fragmentado e esse pacote excede a MTU da interface do dispositivo, o comportamento padrão é para o dispositivo descartar o pacote. O dispositivo envia uma mensagem de fragmentação de ICMP necessária de volta para a fonte original do pacote.

#### <a name="performance-implications-of-fragmentation"></a>Implicações de desempenho da fragmentação

A fragmentação pode ter implicações de desempenho negativas. Uma das principais razões para o efeito no desempenho é o impacto da CPU/memória da fragmentação e da remontagem dos pacotes. Quando um dispositivo de rede precisar fragmentar um pacote, ele precisará alocar recursos de CPU/memória para executar a fragmentação.

A mesma coisa acontece quando o pacote é remontado. O dispositivo de rede precisa armazenar todos os fragmentos até que eles sejam recebidos para que possam remontá-los no pacote original. Esse processo de fragmentação e remontagem também pode causar latência.

A outra possível implicação negativa de desempenho da fragmentação é que os pacotes fragmentados podem chegar fora de ordem. Quando os pacotes são recebidos fora de ordem, alguns tipos de dispositivos de rede podem descartá-los. Quando isso acontece, o pacote inteiro precisa ser retransmitido.

Os fragmentos normalmente são descartados por dispositivos de segurança como firewalls de rede ou quando os buffers de recebimento de um dispositivo de rede são esgotados. Quando os buffers de recebimento de um dispositivo de rede são esgotados, um dispositivo de rede está tentando remontar um pacote fragmentado, mas não tem os recursos para armazenar e reassumir o pacote.

A fragmentação pode ser vista como uma operação negativa, mas o suporte à fragmentação é necessário quando você está conectando diversas redes pela Internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Benefícios e consequências de modificar a MTU

Em geral, você pode criar uma rede mais eficiente aumentando a MTU. Cada pacote transmitido tem informações de cabeçalho que são adicionadas ao pacote original. Quando a fragmentação cria mais pacotes, há mais sobrecarga de cabeçalho e isso torna a rede menos eficiente.

Aqui está um exemplo. O tamanho do cabeçalho de Ethernet é de 14 bytes mais uma sequência de verificação de quadro de 4 bytes para garantir a consistência do quadro. Se o pacote de 1 2.000 bytes for enviado, 18 bytes de sobrecarga de Ethernet serão adicionados na rede. Se o pacote estiver fragmentado em um pacote de 1.500 bytes e um pacote de 500 bytes, cada pacote terá 18 bytes de cabeçalho Ethernet, um total de 36 bytes.

Tenha em mente que o aumento da MTU não criará, necessariamente, uma rede mais eficiente. Se um aplicativo enviar somente pacotes de 500 bytes, a mesma sobrecarga de cabeçalho existirá se o MTU for 1.500 bytes ou 9.000 bytes. A rede se tornará mais eficiente apenas se usar tamanhos de pacotes maiores que são afetados pela MTU.

#### <a name="azure-and-vm-mtu"></a>Azure e MTU de VM

O MTU padrão para VMs do Azure é de 1.500 bytes. A pilha de rede virtual do Azure tentará fragmentar um pacote em 1.400 bytes.

Observe que a pilha de rede virtual não é inerentemente ineficiente porque fragmentos de pacotes a 1.400 bytes, embora as VMs tenham um MTU de 1.500. Um grande percentual de pacotes de rede é muito menor do que 1.400 ou 1.500 bytes.

#### <a name="azure-and-fragmentation"></a>Azure e fragmentação

A pilha de rede virtual é configurada para descartar "fragmentos fora de ordem", ou seja, pacotes fragmentados que não chegam em sua ordem fragmentada original. Esses pacotes são descartados principalmente devido a uma vulnerabilidade de segurança de rede anunciada em novembro de 2018 chamada FragmentSmack.

FragmentSmack é um defeito na maneira como o kernel do Linux tratou de remontagem de pacotes IPv4 e IPv6 fragmentados. Um invasor remoto pode usar essa falha para disparar operações caras de remontagem de fragmento, o que poderia levar a uma CPU maior e a uma negação de serviço no sistema de destino.

#### <a name="tune-the-mtu"></a>Ajustar a MTU

Você pode configurar uma MTU de VM do Azure, como você pode em qualquer outro sistema operacional. Mas você deve considerar a fragmentação que ocorre no Azure, descrita acima, quando você está configurando uma MTU.

Não incentivamos os clientes a aumentar os MTUs da VM. Esta discussão destina-se a explicar os detalhes de como o Azure implementa a MTU e executa a fragmentação.

> [!IMPORTANT]
>O aumento de MTU não é conhecido por melhorar o desempenho e pode ter um efeito negativo no desempenho do aplicativo.
>
>

#### <a name="large-send-offload"></a>Descarregamento de envio grande

O LSO (carregamento de envio grande) pode melhorar o desempenho da rede descarregando a segmentação de pacotes para o adaptador Ethernet. Quando o LSO é habilitado, a pilha TCP/IP cria um pacote TCP grande e o envia ao adaptador Ethernet para segmentação antes de encaminhá-lo. O benefício do LSO é que ele pode liberar a CPU de segmentar pacotes em tamanhos que estão de acordo com a MTU e descarregar o processamento para a interface Ethernet onde ele é executado no hardware. Para saber mais sobre os benefícios do LSO, consulte [suporte a descarregamento de envio grande](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Quando o LSO está habilitado, os clientes do Azure podem ver tamanhos de quadros grandes quando executam capturas de pacote. Esses grandes tamanhos de quadros podem levar alguns clientes a acreditar que a fragmentação está ocorrendo ou que uma MTU grande está sendo usada quando não está. Com o LSO, o adaptador Ethernet pode anunciar um MSS (tamanho máximo de segmento) maior para a pilha TCP/IP para criar um pacote TCP maior. Em seguida, esse quadro não segmentado inteiro é encaminhado para o adaptador Ethernet e fica visível em uma captura de pacote executada na VM. Mas o pacote será dividido em vários quadros menores pelo adaptador Ethernet, de acordo com o MTU do adaptador Ethernet.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>Dimensionamento e PMTUD da janela TCP MSS

#### <a name="tcp-maximum-segment-size"></a>Tamanho máximo de segmento TCP

O MSS (tamanho máximo de segmento) do TCP é uma configuração que limita o tamanho dos segmentos TCP, o que evita a fragmentação de pacotes TCP. Os sistemas operacionais geralmente usarão essa fórmula para definir o MSS:

`MSS = MTU - (IP header size + TCP header size)`

O cabeçalho IP e o cabeçalho TCP têm 20 bytes cada, ou 40 bytes de total. Portanto, uma interface com um MTU de 1.500 terá um MSS de 1.460. Mas o MSS é configurável.

Essa configuração é acordada no handshake de três vias TCP quando uma sessão TCP é configurada entre uma origem e um destino. Ambos os lados enviam um valor de MSS e o menor dos dois é usado para a conexão TCP.

Tenha em mente que os MTUs da origem e do destino não são os únicos fatores que determinam o valor de MSS. Os dispositivos de rede intermediários, como gateways de VPN, incluindo o gateway de VPN do Azure, podem ajustar o MTU independentemente da origem e do destino para garantir o desempenho de rede ideal.

#### <a name="path-mtu-discovery"></a>Descoberta de MTU de caminho

O MSS é negociado, mas pode não indicar o MSS real que pode ser usado. Isso ocorre porque outros dispositivos de rede no caminho entre a origem e o destino podem ter um valor de MTU menor do que a origem e o destino. Nesse caso, o dispositivo cuja MTU é menor do que o pacote removerá o pacote. O dispositivo enviará de volta uma mensagem de fragmentação ICMP necessária (tipo 3, código 4) que contém seu MTU. Essa mensagem ICMP permite que o host de origem reduza seu MTU de caminho adequadamente. O processo é chamado de PMTUD (descoberta de MTU de caminho).

O processo PMTUD é ineficiente e afeta o desempenho da rede. Quando são enviados pacotes que excedem o MTU de um caminho de rede, os pacotes precisam ser retransmitidos com um MSS inferior. Se o remetente não receber a mensagem de fragmentação de ICMP necessária, talvez devido a um firewall de rede no caminho (comumente chamado de *Blackhole do PMTUD*), o remetente não saberá que ele precisa reduzir o MSS e retransmitirá o pacote continuamente. É por isso que não recomendamos aumentar a MTU de VM do Azure.

#### <a name="vpn-and-mtu"></a>VPN e MTU

Se você usar VMs que executam encapsulamento (como VPNs IPsec), há algumas considerações adicionais sobre o tamanho do pacote e a MTU. As VPNs adicionam mais cabeçalhos aos pacotes, o que aumenta o tamanho do pacote e requer um MSS menor.

Para o Azure, recomendamos que você defina TCP MSS fixação MSS como 1.350 bytes e MTU da interface de túnel como 1.400. Para obter mais informações, consulte a [página dispositivos VPN e parâmetros de IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Latência, tempo de viagem de ida e volta e dimensionamento de janela TCP

#### <a name="latency-and-round-trip-time"></a>Latência e tempo de ida e volta

A latência de rede é regida pela velocidade de luz em uma rede de fibra óptica. A taxa de transferência de rede do TCP também é efetivamente controlada pelo RTT (tempo de ida e volta) entre dois dispositivos de rede.

| | | | |
|-|-|-|-|
|**Route**|**Alcance**|**Tempo unidirecional**|**RTT**|
|Nova York a São Francisco|4\.148 km|21 ms|42 ms|
|Nova York para Londres|5\.585 km|28 ms|56 ms|
|Nova York para Sydney|15.993 km|80 ms|160 ms|

Esta tabela mostra a distância de linha reta entre dois locais. Em redes, a distância normalmente é maior do que a distância de linha reta. Aqui está uma fórmula simples para calcular o mínimo de RTT como governado pela velocidade de luz:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

Você pode usar 200 para a velocidade de propagação. Essa é a distância, em metros, que a luz viaja em 1 milissegundo.

Vamos pegar uma Nova York a San Francisco como exemplo. A distância de linha reta é de 4.148 km. Ao conectar esse valor à equação, obtemos o seguinte:

`Minimum RTT = 2 * (4,148 / 200)`

A saída da equação está em milissegundos.

Se você quiser obter o melhor desempenho de rede, a opção lógica é selecionar destinos com a distância mais curta entre eles. Você também deve projetar sua rede virtual para otimizar o caminho do tráfego e reduzir a latência. Para obter mais informações, consulte a seção "considerações de design de rede" deste artigo.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Latência e efeitos de tempo de ida e volta no TCP

O tempo de ida e volta tem um efeito direto na taxa de transferência máxima de TCP. No protocolo TCP, *tamanho da janela* é a quantidade máxima de tráfego que pode ser enviada por uma conexão TCP antes que o remetente precise receber a confirmação do receptor. Se o TCP MSS estiver definido como 1.460 e o tamanho da janela TCP for definido como 65.535, o remetente poderá enviar pacotes 45 antes de receber a confirmação do receptor. Se o remetente não receber a confirmação, ele retransmitirá os dados. Esta é a fórmula:

`TCP window size / TCP MSS = packets sent`

Neste exemplo, 65.535/1.460 é arredondado para 45.

Esse estado "aguardando confirmação", um mecanismo para garantir a entrega confiável de dados, é o que faz com que o RTT afete a taxa de transferência de TCP. Quanto mais tempo o remetente aguardar a confirmação, mais tempo ele precisará aguardar antes de enviar mais dados.

Aqui está a fórmula para calcular a taxa de transferência máxima de uma única conexão TCP:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Esta tabela mostra a taxa de transferência máxima de megabytes/por segundo de uma única conexão TCP. (Para facilitar a leitura, é usado megabytes para a unidade de medida.)

| | | | |
|-|-|-|-|
|**Tamanho da janela TCP (bytes)**|**Latência de RTT (MS)**|**Taxa de transferência máxima de megabytes/segundo**|**Taxa de transferência máxima de megabits/segundo**|
|65.535|1|65,54|524,29|
|65.535|30|2,18|17,48|
|65.535|60|1.09|8,74|
|65.535|90|.73|5,83|
|65.535|120|.55|4.37|

Se os pacotes forem perdidos, a taxa de transferência máxima de uma conexão TCP será reduzida enquanto o remetente retransmite os dados já enviados.

#### <a name="tcp-window-scaling"></a>Dimensionamento da janela TCP

O dimensionamento da janela TCP é uma técnica que aumenta dinamicamente o tamanho da janela TCP para permitir que mais dados sejam enviados antes que uma confirmação seja necessária. No exemplo anterior, 45 pacotes seriam enviados antes que uma confirmação fosse necessária. Se você aumentar o número de pacotes que podem ser enviados antes que uma confirmação seja necessária, você está reduzindo o número de vezes que um remetente está aguardando a confirmação, o que aumenta a taxa de transferência máxima de TCP.

Esta tabela ilustra essas relações:

| | | | |
|-|-|-|-|
|**Tamanho da janela TCP (bytes)**|**Latência de RTT (MS)**|**Taxa de transferência máxima de megabytes/segundo**|**Taxa de transferência máxima de megabits/segundo**|
|65.535|30|2,18|17,48|
|131.070|30|4.37|34,95|
|262.140|30|8,74|69,91|
|524.280|30|17,48|139,81|

Mas o valor do cabeçalho TCP para o tamanho da janela TCP é de apenas 2 bytes, o que significa que o valor máximo para uma janela de recepção é 65.535. Para aumentar o tamanho máximo da janela, um fator de escala de janela TCP foi introduzido.

O fator de escala também é uma configuração que você pode configurar em um sistema operacional. Aqui está a fórmula para calcular o tamanho da janela TCP usando fatores de escala:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Este é o cálculo de um fator de escala da janela de 3 e um tamanho de janela de 65.535:

`65,535 \* (2^3) = 262,140 bytes`

Um fator de escala de 14 resulta em um tamanho de janela TCP de 14 (o deslocamento máximo permitido). O tamanho da janela TCP será de 1.073.725.440 bytes (8,5 gigabits).

#### <a name="support-for-tcp-window-scaling"></a>Suporte para dimensionamento de janela TCP

O Windows pode definir fatores de dimensionamento diferentes para tipos de conexão diferentes. (As classes de conexões incluem datacenter, Internet e assim por diante.) Use o comando `Get-NetTCPConnection` do PowerShell para exibir o tipo de conexão de dimensionamento de janela:

```powershell
Get-NetTCPConnection
```

Você pode usar o `Get-NetTCPSetting` comando do PowerShell para exibir os valores de cada classe:

```powershell
Get-NetTCPSetting
```

Você pode definir o tamanho inicial da janela TCP e o fator de dimensionamento TCP no `Set-NetTCPSetting` Windows usando o comando do PowerShell. Para obter mais informações, consulte [set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Essas são as configurações de TCP efetivas para `AutoTuningLevel`:

| | | | |
|-|-|-|-|
|**AutoTuningLevel**|**Fator de dimensionamento**|**Multiplicador de dimensionamento**|**Fórmula para<br/>calcular o tamanho máximo da janela**|
|Desabilitado|Nenhum|Nenhum|Tamanho da janela|
|Restrito|4|2^4|Tamanho da janela * (2 ^ 4)|
|Altamente restrito|2|2^2|Tamanho da janela * (2 ^ 2)|
|Normal|8|2^8|Tamanho da janela * (2 ^ 8)|
|Habilitação|14|2^14|Tamanho da janela * (2 ^ 14)|

Essas configurações são mais prováveis de afetar o desempenho do TCP, mas tenha em mente que muitos outros fatores na Internet, fora do controle do Azure, também podem afetar o desempenho do TCP.

#### <a name="increase-mtu-size"></a>Aumentar tamanho de MTU

Como um MTU maior significa um MSS maior, você pode se perguntar se o aumento da MTU pode aumentar o desempenho do TCP. Provavelmente, não. Há prós e contras no tamanho do pacote além do tráfego TCP. Conforme discutido anteriormente, os fatores mais importantes que afetam o desempenho da taxa de transferência de TCP são o tamanho da janela TCP, a perda de pacotes e o RTT.

> [!IMPORTANT]
> Não recomendamos que os clientes do Azure alterem o valor padrão de MTU em máquinas virtuais.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Rede acelerada e recebimento de escala lateral

#### <a name="accelerated-networking"></a>Redes aceleradas

As funções de rede de máquina virtual têm historicamente o uso intensivo da CPU na VM convidada e no hipervisor/host. Cada pacote que está em trânsito pelo host é processado no software pela CPU do host, incluindo todos os encapsulamentos de rede virtual e desencapsulamento. Portanto, quanto mais tráfego passar pelo host, maior será a carga da CPU. E se a CPU do host estiver ocupada com outras operações, isso também afetará a taxa de transferência e a latência da rede. O Azure resolve esse problema com a rede acelerada.

A rede acelerada fornece latência de rede ultralow consistente por meio do hardware programável interno do Azure e de tecnologias como SR-IOV. A rede acelerada move grande parte da pilha de rede definida pelo software do Azure para fora das CPUs e para o SmartNICs baseado em FPGA. Essa alteração permite que os aplicativos do usuário final recuperem ciclos de computação, o que coloca menos carga na VM, diminuindo a tremulação e inconsistência na latência. Em outras palavras, o desempenho pode ser mais determinístico.

A rede acelerada melhora o desempenho, permitindo que a VM convidada ignore o host e estabeleça um DataPath diretamente com o SmartNIC de um host. Aqui estão alguns benefícios da rede acelerada:

- **Latência mais baixa/pacotes maiores por segundo (PPS)** : A remoção do comutador virtual do caminho de data elimina o tempo que os pacotes gastam no host para o processamento da política e aumenta o número de pacotes que podem ser processados na VM.

- **Variação reduzida**: O processamento do comutador virtual depende da quantidade de política que precisa ser aplicada e da carga de trabalho da CPU que está fazendo o processamento. O descarregamento da imposição de política para o hardware remove essa variabilidade ao entregar pacotes diretamente à VM, eliminando a comunicação de host para VM e todas as interrupções de software e alternâncias de contexto.

- **Utilização de CPU reduzida**: ignorar o comutador virtual no host resulta em menor utilização da CPU para processar o tráfego de rede.

Para usar a rede acelerada, você precisa habilitá-la explicitamente em cada VM aplicável. Confira [criar uma máquina virtual Linux com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) para obter instruções.

#### <a name="receive-side-scaling"></a>Receber dimensionamento lateral

O RSS (recebimento de escala lateral) é uma tecnologia de driver de rede que distribui o recebimento de tráfego de rede com mais eficiência, distribuindo o processamento de recebimento entre várias CPUs em um sistema multiprocessador. Em termos simples, o RSS permite que um sistema processe o tráfego mais recebido porque ele usa todas as CPUs disponíveis em vez de apenas uma. Para obter uma discussão mais técnica sobre o RSS, consulte [introdução ao dimensionamento do lado de recebimento](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

Para obter o melhor desempenho quando a rede acelerada está habilitada em uma VM, você precisa habilitar o RSS. O RSS também pode fornecer benefícios em VMs que não usam rede acelerada. Para obter uma visão geral de como determinar se o RSS está habilitado e como habilitá-lo, consulte [otimizar a taxa de transferência de rede para máquinas virtuais do Azure](https://aka.ms/FastVM).

### <a name="tcp-timewait-and-timewait-assassination"></a>TCP TIME_WAIT e TIME_WAIT Assassination

O TCP TIME_WAIT é outra configuração comum que afeta o desempenho da rede e do aplicativo. Em VMs ocupadas que estão abrindo e fechando muitos soquetes, seja como clientes ou como servidores (IP de origem: porta de origem + IP de destino: porta de destino), durante a operação normal do TCP, um determinado soquete pode terminar em um estado TIME_WAIT por muito tempo. O estado TIME_WAIT destina-se a permitir que quaisquer dados adicionais sejam entregues em um soquete antes de fechá-lo. Portanto, as pilhas de TCP/IP geralmente impedem a reutilização de um soquete ao remover silenciosamente o pacote TCP SYN do cliente.

A quantidade de tempo em que um soquete está em TIME_WAIT é configurável. Ele pode variar de 30 segundos a 240 segundos. Os soquetes são um recurso finito e o número de soquetes que podem ser usados em um determinado momento é configurável. (O número de soquetes disponíveis normalmente é cerca de 30.000.) Se os soquetes disponíveis forem consumidos ou se os clientes e servidores tiverem configurações de TIME_WAIT incompatíveis e uma VM tentar reutilizar um soquete em um estado de TIME_WAIT, novas conexões falharão, pois os pacotes TCP SYN serão silenciosamente descartados.

O valor para o intervalo de portas para soquetes de saída geralmente é configurável na pilha TCP/IP de um sistema operacional. A mesma coisa é verdadeira para configurações de TIME_WAIT de TCP e reutilização de soquete. Alterar esses números pode potencialmente melhorar a escalabilidade. Mas, dependendo da situação, essas alterações podem causar problemas de interoperabilidade. Você deve ter cuidado se alterar esses valores.

Você pode usar TIME_WAIT Assassination para resolver essa limitação de dimensionamento. TIME_WAIT Assassination permite que um soquete seja reutilizado em determinadas situações, como quando o número de sequência no pacote IP da nova conexão excede o número de sequência do último pacote da conexão anterior. Nesse caso, o sistema operacional permitirá que a nova conexão seja estabelecida (ele aceitará o novo SYN/ACK) e forçará o fechamento da conexão anterior que estava em um estado TIME_WAIT. Esse recurso tem suporte em VMs do Windows no Azure. Para saber mais sobre o suporte em outras VMs, verifique com o fornecedor do sistema operacional.

Para saber mais sobre como definir as configurações de TIME_WAIT de TCP e o intervalo de portas de origem, consulte [configurações que podem ser modificadas para melhorar o desempenho da rede](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Fatores de rede virtual que podem afetar o desempenho

### <a name="vm-maximum-outbound-throughput"></a>Taxa de transferência máxima de saída da VM

O Azure fornece uma variedade de tamanhos e tipos de VM, cada um com uma combinação diferente de recursos de desempenho. Um desses recursos é a taxa de transferência de rede (ou largura de banda), que é medida em megabits por segundo (Mbps). Como as máquinas virtuais são hospedadas em hardware compartilhado, a capacidade de rede precisa ser compartilhada bem entre as máquinas virtuais usando o mesmo hardware. Máquinas virtuais maiores são alocadas mais largura de banda do que máquinas virtuais menores.

A largura de banda de rede alocada a cada máquina virtual é limitada no tráfego de saída (saída) da máquina virtual. Todo o tráfego de rede que deixa a máquina virtual é contado para o limite alocado, independentemente do destino. Por exemplo, se uma máquina virtual tiver um limite de 1.000 Mbps, esse limite se aplicará se o tráfego de saída for destinado a outra máquina virtual na mesma rede virtual ou uma fora do Azure.

A entrada não é limitada diretamente. Mas há outros fatores, como limites de CPU e de armazenamento, que podem afetar a capacidade de uma máquina virtual de processar dados de entrada.

A rede acelerada foi projetada para melhorar o desempenho da rede, incluindo latência, taxa de transferência e utilização da CPU. A rede acelerada pode melhorar a taxa de transferência de uma máquina virtual, mas pode fazer isso apenas até a largura de banda alocada da máquina virtual.

As máquinas virtuais do Azure têm pelo menos uma interface de rede anexada a elas. Eles podem ter vários. A largura de banda alocada para uma máquina virtual é a soma de todo o tráfego de saída em todas as interfaces de rede conectadas ao computador. Em outras palavras, a largura de banda é alocada em uma base por máquina virtual, independentemente de quantos adaptadores de rede estão conectados à máquina.

A taxa de transferência de saída esperada e o número de interfaces de rede com suporte de cada tamanho de VM são detalhados em [tamanhos de máquinas virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Para ver a taxa de transferência máxima, selecione um tipo, como **finalidade geral**, e localize a seção sobre a série de tamanho na página resultante (por exemplo, "série Dv2"). Para cada série, há uma tabela que fornece especificações de rede na última coluna, que é intitulada "máximo de NICs/largura de banda de rede esperada (Mbps)".

O limite de taxa de transferência se aplica à máquina virtual. A taxa de transferência não é afetada por esses fatores:

- **Número de interfaces de rede**: O limite de largura de banda se aplica à soma de todo o tráfego de saída da máquina virtual.

- **Rede acelerada**: Embora esse recurso possa ser útil para atingir o limite publicado, ele não altera o limite.

- **Destino do tráfego**: Todos os destinos contam para o limite de saída.

- **Protocolo**: Todo o tráfego de saída em todos os protocolos conta em direção ao limite.

Para obter mais informações, consulte [largura de banda de rede da máquina virtual](https://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Considerações de desempenho da Internet

Conforme discutido em todo este artigo, fatores na Internet e fora do controle do Azure podem afetar o desempenho da rede. Aqui estão alguns desses fatores:

- **Latência**: O tempo de ida e volta entre dois destinos pode ser afetado por problemas em redes intermediárias, pelo tráfego que não assume o caminho de distância "mais curto" e por caminhos de emparelhamento de qualidade inferior.

- **Perda de pacotes**: A perda de pacotes pode ser causada por congestionamento de rede, por problemas de caminho físico e pela subexecução de dispositivos de rede.

- **Tamanho/fragmentação de MTU**: A fragmentação ao longo do caminho pode levar a atrasos na chegada de dados ou em pacotes que chegam fora de ordem, o que pode afetar a entrega de pacotes.

O traceroute é uma boa ferramenta para medir as características de desempenho da rede (como perda e latência de pacotes) ao longo de cada caminho de rede entre um dispositivo de origem e um dispositivo de destino.

### <a name="network-design-considerations"></a>Considerações de design de rede

Junto com as considerações discutidas anteriormente neste artigo, a topologia de uma rede virtual pode afetar o desempenho da rede. Por exemplo, um design de Hub e spoke que redireciona o tráfego globalmente para uma rede virtual de único Hub introduzirá a latência de rede, o que afetará o desempenho geral da rede.

O número de dispositivos de rede que o tráfego de rede passa por meio também pode afetar a latência geral. Por exemplo, em um design de Hub e spoke, se o tráfego passar por uma solução de virtualização de rede spoke e um dispositivo virtual de Hub antes de entrar na Internet, as soluções de virtualização de rede poderão introduzir latência.

### <a name="azure-regions-virtual-networks-and-latency"></a>Regiões do Azure, redes virtuais e latência

As regiões do Azure são formadas por vários data centers que existem dentro de uma área geográfica geral. Esses data centers podem não estar fisicamente próximos um do outro. Em alguns casos, eles são separados por até 10 quilômetros. A rede virtual é uma sobreposição lógica sobre a rede de datacenter física do Azure. Uma rede virtual não implica em nenhuma topologia de rede específica no datacenter.

Por exemplo, duas VMs que estão na mesma rede virtual e sub-rede podem estar em diferentes racks, linhas ou até mesmo data centers. Eles podem ser separados por pés de cabo de fibra óptica ou por quilômetros de cabo de fibra ótica. Essa variação pode introduzir latência variável (algumas diferenças de milissegundos) entre VMs diferentes.

A colocação geográfica das VMs e a possível latência resultante entre duas VMs podem ser influenciadas pela configuração dos conjuntos de disponibilidade e Zonas de Disponibilidade. Mas a distância entre os data centers em uma região é específica da região e, principalmente, influenciada pela topologia do datacenter na região.

### <a name="source-nat-port-exhaustion"></a>Esgotamento de porta NAT de origem

Uma implantação no Azure pode se comunicar com pontos de extremidade fora do Azure na Internet pública e/ou no espaço IP público. Quando uma instância inicia uma conexão de saída, o Azure mapeia dinamicamente o endereço IP privado para um endereço IP público. Depois que o Azure cria esse mapeamento, o tráfego de retorno para o fluxo originado de saída também pode alcançar o endereço IP privado onde o fluxo foi originado.

Para cada conexão de saída, o Azure Load Balancer precisa manter esse mapeamento por um período de tempo. Com a natureza multilocatário do Azure, manter esse mapeamento para cada fluxo de saída para cada VM pode consumir muitos recursos. Portanto, há limites que são definidos e baseados na configuração da rede virtual do Azure. Ou, para dizer que mais precisamente, uma VM do Azure só pode fazer um determinado número de conexões de saída em um determinado momento. Quando esses limites forem atingidos, a VM não poderá fazer mais conexões de saída.

Mas esse comportamento é configurável. Para obter mais informações sobre o esgotamento de porta SNAT e SNAT, consulte [Este artigo](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Medir o desempenho de rede no Azure

Vários dos máximos de desempenho neste artigo estão relacionados à latência de rede/tempo de ida e volta (RTT) entre duas VMs. Esta seção fornece algumas sugestões sobre como testar a latência/RTT e como testar o desempenho do TCP e o desempenho da rede VM. Você pode ajustar e testar o desempenho dos valores de TCP/IP e de rede discutidos anteriormente usando as técnicas descritas nesta seção. Você pode conectar valores de latência, MTU, MSS e tamanho de janela nos cálculos fornecidos anteriormente e comparar os máximos teóricos com os valores reais que você observa durante o teste.

### <a name="measure-round-trip-time-and-packet-loss"></a>Medir o tempo de ida e volta e a perda de pacotes

O desempenho do TCP depende muito do RTT e da perda de pacotes. O utilitário PING disponível no Windows e no Linux fornece a maneira mais fácil de medir a perda de RTT e de pacotes. A saída do PING mostrará a latência mínima/máxima/média entre uma origem e um destino. Ele também mostrará a perda de pacotes. O PING usa o protocolo ICMP por padrão. Você pode usar PsPing para testar o RTT de TCP. Para obter mais informações, consulte [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Medir a taxa de transferência real de uma conexão TCP

NTttcp é uma ferramenta para testar o desempenho de TCP de uma VM Linux ou Windows. Você pode alterar várias configurações de TCP e, em seguida, testar os benefícios usando o NTttcp. Para obter mais informações, consulte estes recursos:

- [NTttcp (largura de banda/teste de taxa de transferência)](https://aka.ms/TestNetworkThroughput)

- [Utilitário NTttcp](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Medir a largura de banda real de uma máquina virtual

Você pode testar o desempenho de diferentes tipos de VM, rede acelerada e assim por diante, usando uma ferramenta chamada iPerf. o iPerf também está disponível no Linux e no Windows. iPerf pode usar TCP ou UDP para testar a taxa de transferência geral da rede. os testes de taxa de transferência TCP do iPerf são influenciados pelos fatores discutidos neste artigo (como latência e RTT). Portanto, o UDP poderá produzir resultados melhores se você quiser apenas testar a taxa de transferência máxima.

Para obter mais informações, consulte estes artigos:

- [Solucionando problemas de desempenho de rede do Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Como validar a taxa de transferência VPN para uma rede virtual](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Detectar comportamentos de TCP ineficientes

Em capturas de pacote, os clientes do Azure podem ver pacotes TCP com sinalizadores TCP (SACK, duplicação de DUP, retransmissão e retransmissão rápida) que podem indicar problemas de desempenho de rede. Esses pacotes indicam especificamente as ineficiências de rede que resultam da perda de pacotes. Mas a perda de pacotes não é necessariamente causada por problemas de desempenho do Azure. Problemas de desempenho podem ser o resultado de problemas de aplicativo, problemas do sistema operacional ou outros problemas que podem não estar diretamente relacionados à plataforma do Azure.

Além disso, tenha em mente que algumas retransmissão e confirmações duplicadas são normais em uma rede. Os protocolos TCP foram criados para serem confiáveis. A evidência desses pacotes TCP em uma captura de pacotes não indica necessariamente um problema de rede do sistema, a menos que eles sejam excessivos.

Ainda assim, esses tipos de pacotes são indicações de que a taxa de transferência de TCP não está atingindo seu desempenho máximo, por motivos discutidos em outras seções deste artigo.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre o ajuste de desempenho de TCP/IP para VMs do Azure, talvez queira ler sobre outras considerações para [planejar redes virtuais](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) ou [saber mais sobre como conectar e configurar redes virtuais](https://docs.microsoft.com/azure/virtual-network/).
