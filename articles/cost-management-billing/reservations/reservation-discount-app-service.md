---
title: Descontos de reserva para o Serviço de Aplicativo do Azure
description: Saiba como os descontos de reserva se aplicam às instâncias do Serviço de Aplicativo do Azure Premium v3 e aos Selos Isolados.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: banders
ms.openlocfilehash: c599c64ce4b22bbf7bece77602b22fef6629d07c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369723"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-premium-v3-instances-and-isolated-stamps"></a>Como os descontos de reserva se aplicam às instâncias do Serviço de Aplicativo do Azure Premium v3 e aos Selos Isolados

Este artigo ajuda você a entender como os descontos se aplicam às instâncias do Serviço de Aplicativo do Azure Premium v3 e aos Selos Isolados.

## <a name="how-reservation-discounts-apply-to-premium-v3-instances"></a>Como os descontos de reserva se aplicam às instâncias Premium v3

Depois que você comprar uma Instância Reservada do Serviço de Aplicativo do Azure Premium v3, o desconto de reserva será aplicado automaticamente às instâncias do Serviço de Aplicativo que correspondem aos atributos e à quantidade da reserva. Uma reserva abrange o custo das suas instâncias Premium v3. 

### <a name="how-the-discount-is-applied-to-azure-app-service"></a>Como o desconto é aplicado ao Serviço de Aplicativo do Azure 

Um desconto de reserva é do tipo *usar ou perder*. Portanto, se você não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível postergar horas reservadas não utilizadas.
Quando você desliga um recurso, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se nenhum recurso correspondente for encontrado no escopo especificado, as horas reservadas serão perdidas.

### <a name="reservation-discount-for-premium-v3-instances"></a>Desconto de reserva para instâncias Premium v3

O desconto de reserva do Azure é aplicado às instâncias Premium v3 em execução, por hora. Para a aplicação do desconto de reserva, as reservas compradas são comparadas ao uso emitido pelas instâncias Premium v3 em execução. Para as instâncias Premium v3 que podem não ser executadas por uma hora completa, a reserva será preenchida com outras instâncias que não estejam usando uma reserva, incluindo as instâncias em execução simultânea. Ao final da hora, a aplicação da reserva para as instâncias em uma hora é bloqueada. No caso de uma instância não ser executada por uma hora ou de instâncias simultâneas em uma hora não preencherem a hora de reserva, a reserva é subutilizada nessa hora. O gráfico a seguir ilustra a aplicação de uma reserva ao uso faturável de VM. A ilustração baseia-se na compra de uma reserva e duas instâncias de VM correspondentes.

![Imagem mostrando a aplicação de uma reserva para uso faturável de VM](./media/reservation-discount-app-service/reserved-premium-v3-instance-application.png)

1.  Qualquer uso acima da linha de reserva é cobrado com base nas taxas regulares pagas conforme o uso. Não há cobrança por uso abaixo das reservas, pois ele já foi pago como parte da compra de reserva.
2.  Na hora 1, a instância 1 é executada por 0,75 hora e a instância 2, por 0,5 hora. O uso total relativo à hora 1 é de 1,25 hora. São cobradas as taxas de pagamento pelo uso da 0,25 hora restante.
3.  Nas horas 2 e 3, ambas as instâncias foram executadas por 1 hora. Uma instância está coberta pela reserva e a outro é cobrada com as taxas de pagamento pelo uso.
4.  Na hora 4, a instância 1 é executada por 0,5 hora e a instância 2, por 1 hora. A instância 1 está totalmente coberta pela reserva, e também 0,5 hora da instância 2. São cobradas as taxas de pagamento pelo uso da 0,5 hora restante.

Para entender e exibir o aplicativo das Reservas do Azure nos relatórios de uso de cobrança, consulte [Entender o uso de reserva](understand-reserved-instance-usage-ea.md).

## <a name="how-reservation-discounts-apply-to-isolated-stamps"></a>Como os descontos de reserva se aplicam aos Selos Isolados

Após a compra da capacidade reservada do Imposto de Selo Isolado do Serviço de Aplicativo, o desconto de reserva é aplicado automaticamente ao Imposto de Selo em uma região. O desconto de reserva aplica-se ao uso emitido pelo medidor de Imposto de Selo Isolado. Trabalhadores, outros front-ends e outros recursos associados ao selo continuam sendo cobrados na taxa regular.

### <a name="reservation-discount-application"></a>Aplicação de desconto de reserva

O desconto do Imposto de Selo Isolado do Serviço de Aplicativo é aplicado aos selos isolados em execução por hora. Caso você não tenha um selo implantado por hora, a capacidade reservada será desperdiçada para essa hora. Ela não é transferida.

Após a compra, a reserva comprada corresponderá a um selo isolado em execução em uma região específica. Se você desligar esse selo, os descontos de reserva serão aplicados automaticamente a outros selos em execução na região. Quando não existe nenhum selo, a reserva é aplicada ao próximo selo criado na região.

Quando os selos não forem executados durante uma hora inteira, a reserva se aplicará automaticamente a outros selos correspondentes na mesma região durante a mesma hora.

### <a name="choose-a-stamp-type---windows-or-linux"></a>Escolha um tipo de selo – Windows ou Linux

Um Selo Isolado vazio emite o medidor de selo do Windows por padrão. Por exemplo, quando nenhum trabalho é implantado. Ele continuará emitindo o medidor quando os trabalhos do Windows forem implantados. O medidor mudará para o medidor de selo do Linux se você implantar um trabalho do Linux. O selo emite o medidor do Windows quando os trabalhos do Linux e do Windows são implantados.

Em decorrência disso, o medidor do selo pode alternar entre o Windows e o Linux durante a vida útil do selo. Enquanto isso, as reservas são específicas do sistema operacional. Será necessário comprar uma reserva compatível com os trabalhos que você planeja implantar no selo. Selos somente do Windows e selos mistos usam a reserva do Windows. Selos apenas com trabalhos do Linux usam a reserva do Linux.

A única vez em que você deve comprar uma reserva do Linux é quando planeja ter _apenas_ trabalhos do Linux no selo.

### <a name="discount-examples"></a>Exemplos de desconto

Os exemplos a seguir mostram como o desconto de instância reservada do Imposto de Selo Isolado se aplica, dependendo das implantações.

- **Exemplo 1**: você compra uma instância da capacidade de Selo Reservado Isolado em uma região sem Selos Isolados do Serviço de Aplicativo. Você implanta um novo selo na região e paga as taxas reservadas para esse selo.
- **Exemplo 2**: você compra uma instância da capacidade de Selo Reservado Isolado em uma região que já tem um Selo Isolados do Serviço de Aplicativo implantado. Você começa recebendo a taxa reservada do selo implantado.
- **Exemplo 3**: você compra uma instância da capacidade de Selo Reservado Isolado em uma região com um Selo Isolados do Serviço de Aplicativo já implantado. Você começa recebendo a taxa reservada no selo implantado. Posteriormente, exclua o selo e implante um novo. Você recebe a taxa reservada do novo selo. Os descontos não são transferidos para durações sem selos implantados.
- **Exemplo 4**: você compra uma instância da capacidade de Selo Reservado do Linux Isolado em uma região e implanta um novo selo na região. Quando o selo é implantado inicialmente sem trabalhos, ele emite o medidor de selo do Windows. Nenhum desconto é recebido. Quando o primeiro trabalho do Linux tiver implantado o selo, ele emitirá o medidor de Selo do Linux e o desconto de reserva será aplicado. Se um trabalho do Windows for implantado posteriormente no selo, o medidor de selo será revertido para o Windows. Você não receberá mais um desconto para a reserva de Selo Reservado do Linux Isolado.

## <a name="next-steps"></a>Próximas etapas

- Para aprender a gerenciar uma reserva, confira [Gerenciar Reservas do Azure](manage-reserved-vm-instance.md).
- Para saber mais sobre como comprar previamente uma capacidade reservada do Serviço de Aplicativo Premium v3 e do Selo Isolado para economizar dinheiro, confira [Pagar antecipadamente o Serviço de Aplicativo do Azure com capacidade reservada](prepay-app-service.md).
- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
  - [O que são Reservas do Azure?](save-compute-costs-reservations.md)
  - [Gerenciar Reservas no Azure](manage-reserved-vm-instance.md)
  - [Noções básicas sobre o uso de reserva para uma assinatura com taxas pagas conforme o uso](understand-reserved-instance-usage.md)
  - [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)
