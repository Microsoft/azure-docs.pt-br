---
title: Solucionar problemas ao entrar em um aplicativo do Azure AD meus aplicativos
description: Solucionar problemas ao entrar em um aplicativo do Azure AD meus aplicativos
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: a54c7d6cc4ccf1d9f42702be030598ad1edfab24
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225142"
---
# <a name="troubleshoot-problems-signing-in-to-an-application-from-azure-ad-my-apps"></a>Solucionar problemas ao entrar em um aplicativo do Azure AD meus aplicativos

Meus aplicativos é um portal baseado na Web que permite a um usuário com uma conta corporativa ou de estudante no Azure Active Directory (AD do Azure) exibir e iniciar aplicativos baseados em nuvem aos quais o administrador do Azure AD concedeu acesso. Meus aplicativos são acessados usando um navegador da Web em [https://myapps.microsoft.com](https://myapps.microsoft.com) .

Para saber mais sobre como usar o Azure AD como um provedor de identidade para um aplicativo, consulte o [que é gerenciamento de aplicativos no Azure ad](what-is-application-management.md). Para começar a trabalhar rapidamente, confira a [série de guias de início rápido sobre o gerenciamento de aplicativos](view-applications-portal.md).

Esses aplicativos são configurados em nome do usuário no portal do Azure AD. O aplicativo deve ser configurado corretamente e atribuído ao usuário ou a um grupo do qual o usuário é membro para ver o aplicativo em meus aplicativos. 

Os tipos de aplicativos que um usuário pode ver se enquadram nas categorias a seguir:
-   Microsoft 365 aplicativos
-   Aplicativos da Microsoft e de terceiros configurados com o SSO baseado em federação
-   Aplicativos de SSO baseadas em senhas
-   Aplicativos com soluções de SSO existentes

Aqui estão algumas coisas para verificar se um aplicativo está aparecendo ou não aparecendo.
- Certifique-se de que o aplicativo seja adicionado ao Azure AD e verifique se o usuário está atribuído. Para saber mais, confira a [série de guias de início rápido sobre gerenciamento de aplicativos](add-application-portal.md).
- Se um aplicativo foi adicionado recentemente, faça com que o usuário saia e entre novamente. 
- Se o aplicativo exigir uma licença, como o Office, verifique se o usuário está atribuído à licença apropriada.
- O tempo necessário para as alterações de licenciamento pode variar dependendo do tamanho e da complexidade do grupo.

## <a name="general-issues-to-check-first"></a>Problemas gerais para verificar primeiro

-   Verifique se o navegador da Web atende aos requisitos, consulte [meus aplicativos com suporte nos navegadores](../user-help/my-apps-portal-end-user-access.md).
-   Verifique se o navegador do usuário adicionou a URL do aplicativo ao seus **sites confiáveis**.
-   Verifique se o aplicativo está **configurado** corretamente.
-   Certifique-se de que a conta do usuário está **habilitada** para entradas.
-   Verifique se a conta do usuário **não está bloqueada.**
-   Verifique se a **senha do usuário não expirou ou foi esquecida.**
-   Verifique se a **Autenticação Multifator** não está bloqueando o acesso do usuário.
-   Verifique se uma **Política de acesso condicional** ou política de **Proteção de Identidade** não está bloqueando o acesso do usuário.
-   Verifique se as **informações de contato de autenticação** de um usuário estão atualizadas para permitir a aplicação da Autenticação Multifator ou de políticas de Acesso Condicional.
-   Tente também eliminar os cookies do navegador e tente entrar novamente.

## <a name="problems-with-the-users-account"></a>Problemas com a conta do usuário
O acesso aos meus aplicativos pode ser bloqueado devido a um problema com a conta do usuário. Seguem-se algumas maneiras de solucionar e resolver problemas com os usuários e as configurações de sua conta:
-   [Verificar se existe uma conta de usuário no Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)
-   [Verificar o status da conta do usuário](#check-a-users-account-status)
-   [Redefinir a senha de um usuário](#reset-a-users-password)
-   [Habilitar a redefinição de senha por autoatendimento](#enable-self-service-password-reset)
-   [Verificar o status da Autenticação Multifator de um usuário](#check-a-users-multi-factor-authentication-status)
-   [Verificar as informações de contato de autenticação de um usuário](#check-a-users-authentication-contact-info)
-   [Verificar as associações de grupo de um usuário](#check-a-users-group-memberships)
-   [Verificar se um usuário tem mais de 999 atribuições de função de aplicativo](#check-if-a-user-has-more-than-999-app-role-assignments)
-   [Verificar as licenças atribuídas de um usuário](#check-a-users-assigned-licenses)
-   [Atribuir uma licença a um usuário](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Verificar se existe uma conta de usuário no Azure Active Directory
Para verificar se a conta de um usuário está presente, siga estas etapas:
1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**
2.  Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.
3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.
4.  Selecione **usuários e grupos** no menu de navegação.
5.  Selecione **Todos os usuários**.
6.  **Procure** o usuário em que você está interessado e **Selecione a linha** a ser selecionada.
7.  Verifique as propriedades do objeto do usuário para ter certeza de que elas estejam definidas conforme o esperado e de que nenhum dado esteja faltando.

### <a name="check-a-users-account-status"></a>Verificar o status da conta do usuário
Para verificar o status da conta de um usuário, siga estas etapas:
1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**
2.  Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.
3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.
4.  Selecione **usuários e grupos** no menu de navegação.
5.  Selecione **Todos os usuários**.
6.  **Procure** o usuário em que você está interessado e **Selecione a linha** a ser selecionada.
7.  Selecione **perfil**.
8.  Em **Configurações** verifique se **Bloquear entrada** está definido como **Não**.

### <a name="reset-a-users-password"></a>Redefinir a senha de um usuário
Para redefinir a senha de um usuário, siga estas etapas:
1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**
2.  Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.
3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.
4.  Selecione **usuários e grupos** no menu de navegação.
5.  Selecione **Todos os usuários**.
6.  **Procure** o usuário em que você está interessado e **Selecione a linha** a ser selecionada.
7.  Selecione o botão **Redefinir senha** na parte superior do painel do usuário.
8.  Selecione o botão **Redefinir senha** no painel **Redefinir senha** que aparece.
9.  Copie a **senha temporária** ou **insira uma nova senha** para o usuário.
10. Informe essa nova senha para o usuário, e que ele precisa alterar essa senha durante o próximo logon no Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Habilitar a redefinição de senha por autoatendimento
Para habilitar a redefinição de senha por autoatendimento, siga estas etapas de implantação:
-   [Permitir que os usuários redefinam suas senhas do Azure Active Directory](../authentication/tutorial-enable-sspr.md)
-   [Permitir que os usuários redefinam ou alterem suas senhas locais do Active Directory](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>Verificar o status da Autenticação Multifator de um usuário
Para verificar o status da Autenticação Multifator de um usuário, siga estas etapas:
1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**
2. Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.
3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.
4. Selecione **usuários e grupos** no menu de navegação.
5. Selecione **Todos os usuários**.
6. Selecione o botão **autenticação multifator** na parte superior do painel.
7. Depois que o **portal de administração da autenticação multifator** carregar, verifique se você está na guia **usuários** .
8. Encontre o usuário na lista de usuários pesquisando, filtrando ou classificando.
9. Selecione o usuário na lista de usuários e **Habilite**, **Desabilite** ou **Imponha** a autenticação multifator conforme o desejado.
   >[!NOTE]
   >Se um usuário estiver em um estado **Imposto**, defina-o temporariamente como **Desabilitado** para deixá-lo entrar novamente na conta. Quando ele puder entrar novamente, altere novamente o estado para **Habilitado** para exigir o novo registro de suas informações de contato durante a próxima entrada. Como alternativa, execute as etapas em [Verificar as informações de contato de autenticação do usuário](#check-a-users-authentication-contact-info) para verificar ou definir esses dados para eles.

### <a name="check-a-users-authentication-contact-info"></a>Verificar as informações de contato de autenticação de um usuário
Para verificar as informações de contato de autenticação do usuário usadas para Autenticação Multifator, Acesso Condicional, Proteção de Identidade e Redefinição de Senha, siga estas etapas:
1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**
2.  Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.
3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.
4.  Selecione **usuários e grupos** no menu de navegação.
5.  Selecione **Todos os usuários**.
6.  **Procure** o usuário em que você está interessado e **Selecione a linha** a ser selecionada.
7.  Selecione **perfil**.
8.  Role a tela para baixo até **Informações de contato de autenticação**.
9.  **Revise** os dados registrados para o usuário e a atualização conforme o necessário.

### <a name="check-a-users-group-memberships"></a>Verificar as associações de grupo de um usuário
Para verificar as associações de grupo de um usuário, siga estas etapas:
1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**
2.  Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.
3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.
4.  Selecione **usuários e grupos** no menu de navegação.
5.  Selecione **Todos os usuários**.
6.  **Procure** o usuário em que você está interessado e **Selecione a linha** a ser selecionada.
7.  Selecione **grupos** para ver de quais grupos o usuário é membro.

### <a name="check-if-a-user-has-more-than-999-app-role-assignments"></a>Verificar se um usuário tem mais de 999 atribuições de função de aplicativo
Se um usuário tiver mais de 999 atribuições de função de aplicativo, eles não poderão ver todos os seus aplicativos em meus aplicativos.

Isso ocorre porque meus aplicativos atualmente lêem até 999 atribuições de função de aplicativo para determinar os aplicativos aos quais os usuários são atribuídos. Se um usuário for atribuído a mais de 999 aplicativos, não será possível controlar quais desses aplicativos serão mostrados no portal meus aplicativos.

Para verificar se um usuário tem mais de 999 atribuições de função de aplicativo, siga estas etapas:
1. Instale o módulo [**Microsoft. Graph**](https://github.com/microsoftgraph/msgraph-sdk-powershell) PowerShell.
2. Execute `Connect-MgGraph -Scopes "User.ReadBasic.All Application.Read.All"`.
3. Execute `(Get-MgUserAppRoleAssignment -UserId "<user-id>" -PageSize 999).Count` para determinar o número de atribuições de função de aplicativo que o usuário concedeu atualmente.
4. Se o resultado for 999, o usuário provavelmente terá mais de 999 atribuições de funções de aplicativo.

### <a name="check-a-users-assigned-licenses"></a>Verificar as licenças atribuídas de um usuário
Para verificar as licenças atribuídas de um usuário, siga estas etapas:
1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**
2.  Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.
3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.
4.  Selecione **usuários e grupos** no menu de navegação.
5.  Selecione **Todos os usuários**.
6.  **Procure** o usuário em que você está interessado e **Selecione a linha** a ser selecionada.
7.  Selecione **licenças** para ver quais licenças o usuário atribuiu atualmente.

### <a name="assign-a-user-a-license"></a>Atribuir uma licença a um usuário 
Para atribuir uma licença a um usuário, siga estas etapas:
1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**
2.  Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.
3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.
4.  Selecione **usuários e grupos** no menu de navegação.
5.  Selecione **Todos os usuários**.
6.  **Procure** o usuário em que você está interessado e **Selecione a linha** a ser selecionada.
7.  Selecione **licenças** para ver quais licenças o usuário atribuiu atualmente.
8.  Selecione o botão **atribuir** .
9.  Selecione **um ou mais produtos** da lista de produtos disponíveis.
10. **Opcional** selecione o item **Opções de atribuição** para atribuir produtos de maneira granular. Selecione **OK**.
11. Selecione o botão **atribuir** para atribuir essas licenças a este usuário.

## <a name="troubleshooting-deep-links"></a>Solucionando problemas de links profundos
DeepLinks ou URLs de acesso do Usuário são links que seus usuários podem usar para acessar seus aplicativos SSO de senha diretamente de suas barras de URLs de navegadores. Ao navegar até esse link, os usuários são automaticamente conectados ao aplicativo sem precisar ir primeiro para meus aplicativos. O link é o mesmo que os usuários usam para acessar esses aplicativos por meio do iniciador de aplicativo Microsoft 365.

### <a name="checking-the-deep-link"></a>Verificando o link profundo

Para verificar se você tem o link profundo correto, siga estas etapas:
1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**
2. Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.
3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.
4. Escolha **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.
5. Marque **Todos os aplicativos** para exibir uma lista com todos os seus aplicativos.
   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**
6. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**
7. Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.
8. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.
9. Escolha **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.
10. Marque **Todos os aplicativos** para exibir uma lista com todos os seus aplicativos.
    * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**
11. Selecione o aplicativo para o qual você deseja verificar o link profundo.
12. Localize o rótulo **URL de acesso do Usuário**. Seu link profundo deve corresponder a esta URL.

## <a name="contact-support"></a>Contate o suporte
Abra um tíquete de suporte com as seguintes informações, se disponíveis:
-   ID de erro de correlação
-   UPN (endereço de email de usuário)
-   TenantID
-   Tipo de navegador
-   Fuso horário e hora/cronograma durante o erro
-   Rastreamentos do Fiddler

## <a name="next-steps"></a>Próximas etapas
- [Série de Início Rápido no Gerenciamento de Aplicativos](view-applications-portal.md)
