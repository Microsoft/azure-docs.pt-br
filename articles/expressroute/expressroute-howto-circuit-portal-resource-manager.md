---
title: 'Criar e modificar um circuito do ExpressRoute - portal: Azure | Microsoft Docs'
description: Criar, provisionar, verificar, atualizar, excluir e desprovisionar um circuito do ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 10/20/2018
ms.author: cherylmc
ms.reviewer: ganesr
ms.custom: seodec18
ms.openlocfilehash: 25821f60f47b1279e70cba2574901cd5df3d327f
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846595"
---
# <a name="create-and-modify-an-expressroute-circuit"></a>Criar e modificar um circuito do ExpressRoute

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Modelo do Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Vídeo – Portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este artigo ajuda você a criar um circuito do ExpressRoute usando o portal do Azure e o modelo de implantação do Azure Resource Manager. Você também pode verificar o status, atualizar, excluir ou desprovisionar um circuito.

## <a name="before-you-begin"></a>Antes de começar

* Examine os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Verifique se você tem acesso ao [portal do Azure](https://portal.azure.com).
* Verifique se você tem permissões para criar novos recursos de rede. Se você não tiver as permissões corretas, entre em contato com o administrador da conta.
* Você pode [exibir um vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) antes de começar para entender melhor as etapas.

## <a name="create"></a>Criar e provisionar um circuito do ExpressRoute

### <a name="1-sign-in-to-the-azure-portal"></a>1. Entrar no Portal do Azure

Em um navegador, acesse o [Portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Criar um novo circuito do ExpressRoute

> [!IMPORTANT]
> O circuito do ExpressRoute será cobrado a partir do momento em que uma chave de serviço for emitida. Execute esta operação quando o provedor de conectividade estiver pronto para provisionar o circuito.

1. Você pode criar um circuito do ExpressRoute selecionando a opção de criar um novo recurso. Clique em **Criar um recurso** > **Rede** > **ExpressRoute**, conforme mostrado na imagem a seguir:

   ![Criar um circuito do ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Após clicar em **ExpressRoute**, você verá a página **Criar circuito do ExpressRoute**. Ao preencher os valores nessa página, especifique a camada de SKU (Standard ou Premium) e o modelos de cobrança de medição de dados (Limitado ou Ilimitado) corretos.

   ![Configurar a camada da SKU e a medição de dados](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * **camada** determina se um complemento padrão ou premium do ExpressRoute está habilitado. Você pode especificar **Standard** para obter o SKU padrão ou **Premium** para o complemento premium.
   * **medição de dados** determina o tipo de cobrança. Você pode especificar **Limitado** para um plano de dados limitado e **Ilimitado** para um plano de dados ilimitado. Observe que você pode alterar o tipo de cobrança de **Limitada** para **Ilimitada**.

     > [!IMPORTANT]
     > Não é possível alterar o tipo de **ilimitado** para **limitado**.

   * **Local de Emparelhamento** é o local físico em que você está realizando o emparelhamento com a Microsoft.

     > [!IMPORTANT]
     > O Local de Emparelhamento indica o [local físico](expressroute-locations.md) em que você está realizando o emparelhamento com a Microsoft. Isso **não** tem vínculo à propriedade "Local", que se refere à posição geográfica na qual o Provedor de Recursos de Rede do Azure está localizado. Embora eles não estejam relacionados, é uma boa prática escolher um provedor de recursos de rede geograficamente próximo do Local de Emparelhamento do circuito.

### <a name="3-view-the-circuits-and-properties"></a>3. Exibir os circuitos e as propriedades

**Exibir todos os circuitos**

Você pode exibir todos os circuitos que criou selecionando **Todos os recursos** no menu do lado esquerdo.

![Exibir circuitos](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Exibir as propriedades**

Selecione o circuito para exibir as propriedades dele. Na página **Visão geral** do circuito, a chave de serviço é exibida no campo de chave de serviço. É preciso copiar a chave de serviço do circuito e passá-la para o provedor de serviços para concluir o processo de provisionamento. A chave de serviço do circuito é específica para o circuito.

![Exibir propriedades](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Enviar a chave de serviço ao seu provedor de conectividade para obter provisionamento

Nessa página, **Status do provedor** fornece informações sobre o estado de provisionamento atual no lado do provedor de serviço. **Status de circuito** fornece o estado no lado da Microsoft. Para saber mais sobre estados de provisionamento do circuito, confira o artigo [Fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quando você cria um novo circuito do ExpressRoute, ele está no seguinte estado:

Status do provedor: Não provisionado<BR>
Status do circuito: Enabled

![Iniciar o processo de provisionamento](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

O circuito assumirá o seguinte o estado quando o provedor de conectividade estiver habilitando-o para você:

Status do provedor: Provisionamento<BR>
Status do circuito: Enabled

Para que você consiga usar um circuito do ExpressRoute, ele deverá estar no seguinte estado:

Status do provedor: Provisionado<BR>
Status do circuito: Enabled

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Verifique periodicamente o status e o estado da chave do circuito

Você pode exibir as propriedades do circuito de seu interesse selecionando-o. Verifique o **Status do provedor** e se ele mudou para **Provisionado** antes de continuar.

![Status do circuito e do provedor](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Criar sua configuração de roteamento

Para obter instruções passo a passo, confira o artigo [Configuração do roteamento de circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) para criar e modificar os emparelhamentos de circuito.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas a circuitos criados com provedores de serviço que oferecem serviços de conectividade de camada 2. Se você estiver usando um provedor de serviços que oferece serviços gerenciados de camada 3 (normalmente um IP VPN, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Vincular uma rede virtual a um circuito de ExpressRoute

Em seguida, vincule uma rede virtual a seu circuito do ExpressRoute. Use o artigo [Vincular redes virtuais a circuitos do ExpressRoute](expressroute-howto-linkvnet-arm.md) ao trabalhar com o modelo de implantação do Gerenciador de Recursos.

## <a name="status"></a>Obtendo o status de um circuito do ExpressRoute

Você pode exibir o status de um circuito selecionando-o e mostrando a página Visão geral.

## <a name="modify"></a>Modificar um circuito do ExpressRoute

Você pode modificar certas propriedades de um circuito do ExpressRoute sem afetar a conectividade. É possível modificar a largura de banda, o SKU, o modelo de cobrança e permitir operações clássicas na página **Configurações**. Para saber mais sobre os limites e as limitações, consulte as [Perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md).

É possível executar as seguintes tarefas sem tempo de inatividade:

* Habilitar ou desabilitar um complemento Premium do ExpressRoute para seu circuito do ExpressRoute.
* Aumentar a largura de banda do circuito do ExpressRoute, desde que haja capacidade disponível na porta.

  > [!IMPORTANT]
  > Não há suporte para o downgrade da largura de banda de um circuito.

* Alterar o plano de medição de *Dados Limitados* para *Dados Ilimitados*.

  > [!IMPORTANT]
  > Não há suporte para alteração do plano de medição de Dados Ilimitados para Dados Limitados.

* Você pode habilitar e desabilitar *Permitir Operações Clássicas*.
  > [!IMPORTANT]
  > Talvez seja necessário recriar o circuito de ExpressRoute se não houver capacidade adequada na porta existente. Você não pode atualizar o circuito não se houver capacidade adicional disponível nesse local.
  >
  > Embora seja possível atualizar a largura de banda sem problemas, não é possível reduzir a largura de banda de um circuito ExpressRoute sem interrupção. O downgrade da largura de banda exige o desprovisionamento do circuito do ExpressRoute e um reprovisionamento de um novo circuito do ExpressRoute.
  >
  > Poderá ocorrer uma falha ao desabilitar a operação do complemento Premium se você estiver usando recursos que ultrapassam o que é permitido para o circuito standard.

Para modificar um circuito do ExpressRoute, clique em **Configuração**.

![Modificar o circuito](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)

## <a name="delete"></a>Desprovisionamento e exclusão de um circuito do ExpressRoute

Você pode excluir seu circuito do ExpressRoute selecionando o ícone **Excluir** . Observe as seguintes informações:

* Você deve desvincular todas as redes virtuais do circuito do ExpressRoute. Se essa operação falhar, verifique se há redes virtuais vinculadas ao circuito.
* Se o estado de provisionamento do provedor de serviço de circuito de ExpressRoute for **Provisionando** ou **Provisionado**, você deverá trabalhar com seu provedor de serviços para que ele desprovisione o circuito. Continuaremos a reservar recursos e a cobrar de você até que o provedor de serviços complete o desprovisionamento do circuito e nos notifique.
* Se o provedor de serviços tiver desprovisionado o circuito (o estado de provisionamento do provedor de serviços estiver definido como **Não provisionado**), exclua o circuito. Isso interrompe a cobrança pelo circuito.

## <a name="next-steps"></a>Próximas etapas

Depois de criar o circuito, continue para as próximas etapas:

* [Criar e modificar o roteamento do circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Vincular a rede virtual ao circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
