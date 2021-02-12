---
title: Entender o desconto de reserva – Banco de Dados do Azure para MariaDB
description: Saiba como um desconto de reserva é aplicado ao Banco de Dados do Azure para MariaDB
author: mksuni
ms.author: sumuth
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 2197f3ec9907aa9f1afde6272dd2e8a807465476
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240385"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mariadb"></a>Como um desconto de reserva é aplicado ao Banco de Dados do Azure para MariaDB

Depois de comprar uma capacidade reservada do Banco de Dados do Azure para MariaDB, o desconto de reserva é aplicado automaticamente aos servidores MariaDB que correspondam aos atributos e à quantidade da reserva. Uma reserva abrange somente os custos de computação do Banco de Dados do Azure para MariaDB. Você será cobrado por armazenamento e rede com as taxas normais.

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

Um desconto de reserva é do tipo * **usar ou perder** _. Portanto, se você não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível postergar horas reservadas não utilizadas.

Quando você desliga um recurso, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se nenhum recurso correspondente for encontrado no escopo especificado, as horas reservadas serão perdidas.

## <a name="discount-applied-to-azure-database-for-mariadb"></a>Desconto aplicado ao Banco de Dados do Azure para MariaDB

O desconto de capacidade reservada do Banco de Dados do Azure para MariaDB é aplicado na execução de seus servidores MariaDB com uma cobrança por hora. A reserva que você compra corresponde ao uso de computação emitido pelos servidores em execução do Banco de Dados do Azure para MariaDB. Para servidores MariaDB que não são executados por uma hora inteira, a reserva é aplicada automaticamente a outros Bancos de Dados do Azure para MariaDB que correspondam aos atributos de reserva. O desconto pode se aplicado aos servidores do Banco de Dados do Azure para MariaDB que estejam em execução simultânea. Se você não tiver um servidor MariaDB que seja executado durante a hora inteira que corresponda aos atributos de reserva, não obterá o benefício total do desconto de reserva para essa hora.

Os exemplos a seguir mostram como o desconto de capacidade reservada do Banco de Dados do Azure para MariaDB se aplica, dependendo do número de núcleos que você comprou e quando está sendo executado.

_ **Exemplo 1** : Você compra capacidade reservada do Banco de Dados do Azure para MariaDB para um 8 vCores. Se você estiver executando um servidor de Banco de Dados do Azure para MariaDB de 16 vCores que corresponda ao restante dos atributos da reserva, você será cobrado pelo preço pré-pago de 8 vCores de sua utilização de computação do servidor MariaDB e obterá o desconto de reserva por uma hora do uso de computação do servidor MariaDB de 8 vCores.

Para o restante desses exemplos, suponha que a capacidade reservada do Banco de Dados do Azure para MariaDB que comprar seja para um Banco de Dados do Azure para MariaDB de 16 vCores e o restante dos atributos de reserva corresponder os servidores MariaDB em execução.

* **Exemplo 2** : Você executa dois servidores do Banco de Dados do Azure para MariaDB com oito vCores cada por uma hora. O desconto de reserva de 16 vCores é aplicado para computação de uso para os dois servidores 8 vCores do Banco de Dados do Azure para MariaDB.

* **Exemplo 3** : Você executa um servidor do Banco de Dados do Azure para MariaDB de 16 vCores de 13:00 às 13:30. Você executa outro servidor do Banco de Dados do Azure para MariaDB de 16 vCores de 13:30 às 14:00. Ambos são cobertas pelo desconto de reserva.

* **Exemplo 4** : Você executa um servidor do Banco de Dados do Azure para MariaDB de 16 vCores de 13:00 às 13:45. Você executa outro servidor do Banco de Dados do Azure para MariaDB de 16 vCores de 13:30 às 14:00. Você será cobrado o preço pago conforme o uso para a sobreposição de 15 minutos. O desconto de reserva se aplica ao uso de computação para o restante do tempo.

Para entender e visualizar o aplicativo de suas reservas do Azure nos relatórios de uso de faturamento, consulte [Entender o uso de reserva do Azure](./understand-reserved-instance-usage-ea.md).

## <a name="next-steps"></a>Próximas etapas

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).