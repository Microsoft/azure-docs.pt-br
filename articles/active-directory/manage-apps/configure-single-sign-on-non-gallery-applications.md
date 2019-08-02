---
title: Logon único - inexistente na Galeria de aplicativos - plataforma de identidade da Microsoft | Microsoft Docs
description: Configurar o logon único (SSO) para inexistente na Galeria aplicativos na plataforma de identidade do Microsoft (Azure AD Azure)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: a72cb7bc7feeba984d568a0465d4f23a494496e8
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807643"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-microsoft-identity-platform"></a>Configurar o logon único para aplicativos na plataforma de identidade da Microsoft

Este artigo é sobre um recurso que permite aos administradores configurar o logon único para aplicativos faltando na Galeria de aplicativos de plataforma Microsoft identity *sem precisar escrever código*.

Se você estiver procurando orientações para desenvolvedores sobre como integrar aplicativos personalizados com o AD do Azure por meio de código, veja [Cenários de autenticação do AD do Azure](../develop/authentication-scenarios.md).

Galeria de aplicativos da plataforma de identidade do Microsoft fornece uma lista de aplicativos que são conhecidos para dar suporte a um formulário de logon único com a plataforma de identidade da Microsoft, conforme descrito em [deste artigo](what-is-single-sign-on.md). Como especialista em TI ou integrador de sistema em sua organização, quando você tiver localizado o aplicativo que deseja conectar, poderá começar seguindo as instruções apresentadas no Portal do Azure para habilitar o logon único.

Os seguintes recursos também estão disponíveis, acordo com seu contrato de licença. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/active-directory/).

- Integração de autoatendimento de um aplicativo que use um protocolo modernos, como [OpenId Connect/OAuth](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) para autenticar seus usuários e obter tokens para [o Microsoft Graph](https://graph.microsoft.com).
- Integração de autoatendimento de qualquer aplicativo que dá suporte a [marcação linguagem SAML (Security Assertion) 2.0](https://wikipedia.org/wiki/SAML_2.0) provedores de identidade (iniciado por SP ou IDP)
- Integração de autoatendimento de qualquer aplicativo Web que tenha uma página de entrada baseada em HTML usando o [SSO baseado em senha](what-is-single-sign-on.md#password-based-sso)
- Conexão de autoatendimento de aplicativos que usam o [sistema para o protocolo de gerenciamento de identidade entre domínios (SCIM) para provisionamento de usuário](use-scim-to-provision-users-and-groups.md)
- Capacidade de adicionar links aos aplicativos no [inicializador de aplicativos do Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) ou no [Painel de acesso do Azure AD](what-is-single-sign-on.md#linked-sign-on)

Esses recursos podem incluir a integração de autoatendimento de um software como um aplicativo de serviço (SaaS) que você usa, mesmo que ninguém tenha integrado o aplicativo na Galeria de aplicativo do Azure AD ainda. Outro recurso é a integração de autoatendimento de um aplicativo web de terceiros que sua organização tiver implantado servidores de que controle, seja na nuvem ou local.

Também conhecido como *modelos de integração de aplicativos*, esses recursos fornecem pontos de conexão baseada em padrões para aplicativos que dão suporte a SAML, SCIM ou autenticação baseada em formulários. Os recursos incluem opções e configurações para compatibilidade com um grande número de aplicativos flexíveis.

## <a name="adding-an-unlisted-application"></a>Adicionando um aplicativo não listado

A plataforma de identidade da Microsoft fornece dois mecanismos para registrar aplicativos.

Um aplicativo que usa um protocolo modernos, como [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) autenticar seus usuários é registrado usando o [portal de registro de aplicativo](../develop/quickstart-register-app.md).

Para registrar os aplicativos que usam todos os outros tipos de [suporte para mecanismos de autenticação](what-is-single-sign-on.md), como o [SAML](../develop/single-sign-on-saml-protocol.md) de protocolo, use o **aplicativos empresariais** folha para se conectar -los com a plataforma de identidade da Microsoft.

Para se conectar a um aplicativo não listado usando um modelo de integração do aplicativo, siga estas etapas:

1. Entrar para o [portal do Azure Active Directory](https://aad.portal.azure.com/) usando sua conta de administrador do Microsoft identity platform.
1. Selecione **aplicativos empresariais** > **novo aplicativo**.
1. (Opcional mas recomendado) No **adicionar da galeria** caixa de pesquisa, digite o nome de exibição do aplicativo. Se o aplicativo for exibido nos resultados da pesquisa, selecione-o e ignore o restante deste procedimento.
1. Selecione **aplicativo inexistente na galeria**. O **adicionar seu próprio aplicativo** página será exibida.

   ![Mostra a adicionar sua própria página de aplicativo](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)

1. Insira o nome de exibição para seu novo aplicativo.
1. Selecione **Adicionar**.

Adicionando um aplicativo dessa maneira, você pode fornecer uma experiência semelhante àquela disponível para aplicativos pré-integrados. Primeiro, selecione **logon único** da barra lateral da caixa de diálogo. A próxima página (**selecionar um método de logon único**) apresenta as opções para configurar o SSO:

- **SAML**
- **Baseado em senha**
- **Vinculado**

![Mostra a seleção uma página de método de logon único](./media/configure-single-sign-on-non-gallery-applications/select-a-single-sign-on-method.png)

Para obter mais informações sobre essas opções, consulte as seguintes seções deste artigo.

## <a name="saml-based-single-sign-on"></a>Logon único baseado em SAML

Selecione o **SAML** opção para configurar a autenticação baseada em SAML para o aplicativo. (Essa opção requer que o aplicativo oferecer suporte a SAML 2.0.) O **definir o logon único com SAML** página será exibida.

![Mostra o conjunto de backup de logon único com página SAML](./media/configure-single-sign-on-non-gallery-applications/set-up-single-sign-on-with-saml.png)

Esta página possui cinco títulos diferentes:

| Número do título | Nome do título | Para obter um resumo deste cabeçalho, consulte: |
| --- | --- | --- |
| 1 | **Configuração básica de SAML** | [Insira a configuração básica do SAML](#enter-basic-saml-configuration) |
| 2 | **Atributos de usuário e declarações** | [Revisar ou personalizar as declarações emitidas no token SAML](#review-or-customize-the-claims-issued-in-the-saml-token) |
| 3 | **Certificado de Autenticação SAML** | [Dados de expiração do certificado de revisão, status e notificação por email](#review-certificate-expiration-data-status-and-email-notification) |
| 4 | **Configurar \<nome do aplicativo >** | [Configurar o aplicativo de destino](#set-up-target-application) |
| 5 | **Testar o logon único com \<nome do aplicativo >** | [Testar o aplicativo SAML](#test-the-saml-application) |

Agora, colete informações sobre como usar os recursos SAML do aplicativo antes de continuar. Conclua as seções a seguir para configurar o SSO entre aplicativos e o Azure AD.

### <a name="enter-basic-saml-configuration"></a>Configuração Básica de SAML

Para configurar o AD do Azure, vá para o **básicas de configuração de SAML** título e selecione sua **editar** ícone (um lápis). Pode inserir os valores manualmente ou carregar um arquivo de metadados para extrair o valor dos campos.

![Mostra a página de configuração de SAML básico](./media/configure-single-sign-on-non-gallery-applications/basic-saml-configuration.png)

Os dois campos a seguir são necessários:

- **Identificador**. Esse valor deve identificar exclusivamente o aplicativo para o qual o logon único está sendo configurado. Você pode encontrar esse valor como o **emissor** elemento o **AuthnRequest** (solicitação SAML) enviada pelo aplicativo. Esse valor também aparece como a **ID da entidade** em todos os metadados SAML fornecidos pelo aplicativo. Verifique a documentação do SAML do aplicativo para obter detalhes sobre o que seu **ID da entidade** ou **público** é de valor.

  O seguinte código mostra como o **identificador** ou **emissor** aparece na solicitação SAML que o aplicativo envia ao Azure AD:

  ```xml
  <samlp:AuthnRequest
  xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
  ID="id6c1c178c166d486687be4aaf5e482730"
  Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
  xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
  </samlp:AuthnRequest>
  ```

- **A URL de resposta**. A URL de resposta é onde o aplicativo espera receber o token SAML. Essa URL também é chamada como a URL de ACS (serviço) do consumidor de declaração. Verifique a documentação SAML do aplicativo para obter detalhes sobre o que é a URL de resposta de token SAML ou a URL de ACS.

  Para configurar várias URLs de resposta, você pode usar o seguinte script do PowerShell.

  ```powershell
  $sp = Get-AzureADServicePrincipal -SearchString "<Exact app name>"
  $app = Get-AzureADApplication -SearchString "<Exact app name>"
  $urllist = New-Object "System.Collections.Generic.List[String]"
  $urllist.Add("<reply URL 1>")
  $urllist.Add("<reply URL 2>")
  $urllist.Add("<reply URL 3>")
  Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls $urllist
  Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls $urllist
  ```

Três campos a seguir são opcionais:

- **URL de logon (iniciado por SP somente)** . Esse valor indica onde o usuário vai para entrar nesse aplicativo. Se o aplicativo executa SSO iniciado por SP, em seguida, quando um usuário navega para essa URL, SP fará o redirecionamento necessário para o Azure AD para autenticar e conectar o usuário. Se você especificar esse campo, o Azure AD usa essa URL para iniciar o aplicativo do Office 365 e o painel de acesso do AD do Azure. Se você omitir esse campo, do Azure AD em vez disso executa logon iniciado pelo IdP durante inicializações do aplicativo do Office 365, o painel de acesso do AD do Azure ou a URL de SSO do Azure AD (que você pode copiar do **Dashboard** página).

- **Estado de retransmissão**. Você pode especificar um estado de retransmissão do SAML para instruir o aplicativo para onde redirecionar usuários após a autenticação. O valor é normalmente uma URL ou caminho que leva os usuários em um local específico dentro do aplicativo.

- **URL de Logoff**. Esse valor é usado para enviar a resposta de logoff do SAML para o aplicativo.

Para obter mais informações, consulte [único protocolo de logon SAML](../develop/single-sign-on-saml-protocol.md).

### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Revise ou personalize as reivindicações emitidas no token SAML

Quando um usuário é autenticado para o aplicativo, Azure AD emite o aplicativo um token SAML com informações (ou declarações) sobre o usuário que identifica com exclusividade. Por padrão, essas informações incluem o nome de usuário, endereço de email, nome e sobrenome do usuário.

Para exibir ou editar as declarações enviadas no token SAML para o aplicativo:

- Vá para o **atributos de usuário e declarações** título e selecione o **editar** ícone. O **atributos de usuário & declarações** página será exibida.

![Mostra a página de atributos e declarações de usuário](./media/configure-single-sign-on-non-gallery-applications/user-attributes-and-claims.png)

Talvez você precise editar as declarações emitidas no token SAML por dois motivos:

- O aplicativo exige um conjunto diferente de URIs ou valores de declaração.
- Seu aplicativo requer o **Nomeie o valor do identificador** alegam ser algo que não seja o nome de usuário (também conhecido como o nome UPN) armazenado na plataforma de identidade da Microsoft.

Para obter mais informações, confira [Como: Personalizar declarações emitidas no token SAML para aplicativos empresariais](../develop/active-directory-saml-claims-customization.md).

### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Revisar dados de expiração de certificado, status e notificação por email

Quando você cria uma galeria ou um aplicativo inexistente na galeria, o Azure AD cria um certificado de específico do aplicativo que expira três anos a partir da data de criação. Você precisa deste certificado para configurar a confiança entre o Azure AD e o aplicativo. Para obter detalhes sobre o formato de certificado, consulte a documentação do aplicativo SAML.

Do Azure AD, você pode baixar o certificado do Active Directory no formato Base64 ou Raw diretamente da principal **definir o logon único com SAML** página. Como alternativa, você pode obter o certificado do Active Directory ao baixar o arquivo de XML de metadados do aplicativo ou usando a URL de metadados de Federação do aplicativo.

Para exibir, criar ou fazer o download de seus certificados (ativos ou inativos), vá para o **certificado de autenticação SAML** título e selecione o **editar** ícone. O **certificado de autenticação SAML** é exibida.

![Mostra a página de certificado de assinatura de SAML](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)

Verifique se que o certificado tem:

- *A data de vencimento desejadas.* Você pode configurar a data de validade para até três anos no futuro.
- *Um status de ativo para o certificado desejado.* Se o status for **Inactive**, altere o status para **Active**. Para alterar o status, linha desejada do certificado com o botão direito e selecione **ativar o certificado**.
- *A opção de assinatura correta e o algoritmo.*
- *Os endereços de email de notificação correto.* Quando o certificado do Active Directory está próximo a data de validade, o Azure AD envia uma notificação ao endereço de email configurado neste campo.  

Para obter mais informações, consulte [gerenciar certificados para logon único federado](manage-certificates-for-federated-single-sign-on.md) e [avançadas de opções no token SAML de assinatura de certificado](certificate-signing-options.md).

### <a name="set-up-target-application"></a>Configurar aplicativo de destino

Para configurar o aplicativo para que o SSO, localize a documentação do aplicativo. Para localizar a documentação, vá para o **configure \<nome do aplicativo >** título e selecione **exibir instruções passo a passo**. A documentação é exibida na **configurar o logon** página. Nessa página ajuda a preencher a **URL de logon**, **identificador do Azure AD**, e **URL de logoff** valores no **configurar \<nome do aplicativo >** título.

Os valores necessários variam de acordo com o aplicativo. Para obter detalhes, consulte a documentação do aplicativo SAML. O **URL de logon** e **URL de logoff** valores resolvem para o mesmo ponto de extremidade, que é o ponto de extremidade de tratamento de solicitação SAML para a instância do AD do Azure. O **identificador do Azure AD** é o valor da **emissor** no token SAML emitido para o aplicativo.

### <a name="assign-users-and-groups-to-your-saml-application"></a>Atribuir usuários e grupos ao seu aplicativo SAML

Depois de configurar seu aplicativo para usar o Azure AD como um provedor de identidade baseado em SAML, está quase pronto para teste. Como um controle de segurança, o Azure AD emite apenas um token que permite ao usuário entrar no aplicativo, se o Azure AD tem acesso concedido ao usuário. Os usuários podem obter acesso diretamente ou por meio de uma associação de grupo.

Para atribuir um novo usuário ou grupo ao seu aplicativo:

1. Na barra lateral do aplicativo, selecione **usuários e grupos**. O  **\<nome do aplicativo >-usuários e grupos** página for exibida, que mostra a lista atual de usuários e grupos atribuídos.
1. Selecione **adicionar usuários**. O **Adicionar atribuições** página será exibida.
1. Selecione **usuários e grupos (\<número > selecionados)** . O **usuários e grupos** página for exibida, mostrando uma lista de usuários e grupos disponíveis.
1. Tipo ou role para encontrar o usuário ou grupo que você deseja atribuir na lista.
1. Selecione cada usuário ou grupo que você deseja adicionar e, em seguida, selecione a **selecionar** botão. O **usuários e grupos** desaparece da página.
1. No **Adicionar atribuições** página, selecione **atribuir**. O  **\<nome do aplicativo >-usuários e grupos** página será exibida com os mostrado na lista de usuários adicionais.

   ![Mostra a página de usuários e grupos de aplicativo](./media/configure-single-sign-on-non-gallery-applications/application-users-and-groups.png)

Nesta lista, você pode:

- Remova um usuário.
- Edite sua função.
- Atualize suas credenciais (nome de usuário e senha) para que o usuário pode autenticar para o aplicativo no painel de acesso do usuário.

Você pode editar ou remover vários usuários ou grupos por vez.

Atribuir um usuário permite que o Azure AD para emitir um token de usuário. Isso também faz com que um bloco para este aplicativo apareça no Painel de Acesso do usuário. Um bloco do aplicativo também aparece no iniciador de aplicativo do Office 365 se o usuário estiver usando o Office 365.

> [!NOTE]
> Você pode carregar um logotipo de bloco para o aplicativo usando o botão **Carregar Logotipo** na guia **Configurar** do aplicativo.

### <a name="test-the-saml-application"></a>Teste o aplicativo SAML

Antes de testar o aplicativo SAML, você deve já configurar o aplicativo com o Azure AD e atribuídos a usuários ou grupos ao aplicativo. Para testar o aplicativo SAML, selecione **logon único**, que retornará o **baseado em SAML logon** página. (Se um método diferente de SSO estava em vigor, selecione **alterar modos de logon únicos** > **SAML** muito.) Em seguida, nos **testar o logon único com \<nome do aplicativo >** título, selecione **teste**. Para obter mais informações, consulte [baseado em SAML depurar logon único para aplicativos no Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="password-single-sign-on"></a>Logon único com senha

Selecione esta opção para configurar [baseado em senha de logon único no](what-is-single-sign-on.md) para um aplicativo web com uma página de entrada de HTML. O SSO baseado em senha, também conhecido como armazenamento de senha em cofre, permite que você gerencie o acesso de usuários e senhas para aplicativos Web que não dão suporte a federação de identidades. Também é útil para cenários onde vários usuários precisam compartilhar uma única conta, como contas de aplicativo de mídia social da sua organização.

Depois de selecionar **baseado em senha**, você será solicitado a inserir a URL da baseado na web página de entrada do aplicativo.

![Mostra a página de URL de entrada para inserir a URL de logon](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

Em seguida, siga estas etapas:

1. Insira a URL. Essa cadeia de caracteres deve ser a página que inclui o campo de entrada de nome de usuário.
1. Clique em **Salvar**. Azure AD tenta analisar a página de entrada para um nome de usuário de entrada e uma senha de entrada.
1. Se a tentativa falhar de análise do Azure AD, selecione **configurar \<nome do aplicativo > configurações de logon único com senha** para exibir o **configura o logon** página. (Se a tentativa for bem-sucedida, você pode ignorar o restante deste procedimento.)
1. Selecione **detectar manualmente campos de entrada**. Instruções adicionais que descrevem a detecção manual de campos de entrada são exibidos.

   ![Configuração manual de baseado em senha de logon único](./media/configure-single-sign-on-non-gallery-applications/password-configure-sign-on.png)

1. Selecione **capturar campos de entrada**. Uma página de status de captura é aberta em uma nova guia, mostrando a mensagem **captura de metadados está atualmente em andamento**.
1. Se o **extensão do painel de acesso necessária** caixa é exibida em uma nova guia, selecione **instalar agora** para instalar o **extensão de entrada segura dos meus aplicativos** extensão do navegador. (Requer que a extensão do navegador Microsoft Edge, Chrome ou Firefox.) Em seguida, instalar, iniciar, habilitar a extensão e atualize a página de status de captura.

   A extensão do navegador, em seguida, abre outra guia que exibe a URL digitada.

1. Na guia com a URL digitada, percorra o processo de logon. Preencha os campos de nome de usuário e senha e tente entrar. (Você não precisa fornecer a senha correta.)

   Um prompt pedirá que você salve os campos de entrada capturados.

1. Selecione **OK**. Fecha a guia, a extensão do navegador atualiza a página de status de captura com a mensagem **metadados foi atualizado para o aplicativo**e esse navegador guia também é fechado.
1. No Azure AD **configurar o logon** página, selecione **Okey, foi capaz de entrar no aplicativo com êxito**.
1. Selecione **OK**.

Após a captura da página de entrada, você pode atribuir usuários e grupos, e você pode configurar políticas de credencial como regular [aplicativos de SSO de senha](what-is-single-sign-on.md).

> [!NOTE]
> Você pode carregar um logotipo de bloco para o aplicativo usando o botão **Carregar Logotipo** na guia **Configurar** do aplicativo.

## <a name="existing-single-sign-on"></a>Logon único existente

Selecione esta opção para adicionar um link para o aplicativo no portal do painel de acesso do AD do Azure ou Office 365 da sua organização. Você pode usar esse método para adicionar links aos aplicativos da web personalizado que atualmente usam os serviços de Federação do Active Directory (ou outro serviço de federação) em vez do Azure AD para autenticação. Ou você pode adicionar links profundos para páginas específicas do SharePoint ou outras páginas da Web que você queira que apareçam somente nos Painéis de Acesso do usuário.

Depois de selecionar **vinculado**, você será solicitado a inserir a URL do aplicativo para vincular a. Digite a URL e selecione **salvar**. Você pode atribuir usuários e grupos para o aplicativo, o que faz com que o aplicativo seja exibido na [inicializador de aplicativos do Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou o [painel de acesso do AD do Azure](end-user-experiences.md) para esses usuários.

> [!NOTE]
> Você pode carregar um logotipo de bloco para o aplicativo usando o botão **Carregar Logotipo** na guia **Configurar** do aplicativo.

## <a name="related-articles"></a>Artigos relacionados

- [Como: Personalizar declarações emitidas no token SAML para aplicativos empresariais](../develop/active-directory-saml-claims-customization.md)
- [Depurar baseado em SAML SSO para aplicativos no Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md)
- [Plataforma de identidade da Microsoft (anteriormente conhecido como Azure Active Directory para desenvolvedores)](../develop/index.yml)
