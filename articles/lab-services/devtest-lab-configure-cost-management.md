---
title: Exibir a tendência de custo estimado mensal do laboratório no Azure DevTest Labs | Microsoft Docs
description: Saiba mais sobre o gráfico de tendência de custo estimado mensal do Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: spelluru
ms.openlocfilehash: f761af3a5a3f08e4da89d8869aea5d666ecd69d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60868194"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Controlar os custos associados a um laboratório no Azure DevTest Labs
Este artigo fornece informações sobre como controlar o custo do seu laboratório. Ele mostra como exibir a estimados trent de custo para o mês do calendário atual para o laboratório. O artigo também mostra como exibir month-to-date custo por recurso no laboratório.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Exibir a tendência de custo mensal estimado de laboratório 
Nesta seção, você aprenderá a usar o **tendência de custo estimado mensal** gráfico para exibir estimado custo para a data o mês do calendário atual e o custo projetado do final do mês para o mês do calendário atual. Você também aprenderá a gerenciar os custos de laboratório definindo metas e limites de gastos que, quando alcançadas, fazem DevTest Labs para relatar os resultados para você.

Para exibir o gráfico Tendência de custo estimado mensal, siga estas etapas: 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório.  
4. Selecione **configuração e políticas** no menu à esquerda.  
4. Selecione **tendência de custo** na **controle de custos** seção no menu à esquerda. Captura de tela a seguir mostra um exemplo de um gráfico de custo. 
   
    ![Gráfico de custo](./media/devtest-lab-configure-cost-management/graph.png)

    O valor **Custo estimado** é o custo estimado até a data do mês do calendário atual. O **Custo projetado** é o custo estimado para todo o mês do calendário atual, calculado usando o custo do laboratório nos cinco dias anteriores.

    Observe que os valores do custo são arredondados para o próximo número inteiro. Por exemplo:  

   * 5.01 arredonda até 6 
   * 5.50 arredonda até 6
   * 5.99 arredonda até 6

     Como é indicado acima do gráfico, os custos vistos por padrão no gráfico são custos *estimados* usando as tarifas da oferta [Pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/). Você também pode definir duas metas de gastos que são exibidas nos gráficos [gerenciando as metas de custos de seu laboratório.](#managing-cost-targets-for-your-lab)

     São os seguintes custos *não* incluídos no cálculo de custo:

   * As assinaturas do CSP e do Dreamspark não têm suporte atualmente, pois o Azure DevTest Labs usa as [APIs de cobrança do Azure](../billing/billing-usage-rate-card-overview.md) para calcular o custo do laboratório e essas APIs não dão suporte a assinaturas do Dreamspark ou do CSP.
   * Suas tarifas da oferta. No momento, não é possível usar as tarifas de oferta (mostradas em sua assinatura) que você negociou com a Microsoft ou parceiros Microsoft. Somente as tarifas pagas conforme o uso são usadas.
   * Seus impostos
   * Seus descontos
   * Sua moeda de cobrança. No momento, o custo do laboratório é exibido apenas na moeda USD.

### <a name="managing-cost-targets-for-your-lab"></a>Gerenciando as metas de custos para seu laboratório
O DevTest Labs lhe permite gerenciar melhor os custos em seu laboratório definindo uma meta de gasto que você pode exibir no gráfico Tendência de custo estimada por mês. O DevTest Labs também pode enviar uma notificação quando a meta ou o limite de gasto especificado é atingido. 

1. Sobre o **tendência de custo** página, selecione **gerenciar meta**.

    ![Botão Gerenciar meta](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Sobre o **gerenciar meta** , especifique um destino de gasto e limites. Você também pode definir se cada limite selecionado é relatado no gráfico de tendência de custos ou por meio de uma notificação de webhook.

    ![Painel Gerenciar meta](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Selecione um período durante o qual você deseja que as metas de custo sejam controladas.
      - **Mensal**: as metas de custo são controladas por mês.
      - **Fixo**: metas de custo são controladas para o intervalo de datas que você especificar as datas de início e término. Normalmente, esses valores representam quanto seu projeto está agendado para ser executado.
   - Especifique uma **Meta de custo**. Por exemplo, quanto você planeja gastar neste laboratório no período de tempo que você definiu.
   - Selecione para habilitar ou desabilitar qualquer limite que você deseja que seja relatado – em incrementos de 25% –, até 125% de sua **Meta de custo** especificada.
      - **Notificar**: Quando esse limite for atingido, você será notificado por uma URL de webhook que você especificar.
      - **Plotar no gráfico**: Quando esse limite for atingido, os resultados serão plotados no gráfico de tendência de custo que você pode exibir, conforme descrito em visualizando o gráfico de tendência de custo estimado mensal.
   - Se optar por **Notificar** quando o limite for atingido, você precisará especificar uma URL de webhook. Na área de integração de custos, selecione **Clique aqui para adicionar uma integração**. Insira um **URL de Webhook** no painel Configurar notificação e, em seguida, selecione **Okey**.

       ![Painel Configurar notificação](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - Se especificar **Notificar**, você precisará definir uma URL de webhook.
     - Da mesma forma, se definir uma URL de webhook, você precisará definir **Notificação** como **Ativado** no painel de limite de custos.
     - Você deve criar um webhook antes de inseri-lo aqui.  

       Para saber mais sobre os webhooks, veja [Criar um webhook ou uma função da API do Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Exibir custo por recurso 
O recurso de tendência de custo mensal Labs permite que você veja o quanto você gastou no mês do calendário atual. Ele também mostra a projeção dos gastos com até o final do mês, com base em seus gastos nos últimos sete dias. Para ajudá-lo a entender por que os gastos no laboratório atenda os limites logo no início, você pode usar o **custo por recurso** recurso que mostra o custo de month-to-date **por recurso** em uma tabela.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Selecione **configuração e políticas** no menu à esquerda.
5. Selecione **custo por recurso** na **controle de custos** seção no menu à esquerda. Você verá os custos associados a cada recurso associado com um laboratório. 

    ![Custo por recurso](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Esse recurso ajuda a identificar facilmente os recursos que custam mais para que você pode tomar medidas para reduzir os gastos de laboratório. Por exemplo, o custo de uma VM é baseado no tamanho da VM. Quanto maior o tamanho da VM, mais é o custo. Você pode localizar facilmente o tamanho de uma VM e o proprietário, para que você pode se comunicar com o proprietário da VM para entender por que esse tamanho VM é necessária e se há uma chance para reduzir o tamanho.

[Política de desligamento automático](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown) ajuda a reduzir o custo por desligar as VMs de laboratório em um determinado momento do dia. No entanto, um usuário de laboratório pode recusar a política de desligamento, o que aumenta o custo da execução da VM. Você pode selecionar uma VM na tabela para ver se ele tem sido optou por horizontal da política de desligamento automático. Se esse for o caso, você pode conversar com o proprietário da VM para descobrir por que a VM tem sido tiver optado-da política.
 
## <a name="next-steps"></a>Próximas etapas
Eis aqui algumas coisas para experimentar a seguir:

* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) - saiba como definir as várias políticas usadas para controlar como seu laboratório e suas VMs são usados. 
* [Criar imagem personalizada](devtest-lab-create-template.md) – durante a criação de uma VM, você especifica uma base, que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada de um arquivo VHD.
* [Configurar imagens do Marketplace](devtest-lab-configure-marketplace-images.md) – os DevTest Labs dão suporte à criação de VMs com base em imagens do Azure Marketplace. Este artigo ilustra como especificar quais imagens (caso haja alguma) do Azure Marketplace podem ser usadas durante a criação de VMs em um laboratório.
* [Criar uma VM em um laboratório](devtest-lab-add-vm.md) – ilustra como criar uma VM de uma imagem base (personalizada ou do Marketplace) e como trabalhar com artefatos na VM.

