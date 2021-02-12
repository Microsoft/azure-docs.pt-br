---
title: Configuração de novo agente de sincronização de nuvem Azure AD Connect
description: Este artigo descreve como instalar a sincronização de nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffdd27b67a122f82cc5fdb5568f11c85387955e8
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660789"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-sync"></a>Criar uma nova configuração para Azure AD Connect sincronização de nuvem

Depois de instalar o agente de provisionamento do Azure AD Connect, você precisará entrar no portal do Azure e configurá-lo. Siga estas etapas para habilitar o agente.

## <a name="configure-provisioning"></a>Configurar provisionamento
Para configurar o provisionamento, siga estas etapas.

 1. Na portal do Azure, selecione **Azure Active Directory**
 2. Selecione **Azure AD Connect**.
 3. Selecione **gerenciar sincronização de nuvem**.

 ![Gerenciar provisionamento](media/how-to-install/install-6.png)
 
 4. Selecione **nova configuração**.
 5. Na tela configuração, selecione seu domínio e se deseja habilitar a sincronização de hash de senha.  Clique em **criar**.  
 
 ![Criar nova configuração](media/how-to-configure/configure-1.png)


 6.  A tela Editar configuração de provisionamento será aberta.

   ![Editar configuração](media/how-to-configure/con-1.png)

 7. Insira um **email de notificação**. Este email será notificado quando o provisionamento não estiver íntegro.  É recomendável que você continue **impedindo a exclusão acidental** habilitada e defina o **limite de exclusão acidental** como um número sobre o qual você deseja ser notificado.  Para obter mais informações, consulte [exclusões acidentais](#accidental-deletions) abaixo.
 8. Mova o seletor para habilitar e selecione salvar.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Provisionamento de escopo para usuários e grupos específicos
Você pode fazer o escopo do agente para sincronizar usuários e grupos específicos usando grupos de Active Directory locais ou unidades organizacionais. Você não pode configurar grupos e unidades organizacionais em uma configuração. 

 1.  No portal do Azure, selecione **Azure Active Directory**.
 2. Selecione **Azure AD Connect**.
 3. Selecione **gerenciar sincronização de nuvem**.
 4. Em **configuração**, selecione sua configuração.

 ![Seção de configuração](media/how-to-configure/scope-1.png)
 
 5. Em **Configurar**, selecione **Editar filtros de escopo** para alterar o escopo da regra de configuração.
 ![Editar escopo](media/how-to-configure/scope-3.png)
 7. À direita, você pode alterar o escopo.  Clique em **concluído**  e em **salvar** quando terminar.
 8. Depois de alterar o escopo, você deve [reiniciar o provisionamento](#restart-provisioning) para iniciar uma sincronização imediata das alterações.

## <a name="attribute-mapping"></a>Mapeamento de atributos
Azure AD Connect a sincronização de nuvem permite que você mapeie facilmente atributos entre seus objetos de usuário/grupo local e os objetos no Azure AD.  Você pode personalizar mapeamentos de atributo padrão de acordo com suas necessidades comerciais. Sendo assim, você pode alterar ou excluir mapeamentos de atributos existentes ou criar mapeamentos.  Para obter mais informações, consulte [mapeamento de atributo](how-to-attribute-mapping.md).

## <a name="on-demand-provisioning"></a>Provisionamento sob demanda
Azure AD Connect a sincronização de nuvem permite que você teste as alterações de configuração, aplicando essas alterações a um único usuário ou grupo.  Você pode usar isso para validar e verificar se as alterações feitas na configuração foram aplicadas corretamente e estão sendo sincronizadas corretamente com o Azure AD.  Para obter mais informações, consulte [provisionamento sob demanda](how-to-on-demand-provision.md).

## <a name="accidental-deletions"></a>Exclusões acidentais
O recurso de exclusão acidental foi projetado para protegê-lo contra alterações de configuração acidentais e alterações em seu diretório local que afetariam muitos usuários e grupos.  Esse recurso permite que você:

- Configure a capacidade de impedir exclusões acidentais automaticamente. 
- Definir o número de objetos (limite) Além do qual a configuração entrará em vigor 
- Configure um endereço de email de notificação para que eles possam receber uma notificação por email depois que o trabalho de sincronização em questão for colocado em quarentena para esse cenário 

Para obter mais informações, consulte [exclusões acidentais](how-to-accidental-deletes.md)

## <a name="quarantines"></a>Coloca
A sincronização de nuvem monitora a integridade de sua configuração e coloca objetos não íntegros em um estado de quarentena. Se a maioria ou todas as chamadas feitas no sistema de destino falharem consistentemente devido a um erro, por exemplo, credenciais de administrador inválidas, o trabalho de sincronização será marcado como em quarentena.  Para obter mais informações, consulte a seção de solução de problemas em [quarentenas](how-to-troubleshoot.md#provisioning-quarantined-problems).

## <a name="restart-provisioning"></a>Reiniciar o provisionamento 
Se você não quiser aguardar a próxima execução agendada, acione o provisionamento executado usando o botão **reiniciar provisionamento** . 
 1.  No portal do Azure, selecione **Azure Active Directory**.
 2. Selecione **Azure AD Connect**.
 3.  Selecione **gerenciar sincronização de nuvem**.
 4. Em **configuração**, selecione sua configuração.

   ![Seleção de configuração para reiniciar o provisionamento](media/how-to-configure/scope-1.png)

 5. Na parte superior, selecione **reiniciar provisionamento**.

## <a name="remove-a-configuration"></a>Remover uma configuração
Para excluir uma configuração, siga estas etapas.

 1.  No portal do Azure, selecione **Azure Active Directory**.
 2. Selecione **Azure AD Connect**.
 3. Selecione **gerenciar sincronização de nuvem**.
 4. Em **configuração**, selecione sua configuração.
   
   ![Seleção de configuração para remover a configuração](media/how-to-configure/scope-1.png)

 5. Na parte superior da tela de configuração, selecione **excluir**.

>[!IMPORTANT]
>Não há nenhuma confirmação antes de excluir uma configuração. Certifique-se de que essa é a ação que você deseja executar antes de selecionar **excluir**.


## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem do Azure AD Connect?](what-is-cloud-sync.md)
