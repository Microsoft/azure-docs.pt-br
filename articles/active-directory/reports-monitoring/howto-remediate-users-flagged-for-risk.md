---
title: Usuários sinalizados para risco no portal de Azure Active Directory | Microsoft Docs
description: Saiba mais sobre os usuários sinalizados para o relatório de risco na segurança no portal do Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23bd7a26bc6215f50a2860ab4aac84f1b46301f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92308955"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Corrigir usuários sinalizados como de risco no portal do Azure Active Directory

Com os relatórios de segurança no Active Directory Domain Services (Azure AD), você pode avaliar a probabilidade de contas de usuário comprometidas em seu ambiente. Um usuários sinalizado como de risco é um indicador de que uma conta de usuário pode ter sido comprometida.

A Microsoft está empenhada em ajudar a manter seus ambientes protegidos. Como parte desse compromisso, a Microsoft monitora continuamente atividades incomuns ou consistentes com padrões de ataques conhecidos. 

Se atividades incomuns que possam indicar acesso não autorizado a algumas das contas de seus usuários forem detectadas, você receberá notificações para que você possa realizar uma ação. Isso não significa que os sistemas da Microsoft tem sido comprometidos.

## <a name="access-the-users-flagged-for-risk-report"></a>Acesse os usuários sinalizados para o relatório de risco

Você pode revisar usuários sinalizados para risco por meio do [usuário no relatório de risco](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) no portal do Azure. Se você não tiver o Azure AD, poderá se inscrever gratuitamente em [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD) . 

Usuários sinalizados como risco, você pode executar as seguintes ações para cada usuário:

- Gerar senha temporária
- Exigir que o usuário redefina a senha de forma segura na próxima vez que ele se conectar
- Ignorar o risco de usuário sem realizar nenhuma ação de correção.

Para obter mais informações, consulte [Usuários sinalizados para o relatório de segurança de risco](../identity-protection/overview-identity-protection.md).

### <a name="azure-ad-subscription-for-microsoft-365-customers"></a>Assinatura do Azure AD para clientes Microsoft 365

Você também pode usar suas credenciais de Microsoft 365 para acessar o **centro de administração do Azure**. Após ativar seu acesso ao Azure AD, você será redirecionado para o portal do Azure AD. No nível de assinatura básica, a quantidade de detalhes fornecida nos relatórios é limitada. Outras análises e dados estão disponíveis para assinantes Premium do Azure.

Para acessar os **usuários sinalizados para** relatórios de risco no centro de administração Microsoft 365:

1.  No menu de navegação à esquerda, selecione **Centros de administração**. 
2.  Selecione **Microsoft Azure Active Directory**.
3.  Entre no **centro de administração do Azure Active Directory**.
4.  Se um banner for exibido na parte superior da página que diz **Confira o novo portal**, selecione o link.
4.  No menu de navegação à esquerda, selecione **Active Directory Domain Services**. 
5.  No painel de navegação, selecione **usuários sinalizados para risco** da seção **segurança**.

## <a name="remediation-actions"></a>Ações de correção

Execute as seguintes ações para ajudar a corrigir as contas afetadas e proteger seu ambiente:

1.  [Valide as informações corretas](https://aka.ms/MFAValid) para autenticação de vários fatores e redefinição de senha de autoatendimento. 
2.  [Habilite a autenticação multifator](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication) para todos os usuários. 
3.  Use esta [script de correção](https://aka.ms/remediate) para cada conta afetada, para executar automaticamente as etapas a seguir: 

    a. Redefina a senha para proteger a conta e elimine as sessões ativas.

    b. Remover representantes de caixa de correio.

    c. Desabilitar regras de encaminhamento de mensagens para domínios externos.

    d. Remover a propriedade de encaminhamento de email global na caixa de correio.

    e. Habilitar a MFA na conta do usuário.

    f. Definir a complexidade da senha na conta como alta.

    g. Habilitar auditoria na caixa de correio.

    h. Produza um log de auditoria para o administrador revisar.

4. Investigue seu locatário de Microsoft 365 e outra infraestrutura de ti, incluindo uma revisão de todas as configurações de locatário, contas de usuário e definições de configuração por usuário para uma possível modificação. Verifique se há indicadores de métodos de persistência e indicadores de que um invasor possa se aproveitado de alguma forma para obter credenciais de VPN ou acesso a outros recursos organizacionais. 

5.  Como parte da sua investigação, considere se você deve notificar as autoridades do governo, incluindo a aplicação da lei.

Além disso, você deve:

- Leia e implemente essas [diretrizes sobre como lidar com atividades incomuns](/archive/blogs/office365security/how-to-fix-a-compromised-hacked-microsoft-office-365-account). 
- [ Ative o pipeline de auditoria ](/archive/blogs/office365security/using-office-365-activity-data-to-improve-your-cybersecurity-stance-and-capability) para ajudá-lo a analisar a atividade em seu locatário. Depois de concluído, seu armazenamento de auditoria começa a ser preenchido com logs de atividades. Nesse ponto, você também pode aproveitar o recurso de pesquisa e investigação do [Security e Compliance Center](https://aka.ms/sccsearch). 
- Examine as permissões de representante e as regras de encaminhamento para todas as suas caixas de correio de email. Você pode usar este [script do PowerShell](https://aka.ms/delegateforwardrules) para executar a tarefa. 

## <a name="next-steps"></a>Próximas etapas

* [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md)
* [Usuários sinalizados por risco](../identity-protection/overview-identity-protection.md)