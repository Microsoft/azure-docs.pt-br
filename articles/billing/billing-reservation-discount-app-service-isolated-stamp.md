---
title: Como os descontos de reserva se aplicam a carimbos de Azure App de serviço isolados
description: Saiba como os descontos de reserva se aplicam a carimbos de Azure App de serviço isolados.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: banders
ms.openlocfilehash: cb4b371deadb1ae9e6ae048c3157809aff857c9d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298250"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>Como os descontos de reserva se aplicam a carimbos de Azure App de serviço isolados

Depois de comprar Serviço do Aplicativo Isolado capacidade reservada da taxa de selo, o desconto de reserva é aplicado automaticamente à taxa de carimbo em uma região. O desconto de reserva aplica-se ao uso emitido pelo medidor de taxa de carimbo isolado. Os trabalhadores, front-ends adicionais e quaisquer outros recursos associados ao carimbo continuam a ser cobrados na taxa regular.

## <a name="reservation-discount-application"></a>Aplicativo de desconto de reserva

O desconto da taxa de selo Serviço do Aplicativo Isolado é aplicado à execução de carimbos isolados por hora. Se você não tiver um carimbo implantado por uma hora, a capacidade reservada será desperdiçada para essa hora. Ele não é transportado.

Após a compra, a reserva que você compra é correspondida a um carimbo isolado em execução em uma região específica. Se você desligar esse carimbo, os descontos de reserva serão aplicados automaticamente a quaisquer outros carimbos em execução na região. Quando não houver carimbos, a reserva será aplicada ao próximo carimbo criado na região.

Quando os carimbos não são executados por uma hora completa, a reserva se aplica automaticamente a outros carimbos correspondentes na mesma região durante a mesma hora.

## <a name="choose-a-stamp-type---windows-or-linux"></a>Escolha um tipo de carimbo-Windows ou Linux

Um carimbo isolado vazio emite o medidor de selo do Windows por padrão. Por exemplo, quando nenhum trabalho é implantado. Ele continua emitindo o medidor quando os trabalhos do Windows são implantados. O medidor muda para o medidor de selo do Linux se você implantar um trabalho do Linux. O carimbo emite o medidor do Windows quando os trabalhadores do Linux e do Windows são implantados.

Como resultado, o medidor de selo pode mudar entre o Windows e o Linux durante a vida útil do carimbo. Enquanto isso, as reservas são específicas do sistema operacional. Você precisará comprar uma reserva que dá suporte aos trabalhos que planeja implantar no carimbo. Carimbos somente no Windows e carimbos mistos usam a reserva do Windows. Os carimbos com apenas trabalhadores do Linux usam a reserva do Linux.

A única vez que você deve comprar uma reserva do Linux é quando planeja ter _apenas_ trabalhadores do Linux no selo.

## <a name="discount-examples"></a>Exemplos de desconto

Os exemplos a seguir mostram como o desconto de instância reservada da taxa de carimbo isolado se aplica, dependendo das implantações.

- **Exemplo 1**: Você adquire uma instância de capacidade de carimbo reservado isolado em uma região sem carimbos de Serviço do Aplicativo Isolado. Você implanta um novo carimbo na região e paga taxas reservadas para esse carimbo.
- **Exemplo 2**: Você adquire uma instância da capacidade de carimbo reservado isolado em uma região que já tem um carimbo Serviço do Aplicativo Isolado implantado. Você começa a receber a taxa reservada para o carimbo implantado.
- **Exemplo 3**: Você adquire uma instância de capacidade de carimbo reservado isolado em uma região com um carimbo de Serviço do Aplicativo Isolado já implantado. Você começa a receber a taxa reservada no carimbo implantado. Posteriormente, você exclui o carimbo e implanta um novo. Você recebe a taxa reservada para o novo carimbo. Os descontos não são transferidos por durações sem carimbos implantados.
- **Exemplo 4**: Você adquire uma instância de capacidade de carimbo reservado do Linux isolado em uma região e, em seguida, implanta um novo carimbo na região. Quando o carimbo é inicialmente implantado sem trabalhadores, ele emite o medidor de selo do Windows. Nenhum desconto é recebido. Quando o primeiro trabalho do Linux é implantado no carimbo, ele emite o medidor de carimbo do Linux e o desconto de reserva se aplica. Se um Windows Worker for implantado posteriormente no carimbo, o medidor de selo será revertido para o Windows. Você não recebe mais um desconto para a reserva de carimbo reservado do Linux isolado.

## <a name="next-steps"></a>Próximas etapas

- Para aprender a gerenciar uma reserva, confira [Gerenciar Reservas do Azure](billing-manage-reserved-vm-instance.md).
- Para saber mais sobre a pré-aquisição de Serviço do Aplicativo Isolado carimbo de capacidade reservada para economizar dinheiro, confira [pagar pela taxa de carimbo isolado do serviço Azure app com capacidade reservada](billing-prepay-app-service-isolated-stamp.md).
- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
  - [O que são Reservas do Azure?](billing-save-compute-costs-reservations.md)
  - [Gerenciar Reservas no Azure](billing-manage-reserved-vm-instance.md)
  - [Entender o uso de reserva para uma assinatura com tarifas pagas conforme o uso](billing-understand-reserved-instance-usage.md)
  - [Entender o uso de reserva para seu registro de empresa](billing-understand-reserved-instance-usage-ea.md)
