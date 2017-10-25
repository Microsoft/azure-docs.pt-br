---
title: Entrada pelo telefone do Microsoft Authenticator - Contas do Azure e da Microsoft | Microsoft Docs
description: Use seu telefone para entrar em sua conta da Microsoft em vez de digitar sua senha. Este artigo responde a Perguntas Frequentes sobre esse recurso.
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: femila
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 9fb25f9f26a220c90f82add02fa5bdb954a926c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sign-in-with-your-phone-not-your-password"></a>Entre com seu telefone, não com sua senha

O aplicativo Microsoft Authenticator ajuda a proteger suas contas executando verificação em duas etapas depois de inserir sua senha. Mas você sabia que ele pode substituir totalmente a senha de sua conta pessoal da Microsoft?

Esse recurso está disponível em dispositivos iOS e Android e funciona com contas pessoais da Microsoft.

## <a name="how-it-works"></a>Como ele funciona

Muitos de vocês usam o aplicativo Microsoft Authenticator para verificação em duas etapas quando você entrar em sua conta da Microsoft. Digite sua senha, e acesse o aplicativo para aprovar uma notificação ou obter um código de verificação. Com a entrada pelo telefone, você ignora a senha e faz toda a verificação de identidade em seu telefone. Porque a entrada do telefone é um tipo de verificação em duas etapas, você ainda precisa fornecer uma coisa que você sabe e algo que você precisa verificar sua identidade. O telefone ainda é a coisa que você tem e PIN ou a chave biométrico seu telefone é a coisa que você sabe.

## <a name="how-to-get-started"></a>Como começar

Para entrar em sua conta pessoal da Microsoft com seu telefone, siga estas etapas:

1. Habilitar entrada pelo telefone para sua conta.

  - Se você ainda não tiver o aplicativo Microsoft Authenticator, instale e adicione sua conta pessoal da Microsoft de acordo com as etapas na [Página do Microsoft Authenticator](microsoft-authenticator-app-how-to.md). Contas recém-adicionadas são habilitadas automaticamente, portanto você está pronto para começar.

  - Se você já usa o Microsoft Authenticator para verificação em duas etapas, selecione sua conta na página inicial do aplicativo e selecione **Habilitar entrada pelo telefone** no menu suspenso.

  >[!NOTE]
  >Para proteger sua conta, exigimos um PIN ou bloqueio biométrico em seu dispositivo. Se você mantiver seu telefone desbloqueado, o aplicativo exibirá uma solicitação para que você configure um bloqueio antes de habilitar a entrada pelo telefone.

3. A maioria das páginas em que você normalmente digitaria sua senha de conta da Microsoft tem um link que diz **Usar um aplicativo**. Selecione este link para entrar com seu telefone.

4. A Microsoft envia uma notificação para seu telefone. Aprove a notificação para entrar em sua conta.   

## <a name="faq"></a>Perguntas frequentes

### <a name="how-is-signing-in-with-my-phone-more-secure-than-typing-a-password"></a>Como a entrada com meu telefone é mais segura do que digitar uma senha?  

Hoje em dia a maioria das pessoas entra em sites ou aplicativos usando um nome de usuário e senha.  Infelizmente, as senhas são geralmente perdidas, roubadas ou adivinhadas por hackers. Quando você configura o aplicativo Microsoft Authenticator para entrar, geramos uma chave em seu telefone que pode desbloquear sua conta. Podemos proteger essa chave com o PIN ou a biométrica que você já usa em seu telefone.  Quando você entra com seu telefone, essa chave é usada para provar sua identidade com segurança com dois fatores – o telefone em si e sua capacidade para desbloqueá-lo. 

A chave usada é semelhante às chaves usadas no Windows Hello e nas especificações FIDO Alliance UAF. Seus dados biográficos são usados apenas para proteger a chave localmente e nunca são enviados ou armazenados na nuvem. 
 
### <a name="where-can-i-use-my-phone-to-replace-my-password-and-where-would-i-still-need-the-password"></a>Onde posso usar meu telefone para substituir a minha senha e onde eu ainda precisaria da senha?  

Atualmente, o recurso de entrada pelo telefone só funciona com aplicativos Web e serviços que são ativados pelas contas da Microsoft, aplicativos iOS ou Android que usam uma conta pessoal da Microsoft e aplicativos no Windows 10 que usam uma conta pessoal da Microsoft. Quando você entra um desses sites ou aplicativos, na página em que você normalmente digita sua senha há um link que diz **Usar um aplicativo**. 

No momento, a entrada do telefone não pode ser usada para desbloquear um computador com Windows, XBOX ou qualquer versão da área de trabalho de aplicativos Microsoft, como aplicativos do Office.
 
### <a name="does-this-replace-two-step-verification-should-i-turn-it-off"></a>Isso substitui a verificação em duas etapas? Devo desativá-la?   

Às vezes. Estamos trabalhando na expansão do escopo da entrada pelo telefone, mas por enquanto ainda existem locais no ecossistema da Microsoft que não oferecem suporte a ele. Nesses locais, ainda estamos usando a verificação em duas etapas para entrada segura. Por esse motivo, não, você não deve desativar a verificação em duas etapas para a sua conta.
 
### <a name="okay-if-i-keep-two-step-verification-turned-on-for-my-account-do-i-have-to-approve-two-notifications"></a>Ok. Se eu mantiver a verificação em duas etapas ativa em minha conta, será preciso aprovar as duas notificações?

Não, não será necessário. Entrar em sua conta da Microsoft com seu telefone conta como verificação em duas etapas. Em vez de digitar sua senha, ao aprovar uma notificação você comprova sua identidade por saber desbloquear seu telefone e, em seguida, aprovar uma notificação. Não enviaremos uma segunda notificação de aprovação.

### <a name="what-if-i-lose-my-phone-or-dont-have-it-with-me-how-can-i-access-my-account"></a>E se eu perder meu telefone ou não o tiver comigo, como posso acessar minha conta?  

Você sempre poderá clicar em **Usar uma senha** na página de entrada para voltar a usar sua senha. Tenha em mente que se você usar a verificação em duas etapas, ainda precisará de um segundo método para verificar sua entrada. É por isso que recomendamos que você verifique se possui informações de segurança extra atualizadas em sua conta. Você pode gerenciar suas informações de segurança em https://account.live.com/proofs/manage.
 
### <a name="how-do-i-stop-using-this-feature-and-go-back-to-entering-my-password"></a>Como posso parar de usar esse recurso e voltar a digitar minha senha?

Clique em **Usar uma senha** quando você entrar. Nós o lembramos de sua escolha mais recente e a ofereceremos, por padrão, na próxima vez que você entrar. Se você quiser voltar a entrar com seu telefone, clique em **Usar um aplicativo**. 
 
### <a name="can-i-use-the-app-to-sign-in-to-all-my-accounts-with-microsoft"></a>Posso usar o aplicativo para entrar em todas as minhas contas com a Microsoft?   
No momento, essa funcionalidade só está disponível para contas pessoais da Microsoft. 
 
### <a name="can-i-sign-into-my-pc-with-my-phone"></a>Posso entrar no meu computador com o meu telefone?  
Para o seu computador, é recomendável entrar com o Windows Hello no Windows 10 usando o rosto, impressão digital ou um PIN.   
 
### <a name="can-i-sign-in-with-my-windows-phone"></a>Posso entrar com meu Windows Phone?  
Neste momento, estamos desenvolvendo essa funcionalidade para o Microsoft Authenticator no Windows Phone. 

## <a name="next-steps"></a>Próximas etapas
Se você ainda não baixou o aplicativo Microsoft Authenticator, confira. O aplicativo está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), e a entrada pelo telefone está disponível no aplicativo Microsoft Authenticator para [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Se você tiver dúvidas sobre o aplicativo em geral, acesse as [Perguntas frequentes do Microsoft Autenticador](microsoft-authenticator-app-faq.md)
