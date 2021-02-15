---
title: Solucionar problemas de utilização de reserva do Azure
description: Este artigo ajuda você a entender e solucionar problemas das reservas do Azure que mostram nenhuma utilização ou utilização 0 (zero) no portal do Azure. A utilização que não corresponde também é explicada.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: 9d5706843e8131110566ad8f955415b6db29f7ba
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207420"
---
# <a name="troubleshoot-reservation-utilization"></a>Solucionar problemas de utilização de reserva

Este artigo ajuda você a entender e solucionar problemas das reservas do Azure que mostram nenhuma utilização ou utilização 0 (zero) no portal do Azure. A utilização que não corresponde também é explicada.

## <a name="symptoms"></a>Sintomas

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até **Reservas** .
1. Na lista de reservas, procure a quantidade de utilização de uma reserva na coluna **Utilização (%)** . Pode ser 0%.
1. Selecione a reserva.
1. Na página de visão geral da reserva, o percentual usado no grafo pode não corresponder ao valor mostrado na lista de reserva.

## <a name="cause"></a>Causa

A coluna **Utilização (%)** no portal do Azure mostra o valor do dia atual. O valor é calculado conforme os dados de uso chegam de onde os recursos são executados. O Azure utiliza o uso para calcular o percentual de utilização.

Alguns recursos relatam o uso mais lento do que outros. Além disso, alguns tipos de produtos, como Bancos de Dados SQL, são lentos para reportar os dados de uso deles.

A latência pode fazer o cálculo de utilização mostrar valores mais baixos do que o uso real. A diferença é perceptível no limite diário. Nesses casos, se o Azure não obtiver dados de uso de quatro a oito horas, ele calculará um valor de 0%. O valor de 0% é mostrado porque os dados de uso não chegaram, e parece que a reserva não está aplicando um benefício a nenhum recurso.

Conforme os dados de uso chegam, o valor é alterado para o percentual correto. Quando todos os dados de uso são coletados, o valor correto é determinado e exibido com precisão no grafo.

## <a name="solution"></a>Solução

Se você descobrir que os valores de utilização não correspondem às suas expectativas, examine o grafo para obter a melhor exibição de sua utilização real. Qualquer valor de ponto com mais de dois dias deve ser preciso. As médias de prazo mais longo de sete a 30 dias devem ser precisas.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre como gerenciar reservas, confira [Gerenciar reservas para recursos do Azure](manage-reserved-vm-instance.md).