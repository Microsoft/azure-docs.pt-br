---
title: Configurar logon único – Azure Active Directory | Microsoft Docs
description: Este tutorial usa o portal do Azure para configurar logon único baseado em SAML para um aplicativo com Azure AD (Azure Active Directory).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: c5e8ed4a78fccce4f3a5c631a99a8729114e5722
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422609"
---
# <a name="how-to-configure-saml-based-single-sign-on"></a>Como configurar o logon único baseado em SAML

Depois de adicionar um aplicativo a seus aplicativos empresariais do Azure AD, você definirá as configurações de logon único. Este artigo descreve como configurar o logon único baseado em SAML para um aplicativo inexistente na galeria. 

> [!NOTE]
> Adicionando um aplicativo da galeria? Encontre instruções de instalação passo a passo na [lista de tutoriais de aplicativo SaaS](../saas-apps/tutorial-list.md)

Para configurar o logon único para um aplicativo que não seja de galeria *sem escrever código*, você precisa ter uma assinatura ou o Azure AD Premium e o aplicativo devem ter suporte para SAML 2.0. Para obter mais informações sobre as versões do Azure AD, visite [Preços do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Antes de começar

- Se o aplicativo não tiver sido adicionado ao seu locatário do Azure AD, confira [Adicionar um aplicativo da galeria](add-gallery-app.md) ou [Adicionar um aplicativo inexistente na galeria](add-non-gallery-app.md).
- Entre em contato com o fornecedor do aplicativo para obter as informações corretas para as seguintes configurações:

    | Configuração Básica do SAML | Iniciado por SP | iniciado por idP | DESCRIÇÃO |
    |:--|:--|:--|:--|
    | Identificador (ID da Entidade) | Obrigatório para alguns aplicativos | Obrigatório para alguns aplicativos | Identifica exclusivamente o aplicativo para o qual o logon único está sendo configurado. O Azure AD envia o identificador para o aplicativo como o parâmetro Audience do token SAML. O aplicativo deve validá-lo. Esse valor também aparece como a ID da entidade em todos os metadados SAML fornecidos pelo aplicativo. *Encontre esse valor como o elemento **Emissor** na **AuthnRequest** (solicitação SAML) enviada pelo aplicativo.* |
    | URL de resposta | Opcional | Obrigatório | Especifica onde o aplicativo espera receber o token SAML. A URL de resposta também é chamada de URL do ACS (Serviço do Consumidor de Declaração). |
    | URL de logon | Obrigatório | Não especifique | Quando um usuário abre essa URL, o provedor de serviço redireciona para o Azure AD a fim de autenticar e conectar o usuário. O Azure AD usa a URL para iniciar o aplicativo no Office 365 ou no painel de acesso do Azure AD. Quando ela estiver em branco, o Azure AD dependerá do provedor de identidade para iniciar o logon único quando um usuário iniciar o aplicativo.|
    | Estado de Retransmissão | Opcional | Opcional | Especifica para onde o aplicativo deve redirecionar o usuário depois que a autenticação é concluída. Normalmente, o valor é uma URL válida para o aplicativo. No entanto, alguns aplicativos usam esse campo de forma diferente. Para obter mais informações, pergunte ao fornecedor do aplicativo.
    | URL de logoff | Opcional | Opcional | Usada para enviar as respostas de Logoff do SAML novamente ao aplicativo.

## <a name="step-1-edit-the-basic-saml-configuration"></a>Etapa 1. Editar a Configuração Básica do SAML

1. Entre no [portal do Azure](https://portal.azure.com) como administrador do aplicativo em nuvem ou um administrador de aplicativo para seu locatário do Azure AD.

1. Navegue até **Azure Active Directory** > **Aplicativos empresariais** e selecione o aplicativo na lista. 
   
   - Para pesquisar o aplicativo, no menu **Tipo de Aplicativo**, selecione **Todos os aplicativos** e, em seguida, **Aplicar**. Insira o nome do aplicativo na caixa de pesquisa e, em seguida, selecione o aplicativo nos resultados.

1. Na seção **Gerenciar**, selecione **Logon único**. 

1. Selecione **SAML**. A página **Configurar logon único com SAML – Visualização** será exibida.

1. Para editar as opções de configuração básica do SAML, selecione o ícone **Editar** (um lápis) no canto superior direito da seção **Configuração Básica do SAML**.

     ![Configurar certificados](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

1. Nos campos apropriados, insira as informações descritas na seção [Antes de começar](#before-you-begin).

1. Na parte superior da página, selecione **Salvar**.

## <a name="step-2-configure-user-attributes-and-claims"></a>Etapa 2. Configurar Atributos e declarações de usuário 

Um aplicativo pode exigir declarações ou atributos de usuário específicos no token SAML recebido do Azure AD quando um usuário se conecta. Por exemplo, URIs de declaração ou valores de declaração específicos podem ser necessários ou o **Nome** pode precisar ser algo diferente do nome de usuário armazenado na plataforma de identidade da Microsoft. Os requisitos para aplicativos da galeria são descritos nos [tutoriais específicos do aplicativo](../saas-apps/tutorial-list.md) ou você pode solicitá-los ao fornecedor do aplicativo. As etapas gerais para configurar atributos e declarações de usuário são descritas abaixo.

1. Na seção **Atributos e declarações de usuário**, selecione o ícone **Editar** (um lápis) no canto superior direito.

1. Verifique o **Valor do Identificador de Nome**. O valor padrão é *user.principalname*. O identificador de usuário identifica exclusivamente cada usuário dentro do aplicativo. Por exemplo, se o endereço de email é tanto o nome de usuário quanto o identificador exclusivo, defina o valor como *user.mail*.

1. Para modificar o **Valor do Identificador de Nome**, selecione o ícone **Editar** (um lápis) para o campo **Valor do Identificador de Nome**. Faça as alterações apropriadas na origem e no formato do identificador, conforme necessário. Para obter detalhes, confira [Como editar a NameId](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#editing-nameid). Salve as alterações quando terminar. 
 
1. Para configurar as declarações de grupo, selecione o ícone **Editar** para o campo **Grupos retornados na declaração**. Para obter detalhes, confira [Configurar declarações de grupo](../hybrid/how-to-connect-fed-group-claims.md).

3. Para adicionar uma declaração, selecione **Adicionar nova declaração** na parte superior da página. Insira o **Nome** e selecione a fonte apropriada. Se você selecionar a origem **Atributo**, precisará escolher o **Atributo de origem** que deseja usar. Se você selecionar a origem **Tradução**, precisará escolher a **Transformação** e o **Parâmetro 1** que deseja usar. Para obter detalhes, confira [Como adicionar declarações específicas do aplicativo](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#adding-application-specific-claims). Salve as alterações quando terminar. 

4. Clique em **Salvar**. A nova declaração será exibida na tabela.

   > [!NOTE]
   > Para obter outras maneiras de personalizar o token SAML do Azure AD para seu aplicativo, confira os recursos a seguir.
   >- Para criar funções personalizadas por meio do portal do Azure, confira [Configurar declarações de função](../develop/active-directory-enterprise-app-role-management.md).
   >- Para personalizar as declarações por meio do PowerShell, confira [Personalizar declarações – PowerShell](../develop/active-directory-claims-mapping.md).
   >- Para modificar o manifesto do aplicativo a fim de configurar declarações opcionais para seu aplicativo, confira [Configurar declarações opcionais](../develop/active-directory-optional-claims.md).
   >- Para definir políticas de tempo de vida de token para tokens de atualização, tokens de acesso, tokens de sessão e tokens de ID, confira [Configurar tempos de vida de tokens](../develop/active-directory-configurable-token-lifetimes.md). Ou para restringir sessões de autenticação por meio do acesso condicional do Azure AD, confira [Funcionalidades do gerenciamento de sessão de autenticação](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Etapa 3. Gerenciar o certificado de autenticação SAML

O Azure AD usa um certificado para assinar os tokens SAML que ele envia para o aplicativo. Na página **Configurar o Logon Único com o SAML**, você pode exibir ou baixar o certificado ativo. Você também pode atualizar, criar ou importar um certificado. Para aplicativos da galeria, os detalhes sobre o formato do certificado estão disponíveis na documentação do SAML do aplicativo (confira os [tutoriais específicos do aplicativo](../saas-apps/tutorial-list.md)). 

1. Acesse a seção **Certificado de Autenticação SAML**. Dependendo do tipo de aplicativo, você verá opções para baixar o certificado no formato Base64, em formato bruto ou no XML de Metadados de Federação. O Azure AD também fornece a **URL de Metadados de Federação do Aplicativo**, na qual você pode acessar os metadados específicos do aplicativo no formato `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

1. Para gerenciar, criar ou importar um certificado, selecione o ícone **Editar** (um lápis) no canto superior direito da seção **Certificado de Autenticação SAML** e, em seguida, siga um destes procedimentos:

   - Para criar um certificado, selecione **Novo Certificado**, selecione a **Data de Validade** e, em seguida, **Salvar**. Para ativar o certificado, selecione o menu de contexto ( **...** ) e selecione **Tornar o certificado ativo**.
   - Para carregar um certificado com as credenciais de chave privada e do PFX, selecione **Importar Certificado** e procure o certificado. Insira a **Senha do PFX** e, em seguida, selecione **Adicionar**.  
   - Para configurar as opções avançadas de assinatura de certificado, use as opções a seguir. Para obter descrições dessas opções, confira o artigo [Opções avançadas de assinatura de certificado](certificate-signing-options.md).
      - Na lista suspensa **Opção de Assinatura**, escolha **Assinar resposta SAML**, **Assinar declaração SAML** ou **Assinar resposta e declaração SAML**.
      - Na lista suspensa **Algoritmo de Assinatura**, escolha **SHA-1** ou **SHA-256**.
   - Para notificar outras pessoas quando o certificado ativo estiver próximo da data de validade, insira os endereços nos campos **Endereços de email de notificação**.

1. Selecione **Salvar** na parte superior da seção **Certificado de Autenticação SAML**. 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Etapa 4. Configurar o aplicativo para usar o Azure AD

A seção **Configurar o \<nome_do_aplicativo>** lista os valores que precisam ser configurados no aplicativo, de modo que ele use o Azure AD como um provedor de identidade SAML. Os valores necessários variam de acordo com o aplicativo. Para obter detalhes, consulte a documentação do aplicativo SAML.

1. Role para baixo até a seção **Configurar \<applicationName >** . 
2. Copie o valor de cada linha dessa seção, conforme necessário, e siga as instruções específicas do aplicativo para adicionar o valor ao aplicativo. Para aplicativos da galeria, veja a documentação selecionando **Exibir instruções passo a passo**. 
   - Os valores da **URL de Logon** e da **URL de Logoff** são resolvidos para o mesmo ponto de extremidade, que é o ponto de extremidade de tratamento de solicitações SAML para sua instância do Azure AD. 
   - O **Identificador do Azure AD** é o valor do **Emissor** no token SAML emitido para o aplicativo.
1. Depois de colar todos os valores nos campos apropriados, selecione **Salvar**.

## <a name="step-5-validate-single-sign-on"></a>Etapa 5. Validar logon único

Você está pronto para testar as configurações para ver se o logon único funciona para você, o administrador.  

1. Abra as configurações de logon único do aplicativo. 
2. Role a página até a seção **Validar o logon único com o <applicationName>** . Para este tutorial, esta seção é chamada **Configurar GitHub-test**.
3. Selecione **Testar**. As opções de teste são exibidas.
4. Selecione **Entrar como o usuário atual**. 

Se o logon for bem-sucedido, você estará pronto para atribuir usuários e grupos ao aplicativo SAML.
Se uma mensagem de erro for exibida, conclua as seguintes etapas:

1. Copie e cole as informações específicas na caixa **Como é o erro?** .

    ![Use a caixa de "Qual é a aparência do erro" para obter diretrizes de resolução](media/configure-single-sign-on-portal/error-guidance.png)

1. Selecione **Obter diretrizes de resolução**. A causa raiz e as orientações para resolução aparecem.  Neste exemplo, o usuário não estava atribuído ao aplicativo.
1. Leia as diretrizes de resolução e, em seguida, se possível, corrija o problema.
1. Execute o teste novamente até que ele seja concluído com êxito.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir usuários e grupos ao aplicativo](methods-for-assigning-users-and-groups.md)
- [Configurar o provisionamento automático de conta de usuário](configure-automatic-user-provisioning-portal.md)
