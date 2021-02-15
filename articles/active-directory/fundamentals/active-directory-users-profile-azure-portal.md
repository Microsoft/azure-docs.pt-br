---
title: Adicionar ou atualizar informações de perfil do usuário-Azure AD
description: Instruções sobre como adicionar informações a um perfil de usuário no Azure Active Directory, incluindo detalhes de uma imagem e o trabalho.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 04/11/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27896bac6f5436fb6717e4bc1ab0a1f9381904ad
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370909"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Como adicionar ou atualizar as informações do perfil de um usuário usando o Azure Active Directory
Adicione informações de perfil de usuário, incluindo uma imagem do perfil, informações específicas do trabalho e algumas configurações usando o Azure AD (Azure Active Directory). Para obter mais informações sobre como adicionar novos usuários, consulte [Como adicionar ou excluir usuários no Azure Active Directory](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Adicionar ou alterar informações do perfil
Como você verá, há mais informações disponíveis no perfil de um usuário do que você pode adicionar durante a criação do usuário. Todas essas informações adicionais são opcionais e podem ser adicionadas conforme a necessidade da sua organização.

## <a name="to-add-or-change-profile-information"></a>Para adicionar ou as alterar informações do perfil
1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador de usuário para a organização.

2. Selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, selecione um usuário. Por exemplo, _Alain Charon_.

    A página **Alain Charon - Perfil ** é exibida.

    ![Página do perfil do usuário, incluindo informações editáveis](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Selecione **Editar** para adicionar ou atualizar as informações incluídas em cada uma das seções disponíveis.

    ![Página de perfil do usuário, mostrando as áreas editáveis](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Imagem do perfil.** Selecione uma imagem em miniatura para a conta do usuário. Esta imagem aparece no Azure Active Directory e nas páginas pessoais do usuário, como a página myapps.microsoft.com.

    - **Identidade.** Adicione ou atualize um valor de identidade adicional para o usuário, como um sobrenome casado. Você pode definir esse nome independentemente dos valores de First Name e Last Name. Por exemplo, você pode usá-lo para incluir iniciais, um nome de empresa ou alterar a sequência de nomes mostrados. Em outro exemplo, para dois usuários cujos nomes são "Chris Green", você pode usar a cadeia de caracteres de identidade para definir seus nomes como "Chris B. Green" "Chris R. Green (contoso)."

    - **Informações do trabalho.** Adicione qualquer informação relacionada ao trabalho como cargo, departamento ou gerente do usuário.

    - **Configurações.** Decida se o usuário pode entrar no locatário do Azure Active Directory. Você também pode especificar a localização global do usuário.

    - **Informações de contato.** Adicione quaisquer informações de contato relevantes para o usuário, exceto para algumas informações de contato móvel ou telefone do usuário (somente um administrador global pode atualizar para usuários em funções de administrador).

    - **Informações de contato de autenticação.** Verifique essas informações para certificar-se de que há um número de telefone e um endereço de email ativos para o usuário. Essas informações são usadas pelo Azure Active Directory para garantir que o usuário seja realmente o usuário durante a entrada. As informações de contato de autenticação somente podem ser atualizadas por um administrador global.

4. Clique em **Salvar**.

    Todas as alterações são salvas para o usuário.

    >[!Note]
    >Você deve usar o Windows Server Active Directory para atualizar a identidade, as informações de contato ou as informações do trabalho para os usuários cuja fonte de autoridade é o Windows Server Active Directory. Depois de concluir sua atualização, você deve aguardar a conclusão do próximo ciclo de sincronização antes de ver as alterações.

## <a name="next-steps"></a>Próximas etapas
Após atualizar os perfis dos usuários, você poderá executar os seguintes processos básicos:

- [Adicionar ou excluir usuários](add-users-azure-active-directory.md)

- [Atribuir funções aos usuários](active-directory-users-assign-role-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

Ou você pode executar outras tarefas de gerenciamento de usuários como atribuir delegados, usar políticas e compartilhar contas de usuários. Para obter mais informações sobre outras ações disponíveis, consulte [documentação de gerenciamento de usuário do Azure Active Directory](../enterprise-users/index.yml).
