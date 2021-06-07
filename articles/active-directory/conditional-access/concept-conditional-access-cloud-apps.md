---
title: Aplicativos de nuvem ou ações na política de Acesso Condicional – Azure Active Directory
description: O que são aplicativos de nuvem ou ações em uma política de Acesso Condicional do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c8024a2083d09fcbd53a37f0d391c4589748eea
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605069"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Acesso Condicional: Aplicativos na nuvem ou ações

Os aplicativos de nuvem ou as ações são um sinal importante em uma política de Acesso Condicional. Políticas de Acesso Condicional permitem que administradores atribuam controles a aplicativos ou ações específicas.

- Os administradores podem escolher na lista de aplicativos que incluem aplicativos da Microsoft internos e [aplicativos integrados do Azure AD](../manage-apps/what-is-application-management.md) incluindo aplicativos da galeria, que não são da galeria e os publicados por meio do [Proxy de Aplicativo](../manage-apps/what-is-application-proxy.md).
- Os administradores podem optar por definir a política não baseada em um aplicativo de nuvem, mas em uma ação do usuário. A única ação com suporte é Registrar informações de segurança (versão prévia), permitindo o Acesso Condicional para impor controles em relação à [experiência combinada de registro de informações de segurança](../authentication/howto-registration-mfa-sspr-combined.md).

![Definir uma política de Acesso Condicional e especificar aplicativos de nuvem](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Aplicativos em nuvem da Microsoft

Muitos aplicativos de nuvem da Microsoft existentes estão incluídos na lista de aplicativos entre os quais você pode selecionar. 

Os administradores podem atribuir uma política de Acesso Condicional aos aplicativos de nuvem da Microsoft a seguir. Alguns aplicativos, como o Office 365 e o gerenciamento de Microsoft Azure, incluem vários aplicativos ou serviços filho relacionados. Nós adicionamos continuamente mais aplicativos, portanto, a lista a seguir não é exaustiva e está sujeita a alterações.

- [Office 365](#office-365)
- Azure Analysis Services
- Azure DevOps
- [Banco de dados SQL do Azure e análise de Synapse do Azure](../../azure-sql/database/conditional-access-configure.md)
- Dynamics CRM Online
- Análise do Microsoft Application Insights
- [Proteção de Informações do Microsoft Azure](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Gerenciamento do Microsoft Azure](#microsoft-azure-management)
- Gerenciamento de Assinaturas do Microsoft Azure
- Microsoft Cloud App Security
- Portal de Controle de Acesso de Ferramentas do Microsoft Commerce
- Serviço de Autenticação de Ferramentas do Microsoft Commerce
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Registro do Microsoft Intune](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Pesquisa da Microsoft no Bing
- Microsoft StaffHub
- Microsoft Stream
- Equipes da Microsoft
- Exchange Online
- SharePoint
- Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Serviço do Power BI
- Project Online
- Skype for Business Online
- VPN (Rede Privada Virtual)
- Windows Defender ATP

Os aplicativos que estão disponíveis para acesso condicional passaram por uma integração e um processo de validação. Isso não inclui todos os aplicativos da Microsoft, pois muitos são serviços de back-end e não devem ter a política aplicada diretamente a eles. Se você estiver procurando um aplicativo que está ausente, você pode entrar em contato com a equipe de aplicativo específica ou fazer uma solicitação no [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=167259).

### <a name="office-365"></a>Office 365

O Microsoft 365 fornece serviços de produtividade e colaboração baseados em nuvem como o Exchange, SharePoint e Microsoft Teams. Microsoft 365 serviços de nuvem estão profundamente integrados para garantir experiências suaves e colaborativas. Essa integração pode causar confusão ao criar políticas, pois alguns aplicativos como o Microsoft Teams têm dependências de outros como SharePoint ou Exchange.

O aplicativo Office 365 torna possível direcionar esses serviços de uma só vez. É recomendável usar o novo aplicativo do Office 365, em vez de direcionar aplicativos de nuvem individuais para evitar problemas com [dependências de serviço](service-dependencies.md). Direcionar esse grupo de aplicativos ajuda a evitar problemas que possam surgir devido a políticas e dependências inconsistentes.

Os administradores podem optar por excluir aplicativos específicos da política se quiserem incluindo o aplicativo do Office 365 e excluindo os aplicativos específicos de sua escolha na política.

Aplicativos-chave incluídos no aplicativo cliente do Office 365:

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Equipes da Microsoft
   - Exchange Online
   - SharePoint online
   - Microsoft 365 Serviço de Pesquisa
   - Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype for Business Online
   - Sway

### <a name="microsoft-azure-management"></a>Gerenciamento do Microsoft Azure

O aplicativo de Gerenciamento do Microsoft Azure inclui vários serviços subjacentes. 

   - Portal do Azure
   - Provedor do Azure Resource Manager
   - APIs de modelo de implantação clássico
   - Azure PowerShell
   - CLI do Azure
   - Portal do administrador de assinaturas do Visual Studio
   - Azure DevOps
   - Portal do Azure Data Factory

> [!NOTE]
> O aplicativo de Gerenciamento do Microsoft Azure se aplica ao Azure PowerShell, que chama a API do Azure Resource Manager. Não se aplica ao PowerShell do Azure AD, que chama o Microsoft Graph.

## <a name="other-applications"></a>Outros aplicativos

Além dos aplicativos Microsoft, os administradores podem adicionar qualquer aplicativo registrado no Azure AD a políticas de Acesso Condicional. Estes aplicativos podem incluir: 

- Aplicativos publicados por meio do [Proxy de Aplicativo do Azure AD](../manage-apps/what-is-application-proxy.md)
- [Aplicativos adicionados da galeria](../manage-apps/add-application-portal.md)
- [Os aplicativos personalizados não estão na galeria](../manage-apps/view-applications-portal.md)
- [Aplicativos herdados publicados por meio de redes e controladores de entrega de aplicativos](../manage-apps/secure-hybrid-access.md)
- Aplicativos que usam [logon único baseado em senha](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

> [!NOTE]
> Como a política de acesso condicional define os requisitos para acessar um serviço, você não pode aplicá-lo a um aplicativo cliente (público/nativo). Em outras palavras, a política não é definida diretamente em um aplicativo cliente (público/nativo), mas é aplicada quando um cliente chama um serviço. Por exemplo, uma política definida no serviço SharePoint se aplica aos clientes que chamam o SharePoint. Uma política definida no Exchange se aplica à tentativa de acessar o email usando o cliente do Outlook. É por isso que aplicativos cliente (públicos/nativos) não estão disponíveis para seleção no seletor de Aplicativos de Nuvem e a opção de Acesso Condicional não está disponível nas configurações do aplicativo para o aplicativo cliente (público/nativo) registrado em seu locatário. 

## <a name="user-actions"></a>Ações do usuário

As ações do usuário são tarefas que podem ser executadas por um usuário. Atualmente, o acesso condicional dá suporte a duas ações do usuário: 

- **Registrar informações de segurança**: essa ação do usuário permite que a política de acesso condicional seja aplicada quando os usuários que estão habilitados para registro combinado tentam registrar suas informações de segurança. Mais informações podem ser encontradas no artigo [Registro de informações de segurança combinadas](../authentication/concept-registration-mfa-sspr-combined.md).

- **Registrar ou ingressar dispositivos (visualização)**: essa ação do usuário permite que os administradores imponham a política de acesso condicional quando os usuários [registram](../devices/concept-azure-ad-register.md) ou [ingressam](../devices/concept-azure-ad-join.md) em dispositivos no Azure AD. Há duas considerações importantes com essa ação do usuário: 
   - `Require multi-factor authentication` o é o único controle de acesso disponível com essa ação do usuário e todos os outros estão desabilitados. Essa restrição impede conflitos com controles de acesso que dependem do registro de dispositivo do Azure AD ou que não se aplicam ao registro de dispositivos do Azure AD. 
   - Quando uma política de acesso condicional é habilitada com essa ação do usuário, você deve definir **Azure Active Directory**  >    >  **configurações**  -  `Devices to be Azure AD joined or Azure AD registered require Multi-Factor Authentication` de dispositivo de dispositivos como **não**. Caso contrário, a política de acesso condicional com essa ação do usuário não será imposta corretamente. Mais informações sobre essa configuração de dispositivo podem ser encontradas em [definir configurações do dispositivo](../devices/device-management-azure-portal.md#configure-device-settings). Essa ação do usuário fornece flexibilidade para exigir a autenticação multifator para registrar ou ingressar dispositivos para usuários e grupos específicos, em vez de ter uma política em todo o locatário em configurações do dispositivo. 
   
## <a name="next-steps"></a>Próximas etapas

- [Acesso Condicional: condições](concept-conditional-access-conditions.md)

- [Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)
- [Dependências de aplicativo cliente](service-dependencies.md)
