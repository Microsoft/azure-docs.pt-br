---
title: "O que é o acesso a aplicativos e logon único com o Azure Active Directory? | Microsoft Docs"
description: "Use o Active Directory do Azure para habilitar o logon único para todos os aplicativos SaaS e da Web que você precisa para a empresa."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 75d1a3fd-b3c5-4495-a5c8-c4c24145ff00
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: curtand
ms.reviewer: asmalser
ms.custom: it-pro
ms.openlocfilehash: 97df68d446755414cbd427d59f399d6430580f42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-application-access-and-single-sign-on-with-azure-active-directory"></a>O que é o acesso a aplicativos e logon único com o Azure Active Directory?
Sign-on único significa poder acessar todos os aplicativos e recursos que você precisa para fazer negócios, inscrevendo-se apenas uma vez usando uma conta de usuário único. Depois de conectado, você pode acessar todos os aplicativos sem a exigência de autenticação (por exemplo, digite uma senha) uma segunda vez.

Muitas organizações contam com o software como um aplicativo de serviço (SaaS) como o Office 365, Box e Salesforce para produtividade do usuário final. Historicamente, a equipe de TI precisa criar individualmente e atualizar contas de usuário em cada aplicativo SaaS, e os usuários precisam lembrar uma senha para cada aplicativo SaaS.

O Active Directory do Azure estende o Active Directory no local para a nuvem, permitindo que os usuários usem suas contas institucionais primárias não apenas para entrar em seus dispositivos integrados a domínios em recursos da empresa, mas também todos os aplicativos web e SaaS necessários para seu trabalho.

Então, os usuários não precisam gerenciar vários conjuntos de nomes de usuário e senhas, os aplicativos de acesso podem ser provisionados ou desconfigurados automaticamente com base em seus membros de grupo da organização e seu status como um funcionários. O Azure Active Directory introduz controles de governança de segurança e acesso que permite que você gerencie centralmente o acesso dos usuários nos aplicativos SaaS.

O Azure AD permite fácil integração com muitos aplicativos SaaS populares de hoje; ele fornece gerenciamento de identidades e acesso e permite que os usuários façam logon único diretamente em aplicativos, ou os descubram e os iniciem a partir de um portal, como o Office 365 ou painel de acesso do AD do Azure.

A arquitetura da integração consiste nos seguintes quatro principais blocos de construção:

* O logon único permite aos usuários acessar seus aplicativos SaaS com base em sua conta institucional no AD do Azure. O logon único é o que permite que os usuários autentiquem em um aplicativo usando suas contas institucionais únicas.
* O provisionamento do usuário permite que o usuário de provisionamento e desprovisionamento no SaaS com base nas alterações feitas no Active Directory do Windows Server e/ou Azure AD de destino. Uma conta provisionada é o que permite que um usuário autorizado use um aplicativo após ser autenticado por meio de logon único.
* O gerenciamento de acesso de aplicativos centralizado no Portal de gerenciamento do Azure permite acesso a aplicativos ponto único de SaaS e gerenciamento, com a capacidade de delegar a tomada de decisões de acesso do aplicativo e aprovações para qualquer pessoa da organização
* Relatório unificado e monitoramento de atividade de usuário no AD do Azure

## <a name="how-does-single-sign-on-with-azure-active-directory-work"></a>Como funciona o logon único com o Active Directory do Azure?
Quando um usuário "entra" para um aplicativo, ele passa por um processo de autenticação em que é necessário que ele prove que é quem diz ser. Sem logon único, isso normalmente é feito inserindo uma senha que é armazenada no aplicativo e o usuário é obrigado a conhecer essa senha.

O AD do Azure oferece suporte a três maneiras diferentes de entrar em aplicativos:

* **Logon único federado** permite que os aplicativos sejam redirecionados para o Azure AD para autenticação do usuário em vez de solicitar sua própria senha. Isso tem suporte para aplicativos que compatíveis com protocolos como SAML 2.0, WS-Federation ou OpenID Connect e é o melhor modo de logon único.
* **Logon único baseado em senha** permite proteger o armazenamento de senhas de aplicativo e repetir usando uma extensão de navegador da web ou aplicativo móvel. Isso utiliza o processo de entrada existente fornecido pelo aplicativo, mas permite que um administrador gerencie as senhas e não exigem que o usuário saiba a senha.
* **Logon único existente** permite que o AD do Azure para utilize qualquer logon único existente que foi configurado para o aplicativo, mas permite que esses aplicativos a sejam vinculados aos portais de painel de acesso do Office 365 ou Azure AD e também permite que outros tipos de relatório no Azure AD quando os aplicativos são iniciados lá.

Depois que um usuário tenha sido autenticado com um aplicativo, eles também precisa ter um registro de conta provisionado no aplicativo que diz ao aplicativo onde há permissões e nível de acesso que estão dentro do aplicativo. O provisionamento deste registro de conta ou pode ocorrer automaticamente, ou pode ocorrer manualmente por um administrador antes do usuário recebe acesso de logon único.

 Mais detalhes sobre esses modos de logon único e provisionamento abaixo.

### <a name="federated-single-sign-on"></a>Logon único federado
O logon único permite que logon federado dos usuários em sua organização entrem automaticamente em um aplicativo de SaaS de terceiros pelo AD do Azure usando as informações de conta de usuário do AD do Azure.

Nesse cenário, quando você já fez no AD do Azure e para acessar recursos que são controlados por um aplicativo de SaaS de terceiros, a federação elimina a necessidade de um usuário precisar ser autenticado novamente.

AD do Azure pode oferecer suporte ao logon único federado com aplicativos que oferecem suporte a protocolos de conexão WS-Federation, SAML 2.0 ou OpenID.

Consulte também: [Gerenciar certificados para federados Single Sign-On](active-directory-sso-certs.md)

### <a name="password-based-single-sign-on"></a>Logon único baseado em senha
O logon único com base em senha permite aos usuários em sua organização entrar automaticamente em um aplicativo de SaaS de terceiros pelo AD do Azure usando as informações de conta de usuário do aplicativo SaaS de terceiros. Quando você habilita esse recurso, o Azure AD coleta e armazena com segurança as informações de conta de usuário e a senha relacionada.

O AD do Azure pode dar suporte a logon único baseado em senha em qualquer aplicativo baseado em nuvem que tem uma página de entrada baseada em HTML. Usando um plug-in de navegador personalizado, AAD automatiza o processo por meio de recuperar com segurança com as credenciais do aplicativo, como o nome de usuário e a senha do diretório de entrada do usuário e insere essas credenciais na página em nome do usuário de entrada do aplicativo. Há dois casos de uso:

1. **Administrador gerencia credenciais** – os administradores podem criar e gerenciar credenciais de aplicativo e atribuir essas credenciais para usuários ou grupos que precisam de acesso ao aplicativo. Nesses casos, o usuário final não precisa saber as credenciais, mas ainda obtém acesso de logon único ao aplicativo simplesmente clicando no seu painel de acesso ou por meio de um link fornecido. Isso permite o gerenciamento de ciclo de vida das credenciais de administrador e a conveniência dos usuários finais por meio do qual não precisam se lembrar ou gerenciar senhas específicas de aplicativo. As credenciais são ofuscadas do usuário final durante o logon automatizado no processo; no entanto, elas são tecnicamente detectáveis pelo usuário usando ferramentas de depuração da web e os usuários e administradores devem seguir as mesmas políticas de segurança, como se as credenciais foram apresentadas diretamente pelo usuário. As credenciais fornecidas pelo administrador são muito úteis ao fornecer acesso de conta que é compartilhado entre vários usuários, como mídia social ou aplicativos de compartilhamento de documentos.
2. **Usuário gerencia credenciais** – os administradores podem atribuir aplicativos a usuários finais ou grupos e permitir que os usuários finais digitem suas próprias credenciais diretamente ao acessar o aplicativo pela primeira vez no seu painel de acesso. Isso cria uma conveniência para os usuários finais por meio da qual não precisam inserir continuamente as senhas específicas do aplicativo sempre que acessarem o aplicativo. Esse caso de uso também pode ser usado como um ponto de partida para o gerenciamento de credenciais, no qual o administrador pode definir novas credenciais para o aplicativo no futuro sem alterar a experiência de acesso do aplicativo do usuário final.

Em ambos os casos, as credenciais são armazenadas em um estado criptografado no diretório em somente são passadas via HTTPS durante o processo de logon automatizado. Usando o logon único baseado em senha, o AD do Azure oferece uma solução de gerenciamento de acesso conveniente de identidade para aplicativos que não é capaz de oferecer suporte a protocolos de Federação.

O SSO baseado em senha conta com uma extensão de navegador para recuperar as informações específicas do aplicativo e de usuário do AD do Azure e aplicá-la ao serviço com segurança. A maioria dos aplicativos SaaS de terceiros que são suportados pelo AD do Azure oferecem suporte a esse recurso.

Para SSO baseado em senha, os navegadores do usuário final podem ser:

* Internet Explorer 8, 9, 10 e 11 – no Windows 7 ou posterior (Consulte também [guia de implantação de extensão do IE](active-directory-saas-ie-group-policy.md))
* Chrome – No Windows 7 ou posterior e no MacOS X ou posterior
* Firefox 26.0 ou posterior, no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior

**Observação:** a extensão SSO baseada em senha será disponibilizada para borda no Windows 10 quando extensões do navegador se tornarem compatíveis para borda.

### <a name="existing-single-sign-on"></a>Logon único existente
Ao configurar o logon único para um aplicativo, o portal de gerenciamento do Azure fornece uma terceira opção de "Logon único existente". Simplesmente, essa opção permite ao administrador criar um link para um aplicativo e colocá-lo no painel de acesso para os usuários selecionados.

Por exemplo, se houver um aplicativo que está configurado para autenticar usuários usando o Active Directory Federation Services 2.0, um administrador pode usar a opção "Logon único existente" para criar um link para ele no painel de acesso. Quando os usuários acessam o link, eles são autenticados usando o Active Directory Federation Services 2.0 ou qualquer que seja a solução de logon único existente fornecida pelo aplicativo.

### <a name="user-provisioning"></a>Provisionamento do usuário
Para selecionar aplicativos, o AD do Azure permite provisionamento automatizado de usuários e desprovisionamento de contas em aplicativos SaaS de terceiros de dentro do Portal de gerenciamento do Azure, usando suas informações de identidade do Active Directory do Windows Server ou AD do Azure. Quando um usuário recebe permissões no AD do Azure para um desses aplicativos, uma conta pode ser automaticamente criada (provisionada) no aplicativo SaaS de destino.

Quando um usuário é excluído ou suas informações mudam no AD do Azure, essas alterações também são refletidas no aplicativo SaaS. Isso significa que a configuração do gerenciamento de ciclo de vida de identidade automatizado permite aos administradores controlar e fornecer provisionamento e desprovisionamento de aplicativos SaaS automatizado. No AD do Azure, essa automação do ciclo de vida do gerenciamento de identidades é habilitada pelo provisionamento do usuário.

Para obter mais informações, consulte [Desprovisionamento para aplicativos SaaS e provisionamento automatizado do usuário](active-directory-saas-app-provisioning.md)

## <a name="get-started-with-the-azure-ad-application-gallery"></a>Introdução à Galeria de aplicativos do AD do Azure
Pronto para começar? Para implantar o logon único entre o AD do Azure e aplicativos SaaS que sua organização usa, siga estas diretrizes.

### <a name="using-the-azure-ad-application-gallery"></a>Usando a galeria de aplicativos do AD do Azure
A [Galeria de aplicativos do Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/) fornece uma lista de aplicativos que são conhecidos para dar suporte a um formulário de logon único com o Active Directory do Azure.

![][1]

Aqui estão algumas dicas para localizar aplicativos segundo os recursos com os quais eles são compatíveis:

* O AD do Azure dá suporte ao provisionamento automático e desprovisionamento para todos os aplicativos "Featured" na [Galeria de aplicativos do Active Directory do Azure](https://azure.microsoft.com/marketplace/active-directory/all/).
* Uma lista de aplicativos federados que dão suporte especificamente a logon único federado usando um protocolo como SAML, WS-Federation ou OpenID Connect pode ser encontrada [aqui](http://social.technet.microsoft.com/wiki/contents/articles/20235.azure-active-directory-application-gallery-federated-saas-apps.aspx).

Depois de localizar seu aplicativo, você pode começar seguindo as instruções apresentadas na Galeria de aplicativos e no portal de gerenciamento do Azure para habilitar o logon único.

### <a name="application-not-in-the-gallery"></a>Aplicativo não está na Galeria?
Se seu aplicativo não for encontrado na Galeria de aplicativos do AD do Azure, você tem estas opções:

* **Adicionar um aplicativo não listado que você está usando** - Use a categoria Personalizada na galeria de aplicativos no Portal de Gerenciamento do Azure para se conectar a um aplicativo não listado que sua organização está usando. Você pode adicionar qualquer aplicativo que ofereça suporte ao SAML 2.0 como um aplicativo federado ou qualquer aplicativo que tenha uma página de entrada baseada em HTML como um aplicativo do SSO de senha. Para obter mais detalhes, veja este artigo sobre [como adicionar seu próprio aplicativo](active-directory-saas-custom-apps.md).
* **Adicione seu próprio aplicativo que estiver desenvolvendo** - se você tiver desenvolvido o aplicativo, siga as orientações na documentação do desenvolvedor do AD do Azure para implementar o logon único federado ou provisionamento usando o Graph API do AD do Azure. Para obter mais informações, consulte estes recursos:
  
  * [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md)
  * [https://GitHub.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-dotnet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet)
  * [https://GitHub.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-dotnet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet)
  * [https://GitHub.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore)
* **Solicitar uma integração de aplicativo** -Solicite suporte para o aplicativo que você precisa usando o [Fórum de comentários do AD do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/).

### <a name="using-the-azure-management-portal"></a>Usando o Portal de Gerenciamento do Azure
Você pode usar a extensão do Active Directory no Portal de gerenciamento do Azure para configurar o aplicativo de logon único. Como uma primeira etapa, você precisa selecionar um diretório da seção do Active Directory no portal:

![][2]

Para gerenciar seus aplicativos SaaS de terceiros, você poderá alternar para a guia aplicativos do diretório selecionado. Essa exibição permite aos administradores:

* Adicionar novos aplicativos da Galeria do AD do Azure, bem como os aplicativos que você está desenvolvendo
* Excluir aplicativos integrados
* Gerenciar os aplicativos que eles já integrados

Tarefas administrativas típicas para um aplicativo de SaaS de terceiros são:

* Habilitar logon único com o AD do Azure, usando SSO de senha ou, se disponível para SaaS de destino, SSO federado
* Opcionalmente, habilitar o provisionamento de usuário para provisionamento e desprovisionamento de usuário (gerenciamento do ciclo de vida de identidade)
* Para aplicativos em que o provisionamento do usuário esteja habilitado, selecionando quais usuários têm acesso a esse aplicativo

Para aplicativos da Galeria que oferecem suporte um logon único federado, geralmente a configuração exige que você forneça configurações adicionais como certificados e metadados para criar uma confiança federada entre o aplicativo de terceiros e o AD do Azure. O Assistente de configuração orienta os detalhes e fornece acesso fácil aos dados específicos de aplicativos SaaS e instruções.

Para aplicativos da Galeria que oferecem suporte ao provisionamento automático de usuário, é necessário que você conceda permissões do AD do Azure para gerenciar suas contas no aplicativo SaaS. No mínimo, você precisa fornecer credenciais do AD do Azure deve usar quando autenticar no aplicativo de destino. Se houver a necessidade de fornecimento de parâmetros de configuração adicionais, isso dependerá dos requisitos do aplicativo.

## <a name="deploying-azure-ad-integrated-applications-to-users"></a>Implantação de aplicativos do AD do Azure integrado para usuários
O AD do Azure fornece várias maneiras personalizáveis para implantar aplicativos para os usuários finais em sua organização:

* Painel de acesso do AD do Azure
* Iniciador de aplicativos do Office 365
* Logon direto a aplicativos federados
* Links profundos a aplicativos federados, baseado em senha, ou existentes

A escolha dos métodos para implantação de sua organização fica seu critério.

### <a name="azure-ad-access-panel"></a>Painel de acesso do AD do Azure
O painel de acesso em https://myapps.microsoft.com é um portal baseado na web que permite que um usuário final, com uma conta organizacional no Azure Active Directory exiba e inicialize aplicativos baseados em nuvem para os quais tenham sido concedido acesso pelo administrador do AD do Azure. Se você for um usuário final com [Active Directory Premium do Azure](https://azure.microsoft.com/pricing/details/active-directory/), você também pode utilizar os recursos de gerenciamento de grupo de autoatendimento por meio do Painel de acesso.

![][3]

O painel de acesso é separado do Portal de gerenciamento do Azure e não requer que os usuários tenham uma assinatura do Azure ou Office 365.

Para obter mais informações sobre o painel de acesso do AD do Azure, consulte a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

### <a name="office-365-application-launcher"></a>Iniciador de aplicativos do Office 365
Para organizações que implantaram o Office 365, os aplicativos atribuídos aos usuários por meio do AD do Azure também serão exibido no portal do Office 365 em https://portal.office.com/myapps. Isso torna simples e fácil para os usuários em uma organização para iniciar seus aplicativos sem precisar usar um segundo portal e é a solução de inicialização do aplicativo recomendada para organizações que usam o Office 365.

![][4]

Para obter mais informações sobre o iniciador do aplicativo Office 365, consulte [Faça o seu aplicativo ser exibido no iniciador do aplicativo do Office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

### <a name="direct-sign-on-to-federated-apps"></a>Logon direto a aplicativos federados
A maioria dos aplicativos federados que oferecem suporte à conexão SAML 2.0, WS-Federation ou OpenID também oferecem suporte à capacidade dos usuários de iniciar o aplicativo e, em seguida, obter conexão por meio do AD do Azure pelo redirecionamento automático ou clicando em um link para entrar. Isso é conhecido como serviço de logon iniciado pelo provedor e a maioria dos aplicativos federados na Galeria de aplicativos do AD do Azure oferece suporte a eles (consulte a documentação vinculada do Assistente de configuração de logon único do aplicativo no portal de gerenciamento do Azure para obter detalhes).

![][5]

### <a name="direct-sign-on-links-for-federated-password-based-or-existing-apps"></a>Links diretos logon para aplicativos federados, baseados em senha ou existentes
O AD do Azure também oferece suporte a links de logon únicos diretos para aplicativos individuais que oferecem suporte baseado em senha de logon único, logon único existente e qualquer forma de logon único federado.

Esses links são URLs criadas especificamente que enviam um usuário por meio do sinal do AD do Azure no processo de um aplicativo específico sem exigir que o usuário as inicie do painel de acesso do AD do Azure ou Office 365. Essas URLs de logon único podem ser encontradas na guia Painel de qualquer aplicativo previamente integrado na seção Active Directory do portal de gerenciamento do Azure, conforme mostrado na captura de tela abaixo.

![][6]

Esses links podem ser copiados e colados em qualquer lugar em que você deseja fornecer um link de entrada para o aplicativo selecionado. Essa entrada poderia ser em um email ou em qualquer portal personalizado baseado na Web que você configurou o aplicativo para acesso de usuário. Aqui está um exemplo de uma URL de logon único direto do AD do Azure para o Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Semelhante a URLs específicas da organização para o painel de acesso, você pode personalizar ainda mais essa URL adicionando um dos domínios para seu diretório ativos ou verificados após o domínio myapps.microsoft.com. Isso garante que qualquer identidade visual organizacional seja carregada imediatamente na página de entrada sem que o usuário precise inserir sua ID de usuário pela primeira vez:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Quando um usuário autorizado clica em um desses links específicos do aplicativo, ele primeiro consulta sua página de entrada organizacional (supondo que eles não ainda tenha entrados) e depois de entrada é redirecionado para seu aplicativo sem parar no primeiro painel de acesso. Se o usuário não tiver os pré-requisitos para acessar o aplicativo, como a extensão de navegador de logon único baseado em senha, o link solicitará que o usuário instale a extensão que está faltando. A URL do link também permanece constante se a configuração de logon único para o aplicativo for alterada.

Esses links usam os mesmos mecanismos de controle de acesso que o painel de acesso e o Office 365, e apenas esses usuários ou grupos que foram atribuídos ao aplicativo no portal de gerenciamento do Azure serão capazes de autenticar com êxito. No entanto, todos os usuários não autorizados receberão uma mensagem explicando que não foi concedido acesso e receberão um link para carregar o painel de acesso para exibir os aplicativos disponíveis para os quais têm acesso.

## <a name="related-articles"></a>Artigos relacionados
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [Encontrando aplicativos em nuvem não autorizados com o Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)
* [Introdução ao gerenciamento do acesso a aplicativos](active-directory-managing-access-to-apps.md)
* [Comparando recursos de gerenciamento de identidades externas no Azure AD](active-directory-b2b-compare-external-identities.md)

<!--Image references-->
[1]: ./media/active-directory-appssoaccess-whatis/onlineappgallery.png
[2]: ./media/active-directory-appssoaccess-whatis/azuremgmtportal.png
[3]: ./media/active-directory-appssoaccess-whatis/accesspanel.png
[4]: ./media/active-directory-appssoaccess-whatis/officeapphub.png
[5]: ./media/active-directory-appssoaccess-whatis/workdaymobile.png
[6]: ./media/active-directory-appssoaccess-whatis/deeplink.png
