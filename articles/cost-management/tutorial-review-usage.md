---
title: Examinar o uso e os custos do Azure Cost Management | Microsoft Docs
description: "Examine o uso e os custos para acompanhar tendências, detectar ineficiências e criar alertas."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 09/19/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 4440ca81545978ab7438f1684ce71ded24c326e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="review-usage-and-costs"></a>Examinar o uso e os custos

O Azure Cost Management da Cloudyn mostra o uso e os custos para que seja possível acompanhar tendências, detectar ineficiências e criar alertas. Todos os dados de uso e de custo são exibidos em relatórios e painéis da Cloudyn. Os exemplos neste tutorial demonstram como examinar o uso e os custos usando painéis e relatórios. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Acompanhar as tendências de uso e custo
> * Detectar ineficiências de uso
> * Criar alertas para gastos incomuns ou excesso de gastos



## <a name="open-the-cloudyn-portal"></a>Abra o portal da Cloudyn

O uso e os custos são examinados no portal da Cloudyn. Abra o portal da Cloudyn no portal do Azure ou navegue até https://app.cloudyn.com e faça logon.

## <a name="track-usage-and-cost-trends"></a>Acompanhar as tendências de uso e custo

Acompanhe a quantia de dinheiro real gasta com uso e custos usando relatórios Ao Longo do Tempo para identificar tendências. Para começar a verificar tendências, use o relatório de Custo Real ao Longo do Tempo. No menu de relatórios na parte superior do portal, clique em **Custo** > **Análise de Custo** > **Custo Real ao Longo do Tempo**. Quando você abre o relatório pela primeira vez, não há nenhum grupo ou filtro aplicado.

Aqui está um exemplo de relatório:

![relatório de exemplo](./media/tutorial-review-usage/actual-cost01.png)

O relatório mostra todos os gastos nos últimos 30 dias. Para exibir apenas os gastos para serviços do Azure, aplique o grupo Serviço e, em seguida, filtre todos os serviços do Azure. A imagem abaixo mostra os serviços filtrados.

![serviços filtrados](./media/tutorial-review-usage/actual-cost02.png)

No exemplo acima, uma quantia de dinheiro menor passou a ser gasta a partir de 31-08-2017. Essa tendência de custo continua para vários serviços por cerca de nove dias. Em seguida, as despesas adicionais continuam como antes. No entanto, muitas colunas podem confundir uma tendência óbvia. Você pode alterar o modo de exibição do relatório para um gráfico de linha ou de área para ver os dados exibidos em outros modos de exibição. A imagem abaixo mostra a tendência mais claramente.

![tendência no relatório](./media/tutorial-review-usage/actual-cost03.png)

No exemplo, você vê claramente que o custo de armazenamento do Azure caiu a partir de 31-08-2017 enquanto as despesas com outros serviços do Azure permaneceram no mesmo nível. Portanto, o que causou essa redução de gastos? Neste exemplo, alguns funcionários estavam de férias do trabalho em não usaram o serviço de armazenamento.

## <a name="detect-usage-inefficiencies"></a>Detectar ineficiências de uso

Os relatórios do otimizador melhoram a eficiência, otimizam o uso e identificam maneiras de economizar o dinheiro gasto com recursos de nuvem. Eles são muito úteis para recomendações de dimensionamento econômico cujo objetivo é ajudar a reduzir as VMs ociosas ou caras.

Um problema comum que afeta as organizações quando elas começam a mover recursos para a nuvem é a estratégia de virtualização. Geralmente, elas usam uma abordagem semelhante à que já era usada para a criação de máquinas virtuais para o ambiente de virtualização local. E assumem que os custos serão reduzidos ao mover suas VMs locais para a nuvem, no estado em que se encontram. No entanto, essa abordagem provavelmente não reduz os custos.

O problema é que a infraestrutura existente já foi paga. Os usuários podiam criar e manter grandes VMs em execução caso quisessem, mesmo sendo VMs ociosas, sem grandes consequências. Mover VMs muito grandes ou ociosas para a nuvem provavelmente *aumenta* os custos. A alocação de custo para recursos é muito importante ao iniciar contratos com provedores de serviços de nuvem. Você deverá pagar por aquilo que confirmar mesmo se não usar o recurso completamente.

O relatório Recomendações de Dimensionamento Econômico identifica a possível economia anual comparando a capacidade do tipo de instância VM com os dados históricos de uso de CPU e de memória.  

No menu de relatórios na parte superior do portal, clique em **Otimizador** > **Otimização de Preços** > **Recomendações de Dimensionamento Econômico**. Filtre o provedor para o Azure para ver apenas as VMs do Azure. Aqui está uma imagem de exemplo.

![VMs do Azure](./media/tutorial-review-usage/sizing01.png)

Neste exemplo, é possível economizar $3.114 ao seguir as recomendações para alterar os tipos de instância VM. Clique no símbolo de adição (+) em **Detalhes** da primeira recomendação. Aqui estão os detalhes sobre a primeira recomendação.

![detalhes de recomendação](./media/tutorial-review-usage/sizing02.png)

Clique no símbolo de adição ao lado de **Lista de Candidatos** para exibir os IDs de instância VM.

![Lista de candidatos](./media/tutorial-review-usage/sizing03.png)

## <a name="create-alerts-for-unusual-spending"></a>Criar alertas para gastos incomuns

Você pode alertar os stakeholders automaticamente sobre riscos de anomalia de gastos e de excesso de gastos. Você pode criar alertas com rapidez e facilidade usando relatórios que dão suporte para alertas com base no orçamento e nos limites de custo.

Crie um alerta para qualquer gasto usando qualquer relatório de custo. Neste exemplo, use o relatório Custo Real ao Longo do Tempo para ser notificado quando os gastos da VM do Azure se aproximarem do seu orçamento total. No menu de relatórios na parte superior do portal, clique em **Custo** > **Análise de Custo** > **Custo Real ao Longo do Tempo**. Defina **Grupos** para **Serviço** e defina **Filtrar no serviço** para **Azure/VM**. Na parte superior direita do relatório, clique em **Ações** e, em seguida, selecione **Agendar relatório**.

Use a guia **Agendamento** para enviar o relatório para o seu email usando a frequência desejada. As marcas, o agrupamento e a filtragem que você usar serão incluídos no relatório por email. Clique na guia **Limite** e selecione **Custo Real versus Limite**. Se você tiver um orçamento total de $500.000 e desejar ser notificado quando os custos se aproximarem da metade, crie um **Alerta vermelho** em $250.000 e um **Alerta amarelo** em $240.000. Em seguida, escolha o número de alertas consecutivos. Quando você receber o número total de alertas especificado, não será enviado mais nenhum alerta. Salve o relatório agendado.

![relatório de exemplo](./media/tutorial-review-usage/schedule-alert01.png)

Você também pode escolher a métrica de limite Percentual de Custo versus Limite para criar alertas. Com essa métrica, você pode usar porcentagens do orçamento, em vez de valores de moeda.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Acompanhar as tendências de uso e custo
> * Detectar ineficiências de uso
> * Criar alertas para gastos incomuns ou excesso de gastos


Avance para o próximo tutorial para saber mais sobre como controlar o acesso a dados.

> [!div class="nextstepaction"]
> [Controlar o acesso a dados](tutorial-user-access.md)
