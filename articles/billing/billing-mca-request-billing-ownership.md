---
title: Obter propriedade de assinaturas do Azure de cobrança
description: Saiba como solicitar a propriedade da cobrança de assinaturas do Azure de outros usuários.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 4c9a8c866a157757121e6a9dd07a0a8559937c5e
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490902"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Obter propriedade de assinaturas do Azure de outras contas de cobrança

Talvez você queira assumir a propriedade de assinaturas do Azure se o proprietário de cobrança existente é deixar sua organização ou deseja pagar para as assinaturas por meio de sua conta de cobrança. Apropriação transfere responsabilidades de cobrança de assinaturas para sua conta.

Este artigo se aplicam a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se você tem acesso a um contrato de cliente do Microsoft](#check-for-access).

Para solicitar a propriedade de cobrança, você deve ser um **proprietário da seção de nota fiscal** ou **colaborador da seção de nota fiscal**. Para obter mais informações, consulte [funções de tarefas da seção de nota fiscal](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>Solicitação de propriedade de cobrança

1. Entrar para o [portal do Azure](http://portal.azure.com) como fatura seção proprietário ou colaborador para uma conta de cobrança para o contrato de cliente da Microsoft.

2. Pesquise **custo de gerenciamento + cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure para gerenciamento de custos + cobrança](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Selecione **seções de nota fiscal** do lado esquerdo. Dependendo do seu acesso, você precisa selecionar uma conta de cobrança ou o perfil de cobrança. Na conta ou perfil de cobrança, selecione **seções de nota fiscal**.
   
   ![Captura de tela que mostra como selecionar seções de nota fiscal](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Selecione uma seção de nota fiscal na lista. Depois de assumir a propriedade de assinaturas, eles serão cobrados a esta seção da nota fiscal.

5. Selecione **solicitações de transferência** no lado inferior esquerdo e, em seguida, selecione **Add**.
 
   ![Captura de tela que mostra como selecionar solicitações de transferência](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. Insira o endereço de email do usuário que está solicitando a propriedade da cobrança do. O usuário deve ser um administrador da conta em uma conta de cobrança do programa de serviço Online da Microsoft ou um proprietário de conta em um Enterprise Agreement. Para obter mais informações, consulte [exibir suas contas de cobrança no portal do Azure](billing-view-all-accounts.md). Selecione **enviar solicitação de transferência**.

   ![Captura de tela que mostra a enviar uma solicitação de transferência](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. O usuário recebe um email com instruções para revisar a solicitação de transferência.

   ![Captura de tela desse email de solicitação de transferência de revisão mostra](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. Para aprovar a solicitação de transferência, o usuário seleciona o link no email e segue as instruções.

    ![Captura de tela desse email de solicitação de transferência de revisão mostra](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Verificar o status da solicitação de transferência

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise **custo de gerenciamento + cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure para gerenciamento de custos + cobrança](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. Selecione **seções de nota fiscal** do lado esquerdo. Dependendo do seu acesso, você precisa selecionar uma conta de cobrança ou o perfil de cobrança. Na conta ou perfil de cobrança, selecione **seções de nota fiscal**.
   
   ![Captura de tela que mostra como selecionar seções de nota fiscal](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Selecione a seção de nota fiscal na lista para o qual você enviou a solicitação de transferência.

5. Selecione **solicitações de transferência** do lado inferior esquerdo. A página de solicitações de transferência exibe as seguintes informações:

    ![Captura de tela que mostra a lista de solicitações de transferência](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Coluna|Definição|
   |---------|---------|
   |Data da solicitação|A data em que a solicitação de transferência foi enviada|
   |Destinatário|O endereço de email do usuário que você enviou a solicitação para transferir a propriedade da cobrança|
   |Data de validade|A data de vencimento da solicitação|
   |Status|O status da solicitação de transferência|

    A solicitação de transferência pode ter um dos seguintes status:

   |Status|Definição|
   |---------|---------|
   |Em andamento|O usuário não tiver aceitado a solicitação de transferência|
   |Processando|O usuário aprovar a solicitação de transferência. A cobrança para assinaturas que o usuário selecionou está sendo transferida para a seção de nota fiscal|
   |Concluído| A cobrança para assinaturas que o usuário selecionado é transferida para a seção de nota fiscal|
   |Concluído com erros|A solicitação foi concluída, mas a cobrança por algumas assinaturas que o usuário selecionado não pôde ser transferido|
   |Expirado|O usuário não aceitou a solicitação no tempo e expirado|
   |Cancelado|Alguém com acesso à solicitação de transferência cancelada a solicitação|
   |Recusado|O usuário recusou a solicitação de transferência|

7. Selecione uma solicitação de transferência para exibir detalhes. A página de detalhes da transferência exibe as seguintes informações:
   
   ![Captura de tela que mostra a lista de assinaturas transferidas](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |Coluna  |Definição|
   |---------|---------|
   |ID da solicitação de transferência|A ID exclusiva para a sua solicitação de transferência. Se você enviar uma solicitação de suporte, compartilhe a ID com suporte do Azure para acelerar a sua solicitação de suporte|
   |Transferência solicitada em|A data em que a solicitação de transferência foi enviada|
   |Transferência solicitada por|O endereço de email do usuário que enviou a solicitação de transferência|
   |Solicitação de transferência expira em| A data de expiração a solicitação de transferência|
   |Endereço de email do destinatário|O endereço de email do usuário que você enviou a solicitação para transferir a propriedade da cobrança|
   |Link de transferência enviada ao destinatário|A url que foi enviado para o usuário para revisar a solicitação de transferência|

## <a name="supported-subscription-types"></a>Tipos de assinatura com suporte

Você pode solicitar a propriedade dos tipos de assinatura de cobrança listados abaixo.

- [Pacote de ação](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure no Open Licensing](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Patrocínio do Azure Pass](https://azure.microsoft.com/offers/azure-pass/)\*
- [Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Desenvolvimento/Teste pré-pago](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Oferta patrocinada pelo Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Contrato Enterprise da Microsoft](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Plataformas MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Assinantes do Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Assinantes do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Assinantes do Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Assinantes do Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* Qualquer crédito disponível na assinatura não estarão disponível na nova conta após a transferência.

\*\* Só tem suporte para assinaturas em contas que são criadas durante a inscrição no site do Azure.


## <a name="additional-information"></a>Informações adicionais

A seção a seguir fornece informações adicionais sobre como transferir as assinaturas.

### <a name="no-service-downtime"></a>Não há tempo de inatividade do serviço

Os serviços do Azure na assinatura a ser executados sem nenhuma interrupção. Vamos apenas fazer a transição a relação de cobrança para as assinaturas do Azure que o usuário seleciona para transferir.

### <a name="disabled-subscriptions"></a>Assinaturas desabilitadas

Não possível transferir assinaturas desabilitadas. Assinaturas devem estar no estado ativo para transferir a sua propriedade de cobrança.

### <a name="azure-resources-transfer"></a>Transferência de recursos do Azure

Todos os recursos das assinaturas, como VMs, discos e sites são transferidos.

### <a name="azure-marketplace-products-transfer"></a>Transferência de produtos do Azure Marketplace

Transferência de produtos do Marketplace do Azure, juntamente com suas respectivas assinaturas.

### <a name="azure-reservations-transfer"></a>Transferência de reservas do Azure

Reservas do Azure não movem automaticamente com as assinaturas. [Entre em contato com o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para mover as reservas.

### <a name="access-to-azure-services"></a>Acesso aos serviços do Azure

Acesso para usuários existentes, grupos ou entidades de serviço que foi atribuído por (RBAC (controle de acesso baseado em função) do Azure) [... /Role-Based-Access-Control/Overview.MD] não é afetada durante a transição.

### <a name="azure-support-plan"></a>Plano de suporte do Azure

Suporte do Azure não transfere com as assinaturas. Se o usuário transferir todas as assinaturas do Azure, peça-lhe para cancelar seu plano de suporte.

### <a name="charges-for-transferred-subscription"></a>Encargos de assinatura transferido

O proprietário de cobrança original das assinaturas é responsável por quaisquer encargos relatados até o ponto que a transferência for concluída. A seção de nota fiscal é responsável por encargos relatados a partir do momento da transferência em diante. Pode haver alguns encargos que ocorreram antes da transferência, mas foi relatados posteriormente. Esses encargos aparecem na seção de nota fiscal.

### <a name="cancel-a-transfer-request"></a>Cancelar uma solicitação de transferência

Você pode cancelar a solicitação de transferência até que a solicitação é aprovada ou recusada. Para cancelar a solicitação de transferência, vá para o [página de detalhes da transferência](#check-the-transfer-request-status) e selecione Cancelar na parte inferior da página.

### <a name="software-as-a-service-saas-transfer"></a>Software como uma transferência de serviço (SaaS)

Produtos de SaaS não são transferidas com as assinaturas. Solicitar que o usuário [suporte do Azure de contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para transferir a propriedade de cobrança de produtos de SaaS. Junto com a propriedade de cobrança, o usuário também pode transferir a propriedade de recurso. Propriedade de recursos permite que você execute operações de gerenciamento, como a exclusão e exibindo os detalhes do produto. Usuário deve ser o proprietário do recurso sobre o produto de SaaS para transferir a propriedade de recurso.

## <a name="check-for-access"></a>Verificação de acesso
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

- A propriedade de cobrança das assinaturas do Azure é transferida para a seção de nota fiscal. Manter o controle dos encargos para essas assinaturas na [portal do Azure](https://portal.azure.com).
- Conceda permissões para exibir e gerenciar a cobrança por essas assinaturas a outros. Para obter mais informações, consulte [seção funções e tarefas de nota fiscal](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).
