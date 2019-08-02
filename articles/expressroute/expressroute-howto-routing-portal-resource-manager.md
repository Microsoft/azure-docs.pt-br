---
title: 'Configurar o emparelhamento para um circuito – ExpressRoute: Azure | Microsoft Docs'
description: Este artigo documenta as etapas para criar e provisionar privado de ExpressRoute e emparelhamento da Microsoft. Este artigo também demonstra como verificar o status, atualizar ou excluir emparelhamentos de um circuito.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 08d8103c4b35148a87d347e31b11c7c8c968598b
ms.sourcegitcommit: dda9fc615db84e6849963b20e1dce74c9fe51821
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622339"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Criar e modificar o emparelhamento de um circuito de ExpressRoute

Este artigo ajuda você a criar e gerenciar a configuração de roteamento de um circuito de ExpressRoute do Azure Resource Manager (ARM), usando o portal do Azure. Você também pode verificar o status, atualizar ou excluir e desprovisionar emparelhamentos de um circuito do ExpressRoute. Se quiser usar um método diferente para trabalhar com seu circuito, selecione um artigo na lista a seguir:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Vídeo – Emparelhamento privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo – Emparelhamento público](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vídeo – Emparelhamento da Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

Você pode configurar privado do Azure e emparelhamento da Microsoft para um circuito do ExpressRoute (emparelhamento público do Azure é preterido para novos circuitos). Você pode configurar emparelhamentos em qualquer ordem escolhida. No entanto, você deve concluir a configuração de um emparelhamento por vez. Para obter mais informações sobre o roteamento de domínios e emparelhamentos, confira [Sobre circuitos e emparelhamentos](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Pré-requisitos de configuração

* Verifique se você leu a página de [pré-requisitos](expressroute-prerequisites.md), a página de [requisitos do roteamento](expressroute-routing.md) e a página [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Você deve ter um circuito do ExpressRoute ativo. Antes de continuar, siga as instruções para [criar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e para que o circuito seja habilitado pelo provedor de conectividade. Para configurar o emparelhamento (s), o circuito do ExpressRoute deve ser em um estado provisionado e habilitado. 
* Se você planeja usar um hash MD5/chave compartilhado, certifique-se de usar isso em ambos os lados do túnel e limite o número de caracteres alfanuméricos, um máximo de 25. Não há suporte para caracteres especiais. 

Estas instruções se aplicam apenas a circuitos criados com provedores de serviço que oferecem serviços de conectividade de Camada 2. Se você estiver usando um provedor de serviços que ofereça serviços gerenciados de Camada 3 (normalmente um IPVPN, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você. 

> [!IMPORTANT]
> Atualmente, não anunciamos emparelhamentos configurados pelos provedores de serviço por meio do portal de gerenciamento de serviço. Estamos trabalhando para habilitar esse recurso em breve. Verifique com seu provedor de serviços antes de configurar os emparelhamentos via protocolo BGP.
> 
> 

## <a name="msft"></a>Emparelhamento da Microsoft

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento da Microsoft para um circuito do ExpressRoute.

> [!IMPORTANT]
> O emparelhamento da Microsoft de circuitos do ExpressRoute configurados antes de 1º de agosto de 2017 terá todos os prefixos de serviço anunciados através do emparelhamento da Microsoft, mesmo que os filtros de rota não estejam definidos. O emparelhamento da Microsoft de circuitos de ExpressRoute configurados em ou após 1º de agosto de 2017 não terá nenhum prefixo anunciado até que um filtro de rota seja anexado ao circuito. Para obter mais informações, consulte [Configurar um filtro de rota para o emparelhamento da Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Criar emparelhamento da Microsoft

1. Configure o circuito do ExpressRoute. Verifique as **status do provedor** para garantir que o circuito está totalmente provisionado pelo provedor de conectividade antes de continuar.

   Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, você pode solicitar a ele a habilitação do emparelhamento da Microsoft. Nesse caso, você não precisará seguir as instruções listadas nas próximas seções. No entanto, se seu provedor de conectividade não gerenciar o roteamento para você, após a criação do circuito, continue com estas etapas.

   **Circuito - status do provedor: Não provisionado**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Status do provedor: Não provisionado")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Circuito - status do provedor: Provisionado**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Status do provedor = provisionado")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Configurar o emparelhamento da Microsoft para o circuito. Você precisa ter as seguintes informações antes de continuar:

   * Uma sub-rede /30 para o link principal. Este valor deve ser um prefixo IPv4 público válido próprio e registrado em um RIR/IRR. Dessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao seu roteador já que a Microsoft usa o segundo IP utilizável para seu roteador.
   * Uma sub-rede /30 para o link secundário. Este valor deve ser um prefixo IPv4 público válido próprio e registrado em um RIR/IRR. Dessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao seu roteador já que a Microsoft usa o segundo IP utilizável para seu roteador.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN. Para os links Primário e Secundário, você deve usar a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes.
   * Prefixos anunciados: você precisa fornecer uma lista com todos os prefixos que planeja anunciar na sessão BGP. Somente prefixos de endereços IP públicos são aceitos. Caso você planeje enviar um conjunto de prefixos, poderá enviar uma lista separada por vírgulas. Esses prefixos devem ser registrados em seu nome em um RIR/IRR.
   * **Opcional –** ASN de cliente: se você estiver anunciando prefixos não registrados com o número AS de emparelhamento, especifique o número AS com o qual eles estão registrados.
   * Nome do registro de roteamento: é possível especificar o RIR/IRR no qual o número AS e os prefixos estão registrados.
   * **Opcional –** Um hash MD5 se você optar por usar um.
3. Você pode selecionar o emparelhamento que deseja configurar, conforme mostrado no exemplo a seguir. Selecione a linha de emparelhamento da Microsoft.

   [![Selecione a linha de emparelhamento da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "selecione a linha de emparelhamento da Microsoft")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Configure o emparelhamento da Microsoft. **Salvar** a configuração depois de especificar todos os parâmetros. A imagem a seguir mostra um exemplo de configuração:

   ![Configurar o emparelhamento da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

   Se o circuito for para uma validação' necessária' estado, você deve abrir um tíquete de suporte para mostrar a prova de propriedade dos prefixos à nossa equipe de suporte. Você pode abrir um tíquete de suporte diretamente no portal, conforme mostrado no exemplo a seguir:

   ![Validação é necessária - tíquete de suporte](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Depois que a configuração é aceita com êxito, você verá algo semelhante à seguinte imagem:

   ![Status de emparelhamento: Configurado](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "status de emparelhamento: Configurado")]

### <a name="getmsft"></a>Para exibir detalhes de emparelhamento da Microsoft

Você pode exibir as propriedades de emparelhamento selecionando a linha para o emparelhamento da Microsoft.

[![Exibir propriedades de emparelhamento da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "exibir propriedades")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="updatemsft"></a>Para atualizar a configuração de emparelhamento da Microsoft

Você pode selecionar a linha para o emparelhamento que você deseja modificar e, em seguida, modificar as propriedades de emparelhamento e salvar suas modificações.

![Selecione a linha de emparelhamento](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="deletemsft"></a>Para excluir emparelhamento da Microsoft

Você pode remover a configuração de emparelhamento, clicando no ícone de exclusão, conforme mostrado na imagem a seguir:

![Excluir emparelhamento](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="private"></a>Emparelhamento privado do Azure

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento privado do Azure para um circuito do ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Criar um emparelhamento privado do Azure

1. Configure o circuito do ExpressRoute. Verifique se o circuito foi totalmente provisionado pelo provedor de conectividade antes de continuar. 

   Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, você pode solicitar a ele a habilitação do emparelhamento privado do Azure. Nesse caso, você não precisará seguir as instruções listadas nas próximas seções. No entanto, se seu provedor de conectividade não gerenciar o roteamento para você, após a criação do circuito, continue com as próximas etapas.

   **Circuito - status do provedor: Não provisionado**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Status do provedor = não provisionado")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Circuito - status do provedor: Provisionado**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Status do provedor = provisionado")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Configure o emparelhamento privado do Azure para o circuito. Verifique se você tem os seguintes itens antes de continuar com as próximas etapas:

   * Uma sub-rede /30 para o link principal. A sub-rede não deve fazer parte de nenhum espaço de endereçamento reservado para redes virtuais. Dessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao seu roteador já que a Microsoft usa o segundo IP utilizável para seu roteador.
   * Uma sub-rede /30 para o link secundário. A sub-rede não deve fazer parte de nenhum espaço de endereçamento reservado para redes virtuais. Dessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao seu roteador já que a Microsoft usa o segundo IP utilizável para seu roteador.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN. Para os links Primário e Secundário, você deve usar a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes. Você pode usar um número de AS privado para esse emparelhamento, exceto pelos números de 65515 a 65520, inclusive.
   * Você deve anunciar as rotas do seu roteador de borda locais para o Azure por meio do BGP quando você configura o emparelhamento privado.
   * **Opcional –** Um hash MD5 se você optar por usar um.
3. Selecione a linha emparelhamento privada do Azure, conforme mostrado no exemplo a seguir:

   [![Selecione a linha de emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "selecione a linha de emparelhamento privado")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Configure o emparelhamento privado. **Salvar** a configuração depois de especificar todos os parâmetros.

   ![configurar o emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Depois que a configuração é aceita com êxito, você vê algo semelhante ao exemplo a seguir:

   ![salva o emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="getprivate"></a>Para exibir detalhes sobre o emparelhamento privado do Azure

Você pode exibir as propriedades de emparelhamento privado do Azure selecionando o emparelhamento.

[![Exibir propriedades de emparelhamento privadas](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "exibir propriedades de emparelhamento privadas")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="updateprivate"></a>Para atualizar a configuração de emparelhamento privado do Azure

Você pode selecionar a linha para emparelhamento e modificar as propriedades de emparelhamento. Depois de atualizar, salve suas alterações.

![atualizar emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="deleteprivate"></a>Para excluir um emparelhamento privado do Azure

Você pode remover a configuração de emparelhamento selecionando o ícone Excluir, conforme mostrado na imagem a seguir:

> [!WARNING]
> Você deve garantir que todas as conexões de Alcance Global do ExpressRoute e redes virtuais são removidas antes de executar este exemplo. 
> 
> 

![excluir emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)

## <a name="public"></a>Emparelhamento público do Azure

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento público do Azure para um circuito do ExpressRoute.

> [!Note]
> Emparelhamento público do Azure foi preterido para circuitos de novo. Para obter mais informações, consulte [emparelhamento de ExpressRoute](expressroute-circuit-peerings.md).
>

### <a name="getpublic"></a>Para exibir detalhes sobre o emparelhamento público do Azure

Exiba as propriedades de emparelhamento público do Azure, selecionando o emparelhamento.

### <a name="updatepublic"></a>Para atualizar a configuração de emparelhamento público do Azure

Selecione a linha de emparelhamento e, em seguida, modificar as propriedades de emparelhamento.

### <a name="deletepublic"></a>Para excluir o emparelhamento público do Azure

Remova a configuração de emparelhamento selecionando o ícone Excluir.

## <a name="next-steps"></a>Próximas etapas

A próxima etapa será [Vincular uma Rede Virtual a um circuito de ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Para saber mais sobre fluxos de trabalho do ExpressRoute, confira [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre o emparelhamento de circuito, veja [Circuitos e domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md).
* Para saber mais sobre redes virtuais, confira [Visão geral da rede virtual](../virtual-network/virtual-networks-overview.md).
