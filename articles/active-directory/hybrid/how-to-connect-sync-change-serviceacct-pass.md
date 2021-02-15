---
title: 'Sincronização de Azure AD Connect: alterando a conta de serviço ADSync | Microsoft Docs'
description: Este documento tópico descreve a chave de criptografia e como abandoná-la depois que a senha tiver sido alterada.
services: active-directory
keywords: Conta do serviço de sincronização do Azure AD, senha
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4dcc7ed6076c3bac723d709f50f1b3ab2ce8f58
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996552"
---
# <a name="changing-the-adsync-service-account-password"></a>Alterando a senha da conta de serviço do ADSync
Se você alterar a senha da conta de serviço do ADSync, o serviço de sincronização não será capaz de iniciar corretamente até que você tenha abandonado a chave de criptografia e reinicializado a senha da conta de serviço do ADSync. 

Azure AD Connect, como parte dos serviços de sincronização, o usa uma chave de criptografia para armazenar as senhas da conta do conector de AD DS e da conta de serviço ADSync.  Essas contas são criptografadas antes de serem armazenadas no banco de dados. 

A chave de criptografia usada é protegida usando o [Data Protection do Windows (DPAPI)](/previous-versions/ms995355(v=msdn.10)). A DPAPI protege a chave de criptografia usando a **conta de serviço AdSync**. 

Se você precisar alterar a senha da conta de serviço, poderá usar os procedimentos em [abandonar a chave de criptografia da conta de serviço do AdSync](#abandoning-the-adsync-service-account-encryption-key) para fazer isso.  Estes procedimentos também deverão ser usados se você precisar abandonar a chave de criptografia por algum motivo.

## <a name="issues-that-arise-from-changing-the-password"></a>Os problemas que surgem da alteração da senha
Há duas coisas que precisam ser feitas quando você altera a senha da conta do serviço.

Primeiro, você precisa alterar a senha no Gerenciador de Controle de Serviços do Windows.  Até que esse problema seja resolvido, você verá os seguintes erros:


- Se você tentar iniciar o serviço de sincronização no Gerenciador de Controle de Serviços do Windows, receberá o erro "**O Windows não pôde iniciar o serviço de sincronização do Microsoft Azure AD no computador local**". **Erro 1069: O serviço não foi iniciado devido a uma falha de logon.**"
- No Visualizador de Eventos do Windows, o log de eventos do sistema contém um erro com **ID de evento 7038** e a mensagem "**O serviço ADSync não pôde fazer logon com a senha configurada atualmente devido ao seguinte erro: nome de usuário ou senha incorretos.**"

Segundo, sob condições específicas, se a senha for atualizada, o serviço de sincronização não poderá mais recuperar a chave de criptografia pelo DPAPI. Sem a chave de criptografia, o serviço de sincronização não poderá descriptografar as senhas necessárias para sincronizar de/para o AD local e o Azure AD.
Você verá erros, como:

- Em Gerenciador de controle de serviço do Windows, se você tentar iniciar o serviço de sincronização e ele não puder recuperar a chave de criptografia, ele falhará com o erro "<strong>o Windows não pôde iniciar a sincronização de Microsoft Azure AD no computador local. Para obter mais informações, examine o log de eventos do sistema. Se esse for um serviço que não seja da Microsoft, entre em contato com o fornecedor do serviço e consulte o código de erro específico do serviço-21451857952</strong>. "
- No Windows Visualizador de Eventos, o log de eventos do aplicativo contém um erro com a **ID de evento 6028** e *a mensagem de erro "a chave de criptografia do servidor não pode ser acessada".*

Para garantir que você não receba esses erros, siga os procedimentos em [abandonar a chave de criptografia da conta de serviço do AdSync](#abandoning-the-adsync-service-account-encryption-key) ao alterar a senha.
 
## <a name="abandoning-the-adsync-service-account-encryption-key"></a>Abandonando a chave de criptografia da conta de serviço do ADSync
>[!IMPORTANT]
>Os procedimentos a seguir aplicam-se somente ao Azure AD Connect compilação 1.1.443.0 ou anterior. Isso não pode ser usado para versões mais recentes do Azure AD Connect porque abandonar a chave de criptografia é manipulado pelo Azure AD Connect propriamente dito quando você altera a senha da conta de serviço do AD Sync para que as etapas a seguir não sejam necessárias nas versões mais recentes.   

Use os procedimentos a seguir para abandonar a chave de criptografia.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>O que fazer se você precisar abandonar a chave de criptografia

Se você precisar abandonar a chave de criptografia, use os procedimentos a seguir para fazer isso.

1. [Parar o serviço de sincronização](#stop-the-synchronization-service)

1. [Abandone a chave de criptografia existente](#abandon-the-existing-encryption-key)

2. [Forneça a senha da conta do conector de AD DS](#provide-the-password-of-the-ad-ds-connector-account)

3. [Reinicializar a senha da conta de serviço do ADSync](#reinitialize-the-password-of-the-adsync-service-account)

4. [Inicie o serviço de sincronização](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>Parar o serviço de sincronização
Primeiro, você pode parar o serviço Gerenciador de controle de serviço no Windows.  Verifique se o serviço não está em execução durante a tentativa de interrompê-lo.  Se for, aguarde até que ele for concluído e, em seguida, pará-lo.


1. Vá para o Gerenciador de Controle de Serviços do Windows (INICIAR → Serviços).
2. Selecione **Microsoft Azure AD Sync** e clique em Parar.

#### <a name="abandon-the-existing-encryption-key"></a>Abandone a chave de criptografia existente
Abandone a chave de criptografia existente para que essa nova chave de criptografia possa ser criada:

1. Entre no seu servidor de Azure AD Connect como administrador.

2. Inicie uma nova sessão do PowerShell.

3. Navegue até a pasta: `'$env:ProgramFiles\Microsoft Azure AD Sync\bin\'`

4. Execute o comando: `./miiskmu.exe /a`

![Captura de tela que mostra o PowerShell após a execução do comando.](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-connector-account"></a>Forneça a senha da conta do conector de AD DS
Como as senhas existentes armazenadas no banco de dados não podem mais ser descriptografadas, você precisa fornecer o serviço de sincronização com a senha da conta do conector de AD DS. O serviço de sincronização criptografa as senhas usando a nova chave de criptografia:

1. Inicie o Synchronization Service Manager (INICIAR → Serviço de Sincronização).
</br>![Synchronization Service Manager](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Acesse a guia **Conectores**.
3. Selecione o **AD Connector** que corresponde ao seu AD local. Se você tiver mais de um AD Connector, repita as etapas a seguir para cada um deles.
4. Em **ações**, selecione **Propriedades**.
5. Na caixa de diálogo pop-up, selecione **Conectar-se à floresta do Active Directory**:
6. Insira a senha da conta do AD DS na caixa de texto **Senha**. Se você não souber a senha, configure-a para um valor conhecido antes de executar essa etapa.
7. Clique em **OK** para salvar a nova senha e fechar a caixa de diálogo pop-up.
![Captura de tela que mostra a página "conectar-se à Active Directory floresta" na janela "Propriedades".](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-adsync-service-account"></a>Reinicializar a senha da conta de serviço do ADSync
Você não pode fornecer diretamente a senha da conta de serviço do Azure AD para o serviço de sincronização. Em vez disso, você precisa usar o cmdlet **Add-ADSyncAADServiceAccount** para reinicializar a conta de serviço do Azure AD. O cmdlet redefine a senha da conta e a torna disponível para o serviço de sincronização:

1. Inicie uma nova sessão do PowerShell no servidor do Azure AD Connect.
2. Execute o cmdlet `Add-ADSyncAADServiceAccount`.
3. Na caixa de diálogo pop-up, forneça as credenciais de administrador global do Azure AD para seu locatário do Azure AD.
![Utilitário de chave de criptografia de sincronização do Azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Se isso for bem-sucedido, você verá o prompt de comando do PowerShell.

#### <a name="start-the-synchronization-service"></a>Inicie o serviço de sincronização
Agora que o serviço de sincronização tem acesso à chave de criptografia e todas as senhas necessárias, você poderá reiniciar o serviço no Gerenciador de Controle de Serviços do Windows:


1. Vá para o Gerenciador de Controle de Serviços do Windows (INICIAR → Serviços).
2. Selecione **Sincronização do Microsoft Azure AD** e clique em Reiniciar.

## <a name="next-steps"></a>Próximas etapas
**Tópicos de visão geral**

* [Sincronização do Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)

* [Integração de suas identidades locais com o Active Directory do Azure](whatis-hybrid-identity.md)
