---
title: "Sincronização do Azure AD Connect: alterando a conta do serviço de sincronização do Azure AD Connect | Microsoft Docs"
description: "Este documento tópico descreve a chave de criptografia e como abandoná-la depois que a senha tiver sido alterada."
services: active-directory
keywords: "Conta do serviço de sincronização do Azure AD, senha"
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
ms.openlocfilehash: bf6234d0810f870909957ee1c1e33c225a4922b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="changing-the-azure-ad-connect-sync-service-account-password"></a>Alteração da senha da conta do serviço de sincronização do Azure AD Connect
Se você alterar a senha de conta do serviço de sincronização do Azure AD Connect, o serviço de sincronização não será capaz de iniciar corretamente até você ter abandonado a chave de criptografia e reinicializado a senha da conta do serviço de sincronização do Azure AD Connect. 

O Azure AD Connect, como parte dos serviços de sincronização, usa uma chave de criptografia para armazenar as senhas das contas de serviço do AD DS e do Azure AD.  Essas contas são criptografadas antes de serem armazenadas no banco de dados. 

A chave de criptografia usada é protegida usando o [Data Protection do Windows (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx). O DPAPI protege a chave de criptografia usando a **senha da conta do serviço de sincronização do Azure AD Connect**. 

Se você precisar alterar a senha da conta do serviço, use os procedimentos em [Abandono da chave de criptografia de sincronização do Azure AD Connect](#abandoning-the-azure-ad-connect-sync-encryption-key) para fazer isso.  Estes procedimentos também deverão ser usados se você precisar abandonar a chave de criptografia por algum motivo.

##<a name="issues-that-arise-from-changing-the-password"></a>Os problemas que surgem da alteração da senha
Há duas coisas que precisam ser feitas quando você altera a senha da conta do serviço.

Primeiro, você precisa alterar a senha no Gerenciador de Controle de Serviços do Windows.  Até que esse problema seja resolvido, você verá os seguintes erros:


- Se você tentar iniciar o serviço de sincronização no Gerenciador de Controle de Serviços do Windows, receberá o erro "**O Windows não pôde iniciar o serviço de sincronização do Microsoft Azure AD no computador local**". **Erro 1069: O serviço não foi iniciado devido a uma falha de logon.**"
- No Visualizador de Eventos do Windows, o log de eventos do sistema contém um erro com **ID de evento 7038** e a mensagem "**O serviço ADSync não pôde fazer logon com a senha configurada atualmente devido ao seguinte erro: nome de usuário ou senha incorretos.**"

Segundo, sob condições específicas, se a senha for atualizada, o serviço de sincronização não poderá mais recuperar a chave de criptografia pelo DPAPI. Sem a chave de criptografia, o serviço de sincronização não poderá descriptografar as senhas necessárias para sincronizar de/para o AD local e o Azure AD.
Você verá erros, como:

- No Gerenciador de Controle de Serviços do Windows, se você tentar iniciar o serviço de sincronização e ele não conseguir recuperar a chave de criptografia, receberá o erro “**O Windows não pôde iniciar a sincronização do Microsoft Azure AD no computador local. Para saber mais, examine o log de eventos do sistema. Se for um serviço de terceiros, não Microsoft, entre em contato com o fornecedor do serviço e mencione o código de erro específico do serviço **-21451857952****”.
- No Visualizador de Eventos do Windows, o log de eventos do aplicativo contém um erro com **ID de evento 6028** e a mensagem de erro *“**A chave de criptografia do servidor não pode ser acessada* *”.*

Para garantir que você não receba esses erros, siga os procedimentos em [Abandonando a chave de criptografia de sincronização do Azure AD Connect](#abandoning-the-azure-ad-connect-sync-encryption-key) ao alterar a senha.
 
## <a name="abandoning-the-azure-ad-connect-sync-encryption-key"></a>Abandono da chave de criptografia de sincronização do Azure AD Connect
>[!IMPORTANT]
>Os procedimentos a seguir aplicam-se somente ao Azure AD Connect compilação 1.1.443.0 ou anterior.

Use os procedimentos a seguir para abandonar a chave de criptografia.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>O que fazer se você precisar abandonar a chave de criptografia

Se você precisar abandonar a chave de criptografia, use os procedimentos a seguir para fazer isso.

1. [Abandone a chave de criptografia existente](#abandon-the-existing-encryption-key)

2. [Forneça a senha da conta do AD DS](#provide-the-password-of-the-ad-ds-account)

3. [Reinicialize a senha da conta de sincronização do Azure AD](#reinitialize-the-password-of-the-azure-ad-sync-account)

4. [Inicie o serviço de sincronização](#start-the-synchronization-service)

#### <a name="abandon-the-existing-encryption-key"></a>Abandone a chave de criptografia existente
Abandone a chave de criptografia existente para que essa nova chave de criptografia possa ser criada:

1. Faça logon no seu servidor do Azure AD Connect como administrador.

2. Inicie uma nova sessão do PowerShell.

3. Navegue até a pasta: `$env:Program Files\Microsoft Azure AD Sync\bin\`

4. Execute o comando: `./miiskmu.exe /a`

![Utilitário de chave de criptografia de sincronização do Azure AD Connect](media/active-directory-aadconnectsync-encryption-key/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-account"></a>Forneça a senha da conta do AD DS
Como as senhas existentes armazenadas no banco de dados não podem ser descriptografadas, você precisará fornecer ao serviço de sincronização a senha da conta do AD DS. O serviço de sincronização criptografa as senhas usando a nova chave de criptografia:

1. Inicie o Synchronization Service Manager (INICIAR → Serviço de Sincronização).
</br>![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)  
2. Acesse a guia **Conectores**.
3. Selecione o **AD Connector** que corresponde ao seu AD local. Se você tiver mais de um AD Connector, repita as etapas a seguir para cada um deles.
4. Em **Ações**, selecione **Propriedades**.
5. Na caixa de diálogo pop-up, selecione **Conectar-se à floresta do Active Directory**:
6. Insira a senha da conta do AD DS na caixa de texto **Senha**. Se você não souber a senha, configure-a para um valor conhecido antes de executar essa etapa.
7. Clique em **OK** para salvar a nova senha e fechar a caixa de diálogo pop-up.
![Utilitário de chave de criptografia de sincronização do Azure AD Connect](media/active-directory-aadconnectsync-encryption-key/key6.png)

#### <a name="reinitialize-the-password-of-the-azure-ad-sync-account"></a>Reinicialize a senha da conta de sincronização do Azure AD
Você não pode fornecer diretamente a senha da conta de serviço do Azure AD para o serviço de sincronização. Em vez disso, você precisa usar o cmdlet **Add-ADSyncAADServiceAccount** para reinicializar a conta de serviço do Azure AD. O cmdlet redefine a senha da conta e a torna disponível para o serviço de sincronização:

1. Inicie uma nova sessão do PowerShell no servidor do Azure AD Connect.
2. Execute o cmdlet `Add-ADSyncAADServiceAccount`.
3. Na caixa de diálogo pop-up, forneça as credenciais de administrador global do Azure AD para seu locatário do Azure AD.
![Utilitário de chave de criptografia de sincronização do Azure AD Connect](media/active-directory-aadconnectsync-encryption-key/key7.png)
4. Se isso for bem-sucedido, você verá o prompt de comando do PowerShell.

#### <a name="start-the-synchronization-service"></a>Inicie o serviço de sincronização
Agora que o serviço de sincronização tem acesso à chave de criptografia e todas as senhas necessárias, você poderá reiniciar o serviço no Gerenciador de Controle de Serviços do Windows:


1. Vá para o Gerenciador de Controle de Serviços do Windows (INICIAR → Serviços).
2. Selecione **Sincronização do Microsoft Azure AD** e clique em Reiniciar.

## <a name="next-steps"></a>Próximas etapas
**Tópicos de visão geral**

* [Sincronização do Azure AD Connect: compreender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)

* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)
