---
title: "O que é o painel de acesso no Azure Active Directory? | Microsoft Docs"
description: "Saiba como usar variações do painel de acesso (navegador da Web, aplicativo Android, iPhone e iPad) para acessar os aplicativos SaaS."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: bd9066c251188c0f18fe1a9403baa2beaeeb987c
ms.contentlocale: pt-br
ms.lasthandoff: 08/01/2017

---
# <a name="what-is-the-access-panel"></a>O que é o painel de acesso?

O painel de acesso é um portal baseado na Web. Ele permite que um usuário com uma conta corporativa ou de estudante no Azure Active Directory exiba e inicie aplicativos baseados em nuvem que um administrador do Azure AD concedeu acesso. Você também pode usar os recursos de gerenciamento de grupo de autoatendimento e aplicativo por meio do painel de acesso.

O painel de acesso é separado do Portal do Azure e você não tem uma assinatura do Azure.

![Painel de acesso][1]

O painel de acesso permite que você edite algumas de suas configurações de perfil, incluindo a capacidade de:

- Alterar a senha associada a uma conta corporativa ou de estudante

- Editar configurações de redefinição de senha

- Editar configurações de preferências e contato relacionadas a autenticação multifator (para contas cujo administrador solicitou o uso desse recurso)

- Exibir detalhes da conta, como ID de usuário, email alternativo, números de celular e do escritório e dispositivos

- Exibir e iniciar aplicativos baseados em nuvem aos quais o administrador do Azure AD concedeu acesso. Para saber mais sobre o painel de acesso da perspectiva dos usuários, veja Usando o painel de acesso. 

- Autogerenciar grupos. Mais especificamente, o administrador pode criar e gerenciar grupos de segurança e solicitar associações ao grupo de segurança no Azure AD. Para saber mais, confira [Gerenciamento de grupo de autoatendimento para usuários no Azure AD](active-directory-accessmanagement-self-service-group-management.md) e [Gerenciar seus grupos](active-directory-manage-groups.md).




## <a name="accessing-the-access-panel"></a>Acessando o painel de acesso

Você pode acessar o painel de acesso visitando a seguinte URL em um navegador da Web: `http://myapps.microsoft.com`

Se tiver a identidade visual personalizada configurada para sua página de entrada, você poderá carregar essa identidade visual, anexando o domínio da sua organização ao final da URL: `http://myapps.microsoft.com/<your domain>.com`

Nesse caso, você pode usar qualquer nome de domínio ativo ou verificado que foi configurado no portal do Azure.

![Nome de domínio Wingtip Toys][2]  

Você precisa distribuir a URL a todos os usuários que entrarão nos aplicativos integrados ao Azure AD.

## <a name="authentication"></a>Autenticação

Para acessar o painel de acesso, você deve ser autenticado usando uma conta corporativa ou de estudante no Azure AD. Você pode ser autenticado no Azure AD diretamente. Como alternativa, se uma organização tiver configurado a federação usando o AD FS (Serviços de Federação do Active Directory) ou outras tecnologias, você pode ser autenticado pelo Windows Server Active Directory.

Se você tiver uma assinatura do Azure ou Office 365 e estiver usando o Portal do Azure ou um aplicativo do Office 365, você verá a lista de aplicativos sem entrar novamente. Se você não está autenticado receberá uma solicitação para entrar usando o nome de usuário e a senha de sua conta no Azure AD. Se sua organização tiver configurado a federação, digitar o nome do usuário será suficiente.

Quando está autenticado, você pode interagir com os aplicativos integrados ao diretório pelo administrador. Para saber como integrar aplicativos ao Azure AD, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure?](active-directory-appssoaccess-whatis.md).

## <a name="web-browser-requirements"></a>Requisitos de navegador da Web

O painel de acesso exige, pelo menos, um navegador com suporte para JavaScript e CSS habilitado. Para que o usuário possa acessar os aplicativos usando SSO (logon único) baseado em senha, a extensão do painel de acesso deve estar instalada no seu navegador. A extensão é baixada automaticamente quando você seleciona um aplicativo configurado para SSO baseado em senha.

No momento, a extensão do painel de acesso está disponível para os navegadores Internet Explorer 8 e superior, Edge, Chrome e Firefox.

## <a name="mobile-app-support"></a>Suporte a aplicativos móveis

A equipe do Azure Active Directory publica o aplicativo móvel Meus Aplicativos. Quando você instala esse aplicativo, você pode entrar em aplicativos de SSO baseado em senha em dispositivos iOS e Android.

> [!NOTE]
> Você pode entrar em aplicativos que dão suporte à federação com o Azure AD (incluindo Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 e mais de 70 outros) em praticamente qualquer navegador da Web em qualquer dispositivo, sem a necessidade de um plug-in ou aplicativo móvel. O restante da [experiência do Painel de Acesso](https://myapps.microsoft.com/) também não exige que o aplicativo móvel Meus Aplicativos seja usado em um dispositivo móvel.
>
>

### <a name="my-apps-for-android"></a>Meus aplicativos para Android

Meus aplicativos para Android tem suporte em qualquer dispositivo Android que esteja executando o Android versão 4.1 e posteriores.  
Ele está disponível na [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.myapps).

![Meus aplicativos para Android][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>Meus aplicativos para iPhone e iPad

Meus aplicativos para iOS tem suporte em qualquer iPhone ou iPad que esteja executando a versão do iOS 7 e posterior.  
Ele está disponível na [Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8).

![Meus aplicativos para iOS][4]    



## <a name="managed-browser-for-my-apps"></a>Navegador gerenciado para Meus aplicativos

Meus aplicativos também é integrado no Intune Managed Browser. O Intune Managed Browser para dispositivos iOS e Android desempenha uma função fundamental em assegurar que os dados em dispositivos móveis permaneçam seguros. Ele permite exibir e navegar por páginas da web que podem conter informações da empresa e fornece uma experiência de navegação na web segura.  
Você encontrar acesso rápido aos meus aplicativos em sua home page no Managed Browser e em seus favoritos, fornecendo menos cliques para acessar qualquer aplicativo que você deseja acessar.

Ele está disponível na [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) e na [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser&hl=en).

![Navegador gerenciado para Meus aplicativos][5]    





## <a name="tips-for-testing-the-user-experience"></a>Dicas para testar a experiência do usuário

Se você for um administrador do Azure e estiver conectado ao Portal do Azure usando uma conta no diretório, será automaticamente conectado ao painel de acesso como sua conta atual. Nesse caso, você pode ver todos os aplicativos atribuídos a você.

**Para testar como uma *conta de usuário* diferente:**

1. Clique no menu de usuário no canto superior direito do Portal do Azure ou no painel de acesso e, em seguida, selecione **Sair**. 
2. Acesse o [painel de acesso](http://myapps.microsoft.com).
3. Na página de entrada, digite o nome de usuário e a senha da conta no diretório que você quer testar.


## <a name="starting-applications"></a>Iniciar aplicativos

Vários tipos de aplicativos podem aparecer no painel de acesso.

### <a name="office-365-applications"></a>Aplicativos do Office 365

Se sua organização estiver usando aplicativos do Office 365 e você for licenciado para eles, os aplicativos do Office 365 serão exibidos no seu painel de acesso.

Quando você clica em um bloco do aplicativo para um aplicativo do Office 365, você é redirecionado para o aplicativo e conectado automaticamente.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Aplicativos da Microsoft e de terceiros configurados com o SSO baseado em federação

Seu administrador pode adicionar aplicativos na seção Active Directory do Portal do Azure com o modo SSO definido como **Logon único do Azure AD**. Você somente poderá ver esses aplicativos se seu administrador tiver concedido explicitamente a você acesso aos aplicativos.

Quando você clica em um bloco de um desses aplicativos, você é redirecionado e conectado automaticamente a esse aplicativo.

### <a name="password-based-sso-without-identity-provisioning"></a>SSO baseado em senha sem provisionamento de identidade

Seu administrador pode adicionar aplicativos na seção Active Directory do Portal do Azure com o modo SSO definido como **Logon único do baseado em senha**. Todos os usuários no diretório podem ver todos os aplicativos configurados nesse modo.

Na primeira vez que você clica em um bloco de um desses aplicativos, você recebe uma solicitação para instalar o plug-in do SSO de senha para o Internet Explorer ou Chrome. A instalação pode exigir você a reinicialização do seu navegador da Web. Quando você retorna ao painel de acesso e clica novamente no bloco do aplicativo, você recebe uma solicitação para inserir um nome de usuário e senha para o aplicativo. Quando você inseriu o nome de usuário e senha, essas credenciais são armazenadas com segurança no e vinculadas à sua conta no Azure AD.

Na próxima vez que você clicar no bloco de aplicativo, será automaticamente conectado ao aplicativo.  
Você não precisará inserir suas credenciais novamente e/ou instalar o plug-in do SSO de senha.

Se suas credenciais de um usuário forem alteradas no aplicativo de terceiros de destino, você também deverá atualizar suas credenciais armazenadas no Azure AD. 

**Para atualizar as credenciais:**

1. Selecione o ícone no bloco do aplicativo.
2. Selecione **atualizar credenciais** para inserir novamente o nome de usuário e a senha para o aplicativo.


### <a name="password-based-sso-with-identity-provisioning"></a>SSO baseado em senha com provisionamento de identidade

Seu administrador pode adicionar aplicativos na seção **Active Directory** do Portal do Azure com o modo SSO definido como **Logon único do baseado em senha** junto com o provisionamento da identidade.

Na primeira vez que você clica em um bloco de aplicativo de um desses aplicativos, você recebe uma solicitação para instalar o **plug-in do SSO de senha para o Internet Explorer ou Chrome**. A instalação pode exigir você a reinicialização do seu navegador da Web.  
Quando você retorna ao painel de acesso e clica novamente no bloco do aplicativo, você é automaticamente conectado ao aplicativo.

Alguns aplicativos podem exigir que você altere sua senha na primeira entrada. Se suas credenciais de um usuário forem alteradas no aplicativo de terceiros de destino, você também deverá atualizar as credenciais armazenadas no Azure AD. 

**Para atualizar as credenciais:**

1. Selecione o ícone no bloco do aplicativo.
2. Selecione **atualizar credenciais** para inserir novamente o nome de usuário e a senha para o aplicativo.


### <a name="application-with-existing-sso-solutions"></a>Aplicativos com soluções de SSO existentes

Para configurar o SSO para um aplicativo, o Portal do Azure fornece uma terceira opção chamada de **Logon Único Existente**. Essa opção permite que o administrador crie um link para um aplicativo e coloque-o no painel de acesso para os usuários selecionados.

Por exemplo, se um aplicativo for configurado para autenticar usuários usando o AD FS 2.0, seu administrador poderá usar a opção **Logon Único Existente** para criar um link para ele no painel de acesso. Quando os você acessar o link, você é autenticado por meio do AD FS 2.0 ou por qualquer solução de SSO existente fornecida pelo aplicativo.


## <a name="next-steps"></a>Próximas etapas

- Para ver uma lista de todos os tópicos relacionados ao gerenciamento de aplicativos, consulte o [índice do artigo para gerenciamento de aplicativos no Azure Active Directory](active-directory-apps-index.md).
 
- Para saber como integrar um aplicativo SaaS no AD do Azure, consulte a [lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md).
 
- Para saber mais sobre como gerenciar aplicativos com o AD do Azure, consulte a [Introdução ao acesso ao aplicativo de gerenciamento e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).
 
- Para obter mais informações sobre provisionamento de usuário, consulte [aplicativos SaaS para provisionamento e desprovisionamento automatizado do usuário](active-directory-saas-app-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[3]: ./media/active-directory-saas-access-panel-introduction/03.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png

