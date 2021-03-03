---
title: Solucionar problemas do servidor do Azure Route
description: Saiba como solucionar problemas do servidor de rota do Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 02dd9aa74da42f0a5d70de4513b88756a97bea1e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679055"
---
# <a name="troubleshooting-azure-route-server-issues"></a>Solucionando problemas do servidor de rota do Azure

> [!IMPORTANT]
> O servidor de rota do Azure (visualização) está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="bgp-connectivity-issues"></a>Problemas de conectividade BGP

### <a name="why-is-the-bgp-peering-between-my-nva-and-the-azure-route-server-going-up-and-down-flapping"></a>Por que o emparelhamento via protocolo BGP entre meu NVA e o servidor de rota do Azure vai para cima e para baixo ("oscilando")?

A causa da oscilação pode ser devido à configuração do temporizador BGP. Por padrão, o temporizador Keep-Alive no servidor de rota do Azure é definido como 60 segundos e o temporizador suspenso é de 180 segundos.

### <a name="why-can-i-ping-from-my-nva-to-the-bgp-peer-ip-on-azure-route-server-but-after-i-set-up-the-bgp-peering-between-them-i-cant-ping-the-same-ip-anymore-why-does-the-bgp-peering-goes-down"></a>Por que posso executar ping do meu NVA para o IP do par de BGP no servidor de rota do Azure, mas depois de configurar o emparelhamento via protocolo BGP entre eles, não é mais possível executar ping no mesmo IP? Por que o emparelhamento via protocolo BGP fica inativo?

Em algumas NVA, você precisa adicionar uma rota estática para a sub-rede do servidor de rota do Azure. Por exemplo, se o servidor de rota do Azure estiver em 10.0.255.0/27 e seu NVA estiver em 10.0.1.0/24, você precisará adicionar a rota a seguir à tabela de roteamento no NVA:

| Rota | Próximo salto |
|-------|----------|
| 10.0.255.0/27 | 10.0.1.1 |

10.0.1.1 é o IP de gateway padrão na sub-rede em que seu NVA (ou mais precisamente, uma das NICs) está hospedado.

## <a name="bgp-route-issues"></a>Problemas de rota BGP

### <a name="why-does-my-nva-not-receive-routes-from-azure-route-server-even-though-the-bgp-peering-is-up"></a>Por que meu NVA não recebe rotas do servidor de rota do Azure, embora o emparelhamento via protocolo BGP esteja ativo?

O ASN que o servidor de rota do Azure usa é 65515. Certifique-se de configurar um ASN diferente para seu NVA para que uma sessão "eBGP" possa ser estabelecida entre seu NVA e o servidor de rota do Azure para que a propagação de rota possa ocorrer automaticamente.

### <a name="the-bgp-peering-between-my-nva-and-azure-route-server-is-up-i-can-see-routes-exchanged-correctly-between-them-why-arent-the-nva-routes-in-the-effective-routing-table-of-my-vm"></a>O emparelhamento via protocolo BGP entre meu NVA e o servidor de rota do Azure está ativo. Posso ver as rotas trocadas corretamente entre elas. Por que as rotas NVA não estão na tabela de roteamento efetiva da minha VM? 

* Se sua VM estiver na mesma VNet que seu NVA e o servidor de rota do Azure:

     O servidor de rota do Azure expõe dois IPs de par de BGP, que são hospedados em duas VMs que compartilham a responsabilidade de enviar as rotas para todas as outras VMs em execução na sua rede virtual. Cada um de seus NVA deve configurar duas sessões BGP idênticas (por exemplo, usar o mesmo número de as, o mesmo que Path e anunciar o mesmo conjunto de rotas) para as duas VMs para que suas VMs na rede virtual possam obter informações de roteamento consistentes do servidor do Azure Route. Consulte o diagrama a seguir.

    ![Diagrama mostrando uma solução de virtualização de rede com servidor de rota.](./media/faq/network-virtual-appliances.png)

    Se você tiver duas ou mais instâncias do NVA, *poderá* anunciar diferentes caminhos para a mesma rota de diferentes instâncias de NVA se quiser designar uma instância de NVA como ativa e a outra passiva.

* Se sua VM estiver em uma rede virtual diferente daquela que hospeda seu NVA e o servidor de rota do Azure. Verifique se o emparelhamento VNet está habilitado entre os dois VNets *e* se usar servidor de rota remota está habilitado na VNET da VM.

## <a name="next-steps"></a>Próximas etapas

Saiba como [configurar um servidor de rota do Azure](quickstart-configure-route-server-powershell.md)
