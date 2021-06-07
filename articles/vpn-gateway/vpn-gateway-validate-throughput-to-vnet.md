---
title: Validar a taxa de transferência de VPN para um Rede Virtual do Microsoft Azure
description: Este artigo ajuda você a validar a taxa de transferência de rede de seus recursos locais para uma máquina virtual do Azure.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: 2d5b51e8cfbfcb5f771e9da524231f8ddfc40a9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94660926"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Como validar a taxa de transferência VPN para uma rede virtual

Uma conexão de gateway de VPN permite que você estabeleça conectividade entre instalações segura entre a sua Rede Virtual do Azure e sua infraestrutura de TI local.

Este artigo mostra como validar a taxa de transferência de rede dos recursos locais para uma máquina virtual (VM) do Azure.

> [!NOTE]
> Este artigo destina-se a ajudar a diagnosticar e corrigir problemas comuns. Se você não conseguir solucionar o problema usando as informações a seguir, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="overview"></a>Visão geral

A conexão de gateway de VPN envolve os seguintes componentes:

* Dispositivo VPN local (exiba uma lista de [dispositivos VPN validados](vpn-gateway-about-vpn-devices.md#devicetable).)
* Internet pública
* Gateway de VPN do Azure
* VM do Azure

O diagrama a seguir mostra a conectividade lógica de uma rede local para uma rede virtual do Azure por meio de VPN.

![Conectividade Lógica da Rede do Cliente com a Rede MSFT usando VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Calcule a entrada/saída máxima esperada

1. Determine os requisitos da taxa de transferência de linha de base do aplicativo.
1. Determine os limites de taxa de transferência de gateway de VPN do Azure. Para obter ajuda, consulte a seção "SKUs de gateway" de [sobre o gateway de VPN](vpn-gateway-about-vpngateways.md#gwsku).
1. Determine as [Diretrizes de taxa de transferência para a VM do Azure](../virtual-machines/sizes.md) para seu tamanho de VM.
1. Determine a largura de banda do Provedor de Serviços de Internet (ISP).
1. Calcule a taxa de transferência esperada assumindo a menor largura de banda da VM, do gateway de VPN ou do ISP; que é medido em megabits por segundo (/) dividido por oito (8).

Se a produtividade calculada não atender aos requisitos de taxa de transferência de linha de base do aplicativo, você deverá aumentar a largura de banda do recurso identificado como afunilamento. Para redimensionar um Gateway de VPN do Azure, consulte [Alterar um SKU de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Para redimensionar uma máquina virtual, consulte [Redimensionar uma VM](../virtual-machines/windows/resize-vm.md). Se você não estiver experimentando a largura de banda da Internet esperada, também poderá entrar em contato com seu ISP.

> [!NOTE]
> A taxa de transferência do gateway de VPN é uma agregação de todas as Site-to-Site\VNET-to-VNET ou conexões ponto a site.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Validar a taxa de transferência de rede usando as ferramentas de desempenho

Essa validação deve ser executada durante o horário de pico, já que a saturação de taxa de transferência de túnel VPN durante o teste não apresenta resultados precisos.

A ferramenta que usamos para esse teste é iPerf, que funciona tanto no Windows como no Linux e tem modos de cliente e servidor. Ele é limitado a 3Gbps para VMs do Windows.

Essa ferramenta não executa operações de leitura/gravação em disco. A ferramenta somente produz o tráfego TCP gerado automaticamente de uma extremidade à outra. Ele gera estatísticas com base na experimentação que mede a largura de banda disponível entre os nós do cliente e do servidor. Ao testar entre dois nós, um nó atua como o servidor e o outro nó atua como um cliente. Depois que esse teste for concluído, recomendamos que você inverta as funções dos nós para testar a taxa de transferência de upload e download em ambos os nós.

### <a name="download-iperf"></a>Baixar iPerf

Baixe o [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Para obter detalhes, consulte a [Documentação iPerf](https://iperf.fr/iperf-doc.php).

 > [!NOTE]
 > Os produtos de terceiros discutidos neste artigo são fabricados por empresas que são independentes da Microsoft. A Microsoft não oferece nenhuma garantia, implícita ou não, sobre o desempenho ou a confiabilidade desses produtos.

### <a name="run-iperf-iperf3exe"></a>Executar iPerf (iperf3.exe)

1. Habilite uma regra NSG/ACL permitindo o tráfego (para teste de endereço IP público em VM do Azure).

1. Em ambos os nós, habilite uma exceção de firewall para a porta 5001.

   **Windows:** Execute o seguinte comando como um administrador:

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   Para remover a regra após a conclusão do teste, execute esse comando:

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Linux do Azure:** As imagens do Linux do Azure têm firewalls permissivos. Se houver um aplicativo escutando em uma porta, o tráfego será permitido. Imagens personalizadas que são protegidas podem precisar de portas abertas explicitamente. Os firewalls da camada de OS do Linux comuns incluem `iptables`, `ufw`, ou `firewalld`.

1. No nó de servidor, altere para o diretório onde o iperf3.exe é extraído. Em seguida, execute iPerf no modo de servidor e defina-o para escutar na porta 5001 como os seguintes comandos:

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > A porta 5001 é personalizável para considerar restrições de firewall específicas em seu ambiente.

1. No nó de cliente, altere para o diretório onde a ferramenta iperf é extraída e, em seguida, execute o seguinte comando:

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   O cliente está direcionando trinta segundos de tráfego na porta 5001 para o servidor. O sinalizador '-P ' indica que estamos fazendo 32 conexões simultâneas com o nó do servidor.

   A tela a seguir mostra a saída a partir desse exemplo:

   ![Saída](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. (OPCIONAL) Para preservar os resultados dos testes, execute este comando:

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. Depois de concluir as etapas anteriores, execute as mesmas etapas com as funções invertidas, de modo que o nó do servidor agora será o nó do cliente e vice-versa.

> [!Note]
> Iperf não é a única ferramenta. [NTTTCP é uma solução alternativa para teste](../virtual-network/virtual-network-bandwidth-testing.md).

## <a name="test-vms-running-windows"></a>Testar VMs que executam o Windows

### <a name="load-latteexe-onto-the-vms"></a>Carregar Latte.exe nas VMs

Baixe a versão mais recente do [Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Considere colocar Latte.exe em uma pasta separada, como `c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>Permitir Latte.exe por meio do firewall do Windows

No receptor, crie uma regra de permissão no firewall do Windows para permitir que o tráfego de Latte.exe chegue. É mais fácil permitir todo o programa de Latte.exe por nome em vez de permitir portas TCP específicas de entrada.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>Permitir Latte.exe por meio do firewall do Windows como este

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

Por exemplo, se você copiou latte.exe para a pasta "c:\Tools", esse seria o comando

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Executar testes de latência

Iniciar latte.exe no receptor (executar do CMD, não do PowerShell):

`latte -a <Receiver IP address>:<port> -i <iterations>`

Ao contrário das iterações do 65 mil é longa o suficiente para retornar resultados representativos.

Qualquer número de porta disponível está correto.

Se a VM tiver um endereço IP de 10.0.0.4, ela ficaria assim

`latte -c -a 10.0.0.4:5005 -i 65100`

Iniciar latte.exe no remetente (executar do CMD, não do PowerShell)

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

O comando resultante é o mesmo que no receptor, exceto com a adição de "-c" para indicar que este é o "cliente" ou o remetente

`latte -c -a 10.0.0.4:5005 -i 65100`

Aguarde os resultados. Dependendo da distância entre as VMs, pode levar alguns minutos para ser concluída. Considere iniciar com menos iterações para testar o sucesso antes de executar testes mais longos.

## <a name="test-vms-running-linux"></a>Testar VMs executando Linux

Use [SockPerf](https://github.com/mellanox/sockperf) para testar VMS.

### <a name="install-sockperf-on-the-vms"></a>Instalar o SockPerf nas VMs

Nas VMs do Linux (remetente e destinatário), execute estes comandos para preparar o SockPerf em suas VMs:

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS/RHEL-instalar o GIT e outras ferramentas úteis

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu-instalar o GIT e outras ferramentas úteis

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash-todos

Da linha de comando do bash (pressupõe que o Git esteja instalado)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

A Make é mais lenta, pode levar vários minutos

`make`

Fazer a instalação é rápida

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>Executar SockPerf nas VMs

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>Comandos de exemplo após a instalação. Servidor/receptor-supõe que o IP do servidor seja 10.0.0.4

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>Cliente-presume que o IP do servidor seja 10.0.0.4

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> Verifique se não há nenhum salto intermediário (por exemplo, dispositivo virtual) durante o teste de taxa de transferência entre a VM e o gateway.
> Se houver resultados insatisfatórios (em termos de produtividade geral) provenientes dos testes de iPERF/NTTTCP acima, consulte o artigo a seguir para entender os principais fatores por trás das possíveis causas raiz do problema: https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

Em particular, a análise dos rastreamentos de captura de pacotes (Wireshark/Monitor de Rede) coletados em paralelo do cliente e do servidor durante esses testes ajudará nas avaliações de desempenho inadequado. Esses rastreamentos podem incluir perda de pacotes, alta latência, tamanho de MTU. fragmentação, janela TCP 0, fragmentos fora de ordem e assim por diante.

## <a name="address-slow-file-copy-issues"></a>Solucionar problemas com cópia de arquivo lenta

Mesmo que a taxa de transferência geral avaliada com as etapas anteriores (iPERF/NTTTCP/etc.) tenha sido boa, você pode experimentar um pouco de ping de arquivo ao usar o Windows Explorer ou arrastar e soltar por uma sessão RDP. Esse problema normalmente ocorre devido a um ou ambos os seguintes fatores:

* Os aplicativos de cópia de arquivo, como o Windows Explorer e o RDP não usam múltiplos threads ao copiar arquivos. Para obter melhor desempenho, use um aplicativo de cópia de arquivo multi-threaded como o [Richcopy](/previous-versions/technet-magazine/dd547088(v=msdn.10)) para copiar arquivos usando 16 ou 32 threads. Para alterar o número de thread para cópia de arquivo em RichCopy, clique em **ação**  >  **copiar opções**  >  **Copiar arquivo**.

   ![Problemas com cópia de arquivo lenta](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > Nem todos os trabalhos do aplicativo são iguais, e nem todos os aplicativos/processo utilizam todos os threads. Se você executar o teste, poderá ver alguns threads sendo vazios e não fornecerá resultados precisos da taxa de transferência.
   > Para verificar o desempenho da transferência de arquivos do aplicativo, use vários threads aumentando o número de threads em sucessão ou diminuição para encontrar a taxa de transferência ideal do aplicativo ou transferência de arquivo.

* Velocidade de leitura/gravação de disco de VM insuficiente. Para obter mais informações, consulte [Solução de Problemas de Armazenamento do Azure](/previous-versions/azure/storage/common/storage-e2e-troubleshooting).

## <a name="on-premises-device-external-facing-interface"></a>Interface externa do dispositivo local

Foram mencionadas as sub-redes de intervalos locais que você gostaria que o Azure alcançasse via VPN no gateway de rede local. Simultaneamente, defina o espaço de endereço da VNET no Azure para o dispositivo local.

* **Gateway baseado em rota**: o seletor de política ou de tráfego para VPNs baseadas em rota são configurados como qualquer para qualquer (ou curingas).

* **Gateway baseado em política**: as VPNs baseadas em políticas criptografam e direcionam pacotes por meio de túneis IPsec com base nas combinações de prefixos de endereço entre sua rede local e a VNet do Azure. A política (ou o seletor de tráfego) normalmente é definida como uma lista de acesso na configuração de VPN.

* Conexões **UsePolicyBasedTrafficSelector** : ("UsePolicyBasedTrafficSelectors" para $true em uma conexão irá configurar o gateway de VPN do Azure para se conectar ao firewall de VPN baseado em políticas no local. Se você habilitar o PolicyBasedTrafficSelectors, precisará garantir que o dispositivo VPN tenha os seletores de tráfego correspondentes definidos com todas as combinações de prefixos de rede local (gateway de rede locais) de e para os prefixos de rede virtual do Azure, em vez de qualquer para qualquer.

A configuração inadequada pode levar a desconexões frequentes no túnel, quedas de pacotes, taxa de transferência incorreta e latência.

## <a name="check-latency"></a>Verificar latência

Você pode verificar a latência usando as seguintes ferramentas:

* WinMTR
* TCPTraceroute
* `ping` e `psping` (essas ferramentas podem fornecer uma boa estimativa de RTT, mas elas não podem ser usadas em todos os casos.)

![Verificar latência](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

Se você notar um pico de alta latência em qualquer um dos saltos antes de entrar no backbone da rede da MS, convém continuar com investigações adicionais com seu provedor de serviços de Internet.

Se um grande pico de latência incomum for notado de saltos dentro de "msn.net", entre em contato com o suporte da Microsoft para obter mais investigações.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações ou ajuda, confira o seguinte link:

* [Suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)