---
title: Escalar verticalmente um aplicativo no Azure | Microsoft Docs
description: "Saiba como escalar verticalmente um aplicativo no Serviço de Aplicativo do Azure a fim de adicionar capacidade e recursos."
services: app-service
documentationcenter: 
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
ms.openlocfilehash: 248b96cc97367ca2cb3fd82c9824d43dfee43c0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scale-up-an-app-in-azure"></a>Dimensionar um aplicativo no Azure

> [!NOTE]
> O novo tipo de preço **PremiumV2** fornece CPUs mais rápidas, armazenamento SSD e um índice memória/núcleo duas vezes maior que o encontrado nos tipos de preço existentes. Para expandir para o tipo de preço **PremiumV2**, consulte [Configurar o tipo de preço PremiumV2 para o Serviço de Aplicativo](app-service-configure-premium-tier.md).
>

Este artigo mostra como dimensionar seu aplicativo no Serviço de Aplicativo do Azure. Há dois fluxos de trabalho para dimensionamento, escalar verticalmente e escalar horizontalmente, e este artigo explica o fluxo de trabalho de escala vertical.

* [Escalar verticalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): obtenha mais CPU, memória, espaço em disco e recursos adicionais como VMs (máquinas virtuais) dedicadas, domínios e certificados personalizados, slots de preparação, dimensionamento automático e muito mais. Você escala verticalmente alterando o tipo de preço do plano do Serviço de Aplicativo ao qual seu aplicativo pertence.
* [Escalar horizontalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): aumenta o número de instâncias de VM que executam seu aplicativo.
  Você pode escalar horizontalmente até 20 instâncias, dependendo de seu tipo de preço. [Ambientes do Serviço de Aplicativo](environment/intro.md) no tipo de preço **Isolado** aumentam ainda mais sua contagem de expansão para 100 instâncias. Para saber mais sobre a escala horizontal, consulte [Escalar a contagem de instâncias manualmente ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md). Lá você descobre como usar o dimensionamento automático, que serve para dimensionar automaticamente a contagem de instâncias com base em regras e agendamentos predefinidos.

As configurações de escala terão apenas alguns segundos para serem aplicadas e afetam todos os aplicativos em seu [plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
Elas não exigem a alteração de seu código ou a reimplantação de seu aplicativo.

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
3. Escolha seu tipo e depois clique em **Selecionar**.
   
    A guia **Notificações** vai piscar **SUCESSO** em verde quando a operação for concluída.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Escalar recursos relacionados
Se o seu aplicativo depender de outros serviços, como o Banco de Dados SQL do Azure ou o Armazenamento do Azure, você poderá expandir esses recursos separadamente. Esses recursos não são gerenciados pelo plano do Serviço de Aplicativo.

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

> [!NOTE]
> Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/) , em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido e não há compromissos.
> 
> 

<a name="Next Steps"></a>

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre preços, suporte e SLA, visite os links a seguir:
  
    [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [Planos de suporte do Microsoft Azure](https://azure.microsoft.com/support/plans/)
  
    [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/)
  
    [Detalhes de preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Tamanhos de máquina virtual e de serviço de nuvem do Microsoft Azure][vmsizes]
  
* Para obter informações sobre práticas recomendadas do Serviço de Aplicativo do Azure, incluindo a criação de uma arquitetura escalonável e flexível, consulte [Práticas recomendadas: Aplicativos Web do Serviço de Aplicativo do Azure](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).
* Para assistir a vídeos sobre o dimensionamento de aplicativos do Serviço de Aplicativo, consulte os seguintes recursos:
  
  * [Quando dimensionar Sites do Azure - com Stefan Schackow](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)
  * [Dimensionamento automático de Sites do Azure, CPU ou programado - com Stefan Schackow](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)
  * [Como dimensionar sites do Azure - com Stefan Schackow](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:/pricing/details/app-service/
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
