---
title: Condições na política de acesso condicional-Azure Active Directory
description: O que são condições em uma política de acesso condicional do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96d1eaff4d1b93ad3bb489f177020c351fe4d13d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95904028"
---
# <a name="conditional-access-conditions"></a>Acesso condicional: condições

Em uma política de acesso condicional, um administrador pode usar sinais de condições como risco, plataforma de dispositivo ou local para aprimorar suas decisões de política. 

[![Definir uma política de acesso condicional e especificar condições](./media/concept-conditional-access-conditions/conditional-access-conditions.png)](./media/concept-conditional-access-conditions/conditional-access-conditions.png#lightbox)

Várias condições podem ser combinadas para criar políticas de acesso condicional específicas e refinadas.

Por exemplo, ao acessar um aplicativo confidencial, um administrador pode fatorar as informações de risco de entrada da proteção de identidade e o local em sua decisão de acesso, além de outros controles, como a autenticação multifator.

## <a name="sign-in-risk"></a>Risco de entrada

Para clientes com acesso à [proteção de identidade](../identity-protection/overview-identity-protection.md), o risco de entrada pode ser avaliado como parte de uma política de acesso condicional. O risco de entrada representa a probabilidade de que uma determinada solicitação de autenticação não seja autorizada pelo proprietário da identidade. Mais informações sobre o risco de entrada podem ser encontradas nos artigos, [o que é risco](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) e [como: configurar e habilitar políticas de risco](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="user-risk"></a>Risco do usuário 

Para clientes com acesso à [proteção de identidade](../identity-protection/overview-identity-protection.md), o risco do usuário pode ser avaliado como parte de uma política de acesso condicional. Risco do usuário representa a probabilidade de que uma determinada identidade ou conta seja comprometida. Mais informações sobre o risco do usuário podem ser encontradas nos artigos, [o que é risco](../identity-protection/concept-identity-protection-risks.md#user-risk) e [como: configurar e habilitar políticas de risco](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="device-platforms"></a>Plataformas de dispositivo

A plataforma do dispositivo é caracterizada pelo sistema operacional executado em um dispositivo. O Azure AD identifica a plataforma usando as informações fornecidas pelo dispositivo, como cadeias de caracteres do agente do usuário. Como as cadeias de caracteres do agente do usuário podem ser modificadas, essas informações não são verificadas. A plataforma do dispositivo deve ser usada em conjunto com Microsoft Intune políticas de conformidade do dispositivo ou como parte de uma instrução de bloco. O padrão é aplicar a todas as plataformas de dispositivo.

O acesso condicional do Azure AD dá suporte às seguintes plataformas de dispositivo:

- Android
- iOS
- Windows Phone
- Windows
- macOS

Se você bloquear a autenticação herdada usando a condição **outros clientes** , também poderá definir a condição de plataforma do dispositivo.

> [!IMPORTANT]
> A Microsoft recomenda que você tenha uma política de acesso condicional para plataformas de dispositivo sem suporte. Por exemplo, se você quiser bloquear o acesso aos recursos corporativos do Linux ou de qualquer outro cliente sem suporte, configure uma política com uma condição de plataforma de dispositivo que inclua qualquer dispositivo e exclua as plataformas de dispositivo com suporte e conceda o controle definido para bloquear o acesso.

## <a name="locations"></a>Locais

Ao configurar o local como uma condição, as organizações podem optar por incluir ou excluir locais. Esses locais nomeados podem incluir informações de rede IPv4 públicas, país ou região ou até mesmo áreas desconhecidas que não são mapeadas para países ou regiões específicas. Somente os intervalos de IP podem ser marcados como um local confiável.

Ao incluir **qualquer local**, essa opção inclui qualquer endereço IP na Internet não apenas para os locais nomeados configurados. Ao selecionar **qualquer local**, os administradores podem optar por excluir **todos os locais confiáveis** ou **selecionados**.

Por exemplo, algumas organizações podem optar por não exigir a autenticação multifator quando seus usuários estiverem conectados à rede em um local confiável, como sua matriz física. Os administradores podem criar uma política que inclui qualquer local, mas exclui os locais selecionados para suas redes da sede.

Mais informações sobre locais podem ser encontradas no artigo, [qual é a condição de local em Azure Active Directory acesso condicional](location-condition.md).

## <a name="client-apps"></a>Aplicativos cliente

Por padrão, todas as políticas de acesso condicional recém-criadas serão aplicadas a todos os tipos de aplicativo cliente, mesmo se a condição aplicativos cliente não estiver configurada. 

> [!NOTE]
> O comportamento da condição aplicativos cliente foi atualizado em agosto de 2020. Se você tiver políticas de acesso condicional existentes, elas permanecerão inalteradas. No entanto, se você clicar em uma política existente, a opção Configurar alternância foi removida e os aplicativos cliente aos quais a política se aplica estão selecionados.

> [!IMPORTANT]
> Entradas de clientes de autenticação herdados não dão suporte à MFA e não passam informações de estado do dispositivo para o Azure AD, portanto, serão bloqueadas por controles de concessão de acesso condicional, como exigir MFA ou dispositivos compatíveis. Se você tiver contas que devem usar autenticação herdada, deverá excluir essas contas da política ou configurar a política para se aplicar somente aos clientes de autenticação modernos.

A opção **Configurar** alternância quando definido como **Sim** se aplica a itens marcados, quando definido como **não** se aplica a todos os aplicativos cliente, incluindo clientes de autenticação modernos e herdados. Essa alternância não aparece em políticas criadas antes de 2020 de agosto.

- Clientes de autenticação moderna
   - Navegador
      - Isso inclui aplicativos baseados na Web que usam protocolos como SAML, WS-Federation, OpenID Connect ou serviços registrados como um cliente confidencial do OAuth.
   - Aplicativos móveis e clientes de desktop
      -  Essa opção inclui aplicativos como os aplicativos de área de trabalho e de telefone do Office.
- Clientes de autenticação herdados
   - Clientes do Exchange ActiveSync
      - Isso inclui todo o uso do protocolo EAS (Exchange ActiveSync).
      - Quando a política bloqueia o uso do Exchange ActiveSync, o usuário afetado receberá um único email de quarentena. Este email com fornece informações sobre por que eles estão bloqueados e incluem instruções de correção, se possível.
      - Os administradores podem aplicar a política somente às plataformas com suporte (como iOS, Android e Windows) por meio do acesso condicional MS API do Graph.
   - Outros clientes
      - Essa opção inclui clientes que usam protocolos de autenticação básica/herdados que não dão suporte à autenticação moderna.
         - SMTP autenticado - usado por clientes POP e IMAP para enviar mensagens de email.
         - Descoberta automática - usada pelos clientes do Outlook e do EAS para localizar e conectar-se às caixas de correio no Exchange Online.
         - Exchange Online PowerShell - usado para se conectar ao Exchange Online com o PowerShell remoto. Se você bloquear a autenticação básica para o Exchange Online PowerShell, será necessário usar o módulo do PowerShell do Exchange Online para se conectar. Para obter instruções, confira [Conectar ao Exchange Online PowerShell usando a autenticação multifator](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
         - Serviços Web do Exchange (EWS) - uma interface de programação usada pelo Outlook, pelo Outlook para Mac e por aplicativos de terceiros.
         - IMAP4 – usado por clientes de email IMAP.
         - MAPI sobre HTTP (MAPI/HTTP) – usado pelo Outlook 2010 e posterior.
         - OAB (catálogo de endereços offline) – uma cópia das coleções de listas de endereços que são baixadas e usadas pelo Outlook.
         - Outlook em Qualquer Lugar (RPC por HTTP) - usado pelo Outlook 2016 e anterior.
         - Serviço do Outlook – usado pelo aplicativo de email e calendário para Windows 10.
         - POP3 - usado por clientes de email POP.
         - Serviços Web de relatórios - usados para recuperar dados de relatório no Exchange Online.

Essas condições são normalmente usadas quando se requer um dispositivo gerenciado, bloqueando a autenticação herdada e bloqueando aplicativos Web, mas permitindo aplicativos móveis ou de desktop.

### <a name="supported-browsers"></a>Navegadores com suporte

Essa configuração funciona com todos os navegadores. No entanto, para satisfazer uma política de dispositivo, como um requisito de conformidade de dispositivo, há suporte para os sistemas operacionais e navegadores a seguir:

| Sistema operacional | Navegadores |
| :-- | :-- |
| Windows 10 | Microsoft Edge, Internet Explorer, Chrome |
| Windows 8 / 8.1 | Internet Explorer, Chrome |
| Windows 7 | Internet Explorer, Chrome |
| iOS | Microsoft Edge, Intune Managed Browser, Safari |
| Android | Microsoft Edge, Intune Managed Browsers, Chrome |
| Windows Phone | Microsoft Edge, Internet Explorer |
| Windows Server 2019 | Microsoft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome, Safari |

> [!NOTE]
> O Edge 85 + exige que o usuário esteja conectado ao navegador para passar corretamente a identidade do dispositivo. Caso contrário, ele se comporta como Chrome sem a extensão de contas. Essa entrada pode não ocorrer automaticamente em um cenário de ingresso híbrido do Azure AD. 

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Por que vejo um prompt de certificado no navegador

No Windows 7, iOS, Android e macOS, o Azure AD identifica o dispositivo usando um certificado de cliente que é provisionado quando o dispositivo é registrado no Azure AD.  Quando um usuário entra pela primeira vez por meio do navegador, é solicitado que o usuário selecione o certificado. O usuário deve selecionar esse certificado antes de usar o navegador.

#### <a name="chrome-support"></a>Suporte ao Chrome

Para obter suporte ao Chrome na **atualização do Windows 10 para criadores (versão 1703)** ou posterior, instale a [extensão de contas do Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Essa extensão é necessária quando uma política de acesso condicional requer detalhes específicos do dispositivo.

Para implantar automaticamente essa extensão para os navegadores Chrome, crie a seguinte chave do registro:

- Caminho HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist
- Nome 1
- Tipo REG_SZ (cadeia de caracteres)
- Ppnbnpeolgkicgegkbkbjmhlideopiji de dados; https \: //clients2.google.com/Service/Update2/CRX

Para obter suporte ao Chrome no **Windows 8.1 e 7**, crie a seguinte chave do registro:

- Caminho HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls
- Nome 1
- Tipo REG_SZ (cadeia de caracteres)
- Data {"Pattern": " https://device.login.microsoftonline.com ", "filtro": {"emissor": {"CN": "MS-Organization-Access"}}}

Esses navegadores dão suporte à autenticação de dispositivo, permitindo que o dispositivo seja identificado e validado em relação a uma política. A verificação de dispositivo falha caso o navegador esteja sendo executado em modo privado.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Aplicativos móveis e cliente de área de trabalho com suporte

As organizações podem selecionar **aplicativos móveis e clientes de desktop** como aplicativo cliente.

Essa configuração tem um impacto nas tentativas de acesso feitas a partir dos seguintes aplicativos móveis e clientes de desktop:

| Aplicativos cliente | Serviço de Destino | Plataforma |
| --- | --- | --- |
| Aplicativo Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS e Android |
| Aplicativo de Calendário/Email/Pessoas, Outlook 2016 Outlook 2013 (com autenticação moderna)| Exchange Online | Windows 10 |
| Política de localização e MFA para aplicativos. Não há suporte para políticas baseadas em dispositivo.| Qualquer serviço de aplicativo de Meus Aplicativos | Android e iOS |
| Microsoft Teams Services – controla todos os serviços que dão suporte ao Microsoft Teams e todos os seus aplicativos cliente – Windows Desktop, iOS, Android, WP e cliente da Web | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android e macOS |
| Aplicativos do Office 2016, Office 2013 (com autenticação moderna), [cliente de sincronização do onedrive](/onedrive/enable-conditional-access) | SharePoint | Windows 8.1, Windows 7 |
| Aplicativos do Office 2016, aplicativos do Universal Office, Office 2013 (com autenticação moderna), [cliente de sincronização do onedrive](/onedrive/enable-conditional-access) | SharePoint online | Windows 10 |
| Office 2016 (somente Word, Excel, PowerPoint, OneNote). | SharePoint | macOS |
| Office 2019| SharePoint | Windows 10, macOS |
| Aplicativos móveis do Office | SharePoint | Android, iOS |
| Aplicativo Office Yammer | Yammer | Windows 10, iOS, Android |
| Outlook 2019 | SharePoint | Windows 10, macOS |
| Outlook 2016 (Office para macOS) | Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (com autenticação moderna), Skype for Business (com autenticação moderna) | Exchange Online | Windows 8.1, Windows 7 |
| Aplicativo Outlook Mobile | Exchange Online | Android, iOS |
| Aplicativo do Power BI | Serviço do Power BI | Windows 10, Windows 8.1, Windows 7, Android e iOS |
| Skype for Business | Exchange Online| Android, iOS |
| Aplicativo Visual Studio Team Services | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS e Android |

### <a name="exchange-activesync-clients"></a>Clientes do Exchange ActiveSync

- As organizações só podem selecionar clientes do Exchange ActiveSync ao atribuir a política a usuários ou grupos. Selecionar **todos os usuários**, **todos os usuários convidados e externos** ou **funções de diretório** fará com que todos os usuários se tornem bloqueados.
- Ao criar uma política atribuída aos clientes do Exchange ActiveSync, o **Exchange Online** deve ser o único aplicativo de nuvem atribuído à política. 
- As organizações podem restringir o escopo dessa política a plataformas específicas usando a condição de **plataformas de dispositivo** .

Se o controle de acesso atribuído à política usar **exigir aplicativo cliente aprovado**, o usuário será direcionado para instalar e usar o cliente móvel do Outlook. No caso de a **autenticação multifator** ser necessária, os usuários afetados são bloqueados, pois a autenticação básica não oferece suporte à autenticação multifator.

Para obter mais informações, confira os seguintes artigos:

- [Bloquear a autenticação herdada com acesso condicional](block-legacy-authentication.md)
- [Exigindo aplicativos cliente aprovados com acesso condicional](app-based-conditional-access.md)

### <a name="other-clients"></a>Outros clientes

Ao selecionar **Outros clientes**, é possível especificar uma condição que afeta os aplicativos que usam autenticação básica com protocolos de email, como IMAP, MAPI, POP, SMTP e aplicativos mais antigos do Office que não usam autenticação moderna.

## <a name="device-state-preview"></a>Estado do dispositivo (versão prévia)

A condição de estado do dispositivo pode ser usada para excluir dispositivos que são ingressados no Azure AD híbrido e/ou dispositivos marcados como compatíveis com uma política de conformidade de Microsoft Intune das políticas de acesso condicional de uma organização.

Por exemplo, *todos os usuários* que acessam o aplicativo de nuvem de *Gerenciamento de Microsoft Azure* , incluindo todo o estado do **dispositivo** , excluindo o dispositivo **híbrido do Azure ad** e o **dispositivo marcado como compatível** e para *controles de acesso*, **Bloquear**. 
   - Este exemplo criaria uma política que permite apenas o acesso ao gerenciamento de Microsoft Azure de dispositivos que são ingressados no Azure AD híbrido ou dispositivos marcados como compatíveis.

## <a name="next-steps"></a>Próximas etapas

- [Acesso condicional: Grant](concept-conditional-access-grant.md)

- [Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)
