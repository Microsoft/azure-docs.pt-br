---
title: Rotear o tráfego por meio de NVAs usando configurações personalizadas
titleSuffix: Azure Virtual WAN
description: Esse cenário ajuda a rotear o tráfego por meio do NVAs usando um NVA diferente para tráfego vinculado à Internet.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 6291964aff7c215df7f738ab103947dcb9b1b1d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036839"
---
# <a name="scenario-route-traffic-through-nvas-by-using-custom-settings"></a>Cenário: rotear o tráfego por meio de NVAs usando configurações personalizadas

Quando estiver trabalhando com o roteamento de Hub virtual de WAN virtual do Azure, você terá várias opções disponíveis. O foco deste artigo é quando você deseja rotear o tráfego por meio de uma NVA (solução de virtualização de rede) para a comunicação entre redes virtuais e branches e usar um NVA diferente para o tráfego de entrada na Internet. Para obter mais informações, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="design"></a>Design

* **Spokes** para redes virtuais conectadas ao Hub virtual. (Por exemplo, VNet 1, VNet 2 e VNet 3 no diagrama mais adiante neste artigo.)
* **VNet de serviço** para redes virtuais em que os usuários implantaram um NVA para inspecionar o tráfego de não Internet e possivelmente com serviços comuns acessados por spokes. (Por exemplo, VNet 4 no diagrama mais adiante neste artigo.)
* **VNet do perímetro** para redes virtuais em que os usuários implantaram um NVA a ser usado para inspecionar o tráfego vinculado à Internet. (Por exemplo, VNet 5 no diagrama mais adiante neste artigo.)
* **Hubs** para HUBS de WAN virtual gerenciados pela Microsoft.

A tabela a seguir resume as conexões com suporte neste cenário:

| De          | Para|Spokes|VNet de serviço|Branches|Internet|
|---|:---:|:---:|:---:|:---:|:---:|
| **Spokes**| ->| diretamente |diretamente | por meio da VNet de serviço |por meio da VNet do perímetro |
| **VNet de serviço**| ->| diretamente |N/D| diretamente | |
| **Branches** | ->| por meio da VNet de serviço |diretamente| diretamente |  |

Cada uma das células na matriz de conectividade descreve se a conectividade flui diretamente pela WAN virtual ou por uma das redes virtuais com um NVA.

Observe o seguintes detalhes:

* Spokes
  * Os spokes alcançarão outros spokes diretamente sobre os hubs de WAN virtual.
  * Os spokes obterão conectividade com os branches por meio de uma rota estática apontando para a VNet do serviço. Eles não aprendem prefixos específicos dos branches, pois esses prefixos são mais específicos e substituem o resumo.
  * Os spokes enviarão tráfego de Internet para a VNet do perímetro por meio de um emparelhamento VNet direto.
* As ramificações chegarão a spokes por meio de um roteamento estático apontando para a VNet do serviço. Eles não aprendem prefixos específicos das redes virtuais que substituem a rota estática resumida.
* A VNet de serviço será semelhante a uma VNet de serviços compartilhados que precisa ser acessada de todas as redes virtuais e de cada ramificação.
* A VNet do perímetro não precisa ter conectividade sobre a WAN virtual, pois o único tráfego que ele dará suporte vem por emparelhamentos de rede virtual direta. No entanto, para simplificar a configuração, use o mesmo modelo de conectividade que para a VNet do perímetro.

Há três padrões distintos de conectividade, que são convertidos em três tabelas de rotas. As associações para as diferentes redes virtuais são:

* Spokes
  * Tabela de rotas associada: **RT_V2B**
  * Propagando para tabelas de rotas: **RT_V2B** e **RT_SHARED**
* NVA VNets (VNet de serviço e rede virtual DMZ):
  * Tabela de rotas associada: **RT_SHARED**
  * Propagando para tabelas de rotas: **RT_SHARED**
* Filia
  * Tabela de rotas associada: **padrão**
  * Propagando para tabelas de rotas: **RT_SHARED** e **padrão**

> [!NOTE]
> Verifique se os VNets spoke não estão se propagando para o rótulo padrão. Isso garante que o tráfego de branches para spoke VNets seja encaminhado para o NVAs.

Essas rotas estáticas garantem que o tráfego de e para a rede virtual e a ramificação passe pelo NVA na VNet do serviço (VNet 4):

| Descrição | Tabela de rotas | Rota estática              |
| ----------- | ----------- | ------------------------- |
| Branches    | RT_V2B      | 10.2.0.0/16-> vnet4conn  |
| NVA spokes  | Padrão     | 10.1.0.0/16-> vnet4conn  |

Agora você pode usar a WAN virtual para selecionar a conexão correta para a qual enviar os pacotes. Você também precisa usar a WAN virtual para selecionar a ação correta a ser tomada ao receber esses pacotes. Você usa as tabelas de rotas de conexão para isso, da seguinte maneira:

| Descrição | Conexão | Rota estática            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16-> 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16-> 10.4.0.5 |

Para obter mais informações, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="architecture"></a>Arquitetura

O diagrama a seguir mostra a arquitetura descrita anteriormente neste artigo.

No diagrama, há um hub; **Hub 1**.

* O **Hub 1** está diretamente conectado a NVA VNets **vnet 4** e **vnet 5**.

* O tráfego entre VNets 1, 2, 3 e as ramificações devem passar por **VNet 4 NVA** 10.4.0.5.

* Espera-se que todo o tráfego associado à Internet de VNets 1, 2 e 3 Vá por meio do **VNet 5 NVA** 10.5.0.5.

:::image type="content" source="./media/routing-scenarios/nva-custom/architecture.png" alt-text="Diagrama da arquitetura de rede.":::

## <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

:::image type="content" source="./media/routing-scenarios/nva-custom/workflow.png" alt-text="Diagrama de fluxo de trabalho." lightbox="./media/routing-scenarios/nva-custom/workflow.png":::

Para configurar o roteamento via NVA, considere as seguintes etapas:

1. Para que o tráfego associado à Internet vá por meio da VNet 5, você precisa de VNets 1, 2 e 3 para se conectar diretamente por meio do emparelhamento de rede virtual para a VNet 5. Você também precisa de uma configuração de rota definida pelo usuário nas redes virtuais para 0.0.0.0/0 e 10.5.0.5 do próximo salto.

   Se você não quiser se conectar VNets 1, 2 e 3 à VNet 5 e, em vez disso, simplesmente usar o NVA na VNet 4 para rotear o tráfego 0.0.0.0/0 de ramificações (conexões VPN ou ExpressRoute locais), vá para o [fluxo de trabalho alternativo](#alternate).

   No entanto, se você quiser que o tráfego de VNet para VNet entre no NVA, precisará desconectar a VNet 1, 2, 3 do Hub virtual e conectá-la ou empilhá-la acima do VNet4 NVA do spoke. Na WAN virtual, o tráfego de VNet para VNet passa pelo Hub WAN virtual ou pelo firewall do Azure do hub de WAN virtual (Secure Hub). Se VNets emparelhar diretamente usando o emparelhamento de VNet, eles poderão se comunicar diretamente ignorando o trânsito por meio do Hub virtual.

1. Na portal do Azure, vá para o Hub virtual e crie uma tabela de rotas personalizada chamada **RT_Shared**. Esta tabela aprende as rotas por meio da propagação de todas as redes virtuais e conexões de ramificação. Você pode ver essa tabela vazia no diagrama a seguir.

   * **Rotas:** Você não precisa adicionar rotas estáticas.

   * **Associação:** Selecione VNets 4 e 5, o que significa que as conexões dessas redes virtuais são associadas à tabela de rotas **RT_Shared**.

   * **Propagação:** Como você deseja que todas as ramificações e conexões de rede virtual propaguem suas rotas dinamicamente para essa tabela de rotas, selecione branches e todas as redes virtuais.

1. Crie uma tabela de rotas personalizada chamada **RT_V2B** para direcionar o tráfego de VNets 1, 2 e 3 para branches.

   * **Rotas:** Adicione uma entrada de rota estática agregada para branches, com o próximo salto como a conexão VNet 4. Configure uma rota estática na conexão da VNet 4 para prefixos de ramificação. Indique o próximo salto como o IP específico do NVA na VNet 4.

   * **Associação:** Selecione todos os **VNets 1, 2 e 3**. Isso implica que as conexões de VNet 1, 2 e 3 serão associadas a essa tabela de rotas e poderão aprender rotas (estáticas e dinâmicas via propagação) nesta tabela de rotas.

   * **Propagação:** As conexões propagam rotas para tabelas de rotas. Selecionar VNets 1, 2 e 3 permite propagar rotas de VNets 1, 2 e 3 para esta tabela de rotas. Não há necessidade de propagar rotas de conexões de ramificação para **RT_V2B**, porque o tráfego de rede virtual de ramificação passa por NVA na VNet 4.
  
1. Edite a tabela de rotas padrão, **Defaultroutetable**.

   Todas as conexões VPN, Azure ExpressRoute e VPN de usuário estão associadas à tabela de rotas padrão. Todas as conexões VPN, ExpressRoute e VPN de usuário propagam rotas para o mesmo conjunto de tabelas de rotas.

   * **Rotas:** Adicione uma entrada de rota estática agregada para VNets 1, 2 e 3, com o próximo salto como a conexão VNet 4. Configure uma rota estática na conexão da VNet 4 para prefixos agregados de VNet 1, 2 e 3. Indique o próximo salto como o IP específico do NVA na VNet 4.

   * **Associação:** Verifique se a opção de branches (VPN/ER/P2S) está selecionada, garantindo que as conexões de ramificação locais estejam associadas à tabela de rotas padrão.

   * **Propagação de:** Verifique se a opção de branches (VPN/ER/P2S) está selecionada, garantindo que as conexões locais estejam propagando rotas para a tabela de rotas padrão.

## <a name="alternate-workflow"></a><a name="alternate"></a>Fluxo de trabalho alternativo

Neste fluxo de trabalho, você não se conecta VNets 1, 2 e 3 à VNet 5. Em vez disso, você usa o NVA na VNet 4 para rotear o tráfego 0.0.0.0/0 de ramificações (conexões VPN ou ExpressRoute locais).

:::image type="content" source="./media/routing-scenarios/nva-custom/alternate.png" alt-text="Diagrama de fluxo de trabalho alternativo." lightbox="./media/routing-scenarios/nva-custom/alternate.png":::

Para configurar o roteamento via NVA, considere as seguintes etapas:

1. Na portal do Azure, vá para o Hub virtual e crie uma tabela de rotas personalizada chamada **RT_NVA** para direcionar o tráfego por meio do NVA 10.4.0.5  

   * **Rotas:**   Nenhuma ação é necessária.

   * **Associação:**   Selecione **VNet4**. Isso significa que a conexão VNet 4 será associada a essa tabela de rotas e poderá aprender rotas (estáticas e dinâmicas via propagação) nesta tabela de rotas.

   * **Propagação:**   As conexões propagam rotas para tabelas de rotas. Selecionar VNets 1, 2 e 3 permite propagar rotas de VNets 1, 2 e 3 para esta tabela de rotas. Selecionar branches (VPN/ER/P2S) permite propagar rotas de branches/conexões locais para esta tabela de rotas. Todas as conexões VPN, ExpressRoute e VPN de usuário propagam rotas para o mesmo conjunto de tabelas de rotas.

1. Crie uma tabela de rotas personalizada chamada **RT_VNET** para direcionar o tráfego de VNets 1, 2 e 3 para branches ou a Internet (0.0.0.0/0) por meio do NVA VNet4. O tráfego de VNet para VNet será direto e não por meio de NVA da VNet 4. Se você quiser que o tráfego passe pelo NVA, desconecte VNet 1, 2 e 3 e conecte-os usando o emparelhamento VNet ao VNet4.

   * **Circula**  

     * Adicione uma rota agregada ' 10.2.0.0/16 ' com o próximo salto como a conexão VNet 4 para o tráfego que vai de VNets 1, 2 e 3 em relação a ramificações. Na conexão VNet4, configure uma rota para ' 10.2.0.0/16 ' e indique o próximo salto como o IP específico do NVA na VNet 4.

     * Adicione uma rota ' 0.0.0.0/0 ' com o próximo salto como a conexão VNet 4. "0.0.0.0/0" é adicionado para implicar no envio de tráfego para a Internet. Ele não implica em prefixos de endereço específicos pertencentes a VNets ou branches. Na conexão VNet4, configure uma rota para ' 0.0.0.0/0 ' e indique o próximo salto como o IP específico do NVA na VNet 4.

   * **Associação:**   Selecione todos os **VNets 1, 2 e 3**. Isso implica que as conexões de VNet 1, 2 e 3 serão associadas a essa tabela de rotas e poderão aprender rotas (estáticas e dinâmicas via propagação) nesta tabela de rotas.

   * **Propagação:**   As conexões propagam rotas para tabelas de rotas. Selecionar VNets 1, 2 e 3 habilitar a propagação de rotas de VNets 1, 2 e 3 para esta tabela de rotas. Verifique se a opção de branches (VPN/ER/P2S) não está selecionada. Isso garante que as conexões locais não possam chegar ao VNets 1, 2 e 3 diretamente.

1. Edite a tabela de rotas padrão, **Defaultroutetable**.

   Todas as conexões VPN, Azure ExpressRoute e VPN de usuário estão associadas à tabela de rotas padrão. Todas as conexões VPN, ExpressRoute e VPN de usuário propagam rotas para o mesmo conjunto de tabelas de rotas.

   * **Circula**  

     * Adicione uma rota agregada ' 10.1.0.0/16 ' para **VNets 1, 2 e 3** com o próximo salto como a **conexão VNet 4**.

     * Adicione uma rota ' 0.0.0.0/0 ' com o próximo salto como a **conexão VNet 4**. "0.0.0.0/0" é adicionado para implicar no envio de tráfego para a Internet. Ele não implica em prefixos de endereço específicos pertencentes a VNets ou branches. Na etapa anterior da conexão VNet4, você já terá configurado uma rota para ' 0.0.0.0/0 ', com o próximo salto para ser o IP específico do NVA na VNet 4.

   * **Associação:**   Verifique se a opção de branches **(VPN/er/P2S)** está selecionada. Isso garante que as conexões de ramificação locais sejam associadas à tabela de rotas padrão. Todas as conexões VPN, Azure ExpressRoute e VPN de usuário são associadas somente à tabela de rotas padrão.

   * **Propagação de:**   Verifique se a opção de branches **(VPN/er/P2S)** está selecionada. Isso garante que as conexões locais estejam propagando rotas para a tabela de rotas padrão. Todas as conexões VPN, ExpressRoute e VPN de usuário propagam rotas para o ' mesmo conjunto de tabelas de rotas '.

   >[!Note]
   >
   > * Os usuários do portal devem habilitar "propagar para a rota padrão" em conexões (VPN/ER/P2S/VNet) para que a rota 0.0.0.0/0 entre em vigor.
   > * Os usuários do PS/CLI/REST devem definir o sinalizador ' enableinternetsecurity ' como true para que a rota 0.0.0.0/0 tenha efeito.
   > * A conexão de rede virtual não oferece suporte ao próximo IP de salto ' múltiplo/exclusivo ' para a solução de virtualização de rede ' mesma ' em uma VNet SPOKE ' se ' uma das rotas com o próximo IP de salto for indicada como endereço IP público ou 0.0.0.0/0 (Internet)

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
* Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
