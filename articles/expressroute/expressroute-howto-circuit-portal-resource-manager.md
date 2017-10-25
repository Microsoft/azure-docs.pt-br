---
title: 'Criar e modificar um circuito ExpressRoute: portal do Azure | Microsoft Docs'
description: Este artigo descreve como criar, provisionar, verificar, atualizar, excluir e desprovisionar um circuito do ExpressRoute.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 68d59d59-ed4d-482f-9cbc-534ebb090613
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/07/2017
ms.author: cherylmc;ganesr
ms.openlocfilehash: e3721cd3c031622788f553e71a6555b844f3f7dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-modify-an-expressroute-circuit"></a>Criar e modificar um circuito do ExpressRoute
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Vídeo – Portal do Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este artigo descreve como criar um circuito da Azure ExpressRoute usando o portal do Azure e o modelo de implantação do Azure Resource Manager. As etapas a seguir também mostrarão a você como verificar o status do circuito, como atualizá-lo ou como excluí-lo e desprovisioná-lo.


## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Verifique se você tem acesso ao [portal do Azure](https://portal.azure.com).
* Verifique se você tem permissões para criar novos recursos de rede. Se você não tiver as permissões corretas, entre em contato com o administrador da conta.
* Você pode [exibir um vídeo](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) antes de começar para entender melhor as etapas.

## <a name="create-and-provision-an-expressroute-circuit"></a>Criar e provisionar um circuito do ExpressRoute
### <a name="1-sign-in-to-the-azure-portal"></a>1. Entrar no Portal do Azure
Em um navegador, acesse o [Portal do Azure](http://portal.azure.com) e entre com sua conta do Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Criar um novo circuito do ExpressRoute
> [!IMPORTANT]
> O circuito do ExpressRoute será cobrado a partir do momento em que uma chave de serviço for emitida. Execute esta operação quando o provedor de conectividade estiver pronto para provisionar o circuito.
> 
> 

1. Você pode criar um circuito do ExpressRoute selecionando a opção de criar um novo recurso. Clique em **Novo** > **Rede** > **ExpressRoute**, conforme mostrado na seguinte imagem:
   
    ![Criar um circuito do ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Após clicar em **ExpressRoute**, você verá a folha **Criar circuito de ExpressRoute**. Ao preencher os valores nessa folha, especifique a camada de SKU e os dados de medição corretos.
   
   * **camada** determina se um complemento padrão ou premium do ExpressRoute está habilitado. Você pode especificar **Standard** para obter o SKU padrão ou **Premium** para o complemento premium.
   * **medição de dados** determina o tipo de cobrança. Você pode especificar **Limitado** para um plano de dados limitado e **Ilimitado** para um plano de dados ilimitado. Observe que você pode alterar o tipo de cobrança de **Limitado** para **Ilimitado**, mas não pode alterar o tipo de **Ilimitado** para **Limitado**.
     
     ![Configurar a camada da SKU e a medição de dados](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit2.png)

> [!IMPORTANT]
> Esteja ciente de que o Local de Emparelhamento indica o [local físico](expressroute-locations.md) onde você está emparelhamento com a Microsoft. Isso **não** tem vínculo à propriedade "Local", que se refere à posição geográfica na qual o Provedor de Recursos de Rede do Azure está localizado. Embora eles não estejam relacionados, é uma boa prática escolher um provedor de recursos de rede geograficamente próximo do Local de Emparelhamento do circuito. 
> 
> 

### <a name="3-view-the-circuits-and-properties"></a>3. Exibir os circuitos e as propriedades
**Exibir todos os circuitos**

Você pode exibir todos os circuitos que criou selecionando **Todos os recursos** no menu do lado esquerdo.

![Exibir circuitos](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Exibir as propriedades**

    You can view the properties of the circuit by selecting it. On this blade, note the service key for the circuit. You must copy the circuit key for your circuit and pass it down to the service provider to complete the provisioning process. The circuit key is specific to your circuit.

![Exibir propriedades](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Enviar a chave de serviço ao seu provedor de conectividade para obter provisionamento
Nessa folha, **Status do provedor** fornece informações sobre o estado de provisionamento atual no lado do provedor de serviço. **Status de circuito** fornece o estado no lado da Microsoft. Para saber mais sobre estados de provisionamento do circuito, confira o artigo [Fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quando você criar um novo circuito do ExpressRoute, ele estará no seguinte estado:

Status do provedor: não provisionado<BR>
Status do circuito: habilitado

![Iniciar o processo de provisionamento](./media/expressroute-howto-circuit-portal-resource-manager/viewstatus.png)

O circuito assumirá o estado a seguir quando o provedor de conectividade estiver no processo de habilitá-lo para você:

Status do provedor: provisionando<BR>
Status do circuito: habilitado

Para que você consiga usar um circuito do ExpressRoute, ele deverá estar no seguinte estado:

Status do provedor: provisionado<BR>
Status do circuito: habilitado

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Verifique periodicamente o status e o estado da chave do circuito
Você pode exibir as propriedades do circuito de seu interesse selecionando-o. Verifique o **Status do provedor** e se ele mudou para **Provisionado** antes de continuar.

![Status do circuito e do provedor](./media/expressroute-howto-circuit-portal-resource-manager/viewstatusprovisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Criar sua configuração de roteamento
Para obter instruções passo a passo, confira o artigo [Configuração do roteamento de circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) para criar e modificar os emparelhamentos de circuito.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas a circuitos criados com provedores de serviço que oferecem serviços de conectividade de camada 2. Se você estiver usando um provedor de serviços que oferece serviços gerenciados de camada 3 (normalmente um IP VPN, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você.
> 
> 

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Vincular uma rede virtual a um circuito de ExpressRoute
Em seguida, vincule uma rede virtual a seu circuito do ExpressRoute. Use o artigo [Vincular redes virtuais a circuitos do ExpressRoute](expressroute-howto-linkvnet-arm.md) ao trabalhar com o modelo de implantação do Gerenciador de Recursos.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obtendo o status de um circuito do ExpressRoute
Você pode exibir o status de um circuito selecionando-o. 

![Status de um circuito do ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)

## <a name="modifying-an-expressroute-circuit"></a>Modificando um circuito do ExpressRoute
Você pode modificar certas propriedades de um circuito do ExpressRoute sem afetar a conectividade.

Você pode fazer o seguinte sem tempo de inatividade:

* Como habilitar ou desabilitar o complemento premium do ExpressRoute para seu circuito do ExpressRoute.
* Aumente a largura de banda do circuito de ExpressRoute, desde que haja capacidade disponível na porta. Observe que não há suporte para o downgrade da largura de banda de um circuito. 
* Altere o plano de medição de Dados Limitados para Dados Ilimitados. Observe que a alteração do plano de medição de Dados Ilimitados para Dados Limitados não tem suporte.
* Você pode habilitar e desabilitar *Permitir Operações Clássicas*.

Para saber mais sobre limites e limitações, confira as [Perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md).

Para modificar um circuito do ExpressRoute, clique na **Configuração**, conforme mostrado na figura abaixo.

![Modificar o circuito](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)

É possível modificar a largura de banda, o SKU, o modelo de cobrança e permitir operações clássicas na folha de configuração.

> [!IMPORTANT]
> Talvez seja necessário recriar o circuito de ExpressRoute se não houver capacidade adequada na porta existente. Você não pode atualizar o circuito não se houver capacidade adicional disponível nesse local.
>
> Não é possível reduzir a largura de banda de um circuito do ExpressRoute sem interrupções. O downgrade da largura de banda exige o desprovisionamento do circuito do ExpressRoute e um reprovisionamento de um novo circuito do ExpressRoute.
> 
> A desabilitação da operação de complemento premium poderá falhar se você estiver usando recursos que ultrapassam o que é permitido para o circuito padrão.
> 
> 

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Desprovisionamento e exclusão de um circuito do ExpressRoute
Você pode excluir seu circuito do ExpressRoute selecionando o ícone **Excluir** . Observe o seguinte:

* Você deve desvincular todas as redes virtuais do circuito do ExpressRoute. Se essa operação falhar, verifique se há redes virtuais vinculadas ao circuito.
* Se o estado de provisionamento do provedor de serviço de circuito de ExpressRoute for **Provisionando** ou **Provisionado**, você deverá trabalhar com seu provedor de serviços para que ele desprovisione o circuito. Continuaremos a reservar recursos e a cobrar de você até que o provedor de serviços complete o desprovisionamento do circuito e nos notifique.
* Se o provedor de serviços tiver desprovisionado o circuito (o estado de provisionamento do provedor de serviços estiver definido como **Não provisionado**), exclua o circuito. Isso interromperá a cobrança do circuito

## <a name="next-steps"></a>Próximas etapas
Depois de criar seu circuito, faça o seguinte:

* [Criar e modificar o roteamento do circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Vincular a rede virtual ao circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)

