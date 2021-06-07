---
title: Obter ajuda com o portal Meus Aplicativos – Azure Active Directory | Microsoft Docs
description: Obter ajuda para entrar e executar tarefas comuns no portal Meus Aplicativos.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 4377ed76de971f78336ea9024b59dafc5d513487
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100094960"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>Solucionar problemas com o portal Meus Aplicativos

Caso esteja tendo problemas para entrar ou usar o portal **Meus Aplicativos**, tente essas dicas de solução de problemas antes de entrar em contato com a assistência técnica ou com seu administrador para obter ajuda.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Estou com problemas para instalar a Extensão de Conexão Segura dos Meus Aplicativos

Se você estiver com problemas ao instalar a Extensão de Conexão Segura de Meus Aplicativos:

- Verifique se você está usando um navegador com suporte, incluindo:

    - **Microsoft Edge.** Em execução na Edição de Aniversário do Windows 10 ou posterior.

    - **Google Chrome.** Em execução no Windows 7 ou posterior e no macOS X ou posterior.

    - **Mozilla Firefox 26.0 ou posterior.** Em execução no Windows XP SP2 ou posterior e no macOS X 10,6 ou posterior.

    - **Internet Explorer 11.** Em execução no Windows 7 ou posterior (suporte limitado).

- Verifique se as configurações de extensão do navegador estão ativadas.

- Tente reiniciar o navegador e entrar no portal **Meus Aplicativos** novamente.

- Tente limpar os cookies do navegador e, em seguida, reinicie e entre no portal **Meus Aplicativos** novamente.

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>Não consigo entrar no portal **Meus Aplicativos**

Se você estiver com problemas para entrar no portal **Meus Aplicativos**, tente o seguinte:

- Se você vir um erro ao entrar com um conta Microsoft pessoal, ainda poderá entrar usando o nome de domínio da sua organização (como contoso.com) ou a **ID de locatário** da sua organização em uma das seguintes URLs:

   - https://myapplications.microsoft.com?tenantId=*your_domain_name*
   - https://myapplications.microsoft.com?tenant=*your_tenant_ID*

- Verifique se você está usando a URL correta. Ela deve ser https://myapps.microsoft.com ou uma página personalizada para sua organização, como https://myapps.microsoft.com/contoso.com.

- Verifique se sua senha está correta e não expirou. Para obter mais informações, consulte [Redefinir sua senha corporativa ou de estudante](active-directory-passwords-update-your-own-password.md).

- Verifique se suas informações de verificação estão atualizadas e precisas. Para obter mais informações, consulte [o que a autenticação multifator do Azure ad significa para mim?](./multi-factor-authentication-end-user-first-time.md) ou [alterar seus métodos e informações de informações de segurança](./security-info-setup-auth-app.md).

- Adicione a URL do portal **Meu Aplicativo** à configuração **Propriedades da Internet > Segurança > Sites confiáveis**.

- Limpe o cache do navegador e tente entrar novamente.

## <a name="my-password-isnt-working"></a>Minha senha não está funcionando

Caso tenha esquecido sua senha, nunca tenha recebido uma de sua organização, sua conta tenha sido bloqueada ou queira alterar a senha, consulte [Preciso de ajuda, esqueci minha senha do Azure AD](active-directory-passwords-update-your-own-password.md).

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Eu quero poder redefinir minha própria senha

Para poder redefinir sua própria senha, o administrador deve primeiro ativar o recurso para sua organização e, em seguida, você deve atualizar e verificar os métodos de verificação necessários. Para obter mais informações sobre como atualizar seus métodos de verificação, confira [Registrar-se para redefinição de senha por autoatendimento](active-directory-passwords-reset-register.md).

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>Estou recebendo uma mensagem de acesso negado ao iniciar um aplicativo

Se você está recebendo uma mensagem de **Acesso Negado** depois de iniciar um aplicativo no portal **Meu Aplicativo**, pode tentar o seguinte:

- Verifique se você instalou a [Extensão de Conexão Segura de Meus Aplicativos](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) e está usando um [navegador com suporte](my-apps-portal-end-user-access.md#supported-browsers).

- Verifique se você está usando a URL correta para o aplicativo e se a URL está em sua lista de **Propriedades da Internet > Segurança > Sites confiáveis**.

- Verifique se sua senha está correta e não expirou. Para obter mais informações, consulte [Redefinir sua senha corporativa ou de estudante](active-directory-passwords-update-your-own-password.md).

- Verifique se suas informações de verificação estão atualizadas e precisas. Para obter mais informações, consulte [o que a autenticação multifator do Azure ad significa para mim?](./multi-factor-authentication-end-user-first-time.md) ou [alterar seus métodos e informações de informações de segurança](./security-info-setup-auth-app.md).

- Limpe o cache do navegador e tente entrar novamente.

Se, depois de tentar isso, você ainda não conseguir acessar seu aplicativo, entre em contato com o suporte técnico da sua organização para obter assistência.

## <a name="next-steps"></a>Próximas etapas

Depois de entrar no portal **Meus Aplicativos**, você também pode atualizar suas informações de perfil e conta, suas informações de grupo e informações de revisão de acesso (se você tiver permissão).

- [Acesse e use aplicativos no portal Meus Aplicativos](my-apps-portal-end-user-access.md).

- [Alterar as informações do seu perfil](./my-account-portal-settings.md).

- [Exibir e atualizar suas informações relacionadas a grupos](my-apps-portal-end-user-groups.md).

- [Executar suas próprias revisões de acesso](my-apps-portal-end-user-access-reviews.md).
