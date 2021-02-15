---
title: Visão geral técnica e de recursos – Azure Active Directory B2C
description: Uma introdução detalhada aos recursos e às tecnologias no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fef192ab273a0e53958cda315932f91b427a5d7d
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108376"
---
# <a name="technical-and-feature-overview-of-azure-active-directory-b2c"></a>Visão geral técnica e de recursos do Azure Active Directory B2C

Um complemento do artigo [Sobre o Azure Active Directory B2C](overview.md), este artigo fornece uma introdução mais detalhada ao serviço. Discutimos aqui os principais recursos com os quais você trabalha no serviço, seus recursos e como eles permitem que você forneça uma experiência de identidade totalmente personalizada para seus clientes em seus aplicativos.

## <a name="azure-ad-b2c-tenant"></a>Locatário do Azure AD B2C

No Azure AD B2C (Azure Active Directory B2C), um *locatário* representa a sua organização e é um diretório de usuários. Cada locatário do Azure AD B2C é distinto e separado de outros locatários do Azure AD B2C. Um locatário do Azure AD B2C é diferente de um locatário do Azure Active Directory, que talvez você já tenha.

Os principais recursos com os quais você trabalha em um locatário do Azure AD B2C são:

* **Diretório** – O *diretório* é onde o Azure AD B2C armazena as credenciais de seus usuários e os dados de perfil, bem como os registros do aplicativo.
* **Registros de aplicativo** – Você registra seus aplicativos Web, móveis e nativos com o Azure AD B2C para habilitar o gerenciamento de identidades. Além disso, todas as APIs que você deseja proteger com o Azure AD B2C.
* **Fluxos dos usuários** e **políticas personalizadas** – As experiências de identidade interna (fluxos dos usuários) e totalmente personalizável (políticas personalizadas) para seus aplicativos.
  * Use os *fluxos dos usuários* para configuração rápida e habilitação de tarefas de identidade comuns, como inscrever, entrar e edição de perfil.
  * Use *políticas personalizadas* para habilitar experiências de usuário não apenas para tarefas de identidade comuns, mas também para criar suporte para fluxos de trabalho de identidade complexos exclusivos para sua organização, seus clientes, funcionários, parceiros e cidadãos.
* **Provedores de identidade** – Configurações de federação para:
  * Provedores de identidade *social*, como Facebook, LinkedIn ou Twitter, a que você deseja dar suporte em seus aplicativos.
  * Provedores de identidade *externos* que dão suporte a protocolos de identidade padrão como OAuth 2.0, OpenID Connect e muito mais.
  * As contas *locais* que permitem que os usuários se inscrevam e entrem com um nome de usuário (ou endereço de email ou outra ID) e senha.
* **Chaves**: adicione e gerencie chaves de criptografia para assinar e validar tokens, segredos do cliente, certificados e senhas.

Um locatário do Azure AD B2C é o primeiro recurso que você precisa criar para começar a usar o Azure AD B2C. Saiba como em [Tutorial: Criar um locatário do Azure Active Directory B2C](tutorial-create-tenant.md).

## <a name="accounts-in-azure-ad-b2c"></a>Contas no Azure AD B2C

O Azure AD B2C define vários tipos de contas de usuário. O Azure Active Directory, o Azure Active Directory B2B e o Azure Active Directory B2C compartilham esses tipos de conta.

* **Conta corporativa** – Os usuários com contas corporativas podem gerenciar recursos em um locatário e, com uma função de administrador, também podem gerenciar locatários. Usuários com contas corporativas podem criar contas de consumidor, redefinir senhas, bloquear/desbloquear contas e definir permissões ou atribuir uma conta a um grupo de segurança.
* **Conta convidado** – Usuários externos que você convida para seu locatário como convidados. Um cenário típico para convidar um usuário convidado para o locatário do Azure AD B2C é compartilhar responsabilidades de administração.
* **Conta de consumidor** – As contas de consumidor são aquelas criadas no diretório Azure AD B2C quando os usuários concluem o percurso do usuário de inscrição em um aplicativo que você registrou em seu locatário.

![Página de gerenciamento de usuário do Azure AD B2C no portal do Azure](media/technical-overview/portal-01-users.png)<br/>*Figura: Diretório do usuário dentro de um locatário do Azure AD B2C no portal do Azure*

### <a name="consumer-accounts"></a>Contas de consumidor

Com uma conta de *consumidor*, os usuários podem entrar nos aplicativos que você protegeu com o Azure AD B2C. No entanto, os usuários com contas de consumidor não podem acessar recursos do Azure, por exemplo, o portal do Azure.

Uma conta de consumidor pode ser associada a estes tipos de identidade:

* Identidade **local**, com o nome de usuário e a senha armazenados localmente no diretório Azure AD B2C. Geralmente, nos referimos a essas identidades como "contas locais".
* Identidades **sociais** ou **empresariais**, em que a identidade do usuário é gerenciada por um provedor de identidade federado, como Facebook, Microsoft, ADFS ou Salesforce.

Um usuário com uma conta de consumidor pode entrar com várias identidades, por exemplo, nome de usuário, email, ID do funcionário, ID do governo e outros. Uma única conta pode ter várias identidades locais e sociais.

![Identidades da conta de consumidor](media/technical-overview/identities.png)<br/>*Figura: Uma única conta de consumidor com várias identidades no Azure AD B2C*

O Azure AD B2C permite que você gerencie atributos comuns de perfis de conta de consumidor como nome de exibição, sobrenome, nome, cidade e outros. Você também pode estender o esquema do Azure AD para armazenar informações adicionais sobre seus usuários. Por exemplo, seu país/região ou residência, idioma preferencial e preferências, como se desejam assinar um boletim informativo ou habilitar a autenticação multifator.

Saiba mais sobre os tipos de conta de usuário no Azure AD B2C em [Visão geral de contas de usuário no Azure Active Directory B2C](user-overview.md).

## <a name="external-identity-providers"></a>Provedores de identidade externos

Você pode configurar o Azure AD B2C para permitir que os usuários entrem em seu aplicativo com credenciais de IdP (provedores de identidade) social ou empresarial externos. O Azure AD B2C dá suporte a provedores de identidade externos, como Facebook, conta Microsoft, Google, Twitter e qualquer provedor de identidade que dê suporte aos protocolos OAuth 1.0, OAuth 2.0, OpenID Connect e SAML.

![Provedores de identidade externos](media/technical-overview/external-idps.png)

Com a federação do provedor de identidade externa, você pode oferecer aos consumidores a capacidade de entrar com suas contas sociais ou empresariais existentes, sem precisar criar uma conta apenas para seu aplicativo.

Na página de inscrição ou de entrada, o Azure AD B2C apresenta uma lista de provedores de identidade externos que o usuário pode escolher para entrar. Depois de selecionar um dos provedores de identidade externos, o usuário é redirecionado para o site do provedor selecionado para concluir o processo de entrada. Depois de entrar com êxito, o usuário será levado de volta para o Azure AD B2C para autenticação da conta em seu aplicativo.

![Exemplo de entrada móvel com uma conta social (Facebook)](media/technical-overview/external-idp.png)

Para ver como adicionar provedores de identidade no Azure AD B2C, confira o [Tutorial: Adicionar provedores de identidade aos aplicativos no Azure Active Directory B2C](tutorial-add-identity-providers.md).

## <a name="identity-experiences-user-flows-or-custom-policies"></a>Experiências de identidade: fluxos dos usuários ou políticas personalizadas

A estrutura de política extensível do Azure AD B2C é seu ponto forte principal. As políticas descrevem as experiências de identidade dos usuários, tais como inscrição, entrada ou edição de perfil.

No Azure AD B2C, há dois caminhos principais que você pode tomar para fornecer essas experiências de identidade: fluxos dos usuários e políticas personalizadas.

* **Fluxos dos usuários** são políticas predefinidas, internas e configuráveis que fornecemos para que você possa criar experiências de inscrição, entrada e edição de política em minutos.

* **Políticas personalizadas** permitem que você crie seus próprios percursos do usuário para cenários de experiência de identidade complexos.

Os fluxos dos usuários e as políticas personalizadas são alimentados pelo *Identity Experience Framework*, o mecanismo de orquestração de política do Azure AD B2C.

### <a name="user-flow"></a>Fluxo de usuário

Para ajudá-lo a configurar rapidamente as tarefas de identidade mais comuns, o portal do Azure inclui diversas políticas predefinidas e configuráveis chamadas *fluxos dos usuários*.

Você pode definir configurações de fluxo de usuário como essas para controlar os comportamentos de experiência de identidade em seus aplicativos:

* Tipos de conta usados para entrar, por exemplo, contas sociais como as do Facebook ou locais que usam um endereço de email e uma senha para entrada
* Atributos a serem coletados do consumidor, tais como nome, código postal ou país/região de residência
* MFA (Autenticação Multifator) do Azure AD
* Personalização da interface do usuário
* Conjunto de declarações em um token que seu aplicativo recebe depois que o usuário conclui o fluxo do usuário
* Gerenciamento da sessão
* ... e muito mais.

Os cenários de identidade mais comuns para a maioria dos aplicativos móveis, da Web e de página única podem ser definidos e implementados com eficiência com fluxos dos usuários. Recomendamos que você use os fluxos dos usuários internos, a menos que você tenha cenários de percurso do usuário complexos que exigem a flexibilidade total das políticas personalizadas.

Saiba mais sobre fluxos dos usuários em [Fluxos dos usuários no Azure Active Directory B2C](user-flow-overview.md).

### <a name="custom-policy"></a>Política personalizada

As políticas personalizadas desbloqueiam o acesso a todo o poder do mecanismo de orquestração do IEF (Identity Experience Framework). Com as políticas personalizadas, você pode aproveitar o IEF para criar praticamente qualquer autenticação, registro de usuário ou experiência de edição de perfil que você possa imaginar.

O Identity Experience Framework oferece a capacidade de construir percursos do usuário com qualquer combinação de etapas. Por exemplo:

* Federar com outros provedores de identidade
* Desafios de MFA (autenticação multifator) de fornecedores internos e terceiros
* Coletar qualquer entrada do usuário
* Integrar a sistemas externos usando a comunicação da API REST

Cada percurso do usuário é definido por uma política. Você pode criar tantas políticas quanto precisar para habilitar a melhor experiência de usuário para sua organização.

![Diagrama que mostra um exemplo de um percurso de usuário complexo habilitado pelo IEF](media/technical-overview/custom-policy.png)

Uma política personalizada é definida por vários arquivos XML que se referenciam entre si em uma cadeia hierárquica. Os elementos XML definem o esquema de declarações, as transformações de declarações, as definições de conteúdo, os provedores de declarações, os perfis técnicos, as etapas de orquestração do percurso do usuário e outros aspectos da experiência de identidade.

A poderosa flexibilidade das políticas personalizadas é mais adequada para quando você precisa criar cenários de identidade complexos. Os desenvolvedores que configuram as políticas personalizadas devem definir as relações de confiança em detalhes para incluir pontos de extremidade de metadados, definições exatas de troca de declarações e configurar segredos, chaves e certificados, conforme necessário, para cada provedor de identidade.

Saiba mais sobre políticas personalizadas em [Políticas personalizadas no Azure Active Directory B2C](custom-policy-overview.md).

## <a name="protocols-and-tokens"></a>Protocolos e tokens

- Para aplicativos, o Azure AD B2C dá suporte aos [protocolos OAuth 2.0](protocols-overview.md), [OpenID Connect](openid-connect.md) e [SAML](connect-with-saml-service-providers.md) nos percursos do usuário. Seu aplicativo inicia o percurso do usuário emitindo solicitações de autenticação para o Azure AD B2C. O resultado de uma solicitação ao Azure AD B2C é um token de segurança, como um [token de ID, um token de acesso](tokens-overview.md) ou um token SAML. Esse token de segurança define a identidade do usuário no aplicativo.

- Para identidades externas, o Azure AD B2C dá suporte à federação com os provedores de identidade OAuth 1.0, OAuth 2.0, OpenID Connect e SAML.

O seguinte diagrama mostra como o Azure AD B2C pode se comunicar usando uma variedade de protocolos dentro do mesmo fluxo de autenticação:

![Diagrama de aplicativo cliente baseado em OIDC federado com um IdP baseado em SAML](media/technical-overview/protocols.png)

1. O aplicativo de terceira parte confiável inicia uma solicitação de autorização para o Azure AD B2C usando o OpenID Connect.
1. Quando um usuário do aplicativo opta por entrar usando um provedor de identidade externo que usa o protocolo SAML, o Azure AD B2C invoca o protocolo SAML para se comunicar com esse provedor de identidade.
1. Depois que o usuário concluir a operação de entrada com o provedor de identidade externo, o Azure AD B2C retornará o token para o aplicativo de terceira parte confiável usando o OpenID Connect.

## <a name="application-integration"></a>Integração de aplicativos

Quando um usuário deseja entrar em seu aplicativo, seja ele da Web, móvel, de área de trabalho ou um SPA (aplicativo de página única), o aplicativo inicia uma solicitação de autorização para um fluxo de usuário ou um ponto de extremidade fornecido pela política personalizada. O fluxo do usuário ou a política personalizada define e controla a experiência do usuário. Ao concluir um fluxo de usuário, por exemplo, o fluxo de *inscrição ou entrada*, o Azure AD B2C gera um token e redireciona o usuário de volta para seu aplicativo.

![Aplicativo móvel com setas mostrando o fluxo entre a página de entrada do Azure AD B2C](media/technical-overview/app-integration.png)

Vários aplicativos podem usar o mesmo fluxo de usuário ou a mesma política personalizada. Um único aplicativo pode usar vários fluxos dos usuários ou políticas personalizadas.

Por exemplo, para entrar em um aplicativo, o aplicativo usa o fluxo de usuário de *inscrição ou entrada*. Depois que o usuário tiver entrado, talvez queira editar seu perfil, para que o aplicativo inicie outra solicitação de autorização, desta vez usando o fluxo de usuário de *edição de perfil*.

## <a name="seamless-user-experiences"></a>Experiências de usuário integrada

No Azure AD B2C, você pode criar experiências de identidade dos usuários. Assim, as páginas que eles veem se mesclam diretamente com a aparência de sua marca. Você obtém um controle quase total do conteúdo HTML e CSS apresentado aos seus usuários quando eles continuam com os percursos de identidade do aplicativo. Com essa flexibilidade, você pode manter a consistência visual e da marca entre seu aplicativo e o Azure AD B2C.

![Capturas de tela da página de entrada de inscrição personalizada da marca](media/technical-overview/seamless-ux.png)

Para obter informações sobre personalização da interface do usuário, confira [Sobre a personalização da interface do usuário no Azure Active Directory B2C](customize-ui-with-html.md).

## <a name="localization"></a>Localização

A personalização de idioma no Azure AD B2C permite acomodar diferentes idiomas para atender às necessidades do cliente. A Microsoft fornece as traduções para 36 idiomas, mas você também pode fornecer suas próprias traduções para qualquer idioma. Mesmo que sua experiência seja fornecida apenas para um único idioma, você pode personalizar qualquer texto nas páginas.

![Três páginas de entrada de inscrição mostrando o texto da interface do usuário em idiomas diferentes](media/technical-overview/localization.png)

Veja como a localização funciona em [Personalização de idioma no Azure Active Directory B2C](language-customization.md).

## <a name="add-your-own-business-logic"></a>Adicionar sua própria lógica de negócios

Se você optar por usar políticas personalizadas, poderá integrar a uma API RESTful em um percurso do usuário para adicionar sua própria lógica de negócios ao percurso. Por exemplo, o Azure AD B2C pode trocar dados com um serviço RESTful para:

* Exibir mensagens de erro amigáveis personalizadas.
* Validar entrada do usuário para impedir que dados malformados persistam no diretório do usuário. Por exemplo, você pode modificar os dados inseridos pelo usuário, como colocar o primeiro nome em maiúsculas, se eles o inserirem em minúsculas.
* Aprimore os dados do usuário com a integração adicional a seus aplicativos de linha de negócios corporativos.
* Usando chamadas ReSTful, você pode enviar notificações por push, atualizar bancos de dados corporativos, executar um processo de migração de usuário, gerenciar permissões, auditar bancos de dados e muito mais.

Os programas de fidelidade são outro cenário habilitado pelo suporte do Azure AD B2C para chamar APIs REST. Por exemplo, seu serviço RESTful pode receber o endereço de email do usuário, consultar o banco de dados do cliente e retornar o número de fidelidade do usuário ao Azure AD B2C. Os dados de retorno podem ser armazenados na conta de diretório do usuário no Azure AD B2C e ser mais avaliados nas etapas seguintes na política ou incluídos no token de acesso.

![Integração de linha de negócios em um aplicativo móvel](media/technical-overview/lob-integration.png)

Você pode adicionar uma chamada à API REST em qualquer etapa no percurso do usuário definido por uma política personalizada. Por exemplo, você pode chamar uma API REST:

* Durante a entrada, logo antes de o Azure AD B2C validar as credenciais
* Imediatamente após a entrada
* Antes que o Azure AD B2C crie uma conta no diretório
* Depois que o Azure AD B2C criar uma conta no diretório
* Antes que o Azure AD B2C emita um token de acesso

Para saber como usar políticas personalizadas para integração de API RESTful no Azure AD B2C, confira [Integrar trocas de declarações da API REST na política personalizada do Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="protect-customer-identities"></a>Proteger identidades do cliente

O Azure AD B2C está em conformidade com a segurança, a privacidade e outros compromissos descritos na [Central de Confiabilidade do Microsoft Azure](https://www.microsoft.com/trustcenter/cloudservices/azure).

As sessões são modeladas como dados criptografados, com a chave de descriptografia conhecida somente pelo serviço de token de segurança do Azure AD B2C. Um algoritmo de criptografia forte, AES-192, é usado. Todos os caminhos de comunicação são protegidos com TLS para confidencialidade e integridade. Nosso serviço de token de segurança usa um certificado EV (Validação Estendida) para TLS. Em geral, o serviço de token de segurança atenua ataques XSS (script entre sites) não processando a entrada não confiável.

![Diagrama de dados seguros em trânsito e em repouso](media/technical-overview/user-data.png)

### <a name="access-to-user-data"></a>Acesso aos dados do usuário

Os locatários do Azure AD B2C compartilham muitas características com locatários empresariais do Azure Active Directory usados para funcionários e parceiros. Os aspectos compartilhados incluem mecanismos para exibição de funções administrativas, atribuição de funções e atividades de auditoria.

Você pode atribuir funções para controlar quem pode executar determinadas ações administrativas no Azure AD B2C, incluindo:

* Criar e gerenciar todos os aspectos de fluxos dos usuários
* Criar e gerenciar o esquema de atributo disponível para todos os fluxos dos usuários
* Configurar provedores de identidade para uso na federação direta
* Criar e gerenciar políticas de estrutura confiável no Identity Experience Framework (políticas personalizadas)
* Gerenciar segredos para federação e criptografia no Identity Experience Framework (políticas personalizadas)

Para obter mais informações sobre as funções do Azure AD, incluindo suporte à função de administração do Azure AD B2C, confira [Permissões de função do administrador no Azure Active Directory](../active-directory/roles/permissions-reference.md).

### <a name="multi-factor-authentication-mfa"></a>Autenticação Multifator (MFA)

A MFA (Autenticação Multifator) do Azure AD B2C ajuda a proteger o acesso a dados e aplicativos enquanto mantém a simplicidade para os usuários. Ela fornece segurança adicional exigindo uma segunda forma de autenticação e fornece autenticação forte por meio de uma variedade de métodos de autenticação fáceis de usar. Seus usuários podem ou não ser desafiados para MFA com base em decisões de configuração que você pode tomar como administrador.

Veja como habilitar a MFA nos fluxos dos usuários em [Habilitar a autenticação multifator no Azure Active Directory B2C](multi-factor-authentication.md).

### <a name="smart-account-lockout"></a>Bloqueio de conta inteligente

Para evitar tentativas de adivinhação de senha por força bruta, o Azure AD B2C usa uma estratégia sofisticada para bloquear contas com base no IP da solicitação, nas senhas inseridas e em vários outros fatores. A duração do bloqueio é aumentada automaticamente com base no risco e no número de tentativas.

![Bloqueio inteligente de conta](media/technical-overview/smart-lockout1.png)

Para obter mais informações sobre como gerenciar configurações de proteção de senha, confira [Gerenciar ameaças aos recursos e dados no Azure Active Directory B2C](threat-management.md).

### <a name="password-complexity"></a>Complexidade de senha

Durante a inscrição ou redefinição de senha, seus usuários devem fornecer uma senha que atenda às regras de complexidade. Por padrão, o Azure AD B2C impõe uma política de senha forte. O Azure AD B2C também fornece opções de configuração para especificar os requisitos de complexidade das senhas que seus clientes usam.

Você pode configurar os requisitos de complexidade de senha em [fluxos dos usuários](password-complexity.md) e [políticas personalizadas](password-complexity.md).

## <a name="auditing-and-logs"></a>Auditoria e logs

O Azure AD B2C emite os logs de auditoria que contêm informações de atividade sobre os recursos, tokens emitidos e acesso de administrador. Você pode usar esses logs de auditoria para entender a atividade da plataforma e diagnosticar problemas. As entradas do log de auditoria estarão disponíveis logo depois que a atividade que gerou o evento ocorrer.

Em um log de auditoria, que está disponível para seu locatário do Azure AD B2C ou para um usuário específico, você pode encontrar informações, incluindo:

* Atividades relacionadas à autorização de um usuário para acessar os recursos B2C (por exemplo, um administrador ao acessar uma lista de políticas de B2C)
* Atividades relacionadas aos atributos de diretório recuperados quando um administrador faz logon usando o portal do Azure
* Operações CRUD (criar, ler, atualizar e excluir) nos aplicativos B2C
* Operações CRUD nas chaves armazenadas em um contêiner de chave B2C
* Operações CRUD nos recursos B2C (por exemplo, políticas e provedores de identidade)
* Validação de credenciais de usuário e emissão de token

![Log de auditoria de usuário individual mostrado no portal do Azure](media/technical-overview/audit-log.png)

Para obter detalhes adicionais sobre logs de auditoria, confira [Acesso aos logs de auditoria do Azure AD B2C](view-audit-logs.md).

### <a name="usage-insights"></a>Uso de insights

O Azure AD B2C permite que você descubra quando as pessoas se inscrevem ou entram em seu aplicativo Web, onde os usuários estão localizados e quais navegadores e sistemas operacionais eles usam. Ao integrar o Azure Application Insights no Azure AD B2C usando políticas personalizadas, você pode obter insight sobre como as pessoas se inscrevem, entram, redefinem a senha ou editam o perfil. Com esse conhecimento, você pode tomar decisões baseadas em dados sobre os próximos ciclos de desenvolvimento.

Saiba mais sobre a análise de uso em [Rastrear o comportamento do usuário no Azure Active Directory B2C usando o Application Insights](analytics-with-application-insights.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão mais aprofundada dos recursos e dos aspectos técnicos do Azure Active Directory B2C, comece com nosso [tutorial para criar um locatário Azure Active Directory B2C](tutorial-create-tenant.md).
