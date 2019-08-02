---
title: Usuários sinalizados para risco no portal de Azure Active Directory | Microsoft Docs
description: Saiba mais sobre os usuários sinalizados para o relatório de risco na segurança no portal do Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30d02c5484ea4cce2953eac6b1b7b26a17c142bc
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276562"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Corrigir usuários sinalizados como de risco no portal do Azure Active Directory

Com os relatórios de segurança no Active Directory Domain Services (Azure AD), você pode avaliar a probabilidade de contas de usuário comprometidas em seu ambiente. Um usuários sinalizado como de risco é um indicador de que uma conta de usuário pode ter sido comprometida.

A Microsoft está empenhada em ajudar a manter seus ambientes protegidos. Como parte desse compromisso, a Microsoft monitora continuamente atividades incomuns ou consistentes com padrões de ataques conhecidos. 

Se atividades incomuns que possam indicar acesso não autorizado a algumas das contas de seus usuários forem detectadas, você receberá notificações para que você possa realizar uma ação. Isso não significa que os sistemas da Microsoft tem sido comprometidos.

## <a name="access-the-users-flagged-for-risk-report"></a>Acesse os usuários sinalizados para o relatório de risco

Você pode revisar usuários sinalizados para risco por meio do [usuário no relatório de risco](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) no portal do Azure. Se você não tiver o Microsoft Azure Active Directory, poderá se inscrever gratuitamente em [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD). 

Usuários sinalizados como risco, você pode executar as seguintes ações para cada usuário:

- Gerar senha temporária
- Exigir que o usuário redefina a senha de forma segura na próxima vez que ele se conectar
- Ignorar o risco de usuário sem realizar nenhuma ação de correção.

Para obter mais informações, consulte [Usuários sinalizados para o relatório de segurança de risco](concept-user-at-risk.md).

### <a name="azure-ad-subscription-for-office-365-customers"></a>Assinatura do Azure AD para clientes do Office 365

Você também pode usar suas credenciais do Office 365 para acessar o **Azure Admin Center**. Após ativar seu acesso ao Azure AD, você será redirecionado para o portal do Azure AD. No nível de assinatura básica, a quantidade de detalhes fornecida nos relatórios é limitada. Outras análises e dados estão disponíveis para assinantes Premium do Azure.

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
2.  [Ative a autenticação multi fator](https://aka.ms/MFAuth) para todos os usuários. 
3.  Use esta [script de correção](https://aka.ms/remediate) para cada conta afetada, para executar automaticamente as etapas a seguir: 

    a. Redefina a senha para proteger a conta e elimine as sessões ativas.

    b. Remover representantes de caixa de correio.

    c. Desabilitar regras de encaminhamento de mensagens para domínios externos.

    d. Remover a propriedade de encaminhamento de email global na caixa de correio.

    e. Habilitar a MFA na conta do usuário.

    f. Definir a complexidade da senha na conta como alta.

    g. Habilitar auditoria na caixa de correio.

    h. Produza um log de auditoria para o administrador revisar.

4. Investigue o seu locatário do Office 365 e outras infraestruturas de TI, incluindo uma revisão de todas as configurações de locatário, contas de usuário e configurações por usuário para ver as modificações possíveis. Verifique se há indicadores de métodos de persistência e indicadores de que um invasor possa se aproveitado de alguma forma para obter credenciais de VPN ou acesso a outros recursos organizacionais. 

5.  Como parte da sua investigação, considere se você deve notificar as autoridades do governo, incluindo a aplicação da lei.

Além disso, você deve:

- Leia e implemente esta orientação [ sobre o endereçamento de atividades incomuns ](https://aka.ms/fixaccount). 
- [ Ative o pipeline de auditoria ](https://aka.ms/improvesecurity) para ajudá-lo a analisar a atividade em seu locatário. Depois de concluído, seu armazenamento de auditoria começa a ser preenchido com logs de atividades. Nesse ponto, você também pode aproveitar o recurso de pesquisa e investigação do [Security e Compliance Center](https://aka.ms/sccsearch). 
- Use este script [para habilitar a auditoria de caixa de correio](https://aka.ms/mailboxaudit1) para todas as suas contas. 
- Examine as permissões de representante e as regras de encaminhamento para todas as suas caixas de correio de email. Você pode usar este [script do PowerShell](https://aka.ms/delegateforwardrules) para executar a tarefa. 

## <a name="next-steps"></a>Próximas etapas

* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
* [Usuários sinalizados para risco](concept-user-at-risk.md)
