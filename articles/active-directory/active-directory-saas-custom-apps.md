---
title: Configurar o SSO do Azure AD para aplicativos | Microsoft Docs
description: "Saiba como conectar aplicativos ao Active Directory do Azure por conta própria usando SAML e SSO baseado em senha"
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: femila
ms.assetid: 0d42eb0c-6d3f-4557-9030-e88e86709a19
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: asmalser
ms.reviewer: luleon
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9049f526243cb4659aaf86b3d31146abe8f5f3ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Configurando logon único para aplicativos que não estão na galeria de aplicativo do Active Directory do Azure
Este artigo é sobre um recurso que permite aos administradores configurar o logon único para aplicativos que não estejam presentes na galeria de aplicativos do Active Directory do Azure *sem escrever código*. Este recurso foi lançado na visualização técnica de 18 de novembro de 2015 e foi incluído no [Azure Active Directory Premium](active-directory-editions.md). Se você estiver procurando orientações para desenvolvedores sobre como integrar aplicativos personalizados com o AD do Azure por meio de código, veja [Cenários de autenticação do AD do Azure](active-directory-authentication-scenarios.md).

A galeria de aplicativos do Active Directory do Azure fornece uma lista de aplicativos que reconhecidamente dão suporte a um formulário de logon único com o Active Directory do Azure, conforme descrito [neste artigo](active-directory-appssoaccess-whatis.md). Como especialista em TI ou integrador de sistema em sua organização, quando você tiver localizado o aplicativo que deseja conectar, poderá começar seguindo as instruções apresentadas no portal de gerenciamento do Azure para habilitar o logon único.

Os clientes com licenças do [Active Directory Premium do Azure](active-directory-editions.md) também obtêm estes recursos adicionais:

* Integração de autoatendimento de qualquer aplicativo com suporte a provedores de identidade SAML 2.0 (iniciado por SP ou IdP)
* Integração de autoatendimento de qualquer aplicativo Web que tenha uma página de entrada baseada em HTML usando o [SSO baseado em senha](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Conexão de autoatendimento de aplicativos que usam o protocolo SCIM para provisionamento de usuários ([descrito aqui](active-directory-scim-provisioning.md))
* Capacidade de adicionar links aos aplicativos no [inicializador de aplicativos do Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou no [Painel de acesso do Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)

Isso pode incluir não apenas aplicativos SaaS usados por você que ainda não foram integrados à galeria de aplicativos do AD do Azure, mas também aplicativos Web de terceiros que sua organização implantou em servidores sob seu controle, seja na nuvem ou locais.

Esses recursos, também conhecidos como *modelos de integração de aplicativos*, fornecem pontos de conexão baseados em padrões para aplicativos que dão suporte à autenticação baseada em formulários, a SCIM ou a SAML e que incluem opções e configurações flexíveis para compatibilidade com um grande número de aplicativos. 

## <a name="adding-an-unlisted-application"></a>Adicionando um aplicativo não listado
Para conectar um aplicativo usando um modelo de integração de aplicativo, entre no portal de gerenciamento do Azure usando sua conta de administrador do Active Directory do Azure e navegue até a seção **Active Directory > [Diretório] > Aplicativos**, selecione **Adicionar** e **Adicionar um aplicativo da galeria**. 

![][1]

Na galeria de aplicativos, você pode adicionar um aplicativo não listado usando a categoria **Personalizado** à esquerda ou selecionando o link **Adicionar um aplicativo não listado** mostrado nos resultados da pesquisa se seu aplicativo desejado não foi encontrado. Depois de inserir um nome para seu aplicativo, você pode configurar as opções de logon único e comportamento. 

**Dica rápida**: como uma prática recomendada, use a função de pesquisa para verificar se o aplicativo já existe na galeria de aplicativos. Se o aplicativo for encontrado e sua descrição mencionar "logon único", o aplicativo já tem suporte para logon único federado. 

![][2]

A adição de um aplicativo dessa maneira oferece uma experiência muito semelhante àquela disponível para aplicativos previamente integrados. Para iniciar, selecione **Configurar Logon Único**. A próxima tela apresenta três opções para configurar o logon único, descritas nas seções a seguir.

![][3]

## <a name="azure-ad-single-sign-on"></a>Logon único do AD do Azure
Selecione esta opção para configurar a autenticação baseada em SAML no aplicativo. Isso requer que o aplicativo dê suporte a SAML 2.0, e você deve coletar informações sobre como usar os recursos SAML do aplicativo antes de continuar. Depois de selecionar **Próximo**, você será solicitado a inserir três URLs diferentes correspondentes aos pontos de extremidade SAML para o aplicativo. 

![][4]

Estes são:

* **URL de logon (somente iniciado por SP)** – onde o usuário vai para entrar nesse aplicativo. Se o aplicativo estiver configurado para executar logon único iniciado pelo provedor de serviços, quando um usuário navegar para essa URL, o provedor de serviços fará o redirecionamento necessário para o Azure AD a fim de autenticar e conectar o usuário. Se esse campo estiver preenchido, o Azure AD usará essa URL para iniciar o aplicativo do Office 365 e o painel de acesso do Azure AD. Se esse campo for omitido, o Azure AD executará, em vez disso, o logon iniciado pelo provedor de identidade quando o aplicativo for iniciado do Office 365, do painel de acesso do Azure AD ou da URL de logon único do Azure AD (pode ser copiada da guia Painel).
* **URL do emissor** – a URL do emissor deve identificar exclusivamente o aplicativo para o qual o SSO está sendo configurado. Esse é o valor que o Azure AD envia para o aplicativo como o parâmetro **Audiência** do token SAML e o aplicativo deve validá-lo. Esse valor também aparece como a **ID da entidade** em todos os metadados SAML fornecidos pelo aplicativo. Verifique a documentação SAML do aplicativo para obter detalhes sobre o que é a ID da Entidade ou o valor Audiência. Abaixo está um exemplo de como a URL da Audiência aparece no token SAML retornado para o aplicativo:

```
    <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
```

* **URL de resposta** – a URL de resposta é onde o aplicativo espera receber o token SAML. Ela também é chamada de **URL de ACS (Serviço do Consumidor de Declaração)**. Verifique a documentação SAML do aplicativo para obter detalhes sobre o que é a URL de resposta de token SAML ou a URL de ACS.
  Depois que eles tiverem sido inseridos, clique em **Próximo** para prosseguir para a próxima tela. Esta tela fornece informações sobre o que precisa ser configurado no lado do aplicativo para habilitá-lo a aceitar um token SAML do AD do Azure. 

![][5]

Os valores necessários variam de acordo com o aplicativo; portanto, verifique a documentação do SAML do aplicativo para obter detalhes. As URLs de serviço **logon** e **saída** resolvem para o mesmo ponto de extremidade, que é o ponto de extremidade de tratamento de solicitação SAML para a instância do Azure AD. A URL de emissor é o valor que aparece como o "emissor" dentro do token SAML emitido para o aplicativo. 

Depois que o aplicativo tiver sido configurado, clique no botão **Próximo** e em **Concluir** para fechar a caixa de diálogo. 

## <a name="assigning-users-and-groups-to-your-saml-application"></a>Atribuindo usuários e grupos a seu aplicativo SAML
Quando o aplicativo estiver configurado para usar o AD do Azure como um provedor de identidade baseado em SAML, estará quase pronto para teste. Como controle de segurança, o AD do Azure somente emitirá um token permitindo que eles entrem no aplicativo se receberem acesso usando o AD do Azure. Os usuários podem ter acesso diretamente ou através de um grupo do qual sejam membros. 

Para atribuir um usuário ou grupo ao seu aplicativo, clique no botão **Atribuir Usuários** . Selecione o usuário ou grupo que você deseja atribuir e selecione o botão **Atribuir** . 

![][6]

A atribuição de um usuário permite que o AD do Azure emita um token para o usuário e faz aparecer um bloco para esse aplicativo no Painel de Acesso do usuário. Um bloco do aplicativo também aparece no iniciador do aplicativo do Office 365 se o usuário estiver usando o Office 365. 

Você pode carregar um logotipo de bloco para o aplicativo usando o botão **Carregar Logotipo** na guia **Configurar** do aplicativo. 

### <a name="customizing-the-claims-issued-in-the-saml-token"></a>Personalizando as declarações emitidas no token SAML
Quando um usuário é autenticado no aplicativo, o AD do Azure emite um token SAML para o aplicativo que contém informações (ou declarações) sobre o usuário que o identifica com exclusividade. Por padrão, isso inclui o nome de usuário, o endereço de email, o nome e sobrenome do usuário. 

Você pode exibir ou editar as declarações enviadas no token SAML para o aplicativo na guia **Atributos** . 

![][7]

Há duas razões possíveis para você precisar editar as declarações emitidas no token SAML: • O aplicativo foi escrito para exigir um conjunto diferente de URIs de declaração ou valores de declaração • Seu aplicativo foi implantado de uma forma que requer que a declaração NameIdentifier seja algo diferente do nome de usuário (conhecido como UPN) armazenado no Active Directory do Azure. 

Para saber mais sobre como adicionar e editar declarações para esses cenários, confira este [artigo sobre a personalização de declarações](active-directory-saml-claims-customization.md). 

### <a name="testing-the-saml-application"></a>Testando o aplicativo SAML
Depois que o certificado e as URLs de SAML tiverem sido configurados no AD do Azure e no aplicativo, que usuários ou grupos forem atribuídos ao aplicativo no Azure e que as declarações forem examinadas e editadas, se necessário, o usuário estará pronto para entrar no aplicativo. 

Para testar, basta entrar no painel de acesso do Azure AD em https://myapps.microsoft.com usando uma conta de usuário atribuída ao aplicativo e clicar no bloco do aplicativo para iniciar o processo de logon único. Como alternativa, você pode navegar diretamente para a URL de logon do aplicativo e entrar a partir daí. 

Para obter dicas de depuração, confira este [artigo sobre como depurar logon único baseado em SAML para aplicativos](active-directory-saml-debugging.md) 

## <a name="password-single-sign-on"></a>Logon único com senha
Selecione esta opção para configurar o [logon único baseado em senha](active-directory-appssoaccess-whatis.md) para um aplicativo Web que possua uma página de entrada em HTML. O SSO baseado em senha, também conhecido como armazenamento de senha em cofre, permite que você gerencie o acesso de usuários e senhas para aplicativos Web que não dão suporte a federação de identidades. Também é útil para cenários onde vários usuários precisam compartilhar uma única conta, como contas de aplicativo de mídia social da sua organização. 

Depois de selecionar **Próximo**, você deverá inserir a URL da página de entrada baseada na Web do aplicativo. Observe que deve ser a página que inclui os campos de entrada de nome de usuário e senha. Depois de inserida, o AD do Azure iniciará um processo para analisar a página de entrada em busca de entradas de nome de usuário e de senha. Se o processo não for bem-sucedido, ele o orientará por um processo alternativo de instalação de uma extensão de navegador (requer o Internet Explorer, o Chrome ou o Firefox) que permite que você capture os campos manualmente.

Depois que a página for capturada, os usuários e grupos podem ser atribuídos e as políticas de credencial podem ser definidas como [aplicativos de SSO de senha](active-directory-appssoaccess-whatis.md)normais.

Observação: você pode carregar um logotipo de bloco para o aplicativo usando o botão **Carregar Logotipo** na guia **Configurar** do aplicativo. 

## <a name="existing-single-sign-on"></a>Logon único existente
Selecione esta opção para adicionar um link para um aplicativo no Painel de Acesso do AD do Azure ou no portal do Office 365 da sua organização. Você pode usar isso para adicionar links para aplicativos Web personalizado que atualmente usam o Serviços de Federação do Active Directory do Azure (ou outro serviço de federação) em vez do AD do Azure para autenticação. Ou você pode adicionar links profundos para páginas específicas do SharePoint ou outras páginas da Web que você queira que apareçam somente nos Painéis de Acesso do usuário. 

Depois de selecionar **Próximo**, você deverá inserir a URL do aplicativo a ser vinculado. Depois de concluído, os usuários e os grupos poderão ser atribuídos ao aplicativo, o que faz com que o aplicativo seja exibido no [inicializador de aplicativos do Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou no [painel de acesso do Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) desses usuários.

Observação: você pode carregar um logotipo de bloco para o aplicativo usando o botão **Carregar Logotipo** na guia **Configurar** do aplicativo.

## <a name="related-articles"></a>Artigos relacionados
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [Como personalizar declarações emitidas no token SAML para aplicativos pré-integrados](active-directory-saml-claims-customization.md)
* [Solução de problemas de logon único baseado em SAML](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png
