---
title: Otimize os custos para Armazenamento em Disco do Azure com reservas
description: Saiba mais sobre como comprar reservas de Armazenamento em Disco do Azure para economizar custos em discos gerenciados do SSD Premium.
author: roygara
ms.author: rogarana
ms.date: 01/30/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: db6575894904e6ced2d4be48fec5961f5b8b8a54
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602639"
---
# <a name="reduce-costs-with-azure-disks-reservation"></a>Reduzir os custos com a reserva de discos do Azure

Economize em seu uso de Armazenamento em Disco do Azure com capacidade reservada. Armazenamento em Disco do Azure reservas combinadas com as instâncias de máquinas virtuais reservadas do Azure permitem reduzir os custos totais da VM (máquina virtual). O desconto de reserva é aplicado automaticamente aos discos correspondentes no escopo de reserva selecionado. Devido a esse aplicativo automático, você não precisa atribuir uma reserva a um disco gerenciado para obter os descontos.

Os descontos são aplicados por hora, dependendo do uso do disco. A capacidade reservada não utilizada não é transferida. Os descontos de reserva de Armazenamento em Disco do Azure não se aplicam a discos não gerenciados, ultra discos ou consumo de blob de páginas.

## <a name="determine-your-storage-needs"></a>Determinar suas necessidades de armazenamento

Antes de comprar uma reserva, determine suas necessidades de armazenamento. Atualmente, Armazenamento em Disco do Azure reservas estão disponíveis apenas para os SKUs selecionados do SSD do Azure Premium. A SKU de um SSD Premium determina o tamanho e o desempenho do disco.

Ao determinar suas necessidades de armazenamento, não considere os discos com base apenas na capacidade. Por exemplo, você não pode ter uma reserva para um disco P40 e usá-lo para pagar por dois discos p30 menores. Ao comprar uma reserva, você está apenas comprando uma reserva para o número total de discos por SKU.

Uma reserva de disco é feita por SKU de disco. Como resultado, o consumo de reserva é baseado na unidade das SKUs de disco, em vez do tamanho fornecido.

Por exemplo, suponha que você reserve um disco P40 que tenha 2 TiB de capacidade de armazenamento provisionado. Suponha também que você aloque apenas dois discos p30. A reserva de P40 nesse caso não conta com o consumo de p30 e você paga a tarifa paga conforme o uso nos discos p30.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Considerações sobre a compra

Recomendamos as seguintes práticas ao considerar a compra de reserva de disco:

- Analise suas informações de uso para ajudar a determinar quais reservas devem ser compradas. Certifique-se de controlar o uso em SKUs de disco em vez de capacidade de disco provisionada ou usada.
- Examine sua reserva de disco juntamente com sua reserva de VM. É altamente recomendável fazer reservas para uso da VM e uso do disco para economia máxima. Você pode começar a determinar a reserva de VM correta e, em seguida, avaliar a reserva de disco. Em geral, você terá uma configuração padrão para cada uma de suas cargas de trabalho. Por exemplo, um servidor de SQL Server pode ter dois discos de dados P40 e um disco de sistema operacional p30.
  
  Esse tipo de padrão pode ajudá-lo a determinar o valor reservado que você pode comprar. Essa abordagem pode simplificar o processo de avaliação e garantir que você tenha um plano alinhado para a VM e os discos. O plano contém considerações como assinaturas ou regiões.

## <a name="purchase-restrictions"></a>Restrições de compra

Os descontos de reserva não estão disponíveis no momento para o seguinte:

- Discos não gerenciados ou BLOBs de página.
- SSDs padrão ou unidades de disco rígido padrão (HDDs).
- SSD Premium SKUs menores que p30: P1, P2, P3, P4, P6, P10, P15 e SKUs de SSD P20.
- Discos nas regiões Azure governamental, Azure Alemanha ou Azure China.

Em raras circunstâncias, o Azure limita a compra de novas reservas para um subconjunto de SKUs de disco devido à baixa capacidade em uma região.

## <a name="buy-a-disk-reservation"></a>Comprar uma reserva de disco

Você pode comprar Armazenamento em Disco do Azure reservas por meio do [portal do Azure](https://portal.azure.com/). Você pode pagar pela reserva até a frente ou com pagamentos mensais. Para obter mais informações sobre como comprar com pagamentos mensais, consulte [reservas de compra com pagamentos mensais](../cost-management-billing/reservations/prepare-buy-reservation.md#buy-reservations-with-monthly-payments).

Siga estas etapas para comprar a capacidade reservada:

1. Vá para o painel [reservas de compra](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) na portal do Azure.

1. Selecione **Managed disks do Azure** para comprar uma reserva.

    ![Painel para reservas de compra](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. Especifique os valores necessários descritos na tabela a seguir:

   |Elemento  |Descrição  |
   |---------|---------|
   |**Escopo**   |  Quantas assinaturas podem usar o benefício de cobrança associado à reserva. Esse valor também especifica como a reserva é aplicada a assinaturas específicas. <br/><br/> Se você selecionar **compartilhado**, o desconto de reserva será aplicado à capacidade de armazenamento do Azure em cada assinatura no contexto de cobrança. O contexto de cobrança é baseado em como você se inscreveu no Azure. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro. Para clientes pagos conforme o uso, o escopo compartilhado inclui todas as assinaturas individuais com tarifas pagas conforme o uso criadas pelo administrador da conta.  <br/><br/>  Se você selecionar **assinatura única**, o desconto de reserva será aplicado à capacidade de armazenamento do Azure na assinatura selecionada. <br/><br/> Se você selecionar **um único grupo de recursos**, o desconto de reserva será aplicado à capacidade de armazenamento do Azure na assinatura selecionada e no grupo de recursos selecionado da assinatura. <br/><br/> Você pode alterar o escopo de reserva depois de comprar a reserva.  |
   |**Assinatura**  | A assinatura que você usa para pagar pela reserva de armazenamento do Azure. O método de pagamento na assinatura selecionada é usado para cobrar os custos. A assinatura deve ser um dos seguintes tipos:<br/><ul><li> Enterprise Agreement (números de oferta MS-AZR-0017P e MS-AZR-0148P). Para uma assinatura empresarial, os encargos são deduzidos do saldo antecipado do Azure do registro (anteriormente chamado de compromisso monetário) ou cobrados como excedentes.</li><br/><li>Assinatura individual com tarifas pagas conforme o uso (números de oferta MS-AZR-0003P e MS-AZR-0023P). Para uma assinatura individual com tarifas pagas conforme o uso, os encargos são cobrados no cartão de crédito ou no método de pagamento de fatura na assinatura.</li></ul>    |
   | **Discos** | A SKU que você deseja criar. |
   | **Região** | A região em que a reserva está em vigor. |
   | **Frequência de cobrança** | Com que frequência a conta é cobrada pela reserva. As opções incluem **mensalmente** e **antecipadamente**. |

    ![Painel para selecionar o produto que você deseja purchase.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Depois de especificar os valores para sua reserva, o portal do Azure exibirá o custo. O portal também mostra a porcentagem de desconto sobre a cobrança paga conforme o uso. Selecione **Avançar** para continuar no painel **reservas de compra** .

1. No painel **reservas de compra** , você pode nomear sua reserva e selecionar a quantidade total de reservas que deseja fazer. O número de reservas é mapeado para o número de discos. Por exemplo, se você quiser reservar uma centena de discos, insira o valor de **quantidade** **100**.

1. Examine o custo total da reserva.

    ![O painel reservas de compra](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Depois de comprar uma reserva, ela é aplicada automaticamente a todos os recursos existentes do Armazenamento em Disco que correspondam aos termos de reserva. Se você ainda não tiver criado nenhum recurso Armazenamento em Disco, a reserva será aplicada sempre que você criar um recurso que corresponda aos termos de reserva. Em ambos os casos, o termo de reserva começa imediatamente após uma compra bem-sucedida.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Você pode cancelar, trocar ou reembolsar reservas dentro de determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="expiration-of-a-reservation"></a>Expiração de uma reserva

Quando uma reserva expira, qualquer capacidade de Armazenamento em Disco do Azure que você use sob essa reserva será cobrada com a taxa paga conforme o uso. As reservas não são renovadas automaticamente.

Você receberá uma notificação por email 30 dias antes da expiração da reserva e novamente na data de expiração. Para continuar aproveitando a economia de custos que uma reserva fornece, renove-a não depois da data de expiração.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- [O que são Reservas do Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Entenda como seu desconto de reserva é aplicado a Armazenamento em Disco do Azure](../cost-management-billing/reservations/understand-disk-reservations.md)