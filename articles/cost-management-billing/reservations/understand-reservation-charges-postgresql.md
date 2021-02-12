---
title: Entender o desconto de reserva – servidor único do Banco de Dados do Azure para PostgreSQL
description: Saiba como um desconto de reserva é aplicado a um servidor individual do Banco de Dados do Azure para PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: ace362872f0b7ba8e2f3d0302c887e2465c62982
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240334"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-postgresql-single-server"></a>Como um desconto de reserva é aplicado a um servidor individual do Banco de Dados do Azure para PostgreSQL

Depois de comprar uma capacidade reservada de servidor do Banco de Dados do Azure para PostgreSQL, o desconto de reserva é aplicado automaticamente aos bancos de dados de servidores individuais do PostgreSQL que correspondam aos atributos e à quantidade da reserva. Uma reserva abrange somente os custos de computação do Banco de Dados do Azure para PostgreSQL. Você será cobrado por armazenamento e rede com as taxas normais.

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

Um desconto de reserva é do tipo * **usar ou perder** _. Portanto, se você não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível postergar horas reservadas não utilizadas.</br>

Quando você desliga um recurso, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se nenhum recurso correspondente for encontrado no escopo especificado, as horas reservadas serão perdidas.

## <a name="discount-applied-to-azure-database-for-postgresql-single-server"></a>Desconto aplicado ao servidor individual do Banco de Dados do Azure para PostgreSQL

O desconto de capacidade reservado ao servidor individual do Banco de Dados do Azure para PostgreSQL é aplicado na execução por hora do servidor individual do PostgreSQL. A reserva que você compra corresponde ao uso de computação emitido por um servidor individual em execução do Banco de Dados do Azure para PostgreSQL. Para servidores individuais do PostgreSQL que não são executados por uma hora inteira, a reserva é aplicada automaticamente a outro servidor individual do Banco de Dados do Azure para PostgreSQL que corresponda aos atributos de reserva. O desconto pode se aplicado aos servidores do Banco de Dados do Azure para PostgreSQL que estejam em execução simultânea. Se você não tiver um servidor individual PostgreSQL que seja executado durante a hora inteira e que corresponda aos atributos de reserva, não obterá o benefício total do desconto de reserva para essa hora.

Os exemplos a seguir mostram como o desconto de capacidade reservada do servidor individual do Banco de Dados do Azure para PostgreSQL se aplica, dependendo do número de núcleos que você comprou e quando está em execução.

_ **Exemplo 1** : Você comprou uma capacidade reservada para um servidor individual do Banco de Dados do Azure para PostgreSQL para oito vCore. Se você estiver executando um servidor individual de 16 vCore de Banco de Dados do Azure para PostgreSQL que corresponda ao restante dos atributos da reserva, será cobrado de acordo com o preço pago conforme o uso de oito vCore para essa sua utilização de computação no servidor individual do PostgreSQL e obterá o desconto de reserva por uma hora do uso de computação de oito vCore no servidor individual do PostgreSQL.</br>

Para o restante desses exemplos, suponha que a capacidade reservada do Banco de Dados do Azure para PostgreSQL que você comprou seja para um servidor individual de 16 vCore do Banco de Dados do Azure para PostgreSQL e o restante dos atributos de reserva correspondem aos servidores individuais do PostgreSQL em execução.

* **Exemplo 2** : você executa dois servidores individuais de oito vCore do Banco de Dados do Azure para PostgreSQL cada por uma hora. O desconto de reserva de 16 vCore é aplicado para computação de uso para os dois servidores individuais de oito vCore do Banco de Dados do Azure para PostgreSQL.

* **Exemplo 3** : Você executa um servidor individual de 16 vCore do Banco de Dados do Azure para PostgreSQL das 13h às 13h30. Você executa um servidor individual de 16 vCore do Banco de Dados do Azure para PostgreSQL das 13h30 às 14h. Ambos são cobertas pelo desconto de reserva.

* **Exemplo 4** : Você executa um servidor individual de 16 vCore do Banco de Dados do Azure para PostgreSQL das 13h às 13h45. Você executa um servidor individual de 16 vCore do Banco de Dados do Azure para PostgreSQL das 13h30 às 14h. Você será cobrado o preço pago conforme o uso para a sobreposição de 15 minutos. O desconto de reserva se aplica ao uso de computação para o restante do tempo.

Para entender e visualizar o aplicativo de suas reservas do Azure nos relatórios de uso de faturamento, consulte [Entender o uso de reserva do Azure](./understand-reserved-instance-usage-ea.md).

## <a name="next-steps"></a>Próximas etapas

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).