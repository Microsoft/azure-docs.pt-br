---
title: "Referência técnica de acesso condicional do Azure Active Directory | Microsoft Docs"
description: "Saiba como utilizar o controle de acesso condicional no Azure Active Directory. Especifique as condições para autenticar usuários e controlar o acesso ao aplicativo. Quando as condições especificadas são atendidas, os usuários são autenticados e o acesso ao aplicativo será concedido."
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 27ace4e9bc4a1626059fba657dce0c629d52f32d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Referência técnica de acesso condicional do Azure Active Directory

É possível utilizar o acesso condicional do [Azure AD (Azure Active Directory)](active-directory-conditional-access-azure-portal.md) para ajustar como os usuários autorizados podem acessar os recursos.  

Este tópico fornece informações de suporte para as seguintes opções de configuração para uma política de acesso condicional: 

- Atribuições de aplicativos em nuvem

- Condição de plataforma de dispositivo 

- Condição de aplicativos cliente

- Requisito de aplicativo cliente aprovado



## <a name="cloud-apps-assignments"></a>Atribuições de aplicativos de nuvem

Ao configurar uma política de acesso condicional, será necessário selecionar [os aplicativos de nuvem que utilizam sua política](active-directory-conditional-access-azure-portal.md#who). 

![Selecione os aplicativos em nuvem para sua política](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Aplicativos em nuvem da Microsoft

Você pode atribuir uma política de acesso condicional aos seguintes aplicativos de nuvem da Microsoft:

- RemoteApp do Azure

- Microsoft Dynamics 365

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (inclui o OneDrive for Business)

- Microsoft Power BI 

- Microsoft Visual Studio Team Services

- Equipes da Microsoft


### <a name="other-applications"></a>Outros aplicativos 

Além de aplicativos de nuvem da Microsoft, você pode atribuir uma política de acesso condicional aos seguintes tipos de aplicativos de nuvem:

- Aplicativos conectados ao Azure AD - Microsoft Azure Active Directory

- Aplicativo SaaS (software como serviço) federado e pré-integrado

- Aplicativos que usam SSO (logon único) com senha

- Aplicativos de linha de negócios

- Aplicativos que usam o Proxy de Aplicativo do Azure AD - Microsoft Azure Active Directory


## <a name="device-platform-condition"></a>Condição de plataforma de dispositivo

Em uma política de acesso condicional, é possível configurar a condição de plataforma do dispositivo para vincular a política ao sistema operacional em um cliente.

![Vincular a política de acesso ao sistema operacional do cliente](./media/active-directory-conditional-access-technical-reference/41.png)

O acesso condicional do Azure AD dá suporte às seguintes plataformas de dispositivo:

- Android

- iOS

- Windows Phone

- Windows

- macOS (versão prévia)



## <a name="client-apps-condition"></a>Condição de aplicativos cliente 

Ao configurar uma política de acesso condicional, será possível [selecionar aplicativos cliente](active-directory-conditional-access-azure-portal.md#client-apps) para a condição de aplicativo cliente. Defina a condição de aplicativos cliente para conceder ou bloquear acesso quando uma tentativa de acesso for realizada a partir dos seguintes tipos de aplicativos cliente:

- Navegador
- Aplicativos móveis e de da área de trabalho

![Controlar acesso para aplicativos cliente](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>Navegadores com suporte 

Controle o acesso do navegador, utilizando a opção **Navegador** na sua política de acesso condicional. O acesso será concedido somente quando a tentativa de acesso for realizada por meio de um navegador compatível. Quando uma tentativa de acesso for realizada por meio de um navegador sem suporte, a tentativa será bloqueada.

![Controlar acesso para navegadores com suporte](./media/active-directory-conditional-access-technical-reference/05.png)

Em sua política de acesso condicional, há suporte para os seguintes navegadores: 


| SO                     | Navegadores                    | Suporte     |
| :--                    | :--                         | :-:         |
| Windows 10             | Internet Explorer, Edge     | ![Verificação][1] |
| Windows 10             | Chrome                      | ![Verificação][1] |
| Windows 8 / 8.1        | Internet Explorer, Chrome   | ![Verificação][1] |
| Windows 7              | Internet Explorer, Chrome   | ![Verificação][1] |
| iOS                    | Safari, Intune Managed Browser                      | ![Verificação][1] |
| Android                | Chrome, Intune Managed Browser                      | ![Verificação][1] |
| Windows Phone          | Internet Explorer, Edge     | ![Verificação][1] |
| Windows Server 2016    | Internet Explorer, Edge     | ![Verificação][1] |
| Windows Server 2016    | Chrome                      | Em breve |
| Windows Server 2012 R2 | Internet Explorer, Chrome   | ![Verificação][1] |
| Windows Server 2008 R2 | Internet Explorer, Chrome   | ![Verificação][1] |
| macOS                  | Safari                      | ![Verificação][1] |
| macOS                  | Chrome                      | Em breve |

> [!NOTE]
> Para suporte ao Chrome, você utiliza o Windows 10 Creators Update (versão 1703) ou posterior.<br>
> Você pode instalar [essa extensão](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="supported-mobile-applications-and-desktop-clients"></a>Aplicativos móveis e cliente de área de trabalho com suporte

Controle o acesso do cliente e aplicativo, utilizando a opção **Aplicativos móveis e cliente de área de trabalho** na sua política de acesso condicional. O acesso será concedido somente quando a tentativa de acesso for realizada por um aplicativo móvel ou cliente de área de trabalho com suporte. Quando uma tentativa de acesso for realizada por um cliente ou aplicativo sem suporte, a tentativa será bloqueada.

![Controlar acesso para aplicativos móveis ou clientes de área de trabalho com suporte](./media/active-directory-conditional-access-technical-reference/06.png)

Os aplicativos móveis e clientes de área de trabalho a seguir oferecem suporte ao acesso condicional para o Office 365 e outros aplicativos de serviço conectados ao Azure AD:


| Aplicativos cliente| Serviço de Destino| Plataforma |
| --- | --- | --- |
| Autenticação Multifator do Microsoft Azure e política de local para aplicativos (políticas com base em dispositivo não têm suporte)| Qualquer serviço de aplicativo de Meus Aplicativos| Android, iOS|
| RemoteApp do Azure| Serviço RemoteApp do Azure| Windows 10, Windows 8.1, Windows 7, iOS, Android, macOS|
| Aplicativo Dynamics 365| Dynamics 365| Windows 10, Windows 8.1, Windows 7, iOS, Android|
| Microsoft Office 365 Teams (controla todos os serviços que dão suporte ao Microsoft Teams e todos seus aplicativos clientes: Windows Desktop, iOS, Android, Windows Phone, cliente Web)| Equipes da Microsoft| Windows 10, Windows 8.1, Windows 7, iOS, Android|
| Aplicativo de Calendário/Email/Pessoas, Outlook 2016, Outlook 2013 (com autenticação moderna), Skype for Business (com autenticação moderna)| Office 365 Exchange Online| Windows 10|
| Outlook 2016, Outlook 2013 (com autenticação moderna), Skype for Business (com autenticação moderna)| Office 365 Exchange Online| Windows 8.1, Windows 7|
| Aplicativo móvel do Outlook| Office 365 Exchange Online| iOS|
| Outlook 2016 (Office para macOS)| Office 365 Exchange Online| macOS|
| Aplicativos do Office 2016, aplicativos do Universal Office, Office 2013 (com autenticação moderna), cliente de sincronização do [OneDrive](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e), suporte futuro planejado para Grupos do Office e aplicativo SharePoint| Office 365 SharePoint Online| Windows 10|
| Aplicativos do Office 2016, Office 2013 (com autenticação moderna), cliente de sincronização do [OneDrive](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)| Office 365 SharePoint Online| Windows 8.1, Windows 7|
| Aplicativos móveis do Office| Office 365 SharePoint Online| iOS, Android|
| Office 2016 para macOS (suporte apenas para Word, Excel, PowerPoint, OneNote), suporte futuro planejado para OneDrive for Business| Office 365 SharePoint Online| macOS|
| Aplicativo Office Yammer| Office 365 Yammer| Windows 10, iOS, Android|
| Aplicativo PowerBI (atualmente sem suporte em Android)| Serviço PowerBI| Windows 10, Windows 8.1, Windows 7 e iOS|
| Aplicativo Visual Studio Team Services| Visual Studio Team Services| Windows 10, Windows 8.1, Windows 7, iOS, Android|



## <a name="approved-client-app-requirement"></a>Requisito de aplicativo cliente aprovado 

Controle as conexões do cliente, utilizando a opção **Exigir o aplicativo do cliente aprovado** na sua política de acesso condicional. O acesso é concedido somente quando uma tentativa de conexão é realizada por um aplicativo cliente aprovado.

![Controlar acesso para aplicativos cliente aprovados](./media/active-directory-conditional-access-technical-reference/21.png)

Os seguintes aplicativos cliente podem ser utilizados com o requisito de aplicativo cliente aprovado:

- Microsoft Excel

- Microsoft OneDrive

- Microsoft Outlook

- Microsoft OneNote

- Microsoft PowerPoint

- Microsoft SharePoint

- Microsoft Skype for Business

- Equipes da Microsoft

- Microsoft Visio

- Microsoft Word


**Comentários**

- Os aplicativos cliente aprovados fornecem suporte ao recurso de gerenciamento de aplicativo móvel Intune.

- O requisito **Exigir o aplicativo do cliente aprovado**:

    - Fornece suporte apenas para iOS e Android para [condição de plataforma de dispositivo](#device-platforms-condition).

    - Não fornece suporte à opção **Navegador** para a [condição de aplicativos cliente](#supported-browsers).
    
    - Substitui a opção **Aplicativos móveis e cliente de área de trabalho** para a [condição de aplicativos cliente](#supported-mobile-apps-and-desktop-clients) quando essa opção é selecionada.


## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do acesso condicional, consulte [Acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md).
- Se estiver pronto para configurar políticas de acesso condicional em seu ambiente, consulte as [práticas recomendadas para acesso condicional no Azure Active Directory](active-directory-conditional-access-best-practices.md).



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png


