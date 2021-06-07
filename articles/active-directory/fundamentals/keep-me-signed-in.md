---
title: Configurar o ' permanecer conectado? ' solicitar contas de Azure Active Directory
description: Saiba mais sobre como manter-me conectado (KMSI), que exibe a permanência conectada? avisar, como configurá-lo no portal de Azure Active Directory e como solucionar problemas de entrada.
services: active-directory
author: CelesteDG
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: fundamentals
ms.date: 06/05/2020
ms.author: celested
ms.reviewer: asteen, jlu, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: bed6bc43dfc15abf2bdf9f38a5de2240d348d6fb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89320249"
---
# <a name="configure-the-stay-signed-in-prompt-for-azure-ad-accounts"></a>Configurar o ' permanecer conectado? ' solicitar contas do Azure AD

Manter-me conectado (KMSI) exibe uma conexão **permanecer conectado?** depois que um usuário entrar com êxito. Se um usuário responder **Sim** para esse prompt, o serviço me manter conectado fornecerá um token de [atualização](../develop/developer-glossary.md#refresh-token)persistente. Para locatários federados, o prompt será exibido depois que o usuário for autenticado com êxito com o serviço de identidade federada.

O diagrama a seguir mostra o fluxo de entrada do usuário para um locatário gerenciado e um locatário federado e o novo prompt me mantenha-me conectado. Esse fluxo contém a lógica inteligente para que a opção **permanecer conectado?** não será exibida se o sistema de Machine Learning detectar uma entrada de alto risco ou uma entrada de um dispositivo compartilhado.

:::image type="content" source="./media/keep-me-signed-in/kmsi-workflow.png" alt-text="Diagrama mostrando o fluxo de entrada do usuário para um locatário gerenciado vs. federado":::

> [!NOTE]
> Configurar a opção manter-me conectado exige que você use as edições Azure Active Directory (Azure AD) Premium 1, Premium 2 ou Basic ou para ter uma licença de Microsoft 365. Para obter mais informações sobre licenciamento e edições, consulte [Inscrever-se no Azure AD Premium](active-directory-get-started-premium.md).<br><br>As edições Azure AD Premium e Basic estão disponíveis para clientes na China usando a instância Mundial do Azure AD. Atualmente, as edições Azure AD Premium e Básico não têm suporte no serviço do Azure operado pela 21Vianet na China. Para obter mais informações, fale conosco usando o [Fórum do Azure ad](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="configure-kmsi"></a>Configurar o KMSI

1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador Global para o diretório.
1. Selecione **Azure Active Directory**, selecione **identidade visual da empresa** e, em seguida, selecione **Configurar**.
1. Na seção **Configurações avançadas** , localize a **opção mostrar para permanecer conectado à** configuração.

   Essa configuração permite que você escolha se os usuários permanecem conectados ao Azure AD até que eles se desconectem explicitamente.
   * Se você escolher **não**, a opção **permanecer conectado?** ficará oculta depois que o usuário entrar com êxito e o usuário deverá entrar sempre que o navegador for fechado e reaberto.
   * Se você escolher **Sim**, a opção **permanecer conectado?** será mostrada ao usuário.

    :::image type="content" source="./media/keep-me-signed-in/kmsi-company-branding-advanced-settings-kmsi-1.png" alt-text="Captura de tela mostra a opção mostrar para permanecer conectado":::

## <a name="troubleshoot-sign-in-issues"></a>Solucionar problemas de entrada

Se um usuário não agir no prompt **permanecer conectado?** , conforme mostrado no diagrama a seguir, mas abandonar a tentativa de entrada, você verá uma entrada de log de entrada que indica a interrupção.

:::image type="content" source="./media/keep-me-signed-in/kmsi-stay-signed-in-prompt.png" alt-text="Mostra a permanência conectada? aviso":::

Os detalhes sobre o erro de logon são os seguintes e realçados no exemplo.

* **Código de erro de entrada**: 50140
* **Motivo da falha**: esse erro ocorreu devido à interrupção "Mantenha-me conectado" quando o usuário estava entrando.

:::image type="content" source="./media/keep-me-signed-in/kmsi-sign-ins-log-entry.png" alt-text="Exemplo de entrada de log de logon com a interrupção de se manter conectado":::

Você pode impedir que os usuários vejam a interrupção definindo a **opção mostrar como permanecer conectado** à configuração para **não** nas configurações avançadas de identidade visual. Isso desabilita o prompt KMSI para todos os usuários em seu diretório do Azure AD.

Você também pode usar os controles de sessão persistente do navegador no acesso condicional para impedir que os usuários tenham visto o prompt KMSI. Essa opção permite desabilitar o prompt do KMSI para um grupo de usuários selecionado (como os administradores globais) sem afetar o comportamento de entrada para os usuários restantes no diretório. Para obter mais informações, consulte [frequência de entrada do usuário](../conditional-access/howto-conditional-access-session-lifetime.md). 

Para garantir que o prompt KMSI seja mostrado somente quando ele puder beneficiar o usuário, o prompt KMSI não será mostrado intencionalmente nos seguintes cenários:

* O usuário está conectado por meio de SSO contínuo e autenticação integrada do Windows (IWA)
* O usuário está conectado por meio de Serviços de Federação do Active Directory (AD FS) e IWA
* O usuário é um convidado no locatário
* A pontuação de risco do usuário é alta
* A entrada ocorre durante o fluxo de consentimento do usuário ou do administrador
* O controle de sessão persistente do navegador está configurado em uma política de acesso condicional

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre outras configurações que afetam o tempo limite da sessão de entrada:

* Microsoft 365 – [tempo limite de sessão ociosa](/sharepoint/sign-out-inactive-users)
* Acesso condicional do Azure AD- [frequência de entrada do usuário](../conditional-access/howto-conditional-access-session-lifetime.md)
* Portal do Azure – [tempo limite de inatividade no nível do diretório](../../azure-portal/set-preferences.md#change-the-directory-timeout-setting-admin)