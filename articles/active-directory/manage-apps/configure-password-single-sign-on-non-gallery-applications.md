---
title: Entender o SSO (logon único) baseado em senha para aplicativos no Azure Active Directory
description: Entender o SSO (logon único) baseado em senha para aplicativos no Azure Active Directory
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: kenwith
ms.openlocfilehash: bac04bd70469d7b9c4d9485b6a87fd7133967154
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255298"
---
# <a name="understand-password-based-single-sign-on"></a>Entender o logon único baseado em senha

Na [série de guias de início rápido](view-applications-portal.md) sobre o gerenciamento de aplicativos, você aprendeu a usar o Azure ad como o IDP (provedor de identidade) para um aplicativo. No guia de início rápido, você configura o SSO baseado em SAML ou OIDC. Outra opção é o SSO baseado em senha. Este artigo apresenta mais detalhes sobre a opção de SSO baseado em senha. 

Essa opção está disponível para qualquer site com uma página de entrada HTML. O SSO baseado em senha também é conhecido como compartimentação de senha. O SSO baseado em senha permite que você gerencie o acesso do usuário e as senhas para aplicativos Web que não dão suporte à Federação de identidade. Também é útil quando vários usuários precisam compartilhar uma única conta, como as contas de aplicativo de mídia social da sua organização.

O SSO baseado em senha é uma ótima maneira de começar a integrar aplicativos ao Azure AD rapidamente e permite que você:

- Habilite o logon único para seus usuários armazenando e reproduzindo com segurança nomes de usuário e senhas

- Dar suporte a aplicativos que exigem vários campos de entrada, para aplicativos que exigem mais do que apenas os campos de nome de usuário e senha para entrar

- Personalizar os rótulos dos campos de nome de usuário e senha que os usuários veem em [meus aplicativos](../user-help/my-apps-portal-end-user-access.md) quando inserem suas credenciais

- Permita que os usuários forneçam seus próprios nomes de usuário e senhas para as contas de aplicativos existentes que estão digitando manualmente.

- Permitir que um membro do grupo de negócios especifique os nomes de usuário e senhas atribuídos a um usuário usando o recurso de [Autoatendimento de Acesso ao Aplicativo](./manage-self-service-access.md)

-   Permitir que um administrador especifique um nome de usuário e uma senha a serem usados por indivíduos ou grupos ao entrar no aplicativo com o recurso atualizar credenciais 

## <a name="before-you-begin"></a>Antes de começar

Usar o Azure AD como seu IdP (provedor de identidade) e configurar o logon único (SSO) pode ser simples ou complexo, dependendo do aplicativo que está sendo usado. Alguns aplicativos podem ser configurados com apenas algumas ações. Outros exigem configuração detalhada. Para aprimorar o conhecimento rapidamente, percorra a [série de guias de início rápido](view-applications-portal.md) sobre o gerenciamento de aplicativos. Se o aplicativo que você está adicionando for simples, provavelmente você não precisará ler este artigo. Se o aplicativo que você está adicionando requer configuração personalizada e você precisa usar o SSO baseado em senha, este artigo é para você.

> [!IMPORTANT] 
> Há alguns cenários em que a opção de **logon único** não estará na navegação de um aplicativo em **aplicativos empresariais**. 
>
> Se o aplicativo tiver sido registrado usando **registros de aplicativo** , a funcionalidade de logon único será configurada para usar o OIDC OAuth por padrão. Nesse caso, a opção de **logon único** não será mostrada na navegação em **aplicativos empresariais**. Ao usar **registros de aplicativo** para adicionar seu aplicativo personalizado, você configura opções no arquivo de manifesto. Para saber mais sobre o arquivo de manifesto, consulte [Azure Active Directory manifesto do aplicativo](../develop/reference-app-manifest.md). Para saber mais sobre os padrões de SSO, consulte [autenticação e autorização usando a plataforma de identidade da Microsoft](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform). 
>
> Outros cenários em que o **logon único** ficará ausente da navegação incluem quando um aplicativo é hospedado em outro locatário ou se sua conta não tem as permissões necessárias (administrador global, administrador de aplicativos de nuvem, administrador de aplicativos ou proprietário da entidade de serviço). As permissões também podem causar um cenário em que você pode abrir o **logon único** , mas não poderá salvar. Para saber mais sobre as funções administrativas do Azure AD, consulte ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-configuration"></a>Configuração básica

Na [série de início rápido](view-applications-portal.md), você aprendeu a adicionar um aplicativo ao seu locatário, o que permite que o Azure ad saiba que ele está sendo usado como o IDP (provedor de identidade) para o aplicativo. Alguns aplicativos já estão pré-configurados e são mostrados na galeria do Azure AD. Outros aplicativos não estão na galeria e você precisa criar um aplicativo genérico e configurá-lo manualmente. Dependendo do aplicativo, a opção SSO baseado em senha pode não estar disponível. Se você não vir a lista de opções baseadas em senha na página logon único do aplicativo, ela não estará disponível.

> [!IMPORTANT]
> A extensão do navegador meus aplicativos é necessária para o SSO baseado em senha. Para saber mais, confira [planejar a implantação de meus aplicativos](my-apps-deployment-plan.md).

A página de configuração para o SSO baseado em senha é simples. Ele inclui apenas a URL da página de logon que o aplicativo usa. Essa cadeia de caracteres deve ser a página que inclui o campo de entrada nome de usuário.

Depois de inserir a URL, selecione **salvar**. O Azure AD analisa o HTML da página de entrada para campos de entradas de nome de usuário e senha. Se a tentativa for bem-sucedida, você terminará.
 
A próxima etapa é [atribuir usuários ou grupos ao aplicativo](./assign-user-or-group-access-portal.md). Depois de atribuir usuários e grupos, você pode fornecer credenciais a serem usadas para um usuário quando eles entrarem no aplicativo. Selecione **usuários e grupos**, marque a caixa de seleção da linha do usuário ou do grupo e, em seguida, selecione **Atualizar credenciais**. Por fim, insira o nome de usuário e a senha a serem usados para os usuários ou grupos. Caso contrário, os usuários serão solicitados a inserir as próprias credenciais na inicialização.
 

## <a name="manual-configuration"></a>Configuração manual

Se a tentativa de análise do Azure AD falhar, você poderá configurar o logon manualmente.

1. Em **\<application name> configuração**, selecione **definir \<application name> configurações de logon único com senha** para exibir a página **Configurar logon** . 

2. Selecione **detectar manualmente os campos de entrada**. São exibidas instruções adicionais que descrevem a detecção manual de campos de entrada.

   ![Configuração manual de logon único baseado em senha](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Selecione **capturar campos de entrada**. Uma página de status de captura é aberta em uma nova guia, mostrando que a captura de metadados de mensagem **está em andamento no momento**.

4. Se a caixa a **extensão meus aplicativos necessária** aparecer em uma nova guia, selecione **instalar agora** para instalar a extensão de navegador de **extensão de entrada segura dos meus aplicativos** . (A extensão do navegador requer o Microsoft Edge, o Chrome ou o Firefox.) Em seguida, instale, inicie e habilite a extensão e atualize a página de status de captura.

   A extensão do navegador abre outra guia que exibe a URL inserida.
5. Na guia com a URL inserida, percorra o processo de entrada. Preencha os campos de nome de usuário e senha e tente entrar. (Você não precisa fornecer a senha correta.)

   Um prompt solicita que você salve os campos de entrada capturados.
6. Selecione **OK**. A extensão do navegador atualiza a página de status de captura com os metadados da mensagem **foram atualizados para o aplicativo**. A guia navegador é fechada.

7. Na página de **logon configurar** do Azure AD, selecione **OK, eu consegui entrar no aplicativo com êxito**.

8. Selecione **OK**.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir usuários e grupos ao aplicativo](./assign-user-or-group-access-portal.md)
- [Configurar o provisionamento automático de conta de usuário](../app-provisioning/configure-automatic-user-provisioning-portal.md)
