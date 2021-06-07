---
title: Solucionar problemas do servidor do Azure Route
description: Saiba como solucionar problemas do servidor de rota do Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: how-to
ms.date: 03/15/2021
ms.author: duau
ms.openlocfilehash: 83f1e83653c5674988cadcb5b54d3c675ae0b8b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489433"
---
# <a name="troubleshooting-azure-route-server-issues"></a>Solucionando problemas do servidor de rota do Azure

> [!IMPORTANT]
> No momento, o Servidor de Rota do Azure (versão prévia) está na versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="connectivity-issues"></a>Problemas de conectividade

### <a name="why-does-my-nva-lose-internet-connectivity-after-it-advertises-the-default-route-00000-to-azure-route-server"></a>Por que minhas NVA perdem a conectividade com a Internet depois de anunciar a rota padrão (0.0.0.0/0) para o servidor de rota do Azure?
Quando o NVA anuncia a rota padrão, o servidor de rota do Azure o programa para todas as VMs na rede virtual, incluindo o próprio NVA. Essa rota padrão define o NVA como o próximo salto para todo o tráfego associado à Internet. Se seu NVA precisar de conectividade com a Internet, você precisará configurar uma [rota definida pelo usuário](../virtual-network/virtual-networks-udr-overview.md) para substituir essa rota padrão do NVA e anexar o UDR à sub-rede onde o NVA está hospedado (consulte o exemplo abaixo). Caso contrário, o computador host NVA continuará enviando o tráfego de entrada na Internet, incluindo aquele enviado pelo NVA de volta para o NVA em si.

| Rota | Próximo salto |
|-------|----------|
| 0.0.0.0/0 | Internet |


### <a name="why-can-i-ping-from-my-nva-to-the-bgp-peer-ip-on-azure-route-server-but-after-i-set-up-the-bgp-peering-between-them-i-cant-ping-the-same-ip-anymore-why-does-the-bgp-peering-go-down"></a>Por que posso executar ping do meu NVA para o IP do par de BGP no servidor de rota do Azure, mas depois de configurar o emparelhamento via protocolo BGP entre eles, não é mais possível executar ping no mesmo IP? Por que o emparelhamento via protocolo BGP fica inativo?

Em algumas NVA, você precisa adicionar uma rota estática para a sub-rede do servidor de rota do Azure. Por exemplo, se o servidor de rota do Azure estiver em 10.0.255.0/27 e seu NVA estiver em 10.0.1.0/24, você precisará adicionar a rota a seguir à tabela de roteamento no NVA:

| Rota | Próximo salto |
|-------|----------|
| 10.0.255.0/27 | 10.0.1.1 |

10.0.1.1 é o IP de gateway padrão na sub-rede em que seu NVA (ou mais precisamente, uma das NICs) está hospedado.

### <a name="why-do-i-lose-connectivity-to-my-on-premises-network-over-expressroute-andor-azure-vpn-when-im-deploying-azure-route-server-to-a-virtual-network-that-already-has-expressroute-gateway-andor-azure-vpn-gateway"></a>Por que eu perco a conectividade com minha rede local por meio do ExpressRoute e/ou da VPN do Azure quando estou implantando o servidor de rota do Azure em uma rede virtual que já tem o gateway de ExpressRoute e/ou o gateway de VPN do Azure?
Quando você implanta o servidor de rota do Azure em uma rede virtual, precisamos atualizar o plano de controle entre os gateways e a rede virtual. Durante essa atualização, há um período de tempo em que as VMs na rede virtual perderão a conectividade com a rede local. É altamente recomendável que você agende uma manutenção para implantar o servidor de rota do Azure em seu ambiente de produção.  

## <a name="control-plane-issues"></a>Problemas de plano de controle

### <a name="why-is-the-bgp-peering-between-my-nva-and-the-azure-route-server-going-up-and-down-flapping"></a>Por que o emparelhamento via protocolo BGP entre meu NVA e o servidor de rota do Azure vai para cima e para baixo ("oscilando")?

A causa da oscilação pode ser devido à configuração do temporizador BGP. Por padrão, o temporizador Keep-Alive no servidor de rota do Azure é definido como 60 segundos e o temporizador suspenso é de 180 segundos.

### <a name="why-does-my-nva-not-receive-routes-from-azure-route-server-even-though-the-bgp-peering-is-up"></a>Por que meu NVA não recebe rotas do servidor de rota do Azure, embora o emparelhamento via protocolo BGP esteja ativo?

O ASN que o servidor de rota do Azure usa é 65515. Certifique-se de configurar um ASN diferente para seu NVA para que uma sessão "eBGP" possa ser estabelecida entre seu NVA e o servidor de rota do Azure para que a propagação de rota possa ocorrer automaticamente. Certifique-se de habilitar o "multi-hop" na configuração de BGP porque o NVA e o servidor de rota do Azure estão em sub-redes diferentes na rede virtual.

### <a name="the-bgp-peering-between-my-nva-and-azure-route-server-is-up-i-can-see-routes-exchanged-correctly-between-them-why-arent-the-nva-routes-in-the-effective-routing-table-of-my-vm"></a>O emparelhamento via protocolo BGP entre meu NVA e o servidor de rota do Azure está ativo. Posso ver as rotas trocadas corretamente entre elas. Por que as rotas NVA não estão na tabela de roteamento efetiva da minha VM? 

* Se sua VM estiver na mesma VNet que seu NVA e o servidor de rota do Azure:

     O servidor de rota do Azure expõe dois IPs de par de BGP, que são hospedados em duas VMs que compartilham a responsabilidade de enviar as rotas para todas as outras VMs em execução na sua rede virtual. Cada um de seus NVA deve configurar duas sessões BGP idênticas (por exemplo, usar o mesmo número de as, o mesmo que Path e anunciar o mesmo conjunto de rotas) para as duas VMs para que suas VMs na rede virtual possam obter informações de roteamento consistentes do servidor do Azure Route. Consulte o diagrama a seguir.

    ![Diagrama mostrando uma solução de virtualização de rede com servidor de rota.](./media/faq/network-virtual-appliances.png)

    Se você tiver duas ou mais instâncias do NVA, *poderá* anunciar diferentes caminhos para a mesma rota de diferentes instâncias de NVA se quiser designar uma instância de NVA como ativa e a outra passiva.

* Se sua VM estiver em uma rede virtual diferente daquela que hospeda seu NVA e o servidor de rota do Azure. Verifique se o emparelhamento VNet está habilitado entre os dois VNets *e* se usar servidor de rota remota está habilitado na VNET da VM.

## <a name="next-steps"></a>Próximas etapas

Saiba como [configurar um servidor de rota do Azure](quickstart-configure-route-server-powershell.md)
