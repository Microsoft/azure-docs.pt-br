---
title: "Solucionar problemas de verificação em duas etapas | Microsoft Docs"
description: "Este documento fornecerá informações aos usuários sobre o que fazer em caso de um problema com a Autenticação Multifator do Azure."
services: multi-factor-authentication
keywords: "autenticação multifator do cliente, problema de autenticação, ID de correlação"
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: end-user
ms.openlocfilehash: 9dbe88a59b68bfb424c43dd89acf55d8c73fdf39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-help-with-two-step-verification"></a>Obter ajuda com a verificação em duas etapas
Este artigo responde às perguntas mais comuns que as pessoas fazem sobre verificação em duas etapas. 

## <a name="why-do-i-have-to-perform-two-step-verification-can-i-turn-it-off"></a>Por que é necessário que executar a verificação em duas etapas? Posso desativá-la?

A verificação em duas etapas é um recurso de segurança que sua organização decidiu usar para proteger suas contas. É mais segura do que apenas uma senha, porque se baseia em duas formas de autenticação: algo que você sabe e algo que você tem com você. Algo que você sabe é a sua senha. Algo que você tem com você é um telefone ou dispositivo que você normalmente carrega. Quando sua conta estiver protegida com a verificação em duas etapas, um hacker mal-intencionado não poderá entrar como se fosse você, mesmo se ele obtiver sua senha. Eles não podem entrar porque não têm acesso ao seu telefone. 

A Microsoft oferece verificação em duas etapas, mas sua organização escolhe se vai usar o recurso. Você não poderá recusar se o departamento de TI exigir que você use, assim como você não pode optar por não usar uma senha para proteger sua conta. 

Se você tiver a verificação em duas etapas ativada para a sua conta pessoal da Microsoft e deseja alterar as configurações, leia [Sobre verificação em duas etapas](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) em vez disso. 

## <a name="i-dont-have-my-phone-with-me-today"></a>Não tenho meu telefone comigo hoje

Em alguns dias, você deixa seu telefone em casa, mas ainda precisa entrar no trabalho. A primeira opção que você deve tentar é entrar com um método de verificação diferente. Ao registrar-se para a verificação em duas etapas, você configurou mais de um número de telefone? Para tentar entrar com um método diferente, siga estas etapas:

1. Entre como faria normalmente.
2. Quando a página de verificação em duas etapas for aberta, escolha **Usar outra opção de verificação**.

   ![Verificação diferente](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Selecione a opção de verificação que você deseja usar. 
  - Se você não tiver acesso a seus métodos alternativos, entre em contato com seu departamento de TI para obter ajuda para entrar na sua conta.
  - Se você tem acesso a seus métodos alternativos, continue com a verificação em duas etapas.

Se você não vir o link **Usar outra opção de verificação**, isso significa que você não configurou métodos alternativos ao registrar-se inicialmente para a verificação em duas etapas. Entre em contato com o departamento de TI para obter ajuda para entrar na sua conta. Quando você estiver conectado, [gerencie suas configurações](multi-factor-authentication-end-user-manage-settings.md) para adicionar mais métodos de verificação para a próxima vez. 

## <a name="i-lost-my-phone-or-got-a-new-number"></a>Eu perdi meu telefone ou tenho um novo número
Há duas maneiras de recuperar sua conta. A primeira é fazer logon usando seu número de telefone de autenticação alternativo, se você tiver definido um. A segunda opção é solicitar que o departamento de TI limpe as suas configurações.

Se o seu telefone tiver sido perdido ou roubado, também recomendamos que você informe seu departamento de TI. Será necessário redefinir suas senhas de aplicativos e desmarcar quaisquer dispositivos lembrados. 

### <a name="use-an-alternate-phone-number"></a>Use um número de telefone alternativo
Se você tiver configurado várias opções de verificação, como um número de telefone secundário ou um aplicativo autenticador em um dispositivo diferente, use uma destas opções para entrar.

Para fazer entrar usando o número de telefone alternativo, siga estas etapas:

1. Entre como faria normalmente.
2. Quando solicitado a verificar sua conta, escolha **Use uma opção de verificação diferente**.
   
   ![Verificação diferente](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Selecione o número de telefone ou o dispositivo ao qual você tem acesso.
4. Quando estiver em sua conta, [gerencie suas configurações](multi-factor-authentication-end-user-manage-settings.md) para alterar o número de telefone de autenticação.

### <a name="clear-your-settings"></a>Limpe suas configurações
Se você não tiver configurado um número de telefone de autenticação secundário, entre em contato com o departamento de TI para obter ajuda. Peça que limpem as suas configurações para que, na próxima vez em que você entrar, seja solicitado a [registrar-se para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md) novamente.

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>Não estou recebendo uma mensagem de texto ou chamada telefônica em meu telefone
Há várias razões por que você pode tentar entrar, mas não recebe a chamada telefônica ou a mensagem de texto. Se você já recebeu com êxito mensagens ou chamadas telefônicas em seu telefone antes, então, esse é provavelmente um problema com o provedor de telefone, não sua conta. Verifique se o sinal do seu celular é bom. E, se você estiver tentando receber uma mensagem de texto, certifique-se de que você é capaz de receber mensagens de texto. Peça um amigo para ligar ou enviar uma mensagem de texto a você como teste. 

Se você aguardou vários minutos por uma chamada ou mensagem de texto, a maneira mais rápida de acessar sua conta é tentar uma opção diferente.

1. Selecione **Usar uma opção de verificação diferente** na página que está aguardando a sua verificação.
   
    ![Verificação diferente](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. Selecione o número de telefone ou método de entrega que deseja usar.
   
    Se você tiver recebido vários códigos de verificação, use o mais recente.

Se você não tiver outro método configurado, contate o departamento de TI e peça para limpar as suas configurações. Na próxima vez que entrar, você será solicitado a [Configurar autenticação multifator](multi-factor-authentication-end-user-first-time.md) novamente.

Se você geralmente tem atrasos na recepção devido a um sinal de celular ruim, recomendamos que você use o [aplicativo Microsoft Authenticator](microsoft-authenticator-app-how-to.md) em seu smartphone. O aplicativo pode gerar códigos de segurança aleatórios que você usa para entrar, e esses códigos não exigem qualquer conexão de internet ou um sinal de celular.

## <a name="app-passwords-are-not-working"></a>As senhas de aplicativo não estão funcionando.
Primeiro, verifique se você inseriu a senha de aplicativo corretamente. A senha de aplicativo gerada substitui a senha normal, mas somente para aplicativos de área de trabalho mais antigos que não dão suporte à verificação em duas etapas. Se isso ainda não estiver funcionando, tente conectar-se e [criar uma nova senha de aplicativo](multi-factor-authentication-end-user-app-passwords.md).  Se isso ainda não funcionar, entre em contato com o departamento de TI e peça para [excluir suas senhas de aplicativo existentes](../multi-factor-authentication-manage-users-and-devices.md), então você poderá criar uma nova.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Não encontrei uma resposta para o meu problema.
Se você tiver tentado essas etapas de solução de problemas, mas ainda estiver encontrando problemas, entre em contato com o seu departamento de TI. Eles devem ser capazes de ajudá-lo.

## <a name="related-topics"></a>Tópicos relacionados
* [Gerenciar as configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md)  
* [Perguntas frequentes sobre o aplicativo Microsoft Authenticator](microsoft-authenticator-app-faq.md)

