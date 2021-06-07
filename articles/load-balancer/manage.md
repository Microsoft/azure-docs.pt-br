---
title: Configurações do portal do Azure Load Balancer
description: Introdução ao aprendizado sobre Azure Load Balancer configurações do portal
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/8/2020
ms.author: allensu
ms.openlocfilehash: e1080aea12e70f4312fbee07b063d5a5cfbd1201
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89596248"
---
# <a name="azure-load-balancer-portal-settings"></a>Configurações do portal do Azure Load Balancer

Conforme você cria Azure Load Balancer, as informações neste artigo o ajudarão a saber mais sobre as configurações individuais e qual é a configuração certa para você.

## <a name="create-load-balancer"></a>Criar um balanceador de carga

Azure Load Balancer é um balanceador de carga de rede que distribui o tráfego entre instâncias de VM no pool de back-end.

### <a name="basics"></a>Noções básicas

Na guia **noções básicas** da página Criar portal do balanceador de carga, você verá as seguintes informações:



| Setting |  Detalhes |
| ---------- | ---------- |
| Subscription  | Selecione sua assinatura. Essa seleção é a assinatura na qual você deseja que o balanceador de carga seja implantado. |
| Resource group | Selecione **criar novo** e digite o nome do grupo de recursos na caixa de texto. Se você tiver um grupo de recursos existente criado, selecione-o. |
| Nome | Essa configuração é o nome do seu Azure Load Balancer. |
| Região | Selecione uma região do Azure na qual você gostaria de implantar o balanceador de carga. |
| Tipo | O balanceador de carga tem dois tipos: </br> **Interno (privado)** </br> **Público (externo)**.</br> Um ILB (balanceador de carga interno) roteia o tráfego para os membros do pool de back-end por meio de um endereço IP privado.</br> Um balanceador de carga público direciona solicitações de clientes pela Internet para o pool de back-end.</br> Saiba mais sobre os [tipos de balanceador de carga](components.md#frontend-ip-configuration-).|
| SKU  | Selecione **Padrão**. </br> O balanceador de carga tem duas SKUs: **básico** e **Standard**. </br> O básico tem funcionalidade limitada. </br> O **padrão** é recomendado para cargas de trabalho de produção. </br> Saiba mais sobre [SKUs](skus.md). |

Se você selecionar **público** como seu tipo, verá as seguintes informações:

| Setting |  Detalhes |
| ---------- | ---------- |
| Endereço IP público | Selecione **criar novo** para criar um endereço IP público para o balanceador de carga público. </br> Se você tiver um IP público existente, selecione **usar existente**.  |
| Nome do endereço IP público | O nome do endereço IP público.|
| SKU do endereço IP público | Os endereços IP públicos têm dois SKUs: **básico** e **Standard**. </br> Basic não dá suporte a atributos de resiliência de zona e zonas. </br> O **padrão** é recomendado para cargas de trabalho de produção. </br> As SKUs do balanceador de carga e do endereço IP público **devem corresponder**. |
| Atribuição | **Estático** é selecionado automaticamente para Standard. </br> Os IPs públicos básicos têm dois tipos: **dinâmico** e **estático**. </br> Os endereços IP públicos dinâmicos não são atribuídos até a criação. </br> Os IPs poderão ser perdidos se o recurso for excluído. </br> São recomendados endereços IP estáticos. |
| Zona de disponibilidade | Selecione **Com redundância de zona** para criar uma balanceador de carga resiliente. </br> Para criar um balanceador de carga zonal, selecione uma zona específica de **1**, **2** ou **3**. </br> Zonas de suporte do balanceador de carga padrão e IPs públicos. </br> Saiba mais sobre o [balanceador de carga e zonas de disponibilidade](load-balancer-standard-availability-zones.md). </br> Você não verá a seleção de zona para básico. O balanceador de carga básico não é compatível com zonas. |
| Adicionar um endereço IPv6 público | O balanceador de carga dá suporte a endereços **IPv6** para seu front-end. </br> Saiba mais sobre o [balanceador de carga e o IPv6](load-balancer-ipv6-overview.md)
| Preferência de roteamento | Selecione **Microsoft Network**. </br> Rede da Microsoft significa que o tráfego é roteado por meio da rede global da Microsoft. </br> Internet significa que o tráfego é roteado pela rede do provedor de serviços de Internet. </br> Saiba mais sobre as [preferências de roteamento](../virtual-network/routing-preference-overview.md)|

:::image type="content" source="./media/manage/create-public-load-balancer-basics.png" alt-text="Crie um balanceador de carga público." border="true":::

Se você selecionar **interno** no tipo, verá as seguintes informações:

| Setting |  Detalhes |
| ---------- | ---------- |
| Rede virtual | A rede virtual da qual você deseja que o balanceador de carga interno faça parte. </br> O endereço IP de front-end privado que você selecionar para o balanceador de carga interno será proveniente dessa rede virtual. |
| Atribuição de endereço IP | Suas opções são **estáticas** ou **dinâmicas**. </br> Estático garante que o IP não seja alterado. Um IP dinâmico pode ser alterado. |
| Zona de disponibilidade | As opções são: </br> **Redundância de zona** </br> **Zona 1** </br> **Zona 2** </br> **Zona 3** </br> Para criar um balanceador de carga que seja altamente disponível e resiliente a falhas de zona de disponibilidade, selecione um IP **com redundância de zona** . |

:::image type="content" source="./media/manage/create-internal-load-balancer-basics.png" alt-text="Crie um balanceador de carga interno." border="true":::

## <a name="frontend-ip-configuration"></a>Configuração do IP front-end

O endereço IP de seu Azure Load Balancer. Trata-se do ponto de contato para os clientes. 

Você pode ter uma ou várias configurações de IP de front-end. Se você tiver feito a seção noções básicas acima, já terá criado um front-end para o balanceador de carga. 

Se você quiser adicionar uma configuração de IP de front-end ao balanceador de carga, vá para o balanceador de carga no portal do Azure, selecione **configuração de IP de front-end** e, em seguida, selecione **+ Adicionar**.

| Setting |  Detalhes |
| ---------- | ---------- |
| Nome | O nome da sua configuração de IP de front-end. |
| Versão IP | A versão do endereço IP que você gostaria que o front-end tivesse. </br> O balanceador de carga dá suporte às configurações de IP de front-end IPv4 e IPv6. |
| Tipo de IP | Tipo de IP determina se um único endereço IP está associado ao front-end ou a um intervalo de endereços IP usando um prefixo de IP. </br> Um [prefixo IP público](../virtual-network/public-ip-address-prefix.md) auxilia quando você precisa se conectar ao mesmo ponto de extremidade repetidamente. O prefixo garante que portas suficientes sejam dadas para auxiliar com problemas de porta SNAT. |
| Endereço IP público (ou prefixo se você selecionou o prefixo acima) | Selecione ou crie um novo IP público (ou prefixo) para o front-end do balanceador de carga. |

:::image type="content" source="./media/manage/frontend.png" alt-text="Página Criar configuração de IP de front-end." border="true":::

## <a name="backend-pools"></a>Pools de back-end

Um pool de endereços de back-end contém os endereços IP das interfaces de rede virtual no pool de back-end. 

Se você quiser adicionar um pool de back-end ao balanceador de carga, vá para o balanceador de carga no portal do Azure, selecione **pools de back-end** e, em seguida, selecione **+ Adicionar**.

| Setting | Detalhes |
| ---------- |  ---------- |
| Nome | O nome do pool de back-end. |
| Rede virtual | A rede virtual em que as instâncias de back-end são. |
| Versão IP | Suas opções são **IPv4** ou **IPv6**. |

Você pode adicionar máquinas virtuais ou conjuntos de dimensionamento de máquinas virtuais ao pool de back-end de seu Azure Load Balancer. Crie primeiro as máquinas virtuais ou os conjuntos de dimensionamento de máquinas virtuais. Em seguida, adicione-os ao balanceador de carga no Portal.

:::image type="content" source="./media/manage/backend.png" alt-text="Página Criar pool de back-end." border="true":::

## <a name="health-probes"></a>Investigações de integridade

Uma investigação de integridade é usada para monitorar o status de suas VMs ou instâncias de back-end. O status da investigação de integridade determina quando novas conexões são enviadas a uma instância baseada em verificações de integridade. 

Se você quiser adicionar uma investigação de integridade ao balanceador de carga, vá para o balanceador de carga no portal do Azure, selecione **investigações de integridade** e, em seguida, selecione **+ Adicionar**.

| Setting | Detalhes |
| ---------- | ---------- |
| Nome | O nome da sua investigação de integridade. |
| Protocolo | O protocolo selecionado determina o tipo de verificação usado para determinar se as instâncias de back-end estão íntegras. </br> As opções são: </br> **TCP** </br> **HTTPS** </br> **HTTP** </br> Verifique se você está usando o protocolo certo. Essa seleção dependerá da natureza do seu aplicativo. </br> A configuração da investigação de integridade e das respostas de investigação determina quais instâncias de pool de back-end receberão novos fluxos. </br> Você pode usar investigações de integridade para detectar a falha de um aplicativo em um ponto de extremidade de back-end. </br> Saiba mais sobre [investigações de integridade](load-balancer-custom-probe-overview.md). |
| Porta | A porta de destino para a investigação de integridade. </br> Essa configuração é a porta na instância de back-end que a investigação de integridade usará para determinar a integridade da instância. |
| Intervalo | O número de segundos entre as tentativas de investigação. </br> O intervalo determinará com que frequência a investigação de integridade tentará alcançar a instância de back-end. </br> Se você selecionar 5, a segunda tentativa de investigação será feita após 5 segundos e assim por diante. |
| Limite não íntegro | O número de falhas de investigação consecutivas que devem ocorrer antes que uma VM seja considerada não íntegra.</br> Se você selecionar 2, nenhum fluxo novo será definido para essa instância de back-end após duas falhas consecutivas. |

:::image type="content" source="./media/manage/health-probe.png" alt-text="Adicionar investigação de integridade." border="true":::

## <a name="load-balancing-rules"></a>Regras de balanceamento de carga

Define como o tráfego de entrada é distribuído a todas as instâncias no pool de back-end. Uma regra de balanceamento de carga mapeia uma configuração especificada de porta e IP de front-end para várias portas e endereços IP de back-end.

Se você quiser adicionar uma regra de balanceador de carga ao balanceador de carga, vá para o balanceador de carga no portal do Azure, selecione **regras de balanceamento de carga** e, em seguida, selecione **+ Adicionar**.
    
| Setting | Detalhes |
| ---------- | ---------- |
| Nome | O nome da regra do balanceador de carga. |
| Versão IP | Suas opções são **IPv4** ou **IPv6**.  |
| Endereço IP de front-end | Selecione o endereço IP de front-end. </br> O endereço IP de front-end do balanceador de carga ao qual você deseja associar a regra de balanceador de carga.|
| Protocolo | Azure Load Balancer é um balanceador de carga de rede de camada 4. </br> Suas opções são: **TCP** ou **UDP**. |
| Porta | Essa configuração é a porta associada ao IP de front-end em que você deseja que o tráfego seja distribuído com base nessa regra de balanceamento de carga. |
| Porta de back-end | Essa configuração é a porta nas instâncias no pool de back-end para o qual você deseja que o balanceador de carga envie o tráfego. Essa configuração pode ser igual à porta de front-end ou diferente se você precisar da flexibilidade para seu aplicativo. |
| Pool de back-end | O pool de back-end para o qual você deseja aplicar essa regra de balanceador de carga. |
| Investigação de integridade | A investigação de integridade que você criou para verificar o status das instâncias no pool de back-end. </br> Somente as instâncias íntegras receberão novo tráfego. |
| Persistência de sessão |  As opções são: </br> **Nenhum** </br> **IP do cliente** </br> **IP e protocolo do cliente**</br> </br> Mantenha o tráfego de um cliente para a mesma máquina virtual no pool de back-end. Esse tráfego será mantido durante a sessão. </br> **Nenhum** especifica que as solicitações sucessivas do mesmo cliente podem ser tratadas por qualquer máquina virtual. </br> **IP do cliente** especifica que as solicitações sucessivas do mesmo endereço IP do cliente serão tratadas pela mesma máquina virtual. </br> **IP do cliente e protocolo** Verifique se as solicitações sucessivas do mesmo endereço IP do cliente e do protocolo serão manipuladas pela mesma máquina virtual. </br> Saiba mais sobre [modos de distribuição](load-balancer-distribution-mode.md). |
| Tempo limite de ociosidade (minutos) | Manter uma conexão **TCP** ou **http** aberta sem depender de clientes para enviar mensagens Keep-Alive |  
| Redefinição de TCP | O balanceador de carga pode enviar **redefinições de TCP** para ajudar a criar um comportamento de aplicativo mais previsível quando a conexão estiver ociosa. </br> Saiba mais sobre a [redefinição de TCP](load-balancer-tcp-reset.md)|
| IP flutuante | O IP flutuante é a terminologia do Azure para uma parte do que é conhecido como **DSR (retorno de servidor direto)**. </br> O DSR consiste em duas partes: <br> 1. topologia de fluxo </br> 2. um esquema de mapeamento de endereço IP em um nível de plataforma. </br></br> Azure Load Balancer sempre funciona em uma topologia de fluxo DSR se o IP flutuante está habilitado ou não. </br> Essa operação significa que a parte de saída de um fluxo é sempre reescrita corretamente para fluir diretamente de volta para a origem. </br> Sem o IP flutuante, o Azure expõe um esquema de mapeamento de endereço IP de balanceamento de carga tradicional, o IP das instâncias de VM. </br> Habilitar o IP flutuante altera o mapeamento de endereço IP para o IP de front-end do balanceador de carga para permitir flexibilidade adicional. </br> Para obter mais informações, consulte [vários front-ends para Azure Load Balancer](load-balancer-multivip-overview.md).|
| Criar regras de saída implícitas | Selecione **Não**. </br> Padrão: **disableOutboundSnat = false**  </br> Nesse caso, a saída ocorre por meio do mesmo IP de front-end. </br></br> **disableOutboundSnat = true** </br>Nesse caso, as regras de saída são necessárias para a saída. |

:::image type="content" source="./media/manage/load-balancing-rule.png" alt-text="Adicionar regra de balanceamento de carga." border="true":::

## <a name="inbound-nat-rules"></a>Regras NAT de entrada

Uma regra NAT de entrada encaminha o tráfego de entrada enviado para a combinação de porta e endereço IP de front-end. 

O tráfego é enviado para uma máquina virtual ou uma instância específica no pool de back-end. O encaminhamento de porta é realizado pela mesma distribuição baseada em hash que o balanceamento de carga.

Se seu cenário exigir sessões de protocolo RDP (RDP) ou Secure Shell (SSH) para separar instâncias de VM em um pool de back-end. Vários pontos de extremidade internos podem ser mapeados para portas no mesmo endereço IP de front-end. 

Os endereços IP de front-end podem ser usados para administrar remotamente suas VMs sem uma caixa de salto adicional.

Se você quiser adicionar uma regra NAT de entrada ao balanceador de carga, vá para o balanceador de carga no portal do Azure, selecione **regras NAT de entrada** e, em seguida, selecione **+ Adicionar**.

| Setting | Detalhes |
| ---------- | ---------- |
| Nome | O nome da sua regra NAT de entrada |
| Endereço IP de front-end | Selecione o endereço IP de front-end. </br> O endereço IP de front-end do balanceador de carga ao qual você deseja associar a regra NAT de entrada. |
| Versão IP | Suas opções são IPv4 e IPv6. |
| Serviço | O tipo de serviço em que você estará executando Azure Load Balancer. </br> Uma seleção aqui atualizará as informações de porta adequadamente. |
| Protocolo | Azure Load Balancer é um balanceador de carga de rede de camada 4. </br> Suas opções são: TCP ou UDP. |
| Tempo limite de ociosidade (minutos) | Mantenha uma conexão TCP ou HTTP aberta sem depender de clientes para enviar mensagens Keep-Alive. |
| Redefinição de TCP | Load Balancer pode enviar redefinições de TCP para ajudar a criar um comportamento de aplicativo mais previsível quando a conexão estiver ociosa. </br> Saiba mais sobre a [redefinição de TCP](load-balancer-tcp-reset.md) |
| Porta | Essa configuração é a porta associada ao IP de front-end em que você deseja que o tráfego seja distribuído com base nessa regra NAT de entrada. |
| Máquina virtual de destino | A parte da máquina virtual do pool de back-end ao qual você deseja associar esta regra. |
| Mapeamento de porta | Essa configuração pode ser padrão ou personalizada com base em sua preferência de aplicativo. |

:::image type="content" source="./media/manage/inbound-nat-rule.png" alt-text="Adicionar regra NAT de entrada." border="true":::

## <a name="outbound-rules"></a>Regras de saída

As regras de saída do balanceador de carga configuram o SNAT de saída para as VMs no pool de back-end.

Se você quiser adicionar uma regra de saída ao balanceador de carga, vá para o balanceador de carga no portal do Azure, selecione **regras de saída** e, em seguida, selecione **+ Adicionar**.

| Setting | Detalhes |
| ------- | ------ |
| Nome | O nome da regra de saída. |
| Endereço IP de front-end | Selecione o endereço IP de front-end. </br> O endereço IP de front-end do balanceador de carga ao qual você deseja associar a regra de saída. |
| Protocolo | Azure Load Balancer é um balanceador de carga de rede de camada 4. </br> Suas opções são: **All**, **TCP** ou **UDP**. |
| Tempo limite de ociosidade (minutos) | Mantenha uma conexão **TCP** ou **http** aberta sem depender de clientes para enviar mensagens Keep-Alive. |
| Redefinição de TCP | O balanceador de carga pode enviar **redefinições de TCP** para ajudar a criar um comportamento de aplicativo mais previsível quando a conexão estiver ociosa. </br> Saiba mais sobre a [redefinição de TCP](load-balancer-tcp-reset.md) |
| Pool de back-end | O pool de back-end no qual você deseja aplicar essa regra de saída. |

### <a name="port-allocation"></a>Alocação de porta

| Setting | Detalhes |
| ------- | ------ |
| Alocação de porta | É recomendável selecionar **manualmente escolher o número de portas de saída**.|

### <a name="outbound-ports"></a>Portas de saída

| Setting | Detalhes |
| ------- | ------ |
| Escolher por | Selecione **Portas por instância** |
| Portas por instância | Insira **10.000**. |

:::image type="content" source="./media/manage/outbound-rule.png" alt-text="Adicionar regra de saída de entrada." border="true":::

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre os diferentes termos e configurações no portal do Azure para Azure Load Balancer.

* [Saiba](./load-balancer-overview.md) mais sobre Azure Load Balancer.
* [Perguntas frequentes](./load-balancer-faqs.md) para Azure Load Balancer.