---
title: O que é provisionamento de identidade com o Azure AD? | Microsoft Docs
description: Descreve a visão geral do provisionamento de identidade.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1c85f2a6d58a5dbeae93b951cea812d6aa91326
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98614433"
---
# <a name="what-is-identity-provisioning"></a>O que é provisionamento de identidade?

Hoje, as empresas e corporações estão se tornando cada vez mais uma mistura de aplicativos locais e na nuvem.  Os usuários precisam ter acesso local e na nuvem a aos aplicativos. É necessário ter uma única identidade entre esses vários aplicativos (no local, bem como na nuvem).

O provisionamento é o processo de criação de um objeto com base em determinadas condições, mantendo o objeto atualizado e excluindo o objeto quando as condições não são mais atendidas. Por exemplo, quando um novo usuário ingressa em sua organização, esse usuário é inserido no sistema de RH.  Nesse ponto, o provisionamento pode criar uma conta de usuário correspondente na nuvem, no Active Directory e em diferentes aplicativos que o usuário precisa acessar.  Isso permite que o usuário inicie o trabalho e tenha acesso aos aplicativos e sistemas de que precisa, já no primeiro dia. 

![Diagrama que mostra o provisionamento de nuvem com o Azure Active Directory.](media/what-is-provisioning/cloud-1.png)

Com relação ao Azure Active Directory, o provisionamento pode ser dividido nos cenários principais a seguir.  

- **[Provisionamento impulsionado por RH](#hr-driven-provisioning)**  
- **[Provisionamento de aplicativos](#app-provisioning)**  
- **[Provisionamento de diretório](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>Provisionamento impulsionado por RH

![Diagrama que mostra o provisionamento controlado por RH com o Cloud HR, o On-premises HR e o Azure Active Directory.](media/what-is-provisioning/cloud-2.png)

O provisionamento de RH para a nuvem envolve a criação de objetos (usuários, funções, grupos, etc.) com base nas informações que estão em seu sistema de RH.  

O cenário mais comum seria quando um novo funcionário ingressasse na sua empresa e fosse inserido no sistema de RH.  Quando isso ocorre, ele é provisionado na nuvem.  Nesse caso, o Azure AD.  O provisionamento do RH pode abranger os cenários a seguir. 

- **Contratação de novos funcionários** – quando um novo funcionário é adicionado ao Cloud HR, uma conta de usuário é criada automaticamente no Active Directory, no Azure Active Directory e, opcionalmente, no Microsoft 365 e em outros aplicativos SaaS compatíveis com o Azure AD, com write-back do endereço de email para o Cloud HR.
- **Atualizações de perfil e atributo de funcionário** – quando um registro de funcionário é atualizado no Cloud HR (como seu nome, cargo ou gerente), sua conta de usuário é atualizada automaticamente no Active Directory, no Azure Active Directory e, opcionalmente, no Microsoft 365 e em outros aplicativos SaaS compatíveis com o Azure AD.
- **Rescisão de funcionário** – quando um funcionário é rescindido no Cloud HR, sua conta de usuário será desabilitada automaticamente no Active Directory, no Azure Active Directory e, opcionalmente, no Office 365 e outros aplicativos SaaS compatíveis com o Azure AD.
- **Recontratação de funcionário** – quando um funcionário é recontratado no Cloud HR, a conta antiga do funcionário pode ser reativada ou reprovisionada automaticamente (dependendo da sua preferência) no Active Directory, no Azure Active Directory e, opcionalmente, no Microsoft 365 e em outros aplicativos SaaS compatíveis com o Azure AD.


## <a name="app-provisioning"></a>Provisionamento de aplicativos

![Diagrama que mostra o provisionamento de aplicativos com aplicativos locais, aplicativos de nuvem que não são da Microsoft e o Azure Active Directory.](media/what-is-provisioning/cloud-3.png)

No Azure AD (Azure Active Directory), o termo **[provisionamento de aplicativo](../app-provisioning/user-provisioning.md)** refere-se à criação automática de identidades e funções de usuário nos aplicativos de nuvem aos quais os usuários precisam ter acesso. Além de criar identidades de usuário, o provisionamento automático inclui a manutenção e a remoção de identidades de usuário à medida que o status ou as funções mudam. Os cenários comuns incluem o provisionamento de um usuário do Azure AD em aplicativos como [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md), entre outros.

## <a name="directory-provisioning"></a>Provisionamento de diretório

![provisionamento em nuvem](media/what-is-provisioning/cloud-4.png)

O provisionamento local envolve o provisionamento de fontes locais (como Active Directory) para o Azure AD.  

O cenário mais comum seria quando um usuário do AD (Active Directory) fosse provisionado no Azure AD.

Isso foi realizado pela Sincronização do Azure AD Connect, pelo provisionamento do Azure AD Connect e pelo Microsoft Identity Manager. 
 
## <a name="next-steps"></a>Próximas etapas 

- [O que é a sincronização em nuvem do Azure AD Connect?](what-is-cloud-sync.md)
- [Instalar o provisionamento de nuvem](how-to-install.md)