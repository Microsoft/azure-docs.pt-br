---
title: Excluir uma forma de pagamento da cobrança do Azure
description: Descreve como excluir uma forma de pagamento usada por uma assinatura do Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/07/2020
ms.author: banders
ms.openlocfilehash: 15d6c7731b541de638ceaf7828a7ce962cbf154a
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91827546"
---
# <a name="delete-an-azure-billing-payment-method"></a>Excluir uma forma de pagamento da cobrança do Azure

Este documento fornece instruções para ajudar você a excluir uma forma de pagamento, como um cartão de crédito, de diferentes tipos de assinaturas do Azure. Você pode excluir uma forma de pagamento para:

- MCA (Contrato de Cliente da Microsoft)
- MOSP (Programa de Assinatura Online da Microsoft), também conhecido como pré-pago

Seja qual for o tipo de assinatura do Azure, você precisa cancelá-la para que possa excluir a forma de pagamento associada.

Não há suporte para a remoção de formas de pagamento para outros tipos de assinatura do Azure, como o Contrato de Parceiro da Microsoft e o Contrato Enterprise.

## <a name="delete-an-mca-payment-method"></a>Excluir uma forma de pagamento com MCA

Somente o usuário que criou a conta com o Contrato de Cliente da Microsoft pode excluir uma forma de pagamento.

Para excluir uma forma de pagamento para um Contrato de Cliente da Microsoft, execute as etapas a seguir.

1. Entre no Portal do Azure em https://portal.azure.com/.
1. Navegue para **Gerenciamento de Custos + Cobrança**.
1. Se necessário, selecione um escopo do orçamento.
1. Na lista de menus à esquerda em **Cobrança**, selecione **Perfis de cobrança**.  
    :::image type="content" source="./media/delete-azure-payment-method/billing-profiles.png" alt-text="Captura de tela de exemplo mostrando Perfis de cobrança no portal do Azure" lightbox="./media/delete-azure-payment-method/billing-profiles.png" :::
1. Na lista de perfis de cobrança, selecione aquele em que a forma de pagamento está sendo usada.  
    :::image type="content" source="./media/delete-azure-payment-method/select-billing-profile.png" alt-text="Imagem de exemplo mostrando a lista de perfis de cobrança" :::
1. Na lista de menus à esquerda, em **Configurações**, selecione **Formas de pagamento**.
1. Na página de formas de pagamento de seu perfil de cobrança, é mostrada uma tabela de formas de pagamento na seção **Seus cartões de crédito**. Localize o cartão de crédito que deseja excluir, selecione as reticências ( **…** ) e escolha **Excluir**.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-credit-card.png" alt-text="Exemplo mostrando onde excluir um cartão de crédito" :::
1. A página Excluir uma forma de pagamento é exibida. O Azure verifica se a forma de pagamento está em uso.
    - Quando a forma de pagamento não está sendo usada, a opção **Excluir** fica habilitada. Selecione-a para excluir as informações do cartão de crédito.
    - Se a forma de pagamento estiver sendo usada, ele precisará ser substituída ou desanexada. Continue lendo as seções a seguir. Eles explicam como **desanexar** a forma de pagamento que está sendo usada por sua assinatura.

### <a name="detach-payment-method-used-by-an-mca-billing-profile"></a>Desanexar forma de pagamento usada por um perfil de cobrança com MCA

Se a forma de pagamento estiver sendo usada por um perfil de cobrança com MCA, você verá uma mensagem semelhante ao exemplo a seguir.

:::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-microsoft-customer-agreement.png" alt-text="Imagem de exemplo mostrando que uma forma de pagamento está sendo usada por um Contrato de Cliente da Microsoft" :::

Para desanexar uma forma de pagamento, uma lista de condições precisam ser atendidas. Se alguma condição não for atendida, serão exibidas instruções explicando como atendê-la. Também será exibido um link que leva para o local em que você pode resolver a condição.

Quando todas as condições forem satisfeitas, você poderá desanexar a forma de pagamento do perfil de cobrança.

> [!NOTE]
> Quando a forma de pagamento padrão é desanexada, o perfil de cobrança é colocado em um estado _inativo_. Qualquer coisa excluída nesse processo não poderá ser recuperada. Depois que um perfil de cobrança for definido como inativo, você precisará se inscrever para uma nova assinatura do Azure para criar recursos.

#### <a name="to-detach-a-payment-method"></a>Para desanexar uma forma de pagamento

1. Na área Excluir uma forma de pagamento, selecione o link **Desanexar a forma de pagamento atual**.
1. Se todas as condições forem atendidas, selecione **Desanexar**. Caso contrário, prossiga para a próxima etapa.
1. Se Desanexar estiver indisponível, uma lista de condições será mostrada. Execute as ações listadas. Selecione o link mostrado na área Desanexar a forma de pagamento padrão. Veja um exemplo de ação corretiva que explica as medidas que você precisa adotar.  
    :::image type="content" source="./media/delete-azure-payment-method/azure-subscriptions.png" alt-text="Exemplo mostrando uma ação corretiva necessária para desanexar uma forma de pagamento com MCA" :::
1. Ao selecionar o link da ação corretiva, você é redirecionado para a página do Azure em que executará a ação. Execute as ações de correção necessárias.
1. Se necessário, conclua todas as outras ações corretivas.
1. Navegue de volta para **Gerenciamento de Custos + Cobrança** > **Perfis de cobrança** > **Formas de pagamento**. Selecione **Desanexar**. Na parte inferior da página Desanexar a forma de pagamento padrão, selecione **Desanexar**.

> [!NOTE]
> - Após você cancelar uma assinatura, podem ser necessários até 90 dias para que ela seja excluída. Se quiser reduzir a espera, abra uma solicitação de suporte do Azure e peça para que a assinatura seja excluída imediatamente.
> - Você só pode excluir uma forma de pagamento após todos os encargos anteriores de um perfil de cobrança terem sido liquidados. Se você estiver em um período de cobrança ativo, precisará aguardar até o fim do período de cobrança para excluir a forma de pagamento. **Certifique-se de que todas as outras condições de desanexação sejam atendidas enquanto aguarda o encerramento do período de cobrança**.

## <a name="delete-a-mosp-payment-method"></a>Excluir uma forma de pagamento com MOSP

Você precisa ser um administrador da conta para excluir uma forma de pagamento.

Se sua forma de pagamento estiver sendo usada por uma assinatura com MOSP, execute as etapas a seguir.

1. Entre no Portal do Azure em https://portal.azure.com/.
1. Navegue para **Gerenciamento de Custos + Cobrança**.
1. Se necessário, selecione um escopo do orçamento.
1. Na lista de menus à esquerda, em **Cobranças**, selecione **Formas de pagamento**.
1. Na área Formas de pagamento, selecione a _linha_ em que sua forma de pagamento se encontra. Não selecione o link da forma de pagamento. Pode não haver uma indicação visual de que você selecionou a forma de pagamento.
1. Selecione **Excluir**.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-mosp-payment-method.png" alt-text="Exemplo mostrando uma ação corretiva necessária para desanexar uma forma de pagamento com MOSP" :::
1. Na área Excluir uma forma de pagamento, selecione **Excluir** se todas as condições forem atendidas. Se Excluir estiver indisponível, vá para a próxima etapa.
1. Uma lista de condições é mostrada. Execute as ações listadas. Selecione o link mostrado na área Excluir uma forma de pagamento.  
    :::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-mosp.png" alt-text="Imagem de exemplo mostrando que uma forma de pagamento está sendo usada por uma assinatura com MOSP" :::
1. Ao selecionar o link da ação corretiva, você é redirecionado para a página do Azure em que executará a ação. Execute as ações de correção necessárias.
1. Se necessário, conclua todas as outras ações corretivas.
1. Navegue de volta para **Gerenciamento de Custos + Cobrança** > **Perfis de cobrança** > **Formas de pagamento** e exclua a forma de pagamento.

> [!NOTE]
> Após você cancelar uma assinatura, podem ser necessários até 90 dias para que ela seja excluída. Se quiser reduzir a espera, abra uma solicitação de suporte do Azure e peça para que a assinatura seja excluída imediatamente.

## <a name="next-steps"></a>Próximas etapas

- Se precisar de mais informações sobre o cancelamento de sua assinatura do Azure, confira [Cancelar sua assinatura do Azure](cancel-azure-subscription.md).
- Para obter mais informações sobre como adicionar ou atualizar um cartão de crédito, confira [Adicionar ou atualizar um cartão de crédito para o Azure](change-credit-card.md).