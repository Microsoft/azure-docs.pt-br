---
title: "Notificações de Relatórios do Active Directory do Azure"
description: "Como usar as notificações de relatórios do Active Directory do Azure para entradas suspeitas."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ae6d4b0e-5931-4cb3-98bf-9be91b381c92
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: dhanyahk;markvi
ms.custom: oldportal
ms.reviewer: dhanyahk
ms.openlocfilehash: f4632bd2af802b10c8c64972e8c605d7ad7c0eaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-reporting-notifications"></a>Notificações de Relatórios do Active Directory do Azure
## <a name="what-reports-generate-email-notifications"></a>Quais relatórios geram notificações por email
Neste momento, apenas o relatório de atividade de entrada irregular dispara as notificações por email.

## <a name="what-is-an-irregular-sign-in"></a>O que é uma "Entrada irregular"?
Entradas irregulares são aquelas que foram identificadas por nossos algoritmos de aprendizado de máquina, com base em uma condição de “viagem impossível” combinada a um dispositivo e um local de entrada anômalos. Isso pode indicar que um hacker tentou entrar usando essa conta.

## <a name="who-receives-the-email-notifications"></a>Quem recebe as notificações por email?
O email é enviado para todos os administradores globais as quais foi atribuída uma licença do Active Directory Premium. Para garantir que ele seja entregue, podemos enviá-lo também ao endereço de Email alternativo dos administradores. Os administradores devem incluir aad-alerts-noreply@mail.windowsazure.com em suas listas de remetentes seguros, para que eles não deixem de ver o email.

## <a name="how-often-are-these-emails-sent"></a>Com que frequência esses emails são enviados?
O email será enviado se 10 novas atividades de entrada irregular ocorrerem nos últimos 30 dias, ou contando a partir da data em que o último email foi enviado, o período que for mais curto.

## <a name="how-do-i-access-the-report-mentioned-in-the-email"></a>Como acessar o relatório mencionado no email?
Ao clicar no link, você será redirecionado à página do relatório no portal clássico do Azure. Para acessar o relatório, você precisa ser ambos:

* Um administrador ou coadministrador de sua assinatura do Azure
* Um administrador global no diretório e ter uma licença do Active Directory Premium atribuída a você. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).

## <a name="can-i-turn-off-these-emails"></a>Posso desativar esses emails?
Sim. Para desligar as notificações relacionadas a entradas anômalas no Portal clássico do Azure, clique em **Configurar** e selecione **Desabilitado** na seção **Notificações**.

## <a name="whats-next"></a>O que vem a seguir
* Curioso sobre que relatórios de segurança, auditoria e atividade estão disponíveis? Verifique [Relatórios de segurança, auditoria e atividade do AD do Azure](active-directory-view-access-usage-reports.md)
* [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md)
* [Adicionar identidade visual da empresa às páginas de Entrada e do Painel de acesso](active-directory-add-company-branding.md)

