---
title: Salvar para o Serviço de Aplicativo do Azure com capacidade reservada
description: Saiba como você pode economizar dinheiro nas instâncias reservadas do Serviço de Aplicativo do Azure Premium v3 e na Tarifa de Selo Isolado.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 02/01/2021
ms.author: banders
ms.custom: references_regions
ms.openlocfilehash: 92a315121ad8ae6fadcadbf6d531eb3e99ae69a9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374534"
---
# <a name="save-costs-with-azure-app-service-reserved-instances"></a>Reduza os custos com as instâncias reservadas do Serviço de Aplicativo do Azure

Este artigo explica como você pode economizar dinheiro com as instâncias reservadas do Serviço de Aplicativo do Azure para as instâncias Premium v3 e as Tarifas de Selo Isolado.

## <a name="save-with-premium-v3-reserved-instances"></a>Economizar dinheiro com as instâncias reservadas Premium v3

Ao se comprometer com uma instância reservada do Serviço de Aplicativo do Azure Premium v3, você pode economizar dinheiro. O desconto da reserva é aplicado automaticamente ao número de instâncias em execução que correspondem ao escopo e aos atributos da reserva. Você não precisa atribuir uma reserva a uma instância para receber os descontos.

## <a name="determine-the-right-reserved-instance-size-before-you-buy"></a>Determinar o tamanho correto da instância reservada antes da compra

Antes de comprar uma reserva, você deve determinar o tamanho da instância reservada Premium v3 de que precisa. As seções a seguir ajudarão você a determinar o tamanho correto da instância reservada Premium v3.

### <a name="use-reservation-recommendations"></a>Usar as recomendações de reserva

Use as recomendações de reserva para ajudar a determinar as reservas que devem ser compradas.

- As recomendações de compra e a quantidade recomendada são mostradas quando você adquire uma instância reservada Premium v3 no portal do Azure.
- O Assistente do Azure fornece recomendações de compra para assinaturas individuais.
- Use as APIs para obter recomendações de compra para o escopo compartilhado e o escopo de assinatura única. Para obter mais informações, confira [APIs de recomendação de compra de instância reservada para clientes empresariais](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Para os clientes do EA (Contrato Enterprise) e do MCA (Contrato de Cliente da Microsoft), as recomendações de compra para os escopos de assinatura compartilhada e única estão disponíveis no [pacote de conteúdo do Azure Consumption Insights para Power BI](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="analyze-your-usage-information"></a>Analisar suas informações de uso

Analise suas informações de uso para ajudar a determinar quais reservas devem ser compradas. Os dados de uso estão disponíveis no arquivo de uso e nas APIs. Use-os juntos para determinar qual reserva deve ser comprada. Verifique as instâncias Premium v3 que têm alto uso diariamente para determinar a quantidade de reservas a serem compradas.

Seu arquivo de uso mostra os preços por período de cobrança e uso diário. Para obter informações sobre como baixar o arquivo de uso, confira [Ver e baixar o uso e os preços do Azure](../understand/download-azure-daily-usage.md). Em seguida, usando as informações do arquivo de uso, [determine a reserva a ser comprada](determine-reservation-purchase.md).


## <a name="buy-a-premium-v3-reserved-instance"></a>Comprar uma instância reservada Premium v3

Compre uma instância reservada Premium v3 no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Pague pela reserva [antecipadamente ou com pagamentos mensais](prepare-buy-reservation.md). Esses requisitos se aplicam à compra de uma instância reservada Premium v3:

- Você precisa ter a função Proprietário em, no mínimo, uma assinatura do EA ou uma assinatura com uma taxa paga conforme o uso.
- Para as assinaturas do EA, a opção **Adicionar Instâncias Reservadas** precisa estar habilitada no [Portal do EA](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você precisará ser um Administrador de EA da assinatura.
- Para o programa do CSP (Provedor de Solução na Nuvem) somente os agentes administradores ou agentes de vendas podem comprar reservas.

Para comprar uma instância:

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > **Reservas**.
3. Escolha **Adicionar** para comprar uma nova reserva e clique em **Instância**.
4. Preencha os campos obrigatórios. As instâncias reservadas Premium v3 em execução que correspondem aos atributos selecionados se qualificam para o desconto de reserva. O número real das instâncias reservadas Premium v3 que recebem o desconto depende do escopo e da quantidade selecionada.

Se você tiver um contrato EA, poderá usar a opção **Adicionar mais** para adicionar mais instâncias rapidamente. A opção não está disponível para outros tipos de assinaturas.

| **Campo** | **Descrição** |
|---|---|
| Subscription | A assinatura usada para pagar pela reserva. Os custos da reserva são cobrados segundo a forma de pagamento da assinatura. O tipo de assinatura deve ser um contrato empresarial (números da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Contrato de Cliente da Microsoft ou ainda uma assinatura individual com tarifas pagas conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Os preços são deduzidos do saldo do compromisso monetário, se disponível, ou são cobrados como excedente. Para uma assinatura com taxas pagas conforme o uso, as cobranças são feitas na forma de pagamento de cartão de crédito ou de fatura na assinatura. |
| Escopo | O escopo da reserva pode abranger uma ou várias assinaturas (escopo compartilhado). Se você selecionar: <ul><li>**Escopo de grupo de recursos único** — aplica o desconto de reserva apenas aos recursos correspondentes no grupo de recursos selecionado. </li><li>**Escopo de assinatura única** — aplica o desconto de reserva apenas aos recursos correspondentes na assinatura selecionada.</li><li>**Escopo compartilhado** — aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança. Para clientes do EA, o contexto de cobrança é o registro. Para assinaturas individuais com tarifas pagas conforme o uso, o escopo do orçamento são todas as assinaturas qualificadas criadas pelo administrador da conta.</li></ul> |
| Região | A região do Azure que é coberta pela reserva. |
| Tamanho da instância reservada Premium v3 | O tamanho das instâncias reservadas Premium v3. |
| Termo | Um ano ou três anos. Há também um termo de cinco anos disponível somente para instâncias reservadas HBv2 Premium v3. |
| Quantidade | O número de instâncias sendo compradas na reserva. A quantidade é o número de instâncias reservadas Premium v3 em execução que podem receber o desconto de cobrança. Por exemplo, se você estiver executando dez instâncias reservadas Standard\_D2 Premium v3 no Leste dos EUA, especifique a quantidade como dez para maximizar o benefício para todas as instâncias reservadas Premium v3 em execução. |

## <a name="save-with-isolated-stamp-fees"></a>Economizar dinheiro com Tarifas de Selo Isolado

Você pode economizar dinheiro com o Imposto de Selo do Serviço de Aplicativo Isolado do Azure comprometendo-se com uma reserva de uso do selo por uma duração de três anos. Para comprar a capacidade reservada do Imposto de Selo Isolado, você precisa escolher a região do Azure em que o selo será implantado e o número de selos a serem comprados.

Quando você compra uma reserva, o uso do Imposto de Selo Isolado que corresponde aos atributos da reserva deixa de ser cobrado segundo as taxas de pagamento conforme o uso. A reserva é aplicada automaticamente ao número de selos isolados que correspondem à região e ao escopo da capacidade reservada. Você não precisa atribuir uma reserva a um selo isolado. A reserva não se aplica a funções de trabalho, de forma que todos os outros recursos associados ao selo são cobrados separadamente.

Quando a capacidade reservada expira, os Selos Isolados continuam sendo executados, mas são cobrados pela taxa de pagamento conforme o uso. As reservas não são renovadas automaticamente.

## <a name="determine-the-right-isolated-stamp-reservation-to-purchase"></a>Determinar a reserva correta de Selo Isolado a ser comprada

Ao comprar uma reserva, você está se comprometendo a usar as quantidades reservadas nos próximos três anos. Verifique seus dados de uso para determinar quantos Selos do Serviço de Aplicativo Isolado você está usando consistentemente e pode usar no futuro.

Além disso, certifique-se de entender como o Selo Isolado emite o medidor do Linux ou do Windows.

- Por padrão, um Selo Isolado vazio emite o medidor de selo do Windows. Por exemplo, sem trabalhos implantados. Ele continuará emitindo esse medidor se trabalhos do Windows forem implantados no selo.
- O medidor mudará para o medidor de selo do Linux se você implantar um trabalho do Linux.
- Em casos em que trabalhos do Linux e do Windows são implantados, o selo emite o medidor do Windows.

Portanto, o medidor do selo pode alternar entre o Windows e o Linux durante a vida útil do selo.

Compre reservas de selo do Windows se você tiver um ou mais trabalhos do Windows no selo. O único caso em que você deve comprar uma reserva de selo do Linux é quando planeja ter _apenas_ trabalhos do Linux no selo.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Comprar capacidade reservada do Selo Isolado

Você pode comprar capacidade reservada do Selo Isolado no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). Pague pela reserva [antecipadamente ou com pagamentos mensais](./prepare-buy-reservation.md). Para comprar capacidade reservada, é necessário ter a função de proprietário de pelo menos uma assinatura Enterprise ou uma assinatura individual com tarifas pagas conforme o uso.

- Para assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal do EA](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você precisará ser um administrador de EA.
- Para o programa Provedor de Soluções de Nuvem (CSP), apenas os agentes admin ou agentes de vendas podem adquirir a capacidade reservada do Azure Synapse Analytics.

**Para comprar:**

1. Vá para o [Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Selecione uma assinatura. Use a lista **Assinatura** para escolher a assinatura usada para pagar a capacidade reservada. A forma de pagamento da assinatura é cobrada nos custos da capacidade reservada. O tipo de assinatura deve ser um contrato empresarial (números da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Pagamento Conforme o Uso (números da oferta: MS-AZR-0003P ou MS-AZR-0023P) ou uma assinatura de CSP.
    - Para uma assinatura corporativa, os encargos são deduzidos do saldo do Pagamento antecipado do Azure do registro (chamado anteriormente de compromisso monetário) ou cobrados como excedente.
    - Para a assinatura de Pagamento Conforme o Uso, as cobranças são feitas ao cartão de crédito ou à forma de pagamento de faturas na assinatura.
1. Selecione **Escopo** para escolher um escopo da assinatura.
    - **Escopo de grupo de recursos único** — aplica o desconto de reserva apenas aos recursos correspondentes no grupo de recursos selecionado.
    - **Escopo de assinatura única** — aplica o desconto de reserva apenas aos recursos correspondentes na assinatura selecionada.
    - **Escopo compartilhado** — aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança. Para clientes do Contrato Enterprise, o contexto de cobrança é o registro. Para assinaturas individuais com tarifas pagas conforme o uso, o escopo do orçamento são todas as assinaturas qualificadas criadas pelo administrador da conta.
1. Selecione uma **Região** para escolher uma região do Azure coberta pela capacidade reservada e adicione a reserva ao carrinho.
1. Selecione um tipo de Plano Isolado e clique em **Selecionar**.  
    ![Exemplo ](./media/prepay-app-service/app-service-isolated-stamp-select.png)
1. Insira a quantidade de selos do Serviço de Aplicativo Isolado a serem reservados. Por exemplo, uma quantidade de três forneceria três selos reservados por região. Clique em **Avançar: Examinar + Comprar**.
1. Examine e clique em **Comprar agora**.

Após a compra, vá até [Reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) para exibir o status de da compra e monitorá-la a qualquer momento.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](exchange-and-refund-azure-reservations.md).

## <a name="discount-application-shown-in-usage-data"></a>Aplicação do desconto mostrada nos dados de uso

Seus dados de uso têm um preço efetivo de zero para o uso, que obtém um desconto de reserva. Os dados de uso mostram o desconto de reserva para cada instância de selo em cada reserva.

Para saber mais sobre como o desconto de reserva é mostrado nos dados de uso, confira [Obter custos e uso de reserva do Contrato Enterprise](understand-reserved-instance-usage-ea.md) se você for um cliente com EA (Contrato Enterprise). Caso contrário, confira [Entenda o uso de reserva do Azure para uma assinatura individual com taxas pagas conforme o uso](understand-reserved-instance-usage.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
  - [O que são Reservas do Azure?](save-compute-costs-reservations.md)
  - [Entenda como um desconto de reserva de Selo do Serviço de Aplicativo Isolado do Azure é aplicado](reservation-discount-app-service.md)
  - [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)
