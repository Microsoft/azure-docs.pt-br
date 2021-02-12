---
title: Padrões de segurança do Azure Active Directory
description: Políticas de padrão de segurança que ajudam a proteger as organizações contra ataques comuns ao Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 9c912b7122a40ae39bcbb703e1387af25fff2bc8
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029899"
---
# <a name="what-are-security-defaults"></a>O que são os padrões de segurança?

Gerenciar a segurança pode ser difícil com ataques comuns relacionados à identidade, como o spray de senha, reprodução e phishing se tornando cada vez mais popular. Com os padrões de segurança, fica mais fácil proteger sua organização contra esses ataques devido a configurações de segurança pré-definidas que:

- Exigir que todos os usuários se registrem para a autenticação multifator do Azure AD.
- Exigem que os administradores executem a autenticação multifator.
- Bloqueiem protocolos de autenticação herdados.
- Exigem que os usuários executem a autenticação multifator quando necessário.
- Que protejam atividades privilegiadas como o acesso ao portal do Azure.

![Captura de tela do portal do Azure com a alternância para habilitar os padrões de segurança](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
Há mais detalhes sobre por que os padrões de segurança estão sendo disponibilizados podem ser encontrados na postagem do blog de Alex Weinert, [Apresentação dos padrões de segurança](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414).

## <a name="availability"></a>Disponibilidade

A Microsoft está disponibilizando padrões de segurança para todos. A meta é garantir que todas as organizações tenham um nível básico de segurança habilitado sem custos adicionais. A ativação dos padrões de segurança é feita no portal do Azure. Caso seu locatário tenha sido criado em 22 de outubro de 2019 ou após essa data, é possível que os padrões de segurança já estejam habilitados em seu locatário. Em um esforço para proteger todos os nossos usuários, os padrões de segurança estão sendo distribuídos para todos os novos locatários que sejam criados.

### <a name="whos-it-for"></a>Para quem eles são?

- Caso sua organização deseje aumentar sua postura de segurança, mas não sabe como ou onde começar, os padrões de segurança são adequados para você.
- Caso sua organização use o tipo de preço gratuito de licenciamento do Azure Active Directory, os padrões de segurança são adequados para você.

### <a name="who-should-use-conditional-access"></a>Quem deve usar o Acesso Condicional?

- Caso sua organização esteja atualmente usando políticas de Acesso Condicional para reunir sinais, tomar decisões e impor políticas organizacionais, os padrões de segurança provavelmente não são adequados para você. 
- Caso sua organização com licenças do Azure Active Directory Premium, os padrões de segurança provavelmente não são adequados para você.
- Caso sua organização tenha requisitos de segurança complexos, você deve cogitar o uso do Acesso Condicional.

## <a name="policies-enforced"></a>Políticas impostas

### <a name="unified-multi-factor-authentication-registration"></a>Registro unificado da Autenticação Multifator

Todos os usuários em seu locatário devem se registrar para a autenticação multifator (MFA) na forma da autenticação multifator do Azure AD. Os usuários têm 14 dias para se registrarem na autenticação multifator do Azure AD usando o aplicativo Microsoft Authenticator. Depois que os 14 dias tiverem passado, o usuário não conseguirá entrar até que o registro seja concluído. O período de 14 dias de um usuário começa após sua primeira entrada interativa bem-sucedida depois de habilitar os padrões de segurança.

### <a name="protecting-administrators"></a>Administradores de proteção

Usuários com acesso privilegiado aumentaram o acesso ao seu ambiente. Devido à capacidade que essas contas têm, devem ser tratadas com cuidado especial. Um método comum para melhorar a proteção de contas privilegiadas é exigir uma forma mais forte de verificação de para entrar. No Azure AD, você pode obter uma verificação de conta mais forte exigindo a autenticação multifator.

Após a conclusão do registro com a autenticação multifator do Azure AD, serão necessárias as nove funções de administrador do Azure AD a seguir para executar autenticação adicional sempre que entrarem:

- Administrador global
- Administrador do SharePoint
- Administrador do Exchange
- Administrador de acesso condicional
- Administrador de segurança
- Administrador de assistência técnica
- Administrador de cobrança
- Administrador de usuários
- Administrador de autenticação

### <a name="protecting-all-users"></a>Proteção a todos os usuários

Tendemos a imaginar que as contas de administrador são as únicas que precisam de camadas extras de autenticação. Os administradores têm amplo acesso a informações confidenciais e podem fazer alterações nas configurações de toda a assinatura. Mas os invasores costumam ter os usuários finais como alvo. 

Depois que esses invasores conseguirem acesso, eles poderão solicitar acesso a informações privilegiadas em nome do titular da conta original. Eles conseguem até mesmo baixar o diretório inteiro para executar um ataque de phishing em toda a sua organização. 

Um método comum para melhorar a proteção para todos os usuários é exigir uma forma mais forte de verificação da conta para todos, como a MFA. Depois que os usuários concluírem o registro da MFA, eles serão solicitados a usar essa autenticação adicional sempre que necessário. Essa funcionalidade protege todos os aplicativos registrados no Azure AD, incluindo aplicativos SaaS.

### <a name="blocking-legacy-authentication"></a>Bloqueio da autenticação herdada

Para fornecer aos usuários acesso fácil aos aplicativos na nuvem, o Azure AD tem suporte a uma variedade de protocolos de autenticação, incluindo a autenticação herdada. *Autenticação herdada* é um termo que se refere a uma solicitação de autenticação feita por:

- Clientes que não usam uma autenticação moderna (por exemplo, um cliente do Office 2010).
- Qualquer cliente que use protocolos de email mais antigos, como IMAP, SMTP ou POP3.

Hoje, a maior parte de tentativas de entrada comprometidas é proveniente da autenticação herdada. A autenticação herdada não tem suporte para a MFA. Assim, mesmo que você tenha uma política de MFA habilitada em seu diretório, um invasor pode ignorá-la ao se autenticar usando um protocolo mais antigo. 

Depois que os padrões de segurança forem habilitados em seu locatário, todas as solicitações de autenticação feitas por um protocolo mais antigo serão bloqueadas. Os padrões de segurança bloqueiam a autenticação básica Exchange Active Sync.

> [!WARNING]
> Antes de habilitar os padrões de segurança, garanta que os administradores não estejam usando protocolos de autenticação mais antigos. Para obter mais informações, consulte [Como deixar de usar a autenticação herdada](concept-fundamentals-block-legacy-authentication.md).

- [Como configurar um dispositivo ou aplicativo multifuncional para enviar email usando Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)

### <a name="protecting-privileged-actions"></a>Proteção a ações privilegiadas

As organizações usam diversos serviços do Azure gerenciados por meio da API do Azure Resource Manager, incluindo:

- Portal do Azure 
- Azure PowerShell 
- CLI do Azure

O uso do Azure Resource Manager para gerenciar seus serviços é uma ação altamente privilegiada. O Azure Resource Manager pode alterar configurações em todo o locatário, como configurações de serviço e cobranças de assinatura. A autenticação de fator único é vulnerável a diversos tipos de ataques, como pulverização de senha e phishing. 

É importante fazer a verificação da identidade dos usuários que desejam acessar o Azure Resource Manager e atualizar as configurações. Para fazer a verificação de identidade, você deve exigir uma autenticação adicional antes de permitir o acesso.

Depois de habilitar os padrões de segurança em seu locatário, qualquer usuário que estiver acessando o portal do Azure, o Azure PowerShell ou a CLI do Azure precisará realizar a autenticação adicional. Essa política se aplica a todos os usuários que estejam acessando o Azure Resource Manager, sejam eles administradores ou usuários. 

> [!NOTE]
> Os locatários do Exchange Online anteriores a 2017 têm a autenticação moderna desabilitada por padrão. Para evitar que possa ocorrer um loop de logon durante a autenticação por meio desses locatários, você deve [habilitar a autenticação moderna](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

> [!NOTE]
> A conta de sincronização do Azure AD Connect é excluída dos padrões de segurança e não serão solicitados o registro ou a execução da autenticação multifator. As organizações não devem usar essa conta para outras finalidades.

## <a name="deployment-considerations"></a>Considerações de implantação

As considerações adicionais a seguir estão relacionadas à implantação dos padrões de segurança.

### <a name="authentication-methods"></a>Métodos de autenticação

Esses padrões de segurança gratuitos permitem o registro e o uso da autenticação multifator do Azure AD **usando apenas o aplicativo Microsoft Authenticator usando notificações**. O Acesso Condicional permite o uso de qualquer método de autenticação que o administrador escolha habilitar.

| Método | Padrões de segurança | Acesso Condicional |
| --- | --- | --- |
| Notificação pelo aplicativo móvel | X | X |
| O código de verificação do aplicativo móvel ou token de hardware | X** | X |
| Mensagem de texto para telefone |   | X |
| Ligue para o telefone |   | X |
| Senhas de aplicativo |   | X * * _ |

- _ * Os usuários podem usar códigos de verificação do aplicativo Microsoft Authenticator, mas só podem se registrar usando a opção de notificação.
- * * _ As senhas de aplicativo só estarão disponíveis no MFA por usuário com cenários de autenticação herdados somente se habilitadas pelos administradores.

### <a name="disabled-mfa-status"></a>Status desabilitado da MFA

Se sua organização for um usuário anterior da autenticação multifator baseada em usuário do Azure AD, não se assuste para não ver os usuários em um status _ *habilitado** ou **imposto** se você olhar para a página de status da autenticação multifator. **Disabled** é o status apropriado para os usuários que estão usando padrões de segurança ou a autenticação multifator do Azure AD com base no acesso condicional.

### <a name="conditional-access"></a>Acesso Condicional

Você pode usar o Acesso Condicional para configurar políticas semelhantes a padrões de segurança, mas com mais granularidade, incluindo exclusões de usuário, as quais não estão disponíveis em padrões de segurança. Caso esteja usando o Acesso Condicional e tiver políticas habilitadas desse recurso em seu ambiente, os padrões de segurança não estarão disponíveis para você. Caso tenha uma licença que forneça Acesso Condicional, mas não tenha políticas habilitadas desse recurso em seu ambiente, você poderá usar os padrões de segurança até habilitar as políticas de Acesso Condicional. É possível encontrar mais informações sobre o licenciamento do Azure AD podem na página de preços do [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

![Mensagem de aviso informando que você pode ter padrões de segurança ou Acesso Condicional, mas não ambos](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Veja alguns guias passo a passo sobre como você pode usar o Acesso Condicional para configurar políticas equivalentes para essas políticas habilitadas por padrões de segurança:

- [Exigir MFA para administradores](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Exigir MFA para o gerenciamento do Azure](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Bloquear a autenticação herdada](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Exigir MFA para todos os usuários](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Exigir registro do Azure ad MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) -requer Azure ad Identity Protection parte do Azure ad Premium P2.

## <a name="enabling-security-defaults"></a>Habilitar padrões de segurança

Para habilitar padrões de segurança no seu diretório:

1. Entre no  [portal do Azure](https://portal.azure.com) como administrador de segurança, administrador de Acesso Condicional ou administrador global.
1. Acesse  **Azure Active Directory** > **Propriedades**.
1. Selecione **Gerenciar padrões de segurança**.
1. Alterne a opção **Habilitar padrões de segurança** para **Sim**.
1. Clique em **Salvar**.

## <a name="disabling-security-defaults"></a>Desabilitar padrões de segurança

As organizações que optarem por implementar políticas de Acesso Condicional que substituam os padrões de segurança devem desabilitar os padrões de segurança. 

![Mensagem de aviso informando para desabilitar os padrões de segurança para habilitar o Acesso Condicional](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Para desabilitar padrões de segurança no seu diretório:

1. Entre no  [portal do Azure](https://portal.azure.com) como administrador de segurança, administrador de Acesso Condicional ou administrador global.
1. Acesse  **Azure Active Directory** > **Propriedades**.
1. Selecione **Gerenciar padrões de segurança**.
1. Alterne a opção **Habilitar padrões de segurança** para **Não**.
1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

[Políticas de acesso condicional comuns](../conditional-access/concept-conditional-access-policy-common.md)