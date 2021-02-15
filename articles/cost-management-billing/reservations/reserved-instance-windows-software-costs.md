---
title: Custos do software de reservas do Azure
description: Saiba quais medidores de software do Windows não estão incluídos nos custos de Instância de Máquina Virtual Reservada.
author: yashesvi
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/28/2021
ms.author: banders
ms.openlocfilehash: 464e129314a369c14dcc6bc6c16eadf5f8bcf01f
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052982"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Custos do software do Windows não incluídos nas Instâncias de VM Reservadas do Azure

Os descontos da instância reservada da máquina virtual e da capacidade reservada do SQL se aplicam apenas aos custos de infraestrutura, e não aos custos de software. Se você usar a VM Windows e não tiver um Benefício Híbrido do Azure em suas instâncias de máquina virtual reservada, você será cobrado pelos medidores de software listados na seção a seguir. Para implantações de PaaS do SQL, o custo do IP continuará sendo cobrado usando o medidor separado se o Benefício Híbrido do Azure não for selecionado.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Medidores de software do Windows não está incluídos no custo de reserva

| MeterId | MeterName no arquivo de utilização | Utilizado por VM |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Reservation-Windows Svr Intermitente (1 Núcleo) | Série B |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Reservation-Windows Svr Intermitente (2 Núcleos) | Série B |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Reservation-Windows Svr Intermitente (4 Núcleos) | Série B |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Reservation-Windows Svr Intermitente (8 Núcleos) | Série B |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Reservation-Windows Svr (1 Núcleo) | Todas, exceto Série B |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Reservation-Windows Svr (2 Núcleos) | Todas, exceto Série B |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Reservation-Windows Svr (4 Núcleos) | Todas, exceto Série B |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Reservation-Windows Svr (6 Núcleos) | Todas, exceto Série B |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Reservation-Windows Svr (6 Núcleos) | Todas, exceto Série B |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Reservation-Windows Svr (12 Núcleos) | Todas, exceto Série B |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Reservation-Windows Svr (16 Núcleos) | Todas, exceto Série B |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Reservation-Windows Svr (20 Núcleos) | Todas, exceto Série B |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Reservation-Windows Svr (24 Núcleos) | Todas, exceto Série B |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Reservation-Windows Svr (32 Núcleos) | Todas, exceto Série B |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Reservation-Windows Svr (40 Núcleos) | Todas, exceto Série B |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Reservation-Windows Svr (64 Núcleos) | Todas, exceto Série B |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Reservation-Windows Svr (72 Núcleos) | Todas, exceto Série B |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Reservation-Windows Svr (128 Núcleos) | Todas, exceto Série B |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Reservation-Windows Svr (256 Núcleos) | Todas, exceto Série B |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Reservation-Windows Svr (96 Núcleos) | Todas, exceto Série B |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>Medidores de software de serviços de nuvem não estão incluídos no custo de reserva

| MeterId | MeterName no arquivo de utilização |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Licença de 1 vCPU dos Serviços de Nuvem|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Licença de 2 vCPU dos Serviços de Nuvem|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Licença de 4 vCPU dos Serviços de Nuvem|
|13103090-ca72-4825-ab12-7f16c4931d95|Licença de 8 vCPU dos Serviços de Nuvem|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Licença de 16 vCPU dos Serviços de Nuvem|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Licença de 20 vCPU dos Serviços de Nuvem|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|Licença de 32 vCPU dos Serviços de Nuvem|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|Licença de 64 vCPU dos Serviços de Nuvem|
|7a803026-244c-4659-834c-11e6b2d6b76f|Licença de 80 vCPU dos Serviços de Nuvem|

## <a name="get-rates-for-azure-meters"></a>Obter taxas para medidores do Azure

Você pode obter o custo de cada um dos medidores com a API de Preços de Varejo do Azure. Para obter informações sobre como obter as taxas de um medidor do Azure, confira [Visão geral dos Preços de Varejo do Azure](/rest/api/cost-management/retail-prices/azure-retail-prices).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as reservas do Azure, confira os seguintes artigos:

- [O que são reservas para o Azure?](save-compute-costs-reservations.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Gerenciar reservas do Azure](manage-reserved-vm-instance.md)
- [Entender como o desconto de reserva é aplicado](../manage/understand-vm-reservation-charges.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).