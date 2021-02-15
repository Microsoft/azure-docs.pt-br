---
title: Obter a propriedade de cobrança das assinaturas do Azure para o MPA (Contrato de Parceiro da Microsoft)
description: Saiba como solicitar a propriedade da cobrança de assinaturas do Azure de outros usuários para o MPA (Contrato de Parceiro da Microsoft).
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/19/2020
ms.author: banders
ms.openlocfilehash: aaa94f66f1b0441e025013369b3b674d799d4847
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203255"
---
# <a name="get-billing-ownership-of-azure-subscriptions-to-your-mpa-account"></a>Obter a propriedade de cobrança das assinaturas do Azure para sua conta do MPA

A fim de fornecer uma só fatura combinada para os serviços gerenciados e o consumo do Azure, o CSP (Provedor de Soluções na Nuvem) pode assumir a propriedade da cobrança das assinaturas do Azure dos clientes dele com o EA (Contrato Enterprise) Direto.

Esse recurso só está disponível para Parceiros de Cobrança Direta do CSP certificados como [MSP Especialista do Azure](https://partner.microsoft.com/membership/azure-expert-msp). Ele está sujeito à governança e às políticas da Microsoft e pode exigir análise e aprovação para determinados clientes.

Para solicitar a propriedade de cobrança, é necessário ter a função **Administrador Global** ou **Agentes Administrativos** . Para saber mais, confira [Partner Center – Atribuir funções e permissões de usuários](/partner-center/permissions-overview).

Este artigo se aplica às contas de cobrança para Contratos de Parceiro da Microsoft. Essas contas são criadas para CSPs (Provedores de Soluções na Nuvem) para gerenciar a cobrança dos clientes na nova experiência de comércio. A nova experiência só está disponível para parceiros que têm, pelo menos, um cliente que tenha aceitado um MCA (Contrato de Cliente da Microsoft) e tenha um plano do Azure. [Verifique se você tem acesso a um Contrato de Parceiro da Microsoft](#check-access-to-a-microsoft-partner-agreement).

## <a name="prerequisites"></a>Pré-requisitos

1. Estabelecer um [relacionamento de revendedor](/partner-center/request-a-relationship-with-a-customer) com um cliente. Verificar a [Visão Geral da Autorização Regional do CSP](/partner-center/regional-authorization-overview) para garantir que o cliente e o locatário do Parceiro estejam dentro das mesmas regiões autorizadas.
1. [Confirmar a aceitação pelo cliente do Contrato de Cliente da Microsoft](/partner-center/confirm-customer-agreement).
1. Configurar um [plano do Azure](/partner-center/purchase-azure-plan) para o cliente. Se o cliente estiver fazendo a compra por meio de vários revendedores, você precisará configurar um plano do Azure para cada combinação de cliente e revendedor.

## <a name="request-billing-ownership"></a>Solicitar propriedade de cobrança

1. Entre no [portal do Azure](https://portal.azure.com) usando as credenciais de Agente Administrativo do CSP no locatário do CSP.
1. Pesquise **Gerenciamento de Custos + Cobrança** .  
    ![Captura de tela que mostra a pesquisa no portal do Azure de gerenciamento de custos e cobrança para solicitar a propriedade da cobrança.](./media/mpa-request-ownership/search-cmb.png)
1. Selecione **Clientes** do lado esquerdo e, em seguida, selecione um cliente na lista.  
    [![Captura de tela que mostra a seleção de clientes](./media/mpa-request-ownership/mpa-select-customers.png)](./media/mpa-request-ownership/mpa-select-customers.png#lightbox)
1. Selecione **Solicitações de transferência** no lado inferior esquerdo e, em seguida, selecione **Adicionar uma nova solicitação** .  
    [![Captura de tela que mostra a seleção de solicitações de transferência](./media/mpa-request-ownership/mpa-select-transfer-requests.png)](./media/mpa-request-ownership/mpa-select-transfer-requests.png#lightbox)
1. Insira o endereço de email do usuário na organização do cliente que aceitará a solicitação de transferência. O usuário precisa ser um proprietário da conta em um Contrato Enterprise. Selecione **Enviar solicitação de transferência** .  
    [![Captura de tela que mostra o envio de uma solicitação de transferência](./media/mpa-request-ownership/mpa-send-transfer-requests.png)](./media/mpa-request-ownership/mpa-send-transfer-requests.png#lightbox)
1. O usuário recebe um email com instruções para revisar sua solicitação de transferência.  
    ![Captura de tela que mostra o email de análise da solicitação de transferência](./media/mpa-request-ownership/mpa-review-transfer-request-email.png)
1. Para aprovar a solicitação de transferência, o usuário seleciona o link no email e segue as instruções.  
    [![Captura de tela que mostra a revisão da solicitação de transferência](./media/mpa-request-ownership/review-transfer-requests.png)](./media/mpa-request-ownership/review-transfer-requests.png#lightbox) O usuário pode selecionar a conta de cobrança da qual deseja transferir produtos do Azure. Depois de selecionados, os produtos qualificados que podem ser transferidos são mostrados. **Observação:** assinaturas desabilitadas não podem ser transferidas e aparecerão na lista "Produtos não transferíveis do Azure", se aplicável. Depois que os produtos do Azure a serem transferidos estiverem selecionados, selecione **Validar** .
1. A área **Resultado da validação da transferência** mostrará o impacto dos produtos do Azure que serão transferidos. Estes são os estados possíveis:
    * **Aprovado** – a validação deste produto do Azure foi aprovada e pode ser transferida.
    * **Aviso** – há um aviso relacionado ao produto do Azure selecionado. Embora o produto ainda possa ser transferido, fazer isso causará algum impacto do qual o usuário precisa estar ciente caso queira adotar medidas de mitigação. Por exemplo, a assinatura do Azure que está sendo transferida é beneficiada por uma RI. Após a transferência, a assinatura não terá mais esse benefício. Para maximizar a economia, verifique se a RI está associada a outra assinatura que pode aproveitar os benefícios. Em vez disso, o usuário também pode optar por voltar à página de seleção e desmarcar essa assinatura do Azure.
    * **Falha** – o produto do Azure selecionado não pode ser transferido devido a um erro. O usuário precisará voltar à página de seleção e desmarcar este produto para transferir outros produtos do Azure selecionados.  
    ![Captura de tela que mostra a experiência de validação](./media/mpa-request-ownership/validate-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Verifique o status da solicitação de transferência

1. Entre no [portal do Azure](https://portal.azure.com).
1. Pesquise **Gerenciamento de Custos + Cobrança** .  
    ![Captura de tela que mostra a pesquisa no portal do Azure de gerenciamento de custos e cobrança para solicitar o status de transferência.](./media/mpa-request-ownership/billing-search-cost-management-billing.png)
1. Selecione **Clientes** no lado esquerdo.  
    [![Captura de tela que mostra a seleção de clientes](./media/mpa-request-ownership/mpa-select-customers.png)](./media/mpa-request-ownership/mpa-select-customers.png#lightbox)
1. Selecione o cliente na lista para a qual você enviou a solicitação de transferência.
1. Selecione **Solicitações de transferência** no lado inferior esquerdo. A página Solicitações de transferência exibe as seguintes informações: [![Captura de tela que mostra uma lista de solicitações de transferência](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png)](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png#lightbox)

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
   |Processando|O usuário aprovou a solicitação de transferência. A cobrança das assinaturas que o usuário selecionou está sendo transferida para a sua conta|
   |Concluído| A cobrança das assinaturas selecionadas pelo usuário é transferida para a sua conta|
   |Concluído com erros|A solicitação foi concluída, mas a cobrança de algumas assinaturas que o usuário selecionou não pôde ser transferida|
   |Expirado|O usuário não aceitou a solicitação a tempo e ela expirou|
   |Canceled|Alguém com acesso à solicitação de transferência cancelou a solicitação|
   |Recusado|O usuário recusou a solicitação de transferência|

1. Selecione uma solicitação de transferência para exibir detalhes. A página de detalhes da transferência exibe as seguintes informações: [![Captura de tela que mostra a lista de assinaturas transferidas](./media/mpa-request-ownership/mpa-transfer-completed.png)](./media/mpa-request-ownership/mpa-transfer-completed.png#lightbox)

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

* [Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)\*
* [Contrato Enterprise da Microsoft](https://azure.microsoft.com/pricing/enterprise-agreement/)

\* Você precisa converter uma assinatura de Desenvolvimento/Teste em uma oferta do EA Enterprise por meio de um tíquete de suporte. Uma assinatura de Desenvolvimento/Teste Enterprise será cobrada com base em uma taxa paga conforme o uso após a transferência. Qualquer desconto oferecido por meio da oferta de Desenvolvimento/Teste Enterprise por meio do EA do cliente não ficará disponível para o parceiro CSP.

## <a name="additional-information"></a>Informações adicionais

A seção a seguir fornece mais informações sobre como transferir assinaturas.

### <a name="no-service-downtime"></a>Não há tempo de inatividade do serviço

Os serviços do Azure na assinatura continuam sendo executados sem nenhuma interrupção. Apenas fazemos a transição do relacionamento de cobrança das assinaturas do Azure que o usuário selecione para transferir.

### <a name="disabled-subscriptions"></a>Assinaturas desabilitadas

As assinaturas desabilitadas não podem ser transferidas. As assinaturas devem estar no estado ativo para transferir a propriedade de cobrança.

### <a name="azure-resources-transfer"></a>Transferência de recursos do Azure

Todos os recursos de assinaturas como VMs, discos e sites são transferidos. Quando transferidos, as IDs da assinatura e de recurso são preservadas. 

### <a name="azure-marketplace-products-transfer"></a>Transferência de produtos do Azure Marketplace

Os produtos do Azure Marketplace disponíveis para assinaturas gerenciadas por CSPs (Provedores de Soluções na Nuvem) são transferidos juntamente com as respectivas assinaturas. As assinaturas que têm produtos do Azure Marketplace não habilitados para CSPs não podem ser transferidas.

### <a name="azure-reservations-transfer"></a>Transferência de Reservas do Azure

As Reservas do Azure não são movidas automaticamente com as assinaturas. Você pode manter a Reserva em EA para outras assinaturas ou [cancelar a reserva](../reservations/exchange-and-refund-azure-reservations.md), que pode então ser comprada novamente por um parceiro no CSP.

### <a name="access-to-azure-services"></a>Acesso aos serviços do Azure

O acesso de usuários, grupos ou entidades de serviço existentes que foi atribuído usando o [Azure RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md) não é afetado durante a transição. O parceiro não receberá nenhum acesso novo do Azure RBAC às assinaturas.

Os parceiros devem trabalhar com o cliente para ter acesso às assinaturas. Os parceiros precisam obter tíquetes de suporte aberto de acesso de [Administrador em Nome de – AOBO](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) ou [Azure Lighthouse](../../lighthouse/concepts/cloud-solution-provider.md).

### <a name="azure-support-plan"></a>Plano de Suporte do Azure

O Suporte do Azure não é transferido com as assinaturas. Se o usuário transferir todas as assinaturas do Azure, peça que ele cancele o plano de suporte. Após a transferência, o parceiro CSP é responsável pelo suporte. O cliente deve trabalhar com o parceiro CSP em caso de qualquer solicitação de suporte.  

### <a name="charges-for-transferred-subscription"></a>Encargos para assinatura transferida

O proprietário de cobrança original das assinaturas é responsável por encargos que foram relatados até o momento em que a transferência foi concluída. Você é responsável pelos encargos relatados a partir do momento da transferência. Pode haver alguns encargos cobrados antes da transferência, mas que foram relatados posteriormente. Esses encargos são exibidos em sua fatura.

### <a name="cancel-a-transfer-request"></a>Cancelar uma solicitação de transferência

É possível cancelar a solicitação de transferência até que a solicitação seja aprovada ou recusada. Para cancelar a solicitação de transferência, acesse a [página de detalhes de transferência](#check-the-transfer-request-status) e selecione cancelar na parte inferior da página.

### <a name="software-as-a-service-saas-transfer"></a>Transferência SaaS (software como serviço)

Os produtos SaaS não são transferidos com as assinaturas. Peça ao usuário para [Entrar em contato com o Suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para transferir a propriedade de cobrança de produtos SaaS. Junto com a propriedade de cobrança, o usuário também pode transferir a propriedade do recurso. A propriedade do recurso permite que você realize operações de gerenciamento, como excluir e ver os detalhes do produto. O usuário deve ser um proprietário de recurso no produto SaaS para transferir a propriedade do recurso.

### <a name="additional-approval-for-certain-customers"></a>Aprovação adicional para determinados clientes

Algumas das solicitações de transição do cliente podem exigir um processo de análise adicional com a Microsoft devido à natureza da estrutura do registro Enterprise atual do cliente. O parceiro será notificado desses requisitos ao tentar enviar um convite aos clientes. Os parceiros são solicitados a trabalhar com o Gerente de Desenvolvimento do Parceiro e a equipe de contas do cliente para concluir esse processo de análise.

### <a name="azure-subscription-directory"></a>Diretório da assinatura do Azure

O diretório das assinaturas do Azure que são transferidas precisa corresponder ao diretório do cliente que foi selecionado ao estabelecer o relacionamento do CSP.

Se esses dois diretórios não forem correspondentes, as assinaturas não poderão ser transferidas. Você precisa estabelecer um novo relacionamento de revendedor CSP com o cliente selecionando o diretório das assinaturas do Azure ou alterando o diretório das assinaturas do Azure para que correspondam ao diretório de relacionamento entre o CSP e o cliente. Para obter mais informações, confira [Associar uma assinatura existente ao Azure AD Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory).

### <a name="ea-subscription-in-the-non-organization-directory"></a>Assinatura do EA no diretório que não é da organização

As assinaturas do EA de diretórios que não são da organização podem ser transferidas, desde que o diretório tenha um relacionamento de revendedor com o CSP. Se o diretório não tiver uma relação de revendedor, você precisará verificar se tem o usuário da organização no diretório como um *administrador global* que possa aceitar o relacionamento com o parceiro. A parte do nome de domínio do nome de usuário precisa ser o nome de domínio padrão inicial "[nome de domínio]. onmicrosoft.com" ou um nome de domínio personalizado confirmado e não federado, como "contoso.com".  

Para adicionar um novo usuário ao diretório, confira [Guia de início rápido: Adicionar novos usuários ao Azure Active Directory para adicionar o novo usuário ao diretório](../../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Verificar o acesso a um Contrato de Parceiro da Microsoft

[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

* A propriedade de cobrança das assinaturas do Azure é transferida para você. Controle os encargos dessas assinaturas no [portal do Azure](https://portal.azure.com).
* Trabalhe com o cliente para obter acesso às assinaturas transferidas do Azure. [Adicionar ou remover atribuições de função do Azure usando o portal do Azure](../../role-based-access-control/role-assignments-portal.md).