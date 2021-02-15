---
title: 'Azure ExpressRoute: Azure para engenheiros de rede'
description: Esta página explica aos engenheiros de rede tradicionais como as redes funcionam no Azure.
documentationcenter: na
services: networking
author: osamaz
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/25/2020
ms.author: osamaz
ms.openlocfilehash: 9a22e58e4407897fb9418cae0ba9f32408cda8e1
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234198"
---
# <a name="azure-for-network-engineers"></a>Azure para engenheiros de rede
Como engenheiro de rede convencional, você lida com ativos físicos, como roteadores, comutadores, cabos, firewalls para criar infraestrutura. Em uma camada lógica, você configurou a LAN virtual (VLAN), o Spanning Tree Protocol (STP), protocolos de roteamento (RIP, OSPF, BGP). Você gerenciou sua rede usando as ferramentas de gerenciamento e a CLI. A rede na nuvem é diferente onde os pontos de extremidade de rede são lógicos e o uso de protocolos de roteamento é mínimo. Você trabalhará com Azure Resource Manager API, CLI do Azure e PowerShell para configurar e gerenciar ativos no Azure. Você iniciará sua jornada de rede na nuvem compreendendo os locatários básicos da rede do Azure. 
## <a name="virtual-network"></a>Rede virtual
Ao criar uma rede de baixo para cima, você coleta algumas informações básicas. Essas informações podem ser número de hosts, dispositivos de rede, número de sub-redes, roteamento entre sub-redes, domínios de isolamento, como VLANs. Essas informações ajudam a dimensionar a rede e os dispositivos de segurança, bem como criar a arquitetura para dar suporte a aplicativos e serviços.

Ao planejar implantar seus aplicativos e serviços no Azure, você começará criando um limite lógico no Azure, que é chamado de rede virtual. Essa rede virtual é semelhante a um limite de rede física. Como se trata de uma rede virtual, você não precisa de engrenagem física, mas ainda precisa planejar as entidades lógicas, como endereços IP, sub-redes IP, roteamento e políticas.

Quando você cria uma rede virtual no Azure, ela é pré-configurada com um intervalo de IP (10.0.0.0/16). Esse intervalo não é fixo, você pode definir seu próprio intervalo de IP. Você pode definir os intervalos de endereços IPv4 e IPv6. Os intervalos de IP definidos para a rede virtual não são anunciados para a Internet. Você pode criar várias sub-redes do intervalo de IPS. Essas sub-redes serão usadas para atribuir endereços IP a vNICs (interfaces de rede virtual). Os primeiros quatro endereços IP de cada sub-rede são reservados e não podem ser usados para alocação de IP. Não há nenhum conceito de VLANs em uma nuvem pública. No entanto, você pode criar isolamento em uma rede virtual com base em suas sub-redes definidas.

Você pode criar uma sub-rede grande que abrange todo o espaço de endereço de rede virtual ou optar por criar várias sub-redes. No entanto, se você estiver usando um gateway de rede virtual, o Azure exigirá que você crie uma sub-rede com o nome "sub-rede de gateway". O Azure usará essa sub-rede para atribuir endereços IP a gateways de rede virtual. 

## <a name="ip-allocation"></a>Alocação de IP

Ao atribuir um endereço IP a um host, você realmente atribui o IP a uma NIC (placa de interface de rede). Você pode atribuir dois tipos de endereços IP a uma NIC no Azure:

- Endereços IP públicos-usados para comunicar entrada e saída (sem NAT) com a Internet e outros recursos do Azure não conectados a uma rede virtual. Atribuir um endereço IP público a uma NIC é opcional. Os endereços IP públicos pertencem ao espaço de endereço IP da Microsoft.
- Endereços IP privados-usados para comunicação em uma rede virtual, sua rede local e a Internet (com NAT). O espaço de endereço IP que você define na rede virtual é considerado privado mesmo se você configurar o espaço de endereço IP público. A Microsoft não anuncia esse espaço para a Internet. Você deve atribuir pelo menos um endereço IP privado a uma VM.

Assim como ocorre com os dispositivos ou hosts físicos, há duas maneiras de alocar um endereço IP a um recurso-dinâmico ou estático. No Azure, o método de alocação padrão é dinâmico, em que um endereço IP é alocado quando você cria uma VM (máquina virtual) ou inicia uma VM parada. O endereço IP é liberado quando você para ou exclui a VM. Para garantir que o endereço IP para a VM permaneça o mesmo, você pode definir o método de alocação explicitamente como estático. Nesse caso, um endereço IP é atribuído imediatamente. Ele é liberado apenas quando você exclui a VM ou altera seu método de alocação para dinâmico. 

Os endereços IP privados são alocados das sub-redes que você definiu em uma rede virtual. Para uma VM, você escolhe uma sub-rede para a alocação de IP. Se uma VM contiver várias NICs, você poderá escolher uma sub-rede diferente para cada NIC.

## <a name="routing"></a>Roteamento
Quando você cria uma rede virtual, o Azure cria uma tabela de roteamento para sua rede. Essa tabela de roteamento contém os seguintes tipos de rotas.
- Rotas do sistema
- Rotas padrão de sub-rede
- Rotas de outras redes virtuais
- Rotas BGP
- Rotas de ponto de extremidade de serviço
- UDR (rotas definidas pelo usuário)

Quando você cria uma rede virtual pela primeira vez sem definir nenhuma sub-rede, o Azure cria entradas de roteamento na tabela de roteamento. Essas rotas são chamadas de rotas do sistema. As rotas do sistema são definidas neste local. Você não pode modificar essas rotas. No entanto, você pode substituir as rotas de sistema Configurando UDRs.

Quando você cria uma ou várias sub-redes dentro de uma rede virtual, o Azure cria entradas padrão na tabela de roteamento para habilitar a comunicação entre essas sub-redes em uma rede virtual. Essas rotas podem ser modificadas usando rotas estáticas, que são UDR (rotas definidas pelo usuário) no Azure.

quando você cria um emparelhamento de rede virtual entre duas redes virtuais, uma rota é adicionada a cada intervalo de endereços dentro do espaço de endereço de cada rede virtual para a qual um emparelhamento é criado.

Se o gateway de rede local troca as rotas do protocolo BGP (Border Gateway Protocol) com um gateway de rede virtual do Azure, uma rota é adicionada para cada rota propagada do gateway de rede virtual local. Essas rotas aparecem na tabela de roteamento como rotas BGP.

os endereços IP públicos para determinados serviços são adicionados à tabela de rotas pelo Azure quando você habilita um ponto de extremidade de serviço para o serviço. Os pontos de extremidade de serviço são habilitados para sub-redes individuais em uma rede virtual. Quando você habilita um ponto de extremidade de serviço, a rota é adicionada somente à tabela de rotas do para a sub-rede que pertence a esse serviço. O Azure gerencia os endereços na tabela de rotas automaticamente quando os endereços mudam.

As rotas definidas pelo usuário também são chamadas de rotas personalizadas. Você cria UDR no Azure para substituir as rotas do sistema padrão do Azure ou para adicionar outras rotas à tabela de rotas de uma sub-rede. No Azure, você cria uma tabela de rotas e, em seguida, associa a tabela de rotas às sub-redes da rede virtual.

Quando você tem entradas concorrentes em uma tabela de roteamento, o Azure seleciona o próximo salto com base na correspondência de prefixo mais longa semelhante aos roteadores tradicionais. No entanto, se houver várias entradas do próximo salto com o mesmo prefixo de endereço, o Azure selecionará as rotas na ordem a seguir.
1. UDR (rotas definidas pelo usuário)
1. Rotas BGP
1. Rotas do sistema

## <a name="security"></a>Segurança

Você pode filtrar o tráfego de rede de e para recursos em uma rede virtual usando grupos de segurança de rede. Você também pode usar NVA (soluções de virtualização de rede), como firewall do Azure ou firewalls de outros fornecedores. Você pode controlar como o Azure direciona o tráfego de sub-redes. Você também pode limitar quem pode trabalhar com recursos em redes virtuais na sua organização.

Um NSG (grupo de segurança de rede) contém uma lista de regras de ACL (Lista de Controle de Acesso) que permitem ou negam o tráfego de rede para sub-redes, NICs ou ambos. Os NSGs podem ser associados a sub-redes ou NICs individuais conectadas a uma sub-rede. Quando um NSG é associado a uma sub-rede, as regras de ACL se aplicam a todas as VMs na sub-rede. Além disso, o tráfego para uma NIC individual pode ser restringido associando um NSG diretamente a uma NIC.

Os NSGs contêm dois conjuntos de regras: entrada e saída. A prioridade de uma regra deve ser exclusiva em cada conjunto. Cada regra tem propriedades de protocolo, origem e intervalos de porta de destino, prefixos de endereço, direção de tráfego, prioridade e tipo de acesso. Todos os NSGs contêm um conjunto de regras padrão. As regras padrão não podem ser excluídas, mas como recebem a prioridade mais baixa, elas podem ser substituídas pelas regras que você criar.

## <a name="verification"></a>Verificação
### <a name="routes-in-virtual-network"></a>Rotas na rede virtual
A combinação de rotas que você cria, rotas padrão do Azure e quaisquer rotas propagadas da rede local por meio de um gateway de VPN do Azure (se a rede virtual estiver conectada à rede local) por meio do BGP (Border Gateway Protocol), são as rotas efetivas para todos os adaptadores de rede em uma sub-rede. Você pode ver essas rotas em vigor navegando até NIC por meio do portal, do PowerShell ou da CLI.
### <a name="network-security-groups"></a>Grupos de segurança de rede
As regras de segurança efetivas aplicadas a uma interface de rede são uma agregação das regras existentes no NSG associado a uma interface de rede e a sub-rede na qual a interface de rede está inserida. Você pode exibir todas as regras de segurança em vigor do NSGs que são aplicadas nas interfaces de rede da VM navegando até a NIC por meio do portal, do PowerShell ou da CLI.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [rede virtual][VNet].

Saiba mais sobre [Roteamento de rede virtual][vnet-routing].

Saiba mais sobre os [grupos de segurança de rede][network-security].

<!--Link References-->
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[vnet-routing]: ../virtual-network/virtual-networks-udr-overview.md
[network-security]: ../virtual-network/network-security-groups-overview.md