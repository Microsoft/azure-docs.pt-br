---
title: Solicitações de autenticação multifator do Azure AD e tempo de vida da sessão
description: Saiba mais sobre a configuração recomendada para prompts de reautenticação com a autenticação multifator do Azure AD e como a vida útil da sessão é aplicada.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0033b6985313de351ffdaf7028d2c1e9bd76ba72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97963512"
---
# <a name="optimize-reauthentication-prompts-and-understand-session-lifetime-for-azure-ad-multi-factor-authentication"></a>Otimizar prompts de reautenticação e entender o tempo de vida da sessão para a autenticação multifator do Azure AD

O Azure Active Directory (AD do Azure) tem várias configurações que determinam a frequência com que os usuários precisam ser autenticados novamente. Essa reautenticação pode ser com um primeiro fator, como senha, FIDO ou Microsoft Authenticator sem senha, ou para executar a autenticação multifator (MFA). Você pode definir essas configurações de reautenticação conforme necessário para seu próprio ambiente e a experiência do usuário que desejar.

A configuração padrão do Azure AD para a frequência de entrada do usuário é uma janela contínua de 90 dias. Pedir aos usuários que as credenciais geralmente pareça uma coisa sensata a fazer, mas pode ser relançado. Se os usuários são treinados para inserir suas credenciais sem pensar, eles podem inadvertidamente fornecê-los a um prompt de credenciais mal-intencionado.

Pode parecer que o alarme não pede que um usuário entre novamente, embora qualquer violação das políticas de ti revogue a sessão. Alguns exemplos incluem uma alteração de senha, um dispositivo incompatível ou uma operação de desativação de conta. Você também pode [revogar explicitamente as sessões de usuários usando o PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken).

Este artigo detalha as configurações recomendadas e como as configurações diferentes funcionam e interagem entre si.

## <a name="recommended-settings"></a>Configurações recomendadas

Para dar aos usuários o equilíbrio certo de segurança e facilidade de uso solicitando que eles entrem na frequência certa, recomendamos as seguintes configurações:

* Se você tiver Azure AD Premium:
    * Habilite o SSO (logon único) entre aplicativos usando [dispositivos gerenciados](../devices/overview.md) ou [SSO contínuo](../hybrid/how-to-connect-sso.md).
    * Se a reautenticação for necessária, use uma [política de frequência de entrada](../conditional-access/howto-conditional-access-session-lifetime.md)de acesso condicional.
    * Para usuários que se conectam de dispositivos não gerenciados ou cenários de dispositivos móveis, use o acesso condicional para habilitar sessões persistentes do navegador e políticas de frequência de entrada.
* Se você tiver licenças de aplicativos Microsoft 365 ou a camada gratuita do Azure AD:
    * Habilite o SSO (logon único) entre aplicativos usando [dispositivos gerenciados](../devices/overview.md) ou [SSO contínuo](../hybrid/how-to-connect-sso.md).
    * Mantenha a opção de *permanecer conectada* habilitada e Oriente seus usuários a aceitá-la.
* Para cenários de dispositivos móveis, verifique se os usuários usam o aplicativo Microsoft Authenticator. Esse aplicativo é usado como um agente para outros aplicativos federados do Azure AD e reduz os prompts de autenticação no dispositivo.

Nossa pesquisa mostra que essas configurações são certas para a maioria dos locatários. Algumas combinações dessas configurações, como lembrar a *MFA* e *permanecerem em* si, podem resultar em prompts para que os usuários se autentiquem com muita frequência. Os prompts de reautenticação regulares são inválidos para a produtividade do usuário e podem torná-los mais vulneráveis a ataques.

## <a name="azure-ad-session-lifetime-configuration-settings"></a>Definições de configuração de tempo de vida da sessão do Azure AD

Para otimizar a frequência de prompts de autenticação para seus usuários, você pode configurar opções de tempo de vida de sessão do Azure AD. Entenda as necessidades de seus negócios e usuários e defina as configurações que fornecem o melhor equilíbrio para o seu ambiente.

### <a name="evaluate-session-lifetime-policies"></a>Avaliar políticas de tempo de vida da sessão

Sem nenhuma configuração de tempo de vida da sessão, não há cookies persistentes na sessão do navegador. Sempre que um usuário fechar e abrir o navegador, ele receberá uma solicitação de reautenticação. Em clientes do Office, o período de tempo padrão é uma janela sem interrupção de 90 dias. Com essa configuração padrão do Office, se o usuário tiver redefinido sua senha ou se houvesse inatividade de mais de 90 dias, o usuário precisará autenticar novamente com todos os fatores necessários (primeiro e segundo fator).

Um usuário pode ver vários prompts do MFA em um dispositivo que não tem uma identidade no Azure AD. Vários prompts resultam quando cada aplicativo tem seu próprio token de atualização OAuth que não é compartilhado com outros aplicativos cliente. Nesse cenário, a MFA solicita várias vezes, uma vez que cada aplicativo solicita que um token de atualização OAuth seja validado com MFA.

No Azure AD, a política mais restritiva para o tempo de vida da sessão determina quando o usuário precisa autenticar novamente. Considere o seguinte cenário:

* Você habilita o *permaneça conectado*, que usa um cookie de navegador persistente e
* Você também permite *lembrar o MFA por 14 dias*

Neste cenário de exemplo, o usuário precisa se autenticar a cada 14 dias. Esse comportamento segue a política mais restritiva, mesmo que o *mantenha conectado* por si só não exigiria que o usuário refaça a autenticação no navegador.

### <a name="managed-devices"></a>Dispositivos gerenciados

Dispositivos ingressados no Azure AD usando o ingresso no Azure ad ou ingresso no Azure AD híbrido recebem um [PRT (tokens de atualização principal)](../devices/concept-primary-refresh-token.md) para usar o SSO (logon único) entre aplicativos. Esse PRT permite que um usuário entre uma vez no dispositivo e permita que a equipe de ti garanta que os padrões de segurança e conformidade sejam atendidos. Se for necessário que um usuário se conecte com mais frequência em um dispositivo associado para alguns aplicativos ou cenários, isso pode ser feito usando a [frequência de entrada de acesso condicional](../conditional-access/howto-conditional-access-session-lifetime.md).

### <a name="show-option-to-remain-signed-in"></a>Mostrar opção para permanecer conectado

Quando um usuário seleciona **Sim** na opção *permanecer conectado?* durante a entrada, um cookie persistente é definido no navegador. Esse cookie persistente lembra o primeiro e o segundo fator e aplica-se apenas às solicitações de autenticação no navegador.

![Captura de tela do prompt de exemplo para permanecer conectado](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/stay-signed-in-prompt.png)

Se você tiver uma licença Azure AD Premium 1, é recomendável usar a política de acesso condicional para *sessão persistente do navegador*. Essa política substitui a permanência de *entrar?* configuração e fornece uma experiência de usuário aprimorada. Se você não tiver uma licença Azure AD Premium 1, é recomendável habilitar a configuração permanecer conectado para seus usuários.

Para obter mais informações sobre como configurar a opção para permitir que os usuários permaneçam conectados, consulte [personalizar sua página de entrada do Azure ad](../fundamentals/customize-branding.md#customize-your-azure-ad-sign-in-page).

### <a name="remember-multi-factor-authentication"></a>Lembre-se da Autenticação Multifator  

Essa configuração permite configurar valores entre 1-365 dias e define um cookie persistente no navegador quando um usuário seleciona a opção **não perguntar novamente por X dias** ao entrar.

![Captura de tela do prompt de exemplo para aprovar uma solicitação de entrada](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/approve-sign-in-request.png)

Embora essa configuração reduza o número de autenticações em aplicativos Web, ela aumenta o número de autenticações para clientes de autenticação modernos, como clientes do Office. Esses clientes normalmente solicitam somente após a redefinição de senha ou inatividade de 90 dias. No entanto, definir esse valor como menos de 90 dias reduz os prompts de MFA padrão para clientes do Office e aumenta a frequência de reautenticação. Quando usado em combinado com **permanecer conectado** ou políticas de acesso condicional, ele pode aumentar o número de solicitações de autenticação.

Se você usar *lembrar MFA* e tiver Azure ad Premium 1 licenças, considere migrar essas configurações para a frequência de entrada de acesso condicional. Caso contrário, considere usar *manter-me conectado?* em vez disso.

Para obter mais informações, consulte [lembrar a autenticação multifator](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

### <a name="authentication-session-management-with-conditional-access"></a>Gerenciamento de sessão de autenticação com acesso condicional

A **frequência de entrada** permite que o administrador escolha a frequência de entrada que se aplica para o primeiro e o segundo fator no cliente e no navegador. É recomendável usar essas configurações, juntamente com dispositivos gerenciados, em cenários quando você tem a necessidade de restringir a sessão de autenticação, como para aplicativos de negócios críticos.

A **sessão persistente do navegador** permite que os usuários permaneçam conectados após fechar e reabrir a janela do navegador. Semelhante à configuração de *permanecer conectado* , ele define um cookie persistente no navegador. No entanto, como ele é configurado pelo administrador, ele não exige que o usuário selecione **Sim** na opção *permanecer conectado?* portanto, isso fornece uma melhor experiência do usuário. Se você usar a opção *permanecer conectado?* , recomendamos que você habilite a política de **sessão de navegador persistente** em vez disso.

Para obter mais informações. consulte [Configurar o gerenciamento de sessão de autenticação com acesso condicional](../conditional-access/howto-conditional-access-session-lifetime.md).

### <a name="configurable-token-lifetimes"></a>Tempos de vida de token configuráveis

Essa configuração permite a configuração do tempo de vida para o token emitido por Azure Active Directory. Essa política é substituída pelo *Gerenciamento de sessão de autenticação com acesso condicional*. Se você estiver usando *tempos de vida de token configuráveis* hoje, é recomendável iniciar a migração para as políticas de acesso condicional.

## <a name="review-your-tenant-configuration"></a>Examinar sua configuração de locatário  

Agora que você entende como as diferentes configurações funcionam e a configuração recomendada, é hora de verificar sua configuração de locatários e fazer alterações de acordo:

Para configurar ou examinar a opção *permanecer conectado* , conclua as seguintes etapas:

1. No portal do AD do Azure, procure e selecione *Azure Active Directory*.
1. Selecione **identidade visual da empresa**, em seguida, para cada localidade, escolha **Mostrar opção para permanecer conectado**.
1. Escolha *Sim* e, em seguida, selecione **salvar**.

Para lembrar as configurações de autenticação multifator em dispositivos confiáveis, conclua as seguintes etapas:

1. No portal do AD do Azure, procure e selecione *Azure Active Directory*.
1. Selecione **segurança** e **MFA**.
1. Em **Configurar**, selecione **configurações adicionais de MFA baseadas em nuvem**.
1. Na página *configurações do serviço de autenticação multifator* , role para **lembrar configurações de autenticação multifator**. Desabilite a configuração desmarcando a caixa de seleção.

Para configurar políticas de acesso condicional para a frequência de entrada e a sessão persistente do navegador, conclua as seguintes etapas:

1. No portal do AD do Azure, procure e selecione *Azure Active Directory*.
1. Selecione **segurança** e **acesso condicional**.
1. Configure uma política usando as opções de gerenciamento de sessão recomendadas detalhadas neste artigo.

Para examinar os tempos de vida [do token, use o PowerShell do Azure ad para consultar as políticas do Azure ad](../develop/configure-token-lifetimes.md#get-started). Desabilite as políticas que você tiver em vigor.

Se mais de uma configuração estiver habilitada em seu locatário, é recomendável atualizar suas configurações com base no licenciamento disponível para você. Por exemplo, se você tiver licenças do Azure AD Premium, você deve usar apenas a política de acesso condicional de *frequência de entrada* e *sessão de navegador persistente*. Se você tiver aplicativos Microsoft 365 ou licenças gratuitas do Azure AD, deverá usar a configuração *permanecer conectado?* .

Se você tiver habilitado tempos de vida de token configuráveis, esse recurso será removido em breve. Planejar uma migração para uma política de acesso condicional.

A tabela a seguir resume as recomendações com base em licenças:

|              | Aplicativos Azure AD Gratuito e Microsoft 365 | Azure AD Premium |
|------------------------------|-----------------------------------|------------------|
| **SSO**                      | [Ingresso](../devices/concept-azure-ad-join.md) no Azure ad ou [ingresso no Azure ad híbrido](../devices/concept-azure-ad-join-hybrid.md)ou [SSO contínuo](../hybrid/how-to-connect-sso.md) para dispositivos não gerenciados. | Ingresso no Azure AD<br />Ingresso no Azure AD Híbrido |
| **Configurações de reautenticação** | Permanecer conectado                  | Usar políticas de acesso condicional para frequência de entrada e sessão de navegador persistente |

## <a name="next-steps"></a>Próximas etapas

Para começar, conclua o tutorial para [proteger eventos de entrada do usuário com a autenticação multifator do Azure ad](tutorial-enable-azure-mfa.md) ou [use as detecções de risco para entradas de usuário para disparar a autenticação multifator do Azure ad](tutorial-risk-based-sspr-mfa.md).
