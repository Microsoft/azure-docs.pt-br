---
title: Sincronizar atributos com o Azure AD para mapeamento
description: Saiba como sincronizar atributos do seu Active Directory local com o Azure AD. Ao configurar o provisionamento de usuário para aplicativos SaaS, use o recurso de extensão de diretório para adicionar atributos de origem que não são sincronizados por padrão.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/13/2019
ms.author: kenwith
ms.openlocfilehash: 62d035b85850f8ac455a85fd93e4d081bbd386e1
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256078"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Sincronizar um atributo do seu Active Directory local com o Azure AD para provisionamento para um aplicativo

Ao personalizar mapeamentos de atributo para provisionamento de usuário, você pode descobrir que o atributo que você deseja mapear não aparece na lista de **atributos de origem** . Este artigo mostra como adicionar o atributo ausente sincronizando-o do seu Active Directory local (AD) para Azure Active Directory (AD do Azure).

O Azure AD deve conter todos os dados necessários para criar um perfil de usuário ao provisionar contas de usuário do Azure AD para um aplicativo SaaS. Em alguns casos, para disponibilizar os dados, talvez seja necessário sincronizar os atributos do seu AD local com o Azure AD. Azure AD Connect sincroniza automaticamente determinados atributos com o Azure AD, mas não todos os atributos. Além disso, alguns atributos (como SAMAccountName) que são sincronizados por padrão podem não ser expostos usando a API Microsoft Graph. Nesses casos, você pode usar o recurso de extensão de diretório Azure AD Connect para sincronizar o atributo com o Azure AD. Dessa forma, o atributo ficará visível para a API de Microsoft Graph e o serviço de provisionamento do Azure AD.

Se os dados necessários para o provisionamento estiverem em Active Directory, mas não estiverem disponíveis para provisionamento devido aos motivos descritos acima, siga estas etapas.
 
## <a name="sync-an-attribute"></a>Sincronizar um atributo 

1. Abra o assistente de Azure AD Connect, escolha tarefas e, em seguida, escolha **Personalizar opções de sincronização**.

   ![Página tarefas adicionais do assistente de Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Entre como um administrador global do Azure AD. 

3. Na página **recursos opcionais** , selecione a **extensão de diretório sincronização de atributos**.
 
   ![Página de recursos opcionais do assistente de Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Selecione os atributos que você deseja estender para o Azure AD.
   > [!NOTE]
   > A pesquisa em **atributos disponíveis** diferencia maiúsculas de minúsculas.

   ![Captura de tela que mostra a página de seleção "extensões de diretório"](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Conclua o assistente de Azure AD Connect e permita a execução de um ciclo de sincronização completo. Quando o ciclo for concluído, o esquema será estendido e os novos valores serão sincronizados entre o AD local e o Azure AD.
 
6. No portal do Azure, enquanto você estiver [editando mapeamentos de atributo de usuário](customize-application-attributes.md), a lista de **atributos de origem** agora conterá o atributo adicionado no formato `<attributename> (extension_<appID>_<attributename>)` . Selecione o atributo e mapeie-o para o aplicativo de destino para provisionamento.

   ![Página de seleção de extensões de diretório do assistente de Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Atualmente, não há suporte para a capacidade de provisionar atributos de referência do AD local, como **ManagedBy** ou **DN/distinguishedName**. Você pode solicitar esse recurso no [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Próximas etapas

* [Definir quem está no escopo para provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
