---
title: 'Azure Active Directory Domain Services: Política de senha | Microsoft Docs'
description: Entender as políticas de senha em domínios gerenciados
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: ecf38543b2c4e5187aa5c6593c3bccf6668b8a8a
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472757"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Políticas de senha e de bloqueio de conta em domínios gerenciados
Este artigo explica as políticas de senha padrão em domínios gerenciados. Ele também aborda como configurar essas políticas.

## <a name="fine-grained-password-policies-fgpp"></a>Políticas de senha refinadas (FGPP)
Use políticas de senha refinadas para especificar várias políticas de senha em um único domínio. A FGPP permite aplicar diferentes restrições de políticas de senha e de bloqueio de conta a diferentes conjuntos de usuários em um domínio. Por exemplo, é possível aplicar configurações de senha estritas para contas privilegiadas.

Você pode definir as seguintes configurações de senha usando FGPP:
* Tamanho mínimo da senha
* Histórico de senha
* As senhas devem atender a requisitos de complexidade
* Duração mínima da senha
* Duração máxima da senha
* Política de bloqueio de conta
    * Duração do bloqueio de conta
    * Número de tentativas de logon com falha permitido
    * Redefinir a contagem de tentativas de logon com falha após


## <a name="default-fine-grained-password-policy-settings-on-a-managed-domain"></a>Configurações da política de senha refinada padrão em domínios gerenciados
A captura de tela a seguir ilustra a política de senha refinada padrão configurada em um domínio gerenciado do Azure AD Domain Services.

![Política de senha refinada padrão](./media/how-to/default-fgpp.png)

> [!NOTE]
> Não é possível modificar ou excluir a política de senha refinada padrão interna. Os membros do grupo 'Administradores do AAD DC' podem criar uma FGPP personalizada e configurá-la para substituir (ou ter precedência sobre) a FGPP padrão interna.
>
>

## <a name="password-policy-settings"></a>Configurações da política de senha
Em um domínio gerenciado, as seguintes políticas de senha são configuradas por padrão:
* Tamanho mínimo da senha (caracteres): 7
* Duração máxima da senha (vida útil): 90 dias
* As senhas devem atender a requisitos de complexidade

### <a name="account-lockout-settings"></a>Configurações do bloqueio de conta
Em um domínio gerenciado, as seguintes políticas de bloqueio de conta são configuradas por padrão:
* Duração do bloqueio de conta: 30
* Número permitido de tentativas de logon com falha: 5
* Redefinir a contagem de tentativas de logon com falha após: 30 minutos

De fato, as contas de usuários são bloqueadas por 30 minutos quando 5 senhas inválidas são usadas em um período de 2 minutos. As contas são desbloqueadas automaticamente depois de 30 minutos.


## <a name="create-a-custom-fine-grained-password-policy-fgpp-on-a-managed-domain"></a>Criar uma política de senha refinada (FGPP) personalizada em domínios gerenciados
Você pode criar uma FGPP personalizada e aplicá-la a grupos específicos no seu domínio gerenciado. Essa configuração substitui efetivamente a FGPP padrão configurada para o domínio gerenciado.

> [!TIP]
> Somente os membros do grupo **'Administradores do AAD DC'** têm permissões para criar políticas de senha refinadas padrão.
>
>

Além disso, você também pode criar políticas de senha refinadas personalizadas e aplicá-las a quaisquer UOs personalizadas que criar no domínio gerenciado.

É possível configurar uma FGPP personalizada pelos seguintes motivos:
* Para definir uma política de bloqueio de conta diferente.
* Para definir uma configuração de vida útil da senha padrão para o domínio gerenciado.

Para criar uma FGPP personalizada no domínio gerenciado:
1. Entre na VM do Windows usada para administrar seu domínio gerenciado. Se você não tiver uma, siga as instruções para [gerenciar um domínio do Azure AD Domain Services](manage-domain.md).
2. Inicie o **Centro Administrativo do Active Directory** na VM.
3. Clique no nome de domínio (por exemplo, “contoso100.com”).
4. Clique duas vezes em **Sistema** para abrir o contêiner do sistema.
5. Clique duas vezes em **Contêiner de Configuração de Senha**.
6. Você verá a FGPP padrão interna para o domínio gerenciado chamado **AADDSSTFPSO**. Não é possível modificar essa FGPP interna. No entanto, você pode criar uma nova FGPP personalizada que substitua a FGPP padrão.
7. No painel **Tarefas** à direita, clique em **Novo** e em **Configurações de Senha**.
8. Na caixa de diálogo **Criar Configurações de Senha**, especifique as configurações de senha personalizada a serem aplicadas como parte da FGPP personalizada. Lembre-se de definir a prioridade adequadamente para substituir a FGPP padrão.

   ![Criar FGPP personalizada](./media/how-to/custom-fgpp.png)

   > [!TIP]
   > **Lembre-se de desmarcar a opção Proteger contra exclusão acidental.** Se essa opção estiver selecionada, a FGPP não poderá ser salva.
   >
   >

9. Em **Aplica-se Diretamente se a**, clique no botão **Adicionar**. Na caixa de diálogo **Selecionar Usuários ou Grupos**, clique no botão **Locais**.

   ![Selecionar Usuários e grupos](./media/how-to/fgpp-applies-to.png)

10. Na caixa de diálogo **Locais**, expanda o nome de domínio e clique em **Usuários do AADDC**. Agora você pode selecionar um grupo na UO de usuários internos aos quais aplicar a FGPP.

    ![Selecionar a UO à qual esse grupo pertence](./media/how-to/fgpp-container.png)

11. Digite o nome do grupo e clique no botão **Verificar Nomes** para validar a existência do grupo.

    ![Selecionar o grupo ao qual aplicar a FGPP](./media/how-to/fgpp-apply-group.png)

12. O nome do grupo é exibido na seção **Aplica-se Diretamente a**. Clique no botão **OK** para salvar essas alterações.

    ![FGPP aplicada](./media/how-to/fgpp-applied.png)

> [!TIP]
> **Para aplicar política de senha personalizada para contas de usuário em uma UO personalizada:** As políticas de senha refinadas podem ser aplicadas apenas a grupos. Para configurar uma política de senha personalizada somente para usuários de uma UO personalizada, crie um grupo que inclua usuários dessa UO.
>
>

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre políticas de senha refinadas do Active Directory](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Configurar políticas de senha refinadas usando o Centro Administrativo do AD](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)
