---
title: "Gerenciar os programas e os controles para análises de acesso do Azure AD | Microsoft Docs"
description: "Você pode criar programas adicionais para cada iniciativa de governança, gerenciamento de riscos e conformidade da sua organização para coletar e organizar as revisões de acesso do Azure Active Directory como controles."
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: f6d7c2a1a3e3bf78b568f2f6b079476e403b9911
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-programs-and-their-controls"></a>Gerenciar programas e seus controles 

O Azure AD inclui revisões de acesso de membros do grupo e acesso a aplicativos, que são exemplos de controles garantindo a supervisão para quem tem acesso. Esses controles permitem que as organizações lidem eficientemente com os requisitos de governança, gerenciamento de riscos e conformidade.  

## <a name="how-to-manage-programs-and-their-controls"></a>Como gerenciar programas e seus controles
Você pode simplificar o rastreamento e coletar revisões de acesso para finalidades diferentes, organizando-as em programas.  Cada revisão de acesso pode ser vinculada a um programa para que, durante a preparação de relatórios para um auditor, somente as revisões de acesso no escopo de uma determinada iniciativa fiquem visíveis.

Para ver uma lista de programas, vá para a [página revisões de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) e altere para a guia **Programas**.

Há um programa, "Programa padrão", sempre presente.  Se você estiver em uma função de administrador global, poderá criar programas adicionais.  Por exemplo, você pode optar por ter um programa para cada iniciativa de conformidade ou meta de negócios.

Se você não precisar mais de um programa e ele não tiver nenhum controle vinculado a ele, você poderá excluí-lo.

## <a name="next-steps"></a>Próximas etapas

- [Como criar uma revisão de acesso para os membros de um grupo ou para o acesso a um aplicativo](active-directory-azure-ad-controls-create-access-review.md)

