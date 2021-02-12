---
title: Uso de reserva do Azure para uma assinatura individual
description: Saiba como ler seu uso para entender como a reserva do Azure para sua assinatura individual com taxas pagas conforme o uso é aplicada.
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: f6aad15d70157dda110fce233fbdafb7f3d896ad
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055052"
---
# <a name="understand-azure-reservation-usage-for-your-individual-subscription-with-pay-as-you-go-rates-subscription"></a>Entender o uso de reserva do Azure para sua assinatura individual com taxas pagas conforme o uso

Use a ReservationId da [página Reserva](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) e o arquivo de uso do [portal do Azure](https://portal.azure.com) para avaliar o uso da sua reserva.

Se você for um cliente com um Enterprise Agreement, consulte [entender o uso de reserva para seu registro de empresa.](understand-reserved-instance-usage-ea.md).

Este artigo pressupõe que a reserva é aplicada a uma única assinatura. Se a reserva for aplicada a mais de uma assinatura, seu benefício de reserva poderá abranger vários arquivos CSV de uso.

## <a name="usage-for-reserved-virtual-machine-instances"></a>Uso para instâncias de máquina Virtual reservada

As seções a seguir, suponha que você está executando uma VM do Windows Standard_DS1_v2 na região Leste dos EUA e seus reservado VM instância informações como a tabela a seguir:

| Campo | Valor |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Quantidade |1|
|SKU | Standard_DS1_v2|
|Região | eastus |

A parte de hardware da VM é coberta porque a VM implantada coincide com os atributos de reserva. Para ver qual software do Windows não é coberto pela instância de VM reservada, consulte [os custos de software do Windows de instâncias de VM de reserva do Azure](reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv-file-for-vms"></a>Seção de instrução do arquivo CSV para VMs

Esta seção do arquivo CSV mostra o uso total para sua reserva. Aplicar o filtro a **subcategoria de medidor** campo que contém **"Reserva-"** . Você vê algo como a seguinte captura de tela:

![Captura de tela dos detalhes e cobranças do uso da reserva filtrada](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

A linha **VM de Reserva-Base** tem o número total de horas cobertas pela reserva. Essa linha é $0,00 porque a reserva abrange-lo. O **Reservation-Windows Svr (1 núcleo)** linha abrange o custo de software do Windows.

### <a name="daily-usage-section-of-csv-file"></a>Seção uso diário do arquivo CSV

Filtrar **informações adicionais** e digite sua **ID de reserva**. A captura de tela a seguir mostra os campos relacionados à reserva.

![Captura de tela de detalhes de uso diário e cobranças](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **ReservationId** no campo **Additional Info** é a reserva aplicada à VM.
2. **ConsumptionMeter** é a ID do medidor para a VM.
3. A linha **Subcategoria de Medidor** das **Instâncias Reservadas de VM de base** representa o custo de US$ 0 na seção do demonstrativo. O custo de execução desta VM já é pago pela reserva.
4. **ID do medidor** é a ID do medidor para a reserva. O custo deste medidor é $0. Esse ID do medidor aparece para qualquer VM que se qualifique para o desconto de reserva.
5. Standard_DS1_v2 é uma VM vCPU, e a VM é implantada sem o Benefício Híbrido do Azure. Portanto, esse medidor cobre a carga extra do software Windows. Para encontrar o medidor correspondente à VM core da série D 1, consulte [Instâncias de VM de Reserva do Azure Custos do software do Windows](reserved-instance-windows-software-costs.md). Se você tiver o benefício híbrido do Azure, esse custo extra não será aplicado.

## <a name="usage-for-sql-database--cosmos-db-reservations"></a>Uso de reservas do Banco de Dados SQL e Cosmos DB

As seções a seguir usam o Banco de Dados SQL do Azure como exemplo para descrever o relatório de uso. Também é possível usar as mesmas etapas para obter o uso do Azure Cosmos DB.

Suponha que você esteja executando um Banco de Dados SQL Gen 4 na região leste dos EUA e as informações de reserva são semelhantes à tabela a seguir:

| Campo | Valor |
|---| --- |
|ReservationId |446ec809-423D-467c-8c5c-bbd5d22906b1|
|Quantidade |2|
|Produto| Banco de dados do SQL Gen 4 (2 núcleos)|
|Região | eastus |

### <a name="statement-section-of-csv-file"></a>Seção de instrução do arquivo CSV

Filtre no nome do medidor **Uso da Instância Reservada** e escolha a **Categoria do Medidor** necessária: Banco de Dados SQL do Azure ou Azure Cosmos DB. Você vê algo como a seguinte captura de tela:

![A captura de tela mostra uma entrada Categoria de Medidor.](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

O **uso de instância reservado** linha tem o número total de horas de núcleo coberta pela reserva. A taxa é de $ 0 para esta linha, pois a reserva cobriu o custo.

### <a name="detail-section-of-csv-file"></a>Seção de detalhes do arquivo CSV

Filtrar **informações adicionais** e digite sua **ID de reserva**. A captura de tela a seguir mostra os campos relacionados à reserva de capacidade reservada do Banco de Dados SQL.

![A captura de tela mostra detalhes de um arquivo C S V para capacidade reservada.](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. A **ReservationId** no campo **Informações Adicionais** é a reserva de capacidade reservada do Banco de Dados SQL que é aplicada ao recurso de Banco de Dados SQL.
2. **ConsumptionMeter** é o ID do medidor para o recurso Banco de Dados SQL.
3. O **Id do medidor** é o medidor de reserva. O custo deste medidor é $0. Quaisquer recursos de banco de dados SQL que se qualificam para o desconto de reserva mostra essa ID de medidor no arquivo CSV.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são Reservas do Azure?](save-compute-costs-reservations.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Banco de Dados SQL do Azure](../../azure-sql/database/reserved-capacity-overview.md)
- [Gerenciar Reservas do Azure](manage-reserved-vm-instance.md)
- [Entender como o desconto de reserva é aplicado](../manage/understand-vm-reservation-charges.md)
- [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)
- [Custos de software do Windows não estão incluídos nas reservas](reserved-instance-windows-software-costs.md)