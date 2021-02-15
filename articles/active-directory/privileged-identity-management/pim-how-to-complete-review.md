---
title: Concluir a revisão de acesso das funções do Azure AD no PIM – Azure AD | Microsoft Docs
description: Saiba como concluir uma revisão de acesso das funções do Azure AD no Azure AD Privileged Identity Management (PIM) e exibir os resultados
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa7e8089fbbf2ee653100a05383fdbdc877ffda4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84742209"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Concluir uma revisão de acesso das funções do Azure AD no Privileged Identity Management

Os administradores de função com privilégios podem examinar o acesso privilegiado quando uma [revisão de acesso tiver sido iniciada](pim-how-to-start-security-review.md).  Privileged Identity Management (PIM) enviará automaticamente um email aos usuários em sua organização do Azure Active Directory (Azure AD) solicitando que eles revisem seu acesso. Se um usuário não tiver recebido um email, você poderá enviar para ele as instruções sobre [como executar uma revisão de acesso](pim-how-to-perform-security-review.md).

Depois do fim do período da revisão de acesso, ou quando todos os usuários tiverem terminado a autorrevisão, siga as etapas neste artigo para gerenciar a revisão e ver os resultados.

## <a name="manage-access-reviews"></a>Gerenciar revisões de acesso

1. Vá para a [portal do Azure](https://portal.azure.com/) e selecione o serviço de **Azure ad Privileged Identity Management** em seu painel.
1. Clique na seção **Revisões de acesso** do painel.
1. Selecione a análise de acesso que você deseja gerenciar.

Na folha de detalhes da revisão de acesso, há várias opções para gerenciar essa revisão.

![Botões de revisão de acesso do Privileged Identity Management-captura de tela](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Lembrar

Se uma análise de acesso é configurada para que os usuários examinem a si mesmos, o botão **Lembrar** envia uma notificação.

### <a name="stop"></a>Parar

Todas as revisões de acesso têm uma data de término, mas você pode usar o botão **Parar** para concluí-las mais cedo. Se quaisquer usuários ainda não tiverem sido examinados até este momento, eles não poderão ser após você parar a análise. Não é possível reiniciar uma análise após ela ter sido interrompida.

### <a name="apply"></a>Aplicar

Após uma análise de acesso ser concluída, seja porque você atingiu a data de término ou a interrompeu manualmente, o botão **Aplicar** implementa o resultado da análise. Se o acesso de um usuário foi negado na análise, esta é a etapa que removerá sua atribuição de função.  

### <a name="export"></a>Exportação

Se você quiser aplicar os resultados da revisão de acesso manualmente, poderá exportar a revisão. O botão **Exportar** começará a baixar um arquivo CSV. Você pode gerenciar os resultados no Excel ou em outros programas que abrem arquivos CSV.

### <a name="delete"></a>Excluir

Se você não estiver mais interessado na revisão, exclua-a. O botão **excluir** remove a revisão do serviço de Privileged Identity Management.

> [!IMPORTANT]
> Não será necessário confirmar essa alteração destrutiva, portanto, verifique se você deseja excluir essa revisão.

## <a name="next-steps"></a>Próximas etapas

- [Iniciar uma revisão de acesso para as funções do Azure AD no Privileged Identity Management](pim-how-to-start-security-review.md)
- [Executar uma revisão de acesso das minhas funções do Azure AD no Privileged Identity Management](pim-how-to-perform-security-review.md)
