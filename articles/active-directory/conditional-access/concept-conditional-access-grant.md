---
title: Conceder controles na política de acesso condicional-Azure Active Directory
description: O que são controles Grant em uma política de acesso condicional do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d01a750948f8e3c264b9bcffdaad3ae72fa40ac0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579103"
---
# <a name="conditional-access-grant"></a>Acesso condicional: Grant

Em uma política de acesso condicional, um administrador pode fazer uso de controles de acesso para conceder ou bloquear o acesso a recursos.

![Política de acesso condicional com um controle Grant que exige autenticação multifator](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Acesso bloqueado

O bloco leva em conta quaisquer atribuições e impede o acesso com base na configuração da política de acesso condicional.

O bloco é um controle poderoso que deve ser atraente com o conhecimento apropriado. Políticas com instruções de bloco podem ter efeitos colaterais indesejados. Os testes e a validação adequados são vitais antes de habilitar em escala. Os administradores devem utilizar ferramentas como o [modo somente de relatório de acesso condicional](concept-conditional-access-report-only.md) e [a ferramenta de What If no acesso condicional](what-if-tool.md) ao fazer alterações.

## <a name="grant-access"></a>Conceder acesso

Os administradores podem optar por impor um ou mais controles ao conceder acesso. Esses controles incluem as seguintes opções: 

- [Exigir autenticação multifator (autenticação multifator do Azure AD)](../authentication/concept-mfa-howitworks.md)
- [Exigir que o dispositivo seja marcado como em conformidade (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Exigir um dispositivo ingressado no Azure AD híbrido](../devices/concept-azure-ad-join-hybrid.md)
- [Exigir um aplicativo cliente aprovado](app-based-conditional-access.md)
- [Requer política de proteção do aplicativo](app-protection-based-conditional-access.md)
- [Exigir alteração de senha](#require-password-change)

Quando os administradores optam por combinar essas opções, eles podem escolher os seguintes métodos:

- Exigir todos os controles selecionados (controle **e** controle)
- Exigir um dos controles selecionados (controle **ou** controle)

Por padrão, o acesso condicional requer todos os controles selecionados.

### <a name="require-multi-factor-authentication"></a>Exigir autenticação multifator

A seleção dessa caixa de seleção exigirá que os usuários executem a autenticação multifator do Azure AD. Mais informações sobre como implantar a autenticação multifator do Azure AD podem ser encontradas no artigo [planejando uma implantação da autenticação multifator do Azure ad baseada em nuvem](../authentication/howto-mfa-getstarted.md).

O [Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-overview) cumpre o requisito de autenticação multifator em políticas de acesso condicional. 

### <a name="require-device-to-be-marked-as-compliant"></a>Exigir que o dispositivo seja marcado como em conformidade

As organizações que implantaram Microsoft Intune podem usar as informações retornadas de seus dispositivos para identificar os dispositivos que atendem aos requisitos de conformidade específicos. Essas informações de conformidade de política são encaminhadas do Intune para o Azure AD, em que o acesso condicional pode tomar decisões para conceder ou bloquear o acesso a recursos. Para obter mais informações sobre políticas de conformidade, consulte o artigo [definir regras em dispositivos para permitir o acesso a recursos em sua organização usando o Intune](/intune/protect/device-compliance-get-started).

Um dispositivo pode ser marcado como compatível pelo Intune (para qualquer sistema operacional do dispositivo) ou por um sistema MDM de terceiros para dispositivos Windows 10. O JAMF pro é o único sistema MDM de terceiros com suporte. Mais informações sobre a integração podem ser encontradas no artigo [integrar o JAMF pro ao Intune para fins de conformidade](/intune/protect/conditional-access-integrate-jamf).

Os dispositivos devem ser registrados no Azure AD antes que possam ser marcados como em conformidade. Mais informações sobre o registro de dispositivo podem ser encontradas no artigo [o que é uma identidade de dispositivo](../devices/overview.md).

### <a name="require-hybrid-azure-ad-joined-device"></a>Exigir um dispositivo ingressado no Azure AD híbrido

As organizações podem optar por usar a identidade do dispositivo como parte de sua política de acesso condicional. As organizações podem exigir que os dispositivos sejam ingressados no Azure AD híbrido usando essa caixa de seleção. Para obter mais informações sobre identidades de dispositivo, consulte o artigo [o que é uma identidade de dispositivo?](../devices/overview.md).

Ao usar o [fluxo OAuth do código do dispositivo](../develop/v2-oauth2-device-code.md), a condição exigir controle de concessão de dispositivo gerenciado ou estado do dispositivo não é suportada. Isso ocorre porque o dispositivo que executa a autenticação não pode fornecer o estado do dispositivo para o dispositivo que fornece um código e o estado do dispositivo no token está bloqueado para o dispositivo que executa a autenticação. Em vez disso, use o controle exigir autenticação multifator.

### <a name="require-approved-client-app"></a>Exigir um aplicativo cliente aprovado

As organizações podem exigir que uma tentativa de acesso aos aplicativos de nuvem selecionados precise ser feita de um aplicativo cliente aprovado. Esses aplicativos cliente aprovados dão suporte a [políticas de proteção de aplicativo do Intune](/intune/app-protection-policy) independentemente de qualquer solução de MDM (gerenciamento de dispositivo móvel).

Para aproveitar esse controle de concessão, o acesso condicional requer que o dispositivo seja registrado em Azure Active Directory, o que exige o uso de um aplicativo de agente. O aplicativo agente pode ser o Microsoft Authenticator para iOS ou o Microsoft Authenticator ou o portal da empresa da Microsoft para dispositivos Android. Se um aplicativo de agente não estiver instalado no dispositivo quando o usuário tentar se autenticar, o usuário será redirecionado para a loja de aplicativos apropriada para instalar o aplicativo agente necessário.

Essa configuração se aplica aos seguintes aplicativos iOS e Android:

- Proteção de Informações do Microsoft Azure
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Power Automate
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype for Business
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard
- Administrador do Microsoft 365

**Comentários**

- Os aplicativos cliente aprovados fornecem suporte ao recurso de gerenciamento de aplicativo móvel Intune.
- O requisito **Exigir o aplicativo do cliente aprovado**:
   - Fornece suporte apenas para iOS e Android para condição de plataforma de dispositivo.
   - Um aplicativo de agente é necessário para registrar o dispositivo. O aplicativo agente pode ser o Microsoft Authenticator para iOS ou o Microsoft Authenticator ou o portal da empresa da Microsoft para dispositivos Android.
- O acesso condicional não pode considerar o Microsoft Edge no modo InPrivate em um aplicativo cliente aprovado.
- Usando o Azure Proxy de Aplicativo do AD para habilitar o Power BI aplicativo móvel para se conectar ao Servidor de Relatórios do Power BI local não tem suporte com políticas de acesso condicional que exigem o aplicativo Microsoft Power BI como um aplicativo cliente aprovado.

Consulte o artigo [como: exigir aplicativos cliente aprovados para acesso de aplicativo de nuvem com acesso condicional](app-based-conditional-access.md) para exemplos de configuração.

### <a name="require-app-protection-policy"></a>Requer política de proteção do aplicativo

Em sua política de acesso condicional, você pode exigir que uma [política de proteção de aplicativo do Intune](/intune/app-protection-policy) esteja presente no aplicativo cliente antes que o acesso esteja disponível para os aplicativos de nuvem selecionados. 

Para aproveitar esse controle de concessão, o acesso condicional requer que o dispositivo seja registrado em Azure Active Directory, o que exige o uso de um aplicativo de agente. O aplicativo agente pode ser o Microsoft Authenticator para iOS ou o portal da Empresa da Microsoft para dispositivos Android. Se um aplicativo agente não estiver instalado no dispositivo quando o usuário tentar se autenticar, o usuário será redirecionado para a loja de aplicativos para instalar o aplicativo agente.

Os aplicativos precisam ter o **SDK do Intune** com a **garantia de política** implementada e atender a determinados requisitos para dar suporte a essa configuração. Os desenvolvedores que implementam aplicativos com o SDK do Intune podem encontrar mais informações na documentação do SDK sobre esses requisitos.

Os seguintes aplicativos cliente foram confirmados para dar suporte a essa configuração:

- Microsoft Cortana
- Microsoft Edge
- Microsoft Excel
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Word
- Várias linhas para o Intune
- Nove emails calendário de & de email

> [!NOTE]
> Microsoft Teams, Microsoft Kaizala, Microsoft Skype for Business e Microsoft Visio não dão suporte à concessão de **política de proteção de aplicativo necessária** . Se você precisar que esses aplicativos funcionem, use a concessão de **aplicativos aprovados** por exclusividade. O uso da cláusula or entre as duas concessões não funcionará para esses três aplicativos.

**Comentários**

- Aplicativos para a política de proteção de aplicativo dão suporte ao recurso de gerenciamento de aplicativos móveis do Intune com proteção de política.
- Os requisitos da **política exigir proteção de aplicativo** :
    - Fornece suporte apenas para iOS e Android para condição de plataforma de dispositivo.
    - Um aplicativo de agente é necessário para registrar o dispositivo. No iOS, o aplicativo agente é Microsoft Authenticator e no Android, é Portal da Empresa do Intune aplicativo.

Consulte o artigo [como: exigir a política de proteção de aplicativo e um aplicativo cliente aprovado para acesso de aplicativo de nuvem com acesso condicional](app-protection-based-conditional-access.md) para exemplos de configuração.

### <a name="require-password-change"></a>Exigir alteração de senha 

Quando o risco do usuário é detectado, usando as condições da política de risco do usuário, os administradores podem optar por fazer com que o usuário altere a senha com segurança usando a redefinição de senha de autoatendimento do Azure AD. Se o risco do usuário for detectado, os usuários poderão executar uma redefinição de senha de autoatendimento para corrigir automaticamente, isso fechará o evento de risco do usuário para evitar ruídos desnecessários para os administradores. 

Quando um usuário for solicitado a alterar sua senha, primeiro será necessário concluir a autenticação multifator. Você desejará ter certeza de que todos os usuários se registraram para autenticação multifator, para que eles estejam preparados caso o risco seja detectado para sua conta.  

> [!WARNING]
> Os usuários devem ter se registrado previamente para redefinição de senha self-service antes de disparar a política de risco do usuário. 

Existem algumas restrições em vigor quando você configura uma política usando o controle de alteração de senha.  

1. A política deve ser atribuída a ' todos os aplicativos de nuvem '. Isso impede que um invasor use um aplicativo diferente para alterar a senha do usuário e redefinir o risco da conta, simplesmente entrando em um aplicativo diferente. 
1. Exigir alteração de senha não pode ser usado com outros controles, como exigir um dispositivo compatível.  
1. O controle de alteração de senha só pode ser usado com a condição de atribuição de usuário e grupo, condição de atribuição de aplicativo de nuvem (que deve ser definida como todos) e condições de risco do usuário. 

### <a name="terms-of-use"></a>Termos de uso

Se sua organização tiver criado termos de uso, opções adicionais poderão estar visíveis em conceder controles. Essas opções permitem que os administradores exijam a confirmação dos termos de uso como uma condição de acessar os recursos protegidos pela política. Mais informações sobre os termos de uso podem ser encontradas no artigo [Azure Active Directory termos de uso](terms-of-use.md).

## <a name="next-steps"></a>Próximas etapas

- [Acesso condicional: controles de sessão](concept-conditional-access-session.md)

- [Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

- [Modo somente relatório](concept-conditional-access-report-only.md)
