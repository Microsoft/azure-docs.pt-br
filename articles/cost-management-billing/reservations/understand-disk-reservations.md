---
title: Entender o desconto de reservas para o Armazenamento em Disco do Azure
description: Saiba como um desconto dos discos reservados do Azure é aplicado aos seus discos gerenciados SSD Premium do Azure.
author: roygara
ms.author: rogarana
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: cd89c3ddc3c58de02f1104109ce7f243c4d1e6fd
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682443"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>Entenda como o desconto de reserva é aplicado ao Armazenamento em Disco do Azure

Depois de comprar a capacidade reservada de disco do Azure, um desconto de reserva será aplicado automaticamente aos recursos do disco que correspondem aos termos da reserva. O desconto de reserva se aplica apenas aos SKUs do disco. Instantâneos de disco são cobrados em taxas pagas conforme o uso.

Para obter mais informações sobre a reserva de discos do Azure, confira [Economizar custos com a reserva de discos do Azure](../../virtual-machines/disks-reserved-capacity.md). Para obter informações sobre preços de reserva de discos do Azure, confira [Preços do Azure Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="how-the-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

O desconto de reserva de discos do Azure é um desconto do tipo pegar ou largar. Aplica-se a recursos de disco gerenciado por hora. Para uma determinada hora, se não tiver nenhum recurso de disco gerenciado que atenda aos termos de reserva, você perderá uma quantidade de reserva para essa hora. Horas não utilizadas não são postergadas.

Quando você exclui um recurso, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se não for encontrado nenhum recurso correspondente, as horas reservadas serão perdidas.

## <a name="discount-examples"></a>Exemplos de desconto

Os exemplos a seguir mostram como o desconto de reserva de discos do Azure se aplica dependendo de sua implantação.

Suponha que você compre e reserve 100 discos P30 na região Oeste dos EUA 2 durante o prazo de um ano. Cada disco tem aproximadamente 1 TiB de armazenamento. Suponha que o custo desta reserva de exemplo seja de US$ 140.100. Você pode optar por pagar antecipadamente o valor total ou parcelas mensais fixas de US$ 11.675 nos próximos 12 meses.

Os cenários a seguir descrevem o que acontecerá se você subutilizar, usar excessivamente ou dividir em níveis sua capacidade reservada. Para esses exemplos, vamos supor que você tenha se inscrito em um plano de pagamento mensal de reservas.

### <a name="underusing-your-capacity"></a>Subutilização da sua capacidade

Vamos supor que você implante apenas 99 de seus 100 discos P30 SSD (unidade de estado sólido) premium do Azure reservados por uma hora dentro do período de reserva. O disco P30 restante não é aplicado durante essa hora. Ele também não é transferido.

### <a name="overusing-your-capacity"></a>Superutilização da sua capacidade

Suponha que, para uma hora dentro do período de reserva, você use 101 discos P30 SSD Premium. O desconto de reserva se aplica apenas a 100 discos P30. O disco P30 restante é cobrado em taxas pagas conforme o uso para essa hora. Na próxima hora, se o uso diminuir para 100 discos P30, todo o uso será coberto pela reserva.

### <a name="tiering-your-capacity"></a>Divisão em camadas de sua capacidade

Suponha que, em uma determinada hora em seu período de reserva, você queira usar o total de 200 SSDs P30 Premium. Suponha também que você use apenas 100 nos primeiros 30 minutos. Durante esse período, seu uso é totalmente coberto, porque você fez uma reserva de 100 discos P30. Se, em seguida, você descontinuar o uso dos primeiros 100 (ou seja, está usando zero) e começar a usar os outros 100 pelos 30 minutos restantes, esse uso também estará coberto por sua reserva.

![Exemplo de capacidade em subutilização, uso excessivo ou dividida em níveis](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- [Reduzir os custos com a reserva de discos do Azure](../../virtual-machines/disks-reserved-capacity.md)
- [O que são Reservas do Azure?](save-compute-costs-reservations.md)
