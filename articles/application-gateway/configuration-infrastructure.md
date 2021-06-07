---
title: Configuração de infraestrutura de gateway Aplicativo Azure
description: Este artigo descreve como configurar a infraestrutura de gateway de Aplicativo Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: f214b0b0751f44ea1357f569fd814a7621af61ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397613"
---
# <a name="application-gateway-infrastructure-configuration"></a>Configuração de infraestrutura do gateway de aplicativo

A infraestrutura do gateway de aplicativo inclui a rede virtual, as sub-redes, os grupos de segurança de rede e as rotas definidas pelo usuário.

## <a name="virtual-network-and-dedicated-subnet"></a>Rede virtual e sub-rede dedicada

Um gateway de aplicativo é uma implantação dedicada em sua rede virtual. Em sua rede virtual, uma sub-rede dedicada é necessária para o gateway de aplicativo. Você pode ter várias instâncias de uma determinada implantação do gateway de aplicativo em uma sub-rede. Você também pode implantar outros gateways de aplicativo na sub-rede. Mas você não pode implantar nenhum outro recurso na sub-rede do gateway de aplicativo. Você não pode misturar Standard_v2 e o gateway de Aplicativo Azure padrão na mesma sub-rede.

> [!NOTE]
> [As políticas de ponto de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoint-policies-overview.md) atualmente não têm suporte em uma sub-rede do Gateway de Aplicativo.

### <a name="size-of-the-subnet"></a>Tamanho da sub-rede

O gateway de aplicativo usa um endereço IP privado por instância, mais outro endereço IP privado se um IP de front-end privado estiver configurado.

O Azure também reserva cinco endereços IP em cada sub-rede para uso interno: os quatro primeiros e os últimos endereços IP. Por exemplo, considere 15 instâncias de gateway de aplicativo sem um IP de front-end privado. Você precisa de pelo menos 20 endereços IP para esta sub-rede: cinco para uso interno e 15 para as instâncias do gateway de aplicativo.

Considere uma sub-rede que tenha 27 instâncias de gateway de aplicativo e um endereço IP para um IP de front-end privado. Nesse caso, você precisa de 33 endereços IP: 27 para as instâncias do gateway de aplicativo, uma para o front-end privado e cinco para uso interno.

O SKU do gateway de aplicativo (Standard ou WAF) pode dar suporte a até 32 instâncias (32 instância endereços IP + 1 IP privado de front-end + 5 Azure reservado) – portanto, é recomendado um tamanho mínimo de sub-rede de/26

O gateway de aplicativo (Standard_v2 ou WAF_v2 SKU) pode dar suporte a até 125 instâncias (125 instância endereços IP + 1 IP de front-end privado + 5 Azure reservado) – portanto, é recomendado um tamanho mínimo de sub-rede de/24

## <a name="network-security-groups"></a>Grupos de segurança de rede

Os NSGs (grupos de segurança de rede) têm suporte no gateway de aplicativo. Mas há algumas restrições:

- Você precisa permitir o tráfego de entrada na Internet nas portas TCP 65503-65534 para a SKU do Gateway de Aplicativo v1 e as portas TCP 65200-65535 para a SKU V2 com a sub-rede de destino como **Qualquer** e a origem como a marca de serviço **GatewayManager**. Esse intervalo de porta é necessário para a comunicação da infraestrutura do Azure. Essas portas são protegidas (bloqueadas) pelos certificados do Azure. Entidades externas, incluindo os clientes desses gateways, não podem se comunicar nesses pontos de extremidade.

- A conectividade de Internet de saída não pode ser bloqueada. As regras de saída padrão no NSG permitem a conectividade com a Internet. É recomendável que você:

  - Não remova as regras de saída padrão.
  - Não crie outras regras de saída que neguem qualquer conectividade de saída.

- O tráfego da marca **AzureLoadBalancer** com a sub-rede de destino como **qualquer** deve ser permitido.

### <a name="allow-access-to-a-few-source-ips"></a>Permitir acesso a alguns IPs de origem

Para este cenário, use NSGs na sub-rede do gateway de aplicativo. Coloque as seguintes restrições na sub-rede nesta ordem de prioridade:

1. Permita o tráfego de entrada de um IP de origem ou intervalo de IP com o destino como o intervalo de endereços de sub-rede do gateway de aplicativo inteiro e a porta de destino como sua porta de acesso de entrada, por exemplo, a porta 80 para acesso HTTP.
2. Permitir solicitações de entrada da origem como **uma** marca de serviço do **gatewaymanager** e destino como portas de destino e como 65503-65534 para a SKU do gateway de aplicativo v1 e portas 65200-65535 para SKU v2 para [comunicação de status de integridade de back-end](./application-gateway-diagnostics.md). Esse intervalo de porta é necessário para a comunicação da infraestrutura do Azure. Essas portas são protegidas (bloqueadas) pelos certificados do Azure. Sem os certificados apropriados em vigor, as entidades externas não podem iniciar alterações nesses pontos de extremidade.
3. Permitir investigações de Azure Load Balancer de entrada (marca *AzureLoadBalancer* ) e tráfego de rede virtual de entrada (marca *VirtualNetwork* ) no [grupo de segurança de rede](../virtual-network/network-security-groups-overview.md).
4. Bloquear todos os outros tráfegos de entrada usando uma regra negar-tudo.
5. Permitir o tráfego de saída para a Internet para todos os destinos.

## <a name="supported-user-defined-routes"></a>Rotas definidas pelo usuário com suporte 

> [!IMPORTANT]
> Usar UDRs na sub-rede do gateway de aplicativo pode fazer com que o status de integridade na [exibição de integridade do back-end](./application-gateway-diagnostics.md#back-end-health) seja exibido como **desconhecido**. Ele também pode causar falha na geração de logs e métricas do gateway de aplicativo. Recomendamos que você não use UDRs na sub-rede do gateway de aplicativo para poder exibir a integridade, os logs e as métricas de back-end.

- **v1**

   Para a SKU v1, as rotas definidas pelo usuário (UDRs) têm suporte na sub-rede do gateway de aplicativo, desde que elas não alterem a comunicação de solicitação/resposta de ponta a ponta. Por exemplo, você pode configurar um UDR na sub-rede do gateway de aplicativo para apontar para um dispositivo de firewall para inspeção de pacotes. Mas você deve certificar-se de que o pacote pode atingir o destino pretendido após a inspeção. A falha em fazer isso pode resultar em comportamento incorreto de investigação de integridade ou roteamento de tráfego. Isso inclui rotas aprendidas ou rotas 0.0.0.0/0 padrão que são propagadas pelo Azure ExpressRoute ou gateways de VPN na rede virtual. Qualquer cenário no qual 0.0.0.0/0 precisa ser redirecionado localmente (túnel forçado) não tem suporte para v1.

- **v2**

   Para a SKU v2, há cenários com e sem suporte:

   **v2 cenários com suporte**
   > [!WARNING]
   > Uma configuração incorreta da tabela de rotas pode resultar no roteamento assimétrico no gateway de aplicativo v2. Verifique se todo o tráfego do plano de gerenciamento/controle é enviado diretamente para a Internet e não por meio de um dispositivo virtual. O log e as métricas também podem ser afetados.


  **Cenário 1**: UDR para desabilitar a propagação de rota de Border Gateway Protocol (BGP) para a sub-rede do gateway de aplicativo

   Às vezes, a rota de gateway padrão (0.0.0.0/0) é anunciada por meio dos gateways de ExpressRoute ou VPN associados à rede virtual do gateway de aplicativo. Isso interrompe o tráfego do plano de gerenciamento, que requer um caminho direto para a Internet. Nesses cenários, um UDR pode ser usado para desabilitar a propagação de rota BGP. 

   Para desabilitar a propagação de rota BGP, use as seguintes etapas:

   1. Criar um recurso de tabela de rotas no Azure.
   2. Desabilite o parâmetro de **propagação de rota do gateway de rede virtual** . 
   3. Associe a tabela de rotas à sub-rede apropriada. 

   Habilitar o UDR para esse cenário não deve interromper nenhuma configuração existente.

  **Cenário 2**: UDR para direcionar 0.0.0.0/0 para a Internet

   Você pode criar um UDR para enviar o tráfego 0.0.0.0/0 diretamente para a Internet. 

  **Cenário 3**: UDR para o serviço kubernetes do Azure com kubenet

  Se você estiver usando o kubenet com o AKS (serviço kubernetes do Azure) e o AGIC (controlador de entrada do gateway de aplicativo), precisará de uma tabela de rotas para permitir que o tráfego enviado para o pods do gateway de aplicativo seja roteado para o nó correto. Isso não será necessário se você usar o CNI do Azure. 

  Para usar a tabela de rotas para permitir que o kubenet funcione, siga as etapas abaixo:

  1. Vá para o grupo de recursos criado por AKS (o nome do grupo de recursos deve começar com "MC_")
  2. Localize a tabela de rotas criada por AKS nesse grupo de recursos. A tabela de rotas deve ser preenchida com as seguintes informações:
     - O prefixo de endereço deve ser o intervalo de IP do pods que você deseja alcançar no AKS. 
     - O tipo do próximo salto deve ser dispositivo virtual. 
     - O endereço do próximo salto deve ser o endereço IP do nó que hospeda o pods.
  3. Associe essa tabela de rotas à sub-rede do gateway de aplicativo. 
    
  **v2 cenários sem suporte**

  **Cenário 1**: UDR para dispositivos virtuais

  Qualquer cenário em que o 0.0.0.0/0 precisa ser redirecionado por meio de qualquer dispositivo virtual, uma rede virtual hub/spoke ou local (túnel forçado) não tem suporte para a v2.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre a configuração de endereço IP de front-end](configuration-front-end-ip.md).