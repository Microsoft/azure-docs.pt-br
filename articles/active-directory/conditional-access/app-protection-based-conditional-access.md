---
title: Exigir política de proteção de aplicativo para acesso ao aplicativo de nuvem com acesso condicional no Azure Active Directory | Microsoft Docs
description: Saiba como exigir a política de proteção de aplicativo para acesso ao aplicativo de nuvem com acesso condicional no Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 4/4/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e8b7cc1f3a8431986ffbaac604ec5863236f112
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68357110"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Exigir política de proteção de aplicativo para acesso ao aplicativo de nuvem com acesso condicional (versão prévia)

Os funcionários usam dispositivos móveis para tarefas de pessoais e corporativas. Ao mesmo tempo que garante que seus funcionários sejam produtivos, você também quer impedir a perda de dados. Com o acesso condicional do Azure Active Directory (AD do Azure), você pode proteger seus dados corporativos restringindo o acesso aos seus aplicativos de nuvem. Use aplicativos cliente primeiro com uma política de proteção de aplicativo.

Este artigo explica como configurar políticas de acesso condicional que podem exigir uma política de proteção de aplicativo antes que o acesso seja concedido aos dados.

## <a name="overview"></a>Visão geral

Com o [acesso condicional do Azure ad](overview.md), você pode ajustar como os usuários autorizados podem acessar seus recursos. Por exemplo, você pode limitar o acesso aos seus aplicativos de nuvem a dispositivos confiáveis.

Você pode usar [políticas de proteção do aplicativo Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da empresa. As políticas de proteção de aplicativo do Intune não exigem uma solução de MDM (gerenciamento de dispositivo móvel). Você pode proteger os dados da sua empresa com ou sem registrar dispositivos em uma solução de gerenciamento de dispositivo.

Azure Active Directory acesso condicional restringe o acesso aos seus aplicativos de nuvem para aplicativos cliente que o Intune relatou ao Azure AD como recebendo uma política de proteção de aplicativo. Por exemplo, você pode restringir o acesso ao Exchange Online para o aplicativo do Outlook que tem uma política de proteção de aplicativo do Intune.

Na terminologia de acesso condicional, esses aplicativos cliente são conhecidos como política protegidas por uma *política de proteção de aplicativo*.  

![Acesso Condicional](./media/app-protection-based-conditional-access/05.png)

Para obter uma lista de aplicativos cliente protegidos por política, consulte [requisito de política de proteção de aplicativo](technical-reference.md#approved-client-app-requirement).

Você pode combinar políticas de acesso condicional com base em proteção de aplicativo com outras políticas, como [políticas de acesso condicional com base no dispositivo](require-managed-devices.md). Dessa forma, você pode fornecer flexibilidade em como proteger dados para dispositivos pessoais e corporativos.

> [!NOTE]
> As políticas de proteção de aplicativo de acesso condicional não podem ser aplicadas a usuários B2B porque a organização que está convidando não tem visibilidade da organização inicial do usuário B2B.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Benefícios do requisito de acesso condicional baseado em proteção do aplicativo

Semelhante à conformidade que é relatada pelo Intune para iOS e Android para um dispositivo gerenciado, o Intune agora se reporta ao Azure AD se uma política de proteção de aplicativo for aplicada. O acesso condicional pode usar essa política como uma verificação de acesso. Essa nova política de acesso condicional, a política de proteção de aplicativo, aumenta a segurança. Ele protege contra erros de administrador, como:

- Usuários que não têm uma licença do Intune.
- Usuários que não podem receber uma política de proteção de aplicativo do Intune.
- Aplicativos de política de proteção de aplicativo do Intune que não estão configurados para receber uma política.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você esteja familiarizado com:

- A referência técnica do [requisito de política de proteção de aplicativo](technical-reference.md#app-protection-policy-requirement) .
- A referência técnica do [requisito de aplicativo cliente aprovado](technical-reference.md#approved-client-app-requirement).
- Os conceitos básicos de [acesso condicional no Azure Active Directory](overview.md).
- Como [Configurar uma política de acesso condicional](app-based-mfa.md).

## <a name="prerequisites"></a>Pré-requisitos

Para criar uma política de acesso condicional com base na proteção do aplicativo, você deve:

- Ter um Enterprise Mobility + Security ou uma assinatura Azure Active Directory Premium + Intune.
- Verifique se os usuários estão licenciados para Enterprise Mobility + Security ou Azure AD + Intune.
- Verifique se o aplicativo cliente está configurado no Intune para receber uma política de proteção de aplicativo.
- Verifique se os usuários estão configurados no Intune para receber uma política de proteção de aplicativo do Intune.

## <a name="app-protection-based-policy-for-exchange-online"></a>Política baseada em proteção de aplicativo para o Exchange Online

Esse cenário consiste em uma política de acesso condicional baseada em proteção de aplicativo para acesso ao Exchange Online.

### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que um usuário:

- Configura o email usando um aplicativo de email nativo no iOS ou Android para se conectar ao Exchange.
- Recebe um email que indica que o acesso está disponível apenas usando o aplicativo Outlook.
- Baixa o aplicativo com o link.
- Abre o aplicativo do Outlook e entra com as credenciais do Azure AD.
- É solicitado a instalar o **aplicativo Microsoft Authenticator** ou o **portal da empresa do Intune** para continuar.
- Instala o aplicativo e retorna ao aplicativo Outlook para continuar.
- É solicitado a registrar um dispositivo.
- Pode receber uma política de proteção de aplicativo do Intune.
- Pode acessar o email.

Todas as políticas de proteção de aplicativo do Intune devem estar no aplicativo para acessar dados corporativos. As políticas podem solicitar que o usuário reinicie o aplicativo ou use um PIN adicional. Esse é o caso se as políticas estiverem configuradas para o aplicativo e a plataforma.

### <a name="configuration"></a>Configuração

**Etapa 1: Configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional nesta etapa, configure os seguintes componentes:

![Acesso Condicional](./media/app-protection-based-conditional-access/01.png)

1. Insira o nome da política de acesso condicional.
1. Em **atribuições**, em **usuários e grupos**, selecione pelo menos um usuário ou grupo para cada política de acesso condicional.
1. Em **aplicativos de nuvem**, selecione **Office 365 Exchange Online**.

   ![Acesso Condicional](./media/app-protection-based-conditional-access/07.png)

1. Em **condições**, configurar **plataformas de dispositivo** e **aplicativos cliente (versão prévia)** :
   1. Em **plataformas de dispositivo**, selecione **Android** e **Ios**.

      ![Acesso Condicional](./media/app-protection-based-conditional-access/03.png)

   1. Em **aplicativos cliente (versão prévia)** , selecione **aplicativos móveis e clientes de área de trabalho** e clientes de **autenticação moderna**.

      ![Acesso Condicional](./media/app-protection-based-conditional-access/91.png)

1. Em **controles de acesso**, selecione **exigir política de proteção de aplicativo (versão prévia)** .

   ![Acesso Condicional](./media/app-protection-based-conditional-access/05.png)

**Etapa 2: Configurar uma política de acesso condicional do Azure AD para o Exchange Online com o ActiveSync (EAS)**

Para a política de acesso condicional nesta etapa, configure os seguintes componentes:

![Acesso Condicional](./media/app-protection-based-conditional-access/06.png)

1. Insira o nome da política de acesso condicional.
1. Em **atribuições**, em **usuários e grupos**, selecione pelo menos um usuário ou grupo para cada política de acesso condicional.
1. Em **aplicativos de nuvem**, selecione **Office 365 Exchange Online**.

   ![Acesso Condicional](./media/app-protection-based-conditional-access/07.png)

1. Em **condições**, configure **aplicativos cliente (versão prévia)** . 

   1. Em **aplicativos cliente (versão prévia)** , selecione **aplicativos móveis e clientes de área de trabalho** e clientes do **Exchange ActiveSync**.

      ![Acesso Condicional](./media/app-protection-based-conditional-access/92.png)

   1. Em **controles de acesso**, selecione **exigir política de proteção de aplicativo (versão prévia)** .

      ![Acesso Condicional](./media/app-protection-based-conditional-access/05.png)

**Etapa 3: Configurar a política de proteção de aplicativo do Intune para aplicativos cliente iOS e Android**

![Acesso Condicional](./media/app-protection-based-conditional-access/09.png)

Para obter mais informações, consulte [proteger aplicativos e dados com Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Política de dispositivo compatível ou com base na proteção do aplicativo para o Exchange Online

Esse cenário consiste em uma política de acesso condicional de dispositivo compatível ou baseada em proteção de aplicativo para acesso ao Exchange Online.

### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que:
 
- Um usuário já está registrado, com ou sem dispositivos corporativos.
- Os usuários que não estão inscritos e registrados com o Azure AD usando um aplicativo protegido por aplicativo precisam registrar um dispositivo para acessar recursos.
- Os usuários registrados que usam o aplicativo protegido do aplicativo não precisam registrar o dispositivo novamente.
- O usuário poderá receber uma política de proteção de aplicativo do Intune se não estiver registrado.
- O usuário pode acessar o email com o Outlook e uma política de proteção de aplicativo do Intune, se não estiver registrado.
- O usuário poderá acessar o email com o Outlook se o dispositivo estiver registrado.

### <a name="configuration"></a>Configuração

**Etapa 1: Configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional nesta etapa, configure os seguintes componentes:

![Acesso Condicional](./media/app-protection-based-conditional-access/62.png)

1. Insira o nome da política de acesso condicional.
1. Em **atribuições**, em **usuários e grupos**, selecione pelo menos um usuário ou grupo para cada política de acesso condicional.
1. Em **aplicativos de nuvem**, selecione **Office 365 Exchange Online**. 

   ![Acesso Condicional](./media/app-protection-based-conditional-access/07.png)

1. Em **condições**, configure **plataformas de dispositivo** e **aplicativos cliente (versão prévia)** . 
 
   1. Em **plataformas de dispositivo**, selecione **Android** e **Ios**.

      ![Acesso Condicional](./media/app-protection-based-conditional-access/03.png)

   1. Em **aplicativos cliente (versão prévia)** , selecione **aplicativos móveis e clientes de área de trabalho** e clientes de **autenticação moderna**.

      ![Acesso Condicional](./media/app-protection-based-conditional-access/91.png)

5. Em **controles de acesso**, selecione as seguintes opções:
   - **Exigir que o dispositivo seja marcado como em conformidade**
   - **Exigir política de proteção de aplicativo (visualização)**
   - **Exigir um dos controles selecionados**   
 
      ![Acesso Condicional](./media/app-protection-based-conditional-access/11.png)

**Etapa 2: Configurar uma política de acesso condicional do Azure AD para o Exchange Online com o ActiveSync**

Para a política de acesso condicional nesta etapa, configure os seguintes componentes:

![Acesso Condicional](./media/app-protection-based-conditional-access/06.png)

1. Insira o nome da política de acesso condicional.
1. Em **atribuições**, em **usuários e grupos**, selecione pelo menos um usuário ou grupo para cada política de acesso condicional.
1. Em **aplicativos de nuvem**, selecione **Office 365 Exchange Online**. 

   ![Acesso Condicional](./media/app-protection-based-conditional-access/07.png)

1. Em **condições**, configure **aplicativos cliente (versão prévia)** . 

   Em **aplicativos cliente (versão prévia)** , selecione **aplicativos móveis e clientes de área de trabalho** e clientes do **Exchange ActiveSync**.

   ![Acesso Condicional](./media/app-protection-based-conditional-access/92.png)

1. Em **controles de acesso**, selecione as seguintes opções:
   - **Exigir que o dispositivo seja marcado como em conformidade**
   - **Exigir política de proteção de aplicativo (visualização)**
   - **Exigir um dos controles selecionados**

      ![Acesso Condicional](./media/app-protection-based-conditional-access/11.png)

**Etapa 3: Configurar a política de proteção de aplicativo do Intune para aplicativos cliente iOS e Android**

![Acesso Condicional](./media/app-protection-based-conditional-access/09.png)

Para obter mais informações, consulte [proteger aplicativos e dados com Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Política de dispositivo compatível e com base na proteção do aplicativo para o Exchange Online

Esse cenário consiste em uma política de acesso condicional de dispositivo compatível e baseada em proteção de aplicativo para acesso ao Exchange Online.

### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que um usuário:
 
- Configura o email usando um aplicativo de email nativo no iOS ou Android para se conectar ao Exchange.
- Recebe um email que indica que o acesso exige que seu dispositivo seja registrado.
- Baixa Portal da Empresa do Intune e entra no Portal.
- Verifica o email e é solicitado a usar o aplicativo Outlook.
- Baixa o aplicativo Outlook.
- Abre o aplicativo Outlook e insere as credenciais usadas no registro.
- Pode receber uma política de proteção de aplicativo do Intune.
- Pode acessar email com o Outlook e uma política de proteção de aplicativo do Intune.

Todas as políticas de proteção de aplicativo do Intune são ativadas antes que o acesso seja concedido aos dados corporativos. As políticas podem solicitar que o usuário reinicie o aplicativo ou use um PIN adicional. Esse é o caso se as políticas estiverem configuradas para o aplicativo e a plataforma.

### <a name="configuration"></a>Configuração

**Etapa 1: Configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional nesta etapa, configure os seguintes componentes:

![Acesso Condicional](./media/app-protection-based-conditional-access/01.png)

1. Insira o nome da política de acesso condicional.
1. Em **atribuições**, em **usuários e grupos**, selecione pelo menos um usuário ou grupo para cada política de acesso condicional.
1. Em **aplicativos de nuvem**, selecione **Office 365 Exchange Online**. 

   ![Acesso Condicional](./media/app-protection-based-conditional-access/07.png)

1. Em **condições**, configure **plataformas de dispositivo** e **aplicativos cliente (versão prévia)** . 
   1. Em **plataformas de dispositivo**, selecione **Android** e **Ios**.

      ![Acesso Condicional](./media/app-protection-based-conditional-access/03.png)

   1. Em **aplicativos cliente (versão prévia)** , selecione **aplicativos móveis e clientes de área de trabalho** e clientes de **autenticação moderna**.

      ![Acesso Condicional](./media/app-protection-based-conditional-access/91.png)

1. Em **controles de acesso**, selecione as seguintes opções:
   - **Exigir que o dispositivo seja marcado como em conformidade**
   - **Exigir política de proteção de aplicativo (visualização)**
   - **Exigir todos os controles selecionados**   
 
      ![Acesso Condicional](./media/app-protection-based-conditional-access/13.png)

**Etapa 2: Configurar uma política de acesso condicional do Azure AD para o Exchange Online com o ActiveSync**

Para a política de acesso condicional nesta etapa, configure os seguintes componentes:

![Acesso Condicional](./media/app-protection-based-conditional-access/06.png)

1. Insira o nome da política de acesso condicional.
1. Em **atribuições**, em **usuários e grupos**, selecione pelo menos um usuário ou grupo para cada política de acesso condicional.
1. Em **aplicativos de nuvem**, selecione **Office 365 Exchange Online**. 

   ![Acesso Condicional](./media/app-protection-based-conditional-access/07.png)

1. Em **condições**, configure **aplicativos cliente (versão prévia)** . 

   Em **aplicativos cliente (versão prévia)** , selecione **aplicativos móveis e clientes de área de trabalho** e clientes do **Exchange ActiveSync**.

   ![Acesso Condicional](./media/app-protection-based-conditional-access/92.png)

1. Em **controles de acesso**, selecione as seguintes opções:
   - **Exigir que o dispositivo seja marcado como em conformidade**
   - **Exigir política de proteção de aplicativo (visualização)**
   - **Exigir todos os controles selecionados**   
 
      ![Acesso Condicional](./media/app-protection-based-conditional-access/13.png)

**Etapa 3: Configurar a política de proteção de aplicativo do Intune para aplicativos cliente iOS e Android**

![Acesso Condicional](./media/app-protection-based-conditional-access/09.png)

Para obter mais informações, consulte [proteger aplicativos e dados com Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Política baseada em proteção de aplicativo ou em aplicativo para o Exchange Online e o SharePoint Online

Esse cenário consiste em uma política de aplicativos com base na proteção de aplicativo ou aprovado para acesso ao Exchange Online e ao SharePoint Online.

### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que um usuário:

- Configura aplicativos cliente que estão na lista de aplicativos que dão suporte ao requisito de política de proteção de aplicativo ou ao requisito de aplicativos aprovados.  
- Usa aplicativos cliente que atendem ao requisito de política de proteção de aplicativo e podem receber uma política de proteção de aplicativo do Intune.
- Usa aplicativos cliente que atendem ao requisito de política de aplicativos aprovados que dão suporte à política de proteção de aplicativo do Intune.
- Abre o aplicativo para acessar emails ou documentos.
- Abre o aplicativo do Outlook e entra com as credenciais do Azure AD.
- É solicitado a instalar qualquer Microsoft Authenticator para uso do iOS ou Portal da Empresa do Intune para uso do Android se eles ainda não estiverem instalados.
- Instala o aplicativo e pode retornar ao aplicativo Outlook para continuar.
- É solicitado a registrar um dispositivo.
- Pode receber uma política de proteção de aplicativo do Intune.
- Pode acessar email com o Outlook e uma política de proteção de aplicativo do Intune.
- Pode acessar sites e documentos com um aplicativo que não está no requisito de política de proteção de aplicativo, mas listado no requisito de aplicativo aprovado.

Todas as políticas de proteção de aplicativo do Intune são necessárias antes que o acesso seja concedido aos dados corporativos. As políticas podem solicitar que o usuário reinicie o aplicativo ou use um PIN adicional. Esse é o caso se as políticas estiverem configuradas para o aplicativo e a plataforma.

**Comentários**

- Você pode usar esse cenário se quiser dar suporte a políticas de acesso condicional baseadas em aplicativo e com base em proteção de aplicativo.
- Nesta política *ou* , os aplicativos com um requisito de política de proteção de aplicativo são avaliados primeiro para acesso antes do requisito de aplicativos cliente aprovados.

### <a name="configuration"></a>Configuração

**Etapa 1: Configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional nesta etapa, configure os seguintes componentes:

![Acesso Condicional](./media/app-protection-based-conditional-access/62.png)

1. Insira o nome da política de acesso condicional.
1. Em **atribuições**, em **usuários e grupos**, selecione pelo menos um usuário ou grupo para cada política de acesso condicional.
1. Em **aplicativos de nuvem**, selecione **Office 365 Exchange Online**. 

   ![Acesso Condicional](./media/app-protection-based-conditional-access/02.png)

1. Em **condições**, configure **plataformas de dispositivo** e **aplicativos cliente (versão prévia)** . 
   1. Em **plataformas de dispositivo**, selecione **Android** e **Ios**.

      ![Acesso Condicional](./media/app-protection-based-conditional-access/03.png)

   1. Em **aplicativos cliente (versão prévia)** , selecione **aplicativos móveis e clientes de área de trabalho** e clientes de **autenticação moderna**.

      ![Acesso Condicional](./media/app-protection-based-conditional-access/91.png)

1. Em **controles de acesso**, selecione as seguintes opções:
   - **Exigir aplicativo cliente aprovado**
   - **Exigir política de proteção de aplicativo (visualização)**
   - **Exigir um dos controles selecionados**
 
      ![Acesso Condicional](./media/app-protection-based-conditional-access/12.png)

**Etapa 2: Configurar a política de proteção de aplicativo do Intune para aplicativos cliente iOS e Android**

![Acesso Condicional](./media/app-protection-based-conditional-access/09.png)

Para obter mais informações, consulte [proteger aplicativos e dados com Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="next-steps"></a>Próximas etapas

- Se você quiser saber como configurar uma política de Acesso Condicional, consulte [Exigir MFA para aplicativos específicos com Acesso Condicional do Azure Active Directory](app-based-mfa.md).
- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, consulte [práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md).
