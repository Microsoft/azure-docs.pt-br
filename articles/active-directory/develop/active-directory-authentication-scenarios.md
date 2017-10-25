---
title: "Cenários de autenticação do Azure AD | Microsoft Docs"
description: "Uma visão geral dos cinco cenários mais comuns de autenticação para o Azure Active Directory (AAD)"
services: active-directory
documentationcenter: dev-center-name
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: skwan
ms.custom: aaddev
ms.openlocfilehash: 2f9410bdaa037f1839cf7c12c3532b51be669ed5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="authentication-scenarios-for-azure-ad"></a>Cenários de autenticação do AD do Azure
O Azure Active Directory (Azure AD) simplifica a autenticação para os desenvolvedores fornecendo identidade como serviço, com suporte para protocolos padrão da indústria, como OAuth 2.0 e OpenID Connect, bem como bibliotecas de código aberto para plataformas diferentes para ajudá-lo a começar a codificar rapidamente. Este documento ajudará a compreender as vários cenários aos quais o Azure AD oferece suporte e mostrará como começar. Ele é dividido nas seguintes seções:

* [Noções básicas de autenticação no Azure AD.](#basics-of-authentication-in-azure-ad)
* [Declarações em tokens de segurança do Azure AD](#claims-in-azure-ad-security-tokens)
* [Noções básicas de registrar um aplicativo no Azure AD](#basics-of-registering-an-application-in-azure-ad)
* [Cenários e tipos de aplicativos](#application-types-and-scenarios)
  
  * [Navegador da Web para o aplicativo Web](#web-browser-to-web-application)
  * [SPA (Aplicativo de Página Única)](#single-page-application-spa)
  * [Aplicativo nativo para API da Web](#native-application-to-web-api)
  * [Aplicativo Web para API da Web](#web-application-to-web-api)
  * [Servidor ou aplicativo Daemon para API da Web](#daemon-or-server-application-to-web-api)

## <a name="basics-of-authentication-in-azure-ad"></a>Noções básicas de autenticação no Azure AD.
Se você não estiver familiarizado com os conceitos básicos de autenticação no Azure AD, leia esta seção. Caso contrário, convém pular para [Cenários e tipos de aplicativos](#application-types-and-scenarios).

Vamos considerar o cenário mais básico, em que a identidade é necessária: um usuário em um navegador da Web precisa autenticar um aplicativo Web. Esse cenário é descrito mais detalhadamente na seção [Navegador da Web para o aplicativo Web](#web-browser-to-web-application) , no entanto, é útil, como um ponto de partida, ilustrar os recursos do Azure AD e conceitualizar como o cenário funciona. Considere o diagrama a seguir para esse cenário:

![Visão geral de logon no aplicativo Web](./media/active-directory-authentication-scenarios/basics_of_auth_in_aad.png)

Com o diagrama acima em mente, aqui está o que você precisa saber sobre seus vários componentes:

* O Azure AD é o provedor de identidade, responsável por verificar a identidade dos usuários e aplicativos existentes no diretório de uma organização e, por fim, emitir tokens de segurança após a autenticação bem-sucedida desses usuários e aplicativos.
* Um aplicativo que deseja terceirizar a autenticação para o Azure AD deve ser registrado no Azure AD, que registra e identifica exclusivamente o aplicativo no diretório.
* Os desenvolvedores podem usar as bibliotecas de autenticação de código aberto do Azure AD para facilitar a autenticação, manipulando os detalhes de protocolo por você. Consulte [Bibliotecas de autenticação do Azure Active Directory](active-directory-authentication-libraries.md) para obter mais informações.

• Depois que um usuário foi autenticado, o aplicativo deve validar o token de segurança do usuário para garantir que a autenticação tenha sido bem-sucedida para as partes planejadas. Os desenvolvedores podem usar as bibliotecas de autenticação fornecidas para manipular a validação de qualquer token do Azure AD, incluindo o JSON Web Tokens (JWT) ou SAML 2.0. Se você quiser executar a validação manualmente, veja a documentação [Manipulador de token JWT](https://msdn.microsoft.com/library/dn205065.aspx) .

> [!IMPORTANT]
> O Azure AD usa criptografia de chave pública para assinar tokens e verificar se eles são válidos. Consulte [Informações importantes sobre substituição de chave de assinatura no Azure AD](active-directory-signing-key-rollover.md) para obter mais informações sobre a lógica necessária que você deve ter em seu aplicativo para garantir que ele esteja sempre atualizado com as últimas chaves.
> 
> 

• O fluxo de solicitações e respostas para o processo de autenticação é determinado pelo protocolo de autenticação que foi usado, como OAuth 2.0, OpenID Connect, WS-Federation ou SAML 2.0. Esses protocolos são discutidos em mais detalhes no tópico [Protocolos de autenticação do Azure Active Directory](active-directory-authentication-protocols.md) e nas seções a seguir.

> [!NOTE]
> O Azure AD oferece suporte aos padrões OAuth 2.0 e OpenID Connect que fazem uso extensivo de tokens de portador, incluindo tokens de portador representados como JWTs. Um token de portador é um token de segurança leve que concede ao "portador" acesso a um recurso protegido. Nesse sentido, o "portador" é qualquer parte que possa apresentar o token. Embora uma parte deva primeiro se autenticar no Azure AD para receber o token de portador, se as medidas necessárias não forem tomadas para proteger o token durante a transmissão e o armazenamento, ele pode ser interceptado e usado por uma parte não planejada. Embora alguns tokens de segurança tenham um mecanismo interno para impedir que partes não autorizadas os utilizem, tokens de portador não possuem esse mecanismo e devem ser transportados em um canal seguro, como segurança da camada de transporte (HTTPS). Se um token de portador for transmitido livremente, um ataque por parte de intermediários pode ser usado por uma parte mal-intencionada para adquirir o token e usá-lo para um acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança se aplicam ao armazenar ou manter em cache tokens de portador para uso posterior. Sempre certifique-se de que seu aplicativo transmita e armazene tokens de portador de maneira segura. Para obter mais considerações de segurança sobre tokens de portador, consulte [RFC 6750 seção 5](http://tools.ietf.org/html/rfc6750).
> 
> 

Agora que você tem uma visão geral dos conceitos básicos, leia as seções a seguir para entender como funciona o provisionamento no Azure AD e os cenários comuns aos quais o Azure AD oferece suporte.

## <a name="claims-in-azure-ad-security-tokens"></a>Declarações em tokens de segurança do Azure AD
Tokens de segurança emitidos pelo Azure AD contêm declarações ou asserções de informações sobre a entidade que foi autenticada. Essas declarações podem ser usadas pelo aplicativo para várias tarefas. Por exemplo, elas podem ser usadas para validar o token, identificar o locatário de diretório da entidade, exibir informações do usuário, determinar a autorização da entidade e assim por diante. As declarações presentes em qualquer token de segurança variam de acordo com o tipo de token, o tipo de credencial usada para autenticar o usuário e a configuração do aplicativo. Uma breve descrição de cada tipo de declaração emitida pelo Azure AD é fornecida na tabela a seguir. Para obter mais informações, consulte [Tipos de token e de declaração suportados](active-directory-token-and-claims.md).

| Declaração | Descrição |
| --- | --- |
| ID do aplicativo |Identifica o aplicativo que está usando o token. |
| Público-alvo |Identifica o recurso de destinatário ao qual o token é destinado. |
| Referência de classe de contexto de autenticação de aplicativo |Indica como o cliente foi autenticado (cliente público versus cliente confidencial). |
| Instante da autenticação |Registra a data e o horário em que ocorreu a autenticação. |
| Método de autenticação |Indica como a entidade do token foi autenticada (senha, certificado, etc.). |
| Nome |Apresenta o nome fornecido do usuário conforme definido no Azure AD. |
| Grupos |Contém as IDs de objeto dos grupos em que o usuário é membro. |
| Provedor de identidade |Registra o provedor de identidade que autenticou a entidade do token. |
| Emitido em |Registra o horário em que o token foi emitido, geralmente usado para atualização de token. |
| Emissor |Identifica o STS que emitiu o token, bem como o locatário do Azure AD. |
| Sobrenome |Apresenta o sobrenome fornecido do usuário conforme definido no Azure AD. |
| Nome |Fornece um valor legível que identifica a entidade do token. |
| ID do objeto |Contém um identificador exclusivo e imutável da entidade no Azure AD. |
| Funções |Contém os nomes amigáveis de funções de aplicativo do Azure AD que o usuário recebeu. |
| Escopo |Indica as permissões concedidas ao aplicativo cliente. |
| Subject |Indica a entidade de segurança sobre a qual o token declara informações. |
| ID do locatário |Contém um identificador exclusivo e imutável do locatário do diretório que emitiu o token. |
| Vida útil do token |Define o intervalo de tempo no qual um token é válido. |
| Nome principal do usuário |Contém o nome principal de usuário da entidade. |
| Versão |Contém o número de versão do token. |

## <a name="basics-of-registering-an-application-in-azure-ad"></a>Noções básicas de registrar um aplicativo no Azure AD
Qualquer aplicativo que terceirize a autenticação ao Azure AD deve ser registrado em um diretório. Esta etapa envolve comunicar o Azure AD sobre seu aplicativo, incluindo a URL da sua localização, a URL para enviar respostas após a autenticação, o URI para identificar seu aplicativo e mais. Essas informações são necessárias por alguns motivos:

* O Azure AD precisa de coordenadas para se comunicar com o aplicativo ao lidar com tokens de logon ou troca. Elas incluem as seguintes:
  
  * URI de ID do aplicativo: O identificador de um aplicativo. Esse valor é enviado ao Azure AD durante a autenticação para indicar para qual aplicativo o chamador deseja ter um token. Além disso, esse valor é incluído no token para que o aplicativo saiba que ele era o destino pretendido.
  * URL de resposta e URI de redirecionamento: no caso de uma API da Web ou aplicativo Web, a URL de resposta é o local ao qual o Azure AD enviará a resposta de autenticação, incluindo um token caso a autenticação seja bem-sucedida. No caso de um aplicativo nativo, o URI de redirecionamento é um identificador exclusivo para o qual o Azure AD redirecionará o usuário-agente em uma solicitação do OAuth 2.0.
  * ID do Aplicativo: a ID de um aplicativo, que é gerada pelo Azure AD quando o aplicativo é registrado. Ao solicitar um código de autorização ou token, a ID do Aplicativo e a chave são enviadas ao Azure AD durante a autenticação.
  * Chave: a chave enviada com uma ID de Aplicativo durante a autenticação no Azure AD para chamar uma API da Web.
* O Azure AD precisa garantir que o aplicativo tem as permissões necessárias para acessar os dados do diretório, outros aplicativos em sua organização e assim por diante

O provisionamento fica mais claro quando você entende que há duas categorias de aplicativos que podem ser desenvolvidos e integrados no Azure AD:

* Aplicativo de locatário único: um aplicativo de locatário único destina-se ao uso em uma organização. Eles normalmente são aplicativos de linha de negócios (LoB) escritos por um desenvolvedor empresarial. Um aplicativo de locatário único só precisa ser acessado por usuários em um diretório e, como resultado, ele só precisa ser provisionado em um diretório. Esses aplicativos geralmente são registrados por um desenvolvedor na organização.
* Aplicativos multilocatários: um aplicativo multilocatário é destinado para uso em muitas organizações, não apenas em uma organização. Esses são normalmente aplicativos de software como serviço (SaaS) escritos por um fornecedor de software independente (ISV). Aplicativos multilocatários precisam ser provisionados em cada diretório em que serão usados, o que requer o consentimento do usuário ou administrador para registrá-los. Esse processo de consentimento é iniciado quando um aplicativo tiver sido registrado no diretório e receber acesso à Graph API ou talvez a outra API da Web. Quando um usuário ou administrador de uma organização diferente se inscreve para usar o aplicativo, é apresentada uma caixa de diálogo que exibe as permissões exigidas pelo aplicativo. O usuário ou administrador pode então dar consentimento para o aplicativo, que fornece ao aplicativo acesso aos dados declarados e, por fim, registra o aplicativo em seu diretório. Para obter mais informações, consulte [Visão geral da estrutura de consentimento](active-directory-integrating-applications.md#overview-of-the-consent-framework).

Surgem algumas considerações adicionais ao desenvolver um aplicativo multilocatário em vez de um aplicativo de locatário único. Por exemplo, se você estiver disponibilizando seu aplicativo a usuários em vários diretórios, você precisa de um mecanismo para determinar em qual locatário eles estão. Um aplicativo de locatário único só precisa procurar em seu próprio diretório por um usuário, enquanto que um aplicativo multilocatário precisa identificar um usuário específico em todos os diretórios no Azure AD. Para realizar essa tarefa, o Azure AD fornece um ponto de extremidade de autenticação comum em que qualquer aplicativo multilocatário pode direcionar solicitações de entrada, em vez de um ponto de extremidade específico de locatário. Esse ponto de extremidade é https://login.microsoftonline.com/common para todos os diretórios no Azure AD, ao passo que um ponto de extremidade específico do locatário pode ser https://login.microsoftonline.com/contoso.onmicrosoft.com. O ponto de extremidade comum deve ser considerado com especial importância ao desenvolver seu aplicativo, pois você precisará da lógica necessária para lidar com vários locatários durante o login, logout e validação de token.

Se você está desenvolvendo atualmente um aplicativo de locatário único, mas deseja disponibilizá-lo para muitas organizações, você pode facilmente fazer alterações no aplicativo e em sua configuração no Azure AD para habilitá-lo a multilocatários. Além disso, o Azure AD usa a mesma chave de assinatura para todos os tokens em todos os diretórios, independentemente de você estar fornecendo autenticação em um aplicativo de locatário único ou multilocatário.

Cada cenário listado neste documento inclui uma subseção que descreve seus requisitos de provisionamento. Para obter informações mais detalhadas sobre o provisionamento de um aplicativo no AD do Azure e sobre as diferenças entre aplicativos de locatário único e multilocatário, confira [Integrando aplicativos com o Active Directory do Azure](active-directory-integrating-applications.md) . Continue lendo para entender os cenários comuns de aplicativos no Azure AD.

## <a name="application-types-and-scenarios"></a>Cenários e tipos de aplicativos
Cada um dos cenários descritos neste documento pode ser desenvolvido usando várias linguagens e plataformas. Todos eles contam com exemplos de código completos que estão disponíveis em nosso [guia Exemplos de Código](active-directory-code-samples.md) ou diretamente nos [repositórios de exemplo do GitHub](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=active-directory) correspondentes. Além disso, se seu aplicativo precisar de uma parte ou segmento específico de um cenário de ponta a ponta, na maioria dos casos, essa funcionalidade pode ser adicionada independentemente. Por exemplo, se você tiver um aplicativo nativo que chama uma API da Web, você pode adicionar facilmente um aplicativo Web que também chama a API da Web. O diagrama a seguir ilustra esses cenários e tipos de aplicativos e como os diferentes componentes podem ser adicionados:

![Cenários e tipos de aplicativos](./media/active-directory-authentication-scenarios/application_types_and_scenarios.png)

Estes são os cinco cenários de aplicativo principais suportados pelo Azure AD:

* [Navegador da Web para aplicativo Web](#web-browser-to-web-application): um usuário precisa entrar em um aplicativo Web protegido pelo Azure AD.
* [SPA (Aplicativo de Página Única)](#single-page-application-spa): um usuário precisa entrar em um aplicativo de página única protegida pelo Azure AD.
* [Aplicativo nativo para API da Web](#native-application-to-web-api): um aplicativo nativo que é executado em um telefone, tablet ou PC precisa autenticar um usuário para obter recursos de uma API da Web que é protegida pelo Azure AD.
* [Aplicativo Web para API da Web](#web-application-to-web-api): um aplicativo Web precisa obter recursos de uma API da Web protegida pelo Azure AD.
* [Aplicativo daemon ou de servidor para API da Web](#daemon-or-server-application-to-web-api): um aplicativo daemon ou um aplicativo de servidor sem interface do usuário da Web precisa obter recursos de uma API da Web protegida pelo Azure AD.

### <a name="web-browser-to-web-application"></a>Navegador da Web para o aplicativo Web
Esta seção descreve um aplicativo que autentica um usuário em um navegador da Web para um aplicativo Web. Nesse cenário, o aplicativo Web direciona o navegador do usuário para que ele entre no Azure AD. O Azure AD retorna uma resposta de logon por meio do navegador do usuário, que contém declarações sobre o usuário em um token de segurança. Este cenário oferece suporte ao logon usando os protocolos WS-Federation, SAML 2.0 e OpenID Connect.

#### <a name="diagram"></a>Diagrama
![Fluxo de autenticação do navegador para aplicativo Web](./media/active-directory-authentication-scenarios/web_browser_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo
1. Quando um usuário visita o aplicativo e precisa fazer logon, eles são redirecionados por meio de uma solicitação de logon para o ponto de extremidade de autenticação no Azure AD.
2. O usuário faz logon na página de logon.
3. Se a autenticação for bem-sucedida, o Azure AD criará um token de autenticação e retornará uma resposta de conexão para a URL de Resposta do aplicativo que foi configurada no Portal do Azure. Para um aplicativo de produção, essa URL de resposta deve ser HTTPS. O token retornado inclui declarações sobre o usuário e o Azure AD que são exigidas pelo aplicativo para validar o token.
4. O aplicativo valida o token usando uma chave de assinatura pública e informações do emissor disponíveis no documento de metadados de federação para o Azure AD. Depois que o aplicativo valida o token, o Azure AD inicia uma nova sessão com o usuário. Esta sessão permite que o usuário acesse o aplicativo até expirar.

#### <a name="code-samples"></a>Exemplos de código
Consulte os exemplos de código do navegador da Web para cenários de aplicativo Web. Além disso, volte com frequência, adicionamos novos exemplos o tempo todo. [Navegador da Web para aplicativo Web](active-directory-code-samples.md#web-browser-to-web-application).

#### <a name="registering"></a>Registro
* Locatário Único: se você estiver criando um aplicativo apenas para a sua organização, ele deverá ser registrado no diretório da sua empresa usando o Portal do Azure.
* Multilocatário: Se você estiver criando um aplicativo que pode ser usado por usuários fora da sua organização, ele deve ser registrado no diretório da sua empresa, mas também deve ser registrado no diretório de cada organização que usará o aplicativo. Para disponibilizar seu aplicativo em seu diretório, você pode incluir um processo de inscrição para os clientes que os permita ter autorização para seu aplicativo. Ao se inscreverem para seu aplicativo, uma caixa de diálogo será apresentada, mostrando as permissões exigidas pelo aplicativo e, em seguida, a opção de consentimento. Dependendo das permissões necessárias, um administrador na outra organização talvez precise dar consentimento. Quando o usuário ou administrador der seu consentimento, o aplicativo é registrado em seu diretório. Para saber mais, confira [Integrando aplicativos com o Active Directory do Azure](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Expiração do token
A sessão do usuário expira quando a vida útil do token emitido pelo Azure AD expira. Seu aplicativo pode encurtar esse período de tempo, se desejado, por exemplo, desconectando usuários com base em um período de inatividade. Quando a sessão expirar, o usuário deverá fazer logon novamente.

### <a name="single-page-application-spa"></a>SPA (Aplicativo de Página Única)
Esta seção descreve a autenticação para um Aplicativo de Página Única que usa o Azure AD e a concessão de autorização implícita de OAuth 2.0 para proteger o back-end de sua API da Web. Aplicativos de Página Única normalmente são estruturados como uma camada de apresentação de JavaScript (front-end) que é executada no navegador e um back-end de API da Web que é executado em um servidor e implementa a lógica comercial do aplicativo. Para saber mais sobre a concessão de autorização implícita e ajudá-lo a decidir se ela é adequada para seu cenário de aplicativo, consulte [Noções básicas sobre o fluxo de concessão implícita de OAuth2 no Azure Active Directory](active-directory-dev-understanding-oauth2-implicit-grant.md).

Nesse cenário, quando o usuário faz logon, o front-end de JavaScript usa a [Biblioteca de Autenticação do Active Directory para JavaScript (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js/tree/dev) e a concessão de autorização implícita para obter um token de ID (id_token) do Azure AD. O token é armazenado em cache e o cliente o anexa à solicitação como o token de portador ao fazer chamadas paro o back-end de sua API da Web, que é protegido usando o middleware OWIN. 

#### <a name="diagram"></a>Diagrama
![Diagrama de Aplicativo de Página Única](./media/active-directory-authentication-scenarios/single_page_app.png)

#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo
1. O usuário navega até o aplicativo Web.
2. O aplicativo retorna o front-end de JavaScript (camada de apresentação) para o navegador.
3. O usuário inicia o logon, por exemplo, clicando em um link de logon. O navegador envia um GET ao ponto de extremidade de autorização do Azure AD para solicitar um token de ID. Essa solicitação inclui a ID do aplicativo e a URL de resposta nos parâmetros de consulta.
4. O Azure AD valida a URL de Resposta em relação à URL de Resposta registrada que foi configurada no Portal do Azure.
5. O usuário faz logon na página de logon.
6. Se a autenticação for bem-sucedida, o Azure AD cria um token de ID e o retorna como um fragmento de URL (#) à URL de resposta do aplicativo. Para um aplicativo de produção, essa URL de resposta deve ser HTTPS. O token retornado inclui declarações sobre o usuário e o Azure AD que são exigidas pelo aplicativo para validar o token.
7. O código de cliente do JavaScript em execução no navegador extrai o token da resposta para usá-lo para proteger chamadas ao back-end da API da Web do aplicativo.
8. O navegador chama o back-end da API da Web do aplicativo com o token de acesso no cabeçalho de autorização.

#### <a name="code-samples"></a>Exemplos de código
Consulte os exemplos de código para cenários de Aplicativo de Página Única (SPA). Certifique-se de voltar com frequência, adicionamos novos exemplos o tempo todo. [SPA (Aplicativo de Página Única)](active-directory-code-samples.md#single-page-application-spa).

#### <a name="registering"></a>Registro
* Locatário Único: se você estiver criando um aplicativo apenas para a sua organização, ele deverá ser registrado no diretório da sua empresa usando o Portal do Azure.
* Multilocatário: Se você estiver criando um aplicativo que pode ser usado por usuários fora da sua organização, ele deve ser registrado no diretório da sua empresa, mas também deve ser registrado no diretório de cada organização que usará o aplicativo. Para disponibilizar seu aplicativo em seu diretório, você pode incluir um processo de inscrição para os clientes que os permita ter autorização para seu aplicativo. Ao se inscreverem para seu aplicativo, uma caixa de diálogo será apresentada, mostrando as permissões exigidas pelo aplicativo e, em seguida, a opção de consentimento. Dependendo das permissões necessárias, um administrador na outra organização talvez precise dar consentimento. Quando o usuário ou administrador der seu consentimento, o aplicativo é registrado em seu diretório. Para saber mais, confira [Integrando aplicativos com o Active Directory do Azure](active-directory-integrating-applications.md).

Depois de registrar o aplicativo, ele deve ser configurado para usar o protocolo OAuth 2.0 de concessão implícita. Por padrão, esse protocolo é desabilitado para aplicativos. Para habilitar o protocolo de Concessão Implícita OAuth2 para seu aplicativo, edite seu manifesto do aplicativo no Portal do Azure e defina o valor de "oauth2AllowImplicitFlow" como true. Para obter instruções detalhadas, consulte [Habilitar a concessão implícita do OAuth 2.0 para aplicativos de página única](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Expiração do token
Quando você usa ADAL.js para gerenciar a autenticação com o Azure AD, você aproveita vários recursos que facilitam a atualização de um token expirado, bem como a obtenção de tokens para recursos de API da Web adicionais que podem ser chamados pelo aplicativo. Quando o usuário é autenticado com sucesso no Azure AD, é estabelecida uma sessão protegida por um cookie para o usuário entre o navegador e o Azure AD. É importante observar que a sessão existe entre o usuário e o Azure AD e não entre o usuário e o aplicativo Web em execução no servidor. Quando um token expira, o ADAL.js usa essa sessão para obter silenciosamente outro token. Ele faz isso usando um iFrame oculto para enviar e receber a solicitação usando o protocolo de concessão implícita do OAuth. O ADAL.js também pode usar o mesmo mecanismo para obter silenciosamente os tokens de acesso do Azure AD para outros recursos da API da Web que o aplicativo chama, contanto que esses recursos ofereçam suporte a CORS (compartilhamento de recursos entre origens), que eles estejam registrados no diretório do usuário e que qualquer consentimento necessário tenha sido fornecido pelo usuário durante o logon.

### <a name="native-application-to-web-api"></a>Aplicativo nativo para API da Web
Esta seção descreve um aplicativo nativo que chama uma API da Web em nome de um usuário. Este cenário se baseia no tipo de concessão de código de autorização OAuth 2.0 com um cliente público, conforme descrito na seção 4.1 da [especificação do OAuth 2.0](http://tools.ietf.org/html/rfc6749). O aplicativo nativo obtém um token de acesso para o usuário usando o protocolo OAuth 2.0. Esse token de acesso é enviado na solicitação para a API da Web, que autoriza o usuário e retorna o recurso desejado.

#### <a name="diagram"></a>Diagrama
![Diagrama de aplicativo nativo para API da Web](./media/active-directory-authentication-scenarios/native_app_to_web_api.png)

#### <a name="authentication-flow-for-native-application-to-api"></a>Fluxo de autenticação do aplicativo nativo para API
#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo
Se você estiver usando as bibliotecas de autenticação do AD, a maioria dos detalhes do protocolo descritos abaixo será manipulada para você, como o pop-up do navegador, o armazenamento em cache do token e a manipulação de tokens de atualização.

1. Usando um pop-up de navegador, o aplicativo nativo faz uma solicitação para o ponto de extremidade de autorização no Azure AD. Essa solicitação inclui a ID do Aplicativo e o URI de redirecionamento do aplicativo nativo, conforme mostrado no Portal do Azure, e o URI de ID do aplicativo para a API da Web. Se o usuário ainda não tiver feito logon, ele será solicitado a fazer logon novamente
2. O Azure AD autentica o usuário. Se for um aplicativo multilocatário e for necessário um consentimento para usar o aplicativo, o usuário deverá consentir, caso ainda não tenha feito isso. Depois de conceder autorização e após a autenticação bem-sucedida, o Azure AD emite uma resposta de código de autorização para o URI de redirecionamento do aplicativo cliente.
3. Quando o Azure AD emite uma resposta de código de autorização para o URI de redirecionamento, o aplicativo cliente interrompe a interação com o navegador e extrai o código de autorização da resposta. Usando esse código de autorização, o aplicativo cliente envia uma solicitação ao ponto de extremidade do token do Azure AD que inclui o código de autorização, detalhes sobre o aplicativo cliente (ID do Aplicativo e o URI de redirecionamento) e o recurso desejado (URI de ID do aplicativo para a API da Web).
4. O código de autorização e informações sobre o aplicativo cliente e a API da Web são validados pelo Azure AD. Após a validação bem-sucedida, o Azure AD retorna dois tokens: um token de acesso JWT e um token de atualização JWT. Além disso, o Azure AD retorna informações básicas sobre o usuário, como seu nome de exibição e ID do locatário.
5. Via HTTPS, o aplicativo cliente usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "Portador" no cabeçalho de autorização da solicitação à API da Web. A API da Web, em seguida, valida o token JWT e, se a validação for bem-sucedida, retorna o recurso desejado.
6. Quando o token de acesso expira, o aplicativo cliente recebe um erro que indica que o usuário precisa se autenticar novamente. Se o aplicativo tiver um token de atualização válido, ele pode ser usado para adquirir um novo token de acesso sem que um novo logon do usuário seja solicitado. Se o token de atualização expirar, o aplicativo precisará autenticar interativamente o usuário novamente.

> [!NOTE]
> O token de atualização emitido pelo Azure AD pode ser usado para acessar vários recursos. Por exemplo, se você tiver um aplicativo cliente que tenha permissão para chamar duas APIs da Web, o token de atualização pode ser usado para obter um token de acesso para a outra API da Web também.
> 
> 

#### <a name="code-samples"></a>Exemplos de código
Consulte os exemplos de código para os cenários de aplicativo nativo para API da Web. Além disso, volte com frequência, adicionamos novos exemplos o tempo todo. [Aplicativo nativo para API da Web](active-directory-code-samples.md#native-application-to-web-api)

#### <a name="registering"></a>Registro
* Locatário único: O aplicativo nativo e a API da Web devem ser registrados no mesmo diretório no Azure AD. A API da Web pode ser configurada para expor um conjunto de permissões, que são usadas para limitar o acesso do aplicativo nativo a seus recursos. O aplicativo cliente seleciona as permissões desejadas no menu suspenso "Permissões para Outros Aplicativos" no Portal do Azure.
* Multilocatário: Primeiro, o aplicativo nativo registrado apenas no diretório do desenvolvedor ou do editor. Em segundo lugar, o aplicativo nativo é configurado para indicar as permissões necessárias para que seja funcional. Essa lista de permissões necessárias é mostrada em uma caixa de diálogo quando um usuário ou administrador no diretório de destino dá consentimento para o aplicativo, o que o torna disponível para sua organização. Alguns aplicativos exigem apenas permissões de nível de usuário, que qualquer usuário na organização pode conceder. Outros aplicativos exigem permissões de nível administrativo, que um usuário na organização não pode conceder. Somente um administrador do diretório pode dar consentimento para aplicativos que exigem esse nível de permissões. Quando o usuário ou administrador concede sua permissão, somente a API da Web é registrada no diretório. Para saber mais, confira [Integrando aplicativos com o Active Directory do Azure](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Expiração do token
Quando o aplicativo nativo usa seu código de autorização para obter um token de acesso JWT, ele também recebe um token de atualização JWT. Quando o token de acesso expira, o token de atualização pode ser usado para autenticar o usuário novamente sem a necessidade de fazer logon novamente. Esse token de atualização é usado para autenticar o usuário, o que resulta em um novo token de acesso e token de atualização.

### <a name="web-application-to-web-api"></a>Aplicativo Web para API da Web
Esta seção descreve um aplicativo Web que precisa obter recursos de uma API da Web. Nesse cenário, há dois tipos de identidade que o aplicativo Web pode usar para autenticar e chamar a API da Web: uma identidade de aplicativo ou uma identidade de usuário delegada.

*Identidade de aplicativo:* esse cenário usa a concessão de credenciais do cliente OAuth 2.0 para se autenticar como o aplicativo e acessar a API da Web. Ao usar uma identidade de aplicativo, a API da Web só pode detectar que o aplicativo Web a está chamando, uma vez que a API Web não recebe nenhuma informação sobre o usuário. Se o aplicativo receber informações sobre o usuário, ele será enviado por meio do protocolo do aplicativo e ele não será assinado pelo Azure AD. A API da Web confia que o aplicativo Web autenticou o usuário. Por esse motivo, esse padrão é chamado de subsistema confiável.

*Identidade de usuário delegado:* o cenário pode ser realizado de duas maneiras, OpenID Connect e concessão de código de autorização OAuth 2.0 com um cliente confidencial. O aplicativo Web obtém um token de acesso para o usuário, o que prova para a API da Web que o usuário foi autenticado com êxito para o aplicativo Web e que o aplicativo Web foi capaz de obter uma identidade de usuário delegado para chamar a API da Web. Esse token de acesso é enviado na solicitação para a API da Web, que autoriza o usuário e retorna o recurso desejado.

#### <a name="diagram"></a>Diagrama
![Diagrama de aplicativo Web para API da Web](./media/active-directory-authentication-scenarios/web_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo
A identidade do aplicativo e os tipos de identidade do usuário delegado são discutidos no fluxo abaixo. A principal diferença entre eles é que a identidade de usuário delegado deve primeiramente adquirir um código de autorização antes que o usuário possa fazer login e acessar a API da Web.

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identidade do aplicativo com concessão de credenciais do cliente OAuth 2.0
1. Um usuário está conectado ao AD do Azure no aplicativo Web (veja a seção [Navegador da Web para aplicativo Web](#web-browser-to-web-application) acima).
2. O aplicativo Web precisa adquirir um token de acesso para que ele possa autenticar a API da Web e recuperar o recurso desejado. Ele faz uma solicitação ao ponto de extremidade do token do Azure AD, fornecendo a credencial, a ID do Aplicativo e o URI de ID do aplicativo da API da Web.
3. O Azure AD autentica o aplicativo e retorna um token de acesso JWT que é usado para chamar a API da Web.
4. Via HTTPS, o aplicativo Web usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "Portador" no cabeçalho de autorização da solicitação à API da Web. A API da Web, em seguida, valida o token JWT e, se a validação for bem-sucedida, retorna o recurso desejado.

##### <a name="delegated-user-identity-with-openid-connect"></a>Identidade de usuário delegado com OpenID Connect
1. Um usuário está conectado a um aplicativo Web usando o AD do Azure (veja a seção [Navegador da Web para aplicativo Web](#web-browser-to-web-application) acima). Se o usuário do aplicativo Web ainda não tiver consentido para permitir que o aplicativo Web chame a API da Web em seu nome, o usuário precisará de consentimento. O aplicativo exibirá as permissões que ele exige e se qualquer uma dessas permissões for de nível administrativo, um usuário normal no diretório não poderá conceder a permissão. Esse processo de consentimento só se aplica a aplicativos multilocatários, não a aplicativos de locatário único, uma vez que o aplicativo já terá as permissões necessárias. Quando o usuário tiver feito login, o aplicativo terá recebido um token de ID com informações sobre o usuário, bem como um código de autorização.
2. Usando o código de autorização emitido pelo Azure AD, o aplicativo Web envia uma solicitação ao ponto de extremidade do token do Azure AD que inclui o código de autorização, detalhes sobre o aplicativo cliente (ID do Aplicativo e o URI de redirecionamento) e o recurso desejado (URI de ID do aplicativo para a API da Web).
3. O código de autorização e informações sobre o aplicativo Web e a API da Web são validados pelo Azure AD. Após a validação bem-sucedida, o Azure AD retorna dois tokens: um token de acesso JWT e um token de atualização JWT.
4. Via HTTPS, o aplicativo Web usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "Portador" no cabeçalho de autorização da solicitação à API da Web. A API da Web, em seguida, valida o token JWT e, se a validação for bem-sucedida, retorna o recurso desejado.

##### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Identidade de usuário delegado com concessão de código de autorização do OAuth 2.0
1. Um usuário já está conectado a um aplicativo web, cujo mecanismo de autenticação é independente do Azure AD.
2. O aplicativo Web requer um código de autorização para adquirir um token de acesso, por isso, ele emite uma solicitação por meio do navegador ao ponto de extremidade de autorização do Azure AD, fornecendo a ID do Aplicativo e o URI de redirecionamento para o aplicativo Web após uma autenticação bem-sucedida. O usuário faz login no Azure AD.
3. Se o usuário do aplicativo Web ainda não tiver consentido para permitir que o aplicativo Web chame a API da Web em seu nome, o usuário precisará de consentimento. O aplicativo exibirá as permissões que ele exige e se qualquer uma dessas permissões for de nível administrativo, um usuário normal no diretório não poderá conceder a permissão. Esse consentimento se aplica ao aplicativo de único locatário e multilocatário.  No caso de único locatário, um administrador pode executar o consentimento de administrador para consentir em nome de seus usuários.  Isso pode ser feito usando o botão `Grant Permissions` no [portal do Azure](https://portal.azure.com). 
4. Depois que o usuário tiver concedido permissão, o aplicativo Web recebe o código de autorização de que precisa para adquirir um token de acesso.
5. Usando o código de autorização emitido pelo Azure AD, o aplicativo Web envia uma solicitação ao ponto de extremidade do token do Azure AD que inclui o código de autorização, detalhes sobre o aplicativo cliente (ID do Aplicativo e o URI de redirecionamento) e o recurso desejado (URI de ID do aplicativo para a API da Web).
6. O código de autorização e informações sobre o aplicativo Web e a API da Web são validados pelo Azure AD. Após a validação bem-sucedida, o Azure AD retorna dois tokens: um token de acesso JWT e um token de atualização JWT.
7. Via HTTPS, o aplicativo Web usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "Portador" no cabeçalho de autorização da solicitação à API da Web. A API da Web, em seguida, valida o token JWT e, se a validação for bem-sucedida, retorna o recurso desejado.

#### <a name="code-samples"></a>Exemplos de código
Consulte os exemplos de código para os cenários de aplicativo Web para API da Web. Além disso, volte com frequência, adicionamos novos exemplos o tempo todo. [Aplicativo Web para API da Web](active-directory-code-samples.md#web-application-to-web-api).

#### <a name="registering"></a>Registro
* Locatário único: para a identidade do aplicativo e casos de identidade de usuário delegado, o aplicativo Web e a API da Web devem ser registrados no mesmo diretório no Azure AD. A API da Web pode ser configurada para expor um conjunto de permissões, que são usadas para limitar o acesso do aplicativo Web a seus recursos. Se um tipo de identidade de usuário delegado estiver sendo usado, o aplicativo Web precisará selecionar as permissões desejadas no menu suspenso "Permissões para Outros Aplicativos" no Portal do Azure. Essa etapa não será necessária se o tipo de identidade de aplicativo estiver sendo usado.
* Multilocatário: Primeiro, o aplicativo Web é configurado para indicar as permissões necessárias para que seja funcional. Essa lista de permissões necessárias é mostrada em uma caixa de diálogo quando um usuário ou administrador no diretório de destino dá consentimento para o aplicativo, o que o torna disponível para sua organização. Alguns aplicativos exigem apenas permissões de nível de usuário, que qualquer usuário na organização pode conceder. Outros aplicativos exigem permissões de nível administrativo, que um usuário na organização não pode conceder. Somente um administrador do diretório pode dar consentimento para aplicativos que exigem esse nível de permissões. Quando o usuário ou administrador conceder sua permissão, o aplicativo Web e a API da Web são registrados em seu diretório.

#### <a name="token-expiration"></a>Expiração do token
Quando o aplicativo Web usa seu código de autorização para obter um token de acesso JWT, ele também recebe um token de atualização JWT. Quando o token de acesso expira, o token de atualização pode ser usado para autenticar o usuário novamente sem a necessidade de fazer logon novamente. Esse token de atualização é usado para autenticar o usuário, o que resulta em um novo token de acesso e token de atualização.

### <a name="daemon-or-server-application-to-web-api"></a>Servidor ou aplicativo Daemon para API da Web
Esta seção descreve um aplicativo daemon ou de servidor que precisa obter recursos de uma API da Web. Há dois sub-cenários que se aplicam a esta seção: um daemon que precisa chamar uma API da Web, baseado no tipo de concessão de credenciais de cliente OAuth 2.0; e um aplicativo de servidor (por exemplo, uma API da Web) que precisa chamar uma API da Web, baseada na especificação de rascunho OAuth 2.0 On-Behalf-Of.

Para o cenário em que um aplicativo daemon precisa chamar uma API da Web, é importante entender algumas coisas. Primeiro, a interação do usuário não é possível com um aplicativo daemon, que requer que o aplicativo tenha sua própria identidade. Um exemplo de um aplicativo daemon é um trabalho em lotes ou um serviço de sistema operacional sendo executado em segundo plano. Esse tipo de aplicativo solicita um token de acesso usando sua identidade de aplicativo e apresentando sua ID de Aplicativo, credenciais (senha ou certificado) e URI de ID do aplicativo para o Azure AD. Após a autenticação bem-sucedida, o daemon recebe um token de acesso do Azure AD, que é usado para chamar a API da Web.

Para o cenário em que um aplicativo de servidor precisa chamar uma API da Web, é útil usar um exemplo. Imagine que um usuário tenha se autenticado em um aplicativo nativo e que esse aplicativo nativo precisa chamar uma API da Web. O Azure AD emite um token de acesso JWT para chamar a API da Web. Se a API da Web precisar chamar outra API da Web de downstream, ele pode usar o fluxo on-behalf-of para delegar a identidade do usuário e se autenticar à API da Web de segunda camada.

#### <a name="diagram"></a>Diagrama
![Aplicativo Daemon ou de servidor para diagrama da API da Web](./media/active-directory-authentication-scenarios/daemon_server_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo
##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identidade do aplicativo com concessão de credenciais do cliente OAuth 2.0
1. Primeiro, o aplicativo de servidor precisa se autenticar no Azure AD ele mesmo, sem qualquer interação humana, como uma caixa de diálogo de logon interativa. Ele faz uma solicitação ao ponto de extremidade do token do Azure AD, fornecendo a credencial, a ID do Aplicativo e o URI de ID do aplicativo.
2. O Azure AD autentica o aplicativo e retorna um token de acesso JWT que é usado para chamar a API da Web.
3. Via HTTPS, o aplicativo Web usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "Portador" no cabeçalho de autorização da solicitação à API da Web. A API da Web, em seguida, valida o token JWT e, se a validação for bem-sucedida, retorna o recurso desejado.

##### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Identidade de usuário delegado com a especificação de rascunho do OAuth 2.0 On-Behalf-Of
O fluxo discutido abaixo pressupõe que um usuário tenha sido autenticado em outro aplicativo (como um aplicativo nativo) e sua identidade de usuário tiver sido usada para adquirir um token de acesso à API da Web de primeira camada.

1. O aplicativo nativo envia o token de acesso à API da Web de primeira camada.
2. A API da Web de primeira camada envia uma solicitação ao ponto de extremidade do token do Azure AD, fornecendo sua ID de Aplicativo e as credenciais, bem como o token de acesso do usuário. Além disso, a solicitação é enviada com um parâmetro on_behalf_of que indica que a API da Web está solicitando novos tokens para chamar uma API da Web downstream em nome do usuário original.
3. O Azure AD verifica se a API da Web de primeira camada tem permissões para acessar a API da Web de segunda camada e valida a solicitação, retornando um token de acesso JWT e um token de atualização JWT para a API da Web de primeira camada.
4. Via HTTPS, a API da Web de primeira camada chama a API da Web de segunda camada, acrescentando a cadeia de caracteres do token no cabeçalho de autorização na solicitação. A API da Web de primeira camada pode continuar a chamar a API da Web de segunda camada, desde que o token de acesso e os tokens de atualização sejam válidos.

#### <a name="code-samples"></a>Exemplos de código
Consulte os exemplos de código para os cenários de aplicativo daemon ou de servidor para API da Web. Além disso, volte com frequência, adicionamos novos exemplos o tempo todo. [Aplicativo daemon ou de servidor para API da Web](active-directory-code-samples.md#server-or-daemon-application-to-web-api)

#### <a name="registering"></a>Registro
* Locatário único: para a identidade do aplicativo e casos de identidade de usuário delegado, o aplicativo daemon ou de servidor deve ser registrado no mesmo diretório no Azure AD. A API da Web pode ser configurada para expor um conjunto de permissões, que são usadas para limitar o acesso do daemon ou do servidor a seus recursos. Se um tipo de identidade de usuário delegado estiver sendo usado, o aplicativo de servidor precisará selecionar as permissões desejadas no menu suspenso "Permissões para Outros Aplicativos" no Portal do Azure. Essa etapa não será necessária se o tipo de identidade de aplicativo estiver sendo usado.
* Multilocatário: Primeiro, o aplicativo deamon ou de servidor é configurado para indicar as permissões necessárias para que seja funcional. Essa lista de permissões necessárias é mostrada em uma caixa de diálogo quando um usuário ou administrador no diretório de destino dá consentimento para o aplicativo, o que o torna disponível para sua organização. Alguns aplicativos exigem apenas permissões de nível de usuário, que qualquer usuário na organização pode conceder. Outros aplicativos exigem permissões de nível administrativo, que um usuário na organização não pode conceder. Somente um administrador do diretório pode dar consentimento para aplicativos que exigem esse nível de permissões. Quando o usuário ou administrador concede permissão, ambas as APIs Web são registradas no diretório delas.

#### <a name="token-expiration"></a>Expiração do token
Quando o primeiro aplicativo usa seu código de autorização para obter um token de acesso JWT, ele também recebe um token de atualização JWT. Quando o token de acesso expira, o token de atualização pode ser usado para autenticar o usuário novamente sem solicitar credenciais. Esse token de atualização é usado para autenticar o usuário, o que resulta em um novo token de acesso e token de atualização.

## <a name="see-also"></a>Consulte também
[Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md)

[Exemplos de código do Azure Active Directory](active-directory-code-samples.md)

[Informações importantes sobre a substituição da chave de assinatura no Azure AD](active-directory-signing-key-rollover.md)

[OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx)

