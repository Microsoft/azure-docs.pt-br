---
title: Inspeção de pacotes com o Observador de Rede do Azure | Microsoft Docs
description: Este artigo descreve como usar o Observador de Rede para realizar inspeções de pacotes detalhadas coletadas em uma VM
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 7f3fc69bbfd881a26ceb25705852558b66c60153
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64716905"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Inspeção de pacotes com o Observador de Rede do Azure

Usando o recurso de captura de pacotes do Observador de Rede, você pode iniciar e gerenciar sessões de capturas em suas VMs do Azure no portal, PowerShell, CLI e por meio de programação com o SDK e a API REST. A captura de pacotes permite lidar com cenários que exigem dados no nível do pacote fornecendo as informações em um formato prontamente útil. Utilizando as ferramentas disponíveis gratuitamente para inspecionar os dados, você pode examinar as comunicações enviadas para e de suas VMs, e obter informações no tráfego de rede. Alguns usos de exemplo dos dados de captura de pacotes incluem: investigar problemas de rede ou do aplicativo, detectar tentativas de invasão e uso indevido da rede ou manter a conformidade normativa. Neste artigo, mostramos como abrir um arquivo de captura de pacotes fornecido pelo Observador de Rede usando uma ferramenta popular de software livre. Também forneceremos exemplos que mostram como calcular uma latência de conexão, identificar o tráfego anormal e examinar as estatísticas de rede.

## <a name="before-you-begin"></a>Antes de começar

Este artigo percorre alguns cenários pré-configurados em uma captura de pacotes executada anteriormente. Esses cenários ilustram recursos que podem ser acessados examinando uma captura de pacotes. O cenário usa o [WireShark](https://www.wireshark.org/) para analisar a captura de pacotes.

Ele pressupõe que você já executou uma captura de pacotes em uma máquina virtual. Para saber como criar uma captura de pacotes, visite [Gerenciar capturas de pacotes com o portal](network-watcher-packet-capture-manage-portal.md) ou com o REST visitando [Gerenciamento de Capturas de Pacotes com a API REST](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Cenário

Nesse cenário, você irá:

* examinar uma captura de pacotes

## <a name="calculate-network-latency"></a>calcular a latência da rede

Nesse cenário, mostramos como exibir o Tempo Resposta inicial (RTT) de uma conversa TCP (Protocolo de Controle de Transmissão) que ocorre entre dois pontos de extremidade.

Quando uma conexão TCP é estabelecida, os três primeiros pacotes enviados na conexão seguem um padrão conhecido como handshake de três vias. Examinando os dois primeiros pacotes enviados nesse handshake, uma solicitação inicial do cliente e uma resposta do servidor, podemos calcular a latência quando a conexão foi estabelecida. Essa latência é conhecida como Tempo Resposta (RTT). Para obter mais informações sobre o protocolo TCP e o handshake de três vias, consulte o recursos a seguir. [https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip](https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip )

### <a name="step-1"></a>Etapa 1

Iniciar o WireShark

### <a name="step-2"></a>Etapa 2

Carregue o arquivo **.cap** da captura de pacotes. Esse arquivo pode ser encontrado no blob que foi salvo localmente na máquina virtual, dependendo de como você o configurou.

### <a name="step-3"></a>Etapa 3

Para exibir o Tempo Resposta inicial (RTT) nas conversas TCP, veremos apenas os dois primeiros pacotes envolvidos no handshake TCP. Usaremos os dois primeiros pacotes no handshake de três vias, que são os pacotes [SYN], [SYN, ACK]. Eles são chamados de sinalizadores definidos no cabeçalho TCP. O último pacote no handshake, o pacote [ACK], não será usado neste cenário. O pacote [SYN] é enviado pelo cliente. Após ser recebido, o servidor envia o pacote [ACK] como uma confirmação de recebimento do SYN a partir do cliente. Aproveitando o fato de que a resposta do servidor exige muito pouca sobrecarga, calculamos o RTT subtraindo a hora em que o pacote [SYN, ACK] foi recebido pelo cliente da hora em que o pacote [SYN] foi enviado pelo cliente.

Usando o WireShark, esse valor é calculado.

Para exibir mais facilmente os dois primeiros pacotes no handshake TCP de três vias, utilizaremos a capacidade de filtragem fornecida pelo WireShark.

Para aplicar o filtro no WireShark, expanda o Segmento "Protocolo de Controle de Transmissão" de um pacote [SYN] em sua captura e examine os sinalizadores definidos no cabeçalho TCP.

Como estamos procurando filtrar todos os pacotes [SYN] e [SYN, ACK], nos sinalizadores, confirme se o bit Syn está configurado com 1 e, depois, clique com o botão direito do mouse no bit Syn -> Aplicar como Filtro -> Selecionado.

![figura 7][7]

### <a name="step-4"></a>Etapa 4

Agora que você filtrou a janela para ver apenas os pacotes com o bit [SYN] definido, poderá selecionar facilmente as conversas nas quais está interessado para ver o RTT inicial. Uma maneira simples de exibir o RTT no WireShark é simplesmente clicar na análise suspensa marcada como “SEQ/ACK”. Você verá o RTT exibido. Neste caso, o RTT era 0,0022114 segundos, ou 2,211 ms.

![figura 8][8]

## <a name="unwanted-protocols"></a>Protocolos indesejados

Você pode ter vários aplicativos em execução em uma instância de máquina virtual implantada no Azure. Muitos desses aplicativos se comunicam pela rede, talvez sem sua permissão explícita. Usando a captura de pacotes para armazenar a comunicação de rede, podemos investigar como o aplicativo está falando na rede e procurar quaisquer problemas.

Neste exemplo, examinaremos uma captura de pacotes executada antes para os protocolos indesejados que possa indicar a comunicação não autorizada de um aplicativo em execução em seu computador.

### <a name="step-1"></a>Etapa 1

Usando a mesma captura no cenário anterior, clique em **Estatísticas** > **Hierarquia de Protocolos**

![menu da hierarquia de protocolos][2]

É exibida a janela da hierarquia de protocolos. Essa exibição fornece uma lista de todos os protocolos que estavam em uso durante a sessão de captura e o número de pacotes transmitidos e recebidos usando os protocolos. Essa exibição pode ser útil para localizar o tráfego de rede indesejado em suas máquinas virtuais ou rede.

![Hierarquia de protocolo aberta][3]

Como você pode ver na captura de tela a seguir, houve tráfego usando o protocolo BitTorrent, que é utilizado para o compartilhamento de arquivos ponto a ponto. Como administrador, você não espera ver o tráfego BitTorrent nessas máquinas virtuais em particular. Agora que você conhece esse tráfego, pode remover o software de ponto a ponto instalado na máquina virtual ou bloquear o tráfego usando os Grupos de Segurança de Rede ou um Firewall. Além disso, pode optar por executar as capturas de pacotes em um agendamento para poder analisar o uso do protocolo em suas máquinas virtuais regularmente. Para obter um exemplo sobre como automatizar as tarefas de rede no Azure, visite [Monitorar os recursos da rede com a automação do Azure](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>Localizando os principais destinos e portas

Compreender os tipos de tráfego, pontos de extremidade e portas de comunicação é importante ao monitorar ou solucionar problemas dos aplicativos e recursos em sua rede. Utilizando o arquivo de captura de pacotes acima, podemos conhecer rapidamente os principais destinos com os quais nossa VM está comunicando-se e as portas sendo utilizadas.

### <a name="step-1"></a>Etapa 1

Usando a mesma captura no cenário anterior, clique em **Estatísticas** > **Estatísticas IPv4** > **Destinos e Portas**

![janela de captura de pacotes][4]

### <a name="step-2"></a>Etapa 2

Quando examinamos os resultados, uma linha se destaca, havendo várias conexões na porta 111. A porta mais usada foi 3389, que é a área de trabalho remota, e as restantes são as portas dinâmicas de RPC.

Embora esse tráfego pode não significar nada, é uma porta que foi usada para muitas conexões e é desconhecida para o administrador.

![figura 5][5]

### <a name="step-3"></a>Etapa 3

Agora que determinamos uma porta fora do lugar, é possível filtrar nossa captura com base nela.

O filtro nesse cenário seria:

```
tcp.port == 111
```

Inserimos o texto do filtro acima na caixa de texto do filtro e pressionamos Enter.

![figura 6][6]

Nos resultados, podemos ver que todo o tráfego está vindo de uma máquina virtual local na mesma sub-rede. Se ainda não entendemos por que esse tráfego está ocorrendo, podemos examinar ainda mais os pacotes para determinar por que ele está fazendo essas chamadas na porta 111. Com essas informações, podemos tomar a devida ação.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os outros recursos de diagnóstico do Observador de Rede visitando [Visão geral do monitoramento de rede do Azure](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













