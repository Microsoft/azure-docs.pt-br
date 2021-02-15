---
title: Capacidade reservada no Azure Cosmos DB para otimizar o custo
description: Aprenda a comprar a capacidade reservada do Azure Cosmos DB para economizar nos custos de computação.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 5c2d1d286572b21879742a1a9c6ab3975441373d
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602682"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Otimizar o custo com a capacidade reservada no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A capacidade reservada do Azure Cosmos DB ajuda você a economizar dinheiro ao se comprometer com uma reserva do Azure Cosmos DB por um período de um ou três anos. Com a capacidade reservada do Azure Cosmos DB, você pode obter um desconto na taxa de transferência provisionada para recursos do Cosmos DB. Exemplos de recursos são bancos de dados e contêineres (tabelas, coleções e gráficos).

A capacidade reservada do Azure Cosmos DB pode reduzir significativamente os custos do Cosmos DB&mdash;até 65% nos preços regulares, com um compromisso antecipado de um a três anos. A capacidade reservada proporciona um desconto de cobrança e não afeta o estado do runtime dos seus recursos do Azure Cosmos DB.

Capacidade reservada do Azure Cosmos DB cobre a taxa de transferência provisionada para seus recursos. Ela não cobre encargos de armazenamento e rede. Assim que você compra uma reserva, as cobranças de taxa de transferência que correspondem aos atributos de reserva não são mais cobradas nas taxas de pagamento conforme o uso. Para obter mais informações sobre reservas, confira o artigo [Reservas do Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md).

Você pode comprar a capacidade reservada do Azure Cosmos DB no [portal do Azure](https://portal.azure.com). Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/prepare-buy-reservation.md). Para comprar uma capacidade reservada:

* Você deve estar na função de proprietário de pelo menos uma assinatura corporativa ou individual com tarifas pagas conforme o uso.  
* Para as assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal EA](https://ea.azure.com). Ou, se essa configuração estiver desabilitada, você deve ser um administrador de EA na assinatura.
* Para o programa CSP (Provedor de Solução de Nuvem), apenas os agentes admin ou agentes de vendas podem comprar a capacidade reservada do Azure Cosmos DB.

## <a name="determine-the-required-throughput-before-purchase"></a>Determinar a taxa de transferência necessária antes da compra

O tamanho da compra de capacidade reservada deve ser baseado na quantidade total de produtividade que os recursos de Azure Cosmos DB existentes ou em breve ser implantados serão usados por hora. Por exemplo: comprar a capacidade reservada de 30.000 RU/s se esse for o padrão de uso consistente por hora. Neste exemplo, qualquer taxa de transferência provisionada acima de 30.000 RU/s será cobrada usando sua taxa pré-paga. Se a taxa de transferência provisionada estiver abaixo de 30.000 RU/s em uma hora, a capacidade reservada extra para essa hora será desperdiçada.

Calculamos as recomendações de compra com base em seu padrão de uso por hora. O uso nos últimos 7, 30 e 60 dias é analisado e a aquisição da capacidade reservada que maximiza a economia é recomendada. Você pode exibir os tamanhos de reserva recomendados no portal do Azure usando as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).  

2. Selecione **todos os serviços**  >  **reservas**  >  **Adicionar**.

3. No painel **reservas de compra** , escolha **Azure Cosmos DB**.

4. Selecione a guia **recomendado** para exibir as reservas recomendadas:

Você pode filtrar as recomendações pelos seguintes atributos:

- **Termo** (1 ano ou 3 anos)
- **Frequência de cobrança** (mensal ou antecipada)
- **Tipo de taxa de transferência** (ru/s x gravação de várias regiões ru/s)

Além disso, você pode fazer o escopo de recomendações para estar dentro de um único grupo de recursos, assinatura única ou todo o seu registro do Azure. 

Aqui está um exemplo de recomendação:

:::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png" alt-text="Recomendações de capacidade reservada":::

Essa recomendação para comprar uma reserva de 30.000 RU/s indica que, entre as reservas de 3 anos, um tamanho de reserva de 30.000 RU/s aumentará a economia. Nesse caso, a recomendação é calculada com base nos últimos 30 dias de uso de Azure Cosmos DB. Se esse cliente esperar que os últimos 30 dias de uso do Azure Cosmos DB sejam representativos de uso futuro, eles maximizarão a economia adquirindo uma reserva de 30.000 RU/s.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Comprar capacidade reservada do Azure Cosmos DB

1. Entre no [portal do Azure](https://portal.azure.com).  

2. Selecione **todos os serviços**  >  **reservas**  >  **Adicionar**.  

3. No painel **reservas de compra** , escolha **Azure Cosmos DB** para comprar uma nova reserva.  

4. Preencha os campos obrigatórios conforme descrito na tabela a seguir:

   :::image type="content" source="./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png" alt-text="Preencher o formulário de capacidade reservada":::

   |Campo  |Descrição  |
   |---------|---------|
   |Escopo   |   Opção que controla quantas assinaturas podem usar o benefício de cobrança associado com a reserva. Também controla como a reserva será aplicada a assinaturas específicas. <br/><br/>  Se você selecionar **Compartilhado**, o desconto de reserva será aplicado às instâncias do Azure Cosmos DB em execução em qualquer assinatura dentro do seu contexto de cobrança. O contexto de cobrança é baseado em como você se inscreveu no Azure. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro. Para clientes pagos conforme o uso, o escopo compartilhado é todas as assinaturas individuais com tarifas pagas conforme o uso criadas pelo administrador da conta.  <br/><br/>  Se você selecionar **Assinatura única**, o desconto de reserva será aplicado a instâncias do Azure Cosmos DB na assinatura selecionada. <br/><br/> Se você selecionar **um único grupo de recursos**, o desconto de reserva será aplicado a Azure Cosmos DB instâncias na assinatura selecionada e ao grupo de recursos selecionado dentro dessa assinatura. <br/><br/> Você pode alterar o escopo de reserva após comprar a capacidade reservada.  |
   |Assinatura  |   Assinatura usada para pagar pela capacidade reservada do Azure Cosmos DB. O método de pagamento na assinatura selecionada é usado para cobrar os custos. A assinatura deve ser um dos seguintes tipos: <br/><br/>  Enterprise Agreement (números de oferta: MS-AZR-0017P ou MS-AZR-0148P): para uma assinatura corporativa, os encargos são deduzidos do saldo do Azure pré-pago do registro (anteriormente chamado de compromisso monetário) ou cobrados como excedentes. <br/><br/> Assinatura individual com tarifas pagas conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P): para uma assinatura individual com tarifas pagas conforme o uso, os encargos são cobrados no cartão de crédito ou no método de pagamento de fatura na assinatura.    |
   | Grupo de Recursos | Grupo de recursos ao qual o desconto de capacidade reservada é aplicado. |
   |Termo  |   Um ano ou três anos.   |
   |Tipo de taxa de transferência   |  A taxa de transferência é provisionada como unidades de solicitação. Você pode comprar uma reserva para a taxa de transferência provisionada para as duas configurações – gravações de região única, bem como várias gravações de região. O tipo de taxa de transferência tem dois valores a serem escolhidos: 100 RU/s por hora e 100 várias regiões gravadas em RU/s por hora.|
   | Unidades de capacidade reservadas| A quantidade de taxa de transferência que você deseja reservar. Você pode calcular esse valor determinando a taxa de transferência necessária para todos os seus recursos do Cosmos DB (por exemplo, bancos de dados ou contêineres) por região. Em seguida, você o multiplica pelo número de regiões que você associará ao banco de dados Cosmos. Por exemplo: se você tiver cinco regiões com 1 milhão de RU/s em todas as regiões, selecione 5 milhões de RU/s para compra de capacidade de reserva. |


5. Depois de preencher o formulário, o preço necessário para comprar a capacidade reservada é calculado. A saída também mostra a porcentagem de desconto que você obtém com as opções escolhidas. Em seguida, clique em **selecionar**

6. No painel **reservas de compra** , examine o desconto e o preço da reserva. Esse preço de reserva se aplica aos recursos do Azure Cosmos DB com a taxa de transferência provisionada entre todas as regiões.  

   :::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png" alt-text="Resumo da capacidade reservada":::

7. Selecione **revisar + comprar** e **comprar agora**. Quando a compra for bem-sucedida, você verá a seguinte página:

Depois de comprar uma reserva, ela será aplicada imediatamente a qualquer recurso existente do Azure Cosmos DB para corresponder aos termos da reserva. Se você não tiver nenhum recurso existente do Azure Cosmos DB, a reserva será aplicada quando implantar uma nova instância do Cosmos DB que corresponde aos termos da reserva. Em ambos os casos, o período da reserva começa imediatamente após a compra com êxito.

Quando sua reserva expirar, as instâncias do Azure Cosmos DB continuarão sendo executadas e cobradas com as taxas normais de pagamento conforme o uso.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>Próximas etapas

O desconto da reserva é aplicado automaticamente aos recursos do Azure Cosmos DB que correspondem ao escopo e aos atributos da reserva. Você pode atualizar o escopo da reserva por meio do portal do Azure, do PowerShell, da CLI do Azure ou da API.

*  Para saber como os descontos da capacidade reservada são aplicados ao Azure Cosmos DB, confira [Entender o desconto de reserva do Azure](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md).

* Para saber mais sobre reservas do Azure, confira os seguintes artigos:

   * [O que são reservas do Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [Gerenciar reservas do Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [Entender o uso de reserva para seu registro de empresa](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [Entender o uso de reserva para a sua assinatura paga conforme o uso](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [Reservas do Azure no programa CSP do Partner Center](/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).