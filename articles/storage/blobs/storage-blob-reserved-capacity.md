---
title: Otimizar custos para o armazenamento de blobs com capacidade reservada
titleSuffix: Azure Storage
description: Saiba como comprar a capacidade reservada do armazenamento do Azure para economizar custos em blob de blocos e recursos de Azure Data Lake Storage Gen2.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/08/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6fd791495602846b95e2dd3e99423db5505f5307
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98600912"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>Otimizar custos para o armazenamento de blobs com capacidade reservada

Você pode economizar dinheiro em custos de armazenamento para dados de blob com capacidade reservada de armazenamento do Azure. A capacidade reservada do armazenamento do Azure oferece um desconto sobre a capacidade para BLOBs de blocos e para Azure Data Lake Storage Gen2 dados em contas de armazenamento padrão quando você se compromete com uma reserva por um ou três anos. Uma reserva fornece uma quantidade fixa de capacidade de armazenamento para o termo da reserva.

A capacidade reservada de armazenamento do Azure pode reduzir significativamente os custos de capacidade para BLOBs de bloco e dados de Azure Data Lake Storage Gen2. As economias de custo obtidas dependem da duração da sua reserva, da capacidade total que você optar por reservar e da camada de acesso e do tipo de redundância que você escolheu para sua conta de armazenamento. A capacidade reservada fornece um desconto de cobrança e não afeta o estado dos seus recursos de armazenamento do Azure.

Para saber mais sobre os preços de reserva do Armazenamento do Azure, confira [Preços do blob de blocos](https://azure.microsoft.com/pricing/details/storage/blobs/) e [Preços do Azure Data Lake Storage Gen 2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="reservation-terms-for-azure-storage"></a>Termos de reserva para o armazenamento do Azure

As seções a seguir descrevem os termos de uma reserva de armazenamento do Azure.

### <a name="reservation-capacity"></a>Capacidade de reserva

Você pode comprar a capacidade reservada do armazenamento do Azure em unidades de 100 TiB e 1 PiB por mês para um período de um ou três anos.

### <a name="reservation-scope"></a>Escopo de reserva

A capacidade reservada do armazenamento do Azure está disponível para uma única assinatura ou para várias assinaturas (escopo compartilhado). Quando definido como escopo para uma única assinatura, o desconto de reserva é aplicado somente à assinatura selecionada. Quando com escopo para várias assinaturas, o desconto de reserva é compartilhado entre essas assinaturas no contexto de cobrança do cliente.

Ao comprar a capacidade reservada do armazenamento do Azure, você pode usar sua reserva para dados de BLOB e de Azure Data Lake Storage Gen2 de bloco. Uma reserva é aplicada ao seu uso dentro do escopo comprado e não pode ser limitada a uma conta de armazenamento, contêiner ou objeto específico dentro da assinatura.

Uma reserva de armazenamento do Azure abrange apenas a quantidade de dados armazenados em uma assinatura ou grupo de recursos compartilhado. A exclusão antecipada, as operações, a largura de banda e os encargos de transferência de dados não estão incluídos na reserva. Assim que você comprar uma reserva, os encargos de capacidade que correspondem aos atributos de reserva serão cobrados com as tarifas de desconto em vez de nas tarifas pagas conforme o uso. Para obter mais informações sobre reservas do Azure, consulte [o que são as reservas do Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md).

### <a name="supported-account-types-tiers-and-redundancy-options"></a>Tipos de conta, camadas e opções de redundância com suporte

A capacidade reservada do armazenamento do Azure está disponível para recursos em contas de armazenamento Standard, incluindo contas de armazenamento de BLOBs v2 (GPv2) e de uso geral.

Todas as camadas de acesso (quentes, frias e arquivos) têm suporte para reservas. Para obter mais informações sobre camadas de acesso, consulte [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](storage-blob-storage-tiers.md).

Todos os tipos de redundância têm suporte para reservas. Para obter mais informações sobre as opções de redundância, consulte [redundância de armazenamento do Azure](../common/storage-redundancy.md).

> [!NOTE]
> A capacidade reservada do armazenamento do Azure não está disponível para contas de armazenamento Premium, contas de armazenamento de uso geral V1 (GPv1), Azure Data Lake Storage Gen1, blobs de páginas, armazenamento de filas do Azure, armazenamento de tabelas do Azure ou arquivos do Azure.  

### <a name="security-requirements-for-purchase"></a>Requisitos de segurança para a compra

Para comprar a capacidade reservada:

- Você deve estar na função de **proprietário** de pelo menos uma assinatura corporativa ou individual com tarifas pagas conforme o uso.
- Para assinaturas empresariais, **adicionar instâncias reservadas** deve estar habilitada no portal de ea. Ou, se essa configuração estiver desabilitada, você deve ser um administrador de EA na assinatura.
- Para o programa CSP (provedor de soluções na nuvem), somente agentes de administração ou agentes de vendas podem comprar a capacidade reservada do armazenamento de BLOBs do Azure.

## <a name="determine-required-capacity-before-purchase"></a>Determinar a capacidade necessária antes da compra

Ao comprar uma reserva de armazenamento do Azure, você deve escolher a opção região, camada de acesso e redundância para a reserva. Sua reserva é válida somente para dados armazenados nessa região, camada de acesso e nível de redundância. Por exemplo, suponha que você compre uma reserva de dados no oeste dos EUA para a camada quente usando o ZRS (armazenamento com redundância de zona). Você não pode usar a mesma reserva de dados no leste dos EUA, dados na camada de arquivo ou dados no armazenamento com redundância geográfica (GRS). No entanto, você pode comprar outra reserva para suas necessidades adicionais.  

As reservas estão disponíveis hoje para blocos de 100 TiB ou 1 PiB, com descontos maiores para 1 blocos PiB. Quando você compra uma reserva no portal do Azure, a Microsoft pode fornecer recomendações com base no uso anterior para ajudar a determinar qual reserva você deve comprar.

## <a name="purchase-azure-storage-reserved-capacity"></a>Comprar capacidade reservada do armazenamento do Azure

Você pode comprar a capacidade reservada do armazenamento do Azure por meio do [portal do Azure](https://portal.azure.com). Pague pela reserva antecipadamente ou com pagamentos mensais. Para obter mais informações sobre como comprar com pagamentos mensais, consulte [comprar reservas do Azure com pagamentos iniciais ou mensais](../../cost-management-billing/reservations/prepare-buy-reservation.md).

Para obter ajuda para identificar os termos de reserva que são adequados para seu cenário, consulte [entender o desconto de capacidade reservada do armazenamento do Azure](../../cost-management-billing/reservations/understand-storage-charges.md).

Siga estas etapas para comprar a capacidade reservada:

1. Navegue até o painel [reservas de compra](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) na portal do Azure.  
1. Selecione **armazenamento de BLOBs do Azure** para comprar uma nova reserva.  
1. Preencha os campos obrigatórios conforme descrito na tabela a seguir:

    ![Captura de tela mostrando como comprar capacidade reservada](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |Campo  |Descrição  |
   |---------|---------|
   |**Escopo**   |  Indica quantas assinaturas podem usar o benefício de cobrança associado à reserva. Também controla como a reserva será aplicada a assinaturas específicas. <br/><br/> Se você selecionar **compartilhado**, o desconto de reserva será aplicado à capacidade de armazenamento do Azure em qualquer assinatura no contexto de cobrança. O contexto de cobrança é baseado em como você se inscreveu no Azure. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro. Para clientes pagos conforme o uso, o escopo compartilhado inclui todas as assinaturas individuais com tarifas pagas conforme o uso criadas pelo administrador da conta.  <br/><br/>  Se você selecionar **assinatura única**, o desconto de reserva será aplicado à capacidade de armazenamento do Azure na assinatura selecionada. <br/><br/> Se você selecionar **um único grupo de recursos**, o desconto de reserva será aplicado à capacidade de armazenamento do Azure na assinatura selecionada e ao grupo de recursos selecionado dentro dessa assinatura. <br/><br/> Você pode alterar o escopo de reserva depois de comprar a reserva.  |
   |**Assinatura**  | A assinatura que é usada para pagar pela reserva de armazenamento do Azure. O método de pagamento na assinatura selecionada é usado para cobrar os custos. A assinatura deve ser um dos seguintes tipos: <br/><br/>  Contrato Enterprise (números de oferta: MS-AZR-0017P ou MS-AZR-0148P): para uma assinatura corporativa, os encargos são deduzidos do saldo do Azure pré-pago do registro (anteriormente chamado de compromisso monetário) ou cobrados como excedentes. <br/><br/> Assinatura individual com tarifas pagas conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P): para uma assinatura individual com tarifas pagas conforme o uso, os encargos são cobrados no cartão de crédito ou no método de pagamento de fatura na assinatura.    |
   | **Região** | A região em que a reserva está em vigor. |
   | **Camada de acesso** | A camada de acesso em que o para o qual a reserva está em vigor. As opções incluem *quente*, *fria* ou *arquivo morto*. Para obter mais informações sobre as camadas de acesso, consulte [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](storage-blob-storage-tiers.md). |
   | **Redundância** | A opção de redundância para a reserva. As opções incluem *LRS*, *ZRS*, *grs*, *GZRS*, *ra-grs* e *ra-GZRS*. Para obter mais informações sobre as opções de redundância, consulte [redundância de armazenamento do Azure](../common/storage-redundancy.md). |
   | **Frequência de cobrança** | Indica com que frequência a conta é cobrada pela reserva. As opções incluem *mensalmente* ou *antecipadamente*. |
   | **Tamanho** | A quantidade de capacidade a ser reservada. |
   |**Termo**  | Um ano ou três anos.   |

1. Depois de selecionar os parâmetros para sua reserva, o portal do Azure exibirá o custo. O portal também mostra a porcentagem de desconto sobre a cobrança paga conforme o uso.

1. No painel **reservas de compra** , examine o custo total da reserva. Você também pode fornecer um nome para a reserva.

    ![Captura de tela mostrando como comprar uma reserva](media/storage-blob-reserved-capacity/purchase-reservations.png)

Depois de comprar uma reserva, ela é aplicada automaticamente a qualquer blob de blocos de armazenamento do Azure existente ou Azure Data Lake Storage Gen2 recursos que correspondem aos termos da reserva. Se você ainda não tiver criado nenhum recurso de armazenamento do Azure, a reserva será aplicada sempre que você criar um recurso que corresponda aos termos da reserva. Em ambos os casos, o termo da reserva começa imediatamente após uma compra bem-sucedida.

## <a name="exchange-or-refund-a-reservation"></a>Trocar ou reembolsar uma reserva

Você pode trocar ou reembolsar uma reserva, com certas limitações. Essas limitações são descritas nas seções a seguir.

Para trocar ou reembolsar uma reserva, navegue até os detalhes de reserva na portal do Azure. Selecione **Exchange** ou **reembolso** e siga as instruções para enviar uma solicitação de suporte. Quando a solicitação tiver sido processada, a Microsoft lhe enviará um email para confirmar a conclusão da solicitação.

Para obter mais informações sobre as políticas de reservas do Azure, consulte [trocas e reembolsos de autoatendimento para reservas do Azure](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Trocar uma reserva

A troca de uma reserva permite que você receba um reembolso rateado com base na parte não usada da reserva. Em seguida, você pode aplicar o reembolso ao preço de compra de uma nova reserva de armazenamento do Azure.

Não há limite para o número de trocas que você pode fazer. Além disso, não há nenhuma taxa associada a uma troca. A nova reserva que você compra deve ser de valor igual ou maior do que o crédito rateado da reserva original. Uma reserva de armazenamento do Azure pode ser trocada apenas para outra reserva de armazenamento do Azure e não para uma reserva para qualquer outro serviço do Azure.

### <a name="refund-a-reservation"></a>Reembolsar uma reserva

Você pode cancelar uma reserva de armazenamento do Azure a qualquer momento. Ao cancelar, você receberá um reembolso rateado com base no termo restante da reserva, menos uma taxa de rescisão inicial de 12%. O reembolso máximo por ano é de $50000.

O cancelamento de uma reserva termina imediatamente a reserva e retorna os meses restantes à Microsoft. O saldo rateado restante, menos a taxa, será reembolsado em sua forma original de compra.

## <a name="expiration-of-a-reservation"></a>Expiração de uma reserva

Quando uma reserva expira, qualquer capacidade de armazenamento do Azure que você esteja usando sob essa reserva é cobrada com a taxa paga conforme o uso. As reservas não são renovadas automaticamente.

Você receberá uma notificação por email 30 dias antes da expiração da reserva e novamente na data de validade. Para continuar aproveitando a economia de custos que uma reserva fornece, renove-a não depois da data de expiração.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- [O que são Reservas do Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Entenda como o desconto de reserva é aplicado ao Armazenamento do Azure](../../cost-management-billing/reservations/understand-storage-charges.md)