---
title: Lista de associação de grupo de download em massa-portal de Azure Active Directory | Microsoft Docs
description: Adicione usuários em massa no centro de administração do Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4055fb3bfdbcfd7e4a74d9290017a6193578146
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547689"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Fazer download de membros em massa de um grupo no Azure Active Directory

Usando o portal do Azure Active Directory (AD do Azure), você pode fazer o download em massa dos membros de um grupo em sua organização para um arquivo CSV (valores separados por vírgula).

## <a name="to-bulk-download-group-membership"></a>Para baixar em massa a associação ao grupo

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta de administrador de usuários na organização. Os proprietários do grupo também podem fazer o download em massa de membros de grupos que possuem.
1. No Azure AD, escolha **Grupos** > **Todos os grupos**.
1. Abra o grupo cuja associação você deseja baixar e, em seguida, selecione **Membros**.
1. Na página **Membros** , selecione **baixar Membros** para baixar um arquivo CSV listando os membros do grupo.

   ![O comando baixar Membros está na página de perfil do grupo](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Verificar status de download

É possível ver o status de todas as suas solicitações em massa pendentes na página **Resultados da operação em massa**.

[![Verifique o status na página resultados de operações em massa.](./media/groups-bulk-download-members/bulk-center.png)](./media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limites do serviço de download em massa

Cada atividade em massa para baixar uma lista de membros do grupo pode ser executada por até uma hora. Isso permite que você baixe uma lista de pelo menos 500.000 membros.

## <a name="next-steps"></a>Próximas etapas

- [Importar membros do grupo em massa](groups-bulk-import-members.md)
- [Remover membros do grupo em massa](groups-bulk-download-members.md)
