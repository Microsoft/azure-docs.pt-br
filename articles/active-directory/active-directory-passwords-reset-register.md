---
title: 'Azure AD: registro SSPR | Microsoft Docs'
description: "Registrar dados de autenticação para redefinição de senha de autoatendimento do Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 87c92265d4f377e76d3697e6957c5f739413f2b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="register-for-self-service-password-reset"></a>Registro de redefinição de senha de autoatendimento

> [!IMPORTANT]
> **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, [veja aqui como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md).

Como um usuário final, você poderá redefinir sua senha ou desbloquear sua conta sem precisar falar com uma pessoa usando a SSPR (redefinição de senha de autoatendimento). Antes de usar essa funcionalidade, você precisa registrar métodos de autenticação ou confirmar os métodos de autenticação predefinidos que administrador preencheu.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registrar ou confirmar dados de autenticação com SSPR

1. Abra o navegador da web em seu dispositivo e vá para o [página de registro de redefinição de senha](http://aka.ms/ssprsetup)
2. Insira seu nome de usuário e senha fornecidos pelo administrador
3. Dependendo de como sua equipe de TI tiver configurado as coisas, uma ou mais das seguintes opções estão disponíveis para configurar e verificar. O administrador pode preencher algumas para você caso eles tenham permissão para usar as informações.
    * O telefone do escritório só é capaz de ser definida pelo administrador
    * O Telefone de Autenticação deve ser definido como outro número de telefone a que você teria acesso como um telefone celular que possa receber uma chamada ou um texto.
    * O email de autenticação deve ser definido como um endereço de email alternativo que você pode acessar sem a senha que será redefinida.
    * As perguntas de segurança fornecem uma lista de perguntas aprovadas pelo administrador para que você responda. Você não pode usar a mesma resposta para mais de uma pergunta.
4. Forneça e verifique as informações exigidas pelo seu administrador. Se houver mais de uma opção, sugerimos que você registre vários métodos para fornecer flexibilidade quando outro método não estiver disponível (exemplo: viajando e sem poder acessar seu telefone comercial)

    ![Registre os métodos de autenticação e clique em Concluir][Register]

5. Quando concluir a etapa 4, escolha **Concluir** e será capaz de usar a redefinição de senha de autoatendimento quando for necessário.

Se você inserir dados no telefone ou email de autenticação, eles não ficarão visíveis no diretório global. As únicas pessoas que podem ver esses dados são você e seus administradores. Só você pode ver as respostas para suas perguntas de segurança.

Os administradores podem exigir que você confirme os métodos de autenticação após um período de tempo para confirmar que os métodos apropriados estão registrados.

## <a name="common-problems-and-their-solutions"></a>problemas comuns e suas soluções

 Aqui estão alguns casos de erro comuns e suas soluções:

| Caso de erro| Que erro você vê?| Solução |
| --- | --- | --- |
| Uma página “Contate seu administrador” é exibida após a inserção da minha ID de usuário | Entre em contato com o seu administrador <br> <br> Detectamos que a senha da sua conta de usuário não é gerenciada pela Microsoft. Portanto, não podemos redefinir sua senha automaticamente. <br> <br> Você precisa entrar em contato com sua equipe de TI para receber mais assistência. | Você está vendo esta mensagem porque a equipe de TI gerencia sua senha no ambiente local e não permite que você redefina sua senha usando o link Não consegue acessar sua conta? <br> <br> Para redefinir sua senha, contate a equipe de TI diretamente para obter ajuda e informe que deseja redefinir sua senha, para que ela possa habilitar o recurso para você.|
| Recebo uma mensagem de erro "sua conta não está habilitada para redefinição de senha" depois de inserir a ID de usuário | Sua conta não está habilitada para redefinição de senha <br> <br> A equipe de TI não configurou sua conta para usar esse serviço. <br> <br> Se desejar, podemos pode contatar um administrador na sua organização para redefinir a senha para você. | Você está vendo esta mensagem porque a equipe de TI não habilitou a redefinição de senha usando o link Não consegue acessar sua conta? para sua organização ou não licenciou o uso por você. <br> <br> Para redefinir sua senha, clique no link “Contate um administrador” para enviar um email à equipe de TI da empresa e informe que deseja redefinir sua senha para que ela possa habilitar o recurso para você. |
| Recebo uma mensagem de erro "não foi possível verificar sua conta" depois de inserir a ID de usuário | Não foi possível verificar sua conta <br> <br> Se desejar, podemos pode contatar um administrador na sua organização para redefinir a senha para você. | Você está vendo esta mensagem porque a redefinição de senha está habilitada, mas você não se registrou para usar o serviço. Para se registrar para redefinição de senha, vá para http://aka.ms/ssprsetup depois que você tiver recuperado o acesso à sua conta. <br> <br> A fim de redefinir sua senha, clique no link contate um administrador para enviar um email à equipe de TI da empresa. |

## <a name="next-steps"></a>Próximas etapas

* [Como alterar a senha usando a redefinição de senha de autoatendimento](active-directory-passwords-update-your-own-password.md)
* [Página de registro de redefinição de senha](http://aka.ms/ssprsetup)
* [Portal de redefinição de senha:](https://passwordreset.microsoftonline.com/)
* [Você não consegue acessar a sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Página de registro de redefinição de senha mostrando métodos registrados e botão Concluir"
