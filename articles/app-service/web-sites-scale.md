---
title: Escalar verticalmente os recursos e as capacidades – Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como escalar verticalmente um aplicativo no Serviço de Aplicativo do Azure a fim de adicionar capacidade e recursos.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 98d3d1f6fc0f2f30196f360811808579dfbab312
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832062"
---
# <a name="scale-up-an-app-in-azure"></a>Dimensionar um aplicativo no Azure

> [!NOTE]
> A nova camada **PremiumV2** fornece CPUs mais rápidas, armazenamento SSD e um índice memória/núcleo duas vezes maior que o encontrado nas camadas existentes. Com a vantagem de ter um melhor desempenho, você pode economizar dinheiro executando seus aplicativos em menos instâncias. Para expandir para o tipo de preço **PremiumV2**, consulte [Configurar o tipo de preço PremiumV2 para o Serviço de Aplicativo](app-service-configure-premium-tier.md).
>

Este artigo mostra como dimensionar seu aplicativo no Serviço de Aplicativo do Azure. Há dois fluxos de trabalho para dimensionamento, escalar verticalmente e escalar horizontalmente, e este artigo explica o fluxo de trabalho de escala vertical.

* [Escalar verticalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): obtenha mais CPU, memória, espaço em disco e recursos extras, como VMs (máquinas de virtuais) dedicadas, domínios personalizados e certificados, slots de preparo, dimensionamento automático e muito mais. Você escala verticalmente alterando o tipo de preço do plano do Serviço de Aplicativo ao qual seu aplicativo pertence.
* [Escalar horizontalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): aumenta o número de instâncias de VM que executam seu aplicativo.
  Você pode escalar horizontalmente até 20 instâncias, dependendo de seu tipo de preço. [Ambientes do Serviço de Aplicativo](environment/intro.md) no tipo de preço **Isolado** aumentam ainda mais sua contagem de expansão para 100 instâncias. Para saber mais sobre a escala horizontal, consulte [Escalar a contagem de instâncias manualmente ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md). Lá, você descobre como usar o dimensionamento automático, que serve para dimensionar automaticamente a contagem de instâncias com base em regras e agendamentos predefinidos.

As configurações de escala terão apenas alguns segundos para serem aplicadas e afetam todos os aplicativos em seu [plano do Serviço de Aplicativo](../app-service/overview-hosting-plans.md).
Elas não exigem a alteração do código ou a reimplantação do aplicativo.

Para obter informações sobre os preços e recursos de planos individuais do Serviço de Aplicativo, consulte [Detalhes de Preços dos Serviços de Aplicativo](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Antes de mudar um Plano do Serviço de Aplicativo do tipo **Gratuito** , é necessário remover os [limites de gastos](https://azure.microsoft.com/pricing/spending-limits/) em vigor para sua Assinatura do Azure. Para exibir ou alterar as opções da sua assinatura do Serviço de Aplicativo do Microsoft Azure, consulte [Assinaturas do Microsoft Azure][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Escale verticalmente seu tipo de preço
1. No seu navegador, abra o [Portal do Azure][portal].
2. Na página do aplicativo do Serviço de Aplicativo, clique em **Todas as configurações** e em **Expandir**.
   
    ![Navegue para escalar verticalmente seu aplicativo do Azure.][ChooseWHP]
3. Escolha sua camada e, em seguida, clique em **aplicar**.
   
    A guia **Notificações** vai piscar **SUCESSO** em verde quando a operação for concluída.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Escalar recursos relacionados
Se o seu aplicativo depender de outros serviços, como o Banco de Dados SQL do Azure ou o Armazenamento do Azure, você poderá expandir esses recursos separadamente. Esses recursos não são gerenciados pelo Plano do Serviço de Aplicativo.

1. Em **Essentials**, clique no link **Grupo de recursos**.
   
    ![Escale verticalmente os recursos relacionados de seu aplicativo do Azure](./media/web-sites-scale/RGEssentialsLink.png)
2. Na parte **Resumo** da página **Grupo de recursos**, clique em um recurso que você quer escalonar. A captura de tela abaixo mostra um recurso do Banco de Dados SQL e um recurso do Armazenamento do Azure.
   
    ![Navegue até a página do grupo de recursos para expandir seu aplicativo do Azure](./media/web-sites-scale/ResourceGroup.png)
3. Para um recurso do Banco de Dados SQL, clique em **Configurações** > **Tipo de preço** para dimensionar o tipo de preço.
   
    ![Escalar verticalmente o back-end do Banco de Dados SQL para seu aplicativo do Azure](./media/web-sites-scale/ScaleDatabase.png)
   
    Você também pode ativar a [replicação geográfica](../sql-database/sql-database-geo-replication-overview.md) de sua instância do Banco de Dados SQL.
   
    Para um recurso de Armazenamento do Azure, clique em **Configurações** > **Configuração** para escalar verticalmente suas opções de armazenamento.
   
    ![Escalar verticalmente a conta do Armazenamento do Azure usada por seu aplicativo do Azure](./media/web-sites-scale/ScaleStorage.png)

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Comparar tipos de preço
Para obter informações detalhadas, tais como os tamanhos de VM para cada tipo de preço, consulte [Detalhes de preços do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/web-sites/).
Para uma tabela de limites de serviço, cotas, restrições e recursos com suporte em cada tipo de preço, consulte [Limites do Serviço de Aplicativo](../azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre preços, suporte e SLA, visite os links a seguir:
  
    [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [Planos de suporte do Microsoft Azure](https://azure.microsoft.com/support/plans/)
  
    [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/)
  
    [Detalhes de preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Tamanhos de máquina virtual e de serviço de nuvem do Microsoft Azure][vmsizes]
  
* Para obter informações sobre as práticas recomendadas do Serviço de Aplicativo do Azure, incluindo a criação de uma arquitetura escalonável e resiliente, confira [Práticas recomendadas: aplicativos Web do Serviço de Aplicativo do Azure](https://azure.microsoft.com/blog/best-practices-windows-azure-websites-waws/).
* Para assistir a vídeos sobre o dimensionamento de aplicativos do Serviço de Aplicativo, consulte os seguintes recursos:
  
  * [Quando dimensionar Sites do Azure - com Stefan Schackow](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)
  * [Dimensionamento automático de Sites do Azure, CPU ou programado - com Stefan Schackow](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)
  * [Como dimensionar sites do Azure - com Stefan Schackow](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)

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
