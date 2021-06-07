---
title: Solucionar problemas de transferências de reserva do Azure entre locatários
description: Este artigo ajuda os proprietários de reserva a transferir um pedido de reserva de um locatário do Azure Active Directory (diretório) para outro.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 02/24/2021
ms.openlocfilehash: 79473d57cc7504e7e6ef4ef68ba0cee74203f62b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055795"
---
# <a name="troubleshoot-reservation-transfers-between-tenants"></a>Solucionar problemas de transferências de reserva entre locatários

Este artigo ajuda os proprietários de reserva a transferir um pedido de reserva de um locatário do Azure Active Directory (diretório) para outro. Quando você altera o diretório de um pedido de reserva, ele remove qualquer acesso do RBAC do Azure para o pedido de reserva e reservas dependentes. Somente você terá acesso após a alteração. Alterar o diretório não altera a propriedade da cobrança do pedido de reserva. O diretório é alterado para o pedido de reserva pai e as reservas dependentes.

Uma troca e cancelamento da reserva não são necessários para a transferência entre locatários.

Depois de transferir uma reserva para outro locatário, talvez você também queira adicionar proprietários adicionais à reserva. Para obter mais informações, confira [Quem pode gerenciar uma reserva por padrão](view-reservations.md#who-can-manage-a-reservation-by-default).

Quando você transfere um pedido de reserva, todas as reservas no pedido são transferidas com ela.

## <a name="transfer-a-reservation"></a>Transferir uma reserva

Use as etapas a seguir para transferir um pedido de reserva e as reservas dependentes para outro locatário.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Se você não for um administrador de cobrança, mas for um proprietário de reserva, navegue até **Reservas** e pule para a etapa 6.
1. Navegue para **Gerenciamento de Custos + Cobrança**.
    - Se você for um administrador de EA, no menu à esquerda, selecione **Escopos de cobrança** e, na lista de escopos de cobrança, selecione um.
    - Se você for proprietário do perfil de cobrança do Contrato de Cliente da Microsoft, no menu à esquerda, selecione **Perfis de cobrança**. Na lista de perfis de cobrança, selecione um.
1. No menu à esquerda, selecione **Transações de reserva**. A lista de transações de reserva é mostrada.
1. Uma faixa na parte superior da página lê *Agora os administradores de cobrança podem gerenciar reservas. Clique aqui para gerenciar as reservas.* Selecione a faixa.
1. A lista completa de reservas para seu registro ou perfil de cobrança do EA é mostrada.
1. Selecione a reserva que deseja transferir.
1. Nos detalhes da reserva, selecione a ID do pedido de reserva.
1. No pedido da reserva, selecione **Alterar diretório**.
1. No painel Alterar diretório, selecione o diretório do Azure AD para o qual você deseja transferir a reserva e selecione **Confirmar**.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre reservas, confira [O que são as Reservas do Azure?](save-compute-costs-reservations.md).