---
title: Alterar seu cartão de crédito para o Azure
description: Descreve como alterar o cartão de crédito usado para pagar uma assinatura do Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/20/2020
ms.author: banders
ms.openlocfilehash: fbb69a4449c32f85cc4be438645b654608aa7489
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026549"
---
# <a name="add-or-update-a-credit-card-for-azure"></a>Adicionar ou atualizar um cartão de crédito para o Azure

Este documento aplica-se a clientes que se inscreveram no Azure online com um cartão de crédito.

No portal do Azure, você pode alterar a forma de pagamento padrão para um novo cartão de crédito e atualizar os detalhes do cartão de crédito. Você precisa ser [Administrador da Conta](../understand/subscription-transfer.md#whoisaa) ou ter as [permissões MCA](understand-mca-roles.md) corretas para fazer essas alterações.

Se você quiser excluir um cartão de crédito, confira [Excluir uma forma de pagamento de cobrança do Azure](delete-azure-payment-method.md).

Os métodos de pagamento com suporte para Microsoft Azure são cartões de crédito e cheque/transferência eletrônica. Para ser aprovado para pagar por cheque/transferência eletrônica, confira [Pagar assinaturas do Azure por fatura](pay-by-invoice.md).

Com um Contrato de Cliente da Microsoft, as formas de pagamento serão associadas aos perfis de cobrança. Saiba como [verificar o acesso ao Contrato de Cliente da Microsoft](#check-the-type-of-your-account). Se você tiver um MCA, pule para [gerenciar cartões de crédito para um Contrato de Cliente da Microsoft](#manage-credit-cards-for-a-microsoft-customer-agreement).

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>Gerenciar cartões de crédito de uma assinatura do Azure

As seções a seguir se aplicam a clientes que têm uma conta de cobrança do programa Microsoft Online Services. Saiba como [verificar o tipo de sua conta de cobrança](#check-the-type-of-your-account). Se o tipo de conta de cobrança for o programa Microsoft Online Services, os métodos de pagamento serão associados a assinaturas individuais do Azure. Se você receber um erro depois de adicionar o cartão de crédito, consulte [Cartão de crédito recusado na inscrição do Azure](./troubleshoot-declined-card.md).

### <a name="change-credit-card-for-a-subscription-by-adding-a-new-credit-card"></a>Alterar o cartão de crédito de uma assinatura adicionando um novo cartão de crédito

Você pode alterar o crédito padrão da sua assinatura do Azure para um novo cartão de crédito ou um cartão de crédito salvo anteriormente no portal do Azure. Você precisa ser administrador da conta para alterar o cartão de crédito. Se várias assinaturas tiverem a mesma forma de pagamento ativa, a alteração dessa forma de pagamento ativa em qualquer uma dessas assinaturas também atualizará a das demais.

Use estas etapas para alterar o cartão de crédito padrão da sua assinatura para um novo:

1. Entre no [Portal do Azure](https://portal.azure.com) como Administrador da Conta.
1. Pesquise **Gerenciamento de Custos + Cobrança**.  
    ![Captura de tela que mostra a pesquisa](./media/change-credit-card/search.png)
1. Selecione a assinatura à qual você deseja adicionar o cartão de crédito.
1. Selecione **Formas de pagamento**.  
    ![Captura de tela que mostra a opção Gerenciar formas de pagamento selecionada](./media/change-credit-card/payment-methods-blade-x.png)
1. No canto superior esquerdo, selecione "+" para adicionar um cartão. Um formulário de cartão de crédito será exibido à direita.
1. Insira os detalhes do cartão de crédito.  
    ![Captura de tela que mostra como adicionar um novo cartão](./media/change-credit-card/sub-add-new-x.png)
1. Para tornar este cartão sua forma de pagamento ativa, marque a caixa ao lado de **Tornar esta minha forma de pagamento ativa** acima do formulário. Esse cartão se tornará o meio de pagamento ativo para todas as assinaturas usando o mesmo cartão, como a assinatura selecionada.
1. Selecione **Avançar**.

### <a name="change-credit-card-for-a-subscription-to-a-previously-saved-credit-card"></a>Alterar o cartão de crédito de uma assinatura para um cartão de crédito salvo anteriormente

Também é possível executar estas etapas para alterar o cartão de crédito padrão da sua assinatura para um que já esteja salvo em sua conta:

1. Entre no [Portal do Azure](https://portal.azure.com) como Administrador da Conta.
1. Pesquise **Gerenciamento de Custos + Cobrança**.  
    ![Captura de tela que mostra a pesquisa](./media/change-credit-card/search.png)
1. Selecione a assinatura à qual você deseja adicionar o cartão de crédito.
1. Selecione **Formas de pagamento**.
    ![Captura de tela que mostra a opção Gerenciar formas de pagamento selecionada](./media/change-credit-card/payment-methods-blade-x.png)
1. Marque a caixa ao lado do cartão que você gostaria de tornar a forma de pagamento ativa.
1. Selecione **Definir como ativo**.
    ![Captura de tela que mostra o cartão de crédito selecionado e definido como ativo](./media/change-credit-card/sub-change-active-x.png)

### <a name="edit-credit-card-details"></a>Editar os detalhes do cartão de crédito

Caso seu cartão de crédito seja renovado e o número permaneça o mesmo, atualize os detalhes do cartão de crédito existente, como a data de vencimento. Se o número de seu cartão de crédito for alterado devido a perda, roubo ou vencimento do cartão, siga as etapas da seção [Adicionar um cartão de crédito como uma forma de pagamento](#addcard). Você não precisa atualizar o CVV.

1. Entre no [Portal do Azure](https://portal.azure.com) como Administrador da Conta.
1. Pesquise **Gerenciamento de Custos + Cobrança**.
    ![Captura de tela que mostra a pesquisa](./media/change-credit-card/search.png)
1. Selecione **Formas de pagamento**.
    ![Captura de tela que mostra a opção Gerenciar formas de pagamento selecionada](./media/change-credit-card/payment-methods-blade-x.png)
1. Selecione o cartão de crédito que você deseja editar. Um formulário de cartão de crédito será exibido à direita.
    ![Captura de tela que mostra o cartão de crédito selecionado](./media/change-credit-card/edit-card-x.png)
1. Atualize os detalhes do cartão de crédito.
1. Clique em **Salvar**.

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>Gerenciar cartões de crédito para um Contrato de Cliente da Microsoft

As seções a seguir se aplicam a clientes que têm um Contrato de Cliente da Microsoft e se inscreveram no Azure Online com um cartão de crédito e àqueles que têm as [permissões MCA](understand-mca-roles.md) corretas. [Saiba como verificar se você tem um Contrato de Cliente da Microsoft](#check-the-type-of-your-account).

### <a name="change-default-credit-card"></a>Alterar o cartão de crédito padrão

Se você tiver um Contrato de Cliente da Microsoft, seu cartão de crédito será associado a um perfil de cobrança. Para alterar a forma de pagamento de um perfil de cobrança, você deve ser a pessoa que se inscreveu no Azure e criou a conta de cobrança ou você precisa ter as [permissões MCA](understand-mca-roles.md) corretas.

Se você quiser alterar o método de pagamento padrão do perfil de cobrança para cheque/transferência eletrônica, confira [Pagar assinaturas do Azure por fatura](pay-by-invoice.md).

Para alterar seu cartão de crédito, execute estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.
1. No menu à esquerda, selecione **Perfis de cobrança**.
1. Selecione um perfil de cobrança.
1. No menu à esquerda, selecione **Formas de pagamento**.  
   ![Captura de tela que mostra as formas de pagamento no menu](./media/change-credit-card/payment-methods-tab-mca.png)
1. Na seção **Forma de pagamento padrão**, selecione **Substituir**.  
    :::image type="content" source="./media/change-credit-card/change-payment-method-mca.png" alt-text="Captura de tela que mostra a opção de substituição" :::
1. Na nova área à direita, selecione um cartão existente na lista suspensa ou adicione um novo selecionando o link azul **Adicionar nova forma de pagamento**.

### <a name="edit-a-credit-card"></a>Editar um cartão de crédito

É possível editar os detalhes do cartão de crédito (como atualizar a data de validade) no portal do Azure. 

Para editar um cartão de crédito, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.
1. No menu à esquerda, selecione **Perfis de cobrança**.
1. Selecione um perfil de cobrança.
1. No menu à esquerda, selecione **Formas de pagamento**.  
   ![Captura de tela que mostra as formas de pagamento no menu](./media/change-credit-card/payment-methods-tab-mca.png)
1. Na seção **Seus cartões de crédito**, encontre o cartão de crédito que você quer editar.
1. Selecione as reticências (`...`) no final da linha.  
    :::image type="content" source="./media/change-credit-card/edit-delete-credit-card-mca.png" alt-text="Captura de tela que mostra as reticências" :::
1. Para editar os detalhes do seu cartão de crédito, selecione **Editar** no menu de contexto.

## <a name="troubleshooting"></a>Solução de problemas

O Azure não dá suporte a cartões virtuais ou pré-pagos. Se estiver recebendo erros ao adicionar ou atualizar um cartão de crédito válido, tente abrir o navegador no modo privado.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

As seções a seguir respondem às perguntas mais frequentes sobre como alterar as informações do seu cartão de crédito.

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Por que continuo recebendo "Sua sessão de logon expirou. Clique aqui para fazer logon novamente"?

Se você continuar recebendo essa mensagem de erro mesmo que já tenha feito o logout e voltado, tente novamente com uma sessão de navegação privada.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Como usar um cartão diferente para cada assinatura que tenho?

Infelizmente, se suas assinaturas já estiverem usando o mesmo cartão, não será possível usar cartões diferentes. No entanto, ao se inscrever para uma nova assinatura, você pode escolher usar uma nova forma de pagamento para essa assinatura.

### <a name="how-do-i-make-payments"></a>Como faço pagamentos?

Se você tiver configurado um cartão de crédito como forma de pagamento, faremos a cobrança no cartão automaticamente após o período de cobrança. Você não precisa fazer nada.

Se estiver [pagando por fatura](pay-by-invoice.md), envie seu pagamento para o local listado na parte inferior da fatura.

### <a name="how-do-i-change-the-tax-id"></a>Como alterar a ID de imposto?

Para adicionar ou atualizar o TID, atualize seu perfil no [portal do Azure](https://portal.azure.com) e selecione **Registro fiscal**. Essa ID de imposto é usada para cálculos de isenção de impostos e aparece em sua fatura.

## <a name="check-the-type-of-your-account"></a>Verifique o tipo de sua conta

[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [reservas do Azure](../reservations/save-compute-costs-reservations.md) para ver se elas podem fazer você economizar dinheiro.
- Se você quiser excluir um cartão de crédito, confira [Excluir uma forma de pagamento de cobrança do Azure](delete-azure-payment-method.md).