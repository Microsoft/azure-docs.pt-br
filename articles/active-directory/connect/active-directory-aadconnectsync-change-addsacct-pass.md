---
title: "Sincronização do Azure AD Connect: alterando a senha da conta do AD DS | Microsoft Docs"
description: "Este documento de tópico descreve como atualizar o Azure AD Connect depois que a senha da conta do AD DS é alterada."
services: active-directory
keywords: Conta do AD DS, conta do Active Directory, senha
documentationcenter: 
author: cychua
manager: femila
editor: 
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 14e16a238e60ecfeeb3cbf88c3922a79349dcc75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="changing-the-ad-ds-account-password"></a>Alterando a senha da conta do AD DS
A conta do AD DS refere-se à conta de usuário usada pelo Azure AD Connect para se comunicar com o Active Directory local. Se você alterar a senha da conta do AD DS, será necessário atualizar o Azure AD Connect Synchronization Service com a nova senha. Caso contrário, o serviço não poderá mais sincronizar corretamente com o Active Directory local e você encontrará os seguintes erros:

* No Synchronization Service Manager, qualquer operação de importação ou de exportação com o AD local falha com o erro **no-start-credentials**.

* No Visualizador de Eventos do Windows, o log de eventos do aplicativo contém um erro com **ID do evento 6000** e com a mensagem **'O agente de gerenciamento "contoso.com" falhou, porque as credenciais eram inválidas'**.


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>Como atualizar o Synchronization Service com a nova senha de conta do AD DS
Para atualizar o Synchronization Service com a nova senha:

1. Inicie o Synchronization Service Manager (INICIAR → Serviço de Sincronização).
</br>![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)  

2. Vá para a guia **Conectores**.

3. Selecione o **Conector AD** que corresponde à conta do AD DS para a qual a sua senha foi alterada.

4. Em **Ações**, selecione **Propriedades**.

5. Na caixa de diálogo pop-up, selecione **Conectar-se à Floresta do Active Directory**:

6. Insira a nova senha da conta do AD DS na caixa de texto **Senha**.

7. Clique em **OK** para salvar a nova senha e fechar a caixa de diálogo pop-up.

8. Reinicie o Azure AD Connect Synchronization Service no Gerenciador de Controle de Serviço Windows. Isso é para garantir que qualquer referência à senha antiga é removida do cache de memória.

## <a name="next-steps"></a>Próximas etapas
**Tópicos de visão geral**

* [Sincronização do Azure AD Connect: compreender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)

* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)
