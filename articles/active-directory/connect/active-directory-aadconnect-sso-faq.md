---
title: "Azure AD Connect: Logon Único Contínuo – perguntas frequentes | Microsoft Docs"
description: "Respostas às perguntas frequentes sobre o Logon Único Contínuo do Azure Active Directory."
services: active-directory
keywords: "o que é o Azure AD Connect, instalar o Active Directory, componentes necessários do Azure AD, SSO, Logon Único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: f603c4f0305184bfefe23a02b07cef134c83e678
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Logon Único Contínuo do Azure Active Directory: perguntas frequentes

Neste artigo abordamos perguntas frequentes sobre o Logon Único Contínuo do Azure Active Directory (SSO contínuo). Continue verificando para ver novo conteúdo.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Com quais métodos de entrada o SSO Contínuo funcionam?

O SSO Contínuo pode ser combinado com o método de entrada de [Sincronização de Hash de Senha](active-directory-aadconnectsync-implement-password-synchronization.md) ou de [Autenticação de Passagem](active-directory-aadconnect-pass-through-authentication.md). No entanto esse recurso não pode ser usado com os Serviços de Federação do Active Directory (ADFS).

## <a name="is-seamless-sso-a-free-feature"></a>O SSO Contínuo é um recurso gratuito?

O SSO Contínuo é um recurso gratuito e você não precisa de nenhuma edição paga do Azure AD para usá-lo. Ele permanecerá gratuito quando o recurso chegar à disponibilidade geral.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Quais aplicativos tiram proveito da capacidade de parâmetro `domain_hint` ou `login_hint` do SSO Contínuo?

Estamos no processo de compilação da lista de aplicativos que enviam esses parâmetros e aqueles que não. Se houver aplicativos nos quais você tenha interesse, avise-nos na seção comentários.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>O SSO Contínuo dá suporte ao `Alternate ID` como o nome de usuário, em vez de `userPrincipalName`?

Sim. O SSO Contínuo dá suporte ao `Alternate ID` como o nome de usuário quando configurado no Azure AD Connect, conforme mostrado [aqui](active-directory-aadconnect-get-started-custom.md). Nem todos os aplicativos do Office 365 dão suporte ao `Alternate ID`. Consulte a documentação específica do aplicativo para obter o demonstrativo de suporte.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Desejo registrar dispositivos que não sejam Windows 10 com o Azure AD, sem o uso do AD FS. Posso usar o SSO Contínuo em vez disso?

Sim, esse cenário precisa da versão 2.1 ou posterior do [cliente de ingresso no local de trabalho](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Como posso sobrepor a chave de descriptografia de Kerberos a `AZUREADSSOACC` conta de computador?

É importante sobrepor frequentemente a chave de descriptografia de Kerberos a `AZUREADSSOACC` conta de computador (que representa o AD do Azure) criada na floresta do AD do seu local.

>[!IMPORTANT]
>É altamente recomendável sobrepor a chave de descriptografia do Kerberos pelo menos a cada 30 dias.

Siga estas etapas no servidor local em que você está executando o Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Etapa 1. Obter lista de florestas do AD em que o SSO Contínuo foi habilitado

1. Primeiro, baixe e instale o [Assistente de Conexão do Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Em seguida, baixe e instale o [Módulo do Azure Active Directory de 64 bits para Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Navegue até a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importe o módulo do PowerShell de SSO Contínuo usando este comando: `Import-Module .\AzureADSSO.psd1`.
5. Execute o PowerShell como um Administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Esse comando deve fornecer a você um pop-up para inserir suas credenciais de Administrador Global do locatário.
6. Chame `Get-AzureADSSOStatus`. Esse comando fornece a lista de florestas do AD (consulte a lista “Domínios”) em que esse recurso foi habilitado.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>Etapa 2. Atualizar a chave de descriptografia de Kerberos em cada floresta do AD que foi configurado

1. Chame `$creds = Get-Credential`. Quando solicitado, insira as credenciais de Administrador de Domínio da floresta do AD pretendida.
2. Chame `Update-AzureADSSOForest -OnPremCredentials $creds`. Esse comando atualiza a chave de descriptografia do Kerberos para a `AZUREADSSOACC` conta de computador nessa floresta do AD específico e a atualiza no AD do Azure.
3. Repita as etapas anteriores para cada floresta do AD em que você configurou o recurso.

>[!IMPORTANT]
>Certifique-se de _não_ executar o `Update-AzureADSSOForest` comando mais de uma vez. Caso contrário, o recurso deixará de funcionar até o momento em que os tíquetes Kerberos dos usuários expirarem e forem reemitidos pelo Active Directory local.

## <a name="how-can-i-disable-seamless-sso"></a>Como faço para desabilitar o SSO Contínuo?

O SSO Contínuo pode ser desabilitado usando o Azure AD Connect.

Execute o Azure AD Connect, escolha “Alterar página de conexão do usuário” e clique em “Avançar”. Em seguida, desmarque a opção “Habilitar logon único”. Continue com o assistente. Após a conclusão do assistente, o SSO Contínuo é desabilitado em seu locatário.

No entanto, você verá uma mensagem na tela que informa o seguinte:

“O logon único agora está desabilitado, mas há etapas manuais adicionais a serem realizadas para concluir a limpeza. Saiba mais”

Para concluir o processo, siga estas etapas manuais no servidor local em que você está executando o Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Etapa 1. Obter lista de florestas do AD em que o SSO Contínuo foi habilitado

1. Primeiro, baixe e instale o [Assistente de Conexão do Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Em seguida, baixe e instale o [Módulo do Azure Active Directory de 64 bits para Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Navegue até a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importe o módulo do PowerShell de SSO Contínuo usando este comando: `Import-Module .\AzureADSSO.psd1`.
5. Execute o PowerShell como um Administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Esse comando deve fornecer a você um pop-up para inserir suas credenciais de Administrador Global do locatário.
6. Chame `Get-AzureADSSOStatus`. Esse comando fornece a lista de florestas do AD (consulte a lista “Domínios”) em que esse recurso foi habilitado.

### <a name="step-2-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>Etapa 2. Exclua manualmente a conta do computador `AZUREADSSOACCT` de cada floresta do AD que você vir listada.

## <a name="next-steps"></a>Próximas etapas

- [**Início Rápido** ](active-directory-aadconnect-sso-quick-start.md) – colocar o SSO Contínuo do Azure AD em funcionamento.
- [**Aprofundamento técnico**](active-directory-aadconnect-sso-how-it-works.md) – entenda como esse recurso funciona.
- [**Solução de problemas**](active-directory-aadconnect-troubleshoot-sso.md) – Saiba como resolver problemas comuns do recurso.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para registrar solicitações de novos recursos.
