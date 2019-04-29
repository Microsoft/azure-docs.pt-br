---
title: Como os descontos de reserva se aplicam ao SQL Data Warehouse do Azure | Microsoft Docs
description: Saiba como os descontos de reserva se aplicam ao Azure SQL Data Warehouse para ajudar a economizar dinheiro.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 10e19377d31489cd19465fe6171ffb530bd58c28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60918329"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Como os descontos de reserva se aplicam ao Azure SQL Data Warehouse

Depois de comprar capacidade reservada do Azure SQL Data Warehouse, o desconto de reserva é aplicado automaticamente para data warehouses existentes nessa região. O desconto de reserva se aplica ao uso emitido pelo medidor de cDWU SQL Data Warehouse. Rede e armazenamento são cobradas as taxas pré-pagas.

## <a name="reservation-discount-application"></a>Aplicativo de desconto de reserva

O desconto de capacidade reservada do SQL Data Warehouse é aplicado à execução de depósitos por hora. Se você não tiver um depósito implantado em uma hora, a capacidade reservada é desperdiçada para essa hora. Ele não é transferida.

Após a compra, a reserva que você compra é correspondente ao uso de SQL Data Warehouse emitido executando warehouses em qualquer ponto no tempo. Se você desligar alguns depósitos, em seguida, reserva descontos automaticamente se aplicam a quaisquer outros depósitos correspondentes.

Para depósitos que não são executados por uma hora completa, a reserva é aplicada automaticamente a outras instâncias correspondentes naquela hora.

## <a name="discount-examples"></a>Exemplos de desconto

Os exemplos a seguir mostram como aplica o desconto de capacidade reservada do SQL Data Warehouse, dependendo das implantações.

- **Exemplo 1**: Você compra 5 unidades de capacidade de cDWU reservado 100. Você pode executar uma instância do SQL Data Warehouse de DW1500c por uma hora. Nesse caso, o uso é emitido para 15 unidades de 100 uso de cDWU. O desconto de reserva se aplica a 5 unidades em que você usou. Você é cobrado usando as tarifas pré-pagas para o restante de 10 unidades de 100 cDWU uso que você usou.

- **Exemplo 2**: Você compra 5 unidades de capacidade de cDWU reservado 100. Você pode executar duas instâncias de DW100c SQL Data Warehouse por uma hora. Nesse caso, os dois eventos de uso são emitidos por 1 unidade de uso de cDWU 100. Ambos os eventos de uso obtém descontos de capacidade reservada. As 3 unidades restantes da capacidade de 100 cDWU reservado são desperdiçadas e não se transfere para uso futuro.

- **Exemplo 3**: Você compra 1 unidade de capacidade de cDWU reservado 100. Você executa duas instâncias de DW100c SQL Data Warehouse. Cada um é executado por 30 minutos. Nesse caso, os dois eventos de uso obtém descontos de capacidade reservada. Nenhum uso será cobrado usando as taxas pagas conforme o uso.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

- Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são Reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Exibir transações de reserva](billing-view-reservations.md)
- [Obter as transações de reserva e a utilização por meio da API](billing-reservation-apis.md)
- [Gerenciar reservas](billing-manage-reserved-vm-instance.md)
