---
title: Tarefas de administrador da conta no portal do Azure
description: Descreve como executar operações de pagamento no portal do Azure
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/26/2020
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: bd46e7b2f0713da67842def47dfeadc837027d8f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027961"
---
# <a name="account-administrator-tasks-in-the-azure-portal"></a>Tarefas de administrador da conta no portal do Azure

Este artigo explica como executar as seguintes tarefas no portal do Azure:
- Gerenciar os métodos de pagamento da assinatura
- Remover o limite de gastos da sua assinatura
- Adicionar créditos à sua assinatura Azure via Open

Você deve ser o administrador da conta para executar qualquer uma dessas tarefas.

## <a name="navigate-to-your-subscriptions-payment-methods"></a>Navegar até os métodos de pagamento da sua assinatura

1. Entre no Portal do Azure como Administrador da Conta.

1. Pesquise **Gerenciamento de Custos + Cobrança**.

    ![Captura de tela que mostra a pesquisa de gerenciamento de custos + cobrança ](./media/account-admin-tasks/search-bar.png)

1. Na lista **Minhas assinaturas**, selecione a assinatura à qual você deseja adicionar o cartão de crédito.

   ![A captura de tela mostra a página Gerenciamento de Custos e Cobrança em que você pode selecionar uma assinatura.](./media/account-admin-tasks/cost-management-billing-overview-x.png)

   > [!NOTE]
   > Caso não veja algumas de suas assinaturas aqui, pode ser porque você alterou o diretório de assinatura em algum momento. Para essas assinaturas, você precisa alternar o diretório para o diretório original (o diretório no qual você se inscreveu inicialmente). Em seguida, repita a etapa 2.

1. Selecione **Formas de pagamento**.

    ![A captura de tela mostra a página Formas de pagamento em que você pode adicionar uma forma de pagamento.](./media/account-admin-tasks/subscription-payment-methods-blade.png)

Aqui você pode adicionar um novo cartão de crédito, alterar o método de pagamento ativo, editar os detalhes e excluir cartões de crédito.

### <a name="change-active-payment-method"></a>Alterar o método de pagamento ativo

É possível alterar o método de pagamento ativo adicionando um novo cartão de crédito ou escolhendo um que já esteja salvo. Para alterar o método de pagamento ativo para um novo cartão de crédito:

1. No canto superior esquerdo, selecione "+" para adicionar um cartão de crédito.

    ![Captura de tela que mostra o sinal de mais](./media/account-admin-tasks/subscription-payment-methods-plus.png)

1. Insira os detalhes do cartão de crédito no formulário à direita.

    ![Captura de tela que mostra o formulário adicionar cartão de crédito.](./media/account-admin-tasks/subscription-add-payment-method-x.png)

1. Para tornar este cartão sua forma de pagamento ativa, marque a caixa ao lado de **Tornar esta minha forma de pagamento ativa** acima do formulário. Esse cartão se tornará o meio de pagamento ativo para todas as assinaturas usando o mesmo cartão, como a assinatura selecionada.

    ![Captura de tela que mostra a caixa de seleção para tornar o método de pagamento ativo do cartão.](./media/account-admin-tasks/subscription-make-active-payment-method-x.png)

1. Selecione **Avançar**.

Para alterar o método de pagamento ativo para um cartão de crédito que já foi salvo:

1. Marque a caixa ao lado do cartão que você gostaria de tornar a forma de pagamento ativa.

    ![Captura de tela que mostra a caixa marcada ao lado do cartão de crédito](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. Clique em **Definir como ativo** na barra de comandos.

    ![Captura de tela que mostra o botão Definir como ativo](./media/account-admin-tasks/subscription-checked-payment-method-set-active.png)

### <a name="edit-credit-card-details"></a>Editar os detalhes do cartão de crédito

Para editar os detalhes do cartão de crédito, como a data de validade ou o endereço, clique no cartão de crédito que você deseja editar. Um formulário de cartão de crédito será exibido à direita.

![Captura de tela que mostra o cartão de crédito selecionado](./media/account-admin-tasks/subscription-edit-payment-method-x.png)

Atualize os detalhes do cartão de crédito e clique em **Salvar**.

### <a name="remove-a-credit-card-from-the-account"></a>Remover um cartão de crédito da conta

1. Selecione a caixa ao lado do cartão que você deseja excluir.

    ![Captura de tela que mostra a caixa marcada ao lado do cartão de crédito](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. Clique em **Excluir** na barra de comandos.

    ![Captura de tela que mostra o botão excluir](./media/account-admin-tasks/subscription-checked-payment-method-delete.png)

Se o seu cartão de crédito for a forma de pagamento ativa para as assinaturas da Microsoft, não será possível removê-lo de sua conta do Azure. Altere a forma de pagamento ativa para todas as assinaturas vinculadas a esse cartão de crédito e tente novamente.

### <a name="switch-to-invoice-payment"></a>Mudar para detalhes da fatura

Se você estiver qualificado para pagar por fatura (cheque/transferência eletrônica), poderá alternar sua assinatura para pagamento de fatura (cheque/transferência eletrônica) no portal do Azure.

1. Selecione **Pagar por fatura** na barra de comandos.

    ![A captura de tela mostra a página Formas de pagamento com a opção Pagar por fatura selecionada.](./media/account-admin-tasks/subscription-payment-methods-pay-by-invoice.png)

1. Insira o endereço para o método de pagamento da fatura.
1. Clique em **Próximo**.

Se você quiser ser aprovado para pagar por fatura, confira [Saiba como pagar por fatura](pay-by-invoice.md).

### <a name="edit-invoice-payment-address"></a>Editar o endereço de pagamento da fatura

Para editar o endereço do seu método de pagamento da fatura, clique em **Fatura** na lista de métodos de pagamento da sua assinatura. O formulário de endereço será aberto à direita.

## <a name="remove-spending-limit"></a>Remover o limite de gastos

O limite de gastos do Azure impede gastos sobre a sua quantidade de créditos. Você poderá remover o limite de gastos a qualquer momento, contanto que haja uma forma de pagamento válida associada à sua assinatura do Azure. Para os tipos de assinatura que têm créditos durante vários meses, como o Visual Studio Enterprise e o Visual Studio Professional, você pode escolher reabilitar o limite de gastos no início do próximo período de cobrança.

O limite de gastos não está disponível para assinaturas com planos de compromisso ou com preços pagos conforme o uso.

1. Entre no Portal do Azure como Administrador da Conta.
1. Pesquise **Gerenciamento de Custos + Cobrança**.

    ![Captura de tela que mostra a pesquisa de gerenciamento de custos + cobrança ](./media/account-admin-tasks/search-bar.png)

1. Na lista **Minhas assinaturas**, selecione sua assinatura do Visual Studio Enterprise.

   ![A captura de tela mostra a área Minhas assinaturas em que você pode selecionar sua assinatura do Visual Studio Enterprise.](./media/account-admin-tasks/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Caso não veja algumas de suas assinaturas do Visual Studio aqui, pode ser porque você alterou um diretório de assinatura em algum momento. Para essas assinaturas, você precisa alternar o diretório para o diretório original (o diretório no qual você se inscreveu inicialmente). Em seguida, repita a etapa 2.

1. Na visão geral da Assinatura, clique na faixa laranja para remover o limite de gastos.

    ![Captura de tela que mostra a faixa remover o limite de gastos](./media/account-admin-tasks/msdn-remove-spending-limit-banner-x.png)

1. Escolha se deseja remover o limite de gastos indefinidamente ou apenas para o período de cobrança atual.

   ![Captura de tela que mostra a folha remover o limite de gastos](./media/account-admin-tasks/remove-spending-limit-blade-x.png)

1. Clique **Selecionar método de pagamento** para escolher um método de pagamento para sua assinatura. Esse se tornará o método de pagamento ativo da sua assinatura.

1. Clique em **Concluir**.

## <a name="add-credits-to-azure-in-open-subscription"></a>Adicionar créditos à assinatura Azure via Open

Se você tiver uma assinatura Azure no Licenciamento Open, poderá adicionar créditos à sua assinatura no portal do Azure, resgatando uma chave do produto ou comprando créditos com um cartão de crédito.

1. Entre no Portal do Azure como Administrador da Conta.
1. Pesquise **Gerenciamento de Custos + Cobrança**.

    ![Captura de tela que mostra a pesquisa de gerenciamento de custos + cobrança ](./media/account-admin-tasks/search-bar.png)

1. Na lista **Minha assinaturas**, selecione sua assinatura Azure via Open.

    ![A captura de tela mostra a área Minhas assinaturas em que você pode selecionar a assinatura Azure via Open.](./media/account-admin-tasks/cost-management-overview-aio-x.png)

   > [!NOTE]
   > Caso não veja sua assinatura aqui, pode ser porque você alterou o diretório em algum momento. É necessário alternar o diretório da assinatura para o diretório original (o diretório no qual você se inscreveu inicialmente). Em seguida, repita a etapa 2.

1. Selecione **Histórico de crédito**.

    ![Captura de tela que mostra o histórico de crédito](./media/account-admin-tasks/aio-credit-history-blade.png)

1. No canto superior esquerdo, selecione "+" para adicionar mais crédito.

    ![Captura de tela que mostra o botão adicionar créditos](./media/account-admin-tasks/aio-credit-history-plus.png)

1. Selecione um tipo de método de pagamento na lista suspensa. Você pode adicionar uma chave do produto ou comprar créditos com um cartão de crédito.

    ![Captura de tela da lista suspensa do método de pagamento na folha adicionar créditos](./media/account-admin-tasks/add-credits-select-payment-method.png)

1. Se você escolheu chave do produto:
    - Digite a chave do produto
    - Clique em **Validar**

1. Se você escolheu cartão de crédito:
    - Clique **Selecionar método de pagamento** para adicionar um cartão de crédito ou selecionar um existente.
    - Especifique a quantidade de créditos que deseja adicionar.

1. Clique em **Aplicar**

## <a name="troubleshooting"></a>solução de problemas
Não damos suporte a cartões virtuais ou pré-pagos. Se estiver recebendo erros ao adicionar ou atualizar um cartão de crédito válido, tente abrir o navegador no modo privado.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como [analisar encargos inesperados](../understand/analyze-unexpected-charges.md)
