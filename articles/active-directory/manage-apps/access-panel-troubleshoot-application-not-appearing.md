---
title: Um aplicativo atribuído não aparece no painel de acesso | Microsoft Docs
description: Solucionar problemas por que um aplicativo não aparece no Painel de Acesso
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2018
ms.author: mimart
ms.reviwer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10dfcf337dc75a202e781e931f38783291a72fe7
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272753"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Um aplicativo atribuído não aparece no painel de acesso

O Painel de Acesso é um portal baseado na Web, que permite a um usuário com uma conta corporativa ou de estudante no Azure AD (Azure Active Directory) exibir e iniciar aplicativos baseados em nuvem aos quais o administrador do Azure Active Directory concedeu acesso. Esses aplicativos são configurados em nome do usuário no portal do Azure AD. O aplicativo deve ser configurado corretamente e atribuído ao usuário ou a um grupo no qual o usuário é membro para ver o aplicativo no Painel de Acesso.

Os tipos de aplicativos que um usuário pode ver se enquadram nas categorias a seguir:

-   Aplicativos do Office 365

-   Aplicativos da Microsoft e de terceiros configurados com o SSO baseado em federação

-   Aplicativos de SSO baseadas em senhas

-   Aplicativos com soluções de SSO existentes

## <a name="general-issues-to-check-first"></a>Problemas gerais para verificar primeiro

-   Se um aplicativo acabou de ser adicionado a um usuário, tente entrar e sair novamente no Painel de Acesso do usuário após alguns minutos para ver se o aplicativo foi adicionado.

-   Se uma licença tiver acabado de ser removida de um usuário ou de um grupo de que o usuário faz parte, isso poderá levar um longo período, dependendo do tamanho e da complexidade do grupo. Espere mais algum tempo antes de entrar no Painel de Acesso.

## <a name="problems-related-to-application-configuration"></a>Problemas relacionados à configuração de aplicativo

Um aplicativo pode não aparecer no Painel de Acesso de um usuário porque não está configurado corretamente. Abaixo, são apresentadas algumas maneiras de solucionar problemas relacionados à configuração do aplicativo:

-   [Como configurar o logon único federado para um aplicativo na galeria do Azure AD](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Como configurar o logon único federado para um aplicativo na galeria do Azure AD](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Como configurar um aplicativo de logon único com senha para um aplicativo na galeria do Azure AD](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Como configurar um aplicativo de logon único com senha para um aplicativo inexistente na galeria](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar o logon único federado para um aplicativo na galeria do Azure AD

Todos os aplicativos na galeria do Azure AD habilitados com a capacidade de Logon Único Corporativo têm um tutorial passo a passo disponível. É possível acessar a [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) para uma orientação passo a passo detalhada.

Para configurar um aplicativo da galeria do Azure AD será necessário:

-   [Adicionar um aplicativo da galeria do Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurar os valores de metadados do aplicativo no Azure AD (URL de Logon, Identificador, URL de Resposta)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selecionar o Identificador de Usuário e adicionar os atributos de usuário a serem enviados ao aplicativo](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperar certificado e metadados Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Configurar os valores de metadados do Azure AD no aplicativo (URL de Logon, Emissor, URL de Logoff e certificado)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar um aplicativo da galeria do Azure AD

Para adicionar um aplicativo da galeria do Azure AD, siga as etapas abaixo:

1.  Abra o [Portal do Azure](https://portal.azure.com) e entre como um **Administrador Global** ou **Coadministrador**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  clique no botão **Adicionar** no canto superior direito do painel **Aplicativos Empresariais**.

6.  Na caixa de texto **Inserir um nome** da seção **Adicionar da galeria**, digite o nome do aplicativo.

7.  Selecione o aplicativo para o qual você deseja configurar o logon único.

8.  Antes de adicionar o aplicativo, é possível alterar seu nome pela caixa de texto **Nome**.

9.  Clique no botão **Adicionar** para adicionar o aplicativo.

Após um curto período de tempo, você poderá ver o painel de configuração do aplicativo.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Configurar o logon único para um aplicativo da galeria do Azure AD

Para configurar o logon único para um aplicativo, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Selecione o aplicativo para o qual você deseja configurar o logon único.

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. Selecione **Logon com base em SAML** da lista suspensa **Modo**.

9. Insira os valores necessários em **Domínio e URLs.** É possível obter esses valores do fornecedor do aplicativo.

   1. Para configurar o aplicativo como SSO iniciado por SP, a URL de Logon é um valor obrigatório. Para alguns aplicativos, o Identificador também é um valor obrigatório.

   2. Para configurar o aplicativo como SSO iniciado por IdP, a URL de Resposta é um valor obrigatório. Para alguns aplicativos, o Identificador também é um valor obrigatório.

10. **Opcional:** clique **Mostrar configurações avançadas de URL** se quiser ver os valores não obrigatórios.

11. Nos **Atributos de usuário**, selecione o identificador exclusivo para os usuários na lista suspensa **Identificador de usuário**.

12. **Opcional:** clique em **Exibir e editar todos os outros atributos de usuário** para editar os atributos a serem enviados ao aplicativo no token SAML quando os usuário entrarem.

    Para adicionar um atributo:

    1. clique em **Adicionar atributo**. Insira o **Nome** e selecione o **Valor** da lista suspensa.

    2. clique em **Salvar.** Você verá o novo atributo na tabela.

13. clique em **Configurar &lt;nome do aplicativo&gt;**  para acessar a documentação sobre como configurar o logon único no aplicativo. Além disso, você tem as URLs de metadados e o certificado necessários para configurar o SSO com o aplicativo.

14. clique em **Salvar** para salvar a configuração.

15. Atribuir usuários a um aplicativo.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecionar o Identificador de Usuário e adicionar os atributos de usuário a serem enviados ao aplicativo

Para selecionar o Identificador de Usuário ou adicionar os atributos de usuário, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Selecione o aplicativo para o qual você precisa configurar o logon único.

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. Na seção **Atributos de usuário**, selecione o identificador exclusivo para os usuários na lista suspensa **Identificador de usuário**. A opção selecionada precisa corresponder ao valor esperado no aplicativo para autenticar o usuário.

   >[!NOTE] 
   >O Azure Active Directory seleciona o formato para o atributo NameID (Identificador de Usuário) com base no valor selecionado ou no formato solicitado pelo aplicativo no AuthRequest do SAML. Para obter mais informações, consulte o artigo [Protocolo SAML de Logon Único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na seção NameIDPolicy.
   >
   >

9. Para adicionar atributos de usuário, clique em **Exibir e editar todos os outros atributos de usuário** para editar os atributos a serem enviados ao aplicativo no token SAML quando os usuários entrarem.

   Para adicionar um atributo:

   1. clique em **Adicionar atributo**. Insira o **Nome** e selecione o **Valor** da lista suspensa.

   2. clique em **Salvar.** Você verá o novo atributo na tabela.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Baixar o certificado ou metadados do Azure AD

Para baixar o certificado ou metadados do aplicativo Azure AD, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Selecione o aplicativo para o qual você precisa configurar o logon único.

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. Vá para a seção **Certificado de Autenticação SAML** e, em seguida, clique no valor da coluna **Download**. Dependendo do aplicativo que exigir a configuração de logon único, você verá a opção para baixar o Certificado ou o XML de Metadados.

   O Azure AD não fornece uma URL para obter os metadados. Os metadados apenas podem ser recuperados como um arquivo XML.

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Como configurar o logon único federado para um aplicativo inexistente na galeria

Para configurar um aplicativo inexistente na galeria, é necessário ter o Azure AD Premium e o aplicativo com suporte SAML 2.0. Para obter mais informações sobre as versões do Azure AD, visite [Preços do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Configurar os valores de metadados do aplicativo no Azure AD (URL de Logon, Identificador, URL de Resposta)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selecionar o Identificador de Usuário e adicionar os atributos de usuário a serem enviados ao aplicativo](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperar certificado e metadados Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Configurar os valores de metadados do Azure AD no aplicativo (URL de Logon, Emissor, URL de Logoff e certificado)](#configure-the-application-for-password-single-sign-on-1)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Configurar os valores de metadados do aplicativo no Azure AD (URL de Logon, Identificador, URL de Resposta)

Para configurar o logon único para um aplicativo que não está na galeria do Azure AD, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique no botão **Adicionar** no canto superior direito do painel **Aplicativos Empresariais**.

6. clique em **Aplicativo inexistente na galeria** na seção **Adicionar seu próprio aplicativo**.

7. Digite o nome do aplicativo na caixa de texto **Nome**.

8. Clique no botão **Adicionar** para adicionar o aplicativo.

9. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

10. Selecione **Logon com base em SAML** na lista suspensa **Modo**.

11. Insira os valores necessários em **Domínio e URLs.** É possível obter esses valores do fornecedor do aplicativo.

    1. Para configurar o aplicativo como SSO iniciado por IdP, insira a URL de Resposta e o Identificador.

    2.  **Opcional:** Para configurar o aplicativo como SSO iniciado por SP, a URL de Logon é um valor obrigatório.

12. Nos **Atributos de usuário**, selecione o identificador exclusivo para os usuários na lista suspensa **Identificador de usuário**.

13. **Opcional:** clique em **Exibir e editar todos os outros atributos de usuário** para editar os atributos a serem enviados ao aplicativo no token SAML quando os usuário entrarem.

    Para adicionar um atributo:

    1. clique em **Adicionar atributo**. Insira o **Nome** e selecione o **Valor** da lista suspensa.

    2. Clique em **Salvar.** Você verá o novo atributo na tabela.

14. clique em **Configurar &lt;nome do aplicativo&gt;**  para acessar a documentação sobre como configurar o logon único no aplicativo. Além disso, você tem URLs do Azure Active Directory e certificados necessários para o aplicativo.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecionar o Identificador de Usuário e adicionar os atributos de usuário a serem enviados ao aplicativo

Para selecionar o Identificador de Usuário ou adicionar os atributos de usuário, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Selecione o aplicativo para o qual você precisa configurar o logon único.

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. Na seção **Atributos de usuário**, selecione o identificador exclusivo para os usuários na lista suspensa **Identificador de usuário**. A opção selecionada precisa corresponder ao valor esperado no aplicativo para autenticar o usuário.

   >[!NOTE] 
   >O Azure Active Directory seleciona o formato para o atributo NameID (Identificador de Usuário) com base no valor selecionado ou no formato solicitado pelo aplicativo no AuthRequest do SAML. Para obter mais informações, consulte o artigo [Protocolo SAML de Logon Único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na seção NameIDPolicy.
   >
   >

9. Para adicionar atributos de usuário, clique em **Exibir e editar todos os outros atributos de usuário** para editar os atributos a serem enviados ao aplicativo no token SAML quando os usuários entrarem.

   Para adicionar um atributo:

   1. clique em **Adicionar atributo**. Insira o **Nome** e selecione o **Valor** da lista suspensa.

   2. Clique em **Salvar.** Você verá o novo atributo na tabela.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Baixar o certificado ou metadados do Azure AD

Para baixar o certificado ou metadados do aplicativo Azure AD, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Selecione o aplicativo para o qual você precisa configurar o logon único.

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. Vá para a seção **Certificado de Autenticação SAML** e, em seguida, clique no valor da coluna **Download**. Dependendo do aplicativo que exigir a configuração de logon único, você verá a opção para baixar o Certificado ou o XML de Metadados.

O Azure AD não fornece uma URL para obter os metadados. Os metadados apenas podem ser recuperados como um arquivo XML.

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar o login único com senha para um aplicativo na galeria do Azure AD

Para configurar um aplicativo da galeria do Azure AD será necessário:

-   [Adicionar um aplicativo da galeria do Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurar o aplicativo para logon único com senha](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar um aplicativo da galeria do Azure AD

Para adicionar um aplicativo da galeria do Azure AD, siga as etapas abaixo:

1.  Abra o [Portal do Azure](https://portal.azure.com) e entre como um **Administrador Global** ou **Coadministrador**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  clique no botão **Adicionar** no canto superior direito do painel **Aplicativos Empresariais**.

6.  Na caixa de texto **Inserir um nome** da seção **Adicionar da galeria**, digite o nome do aplicativo.

7.  Selecione o aplicativo para o qual você deseja configurar o logon único.

8.  Antes de adicionar o aplicativo, é possível alterar seu nome pela caixa de texto **Nome**.

9.  Clique no botão **Adicionar** para adicionar o aplicativo.

Após um curto período, você poderá ver o painel de configuração do aplicativo.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar o aplicativo para logon único com senha

Para configurar o logon único para um aplicativo, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Selecione o aplicativo para o qual você deseja configurar o logon único.

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. Selecione o modo **Logon baseado em senha.**

9. [Atribuir usuários ao aplicativo](#how-to-assign-a-user-to-an-application-directly).

10. Além disso, também é possível fornecer credenciais em nome do usuário selecionando as linhas dos usuários, clicando em **Atualizar Credenciais** e digitando o nome de usuário e a senha em nome dos usuários. Caso contrário, os usuários serão solicitados a inserir as próprias credenciais na inicialização.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar o logon único com senha para um aplicativo inexistente na galeria

Para configurar um aplicativo da galeria do Azure AD será necessário:

-   [Adicionar um aplicativo inexistente na galeria](#add-a-non-gallery-application)

-   [Configurar o aplicativo para logon único com senha](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Adicionar um aplicativo inexistente na galeria

Para adicionar um aplicativo da galeria do Azure AD, siga as etapas abaixo:

1.  Abra o [Portal do Azure](https://portal.azure.com) e entre como um **Administrador Global** ou **Coadministrador**.

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  clique no botão **Adicionar** no canto superior direito do painel **Aplicativos Empresariais**.

6.  clique em **Aplicativo inexistente na galeria.**

7.  Insira o nome do aplicativo na caixa de texto **Nome**. Selecione **Adicionar.**

Após um curto período de tempo, você poderá ver o painel de configuração do aplicativo.

#### <a name="configure-the-application-for-password-single-sign-on-1"></a> Configurar o aplicativo para logon único senha

Para configurar o logon único para um aplicativo, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

    1.  Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6.  Selecione o aplicativo para o qual você deseja configurar o logon único.

7.  Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8.  Selecione o modo **Logon baseado em senha.**

9.  Insira a **URL de Logon**. Trata-se da URL em que os usuários inserem o nome de usuário e senha para entrar. Verifique se os campos de entrada estão visíveis na URL.

10. [Atribuir usuários ao aplicativo](#how-to-assign-a-user-to-an-application-directly).

11. Além disso, também é possível fornecer credenciais em nome do usuário selecionando as linhas dos usuários, clicando em **Atualizar Credenciais** e digitando o nome de usuário e a senha em nome dos usuários. Caso contrário, os usuários serão solicitados a inserir as próprias credenciais na inicialização.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemas relacionados à atribuição de aplicativos para usuários

Um usuário pode não ver um aplicativo no seu Painel de Acesso porque o usuário não está atribuído ao aplicativo. Abaixo, são apresentadas algumas maneiras de verificação:

-   [Verificar se um usuário está atribuído ao aplicativo](#check-if-a-user-is-assigned-to-the-application)

-   [Como atribuir um usuário diretamente a um aplicativo](#how-to-assign-a-user-to-an-application-directly)

-   [Verificar se um usuário está atribuído a uma licença relacionada ao aplicativo](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Como atribuir uma licença a um usuário](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Verificar se um usuário está atribuído ao aplicativo

Para verificar se um usuário está atribuído ao aplicativo, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

6. **Pesquise** pelo nome do aplicativo em questão.

7. Clique em **Usuários e grupos**.

8. Verifique se o usuário está atribuído ao aplicativo.

   * Se não, siga as etapas em "Como atribuir um usuário diretamente a um aplicativo" para fazer isso.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Como atribuir um usuário diretamente a um aplicativo

Para atribuir um ou mais usuários diretamente a um aplicativo, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global**.

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Na lista, selecione o aplicativo ao qual deseja atribuir um usuário.

7. Após o carregamento do aplicativo, clique em **Usuários e Grupos** no menu de navegação esquerdo do aplicativo.

8. Clique no botão **Adicionar** na parte superior da lista **Usuários e Grupos** para abrir o painel **Adicionar Atribuição**.

9. Clique no seletor **Usuários e grupos** do painel **Adicionar Atribuição**.

10. Digite o **nome completo** ou o **endereço de email** do usuário que você deseja atribuir na caixa de pesquisa **Pesquisar por nome ou endereço de email**.

11. Passe o mouse sobre o **usuário** na lista para mostrar uma **caixa de seleção**. Clique na caixa de seleção ao lado do logotipo ou da foto de perfil do usuário para adicioná-lo à lista **Selecionado**.

12. **Opcional:** Caso queira **adicionar mais de um usuário**, digite outro **nome completo** ou **endereço de email** na caixa de pesquisa **Pesquisar por nome ou endereço de email** e clique na caixa de seleção para adicionar esse usuário à lista **Selecionado**.

13. Ao concluir a seleção dos usuários, clique no botão **Selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

14. **Opcional:** clique no seletor **Selecionar Função** no painel **Adicionar Atribuição** para selecionar uma função que será atribuída aos usuários selecionados.

15. Clique no botão **Atribuir** para atribuir o aplicativo aos usuários selecionados.

Após um breve período, os usuários selecionados poderão iniciar esses aplicativos no Painel de Acesso.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Verificar se um usuário está sob uma licença relacionada ao aplicativo

Para verificar as licenças atribuídas de um usuário, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. Clique em **Usuários e grupos** no menu de navegação.

5. Clique em **Todos os usuários**.

6. **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7. clique em **Licenças** para ver quais licenças o usuário atribuiu atualmente.

   * Se o usuário for atribuído a uma licença do Office, isso permitirá que os aplicativos primários do Office apareçam no Painel de Acesso do usuário.

### <a name="how-to-assign-a-user-a-license"></a>Como atribuir uma licença a um usuário 

Para atribuir uma licença a um usuário, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Usuários e grupos** no menu de navegação.

5.  Clique em **Todos os usuários**.

6.  **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais licenças o usuário atribuiu atualmente.

8.  clique no botão **Atribuir**.

9.  Selecione **um ou mais produtos** da lista de produtos disponíveis.

10. **Opcional** clique no item **opções de atribuição** para atribuir produtos granularmente. Clique em **OK** quando isso for concluído.

11. Clique no botão **Atribuir** para atribuir essas licenças para esse usuário.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemas relacionados à atribuição de aplicativos para usuários

Um usuário pode estar vendo um aplicativo em seu Painel de Acesso por fazer parte de um grupo ao qual o aplicativo foi atribuído. Abaixo, são apresentadas algumas maneiras de verificação:

-   [Verificar as associações de grupo de um usuário](#check-a-users-group-memberships)

-   [Como atribuir um aplicativo diretamente a um grupo](#how-to-assign-an-application-to-a-group-directly)

-   [Verificar se um usuário faz parte do grupo atribuído a uma licença](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Como atribuir uma licença a um grupo](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Verificar as associações de grupo de um usuário

Para verificar as associações de um grupo, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. Clique em **Usuários e grupos** no menu de navegação.

5. Clique em **Todos os usuários**.

6. **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7. clique em **Grupos**.

8. Verifique se o usuário faz parte de um Grupo atribuído ao aplicativo.

   * Se você quiser remover o usuário do grupo, **clique na linha** do grupo e selecione excluir.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Como atribuir um aplicativo diretamente a um grupo

Para atribuir um ou mais usuários diretamente a um aplicativo, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global**.

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Na lista, selecione o aplicativo ao qual deseja atribuir um usuário.

7. Após o carregamento do aplicativo, clique em **Usuários e Grupos** no menu de navegação esquerdo do aplicativo.

8. Clique no botão **Adicionar** na parte superior da lista **Usuários e Grupos** para abrir o painel **Adicionar Atribuição**.

9. Clique no seletor **Usuários e grupos** do painel **Adicionar Atribuição**.

10. Digite o **nome completo do grupo** que você deseja atribuir na caixa de pesquisa **Pesquisar por nome ou endereço de email**.

11. Passe o mouse sobre o **grupo** na lista para mostrar uma **caixa de seleção**. Clique na caixa de seleção próxima ao logotipo ou ao perfil do grupo para adicionar o usuário na lista **Selecionado**.

12. **Opcional:** Caso queira **adicionar mais de um grupo**, digite outro **nome de grupo completo** na caixa de pesquisa **Pesquisar por nome ou endereço de email** e clique na caixa de seleção para adicionar esse grupo à lista **Selecionado**.

13. Ao concluir a seleção dos grupos, clique no botão **Selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

14. **Opcional:** clique no seletor **Selecionar Função** no painel **Adicionar Atribuição** para selecionar uma função que será atribuída aos grupos selecionados.

15. Clique no botão **Atribuir** para atribuir o aplicativo aos grupos selecionados.

Após um breve período, os usuários selecionados poderão iniciar esses aplicativos no Painel de Acesso.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Verificar se um usuário faz parte do grupo atribuído a uma licença

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. Clique em **Usuários e grupos** no menu de navegação.

5. Clique em **Todos os usuários**.

6. **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7. clique em **Grupos**.

8. clique na linha de um grupo específico.

9. Clique em **Licenças** para ver quais licenças o grupo atribuiu a ele.

   * Se o grupo for atribuído a uma licença do Office, isso poderá permitir que determinados aplicativos primários do Office apareçam no Painel de Acesso do usuário.

### <a name="how-to-assign-a-license-to-a-group"></a>Como atribuir uma licença a um grupo

Para atribuir uma licença a um grupo, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Usuários e grupos** no menu de navegação.

5.  clique em **Todos os grupos**.

6.  **Pesquise** pelo grupo no qual você está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais licenças o grupo atribuiu atualmente.

8.  clique no botão **Atribuir**.

9.  Selecione **um ou mais produtos** da lista de produtos disponíveis.

10. **Opcional** clique no item **opções de atribuição** para atribuir produtos granularmente. Clique em **OK** quando isso for concluído.

11. Clique no botão **Atribuir** para atribuir essas licenças para esse grupo. Isso pode levar algum tempo, dependendo do tamanho e da complexidade do grupo.

>[!NOTE]
>Para fazer isso mais rápido, considere atribuir temporariamente uma licença diretamente ao usuário. 
>
>

## <a name="next-steps"></a>Próximas etapas
[Adicionar novos usuários ao Azure Active Directory](./../fundamentals/add-users-azure-active-directory.md)

