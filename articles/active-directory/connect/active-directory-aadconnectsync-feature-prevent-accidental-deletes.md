---
title: "Sincronização do Azure AD Connect: impedir exclusões acidentais | Microsoft Docs"
description: "Este tópico descreve o recurso de prevenção contra exclusões acidentais (que impede exclusões acidentais) no Azure AD Connect."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: a33fb729cff5007e40820af696cfec823a3ecfde
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Sincronização do Azure AD Connect: impedir exclusões acidentais
Este tópico descreve o recurso de prevenção contra exclusões acidentais (que impede exclusões acidentais) no Azure AD Connect.

Ao instalar o Azure AD Connect, o recurso para impedir exclusões acidentais é habilitado por padrão e configurado para não permitir uma exportação com mais de 500 exclusões. Esse recurso destina-se a protegê-lo contra alterações acidentais de configuração e alterações no diretório local que possam afetar muitos usuários e outros objetos.

## <a name="what-is-prevent-accidental-deletes"></a>O que é impedir exclusões acidentais
Os cenários comuns quando você vê muitas exclusões incluem:

* Alterações de [filtragem](active-directory-aadconnectsync-configure-filtering.md) em que todo uma [UO](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) ou [domínio](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) é desmarcado.
* Todos os objetos em uma UO são excluídos.
* Uma UO é renomeada e todos os objetos são considerados fora do escopo de sincronização.

O valor padrão de 500 objetos pode ser alterado com o PowerShell, usando `Enable-ADSyncExportDeletionThreshold`. Você deve configurar esse valor para ajustar o tamanho da sua organização. Como o agendador de sincronização é executado a cada 30 minutos, o valor é o número de exclusões visto em 30 minutos.

Se houver muitas exclusões preparadas para serem exportadas para o Azure AD, a exportação será interrompida e você receberá um email como este:

![Impedir exclusões de email acidentais](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *Saudação (contato técnico). No momento, o Serviço de sincronização de identidade detectou que o número de exclusões excedeu o limite configurado de exclusões para (nome da organização). Um total de (número) objetos foram enviados para exclusão nesta execução da sincronização de Identidade. Isso atingiu ou excedeu o valor de limite de exclusão configurado de (número) objetos. Precisamos que você forneça a confirmação de que essas exclusões devem ser processadas antes de continuarmos. Veja Impedindo exclusões acidentais para obter mais informações sobre o erro listado nesta mensagem de email.*
>
> 

Você também pode ver o status `stopped-deletion-threshold-exceeded` examinando a interface do **Synchronization Service Manager** para o perfil de exportação.
![Impedir exclusões acidentais na interface do usuário do Synchronization Service Manager](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/syncservicemanager.png)

Caso isso não seja esperado, investigue e tome medidas corretivas. Para ver quais objetos estão prestes a ser excluídos, siga este procedimento:

1. Inicie o **Serviço de Sincronização** no Menu Iniciar.
2. Acesse **Conectores**.
3. Selecione o Conector com o tipo **Active Directory do Azure**.
4. Em **Ações**, à direita, selecione **Pesquisar Espaço do Conector**.
5. No pop-up em **Escopo**, selecione **Desconectado desde** e escolha um horário no passado. Clique em **Pesquisar**. Esta página fornece uma exibição de todos os objetos prestes a serem excluídos. Clicando em cada item, você poderá obter informações adicionais sobre o objeto. Você também pode clicar em **Configuração de Coluna** para adicionar outros atributos para exibição na grade.

![Pesquisar Espaço do Conector](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Se todas as exclusões forem desejadas, siga este procedimento:

1. Para recuperar o limite atual de exclusão, execute o cmdlet `Get-ADSyncExportDeletionThreshold` do PowerShell. Forneça uma conta e senha de Administrador Global do Azure AD. O valor padrão é 500.
2. Para desabilitar temporariamente a proteção e permitir que as exclusões ocorram, execute o cmdlet do PowerShell: `Disable-ADSyncExportDeletionThreshold`. Forneça uma conta e senha de Administrador Global do Azure AD.
   ![Credenciais](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
3. Com o Conector do Azure Active Directory ainda selecionado, selecione a ação **Executar** e **Exportar**.
4. Para reabilitar a proteção, execute o cmdlet do PowerShell: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`. Substitua 500 pelo valor que você observou ao recuperar o limite atual de exclusão. Forneça uma conta e senha de Administrador Global do Azure AD.

## <a name="next-steps"></a>Próximas etapas
**Tópicos de visão geral**

* [Sincronização do Azure AD Connect: compreender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)
