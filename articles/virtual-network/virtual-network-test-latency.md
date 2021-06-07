---
title: Testar a latência de rede de máquina virtual do Azure em uma rede virtual do Azure | Microsoft Docs
description: Saiba como testar a latência de rede entre as máquinas virtuais do Azure em uma rede virtual
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 7dc8aac730fdf46cab47a3297b8c001cb0b8e314
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226398"
---
# <a name="test-vm-network-latency"></a>Testar latência da rede de VMs

Para obter os resultados mais precisos, meça a latência de rede da VM (máquina virtual) do Azure com uma ferramenta projetada para a tarefa. Ferramentas disponíveis publicamente como SockPerf (para Linux) e latte.exe (para Windows) podem isolar e medir a latência de rede enquanto exclui outros tipos de latência, como a latência do aplicativo. Essas ferramentas se concentram no tipo de tráfego de rede que afeta o desempenho do aplicativo (ou seja, o protocolo TCP e o tráfego do protocolo UDP). 

Outras ferramentas de conectividade comuns, como ping, podem medir a latência, mas seus resultados podem não representar o tráfego de rede usado em cargas de trabalho reais. Isso porque a maioria dessas ferramentas emprega o protocolo ICMP, que pode ser tratado diferentemente do tráfego do aplicativo e cujos resultados podem não se aplicar a cargas de trabalho que usam TCP e UDP. 

Para um teste preciso de latência de rede dos protocolos usados pela maioria dos aplicativos, SockPerf (para Linux) e latte.exe (para Windows) produzem os resultados mais relevantes. Este artigo aborda essas duas ferramentas.

## <a name="overview"></a>Visão geral

Usando duas VMs, uma como remetente e outra como receptor, você cria um canal de comunicação bidirecional. Com essa abordagem, você pode enviar e receber pacotes em ambas as direções e medir o RTT (tempo de ida e volta).

Você pode usar essa abordagem para medir a latência de rede entre duas VMs ou até mesmo entre dois computadores físicos. As medidas de latência podem ser úteis para os seguintes cenários:

- Estabeleça um parâmetro de comparação para a latência de rede entre as VMs implantadas.
- Compare os efeitos das alterações na latência de rede depois que as alterações relacionadas forem feitas em:
  - Software do sistema operacional (SO) ou pilha de rede, incluindo alterações de configuração.
  - Um método de implantação de VM, como a implantação em uma zona de disponibilidade ou em um grupo de posicionamento de proximidade (PPG).
  - Propriedades da VM, como rede acelerada ou alterações de tamanho.
  - Uma rede virtual, como roteamento ou filtragem de alterações.

### <a name="tools-for-testing"></a>Ferramentas para teste
Para medir a latência, você tem duas opções de ferramenta diferentes:

* Para sistemas baseados no Windows: [latte.exe (Windows)](https://github.com/microsoft/latte/releases/download/v0/latte.exe)
* Para sistemas baseados em Linux: [SockPerf (Linux)](https://github.com/mellanox/sockperf)

Ao usar essas ferramentas, você ajuda a garantir que apenas os tempos de entrega de carga TCP ou UDP sejam medidos e não ICMP (ping) ou outros tipos de pacotes que não são usados por aplicativos e não afetam seu desempenho.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>Dicas para criar uma configuração de VM ideal

Ao criar a configuração da VM, tenha em mente as seguintes recomendações:
- Use a versão mais recente do Windows ou do Linux.
- Habilite a rede acelerada para obter melhores resultados.
- Implante VMs com um [grupo de posicionamento de proximidade do Azure](../virtual-machines/co-location.md).
- VMs maiores geralmente têm um desempenho melhor do que as VMs menores.

### <a name="tips-for-analysis"></a>Dicas para análise

Conforme você está analisando os resultados do teste, tenha em mente as seguintes recomendações:

- Estabeleça uma linha de base antecipadamente, assim que a implantação, a configuração e as otimizações forem concluídas.
- Sempre Compare novos resultados com uma linha de base ou, caso contrário, de um teste para outro com alterações controladas.
- Repita os testes sempre que as alterações forem observadas ou planejadas.


## <a name="test-vms-that-are-running-windows"></a>Testar as VMs que estão executando o Windows

### <a name="get-latteexe-onto-the-vms"></a>Obter latte.exe nas VMs

Baixe a [versão mais recente do latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b).

Considere colocar latte.exe em uma pasta separada, como *c:\Tools*.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>Permitir latte.exe por meio do Windows Defender firewall

No *receptor*, crie uma regra de permissão no Windows Defender firewall para permitir que o tráfego de latte.exe chegue. É mais fácil permitir todo o programa de latte.exe por nome em vez de permitir portas TCP específicas de entrada.

Permita latte.exe por meio do Windows Defender firewall executando o seguinte comando:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

Por exemplo, se você copiou latte.exe para a pasta *c:\Tools* , esse seria o comando:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Executar testes de latência

* No *receptor*, inicie latte.exe (execute-o na janela cmd, não no PowerShell):

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    Cerca de 65.000 iterações são longas o suficiente para retornar resultados representativos.

    Qualquer número de porta disponível está correto.

    Se a VM tiver um endereço IP de 10.0.0.4, o comando ficaria assim:

    `latte -a 10.0.0.4:5005 -i 65100`

* No *remetente*, inicie latte.exe (execute-o na janela cmd, não no PowerShell):

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    O comando resultante é o mesmo que no receptor, exceto com a adição de &nbsp; *-c* para indicar que este é o *cliente* ou *remetente*:

    `latte -c -a 10.0.0.4:5005 -i 65100`

Aguarde os resultados. Dependendo da distância entre as VMs, o teste pode levar alguns minutos para ser concluído. Considere iniciar com menos iterações para testar o sucesso antes de executar testes mais longos.

## <a name="test-vms-that-are-running-linux"></a>Testar as VMs que estão executando o Linux

Para testar as VMs que estão executando o Linux, use [SockPerf](https://github.com/mellanox/sockperf).

### <a name="install-sockperf-on-the-vms"></a>Instalar o SockPerf nas VMs

Nas VMs do Linux, *remetente* e *destinatário*, execute os seguintes comandos para preparar o SockPerf nas VMs. São fornecidos comandos para o distribuições principal.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>Para Red Hat Enterprise Linux (RHEL)/CentOS

Execute os seguintes comandos:

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>Para o Ubuntu

Execute os seguintes comandos:

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>Para todos os distribuições

Copie, compile e instale o SockPerf de acordo com as seguintes etapas:

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>Executar SockPerf nas VMs

Depois que a instalação do SockPerf for concluída, as VMs estarão prontas para executar os testes de latência. 

Primeiro, inicie o SockPerf no *receptor*.

Qualquer número de porta disponível está correto. Neste exemplo, usamos a porta 12345:

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

Agora que o servidor está escutando, o cliente pode começar a enviar pacotes para o servidor na porta em que está escutando (nesse caso, 12345).

Cerca de 100 segundos é longo o suficiente para retornar resultados representativos, conforme mostrado no exemplo a seguir:

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Aguarde os resultados. Dependendo da distância entre as VMs, o número de iterações varia. Para testar o sucesso antes de executar os testes mais longos, considere a possibilidade de começar com testes menores de cerca de 5 segundos.

Este exemplo de SockPerf usa um tamanho de mensagem de 350 bytes, que é típico para um pacote médio. Você pode ajustar o tamanho maior ou menor para obter resultados que representem com mais precisão a carga de trabalho em execução em suas VMs.


## <a name="next-steps"></a>Próximas etapas
* Melhore a latência com um [grupo de posicionamento de proximidade do Azure](../virtual-machines/co-location.md).
* Saiba como [otimizar a rede para VMs](../virtual-network/virtual-network-optimize-network-bandwidth.md) para seu cenário.
* Leia sobre [como a largura de banda é alocada para máquinas virtuais](../virtual-network/virtual-machine-network-throughput.md).
* Para obter mais informações, consulte [perguntas frequentes sobre a rede virtual do Azure](../virtual-network/virtual-networks-faq.md).