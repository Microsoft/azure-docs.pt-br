---
title: Adicionar políticas de proteção de aplicativo com acesso condicional - Azure Active Directory
description: Saiba como exigir políticas de proteção de aplicativo para o acesso ao aplicativo em nuvem com acesso condicional no Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 720cd46ac37a82f56aa37c0041ca8d92db177071
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99575764"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Como fazer: Exigir políticas de proteção de aplicativo e um aplicativo cliente aprovado para acesso ao aplicativo em nuvem com acesso condicional

As pessoas usam regularmente seus dispositivos móveis para tarefas pessoais e de trabalho. Enquanto garantem a produtividade de suas equipes, as organizações também desejam evitar a perda de dados por parte de aplicativos potencialmente não seguros. Com o acesso condicional, as organizações podem restringir o acesso a aplicativos cliente aprovados (com capacidade de autenticação moderna) usando as políticas de proteção de aplicativo do Intune aplicadas a eles.

Este artigo apresenta três cenários para configurar políticas de acesso condicional para recursos como Microsoft 365, Exchange Online e SharePoint.

- [Cenário 1: aplicativos de Microsoft 365 exigem aplicativos aprovados com políticas de proteção de aplicativo](#scenario-1-microsoft-365-apps-require-approved-apps-with-app-protection-policies)
- [Cenário 2: Os aplicativos de navegador exigem aplicativos aprovados com políticas de proteção de aplicativo](#scenario-2-browser-apps-require-approved-apps-with-app-protection-policies)
- [Cenário 3: o Exchange Online e o SharePoint exigem um aplicativo cliente aprovado e uma política de proteção de aplicativo](#scenario-3-exchange-online-and-sharepoint-require-an-approved-client-app-and-app-protection-policy)

No acesso condicional, esses aplicativos cliente são sabidamente protegidos por uma política de proteção de aplicativo. Mais informações sobre as políticas de proteção de aplicativo podem ser encontradas no artigo [Visão geral das políticas de proteção de aplicativo](/intune/apps/app-protection-policy)

> [!WARNING]
> Nem todos os aplicativos têm suporte como aplicativos aprovados ou dão suporte a políticas de proteção de aplicativo. Para obter uma lista de aplicativos cliente qualificados, confira [Requisito de política de proteção de aplicativo](concept-conditional-access-grant.md#require-app-protection-policy).

> [!NOTE]
> "Exigir um dos controles selecionados" em conceder controles é como uma cláusula OR. Isso é usado na política para permitir que os usuários utilizem aplicativos que suportam a **política exigir proteção de aplicativo** ou exigem controles de concessão de **aplicativo cliente aprovados** . **Exigir** que a política de proteção de aplicativo seja imposta quando o aplicativo der suporte a esse controle de concessão. Para saber mais sobre quais aplicativos dão suporte ao controle de concessão **Exigir política de proteção de aplicativo**, confira [Requisito de política de proteção de aplicativo](concept-conditional-access-grant.md#require-app-protection-policy).

## <a name="scenario-1-microsoft-365-apps-require-approved-apps-with-app-protection-policies"></a>Cenário 1: aplicativos de Microsoft 365 exigem aplicativos aprovados com políticas de proteção de aplicativo

Nesse cenário, a Contoso decidiu que todo o acesso móvel aos recursos Microsoft 365 deve usar aplicativos cliente aprovados, como o Outlook Mobile e o OneDrive, protegidos por uma política de proteção de aplicativo antes de receber o acesso. Todos os seus usuários já entram com as credenciais do Azure AD e têm licenças atribuídas a eles que incluem o Azure AD Premium P1 ou P2 e o Microsoft Intune.

As organizações devem concluir as etapas a seguir para exigir o uso de um aplicativo cliente aprovado em dispositivos móveis.

**Etapa 1: configurar uma política de acesso condicional do Azure AD para Microsoft 365**

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições**, selecione **Usuários e grupos**.
   1. Em **Incluir**, selecione **Todos os usuários** ou os **usuários e grupos** específicos aos quais você deseja aplicar essa política. 
   1. Selecione **Concluído**.
1. Em **Aplicativos de nuvem ou ações** > **Incluir**, selecione **Office 365**.
1. Em **Condições**, selecione **Plataformas de dispositivo**.
   1. Defina **Configurar** como **Sim**.
   1. Inclua **Android** e **iOS**.
1. Em **condições**, selecione **aplicativos cliente**.
   1. Defina **Configurar** como **Sim**.
   1. Selecione **aplicativos móveis e clientes de área de trabalho** e desmarque tudo o mais.
1. Em **Controles de acesso** > **Conceder**, selecione as seguintes opções:
   - **Exigir um aplicativo cliente aprovado**
   - **Requer política de proteção do aplicativo**
   - **Exigir um dos controles selecionados**
1. Confirme suas configurações e defina **Habilitar política** como **Ativado**.
1. Selecione **Criar** para criar e habilitar sua política.

**Etapa 2: Configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (ActiveSync)**

Para a política de acesso condicional nesta etapa, configure os seguintes componentes:

1. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições**, selecione **Usuários e grupos**.
   1. Em **Incluir**, selecione **Todos os usuários** ou os **usuários e grupos** específicos aos quais você deseja aplicar essa política. 
   1. Selecione **Concluído**.
1. Em **Aplicativos de nuvem ou ações** > **Incluir**, selecione **Office 365 Exchange Online**.
1. Em **condições**, selecione **aplicativos cliente**:
   1. Defina **Configurar** como **Sim**.
   1. Selecione **clientes do Exchange ActiveSync** e desmarque tudo o mais.
1. Em **Controles de acesso** > **Conceder**, selecione **Conceder acesso**, **Exigir política de proteção de aplicativo** e selecione **Selecionar**.
1. Confirme suas configurações e defina **Habilitar política** como **Ativado**.
1. Selecione **Criar** para criar e habilitar sua política.

**Etapa 3: Configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS**

Confira o artigo [Como criar e atribuir políticas de proteção de aplicativo](/intune/apps/app-protection-policies) para obter as etapas necessárias para criar políticas de proteção de aplicativo para Android e iOS. 

## <a name="scenario-2-browser-apps-require-approved-apps-with-app-protection-policies"></a>Cenário 2: Os aplicativos de navegador exigem aplicativos aprovados com políticas de proteção de aplicativo

Nesse cenário, a Contoso decidiu que todo acesso à navegação na Web móvel para Microsoft 365 recursos deve usar um aplicativo cliente aprovado, como o Edge para iOS e Android, protegido por uma política de proteção de aplicativo antes de receber o acesso. Todos os seus usuários já entram com as credenciais do Azure AD e têm licenças atribuídas a eles que incluem o Azure AD Premium P1 ou P2 e o Microsoft Intune.

As organizações devem concluir as etapas a seguir para exigir o uso de um aplicativo cliente aprovado em dispositivos móveis.

**Etapa 1: configurar uma política de acesso condicional do Azure AD para Microsoft 365**

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições**, selecione **Usuários e grupos**.
   1. Em **Incluir**, selecione **Todos os usuários** ou os **usuários e grupos** específicos aos quais você deseja aplicar essa política. 
   1. Selecione **Concluído**.
1. Em **Aplicativos de nuvem ou ações** > **Incluir**, selecione **Office 365**.
1. Em **Condições**, selecione **Plataformas de dispositivo**.
   1. Defina **Configurar** como **Sim**.
   1. Inclua **Android** e **iOS**.
1. Em **condições**, selecione **aplicativos cliente**.
   1. Defina **Configurar** como **Sim**.
   1. Selecione **navegador** e desmarque tudo o mais.
1. Em **Controles de acesso** > **Conceder**, selecione as seguintes opções:
   - **Exigir um aplicativo cliente aprovado**
   - **Requer política de proteção do aplicativo**
   - **Exigir um dos controles selecionados**
1. Confirme suas configurações e defina **Habilitar política** como **Ativado**.
1. Selecione **Criar** para criar e habilitar sua política.

**Etapa 2: Configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS**

Confira o artigo [Como criar e atribuir políticas de proteção de aplicativo](/intune/apps/app-protection-policies) para obter as etapas necessárias para criar políticas de proteção de aplicativo para Android e iOS. 

## <a name="scenario-3-exchange-online-and-sharepoint-require-an-approved-client-app-and-app-protection-policy"></a>Cenário 3: o Exchange Online e o SharePoint exigem um aplicativo cliente aprovado e uma política de proteção de aplicativo

Nesse cenário, a Contoso decidiu que os usuários só podem acessar dados de email e do SharePoint em dispositivos móveis desde que usem um aplicativo cliente aprovado, como o Outlook Mobile, protegido por uma política de proteção de aplicativo antes de receber o acesso. Todos os seus usuários já entram com as credenciais do Azure AD e têm licenças atribuídas a eles que incluem o Azure AD Premium P1 ou P2 e o Microsoft Intune.

As organizações devem concluir as seguintes três etapas para exigir o uso de um aplicativo cliente aprovado em dispositivos móveis e em cliente do Exchange ActiveSync.

**Etapa 1: política para clientes de autenticação moderna com base em Android e iOS que exigem o uso de um aplicativo cliente aprovado e uma política de proteção de aplicativo ao acessar o Exchange Online e o SharePoint.**

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições**, selecione **Usuários e grupos**.
   1. Em **Incluir**, selecione **Todos os usuários** ou os **usuários e grupos** específicos aos quais você deseja aplicar essa política. 
   1. Selecione **Concluído**.
1. Em **Aplicativos de nuvem ou ações** > **Incluir**, selecione **Office 365 Exchange Online** e **Office 365 SharePoint Online**.
1. Em **Condições**, selecione **Plataformas de dispositivo**.
   1. Defina **Configurar** como **Sim**.
   1. Inclua **Android** e **iOS**.
1. Em **condições**, selecione **aplicativos cliente**.
   1. Defina **Configurar** como **Sim**.
   1. Selecione **aplicativos móveis e clientes de área de trabalho** e desmarque tudo o mais.
1. Em **Controles de acesso** > **Conceder**, selecione as seguintes opções:
   - **Exigir um aplicativo cliente aprovado**
   - **Requer política de proteção do aplicativo**
   - **Exigir um dos controles selecionados**
1. Confirme suas configurações e defina **Habilitar política** como **Ativado**.
1. Selecione **Criar** para criar e habilitar sua política.

**Etapa 2: Política para clientes do Exchange ActiveSync que exigem o uso de um aplicativo cliente aprovado.**

1. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições**, selecione **Usuários e grupos**.
   1. Em **Incluir**, selecione **Todos os usuários** ou os **usuários e grupos** específicos aos quais você deseja aplicar essa política. 
   1. Selecione **Concluído**.
1. Em **Aplicativos de nuvem ou ações** > **Incluir**, selecione **Office 365 Exchange Online**.
1. Em **condições**, selecione **aplicativos cliente**:
   1. Defina **Configurar** como **Sim**.
   1. Selecione **clientes do Exchange ActiveSync** e desmarque tudo o mais.
1. Em **Controles de acesso** > **Conceder**, selecione **Conceder acesso**, **Exigir política de proteção de aplicativo** e selecione **Selecionar**.
1. Confirme suas configurações e defina **Habilitar política** como **Ativado**.
1. Selecione **Criar** para criar e habilitar sua política.

**Etapa 3: Configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS.**

Confira o artigo [Como criar e atribuir políticas de proteção de aplicativo](/intune/apps/app-protection-policies) para obter as etapas necessárias para criar políticas de proteção de aplicativo para Android e iOS. 

## <a name="next-steps"></a>Próximas etapas

[O que é acesso condicional?](overview.md)

[Componentes do acesso condicional](concept-conditional-access-policies.md)

[Políticas de acesso condicional comuns](concept-conditional-access-policy-common.md)

