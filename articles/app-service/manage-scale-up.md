---
title: Escalar verticalmente recursos e capacidades
description: Saiba como escalar verticalmente um aplicativo no Serviço de Aplicativo do Azure. Obtenha mais CPU, memória, espaço em disco e recursos extras.
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.topic: article
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: eb0ce5cfadbab247299cd3c22bf6ee618bf19731
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582490"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Escalar verticalmente um aplicativo no Serviço de Aplicativo do Azure

Este artigo mostra como dimensionar seu aplicativo no Serviço de Aplicativo do Azure. Há dois fluxos de trabalho para dimensionamento, escalar verticalmente e escalar horizontalmente, e este artigo explica o fluxo de trabalho de escala vertical.

* [Escalar verticalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): obtenha mais CPU, memória, espaço em disco e recursos extras, como VMs (máquinas de virtuais) dedicadas, domínios personalizados e certificados, slots de preparo, dimensionamento automático e muito mais. Você escala verticalmente alterando o tipo de preço do plano do Serviço de Aplicativo ao qual seu aplicativo pertence.
* [Escalar horizontalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): aumenta o número de instâncias de VM que executam seu aplicativo.
  Você pode escalar horizontalmente até 30 instâncias, dependendo do tipo de preço. [Ambientes do Serviço de Aplicativo](environment/intro.md) no tipo de preço **Isolado** aumentam ainda mais sua contagem de expansão para 100 instâncias. Para saber mais sobre a escala horizontal, consulte [Escalar a contagem de instâncias manualmente ou automaticamente](../azure-monitor/autoscale/autoscale-get-started.md). Lá, você descobre como usar o dimensionamento automático, que serve para dimensionar automaticamente a contagem de instâncias com base em regras e agendamentos predefinidos.

As configurações de escala terão apenas alguns segundos para serem aplicadas e afetam todos os aplicativos em seu [plano do Serviço de Aplicativo](../app-service/overview-hosting-plans.md).
Elas não exigem a alteração do código ou a reimplantação do aplicativo.

Para obter informações sobre os preços e recursos de planos individuais do Serviço de Aplicativo, consulte [Detalhes de Preços dos Serviços de Aplicativo](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Antes de mudar um Plano do Serviço de Aplicativo do tipo **Gratuito** , é necessário remover os [limites de gastos](https://azure.microsoft.com/pricing/spending-limits/) em vigor para sua Assinatura do Azure. Para ver ou alterar as opções da sua assinatura do Serviço de Aplicativo do Microsoft Azure, confira [Assinaturas do Microsoft Azure][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Escale verticalmente seu tipo de preço

> [!NOTE]
> Para escalar verticalmente para a camada **PremiumV3** , consulte [Configurar a camada PremiumV3 para o serviço de aplicativo](app-service-configure-premium-tier.md).
>

1. No seu navegador, abra o [portal do Azure][portal].

1. Na página do aplicativo do Serviço de Aplicativo, selecione **Escalar Verticalmente (Plano do Serviço de Aplicativo)** .
   
3. Escolha o nível e selecione **Aplicar**. Escolha as diferentes categorias (por exemplo, **Produção**) e também **Ver opções adicionais** para mostrar mais camadas.
   
    ![Navegue para escalar verticalmente seu aplicativo do Azure.][ChooseWHP]

    Quando a operação for concluída, você verá um pop-up de notificação com uma marca de seleção verde de êxito.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Escalar recursos relacionados
Se o seu aplicativo depender de outros serviços, como o Banco de Dados SQL do Azure ou o Armazenamento do Azure, você poderá expandir esses recursos separadamente. Esses recursos não são gerenciados pelo Plano do Serviço de Aplicativo.

1. Na página **Visão geral** do aplicativo, selecione o link **Grupo de recursos**.
   
    ![Escale verticalmente os recursos relacionados de seu aplicativo do Azure](./media/web-sites-scale/RGEssentialsLink.png)

2. Na parte **Resumo** da página **Grupo de recursos**, selecione um recurso que deseja escalar. A captura de tela a seguir mostra um recurso do Banco de Dados SQL.
   
    ![Navegue até a página do grupo de recursos para expandir seu aplicativo do Azure](./media/web-sites-scale/ResourceGroup.png)

    Para escalar verticalmente o recurso relacionado, confira a documentação do tipo de recurso específico. Por exemplo, para escalar verticalmente um Banco de Dados SQL individual, confira [Escalar recursos de banco de dados individual no Banco de Dados SQL do Azure](../azure-sql/database/single-database-scale.md). Para escalar verticalmente um recurso do Banco de Dados do Azure para MySQL, confira [Escalar recursos do MySQL](../mysql/concepts-pricing-tiers.md#scale-resources).

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Comparar tipos de preço

Para obter informações detalhadas, tais como os tamanhos de VM para cada tipo de preço, consulte [Detalhes de preços do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service).

Para uma tabela de limites de serviço, cotas, restrições e recursos com suporte em cada tipo de preço, consulte [Limites do Serviço de Aplicativo](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="more-resources"></a>Mais recursos

[Escalonar a contagem de instâncias manualmente ou automaticamente](../azure-monitor/autoscale/autoscale-get-started.md)  
[Configurar camada de PremiumV3 para o serviço de aplicativo](app-service-configure-premium-tier.md)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png