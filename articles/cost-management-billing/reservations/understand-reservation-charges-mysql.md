---
title: Entender o desconto de reserva – Banco de Dados do Azure para MySQL
description: Saiba como um desconto de reserva é aplicado aos servidores de Banco de Dados do Azure para MySQL.
author: mksuni
ms.author: sumuth
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 85a8c59cce74787a7b1723850915ec133b34b3b0
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240368"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mysql"></a>Como um desconto de reserva é aplicado ao Banco de Dados do Azure para MySQL

Após comprar uma capacidade reservada do Banco de Dados do Azure para MySQL, o desconto de reserva é aplicado automaticamente aos servidores MySQL que correspondem aos atributos e à quantidade da reserva. Uma reserva abrange somente os custos de computação do Banco de Dados do Azure para MySQL. Você será cobrado por armazenamento e rede com as taxas normais.

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

Um desconto de reserva é do tipo * **usar ou perder** _. Portanto, se você não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível postergar horas reservadas não utilizadas.</br>

Quando você desliga um recurso, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se nenhum recurso correspondente for encontrado no escopo especificado, as horas reservadas serão perdidas.

## <a name="discount-applied-to-azure-database-for-mysql"></a>Desconto aplicado ao Banco de Dados do Azure para MySQL

O desconto de capacidade reservada do Banco de Dados do Azure para MySQL é aplicado na execução de seus servidores MySQL com uma cobrança por hora. A reserva que você compra corresponde ao uso de computação emitido pelos servidores em execução do Banco de Dados do Azure para MySQL. Para servidores MySQL que não são executados por uma hora inteira, a reserva é aplicada automaticamente a outros Bancos de Dados do Azure para MySQL que correspondam aos atributos de reserva. O desconto pode se aplicado aos servidores do Banco de Dados do Azure para MySQL que estejam em execução simultânea. Se você não tiver um servidor MySQL em execução durante uma hora inteira que corresponda aos atributos de reserva, não obterá o benefício total do desconto de reserva por aquela hora.

Os exemplos a seguir mostram que a aplicação do desconto de capacidade reservada do Banco de Dados do Azure para MySQL depende do número de núcleos que você comprou e de quando estão em execução.

_ **Exemplo 1** : Você comprou uma capacidade reservada do Banco de Dados do Azure para MySQL para oito vCore. Se você estiver executando um servidor de 16 vCore de Banco de Dados do Azure para MySQL que corresponda ao restante dos atributos da reserva, será cobrado pelo preço pago conforme o uso de oito vCore de sua utilização de computação do MySQL Server e obterá o desconto de reserva por uma hora do uso de computação do servidor de oito vCore do MySQL.</br>

Para o restante desses exemplos, suponha que a capacidade reservada do Banco de Dados do Azure para MySQL que comprar seja para um Banco de Dados do Azure para MySQL de 16 vCore e o restante dos atributos de reserva corresponder os servidores MySQL em execução.

* **Exemplo 2** : Você executa dois servidores de oito vCore cada do Banco de Dados do Azure para MySQL por uma hora. O desconto de reserva de 16 vCore é aplicado para computação de uso para os dois servidores oito vCore do Banco de Dados do Azure para MySQL.

* **Exemplo 3** : Você executa um servidor de 16 vCore do Banco de Dados do Azure para MySQL das 13h às 13h30. Você executa outro servidor de 16 vCore do Banco de Dados do Azure para MySQL das 13h30 às 14h. Ambos são cobertas pelo desconto de reserva.

* **Exemplo 4** : Você executa um servidor de 16 vCore do Banco de Dados do Azure para MySQL das 13h às 13h45. Você executa outro servidor de 16 vCore do Banco de Dados do Azure para MySQL das 13h30 às 14h. Você será cobrado o preço pago conforme o uso para a sobreposição de 15 minutos. O desconto de reserva se aplica ao uso de computação para o restante do tempo.

Para entender e visualizar o aplicativo de suas reservas do Azure nos relatórios de uso de faturamento, consulte [Entender o uso de reserva do Azure](./understand-reserved-instance-usage-ea.md).

## <a name="next-steps"></a>Próximas etapas

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).