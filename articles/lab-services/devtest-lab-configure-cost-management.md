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
ms.openlocfilehash: 9180c29b807ef26c6426aab75fe74870fef9669a
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68318157"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Acompanhe os custos associados a um laboratório no Azure DevTest Labs
Este artigo fornece informações sobre como controlar o custo do seu laboratório. Ele mostra como exibir o Trent de custo estimado para o mês do calendário atual do laboratório. O artigo também mostra como exibir o custo acumulado no mês por recurso no laboratório.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Exibir a tendência de custo estimado mensal do laboratório 
Nesta seção, você aprenderá a usar o gráfico de **tendência de custo estimado mensal** para exibir o custo estimado do mês do calendário atual e o custo do fim do mês projetado para o mês do calendário atual. Você também aprende a gerenciar os custos de laboratório definindo metas de gastos e limites que, quando atingido, disparam os laboratórios de DevTest para relatar os resultados para você.

Para exibir o gráfico Tendência de custo estimado mensal, siga estas etapas: 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório.  
4. Selecione **configuração e políticas** no menu à esquerda.  
4. Selecione **tendência de custo** na seção **controle de custo** no menu à esquerda. A captura de tela a seguir mostra um exemplo de um gráfico de custo. 
   
    ![Gráfico de custo](./media/devtest-lab-configure-cost-management/graph.png)

    O valor **Custo estimado** é o custo estimado até a data do mês do calendário atual. O **Custo projetado** é o custo estimado para todo o mês do calendário atual, calculado usando o custo do laboratório nos cinco dias anteriores.

    Observe que os valores do custo são arredondados para o próximo número inteiro. Por exemplo: 

   * 5.01 arredonda até 6 
   * 5.50 arredonda até 6
   * 5.99 arredonda até 6

     Como é indicado acima do gráfico, os custos vistos por padrão no gráfico são custos *estimados* usando as tarifas da oferta [Pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/). Você também pode definir duas metas de gastos que são exibidas nos gráficos [gerenciando as metas de custos de seu laboratório.](#managing-cost-targets-for-your-lab)

     Os seguintes custos *não* estão incluídos no cálculo de custo:

   * As assinaturas do CSP e do Dreamspark não têm suporte atualmente, pois o Azure DevTest Labs usa as [APIs de cobrança do Azure](../billing/billing-usage-rate-card-overview.md) para calcular o custo do laboratório e essas APIs não dão suporte a assinaturas do Dreamspark ou do CSP.
   * Suas tarifas da oferta. No momento, não é possível usar as tarifas de oferta (mostradas em sua assinatura) que você negociou com a Microsoft ou parceiros Microsoft. Somente as tarifas pagas conforme o uso são usadas.
   * Seus impostos
   * Seus descontos
   * Sua moeda de cobrança. No momento, o custo do laboratório é exibido apenas na moeda USD.

### <a name="managing-cost-targets-for-your-lab"></a>Gerenciando as metas de custos para seu laboratório
O DevTest Labs lhe permite gerenciar melhor os custos em seu laboratório definindo uma meta de gasto que você pode exibir no gráfico Tendência de custo estimada por mês. O DevTest Labs também pode enviar uma notificação quando a meta ou o limite de gasto especificado é atingido. 

1. Na página **tendência de custo** , selecione **gerenciar destino**.

    ![Botão Gerenciar meta](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Na página **gerenciar destino** , especifique um alvo e limites de gastos. Você também pode definir se cada limite selecionado é relatado no gráfico de tendência de custos ou por meio de uma notificação de webhook.

    ![Painel Gerenciar meta](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Selecione um período durante o qual você deseja que as metas de custo sejam controladas.
      - **Mensal**: as metas de custo são controladas por mês.
      - **Corrigido**: os destinos de custo são rastreados para o intervalo de datas especificado nas datas de início e término. Normalmente, esses valores representam por quanto tempo seu projeto está agendado para ser executado.
   - Especifique uma **Meta de custo**. Por exemplo, quanto você planeja gastar neste laboratório no período de tempo definido.
   - Selecione para habilitar ou desabilitar qualquer limite que você deseja que seja relatado – em incrementos de 25% –, até 125% de sua **Meta de custo** especificada.
      - **Notificar**: Quando esse limite for atingido, você será notificado por uma URL de webhook que você especificar.
      - **Plotar no gráfico**: Quando esse limite é atingido, os resultados são plotados no gráfico de tendência de custo que você pode exibir, conforme descrito em exibindo o gráfico de tendência de custo estimado mensal.
   - Se optar por **Notificar** quando o limite for atingido, você precisará especificar uma URL de webhook. Na área de integração de custos, selecione **Clique aqui para adicionar uma integração**. Insira uma **URL** de webhook no painel configurar notificação e, em seguida, selecione **OK**.

       ![Painel Configurar notificação](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - Se especificar **Notificar**, você precisará definir uma URL de webhook.
     - Da mesma forma, se definir uma URL de webhook, você precisará definir **Notificação** como **Ativado** no painel de limite de custos.
     - Você deve criar um webhook antes de inseri-lo aqui.  

       Para saber mais sobre os webhooks, veja [Criar um webhook ou uma função da API do Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Exibir custo por recurso 
O recurso de tendência de custo mensal em laboratórios permite que você veja quanto você gastou no mês do calendário atual. Ele também mostra a projeção dos gastos até o fim do mês, com base em seus gastos nos últimos sete dias. Para ajudá-lo a entender por que os gastos no laboratório estão atendendo aos limites no início, você pode usar o recurso de **custo por recurso** que mostra o custo acumulado do mês **por recurso** em uma tabela.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Selecione **configuração e políticas** no menu à esquerda.
5. Selecione **custo por recurso** na seção **controle de custo** no menu à esquerda. Você verá os custos associados a cada recurso associado a um laboratório. 

    ![Custo por recurso](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Esse recurso ajuda a identificar facilmente os recursos que custam mais para que você possa tomar medidas para reduzir os gastos com o laboratório. Por exemplo, o custo de uma VM é baseado no tamanho da VM. Quanto maior o tamanho da VM, mais é o custo. Você pode encontrar facilmente o tamanho de uma VM e o proprietário, para que você possa se comunicar com o proprietário da VM para entender por que esse tamanho de VM é necessário e se há uma chance de diminuir o tamanho.

A política de desligamento [automático](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) ajuda a reduzir o custo desligando VMs de laboratório em um determinado momento do dia. No entanto, um usuário de laboratório pode recusar a política de desligamento, o que aumenta o custo da execução da VM. Você pode selecionar uma VM na tabela para ver se ela foi recusada da política de desligamento automático. Se esse for o caso, você poderá se comunicar com o proprietário da VM para descobrir por que a VM foi recusada da política.
 
## <a name="next-steps"></a>Próximas etapas
Eis aqui algumas coisas para experimentar a seguir:

* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) - saiba como definir as várias políticas usadas para controlar como seu laboratório e suas VMs são usados. 
* [Criar imagem personalizada](devtest-lab-create-template.md) – durante a criação de uma VM, você especifica uma base, que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada de um arquivo VHD.
* [Configurar imagens do Marketplace](devtest-lab-configure-marketplace-images.md) – os DevTest Labs dão suporte à criação de VMs com base em imagens do Azure Marketplace. Este artigo ilustra como especificar quais imagens (caso haja alguma) do Azure Marketplace podem ser usadas durante a criação de VMs em um laboratório.
* [Criar uma VM em um laboratório](devtest-lab-add-vm.md) – ilustra como criar uma VM de uma imagem base (personalizada ou do Marketplace) e como trabalhar com artefatos na VM.

