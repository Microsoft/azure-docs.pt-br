---
title: Migrar políticas de acesso condicional com autenticação multifator-Azure Active Directory
description: Este artigo mostra como migrar uma política clássica que exige autenticação multifator no portal do Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cc99241f5fa7c09c69db13dcb3f6c3aaa75e376
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87027386"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>Migrar uma política clássica no portal do Azure

Este artigo mostra como migrar uma política clássica que exige **autenticação multifator** para um aplicativo de nuvem. Embora não seja um pré-requisito, recomendamos que você leia [Migrar políticas clássicas no portal do Azure](policy-migration.md) antes de iniciar a migração de suas políticas clássicas.

![Detalhes da política clássica que exigem MFA para o aplicativo Salesforce](./media/policy-migration/33.png)

O processo de migração consiste nas seguintes etapas:

1. [Abra a política clássica](#open-a-classic-policy) para obter as definições de configuração.
1. Crie uma nova política de Acesso Condicional do Azure AD para substituir a política clássica. 
1. Desabilite a política clássica.

## <a name="open-a-classic-policy"></a>Abrir uma política clássica

1. Na [portal do Azure](https://portal.azure.com), navegue até **Azure Active Directory**  >    >  **acesso condicional** de segurança.
1. Selecione **as políticas clássicas**.

   ![Exibição de políticas clássicas](./media/policy-migration-mfa/12.png)

1. Na lista de políticas clássicas, selecione a política que você deseja migrar. Documente as definições de configuração para que você possa recriar com uma nova política de acesso condicional.

## <a name="create-a-new-conditional-access-policy"></a>Criar uma nova política de Acesso Condicional

1. Na [portal do Azure](https://portal.azure.com), navegue até **Azure Active Directory**  >    >  **acesso condicional** de segurança.
1. Para criar uma nova política de acesso condicional, selecione **nova política**.
1. Na página **Novo**, na caixa de texto **Nome**, digite um nome para a política.
1. Na seção **Atribuições**, clique em **Usuários e grupos**.
   1. Se você tiver todos os usuários selecionados em sua política clássica, clique em **Todos os usuários**. 
   1. Se você tiver todos os grupos selecionados em sua política clássica, clique em **Selecionar usuários e grupos** e, em seguida, selecione os usuários e grupos desejados.
   1. Se você tiver os grupos excluídos, clique na guia **Excluir** e, em seguida, selecione os usuários e grupos necessários. 
   1. Selecione **Concluído**
1. Na seção **atribuição** , clique em **aplicativos de nuvem ou ações**.
1. Na página **aplicativos de nuvem ou ações** , execute as seguintes etapas:
   1. Clique em **Selecionar aplicativos**.
   1. Clique em **Selecionar**.
   1. Na página **Selecionar**, selecione seu aplicativo de nuvem e, em seguida, clique em **Selecionar**.
   1. Na página **Aplicativos de nuvem**, clique em **Concluído**.
1. Se você tiver selecionado **Exigir autenticação multifator**:
   1. Na seção **Controles de acesso**, clique em **Conceder**.
   1. Na página **Conceder**, clique em **Conceder acesso** e, em seguida, clique em **Exigir autenticação multifator**.
   1. Clique em **Selecionar**.
1. Clique **em** ativar para habilitar a política e selecione **salvar**.

   ![Criação de política de acesso condicional](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>Desabilite a política clássica

Para desabilitar a sua política clássica, clique em **Desabilitar** no modo de exibição **Detalhes**.

![Desabilitar políticas clássicas](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre a migração de política clássica, consulte [Migrar políticas clássicas no portal do Azure](policy-migration.md).
- [Use o modo somente de relatório para acesso condicional para determinar o impacto das novas decisões de política.](concept-conditional-access-report-only.md)