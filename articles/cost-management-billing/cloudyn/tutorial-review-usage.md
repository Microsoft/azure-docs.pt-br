---
title: 'Tutorial: examinar o uso e os custos com a Cloudyn no Azure'
description: Neste tutorial, você analisa o uso e os custos para acompanhar tendências, detectar ineficiências e criar alertas.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.custom: seodec18
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: afdcd458dbda282a23b7b8f7f1cd8459bc8ec5c5
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131268"
---
<!-- Intent: As a cloud-consuming user, I need to view usage and costs for my cloud resources and services.
-->

# <a name="tutorial-review-usage-and-costs"></a>Tutorial: Examinar o uso e os custos

O Cloudyn mostra o uso e custos para que você possa acompanhar tendências, detectam ineficiências e criar alertas. Todos os dados de uso e de custo são exibidos em relatórios e painéis da Cloudyn. Os exemplos neste tutorial demonstram como examinar o uso e os custos usando painéis e relatórios.

O Gerenciamento de Custos do Azure oferece uma funcionalidade semelhante ao Cloudyn. O Gerenciamento de Custos do Azure é uma solução de gerenciamento de custos nativa do Azure. Ele ajuda a analisar custos, criar e gerenciar orçamentos, exportar dados e examinar e agir de acordo com as recomendações de otimização que visam economizar dinheiro. Para obter mais informações, consulte [gerenciamento de custos do Azure](../cost-management-billing-overview.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Acompanhar as tendências de uso e custo
> * Detectar ineficiências de uso
> * Criar alertas para gastos incomuns ou excesso de gastos
> * Exportar dados

Se você não tem uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter uma conta do Azure.
- Você precisa ter um registro de avaliação ou uma assinatura paga do Cloudyn.

## <a name="open-the-cloudyn-portal"></a>Abra o portal da Cloudyn

O uso e os custos são examinados no portal da Cloudyn. Abra o portal da Cloudyn por meio do portal do Azure ou navegue até https://azure.cloudyn.com e entre.

## <a name="track-usage-and-cost-trends"></a>Acompanhar as tendências de uso e custo

Acompanhe a quantia de dinheiro real gasta com uso e custos usando relatórios Ao Longo do Tempo para identificar tendências. Para começar a verificar tendências, use o relatório de Custo Real ao Longo do Tempo. Na parte superior esquerda do portal, clique em **Custos** > **Análise de Custo** > **Custo Real ao Longo do Tempo**. Quando você abre o relatório pela primeira vez, não há nenhum grupo ou filtro aplicado.

Aqui está um exemplo de relatório:

![Exemplo de relatório de custo real ao longo do tempo](./media/tutorial-review-usage/actual-cost01.png)

O relatório mostra todos os gastos nos últimos 30 dias. Para exibir apenas os gastos para serviços do Azure, aplique o grupo Serviço e, em seguida, filtre todos os serviços do Azure. A imagem a seguir mostra os serviços filtrados.

![Exemplo mostrando serviços do Azure filtrados](./media/tutorial-review-usage/actual-cost02.png)

No exemplo anterior, menos dinheiro foi gasto a partir de 29/10/2018. No entanto, muitas colunas podem confundir uma tendência óbvia. Você pode alterar o modo de exibição do relatório para um gráfico de linha ou de área para ver os dados exibidos em outros modos de exibição. A imagem a seguir mostra a tendência mais claramente.

![Exemplo mostrando uma tendência de custo decrescente de VM do Azure](./media/tutorial-review-usage/actual-cost03.png)

Continuando com o exemplo, você pode ver que o custo da VM do Azure caiu. Os custos para outros serviços do Azure também começaram a cair naquele dia. Portanto, o que causou essa redução de gastos? Neste exemplo, um grande projeto de trabalho foi concluído, de forma que o consumo de vários serviços do Azure também caiu.

Para assistir a um vídeo de tutorial sobre o acompanhamento de tendências de uso e custo, consulte [analisar seus dados versus tempo com a Cloudyn de cobrança da nuvem](https://youtu.be/7LsVPHglM0g).

## <a name="detect-usage-inefficiencies"></a>Detectar ineficiências de uso

Os relatórios do otimizador melhoram a eficiência, otimizam o uso e identificam maneiras de economizar o dinheiro gasto com recursos de nuvem. Eles são muito úteis para recomendações de dimensionamento econômico cujo objetivo é ajudar a reduzir as VMs ociosas ou caras.

Um problema comum que afeta as organizações quando elas começam a mover recursos para a nuvem é a estratégia de virtualização. Geralmente, elas usam uma abordagem semelhante à que já era usada para a criação de máquinas virtuais para o ambiente de virtualização local. E assumem que os custos serão reduzidos ao mover suas VMs locais para a nuvem, no estado em que se encontram. No entanto, essa abordagem provavelmente não reduz os custos.

O problema é que a infraestrutura existente já foi paga. Os usuários podiam criar e manter grandes VMs em execução caso quisessem, mesmo sendo VMs ociosas, sem muita consequência. Mover VMs grandes ou ociosas para a nuvem provavelmente *aumenta* os custos. A alocação de custo para recursos é muito importante ao firmar contratos com provedores de serviços de nuvem. Você deverá pagar por aquilo que confirmar mesmo se não usar o recurso completamente.

O relatório Recomendações de Dimensionamento Econômico identifica a possível economia anual comparando a capacidade do tipo de instância VM com os dados históricos de uso de CPU e de memória.  

No menu na parte superior do portal, clique em **Otimizador** > **Otimização de Dimensionamento** > **Recomendações de Dimensionamento Econômico**. Se for útil, aplique um filtro para reduzir os resultados. Aqui está uma imagem de exemplo.

![Relatório de recomendações de dimensionamento econômico para VMs do Azure](./media/tutorial-review-usage/sizing01.png)

Neste exemplo, é possível economizar US$ 2.382 ao seguir as recomendações para alterar os tipos de instância de VM. Clique no símbolo de adição (+) em **Detalhes** da primeira recomendação. Aqui estão os detalhes sobre a primeira recomendação.

![Exemplo mostrando detalhes da recomendação](./media/tutorial-review-usage/sizing02.png)

Exiba as IDs da instância VM clicando no símbolo de mais ao lado de **Lista de Candidatos**.

![Exemplo mostrando uma lista de candidatos a VM a redimensionar](./media/tutorial-review-usage/sizing03.png)

Para assistir a um vídeo tutorial sobre como detectar ineficiências de uso, consulte [Otimizando o tamanho da VM no Cloudyn](https://youtu.be/1xaZBNmV704).

O Gerenciamento de Custos do Azure também fornece recomendações de economia de custo para serviços do Azure. Para saber mais, confira [Tutorial: Otimizar os custos usando recomendações](../costs/tutorial-acm-opt-recommendations.md).

## <a name="create-alerts-for-unusual-spending"></a>Criar alertas para gastos incomuns

Os alertas permitem que você notifique automaticamente outros stakeholders sobre anomalias de gastos e riscos de excesso de gastos. Você pode criar alertas usando relatórios que dão suporte para alertas com base no orçamento e nos limites de custo.

Este exemplo usa o relatório **Custo real ao longo do tempo** para enviar uma notificação quando os gastos em uma VM do Azure se aproximarem do seu orçamento total. Nesse cenário, você tem um orçamento total de US$ 20.000 e deseja receber uma notificação quando os custos estiverem se aproximando da metade de seu orçamento, US$ 9.000, e um alerta adicional quando os custos atingirem US$ 10.000.

1. No menu na parte superior do portal do Cloudyn, selecione **Custos** > **Análise de custo** > **Custo real ao longo do tempo**.
2. Defina **Grupos** para **Serviço** e defina **Filtrar no serviço** para **Azure/VM**.
3. Na parte superior direita do relatório, selecione **Ações** e selecione **Agendar relatório**.
4. Para enviar um email a si mesmo com o relatório a intervalos programados, selecione a guia **Agendamento** no diálogo **Salvar ou Agendar este relatório**. Selecione **Enviar por email**. As marcas, o agrupamento e a filtragem que você usar serão incluídos no relatório por email.
5. Selecione a guia **Limite** e selecione **Custo real versus Limite**.
   1. Na caixa de limite **Alerta vermelho**, insira 10000.
   2. Na caixa de limite **Alerta amarela**, insira 9000.
   3. Na caixa **Número de alertas consecutivos**, digite o número de alertas consecutivos a receber. Quando você receber o número total de alertas especificado, não será enviado mais nenhum alerta.
6. Clique em **Salvar**.

![Exemplo mostrando alertas amarelos e vermelhos com base nos limites de gastos](./media/tutorial-review-usage/schedule-alert01.png)

Você também pode escolher a métrica de limite **Percentual de Custo versus Orçamento** para criar alertas. Isso permite que você especifique os limites como porcentagens do orçamento, em vez de valores de moeda.

## <a name="export-data"></a>Exportar dados

Assim como você cria alertas para os relatórios, também pode exportar dados de qualquer relatório. Por exemplo, talvez você queira exportar uma lista de contas do Cloudyn ou outros dados do usuário. Para exportar qualquer relatório, abra o relatório e, em seguida, na parte superior direita do relatório, clique em **Ações**. Algumas das ações que talvez você queira executar são **Exportar todos os dados do relatório** para que você possa baixar ou imprimir as informações. Ou, você pode selecionar **Agendar relatório** para agendar o envio do relatório como um email.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Acompanhar as tendências de uso e custo
> * Detectar ineficiências de uso
> * Criar alertas para gastos incomuns ou excesso de gastos
> * Exportar dados


Avance para o próximo tutorial para aprender a prever despesas usando dados históricos.

> [!div class="nextstepaction"]
> [Previsão de despesas futuras](./tutorial-forecast-spending.md)