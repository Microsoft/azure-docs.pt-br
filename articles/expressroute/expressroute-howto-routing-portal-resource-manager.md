---
title: 'Tutorial: Configurar o emparelhamento para o circuito do ExpressRoute – portal do Azure'
description: Este tutorial mostra como criar e provisionar o emparelhamento da Microsoft e privado do ExpressRoute usando o portal do Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 01/11/2021
ms.author: duau
ms.openlocfilehash: c4f76a02a88b91c082106566ca236abe25d4ed88
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567341"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-the-azure-portal"></a>Tutorial: Criar e modificar o emparelhamento de um circuito do ExpressRoute usando o portal do Azure

Este tutorial mostra como criar e gerenciar a configuração de roteamento para um circuito do ExpressRoute do Azure Resource Manager usando o portal do Azure. Você também pode verificar o status, atualizar ou excluir e desprovisionar emparelhamentos de um circuito do ExpressRoute. Se quiser usar um método diferente para trabalhar com seu circuito, selecione um artigo na lista a seguir:

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Emparelhamento público](about-public-peering.md)
> * [Vídeo – Emparelhamento privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo – Emparelhamento da Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

Você pode configurar o emparelhamento privado e o emparelhamento da Microsoft para um circuito do ExpressRoute (o emparelhamento público do Azure está preterido para novos circuitos). Os emparelhamentos poderão ser configurados em qualquer ordem que você escolher. No entanto, você deve concluir a configuração de um emparelhamento por vez. Para obter mais informações sobre o roteamento de domínios e emparelhamentos, consulte [Domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md). Para obter informações sobre o emparelhamento público, confira [Emparelhamento público do ExpressRoute](about-public-peering.md).

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> - Configurar, atualizar e excluir o emparelhamento da Microsoft para um circuito
> - Configurar, atualizar e excluir o emparelhamento privado do Azure para um circuito

## <a name="prerequisites"></a>Pré-requisitos

* Verifique se você analisou as seguintes páginas antes de começar a configuração:
    * [Pré-requisitos](expressroute-prerequisites.md) 
    * [Requisitos de roteamento](expressroute-routing.md)
    * [Fluxos de trabalho](expressroute-workflows.md)
* Você deve ter um circuito do ExpressRoute ativo. Antes de continuar, siga as instruções para [Criar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e para que o circuito seja habilitado pelo provedor de conectividade. Para configurar emparelhamentos, o circuito do ExpressRoute precisa estar em um estado habilitado e provisionado. 
* Se planejar usar um hash de chave compartilhada/MD5, use a chave em ambos os lados do túnel. O limite é 25 caracteres alfanuméricos, no máximo. Não há suporte para caracteres especiais. 

Estas instruções se aplicam apenas a circuitos criados com provedores de serviço que oferecem serviços de conectividade de Camada 2. Se você estiver usando um provedor de serviços que ofereça serviços gerenciados de Camada 3 (normalmente um IPVPN, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você. 

> [!IMPORTANT]
> Atualmente, não anunciamos emparelhamentos configurados pelos provedores de serviço por meio do portal de gerenciamento de serviço. Estamos trabalhando para habilitar esse recurso em breve. Verifique com seu provedor de serviços antes de configurar os emparelhamentos via protocolo BGP.
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Emparelhamento da Microsoft

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento da Microsoft para um circuito do ExpressRoute.

> [!IMPORTANT]
> O emparelhamento da Microsoft de circuitos do ExpressRoute configurados antes de 1º de agosto de 2017 terá todos os prefixos de serviço anunciados através do emparelhamento da Microsoft, mesmo que os filtros de rota não estejam definidos. O emparelhamento da Microsoft de circuitos de ExpressRoute configurados em ou após 1º de agosto de 2017 não terá nenhum prefixo anunciado até que um filtro de rota seja anexado ao circuito. Para obter mais informações, consulte [Configurar um filtro de rota para o emparelhamento da Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Criar emparelhamento da Microsoft

1. Configure o circuito do ExpressRoute. Confira o **Status do provedor** para verificar se o circuito foi totalmente provisionado pelo provedor de conectividade antes de continuar.

   Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, você pode solicitar a ele a habilitação do emparelhamento da Microsoft. Não será necessário seguir as instruções listadas nas próximas seções. No entanto, se o seu provedor de conectividade não gerenciar o roteamento para você, após a criação do circuito, prossiga com as etapas abaixo.

   **Circuito – Status provedor: Não provisionado**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned.png" alt-text="Captura de tela que mostra a página Visão Geral do Circuito de Demonstração do ExpressRoute com uma caixa vermelha realçando o Status do provedor definido como Não provisionado":::

   **Circuito – Status provedor: Provisionado**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned.png" alt-text="Captura de tela que mostra a página Visão Geral do Circuito de Demonstração do ExpressRoute com uma caixa vermelha realçando o Status do provedor definido como Provisionado":::

2. Configurar o emparelhamento da Microsoft para o circuito. Verifique se você tem as informações a seguir antes de continuar.

   * Um par de sub-redes pertencentes a você e registradas em um RIR/IRR. Uma sub-rede será usada para o link principal enquanto a outra será usada para o link secundário. De cada uma dessas sub-redes, você atribuirá o primeiro endereço IP utilizável ao seu roteador, já que a Microsoft usa o segundo IP utilizável para o roteador dela. Você tem três opções para este par de sub-redes:
       * IPv4: duas sub-redes /30. Devem ser prefixos de IPv4 públicos válidos.
       * IPv6: duas sub-redes /126. Devem ser prefixos de IPv6 públicos válidos.
       * Ambas: duas sub-redes /30 e duas sub-redes /126.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN. Para os links Primário e Secundário, você deve usar a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes.
   * Prefixos anunciados: forneça uma lista com todos os prefixos que planeja anunciar na sessão do BGP. Somente prefixos de endereços IP públicos são aceitos. Caso você planeje enviar um conjunto de prefixos, poderá enviar uma lista separada por vírgulas. Esses prefixos devem ser registrados em seu nome em um RIR/IRR.
   * **Opcional –** ASN de cliente: se você estiver anunciando prefixos não registrados com o número AS de emparelhamento, especifique o número AS com o qual eles estão registrados.
   * Nome do registro de roteamento: você pode especificar o RIR/IRR com base no qual o número de AS e os prefixos estão registrados.
   * **Opcional –** Um hash MD5 se você optar por usar um.
1. Você pode selecionar o emparelhamento que deseja configurar, conforme mostrado no exemplo a seguir. Selecione a linha de emparelhamento da Microsoft.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-microsoft-peering.png" alt-text="Selecionar a linha de emparelhamento da Microsoft":::

4. Configure o emparelhamento da Microsoft. **Salve** a configuração depois que você tiver especificado todos os parâmetros. A seguinte imagem mostra um exemplo de configuração:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m-validation-needed.png" alt-text="Configurar a validação de emparelhamento da Microsoft necessária":::

> [!IMPORTANT]
> A Microsoft verifica se os "Prefixos públicos publicados" e o "ASN de ponto" (ou "cliente ASN de cliente") especificados estão atribuídos a você no registro de roteamento da Internet. Se você estiver recebendo os prefixos públicos de outra entidade e se a atribuição não for registrada com o registro de roteamento, a validação automática não será concluída e exigirá validação manual. Se a validação automática falhar, você verá a mensagem "Requer validação". 
>
> Se você vir a mensagem "Requer validação", colete os documentos que mostram que os prefixos públicos foram atribuídos à organização pela entidade que está listada como proprietária dos prefixos no registro de roteamento e envie esses documentos para validação manual, abrindo um tíquete de suporte. 
>

   Se o circuito for para um estado "Validação necessária", você precisará abrir um tíquete de suporte para mostrar a prova de propriedade dos prefixos à nossa equipe de suporte. Você pode abrir um tíquete de suporte diretamente no portal, conforme mostrado no exemplo a seguir:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png" alt-text="Validação necessária – tíquete de suporte":::

5. Depois que a configuração for aceita com êxito, você verá algo semelhante à seguinte imagem:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/microsoft-peering-validation-configured.png" alt-text="Status de emparelhamento: configurado":::

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Para exibir detalhes de emparelhamento da Microsoft

Você pode ver as propriedades do emparelhamento da Microsoft selecionando a linha para o emparelhamento.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="Ver as propriedades de emparelhamento da Microsoft":::

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Atualizar a configuração de emparelhamento da Microsoft

Você pode selecionar a linha para o emparelhamento que deseja modificar e, depois, modificar as propriedades de emparelhamento e salvar suas modificações.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png" alt-text="Selecionar linha de emparelhamento":::

## <a name="azure-private-peering"></a><a name="private"></a>Emparelhamento privado do Azure

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento privado do Azure para um circuito do ExpressRoute.

> [!IMPORTANT]
> A compatibilidade com IPv6 para emparelhamento privado está atualmente em **Versão Prévia Pública**. Se você quiser conectar sua rede virtual a um circuito do ExpressRoute com o emparelhamento privado configurado baseado em IPv6, verifique se a rede virtual é de pilha dupla e se segue as diretrizes descritas [aqui](../virtual-network/ipv6-overview.md).
> 
> 

### <a name="to-create-azure-private-peering"></a>Criar um emparelhamento privado do Azure

1. Configure o circuito do ExpressRoute. Verifique se o circuito foi totalmente provisionado pelo provedor de conectividade antes de continuar. 

   Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, você pode solicitar a ele a habilitação do emparelhamento privado do Azure. Não será necessário seguir as instruções listadas nas próximas seções. No entanto, se o seu provedor de conectividade não gerenciar o roteamento para você, após a criação do circuito, prossiga com as etapas a seguir.

   **Circuito – Status provedor: Não provisionado**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-private.png" alt-text="Captura de tela que mostra a página Visão Geral do Circuito de Demonstração do ExpressRoute com uma caixa vermelha realçando o Status do provedor definido como Não provisionado":::

   **Circuito – Status provedor: Provisionado**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned-private-peering.png" alt-text="Captura de tela que mostra a página Visão Geral do Circuito de Demonstração do ExpressRoute com uma caixa vermelha realçando o Status do provedor definido como Provisionado":::

2. Configure o emparelhamento privado do Azure para o circuito. Verifique se você tem os seguintes itens antes de prosseguir para as próximas etapas:

   * Um par de sub-redes que não fazem parte de nenhum espaço de endereço reservado para redes virtuais. Uma sub-rede será usada para o link principal enquanto a outra será usada para o link secundário. De cada uma dessas sub-redes, você atribuirá o primeiro endereço IP utilizável ao seu roteador, já que a Microsoft usa o segundo IP utilizável para o roteador dela. Você tem três opções para este par de sub-redes:
       * IPv4: duas sub-redes /30.
       * IPv6: duas sub-redes /126.
       * Ambas: duas sub-redes /30 e duas sub-redes /126.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN. Para os links Primário e Secundário, você deve usar a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes. Você pode usar um número de AS privado para esse emparelhamento, exceto pelos números de 65515 a 65520, inclusive.
   * Você precisa anunciar as rotas do seu roteador do Edge local no Azure por meio de BGP ao configurar o emparelhamento privado.
   * **Opcional –** Um hash MD5 se você optar por usar um.
3. Selecione a linha de emparelhamento privado do Azure, conforme mostrado no seguinte exemplo:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-private-peering.png" alt-text="Selecionar a linha de emparelhamento privado":::

4. Configure o emparelhamento privado. **Salve** a configuração depois que você tiver especificado todos os parâmetros.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/private-peering-configuration.png" alt-text="Configurar o emparelhamento privado":::

5. Depois que a configuração é aceita com êxito, você vê algo semelhante ao exemplo a seguir:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/save-private-peering.png" alt-text="Emparelhamento privado salvo":::

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Para exibir detalhes sobre o emparelhamento privado do Azure

Você pode exibir as propriedades de emparelhamento privado do Azure selecionando o emparelhamento.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="Ver propriedades do emparelhamento privado":::

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Atualizar a configuração de emparelhamento privado do Azure

Você pode selecionar a linha para emparelhamento e modificar as propriedades de emparelhamento. Após a atualização, salve as alterações.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/update-private-peering.png" alt-text="Atualizar o emparelhamento privado":::

## <a name="clean-up-resources"></a>Limpar recursos

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Excluir emparelhamento da Microsoft

Você pode remover a configuração de emparelhamento da Microsoft clicando com o botão direito do mouse no emparelhamento e selecionando **Excluir**, conforme mostrado na seguinte imagem:

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-microsoft-peering.png" alt-text="Excluir o emparelhamento da Microsoft":::

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Excluir um emparelhamento privado do Azure

Você pode remover a configuração de emparelhamento privado clicando com o botão direito do mouse no emparelhamento e selecionando **Excluir**, conforme mostrado na seguinte imagem:

> [!WARNING]
> Você deve verificar se todas as redes virtuais e as conexões de Alcance Global do ExpressRoute foram removidas antes de executar esta operação. 
> 

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-private-peering.png" alt-text="Excluir o emparelhamento privado":::

## <a name="next-steps"></a>Próximas etapas

Após configurar o emparelhamento privado do Azure, você poderá criar um gateway do ExpressRoute para vincular uma rede virtual ao circuito. 

> [!div class="nextstepaction"]
> [Configurar um gateway de rede virtual para ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)