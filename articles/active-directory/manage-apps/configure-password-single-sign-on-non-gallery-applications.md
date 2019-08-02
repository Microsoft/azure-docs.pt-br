---
title: Como configurar o logon único com senha para aplicativos do Azure AD | Microsoft Docs
description: Como configurar o SSO (logon único) com senha para seus aplicativos empresariais do Azure AD na plataforma Microsoft Identity (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: d191abafbaad123ed47f8eaae6cdd4e48478da7a
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422617"
---
# <a name="configure-password-single-sign-on"></a>Configurar o logon único com senha

Quando você [adiciona um aplicativo de galeria](add-gallery-app.md) ou um [aplicativo Web que não é da Galeria](add-non-gallery-app.md) a seus aplicativos empresariais do Azure AD, uma das opções de logon único disponíveis para você é [logon único baseado em senha](what-is-single-sign-on.md#password-based-sso). Essa opção está disponível para qualquer Web com uma página de entrada HTML. O SSO baseado em senha, também conhecido como armazenamento de senha em cofre, permite que você gerencie o acesso de usuários e senhas para aplicativos Web que não dão suporte a federação de identidades. Também é útil para cenários em que vários usuários precisam compartilhar uma única conta, como as contas de aplicativo de mídia social da sua organização. 

O SSO baseado em senha é uma ótima maneira de começar a integrar aplicativos ao Azure AD rapidamente e permite que você:

-   Habilitar o **logon único para os usuários** armazenando de forma segura e reproduzindo os nomes de usuário e senhas do aplicativo integrado ao Azure AD

-   **Dar suporte a aplicativos que exigem vários campos de entrada**, para aplicativos que exigem mais do que apenas os campos de nome de usuário e senha para entrar

-   **Personalizar os rótulos** dos campos de entrada de nome de usuário e senha que seus usuários veem no [Painel de Acesso do Aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) quando digitam suas credenciais

-   Permitir que os **usuários** forneçam seus próprios nomes de usuário e senhas para as contas de aplicativos existentes que eles estiverem digitando manualmente no [Painel de Acesso do Aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Permitir que um **membro do grupo de negócios** especifique os nomes de usuário e senhas atribuídos a um usuário usando o recurso de [Autoatendimento de Acesso ao Aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Permitir que um **administrador** especifique um nome de usuário e uma senha a serem usados por indivíduos ou grupos ao entrar no aplicativo usando o recurso atualizar credenciais 

## <a name="before-you-begin"></a>Antes de começar

Se o aplicativo não tiver sido adicionado ao seu locatário do Azure AD, consulte [Adicionar um aplicativo da Galeria](add-gallery-app.md) ou [Adicionar um aplicativo](add-non-gallery-app.md)inexistente na galeria.

## <a name="open-the-app-and-select-password-single-sign-on"></a>Abra o aplicativo e selecione logon único com senha

1. Entre no [portal do Azure](https://portal.azure.com) como administrador do aplicativo em nuvem ou um administrador de aplicativo para seu locatário do Azure AD.

2. Navegue até **Azure Active Directory** > **aplicativos empresariais**. Uma amostra aleatória dos aplicativos em seu locatário do Azure AD é exibida. 

3. No menu **Tipo de Aplicativo**, selecione **Todos os aplicativos** e, em seguida, **Aplicar**.

4. Insira o nome do aplicativo na caixa de pesquisa e, em seguida, selecione o aplicativo nos resultados.

5. Na seção **Gerenciar**, selecione **Logon único**. 

6. Selecione **baseado em senha**.

7. Insira a URL da página de entrada baseada na Web do aplicativo. Essa cadeia de caracteres deve ser a página que inclui o campo de entrada nome de usuário.

   ![Logon único baseado em senha](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. Clique em **Salvar**. O Azure AD tenta analisar a página de entrada para obter uma entrada de nome de usuário e uma entrada de senha. Se a tentativa for bem-sucedida, você terminará. 
 
> [!NOTE]
> A próxima etapa é [atribuir usuários ou grupos ao aplicativo](methods-for-assigning-users-and-groups.md). Depois de atribuir usuários e grupos, você pode fornecer credenciais a serem usadas em nome de um usuário quando eles entrarem no aplicativo. Selecione **usuários e grupos**, marque a caixa de seleção da linha do usuário ou do grupo e clique em **Atualizar credenciais**. Em seguida, insira o nome de usuário e a senha a serem usados em nome do grupo de usuários ou grupos. Caso contrário, os usuários serão solicitados a inserir as próprias credenciais na inicialização.
 

## <a name="manual-configuration"></a>Configuração manual

Se a tentativa de análise do Azure AD falhar, você poderá configurar o logon manualmente.

1. **Em\<nome do aplicativo > configuração**, **selecione \<configurar nome do aplicativo > senha configurações de logon único** para exibir a página **Configurar logon** . 

2. Selecione **detectar manualmente os campos de entrada**. São exibidas instruções adicionais que descrevem a detecção manual de campos de entrada.

   ![Configuração manual de logon único baseado em senha](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Selecione **capturar campos de entrada**. Uma página de status de captura é aberta em uma nova guia, mostrando que a captura de metadados de mensagem **está em andamento no momento**.

4. Se a caixa **extensão do painel de acesso necessária** aparecer em uma nova guia, selecione **instalar agora** para instalar a extensão do navegador de **extensão de entrada segura dos meus aplicativos** . (A extensão do navegador requer o Microsoft Edge, o Chrome ou o Firefox.) Em seguida, instale, inicie e habilite a extensão e atualize a página de status de captura.

   A extensão do navegador abre outra guia que exibe a URL inserida.
5. Na guia com a URL inserida, percorra o processo de entrada. Preencha os campos de nome de usuário e senha e tente entrar. (Você não precisa fornecer a senha correta.)

   Um prompt solicita que você salve os campos de entrada capturados.
6. Selecione **OK**. A extensão do navegador atualiza a página de status de captura com os metadados da mensagem **foram atualizados para o aplicativo**. A guia navegador é fechada.

7. Na página de **logon configurar** do Azure AD, selecione **OK, eu consegui entrar no aplicativo com êxito**.

8. Selecione **OK**.

Após a captura da página de entrada, você pode atribuir usuários e grupos e pode configurar políticas de credenciais como [aplicativos de SSO de senha](what-is-single-sign-on.md)regular.

> [!NOTE]
> Você pode carregar um logotipo de bloco para o aplicativo usando o botão **Carregar Logotipo** na guia **Configurar** do aplicativo.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir usuários ou grupos ao aplicativo](methods-for-assigning-users-and-groups.md)
- [Configurar o provisionamento automático de conta de usuário](configure-automatic-user-provisioning-portal.md)
