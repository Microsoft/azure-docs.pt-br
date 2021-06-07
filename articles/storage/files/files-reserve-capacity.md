---
title: Otimizar custos para arquivos do Azure com capacidade reservada
titleSuffix: Azure Files
description: Saiba como economizar custos em implantações de compartilhamento de arquivos do Azure usando a capacidade reservada de arquivos do Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 918320cdb24442e551249e4e67d65e4ba85846c8
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027532"
---
# <a name="optimize-costs-for-azure-files-with-reserved-capacity"></a>Otimizar custos para arquivos do Azure com capacidade reservada
Você pode economizar dinheiro nos custos de armazenamento para compartilhamentos de arquivos do Azure com reservas de capacidade. A capacidade reservada dos arquivos do Azure oferece um desconto na capacidade de custos de armazenamento quando você se compromete com uma reserva por um ou três anos. Uma reserva fornece uma quantidade fixa de capacidade de armazenamento para o termo da reserva.

A capacidade reservada dos arquivos do Azure pode reduzir significativamente os custos de capacidade para armazenar dados em seus compartilhamentos de arquivos do Azure. A quantidade de salvamento dependerá da duração da sua reserva, da capacidade total que você optar por reservar e das configurações de camada e redundância escolhidas para os compartilhamentos de arquivos do Azure. A capacidade reservada fornece um desconto de cobrança e não afeta o estado de seus compartilhamentos de arquivos do Azure.

Para obter informações sobre preços sobre a capacidade de reserva para arquivos do Azure, consulte [preços de arquivos do Azure](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="reservation-terms-for-azure-files"></a>Termos de reserva para arquivos do Azure
As seções a seguir descrevem os termos de uma reserva de capacidade dos arquivos do Azure.

### <a name="reservation-capacity"></a>Capacidade de reserva
Você pode comprar a capacidade reservada de arquivos do Azure em unidades de 10 TiB e 100 TiB por mês para um período de um ou três anos.

### <a name="reservation-scope"></a>Escopo de reserva
A capacidade reservada dos arquivos do Azure está disponível para uma única assinatura ou para várias assinaturas (escopo compartilhado). Quando definido como escopo para uma única assinatura, o desconto de reserva é aplicado somente à assinatura selecionada. Quando com escopo para várias assinaturas, o desconto de reserva é compartilhado entre essas assinaturas no contexto de cobrança do cliente. Uma reserva se aplica ao seu uso dentro do escopo comprado e não pode ser limitada a uma conta de armazenamento, contêiner ou objeto específico dentro da assinatura.

Uma reserva de capacidade para arquivos do Azure abrange apenas a quantidade de dados armazenados em uma assinatura ou grupo de recursos compartilhado. As cobranças de transações, largura de banda e transferência de dados não são incluídas na reserva. Assim que você comprar uma reserva, os encargos de capacidade que correspondem aos atributos de reserva serão cobrados com as tarifas de desconto em vez das tarifas pagas conforme o uso. Para obter mais informações sobre reservas do Azure, consulte [o que são as reservas do Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md).

### <a name="supported-tiers-and-redundancy-options"></a>Opções de redundância e camadas com suporte
A capacidade reservada dos arquivos do Azure está disponível somente para compartilhamentos de arquivos padrão implantados nas contas de armazenamento (GPv2) da versão de uso geral. A capacidade reservada não está disponível para compartilhamentos de arquivos do Azure nas camadas Premium ou otimizada para transação.

Atualmente, somente os compartilhamentos de arquivos do Azure nas camadas quente e fria dão suporte a reservas. Todas as redundâncias de armazenamento dão suporte para reservas. Para obter mais informações sobre as opções de redundância, consulte [redundância de arquivos do Azure](storage-files-planning.md#redundancy).

### <a name="security-requirements-for-purchase"></a>Requisitos de segurança para a compra
Para comprar a capacidade reservada:

- Você deve estar na função de **proprietário** de pelo menos uma assinatura corporativa ou individual com tarifas pagas conforme o uso.
- Para assinaturas empresariais, **adicionar instâncias reservadas** deve estar habilitada no portal de ea. Ou, se essa configuração estiver desabilitada, você deve ser um administrador de EA na assinatura.
- Para o programa CSP (provedor de soluções na nuvem), somente agentes de administração ou agentes de vendas podem comprar a capacidade reservada de arquivos do Azure.

## <a name="determine-required-capacity-before-purchase"></a>Determinar a capacidade necessária antes da compra
Ao comprar uma reserva de arquivos do Azure, você deve escolher a opção região, camada e redundância para a reserva. Sua reserva é válida somente para dados armazenados nessa região, camada e nível de redundância. Por exemplo, suponha que você compre uma reserva de dados no oeste dos EUA para a camada quente usando o ZRS (armazenamento com redundância de zona). Essa reserva não será aplicada aos dados no leste dos EUA, aos dados na camada fria ou aos dados no armazenamento com redundância geográfica (GRS). No entanto, você pode comprar outra reserva para suas necessidades adicionais.  

As reservas estão disponíveis para os blocos TiB de 10 TiB ou 100, com descontos mais altos para 100 blocos TiB. Quando você compra uma reserva no portal do Azure, a Microsoft pode fornecer recomendações com base no uso anterior para ajudar a determinar qual reserva você deve comprar.

## <a name="purchase-azure-files-reserved-capacity"></a>Comprar capacidade reservada de arquivos do Azure
Você pode comprar a capacidade reservada de arquivos do Azure por meio do [portal do Azure](https://portal.azure.com). Pague pela reserva antecipadamente ou com pagamentos mensais. Para obter mais informações sobre como comprar com pagamentos mensais, consulte [comprar reservas do Azure com pagamentos iniciais ou mensais](../../cost-management-billing/reservations/prepare-buy-reservation.md).

Para obter ajuda para identificar os termos de reserva que são adequados para seu cenário, consulte [entender o desconto de capacidade reservada do armazenamento do Azure](../../cost-management-billing/reservations/understand-storage-charges.md).

Siga estas etapas para comprar a capacidade reservada:

1. Navegue até a folha [reservas de compra](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) no portal do Azure.  
1. Selecione **arquivos do Azure** para comprar uma nova reserva.  
1. Preencha os campos obrigatórios conforme descrito na tabela a seguir:

    ![Captura de tela mostrando como comprar capacidade reservada](./media/files-reserve-capacity/select-reserved-capacity.png)

   |Campo  |Descrição  |
   |---------|---------|
   |**Escopo**   |  Indica quantas assinaturas podem usar o benefício de cobrança associado à reserva. Também controla como a reserva será aplicada a assinaturas específicas. <br/><br/> Se você selecionar **compartilhado**, o desconto de reserva será aplicado à capacidade dos arquivos do Azure em qualquer assinatura no contexto de cobrança. O contexto de cobrança é baseado em como você se inscreveu no Azure. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro. Para clientes pagos conforme o uso, o escopo compartilhado inclui todas as assinaturas individuais com tarifas pagas conforme o uso criadas pelo administrador da conta.  <br/><br/>  Se você selecionar **assinatura única**, o desconto de reserva será aplicado à capacidade dos arquivos do Azure na assinatura selecionada. <br/><br/> Se você selecionar **um único grupo de recursos**, o desconto de reserva será aplicado à capacidade dos arquivos do Azure na assinatura selecionada e ao grupo de recursos selecionado dentro dessa assinatura. <br/><br/> Você pode alterar o escopo de reserva depois de comprar a reserva.  |
   |**Assinatura**  | A assinatura que é usada para pagar pela reserva de arquivos do Azure. O método de pagamento na assinatura selecionada é usado para cobrar os custos. A assinatura deve ser um dos seguintes tipos: <br/><br/>  Contrato Enterprise (números de oferta: MS-AZR-0017P ou MS-AZR-0148P): para uma assinatura corporativa, os encargos são deduzidos do saldo do Azure pré-pago do registro (anteriormente chamado de compromisso monetário) ou cobrados como excedentes. <br/><br/> Assinatura individual com tarifas pagas conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P): para uma assinatura individual com tarifas pagas conforme o uso, os encargos são cobrados no cartão de crédito ou no método de pagamento de fatura na assinatura.    |
   | **Região** | A região em que a reserva está em vigor. |
   | **Camada** | A camada em que o para o qual a reserva está em vigor. As opções incluem *quentes* e *frias*. |
   | **Redundância** | A opção de redundância para a reserva. As opções incluem *LRS*, *ZRS*, *grs* e *GZRS*. Para obter mais informações sobre as opções de redundância, consulte [redundância de arquivos do Azure](storage-files-planning.md#redundancy). |
   | **Frequência de cobrança** | Indica com que frequência a conta é cobrada pela reserva. As opções incluem *mensalmente* ou *antecipadamente*. |
   | **Tamanho** | A quantidade de capacidade a ser reservada. |
   |**Termo**  | Um ano ou três anos.   |

1. Depois de selecionar os parâmetros para sua reserva, o portal do Azure exibirá o custo. O portal também mostra a porcentagem de desconto sobre a cobrança paga conforme o uso.

1. Na folha **reservas de compra** , examine o custo total da reserva. Você também pode fornecer um nome para a reserva.

Depois de comprar uma reserva, ela é aplicada automaticamente a qualquer compartilhamento de arquivos do Azure existente que corresponda aos termos da reserva. Se você ainda não tiver criado nenhum compartilhamento de arquivos do Azure, a reserva será aplicada sempre que você criar um recurso que corresponda aos termos da reserva. Em ambos os casos, o termo da reserva começa imediatamente após uma compra bem-sucedida.

## <a name="exchange-or-refund-a-reservation"></a>Trocar ou reembolsar uma reserva
Você pode trocar ou reembolsar uma reserva, com certas limitações. Essas limitações são descritas nas seções a seguir.

Para trocar ou reembolsar uma reserva, navegue até os detalhes de reserva na portal do Azure. Selecione **Exchange** ou **reembolso** e siga as instruções para enviar uma solicitação de suporte. Quando a solicitação tiver sido processada, a Microsoft lhe enviará um email para confirmar a conclusão da solicitação.

Para obter mais informações sobre as políticas de reservas do Azure, consulte [trocas e reembolsos de autoatendimento para reservas do Azure](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Trocar uma reserva
A troca de uma reserva permite que você receba um reembolso rateado com base na parte não usada da reserva. Em seguida, você pode aplicar o reembolso ao preço de compra de uma nova reserva de arquivos do Azure.

Não há limite para o número de trocas que você pode fazer. Além disso, não há nenhuma taxa associada a uma troca. A nova reserva que você compra deve ser de valor igual ou maior do que o crédito rateado da reserva original. Uma reserva de arquivos do Azure pode ser trocada apenas para outra reserva de arquivos do Azure e não para uma reserva para qualquer outro serviço do Azure.

### <a name="refund-a-reservation"></a>Reembolsar uma reserva
Você pode cancelar uma reserva de arquivos do Azure a qualquer momento. Ao cancelar, você receberá um reembolso rateado com base no termo restante da reserva, menos uma taxa de rescisão inicial de 12%. O reembolso máximo por ano é de $50000.

O cancelamento de uma reserva termina imediatamente a reserva e retorna os meses restantes à Microsoft. O saldo rateado restante, menos a taxa, será reembolsado em sua forma original de compra.

## <a name="expiration-of-a-reservation"></a>Expiração de uma reserva
Quando uma reserva expira, qualquer capacidade de arquivos do Azure que você esteja usando sob essa reserva é cobrada com a taxa paga conforme o uso. As reservas não são renovadas automaticamente.

Você receberá uma notificação por email 30 dias antes da expiração da reserva e novamente na data de validade. Para continuar aproveitando a economia de custos que uma reserva fornece, renove-a não depois da data de expiração.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco
Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas
- [O que são Reservas do Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Entenda como os descontos de reserva são aplicados aos serviços de armazenamento do Azure](../../cost-management-billing/reservations/understand-storage-charges.md)