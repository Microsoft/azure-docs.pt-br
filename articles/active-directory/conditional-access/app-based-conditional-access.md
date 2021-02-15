---
title: Aplicativos cliente aprovados com acesso condicional-Azure Active Directory
description: Saiba como exigir aplicativos cliente aprovados para acesso ao aplicativo de nuvem com acesso condicional no Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 122cc6a2be17cb35e77b638a60fc5fa4f035c0d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91266132"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Como: exigir aplicativos cliente aprovados para acesso ao aplicativo de nuvem com acesso condicional

As pessoas usam regularmente seus dispositivos móveis para tarefas pessoais e de trabalho. Enquanto garantem a produtividade de suas equipes, as organizações também desejam evitar a perda de dados por parte de aplicativos potencialmente não seguros. Com o acesso condicional, as organizações podem restringir o acesso a aplicativos cliente aprovados (com capacidade de autenticação moderna).

Este artigo apresenta dois cenários para configurar políticas de acesso condicional para recursos como Microsoft 365, Exchange Online e SharePoint Online.

- [Cenário 1: os aplicativos Microsoft 365 exigem um aplicativo cliente aprovado](#scenario-1-microsoft-365-apps-require-an-approved-client-app)
- [Cenário 2: o Exchange Online e o SharePoint Online exigem um aplicativo cliente aprovado](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

No acesso condicional, essa funcionalidade é conhecida como exigir um aplicativo cliente aprovado. Para obter uma lista de aplicativos cliente aprovada, veja [requisito de aplicativo cliente aprovado](concept-conditional-access-grant.md#require-approved-client-app).

> [!NOTE]
> Para exigir aplicativos cliente aprovados para dispositivos iOS e Android, esses dispositivos devem primeiro se registrar no Azure AD.

## <a name="scenario-1-microsoft-365-apps-require-an-approved-client-app"></a>Cenário 1: os aplicativos Microsoft 365 exigem um aplicativo cliente aprovado

Nesse cenário, a Contoso decidiu que os usuários que usam dispositivos móveis podem acessar todos os serviços de Microsoft 365 contanto que usem aplicativos cliente aprovados, como o Outlook Mobile, o OneDrive e o Microsoft Teams. Todos os seus usuários já entram com as credenciais do Azure AD e têm licenças atribuídas a eles que incluem o Azure AD Premium P1 ou P2 e o Microsoft Intune.

As organizações devem concluir as três etapas a seguir para exigir o uso de um aplicativo cliente aprovado em dispositivos móveis.

**Etapa 1: política para clientes de autenticação moderna com base em Android e iOS que exigem o uso de um aplicativo cliente aprovado ao acessar o Exchange Online.**

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições**, selecione **Usuários e grupos**.
   1. Em **Incluir**, selecione **Todos os usuários** ou os **usuários e grupos** específicos aos quais você deseja aplicar essa política. 
   1. Selecione **Concluído**.
1. Em **aplicativos de nuvem ou ações**  >  **incluem**, selecione **Office 365**.
1. Em **Condições**, selecione **Plataformas de dispositivo**.
   1. Defina **Configurar** como **Sim**.
   1. Inclua **Android** e **iOS**.
1. Em **Condições**, selecione **Aplicativos cliente (versão preliminar)** .
   1. Defina **Configurar** como **Sim**.
   1. Selecione **Aplicativos móveis e clientes da área de trabalho** e **Clientes de autenticação moderna**.
1. Em **Access controls**  >  **concessão**de controles de acesso, selecione **conceder acesso**, **exigir aplicativo cliente aprovado**e selecione **selecionar**.
1. Confirme suas configurações e defina **Habilitar política** como **Ativado**.
1. Selecione **Criar** para criar e habilitar sua política.

**Etapa 2: Configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (ActiveSync)**

1. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições**, selecione **Usuários e grupos**.
   1. Em **Incluir**, selecione **Todos os usuários** ou os **usuários e grupos** específicos aos quais você deseja aplicar essa política. 
   1. Selecione **Concluído**.
1. Em **Aplicativos de nuvem ou ações** > **Incluir**, selecione **Office 365 Exchange Online**.
1. Em **Condições**:
   1. **Aplicativos cliente (versão preliminar)** :
      1. Defina **Configurar** como **Sim**.
      1. Selecione **Aplicativos móveis e clientes da área de trabalho** e **Clientes do Exchange ActiveSync**.
1. Em **Access controls**  >  **concessão**de controles de acesso, selecione **conceder acesso**, **exigir aplicativo cliente aprovado**e selecione **selecionar**.
1. Confirme suas configurações e defina **Habilitar política** como **Ativado**.
1. Selecione **Criar** para criar e habilitar sua política.

**Etapa 3: Configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS.**

Confira o artigo [Como criar e atribuir políticas de proteção de aplicativo](/intune/apps/app-protection-policies) para obter as etapas necessárias para criar políticas de proteção de aplicativo para Android e iOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>Cenário 2: o Exchange Online e o SharePoint Online exigem um aplicativo cliente aprovado

Nesse cenário, a Contoso decidiu que os usuários só podem acessar dados de email e do SharePoint em dispositivos móveis, desde que usem um aplicativo cliente aprovado como o Outlook Mobile. Todos os seus usuários já entram com as credenciais do Azure AD e têm licenças atribuídas a eles que incluem o Azure AD Premium P1 ou P2 e o Microsoft Intune.

As organizações devem concluir as seguintes três etapas para exigir o uso de um aplicativo cliente aprovado em dispositivos móveis e em cliente do Exchange ActiveSync.

**Etapa 1: política para clientes de autenticação moderna com base em Android e iOS que exigem o uso de um aplicativo cliente aprovado ao acessar o Exchange Online e o SharePoint Online.**

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
1. Em **Condições**, selecione **Aplicativos cliente (versão preliminar)** .
   1. Defina **Configurar** como **Sim**.
   1. Selecione **Aplicativos móveis e clientes da área de trabalho** e **Clientes de autenticação moderna**.
1. Em **Access controls**  >  **concessão**de controles de acesso, selecione **conceder acesso**, **exigir aplicativo cliente aprovado**e selecione **selecionar**.
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
1. Em **Condições**:
   1. **Aplicativos cliente (versão preliminar)** :
      1. Defina **Configurar** como **Sim**.
      1. Selecione **Aplicativos móveis e clientes da área de trabalho** e **Clientes do Exchange ActiveSync**.
1. Em **Access controls**  >  **concessão**de controles de acesso, selecione **conceder acesso**, **exigir aplicativo cliente aprovado**e selecione **selecionar**.
1. Confirme suas configurações e defina **Habilitar política** como **Ativado**.
1. Selecione **Criar** para criar e habilitar sua política.

**Etapa 3: Configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS.**

Confira o artigo [Como criar e atribuir políticas de proteção de aplicativo](/intune/apps/app-protection-policies) para obter as etapas necessárias para criar políticas de proteção de aplicativo para Android e iOS. 

## <a name="next-steps"></a>Próximas etapas

[O que é acesso condicional?](overview.md)

[Componentes do acesso condicional](concept-conditional-access-policies.md)

[Políticas de acesso condicional comuns](concept-conditional-access-policy-common.md)
