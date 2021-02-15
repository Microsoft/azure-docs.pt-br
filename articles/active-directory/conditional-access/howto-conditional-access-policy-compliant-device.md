---
title: Acesso condicional-exigir dispositivos em conformidade-Azure Active Directory
description: Criar uma política de acesso condicional personalizada para exigir dispositivos em conformidade
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c98269f9851272e8caa9b26ae0c57ed13e9a99f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89049121"
---
# <a name="conditional-access-require-compliant-devices"></a>Acesso condicional: exigir dispositivos em conformidade

As organizações que implantaram Microsoft Intune podem usar as informações retornadas de seus dispositivos para identificar os dispositivos que atendem aos requisitos de conformidade, como:

* Exigindo um PIN para desbloquear
* Exigindo criptografia do dispositivo
* Exigindo uma versão mínima ou máxima do sistema operacional
* Exigir um dispositivo não está desbloqueado ou com raiz

Essas informações de conformidade de política são encaminhadas ao Azure AD, em que o acesso condicional pode tomar decisões para conceder ou bloquear o acesso a recursos. Mais informações sobre as políticas de conformidade do dispositivo podem ser encontradas no artigo [definir regras em dispositivos para permitir o acesso a recursos em sua organização usando o Intune](/intune/protect/device-compliance-get-started)

## <a name="create-a-conditional-access-policy"></a>Criar uma política de Acesso Condicional

As etapas a seguir ajudarão a criar uma política de acesso condicional para exigir que os dispositivos que acessam os recursos sejam marcados como em conformidade com as políticas de conformidade do Intune da sua organização.

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições**, selecione **Usuários e grupos**
   1. Em **Incluir**, selecione **Todos os usuários**.
   1. Em **Excluir**, selecione **Usuários e grupos** e escolha o acesso de emergência ou as contas de interrupção da sua organização. 
   1. Selecione **Concluído**.
1. Em **aplicativos de nuvem ou ações**  >  **incluem**, selecione **todos os aplicativos de nuvem**.
   1. Se você precisar excluir aplicativos específicos de sua política, poderá selecioná-los na guia **excluir** em **selecionar aplicativos de nuvem excluídos** e escolher **selecionar**.
   1. Selecione **Concluído**.
1. Em **condições**  >  **aplicativos de cliente (versão prévia)**  >  **Selecione os aplicativos cliente aos quais essa política será aplicada**, deixe todos os padrões selecionados e selecione **concluído**.
1. Em **Access controls**  >  **concessão**de controles de acesso, selecione **exigir que o dispositivo seja marcado como compatível**.
   1. Selecione **Selecionar**.
1. Confirme suas configurações e defina **Habilitar política** como **Ativado**.
1. Selecione **Criar** para criar e habilitar sua política.

> [!NOTE]
> Você pode registrar seus novos dispositivos no Intune mesmo se selecionar exigir que o **dispositivo seja marcado como compatível** para **todos os usuários** e **todos os aplicativos de nuvem** usando as etapas acima. **Exigir que o dispositivo seja marcado como controle em conformidade** não bloqueie o registro do Intune. 

### <a name="known-behavior"></a>Comportamento conhecido

No Windows 7, iOS, Android, macOS e alguns navegadores da Web de terceiros, o Azure AD identifica o dispositivo usando um certificado de cliente que é provisionado quando o dispositivo é registrado no Azure AD. Quando um usuário entra pela primeira vez por meio do navegador, é solicitado que o usuário selecione o certificado. O usuário final deve selecionar esse certificado antes que possa continuar a usar o navegador.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto usando o modo somente relatório de Acesso Condicional](howto-conditional-access-insights-reporting.md)

[Simular comportamento de entrada usando a ferramenta What If de Acesso Condicional](troubleshoot-conditional-access-what-if.md)

[As políticas de conformidade do dispositivo funcionam com o Azure AD](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
