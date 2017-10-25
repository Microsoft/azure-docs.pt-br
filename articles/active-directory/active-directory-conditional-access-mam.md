---
title: Acesso condicional com base no aplicativo do Azure Active Directory | Microsoft Docs
description: Saiba como funciona o acesso condicional com base no aplicativo do Azure Active Directory.
services: active-directory
keywords: "acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/07/2017
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 48c9f55e2296b88acc697ab818f13787695643a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-app-based-conditional-access"></a>Acesso condicional baseado em aplicativo do Azure Active Directory  

Os funcionários usam dispositivos móveis para tarefas de pessoais e corporativas. Ao mesmo tempo que garante que seus funcionários sejam produtivos, você também quer impedir a perda de dados. Com o acesso condicional com base no aplicativo do Azure Active Directory (Azure AD), você pode restringir o acesso aos seus aplicativos de nuvem a aplicativos cliente que possam proteger seus dados corporativos.  

Este tópico explica como configurar o acesso condicional com base no aplicativo do Azure AD.

## <a name="overview"></a>Visão geral

Com o [acesso condicional do Azure AD](active-directory-conditional-access-azure-portal.md), você pode ajustar como os usuários autorizados podem acessar seus recursos. Por exemplo, você pode limitar o acesso aos seus aplicativos de nuvem a dispositivos confiáveis.

Você pode usar [políticas de proteção do aplicativo Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da empresa. As políticas de proteção do aplicativo Intune não exigem a solução de gerenciamento de dispositivo móvel (MDM), que permite que você proteja os dados da sua empresa com ou sem registro de dispositivos em uma solução de gerenciamento de dispositivos.

O acesso condicional baseado em aplicativo do Azure Active Directory permite que você limite o acesso a seus aplicativos de nuvem compatíveis com as políticas de proteção do aplicativo Intune. Por exemplo, você pode restringir o acesso ao Exchange Online para o aplicativo do Outlook.

Na terminologia de acesso condicional, esses aplicativos cliente são conhecidos como **aplicativos cliente aprovados**.  


![Acesso condicional](./media/active-directory-conditional-access-mam/05.png)


Para obter uma lista de aplicativos cliente aprovada, veja [requisito de aplicativo cliente aprovado](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).


Você pode combinar políticas de acesso condicional baseado em aplicaitvos com outras políticas, como as [políticas de acesso condicional baseado em dispositivo](active-directory-conditional-access-policy-connected-applications.md) para fornecer flexibilidade na forma de proteger dados para os dispositivos pessoais e corporativos.

 


##<a name="before-you-begin"></a>Antes de começar

Este tópico pressupõe que você esteja familiarizado com:

- A referência técnica do [requisito de aplicativo cliente aprovado](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).


- Os conceitos básicos do [acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- Como [configurar uma política de acesso condicional](active-directory-conditional-access-azure-portal-get-started.md).

- A [migração das políticas de acesso condicional](active-directory-conditional-access-best-practices.md#policy-migration).
 

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar uma política de acesso condicional com base no aplicativo, você deve ter uma assinatura Premium do Azure Active Directory ou do Enterprise Mobility + Security, e os usuários devem ser licenciados para o EMS ou o Azure AD. 


## <a name="exchange-online-policy"></a>Política do Exchange Online 

Este cenário consiste em uma política de acesso condicional com base no aplicativo para acesso ao Exchange Online.


### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que um usuário:

- Configura o email usando um aplicativo de email nativo no iOS ou Android para se conectar ao Exchange

- Recebe um email que indica que o acesso só está disponível usando o aplicativo Outlook

- Baixa o aplicativo com o link

- Abre o aplicativo Outlook e o assina com as credenciais do Azure AD

- É solicitado a instalar o Authenticor (iOS) ou o Portal da Empresa (Android) para continuar

- Instala o aplicativo e pode retornar ao aplicativo Outlook para continuar

- É solicitado a registrar um dispositivo

- É capaz de acessar o email

Quaisquer eventuais políticas de Proteção de Aplicativo do Intune são ativadas no momento do acesso aos dados corporativos e podem solicitar que o usuário reinicie o aplicativo, que use um PIN adicional, etc. (se configuradas para o aplicativo e a plataforma).

### <a name="configuration"></a>Configuração 

**Etapa 1 – configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/01.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.

3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**:

    a. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/03.png)

    b. Como **Aplicativos cliente**, selecione **Aplicativos móveis e aplicativos da área de trabalho**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/04.png)

5. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/05.png)
 

**Etapa 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (Active Sync)**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/06.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.


3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condições:** como **Condições**, você precisa configurar **Aplicativos cliente**. 

    a. Como **Aplicativos cliente**, selecione **Exchange Active Sync**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/08.png)

    b. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/05.png)


**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS**


![Acesso condicional](./media/active-directory-conditional-access-mam/09.png)

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="exchange-online-and-sharepoint-online-policy"></a>Política do Exchange Online e do SharePoint Online

Este cenário consiste em um acesso condicional com política de gerenciamento de aplicativo móvel para acesso ao Exchange Online e ao SharePoint Online com aplicativos aprovados.

### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que um usuário:

- Tenta usar o aplicativo SharePoint para se conectar e para exibir os sites corporativos desse usuário

- Tenta entrar com as mesmas credenciais usadas pelo aplicativo Outlook

- Não precisa registrar novamente e pode obter acesso aos recursos


### <a name="configuration"></a>Configuração

**Etapa 1 – configurar uma política de acesso condicional do Azure AD para o Exchange Online e o SharePoint Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/71.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.


3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online** e o **Office 365 SharePoint Online**. 

    ![Acesso condicional](./media/active-directory-conditional-access-mam/02.png)

4. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**:

    a. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/03.png)

    b. Como **Aplicativos cliente**, selecione **Aplicativos móveis e aplicativos da área de trabalho**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/04.png)

5. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/05.png)




**Etapa 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (Active Sync)**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/06.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.

3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. Online 

    ![Acesso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condições:** como **Condições**, você precisa configurar **Aplicativos cliente**:

    a. Como **Aplicativos cliente**, selecione **Exchange Active Sync**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/08.png)

    b. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/05.png)




**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS**


![Acesso condicional](./media/active-directory-conditional-access-mam/09.png)

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Política de dispositivo compatível ou com base no aplicativo para o Exchange Online e o SharePoint Online

Este cenário consiste em uma política de acesso condicional de dispositivo compatível ou com base no aplicativo para obter acesso ao Exchange Online.


### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que:
 
- Alguns usuários já estão registrados (com ou sem dispositivos corporativos)

- Os usuários que não estão registrados com o Azure AD usando um aplicativo protegido por aplicativo precisam registrar um dispositivo para acessar os recursos

- Os usuários registrados usando o aplicativo protegido não precisam registrar novamente o dispositivo


### <a name="configuration"></a>Configuração

**Etapa 1 – configurar uma política de acesso condicional do Azure AD para o Exchange Online e o SharePoint Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/62.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.

3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online** e o **Office 365 SharePoint Online**. 

     ![Acesso condicional](./media/active-directory-conditional-access-mam/02.png)

4. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**. 
 
    a. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/03.png)

    b. Como **Aplicativos cliente**, selecione **Aplicativos móveis e aplicativos da área de trabalho**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/04.png)

5. Como **Controles de acesso**, você precisa ter o seguinte selecionado:

    - **Exigir que o dispositivo seja marcado como em conformidade**

    - **Exigir um aplicativo cliente aprovado (versão prévia)**

    - **Exigir um dos controles selecionados**   
 
    ![Acesso condicional](./media/active-directory-conditional-access-mam/11.png)



**Etapa 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (Active Sync)**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/61.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.

3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. 

    ![Acesso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condições:** como **Condições**, você precisa configurar **Aplicativos cliente**. 

    Como **Aplicativos cliente*, selecione **Exchange Active Sync**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/08.png)

5. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.
 
    ![Acesso condicional](./media/active-directory-conditional-access-mam/11.png)




**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS**


![Acesso condicional](./media/active-directory-conditional-access-mam/09.png)

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).





## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Política de dispositivo compatível e com base no aplicativo para o Exchange Online e o SharePoint Online

Este cenário consiste em uma política de acesso condicional de dispositivo compatível e com base no aplicativo para obter acesso ao Exchange Online.


### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que um usuário:
 
-   Configura o email usando um aplicativo de email nativo no iOS ou Android para se conectar ao Exchange
-   Recebe um email que indica que o acesso requer que seu dispositivo seja registrado
-   Baixa e entra no Portal da Empresa
-   Verifica o email e é solicitado a usar o aplicativo Outlook
-   Baixa o aplicativo Outlook
-   Abre o aplicativo Outlook e insere as credenciais usadas no registro
-   O usuário é capaz de acessar o email

Quaisquer eventuais políticas de Proteção de Aplicativo do Intune são ativadas no momento do acesso aos dados corporativos e podem solicitar que o usuário reinicie o aplicativo, que use um PIN adicional, etc. (se configuradas para o aplicativo e a plataforma)


### <a name="configuration"></a>Configuração

**Etapa 1 – configurar uma política de acesso condicional do Azure AD para o Exchange Online e o SharePoint Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/62.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.

3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online** e o **Office 365 SharePoint Online**. 

     ![Acesso condicional](./media/active-directory-conditional-access-mam/02.png)

4. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**. 
 
    a. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/03.png)

    b. Como **Aplicativos cliente**, selecione **Aplicativos móveis e aplicativos da área de trabalho**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/04.png)

5. Como **Controles de acesso**, você precisa ter o seguinte selecionado:

    - **Exigir que o dispositivo seja marcado como em conformidade**

    - **Exigir um aplicativo cliente aprovado (versão prévia)**

    - **Exigir um dos controles selecionados**   
 
    ![Acesso condicional](./media/active-directory-conditional-access-mam/11.png)



**Etapa 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (Active Sync)**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/61.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.

3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. 

    ![Acesso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condições:** como **Condições**, você precisa configurar **Aplicativos cliente**. 

    Como **Aplicativos cliente**, selecione **Exchange Active Sync**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/08.png)

5. Como **Controles de acesso**, você precisa ter o seguinte selecionado:

    - **Exigir que o dispositivo seja marcado como em conformidade**

    - **Exigir um aplicativo cliente aprovado (versão prévia)**

    - **Exigir todos os controles selecionados**   
 
    ![Acesso condicional](./media/active-directory-conditional-access-mam/64.png)




**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS**


![Acesso condicional](./media/active-directory-conditional-access-mam/09.png)

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).






## <a name="next-steps"></a>Próximas etapas

Se você quiser saber como configurar uma política de acesso condicional, veja [Introdução ao acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](active-directory-conditional-access-best-practices.md). 