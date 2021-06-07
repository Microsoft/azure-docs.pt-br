---
title: Desinstalar o Azure AD Connect
description: Este documento descreve como desinstalar o Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5eb537a70c69745c8067ffb71cfb895a0875945
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96934154"
---
# <a name="uninstall-azure-ad-connect"></a>Desinstalar o Azure AD Connect

Este documento descreve como desinstalar corretamente o Azure AD Connect.

## <a name="uninstall-azure-ad-connect-from-the-server"></a>Desinstalar o Azure AD Connect do servidor
A primeira coisa que você precisa fazer é remover Azure AD Connect do servidor em que ele está sendo executado.  Use as seguintes etapas:

 1. No servidor que executa o Azure AD Connect, navegue até o **painel de controle**.
 2. Clique em **desinstalar um programa** 
  ![ desinstalar um programa](media/how-to-connect-uninstall/uninstall-1.png)</br>
 
 3. Selecione **Azure AD Connect**.
 ![Selecionar Azure AD Connect](media/how-to-connect-uninstall/uninstall-2.png)</br>
 
 4. Quando solicitado, clique em **Sim** para confirmar.
 5. Essa confirmação abrirá a tela de Azure AD Connect.  Clique em **Remover**.
 ![Remover](media/how-to-connect-uninstall/uninstall-3.png)</br>
 
 6. Quando essa ação for concluída, clique em **sair**.
 7. ![Fechar](media/how-to-connect-uninstall/uninstall-4.png)</br>
 
 8. Novamente no **painel de controle** , clique em **Atualizar** e todos os componentes devem ter sido removidos.


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
- [Instalar o Azure AD Connect usando um banco de dados ADSync existente](how-to-connect-install-existing-database.md)
- [Instalar o Azure AD Connect usando permissões de administrador do SQL delegado](how-to-connect-install-sql-delegation.md)

