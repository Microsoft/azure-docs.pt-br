---
title: "Implementação: Azure AD SSPR | Microsoft Docs"
description: "Dicas para uma distribuição bem-sucedida da redefinição da senha de autoatendimento do Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 4cfc1652377f0cfd059e336aec6994b40d32c559
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="roll-out-password-reset-for-users"></a>Implementação da redefinição de senha para usuários

A maioria dos clientes segue as etapas a seguir para garantir uma distribuição tranquila da funcionalidade da SSPR.

1. [Habilitar a redefinição de senha em seu diretório](active-directory-passwords-getting-started.md)
2. [Como configurar permissões do AD locais para write-back de senha](active-directory-passwords-how-it-works.md#active-directory-permissions)
3. [Configurar o write-back de senha](active-directory-passwords-writeback.md#configuring-password-writeback) para gravar senhas do Azure AD de volta ao seu diretório local
4. [Como atribuir e verificar as licenças necessárias](active-directory-passwords-licensing.md)
5. Se você quiser distribuir gradualmente, opcionalmente você pode limitar a redefinição de senha para um grupo de usuários para implementar o recurso lentamente ao longo do tempo. Para isso, defina a **Redefinição da Senha de Autoatendimento Habilitada** de **Todos** para **Um grupo** e selecione um grupo de segurança para habilitar a redefinição de senha. Todos os membros desse grupo devem ter licenças atribuídas a eles e é uma ótima maneira de habilitar o [Licenciamento com Base em Grupo](active-directory-passwords-licensing.md#enable-group-or-user-based-licensing).
6. Popule o conjunto mínimo de [dados de autenticação](active-directory-passwords-data.md) com base em sua política.
7. Ensine seus usuários a usar a SSPR, enviando-os instruções de como registrar e como redefinir.
    > [!NOTE]
    > Teste a SSPR com um usuário e não como um administrador, uma vez que a Microsoft impõe requisitos de autenticação fortes para contas de administrador do Azure. Para obter mais informações sobre a política de senha do administrador, confira o nosso [artigo de aprofundamento](active-directory-passwords-how-it-works.md).

8. Você pode optar por impor o registro a qualquer momento e exigir que os usuários confirmem novamente as informações de autenticação após um período determinado. Se não quiser que os usuários se registrem, você pode [implantar a redefinição de senha sem a necessidade de registro do usuário final](active-directory-passwords-data.md).
9. Ao longo do tempo, revise os [relatórios fornecidos pelo Azure AD](active-directory-passwords-reporting.md) para examinar os usuários que usam e se registram.

## <a name="email-based-rollout"></a>Distribuição baseada em email

Muitos clientes acreditam que uma campanha de email com instruções simples de uso é a maneira mais fácil para que os usuários usem a SSPR. [Você criou três e-mails simples que você pode usar como modelos para ajudá-lo em sua distribuição.](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16)

* **Em breve** o modelo de email a ser usado em semanas ou dias antes da distribuição para que os usuários saibam que eles precisam fazer algo.
* **Disponível agora** modelo de email a ser usado no dia de início para orientar os usuários a se registrarem e confirmar seus dados de autenticação para que possam usar a SSPR quando necessário.
* **Lembrete de inscrição** modelo de email por alguns dias ou semanas após a implantação para lembrar aos usuários de se registrarem e confirmarem seus dados de autenticação.

## <a name="creating-your-own-password-portal"></a>Criando seu próprio portal de senha

Muitos de nossos clientes grandes preferem hospedar uma página e criar uma entrada DNS raiz, como https://passwords.contoso.com. Eles populam esta página com links para a redefinição da senha do Azure AD, registro da redefinição de senha, portais de alteração de senha e outras informações específicas da organização. Em comunicações de email ou folhetos enviados, você pode incluir um URL personalizada que seja fácil de lembrar para que os usuários possam acessar quando precisarem usar os serviços.

* Redefinição de senha do portal - https://passwordreset.microsoftonline.com/
* Portal de registro de redefinição de senha - http://aka.ms/ssprsetup
* Portal de alteração de senha - https://account.activedirectory.windowsazure.com/ChangePassword.aspx

## <a name="using-enforced-registration"></a>Usando o registro imposto

Se desejar que os usuários se registrem para a redefinição de senha, você pode forçá-los a se registrar quando entrarem usando o Azure AD. Você pode habilitar essa opção na folha de **redefinição de senha** do seu diretório ativando a opção **Exigir que os Usuários se Registrem ao Entrar** na guia **Registro**.

Os administradores podem exigir que os usuários se registrem novamente após um período definindo o **número de dias antes que os usuários são solicitados a reconfirmar suas informações de autenticação** entre 0 e 730 dias.

Depois de habilitar essa opção, os usuários entrando verão uma mensagem informando-os que seu administrador solicitou que verifiquem suas informações de autenticação.

## <a name="populate-authentication-data"></a>Popular os dados de autenticação

Se você [popular os dados de autenticação para seus usuários](active-directory-passwords-data.md), os usuários não precisam se registrar para redefinição de senha antes de poderem usar a SSPR. Desde que os usuários tenham dados de autenticação definidos que atendam à política de redefinição de senha que você definiu, os usuários serão capazes de redefinir suas senhas.

## <a name="disabling-self-service-password-reset"></a>Desabilitação da redefinição da senha de autoatendimento

Desabilitar a redefinição de senha de autoatendimento é tão simples quanto abrir seu locatário do Azure AD e acessar **Redefinição de Senha**, **Propriedades** e escolher **Ninguém** em **Redefinição da Senha de Autoatendimento Habilitada**

## <a name="next-steps"></a>Próximas etapas

Os links a seguir fornecem informações adicionais sobre a redefinição de senha usando o Azure AD

* [**Início Rápido**](active-directory-passwords-getting-started.md): comece agora mesmo a usar o gerenciamento de autoatendimento de senhas do Azure AD 
* [**Licenciamento**](active-directory-passwords-licensing.md): configure o licenciamento do Azure AD
* [**Dados** ](active-directory-passwords-data.md) - Entender os dados necessários e como são usados para o gerenciamento de senhas
* [**Personalização** ](active-directory-passwords-customize.md) - Personalize a aparência da experiência da SSPR para sua empresa.
* [**Política** ](active-directory-passwords-policy.md) - Como entender e definir políticas de senha do Azure AD
* [**Write-back de senha** ](active-directory-passwords-writeback.md) - Como o write-back de senha opera com o seu diretório local
* [**Relatório** ](active-directory-passwords-reporting.md) - Descubra se, quando e onde os usuários estão acessando a funcionalidade da SSPR
* [**Detalhamento Técnico**](active-directory-passwords-how-it-works.md): veja os bastidores para entender como o recurso funciona
* [**Perguntas frequentes**](active-directory-passwords-faq.md): como? Por quê? O quê? Onde? Quem? Quando? – respostas para perguntas que você sempre quis fazer
* [**Solução de problemas**](active-directory-passwords-troubleshoot.md) - Saiba como resolver problemas comuns que vemos com a SSPR