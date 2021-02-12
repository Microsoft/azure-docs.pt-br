---
title: Obter propriedade de cobrança das assinaturas do Azure
description: Saiba como solicitar propriedade de cobrança de assinaturas do Azure de outros usuários.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 12/09/2020
ms.author: banders
ms.openlocfilehash: ca55773b2deb74ea0647ed33df4040065762f94a
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938470"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Obter propriedade de cobrança das assinaturas do Azure de outras contas

Talvez você queira assumir a propriedade das assinaturas do Azure se o proprietário da cobrança existente sair da sua organização ou quando desejar pagar pelas assinaturas por meio de sua conta de cobrança. Assumir a propriedade transfere as responsabilidades de cobrança da assinatura para sua conta.

Este artigo aplica-se a uma conta de cobrança para um Contrato de Cliente da Microsoft. [Verifique se você tem acesso a um Contrato de Cliente da Microsoft](#check-for-access).

Para solicitar a propriedade de cobrança, você deve ser o **proprietário da seção da fatura** ou **colaborador da seção da fatura**. Para saber mais, confira [tarefas de funções da seção da fatura](understand-mca-roles.md#invoice-section-roles-and-tasks).

A pessoa de quem você está solicitando a propriedade da cobrança precisa ter uma das seguintes funções:

- Para um Contrato Enterprise de Cliente da Microsoft, o usuário é um Proprietário de Cobrança.
- Para um Contrato Enterprise, o usuário precisa ser um Proprietário da Conta.
- Para uma conta de cobrança do Programa Microsoft Online Services, o usuário precisa ser um Administrador da Conta.

Para obter mais informações, confira [exibir suas contas de cobrança no portal do Azure](view-all-accounts.md).

## <a name="request-billing-ownership"></a>Solicitar propriedade de cobrança

1. Entre no [portal do Azure](https://portal.azure.com) como proprietário ou colaborador da seção da fatura para uma conta de cobrança do Contrato de Cliente da Microsoft.
1. Pesquise **Gerenciamento de Custos + Cobrança**.  
    ![Captura de tela que mostra a pesquisa do portal do Azure para gerenciamento de custos + cobrança](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)
1. Na página de escopos do orçamento, selecione **Escopos do orçamento** e a conta de cobrança, que será usada no pagamento do uso das assinaturas. A conta de cobrança deve ser do tipo **Contrato de Cliente da Microsoft**.  
    [![Captura de tela que mostra a pesquisa de gerenciamento de custos + cobrança no portal](./media/mca-request-billing-ownership/list-of-scopes.png)](./media/mca-request-billing-ownership/list-of-scopes.png#lightbox)
    > [!NOTE]
    > O portal do Azure lembra o último escopo do orçamento que você acessou e exibirá o escopo na próxima vez que você visitar a página Gerenciamento de Custos + Cobrança. Você não verá a página de escopos do orçamento se tiver visitado a página de Gerenciamento de Custos + Cobrança anteriormente. Nesse caso, verifique se você está no [escopo certo](#check-for-access). Se não estiver, [alterne o escopo](view-all-accounts.md#switch-billing-scope-in-the-azure-portal) para selecionar a conta de cobrança para um Contrato de Cliente da Microsoft.
1. Selecione **Perfis de cobrança** do lado esquerdo.  
    [![Captura de tela que mostra a seleção de perfis de cobrança](./media/mca-request-billing-ownership/mca-select-profiles.png)](./media/mca-request-billing-ownership/mca-select-profiles.png#lightbox)
    > [!Note]
    > Se você não vê nenhum perfil de cobrança, significa que você não está no escopo do orçamento correto. Você precisa selecionar uma conta de cobrança para um Contrato de Cliente da Microsoft e, em seguida, selecionar Perfis de cobrança. Para saber como alterar os escopos, confira [Alternar os escopos do orçamento no portal do Azure](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).
1. Selecione um **Perfil de cobrança** na lista. Depois de assumir a propriedade das assinaturas, elas o uso delas será cobrado nesse perfil de cobrança.
1. Selecione **Seções da fatura** do lado esquerdo.  
    [![Captura de tela que mostra a seleção das seções da fatura](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)](./media/mca-request-billing-ownership/mca-select-invoice-sections.png#lightbox)   
1. Selecione uma seção da fatura na lista. Depois de assumir a propriedade das assinaturas, elas o uso delas será atribuído para essa seção na fatura do perfil de cobrança.
1. Selecione **Solicitações de transferência** no lado inferior esquerdo e, em seguida, selecione **Adicionar uma nova solicitação**.  
    [![Captura de tela que mostra a seleção de solicitações de transferência](./media/mca-request-billing-ownership/mca-select-transfer-requests.png)](./media/mca-request-billing-ownership/mca-select-transfer-requests.png#lightbox)
1. Insira o endereço de email do usuário do qual você está solicitando propriedade de cobrança. Selecione **Enviar solicitação de transferência**.  
    [![Captura de tela que mostra o envio de uma solicitação de transferência](./media/mca-request-billing-ownership/mca-send-transfer-requests.png)](./media/mca-request-billing-ownership/mca-send-transfer-requests.png#lightbox)
1. O usuário recebe um email com instruções para revisar sua solicitação de transferência.  
    ![Captura de tela que mostra o email de análise da solicitação de transferência](./media/mca-request-billing-ownership/mca-review-transfer-request-email.png)
1. Para aprovar a solicitação de transferência, o usuário seleciona o link no email e segue as instruções.
    [![Captura de tela que mostra a revisão da solicitação de transferência](./media/mca-request-billing-ownership/review-transfer-requests.png)](./media/mca-request-billing-ownership/review-transfer-requests.png#lightbox) O usuário pode selecionar a conta de cobrança da qual deseja transferir produtos do Azure. Depois de selecionados, os produtos qualificados que podem ser transferidos são mostrados. **Observação:** assinaturas desabilitadas não podem ser transferidas e aparecerão na lista "Produtos não transferíveis do Azure", se aplicável. Depois que os produtos do Azure a serem transferidos estiverem selecionados, selecione **Validar**.
1. A área **Resultado da validação da transferência** mostrará o impacto dos produtos do Azure que serão transferidos. Estes são os estados possíveis:
    * **Aprovado** – a validação deste produto do Azure foi aprovada e pode ser transferida.
    * **Aviso** – há um aviso relacionado ao produto do Azure selecionado. Embora o produto ainda possa ser transferido, fazer isso causará algum impacto do qual o usuário precisa estar ciente caso queira adotar medidas de mitigação. Por exemplo, a assinatura do Azure que está sendo transferida é beneficiada por uma RI. Após a transferência, a assinatura não terá mais esse benefício. Para maximizar a economia, verifique se a RI está associada a outra assinatura que pode aproveitar os benefícios. Em vez disso, o usuário também pode optar por voltar à página de seleção e desmarcar essa assinatura do Azure.
    * **Falha** – o produto do Azure selecionado não pode ser transferido devido a um erro. O usuário precisará voltar à página de seleção e desmarcar este produto para transferir outros produtos do Azure selecionados.  
    ![Captura de tela que mostra a experiência de validação](./media/mca-request-billing-ownership/validate-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Verifique o status da solicitação de transferência

1. Entre no [portal do Azure](https://portal.azure.com).
1. Pesquise **Gerenciamento de Custos + Cobrança**.  
    ![Captura de tela que mostra a pesquisa do portal do Azure para gerenciamento de custos + cobrança](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)
1. Na página de escopos do orçamento, selecione a conta de cobrança para a qual a solicitação de transferência foi enviada.
1. Selecione **Perfis de cobrança** do lado esquerdo.  
    [![Captura de tela que mostra a seleção de perfis de cobrança](./media/mca-request-billing-ownership/mca-select-profiles.png)](./media/mca-request-billing-ownership/mca-select-profiles.png#lightbox)
1. Selecione o **perfil de cobrança** para o qual a solicitação de transferência foi enviada.
1. Selecione **Seções da fatura** do lado esquerdo.  
    [![Captura de tela que mostra a seleção das seções da fatura](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)](./media/mca-request-billing-ownership/mca-select-invoice-sections.png#lightbox)   
1. Selecione a seção da fatura na lista para a qual a solicitação de transferência foi enviada.
1. Selecione **Solicitações de transferência** no lado inferior esquerdo. A página Solicitações de transferência exibe as seguintes informações:  
    [![Captura de tela que mostra uma lista de solicitações de transferência](./media/mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)](./media/mca-request-billing-ownership/mca-select-transfer-requests-for-status.png#lightbox)

   |Coluna|Definição|
   |---------|---------|
   |Data da solicitação|A data em que a solicitação de transferência foi enviada|
   |Destinatário|O endereço de email do usuário para quem você enviou a solicitação de transferência da propriedade de cobrança|
   |Data de validade|A data em que a solicitação expira|
   |Status|O status da solicitação de transferência|

    A solicitação de transferência pode ter um dos seguinte status:

   |Status|Definição|
   |---------|---------|
   |Em andamento|O usuário não aceitou a solicitação de transferência|
   |Processando|O usuário aprovou a solicitação de transferência. A cobrança das assinaturas que o usuário selecionou está sendo transferida para a seção da fatura|
   |Concluído| A cobrança das assinaturas que o usuário selecionou foi transferida para a seção da fatura|
   |Concluído com erros|A solicitação foi concluída, mas a cobrança de algumas assinaturas que o usuário selecionou não pôde ser transferida|
   |Expirado|O usuário não aceitou a solicitação a tempo e ela expirou|
   |Canceled|Alguém com acesso à solicitação de transferência cancelou a solicitação|
   |Recusado|O usuário recusou a solicitação de transferência|

1. Selecione uma solicitação de transferência para exibir detalhes. A página de detalhes da transferência exibe as seguintes informações:  
    [![Captura de tela que mostra a lista de assinaturas transferidas](./media/mca-request-billing-ownership/mca-transfer-completed.png)](./media/mca-request-billing-ownership/mca-transfer-completed.png#lightbox)

   |Coluna  |Definição|
   |---------|---------|
   |ID da solicitação de transferência|A ID exclusiva de sua solicitação de transferência. Se você enviar uma solicitação de suporte, compartilhe a ID com o Suporte do Azure para agilizá-la|
   |Transferência solicitada em|A data em que a solicitação de transferência foi enviada|
   |Transferência solicitada por|O endereço de email do usuário que enviou a solicitação de transferência|
   |A solicitação de transferência expira em| A data em que a solicitação de transferência expira|
   |Endereço de email do destinatário|O endereço de email do usuário para quem você enviou a solicitação de transferência da propriedade de cobrança|
   |Link de transferência enviado para o destinatário|A URL que foi enviada para o usuário para examinar a solicitação de transferência|

## <a name="supported-subscription-types"></a>Tipos de assinatura com suporte

É possível solicitar a propriedade de cobrança dos tipos de assinatura listados abaixo.

- [Pacote de ações](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Azure no Licenciamento Open](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure Pass Sponsorship](https://azure.microsoft.com/offers/azure-pass/)\*
- [Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Avaliação Gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Desenvolvimento/Teste pré-pago](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Plano do Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Oferta patrocinada pelo Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Contrato Enterprise da Microsoft](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Contrato de Cliente da Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Plataformas MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Assinantes do Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Assinantes do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Assinantes do Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Assinantes do Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* Qualquer crédito disponível na assinatura não estará disponível na nova conta após a transferência.

\*\* Só há suporte para assinaturas em contas criadas durante a inscrição no site do Azure.

## <a name="additional-information"></a>Informações adicionais

A seção a seguir fornece mais informações sobre como transferir assinaturas.

### <a name="no-service-downtime"></a>Não há tempo de inatividade do serviço

Os serviços do Azure na assinatura continuam sendo executados sem nenhuma interrupção. Apenas fazemos a transição do relacionamento de cobrança das assinaturas do Azure que o usuário selecione para transferir.

### <a name="disabled-subscriptions"></a>Assinaturas desabilitadas

As assinaturas desabilitadas não podem ser transferidas. As assinaturas devem estar no estado ativo para transferir a propriedade de cobrança.

### <a name="azure-resources-transfer"></a>Transferência de recursos do Azure

Todos os recursos de assinaturas como VMs, discos e sites são transferidos.

### <a name="azure-marketplace-products-transfer"></a>Transferência de produtos do Azure Marketplace

Os produtos do Azure Marketplace são transferidos junto com as respectivas assinaturas.

### <a name="azure-reservations-transfer"></a>Transferência de Reservas do Azure

Se você estiver transferindo assinaturas do EA (Contrato Enterprise) ou Contratos de Cliente da Microsoft, as Reservas do Azure não serão automaticamente movidas com as assinaturas. [Entre em contato com o Suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para mover as Reservas.

### <a name="access-to-azure-services"></a>Acesso aos serviços do Azure

O acesso de usuários, grupos ou entidades de serviço existentes que foi atribuído usando o [Azure RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md) não é afetado durante a transição.

### <a name="azure-support-plan"></a>Plano de Suporte do Azure

O Suporte do Azure não é transferido com as assinaturas. Se o usuário transferir todas as assinaturas do Azure, peça que ele cancele o plano de suporte.

### <a name="charges-for-transferred-subscription"></a>Encargos para assinatura transferida

O proprietário de cobrança original das assinaturas é responsável por encargos que foram relatados até o momento em que a transferência foi concluída. A seção da fatura é responsável pelos encargos relatados a partir do momento da transferência. Pode haver alguns encargos cobrados antes da transferência, mas que foram relatados posteriormente. Esses encargos são exibidos na seção de sua fatura.

### <a name="cancel-a-transfer-request"></a>Cancelar uma solicitação de transferência

É possível cancelar a solicitação de transferência até que a solicitação seja aprovada ou recusada. Para cancelar a solicitação de transferência, acesse a [página de detalhes de transferência](#check-the-transfer-request-status) e selecione cancelar na parte inferior da página.

### <a name="software-as-a-service-saas-transfer"></a>Transferência SaaS (software como serviço)

Os produtos SaaS não são transferidos com as assinaturas. Peça ao usuário para [Entrar em contato com o Suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para transferir a propriedade de cobrança de produtos SaaS. Junto com a propriedade de cobrança, o usuário também pode transferir a propriedade do recurso. A propriedade do recurso permite que você conduza operações de gerenciamento, como excluir e exibir os detalhes do produto. O usuário deve ser um proprietário de recurso no produto SaaS para transferir a propriedade do recurso.

## <a name="check-for-access"></a>Verificar o acesso
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

- A propriedade da cobrança das assinaturas do Azure é transferida para a seção de sua fatura. Controle os encargos dessas assinaturas no [portal do Azure](https://portal.azure.com).
- Conceda a outros usuários permissões para exibir e gerenciar a cobrança para essas assinaturas. Para obter mais informações, confira [Funções e tarefas da seção da fatura](understand-mca-roles.md#invoice-section-roles-and-tasks).
