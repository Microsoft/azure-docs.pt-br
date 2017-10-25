---
title: Problemas ao entrar em um aplicativo Microsoft | Microsoft Docs
description: "Solucionar problemas comuns enfrentados ao entrar em aplicativos primários da Microsoft usando o Azure AD (como o Office 365)"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 5638434270ee82d2b9737ea8eed8b5a8c62f7121
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
## <a name="problems-signing-in-to-a-microsoft-application"></a>Problemas ao entrar em um aplicativo Microsoft

Os aplicativos Microsoft (como o Office 365 Exchange, SharePoint, Yammer, etc.) são atribuídos e gerenciados de forma um pouco diferente dos aplicativos SaaS de terceiros ou outros aplicativos que você integra com o Azure AD para o logon único.

Há três principais maneiras que um usuário pode obter acesso a um aplicativo publicado Microsoft.

-   Para aplicativos no Office 365 ou em outros conjuntos de aplicativos pagos, os usuários têm acesso através de **atribuição de licença** diretamente à sua conta de usuário ou através de um grupo que usa nosso recurso de atribuição de licenças baseada em grupo.

-   Para aplicativos que a Microsoft ou terceiros publicam livremente para uso de qualquer pessoa, os usuários podem ter o acesso concedido através do **consentimento do usuário**. Isso significa que o usuário se conecte ao aplicativo usando a conta de Estudante ou do Azure AD Work e permite acesso a um conjunto limitado de dados em sua conta.

-   Para aplicativos que a Microsoft ou terceiros publicam livremente para uso de qualquer pessoa, os usuários também podem ter o acesso concedido através do **consentimento do administrador**. Isso significa que um administrador determinou que o aplicativo pode ser usado por todos na organização, portanto, entram no aplicativo com uma conta de Administrador Global e concedem acesso a todos na organização.

Para solucionar o problema, inicie com as [Áreas com Problemas Gerais com o Acesso do Aplicativo a considerar](#general-problem-areas-with-application-access-to-consider) e leia o [Passo a passo: Etapas para solucionar problemas de acesso ao aplicativo Microsoft](#walkthrough-steps-to-troubleshoot-microsoft-application-access) para obter mais detalhes.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Áreas com Problemas Gerais com o Acesso do Aplicativo a considerar

Abaixo segue uma lista das áreas com problemas gerais que você pode analisar se tiver uma ideia de onde iniciar, mas é recomendável que leia o passo a passo para começar rapidamente: [Passo a passo: Etapas para solucionar problemas de acesso ao aplicativo Microsoft](#walkthrough-steps-to-troubleshoot-microsoft-application-access).

-   [Problemas com a conta do usuário](#problems-with-the-users-account)

-   [Problemas com grupos](#problems-with-groups)

-   [Problemas com políticas de acesso condicional](#problems-with-conditional-access-policies)

-   [Problemas com consentimento do aplicativo](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Solucionar problemas de acesso do aplicativo Microsoft

A seguir, são apresentados alguns problemas comuns que as pessoas se deparam quando seus usuários não podem entram em um aplicativo Microsoft.

-   Problemas gerais a serem primeiramente verificados

  * Certifique-se de que o usuário está entrando na **URL correta** e não em uma URL de aplicativo local.

  * Certifique-se de que a conta do usuário **não está bloqueada.**

  * Certifique-se de que a**conta do usuário existe** no Azure Active Directory. [Verificar se uma conta de usuário existe no Azure Active Directory](#problems-with-the-users-account)

  * Verifique se a conta do usuário está **habilitada** para entradas. [Verificar o status da conta do usuário](#problems-with-the-users-account)

  * Verifique se a **senha do usuário não expirou ou foi esquecida.** [Redefinir uma senha do usuário](#reset-a-users-password) ou [Habilitar a redefinição de senha por autoatendimento](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

   * Certifique-se de que a **Autenticação Multifator** não está bloqueando o acesso do usuário. [Verificar o status de autenticação multifator do usuário](#check-a-users-multi-factor-authentication-status) ou [Verificar informações de contato de autenticação do usuário](#check-a-users-authentication-contact-info)

   * Certifique-se de que uma **Política de Acesso Condicional** ou política de **Proteção de Identidade** não está bloqueando o acesso do usuário. [Verificar uma política específica de acesso condicional ](#problems-with-conditional-access-policies) ou [Verificar uma política específica de acesso condicional do aplicativo](#check-a-specific-applications-conditional-access-policy) ou [Desabilitar uma política específica de acesso condicional ](#disable-a-specific-conditional-access-policy)

   * Certifique-se de que as **informações de contato de autenticação** de um usuário estão atualizadas para permitir que as políticas de Acesso Condicional ou Autenticação Multifator sejam impostas. [Verificar o status de autenticação multifator do usuário](#check-a-users-multi-factor-authentication-status) ou [Verificar informações de contato de autenticação do usuário](#check-a-users-authentication-contact-info)

-   Para aplicativos **Microsoft** **que exigem uma licença** (como o Office365), aqui estão alguns problemas específicos para verificar após ter excluído os problemas gerais acima:

   * Verifique se o usuário ou tem um **licença atribuída.** [Verificar as licenças atribuídas de um usuário](#check-a-users-assigned-licenses) ou [Verificar licenças atribuídas de um grupo](#check-a-groups-assigned-licenses)

   * Se a licença estiver **atribuída a um** **grupo estático**, certifique-se de que o **usuário é um membro** desse grupo. [Verificar as associações de grupo de um usuário](#check-a-users-group-memberships)

   * Se a licença estiver **atribuída a um** **grupo dinâmico**, certifique-se de que o **regra de grupo dinâmico está definida corretamente**. [Verificar os critérios de associação do grupo dinâmico](#check-a-dynamic-groups-membership-criteria)

   * Se a licença estiver **atribuída a um** **grupo dinâmico**, verifique se o grupo dinâmico **terminou de processar** sua associação e se o **usuário é um membro** (isso pode demorar algum tempo). [Verificar as associações de grupo de um usuário](#check-a-users-group-memberships)

   *  Após certificar-se de que a licença está atribuída, certifique-se de que a licença **não expirou**.

   *  Certifique-se de que a licença é **para o aplicativo** que será acessado.

-   Para aplicativos **Microsoft** **que não exigem uma licença**, a seguir são apresentadas algumas outras opções a verificar:

   * Se o aplicativo estiver solicitando **permissões em nível de usuário** (por exemplo "Acesso à caixa de correio do usuário"), certifique-se de que o usuário entrou no aplicativo e executou uma **operação de consentimento de nível de usuário** para permitir que o aplicativo acesse seus dados.

   * Se o aplicativo estriver solicitando **permissões de administrador** (por exemplo "Acesso a caixas de correio de todos os usuários"), certifique-se de que um Administrador Global executou uma **operação de consentimento de nível de administrador em nome de todos os usuários** na organização.

## <a name="problems-with-the-users-account"></a>Problemas com a conta do usuário

O acesso do aplicativo pode ser bloqueado devido a um problema com um usuário atribuído ao aplicativo. Abaixo são apresentadas algumas maneiras para solucionar problemas e resolver problemas com usuários e suas configurações de conta:

-   [Verificar se existe uma conta de usuário no Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Verificar o status da conta do usuário](#check-a-users-account-status)

-   [Redefinir a senha de um usuário](#reset-a-users-password)

-   [Habilitar a redefinição de senha por autoatendimento](#enable-self-service-password-reset)

-   [Verificar o status da Autenticação Multifator de um usuário](#check-a-users-multi-factor-authentication-status)

-   [Verificar as informações de contato de autenticação de um usuário](#check-a-users-authentication-contact-info)

-   [Verificar as associações de grupo de um usuário](#check-a-users-group-memberships)

-   [Verificar as licenças atribuídas de um usuário](#check-a-users-assigned-licenses)

-   [Atribuir uma licença a um usuário](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Verificar se existe uma conta de usuário no Azure Active Directory

Para verificar se há uma conta de usuário presente, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory**" na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Usuários e grupos** no menu de navegação.

5.  Clique em **Todos os usuários**.

6.  **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7.  Verifique as propriedades do objeto do usuário para ter certeza de que elas estejam definidas conforme o esperado e de que nenhum dado esteja faltando.

### <a name="check-a-users-account-status"></a>Verificar o status da conta do usuário

Para verificar o status da conta de um usuário, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory**" na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Usuários e grupos** no menu de navegação.

5.  Clique em **Todos os usuários**.

6.  **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7.  Clique em **Perfil**.

8.  Em **Configurações** verifique se **Bloquear entrada** está definido como **Não**.

### <a name="reset-a-users-password"></a>Redefinir a senha de um usuário

Para redefinir a senha de um usuário, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory**" na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Usuários e grupos** no menu de navegação.

5.  Clique em **Todos os usuários**.

6.  **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7.  Clique no botão **Redefinir senha** na parte superior da folha do usuário.

8.  Clique no botão **Redefinir senha** na folha **Redefinir senha** que aparece.

9.  Copie a **senha temporária** ou **insira uma nova senha** para o usuário.

10. Informe essa nova senha para o usuário, e que ele precisa alterar essa senha durante o próximo logon no Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Habilitar a redefinição de senha por autoatendimento

Para habilitar a redefinição de senhas por autoatendimento, execute as etapas de implantação abaixo:

-   [Permitir que os usuários redefinam suas senhas do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-their-azure-ad-passwords)

-   [Permitir que os usuários redefinam ou alterem suas senhas locais do Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords)

### <a name="check-a-users-multi-factor-authentication-status"></a>Verificar o status da Autenticação Multifator de um usuário

Para verificar o status da Autenticação Multifator de um usuário, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory**" na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Usuários e grupos** no menu de navegação.

5.  Clique em **Todos os usuários**.

6.  Clique no botão **Autenticação Multifator** na parte inferior da folha.

7.  Após o carregamento do **Portal de Administração da Autenticação Multifator**, verifique se você está na guia **Usuários**.

8.  Encontre o usuário na lista de usuários pesquisando, filtrando ou classificando.

9.  Selecione o usuário na lista de usuários e **Habilite**, **Desabilite** ou **Imponha** a autenticação multifator conforme o desejado.

  * **Observação**: Se um usuário estiver em estado **Imposto** defina-o temporariamente como **Desabilitado** para permitir que volte à sua conta. Quando ele puder entrar novamente, altere novamente o estado para **Habilitado** para exigir o novo registro de suas informações de contato durante o próximo logon. Como alternativa, execute as etapas em [Verificar as informações de contato de autenticação do usuário](#check-a-users-authentication-contact-info) para verificar ou definir esses dados para eles.

### <a name="check-a-users-authentication-contact-info"></a>Verificar as informações de contato de autenticação de um usuário

Para verificar as informações de contato de autenticação do usuário usadas para Autenticação Multifator, Acesso Condicional, Proteção de Identidade e Redefinição de Senha, execute as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory**" na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Usuários e grupos** no menu de navegação.

5.  Clique em **Todos os usuários**.

6.  **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7.  Clique em **Perfil**.

8.  Role a tela para baixo até **Informações de contato de autenticação**.

9.  **Revise** os dados registrados para o usuário e a atualização conforme o necessário.

### <a name="check-a-users-group-memberships"></a>Verificar as associações de grupo de um usuário

Para verificar as associações de grupo de um usuário, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory**" na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Usuários e grupos** no menu de navegação.

5.  Clique em **Todos os usuários**.

6.  **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7.  Clique em **Grupos** para ver de quais grupos o usuário é membro.

### <a name="check-a-users-assigned-licenses"></a>Verificar as licenças atribuídas de um usuário

Para verificar as licenças atribuídas de um usuário, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory**" na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Usuários e grupos** no menu de navegação.

5.  Clique em **Todos os usuários**.

6.  **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais licenças o usuário atribuiu atualmente.

### <a name="assign-a-user-a-license"></a>Atribuir uma licença a um usuário 

Para atribuir uma licença a um usuário, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory**" na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Usuários e grupos** no menu de navegação.

5.  Clique em **Todos os usuários**.

6.  **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais licenças o usuário atribuiu atualmente.

8.  clique no botão **Atribuir**.

9.  Selecione **um ou mais produtos** da lista de produtos disponíveis.

10. **Opcional** clique no item **opções de atribuição** para atribuir produtos granularmente. Clique em **OK** quando isso for concluído.

11. Clique no botão **Atribuir** para atribuir essas licenças para esse usuário.

## <a name="problems-with-groups"></a>Problemas com grupos

O acesso a aplicativos pode ser bloqueado devido a um problema com um grupo atribuído ao aplicativo. Abaixo são apresentadas algumas maneiras para solucionar problemas com grupos e associações de grupo:

-   [Verificar associações de um grupo](#check-a-groups-membership)

-   [Verificar os critérios de associação do grupo dinâmico](#check-a-dynamic-groups-membership-criteria)

-   [Verificar as licenças atribuídas de um usuário](#check-a-groups-assigned-licenses)

-   [Reprocessar licenças do um grupo](#reprocess-a-groups-licenses)

-   [Atribuir uma licença a um grupo](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Verificar associações de um grupo

Para verificar as associações de um grupo, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory**" na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Usuários e grupos** no menu de navegação.

5.  clique em **Todos os grupos**.

6.  **Pesquise** pelo grupo no qual você está interessado e **clique na linha** para selecionar.

7.  clique em **Membros** para examinar a lista de usuários atribuídos a esse grupo.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Verificar os critérios de associação do grupo dinâmico 

Para verificar as associações de grupo dinâmico, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory**" na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Usuários e grupos** no menu de navegação.

5.  clique em **Todos os grupos**.

6.  **Pesquise** pelo grupo no qual você está interessado e **clique na linha** para selecionar.

7.  clique em **Regras de associação dinâmica.**

8.  Analise a regra **simples** ou **avançada** definida para esse grupo e certifique-se de que o usuário que pretende ser membro deste grupo atende as esses critérios.

### <a name="check-a-groups-assigned-licenses"></a>Verificar as licenças atribuídas de um usuário

Para verificar as licenças atribuídas de um usuário, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory**" na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Usuários e grupos** no menu de navegação.

5.  clique em **Todos os grupos**.

6.  **Pesquise** pelo grupo no qual você está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais licenças o grupo atribuiu atualmente.

### <a name="reprocess-a-groups-licenses"></a>Reprocessar licenças do um grupo

Para reprocessar as licenças atribuídas de um usuário, execute as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory**" na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Usuários e grupos** no menu de navegação.

5.  clique em **Todos os grupos**.

6.  **Pesquise** pelo grupo no qual você está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais licenças o grupo atribuiu atualmente.

8.  clique no botão **Reprocessar** para garantir que as licenças atribuídas a membros desse grupo sejam atualizadas. Isso pode levar algum tempo, dependendo do tamanho e da complexidade do grupo.

   >[!NOTE]
   >Para fazer isso mais rápido, considere atribuir temporariamente uma licença diretamente ao usuário. [Atribua uma licença a um usuário](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Atribuir uma licença a um grupo

Para atribuir uma licença a um grupo, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory**" na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Usuários e grupos** no menu de navegação.

5.  clique em **Todos os grupos**.

6.  **Pesquise** pelo grupo no qual você está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais licenças o grupo atribuiu atualmente.

8.  clique no botão **Atribuir**.

9.  Selecione **um ou mais produtos** da lista de produtos disponíveis.

10. **Opcional** clique no item **opções de atribuição** para atribuir produtos granularmente. Clique em **OK** quando isso for concluído.

11. Clique no botão **Atribuir** para atribuir essas licenças para esse grupo. Isso pode levar algum tempo, dependendo do tamanho e da complexidade do grupo.

   >[!NOTE]
   >Para fazer isso mais rápido, considere atribuir temporariamente uma licença diretamente ao usuário. [Atribua uma licença a um usuário](#problems-with-application-consent).
   > 
   >

## <a name="problems-with-conditional-access-policies"></a>Problemas com políticas de acesso condicional

### <a name="check-a-specific-conditional-access-policy"></a>Verificar uma política específica de acesso condicional

Para verificar ou validar uma política de acesso condicional única:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory**" na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos empresariais** no menu de navegação.

5.  clique no item de navegação **Acesso condicional**.

6.  clique na política que você pretende inspecionar.

7.  Analise se não há condições, atribuições ou outras configurações específicas que podem estar bloqueando o acesso do usuário.

   >[!NOTE]
   >Talvez você queira desabilitar temporariamente essa política para garantir que ela não está afetando entradas. Para fazer isso, defina a alternância **Habilitar política** para **Não** e clique no botão **Salvar**.
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Verificar uma política específica de acesso condicional do aplicativo

Para verificar ou validar uma política de acesso condicional configurada atualmente de aplicativo único:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory**" na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos empresariais** no menu de navegação.

5.  clique em **Todos os aplicativos**.

6.  Procure o aplicativo de seu interesse, o usuário que está tentando entrar pelo nome de exibição do aplicativo ou ID do aplicativo.

     >[!NOTE]
     >Se você não encontrar o aplicativo que está procurando, clique no botão **Filtrar** e expanda o escopo da lista para **Todos os aplicativos**. Se você quiser ver mais colunas, clique no botão **Colunas** para adicionar detalhes complementares a seus aplicativos.
     >
     >

7.  clique no item de navegação **Acesso condicional**.

8.  clique na política que você pretende inspecionar.

9.  Analise se não há condições, atribuições ou outras configurações específicas que podem estar bloqueando o acesso do usuário.

     >[!NOTE]
     >Talvez você queira desabilitar temporariamente essa política para garantir que ela não está afetando entradas. Para fazer isso, defina a alternância **Habilitar política** para **Não** e clique no botão **Salvar**.
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Desabilitar uma política específica de acesso condicional

Para verificar ou validar uma política de acesso condicional única:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory**" na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos empresariais** no menu de navegação.

5.  clique no item de navegação **Acesso condicional**.

6.  clique na política que você pretende inspecionar.

7.  Desabilite a política, configurando a alternância **Habilitar política** para **Não** e clique no botão **Salvar**.

## <a name="problems-with-application-consent"></a>Problemas com consentimento do aplicativo

O acesso do aplicativo pode ser bloqueado porque a operação de consentimento de permissões apropriadas não ocorreu. Abaixo, são apresentadas algumas maneiras de solucionar problemas e resolver questões relacionados ao consentimento do aplicativo:

-   [Executar uma operação de consentimento de nível de usuário](#perform-a-user-level-consent-operation)

-   [Executar operação de consentimento de nível de administrador para qualquer aplicativo](#perform-administrator-level-consent-operation-for-any-application)

-   [Executar consentimento de nível de administrador para um aplicativo de locatário único](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Executar consentimento de nível de administrador para um aplicativo multilocatário](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Executar uma operação de consentimento de nível de usuário

-   Para qualquer aplicativo habilitado para Open ID Connect que solicita permissões, navegar até a tela de entrada do aplicativo executa um consentimento de nível de usuário para o aplicativo do usuário conectado.

-   Se você quiser fazer isso programaticamente, consulte [Solicitando consentimento do usuário individual](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Executar operação de consentimento de nível de administrador para qualquer aplicativo

-   Somente **para aplicativos desenvolvidos usando o modelo de aplicativo V1**, você pode forçar que esse consentimento de nível de administrador ocorra, adicionando “**?prompt=admin\_consent**” ao final da URL de entrada do aplicativo.

-   Para **qualquer aplicativo desenvolvido usando o modelo de aplicativo V2**, você pode impor que esse consentimento de nível de administrador ocorra, seguindo as instruções na seção **Solicitar permissões de um administrador de diretório** de [Usando o ponto de extremidade de consentimento do administrador](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Executar consentimento de nível de administrador para um aplicativo de locatário único

-   Para **aplicativos de locatário único** que solicitam permissões (como os em desenvolvimento ou próprios da organização), é possível executar uma operação de **consentimento de nível administrativo** em nome de todos os usuários, entrando como um Administrador Global e clicando no botão **Conceder permissões** na parte superior da folha **Registro de Aplicativo -&gt; Todos os Aplicativos -&gt; Selecionar um Aplicativo -&gt; Permissões Necessárias**.

-   Para **qualquer aplicativo desenvolvido usando o modelo de aplicativo V1 ou V2**, você pode impor que esse consentimento de nível de administrador ocorra, seguindo as instruções na seção **Solicitar permissões de um administrador de diretório** de [Usando o ponto de extremidade de consentimento do administrador](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Executar consentimento de nível de administrador para um aplicativo multilocatário

-   Para **Aplicativos de multilocatário** que solicitar permissões (como um aplicativo de terceiros ou desenvolvido pela Microsoft), você pode executar uma operação de **consentimento de nível administrativo**. Entre como um Administrador Global e clique no botão **Conceder permissões** na folha **Aplicativos Empresariais -&gt; Todos os Aplicativos -&gt; Selecionar um Aplicativo -&gt; Permissões** (disponível em breve).

-   É possível impor que esse consentimento de nível de administrador ocorra, seguindo as instruções na seção **Solicitar permissões de um administrador de diretório** de [Usando o ponto de extremidade de consentimento do administrado](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="next-steps"></a>Próximas etapas
[Usando o ponto de extremidade de consentimento do administrador](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

