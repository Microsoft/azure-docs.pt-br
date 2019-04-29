---
title: Otimizar custo dos recursos do Azure Cosmos DB com capacidade reservada
description: Aprenda a comprar a capacidade reservada do Azure Cosmos DB para economizar nos custos de computação.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 0e0d1cd98f27ef40681e37a55f7021bf102fda21
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60892061"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Otimizar o custo com a capacidade reservada no Azure Cosmos DB

A capacidade reservada do Azure Cosmos DB ajuda você a economizar dinheiro ao pagar antecipadamente pelos recursos do Azure Cosmos DB por um período de um a três anos. Com a capacidade reservada do Azure Cosmos DB, você pode obter um desconto na taxa de transferência provisionada para recursos do Cosmos DB. Exemplos de recursos são bancos de dados e contêineres (tabelas, coleções e gráficos).

A capacidade reservada do Azure Cosmos DB pode reduzir significativamente os custos do Cosmos DB&mdash;até 65% nos preços regulares, com um compromisso antecipado de um a três anos. A capacidade reservada proporciona um desconto de cobrança e não afeta o estado do tempo de execução dos seus recursos do Azure Cosmos DB.

Capacidade reservada do Azure Cosmos DB cobre a taxa de transferência provisionada para seus recursos. Ela não cobre encargos de armazenamento e rede. Assim que você compra uma reserva, as cobranças de taxa de transferência que correspondem aos atributos de reserva não são mais cobradas nas taxas de pagamento conforme o uso. Para obter mais informações sobre reservas, confira o artigo [Reservas do Azure](../billing/billing-save-compute-costs-reservations.md).

Você pode comprar a capacidade reservada do Azure Cosmos DB no [portal do Azure](https://portal.azure.com). Para comprar uma capacidade reservada:

* Você deve ser na função de proprietário para pelo menos uma Enterprise ou assinatura paga conforme o uso.  
* Para as assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal EA](https://ea.azure.com). Ou, se essa configuração estiver desabilitada, você deve ser um administrador de EA na assinatura.
* Para o programa CSP (Provedor de Solução de Nuvem), apenas os agentes admin ou agentes de vendas podem comprar a capacidade reservada do Azure Cosmos DB.

## <a name="determine-the-required-throughput-before-purchase"></a>Determinar a taxa de transferência necessária antes da compra

O tamanho da reserva deve ser baseado na quantidade total de taxa de transferência que usará os recursos do Azure Cosmos DB existentes ou a serem implantados em breve. Você pode determinar a taxa de transferência necessária das seguintes maneiras:

* Obtenha os dados históricos para a taxa de transferência total provisionada em suas contas do Azure Cosmos DB, bancos de dados e coleções em todas as regiões. Por exemplo, você pode avaliar a taxa de transferência provisionada média diária baixando sua instrução de uso diário do `https://account.azure.com`.

* Se você for um cliente do EA (Contrato Enterprise), poderá baixar o arquivo de uso para obter os detalhes de taxa de transferência do Azure Cosmos DB. Veja o valor **Tipo de Serviço** na seção **Informações adicionais** do arquivo de uso.

* Você pode somar a taxa de transferência média para todas as cargas de trabalho em suas contas do Azure Cosmos DB que você pretende executar no(s) próximo(s) um a três anos. Em seguida, pode usar essa quantidade para a reserva.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Comprar capacidade reservada do Azure Cosmos DB

1. Entre no [Portal do Azure](https://portal.azure.com).  

2. Selecione **Todos os serviços** > **Reservas** > **Adicionar**.  

3. No painel **Selecionar Tipo de Produto**, escolha **Azure Cosmos DB** > **Selecionar** para comprar uma nova reserva.  

4. Preencha os campos obrigatórios conforme descrito na tabela a seguir:

   ![Preencher o formulário de capacidade reservada](./media/cosmos-db-reserved-capacity/fill_reserved_capacity_form.png)

   |Campo  |DESCRIÇÃO  |
   |---------|---------|
   |NOME   |    Nome da reserva. Este campo é preenchido automaticamente com o `CosmosDB_Reservation_<timeStamp>`. Você pode fornecer um nome diferente durante a criação da reserva. Ou você pode renomeá-la depois que a reserva tiver sido criada.      |
   |Assinatura  |   Assinatura usada para pagar pela capacidade reservada do Azure Cosmos DB. O método de pagamento na assinatura selecionada é usado na cobrança dos custos iniciais. O tipo de assinatura deve ser um dos a seguir: <br/><br/>  Contrato Enterprise (números da oferta: MS-AZR-0017P ou MS-AZR-0148P): Para uma assinatura Enterprise, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como excedente. <br/><br/> Pagamento Conforme o Uso (números da oferta): MS-AZR-0003P ou MS-AZR-0023P): Para uma assinatura de Pagamento Conforme o Uso, as cobranças são feitas ao cartão de crédito ou à forma de pagamento de faturas na assinatura.    |
   |Escopo   |   Opção que controla quantas assinaturas podem usar o benefício de cobrança associado com a reserva. Também controla como a reserva será aplicada a assinaturas específicas.   <br/><br/>  Se você selecionar **Assinatura única**, o desconto de reserva será aplicado a instâncias do Azure Cosmos DB na assinatura selecionada. <br/><br/>  Se você selecionar **Compartilhado**, o desconto de reserva será aplicado às instâncias do Azure Cosmos DB em execução em qualquer assinatura dentro do seu contexto de cobrança. O contexto de cobrança é baseado em como você se inscreveu no Azure. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro. Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.  <br/><br/> Você pode alterar o escopo de reserva após comprar a capacidade reservada.  |
   |Tipo de capacidade reservada   |  Taxa de transferência provisionada como unidades de solicitação. Você pode comprar uma reserva para a taxa de transferência provisionada para as duas configurações - região única grava, bem como várias gravações de regiões.|
   |Unidades de capacidade reservada  |      Quantidade de taxa de transferência que você deseja reservar. Você pode calcular esse valor determinando a taxa de transferência necessária para todos os seus recursos do Cosmos DB (por exemplo, bancos de dados ou contêineres) por região. Em seguida, multiplique pelo número de regiões que você associará com seu banco de dados do Cosmos DB.  <br/><br/> Por exemplo:  se você tiver cinco regiões com 1 milhão de RU/s em todas as regiões, selecione 5 milhões de RU/s para compra de capacidade de reserva.    |
   |Termo  |   Um ano ou três anos.   |

5. Examine o desconto e o preço da reserva na seção **Custos**. Esse preço de reserva se aplica aos recursos do Azure Cosmos DB com a taxa de transferência provisionada entre todas as regiões.  

6. Selecione **Comprar**. Quando a compra for bem-sucedida, você verá a seguinte página:

   ![Preencher o formulário de capacidade reservada](./media/cosmos-db-reserved-capacity/reserved_capacity_successful.png)

Depois de comprar uma reserva, ela será aplicada imediatamente a qualquer recurso existente do Azure Cosmos DB para corresponder aos termos da reserva. Se você não tiver nenhum recurso existente do Azure Cosmos DB, a reserva será aplicada quando implantar uma nova instância do Cosmos DB que corresponde aos termos da reserva. Em ambos os casos, o período da reserva começa imediatamente após a compra com êxito.

Quando sua reserva expirar, as instâncias do Azure Cosmos DB continuarão sendo executadas e cobradas com as taxas normais de pagamento conforme o uso.

## <a name="cancellation-and-exchanges"></a>Cancelamento e trocas

Para obter ajuda na identificação a capacidade reservada à direita, consulte [entender como o desconto de reserva é aplicado ao Azure Cosmos DB](../billing/billing-understand-cosmosdb-reservation-charges.md). Se você precisa cancelar ou trocar uma reserva do Azure Cosmos DB, consulte [trocas de reserva e os reembolsos](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="next-steps"></a>Próximas etapas

O desconto da reserva é aplicado automaticamente aos recursos do Azure Cosmos DB que correspondem ao escopo e aos atributos da reserva. Você pode atualizar o escopo da reserva por meio do portal do Azure, do PowerShell, da CLI do Azure ou da API.

*  Para saber como os descontos da capacidade reservada são aplicados ao Azure Cosmos DB, confira [Entender o desconto de reserva do Azure](../billing/billing-understand-cosmosdb-reservation-charges.md).

* Para saber mais sobre as reservas do Azure, consulte os seguintes artigos:

   * [Quais são as reservas do Azure?](../billing/billing-save-compute-costs-reservations.md)  
   * [Gerenciar reservas do Azure](../billing/billing-manage-reserved-vm-instance.md)  
   * [Entender o uso de reserva para seu registro de empresa](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [Entender o uso de reserva para a sua assinatura paga conforme o uso](../billing/billing-understand-reserved-instance-usage.md)
   * [Reservas do Azure no programa CSP do Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
