---
title: Tutorial para configurar Azure Active Directory B2C com a identidade de ping
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação de Azure AD B2C com a identidade de ping
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 94e7ae93d05ae8ee35028882e14d8da74814d833
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650219"
---
# <a name="tutorial-configure-ping-identity-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Tutorial: configurar a identidade de ping com Azure Active Directory B2C para acesso híbrido seguro

Neste tutorial de exemplo, saiba como estender Azure Active Directory (AD) B2C com  [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) e [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) para habilitar o acesso híbrido seguro.

Muitas propriedades da Web existentes, como sites de comércio eletrônico e aplicativos Web expostos à Internet, são implantadas atrás de um sistema proxy, às vezes chamado de sistema de proxy reverso. Esses sistemas de proxy fornecem várias funções, incluindo pré-autenticação, imposição de políticas e roteamento de tráfego. Os casos de uso de exemplo incluem a proteção do aplicativo Web contra o tráfego da Web de entrada e o fornecimento de um gerenciamento de sessão uniforme entre implantações de servidor distribuído.

Na maioria dos casos, essa configuração inclui uma camada de conversão de autenticação que externalizes a autenticação do aplicativo Web. Os proxies inversos, por sua vez, fornecem o contexto dos usuários autenticados para os aplicativos Web, em uma forma mais simples, como um valor de cabeçalho no formato claro ou resumo. Nessa configuração, os aplicativos não estão usando nenhum token padrão do setor, como Security Assertion Markup Language (SAML), OAuth ou Open ID Connect (OIDC), em vez disso, dependem do proxy para fornecer o contexto de autenticação e manter a sessão com o agente do usuário final, como o navegador ou o aplicativo nativo. Como um serviço em execução em um "Man-in-the-middle", os proxies podem fornecer o controle de sessão final. Isso também significa que o serviço de proxy deve ser altamente eficiente e escalonável, não se torne um afunilamento ou um ponto único de falha para os aplicativos por trás do serviço de proxy. O diagrama é uma representação de uma implementação típica de proxy reverso e do fluxo das comunicações.

![imagem mostra a implementação de proxy reverso](./media/partner-ping/reverse-proxy.png)

Se você estiver em uma situação em que deseja modernizar a plataforma de identidade em tais configurações, as preocupações a seguir serão geradas.

- Como o esforço para a modernização do aplicativo pode ser dissociado da modernização da plataforma de identidade?

- Como um ambiente de coexistência pode ser estabelecido com a autenticação moderna e herdada, consumindo o provedor de serviços de identidade modernizado?

  a. Como impulsionar a consistência da experiência do usuário final?

  b. Como fornecer uma experiência de logon único em todos os aplicativos coexistentes?

Discutimos uma abordagem para resolver essas preocupações integrando Azure AD B2C com as tecnologias [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) e [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) .

## <a name="coexistence-environment"></a>Ambiente de coexistência

Uma solução simples viável tecnicamente que também é econômica é configurar o sistema proxy reverso para usar o sistema de identidade modernizado, delegando a autenticação.  
Os proxies, nesse caso, oferecerão suporte aos protocolos de autenticação modernos e usarão a autenticação baseada em redirecionamento (passiva) que enviará o usuário ao novo IdP (provedor de identidade).

### <a name="azure-ad-b2c-as-an-identity-provider"></a>Azure AD B2C como um provedor de identidade

Azure AD B2C tem a capacidade de definir **políticas** que conduzem diferentes experiências de usuário e comportamentos que também são chamados de percursos do **usuário** como orquestrados a partir da extremidade do servidor. Cada política desse tipo expõe um ponto de extremidade de protocolo que pode executar a autenticação como se fosse um IdP. Não há nenhuma manipulação especial necessária no lado do aplicativo para políticas específicas. O aplicativo simplesmente faz uma solicitação de autenticação padrão do setor para o ponto de extremidade de autenticação específico do protocolo exposto pela política de interesse.  
Azure AD B2C pode ser configurado para compartilhar o mesmo emissor em várias políticas ou emissor exclusivo para cada política. Cada aplicativo pode apontar para uma ou várias dessas políticas fazendo uma solicitação de autenticação nativa de protocolo e direcionar os comportamentos de usuário desejados, como entrada, inscrição e edições de perfil. O diagrama mostra os fluxos de trabalho do aplicativo OIDC e SAML.

![imagem mostra a implementação de OIDC e SAML](./media/partner-ping/azure-ad-identity-provider.png)

Embora o cenário mencionado funcione bem para aplicativos modernizados, pode ser desafiador que os aplicativos herdados redirecionem adequadamente o usuário, uma vez que a solicitação de acesso aos aplicativos pode não incluir o contexto da experiência do usuário. Na maioria dos casos, a camada proxy ou um agente integrado no aplicativo Web intercepta a solicitação de acesso.

### <a name="pingaccess-as-a-reverse-proxy"></a>PingAccess como um proxy reverso

Muitos clientes implantaram o PingAccess como o proxy reverso para reproduzir uma ou várias funções, conforme observado anteriormente neste artigo. O PingAccess pode interceptar uma solicitação direta por meio de ser o Man-in-the-Middle ou um redirecionamento proveniente de um agente em execução no servidor de aplicativos da Web.

PingAccess pode ser configurado com OIDC, OAuth2 ou SAML para realizar a autenticação em um provedor de autenticação upstream. Um único IdP de upstream pode ser configurado para essa finalidade no servidor PingAccess. O diagrama a seguir mostra essa configuração.

![imagem mostra o PingAccess com a implementação de OIDC](./media/partner-ping/authorization-flow.png)

Em uma implantação típica de Azure AD B2C em que várias políticas estão expondo vários **IDPs**, ele representa um desafio. Como PingAccess só pode ser configurado com um único IdP upstream.  

### <a name="pingfederate-as-a-federation-proxy"></a>PingFederate como um proxy de Federação

O PingFederate é uma ponte de identidade empresarial que pode ser totalmente configurada como um provedor de autenticação ou um proxy para outros vários IdPs upstream, se necessário. O diagrama a seguir mostra esse recurso.

![imagem mostra a implementação de PingFederate](./media/partner-ping/pingfederate.png)

Esse recurso pode ser usado para alternar de forma contextual/dinâmica ou declarativa uma solicitação de entrada para uma política de Azure AD B2C específica. Veja a seguir uma representação do fluxo de sequência de protocolo para essa configuração.

![imagem mostra o fluxo de trabalho PingAccess e PingFederate](./media/partner-ping/pingaccess-pingfederate-workflow.png)

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

- Uma assinatura do Azure. Se você não tiver uma, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

- Um [locatário Azure ad B2C](./tutorial-create-tenant.md) que está vinculado à sua assinatura do Azure.

- PingAccess e PingFederate implantados em contêineres do Docker ou diretamente em VMs do Azure.

## <a name="connectivity"></a>Conectividade

Verifique se o seguinte está conectado.

- **PingAccess Server** – capaz de se comunicar com o servidor PingFederate, o navegador do cliente, o OIDC, o OAuth bem conhecido e a descoberta de chaves publicadas pelo serviço de Azure ad B2C e servidor PingFederate.

- **PingFederate Server** – capaz de se comunicar com o servidor PingAccess, o navegador do cliente, o OIDC, o OAuth bem conhecido e a descoberta de chaves publicadas pelo serviço de Azure ad B2C.

- **Aplicativo autenticado baseado em cabeçalho ou herdado** – capaz de se comunicar com o PingAccess Server.

- Aplicativo de terceira parte **confiável SAML** – capaz de acessar o tráfego do navegador a partir do cliente. Capaz de acessar os metadados de Federação SAML publicados pelo serviço de Azure AD B2C.

- **Aplicativo moderno** – capaz de acessar o tráfego do navegador a partir do cliente. Capaz de acessar o OIDC, o OAuth bem conhecido e a descoberta de chaves publicadas pelo serviço de Azure AD B2C.

- **API REST** – capaz de acessar o tráfego de um cliente Web ou nativo. Capaz de acessar o OIDC, o OAuth bem conhecido e a descoberta de chaves publicadas pelo serviço de Azure AD B2C.

## <a name="configure-azure-ad-b2c"></a>Configurar Azure AD B2C

Você pode usar os fluxos de usuário básicos ou as políticas de IEF (identidade empresarial avançada) para essa finalidade. PingAccess gera o ponto de extremidade de metadados com base no valor do **emissor** usando a Convenção de descoberta baseada no [webfinger](https://tools.ietf.org/html/rfc7033) .
Para seguir essa convenção, atualize a Azure AD B2C atualização do emissor usando as propriedades da política em fluxos de usuário.

![imagem mostra as configurações de token](./media/partner-ping/token-setting.png)

Nas políticas avançadas, isso pode ser configurado usando o elemento de metadados **IssuanceClaimPattern** para **AuthorityWithTfp** valor no [perfil técnico do emissor do token JWT](./jwt-issuer-technical-profile.md).

## <a name="configure-pingaccesspingfederate"></a>Configurar PingAccess/PingFederate

A seção a seguir aborda a configuração necessária.
O diagrama ilustra o fluxo geral do usuário para a integração.

![imagem mostra a integração de PingAccess e PingFederate](./media/partner-ping/pingaccess.png)

### <a name="configure-pingfederate-as-the-token-provider"></a>Configurar o PingFederate como o provedor de token

Para configurar o PingFederate como o provedor de token para PingAccess, certifique-se de que a conectividade de PingFederate para PingAccess seja estabelecida seguida pela conectividade de PingAccess para PingFederate.  
Consulte [Este artigo](https://docs.pingidentity.com/bundle/pingaccess-61/page/zgh1581446287067.html) para obter as etapas de configuração.

### <a name="configure-a-pingaccess-application-for-header-based-authentication"></a>Configurar um aplicativo PingAccess para autenticação baseada em cabeçalho

Um aplicativo PingAccess deve ser criado para o aplicativo Web de destino para autenticação baseada em cabeçalho. Siga estas etapas.

#### <a name="step-1--create-a-virtual-host"></a>Etapa 1 – criar um host virtual

>[!IMPORTANT]
>Para configurar essa solução, o host virtual precisa ser criado para cada aplicativo. Para obter mais informações sobre considerações de configuração e seus impactos, consulte [importantes considerações](https://docs.pingidentity.com/bundle/pingaccess-43/page/reference/pa_c_KeyConsiderations.html).

Siga estas etapas para criar um host virtual:

1. Acesse **configurações**  >  **acessar**  >  **hosts virtuais**

2. Selecione **Adicionar host virtual**

3. No campo host, insira a parte FQDN da URL do aplicativo

4. No campo porta, digite **443**

5. Selecione **Salvar**

#### <a name="step-2--create-a-web-session"></a>Etapa 2 – criar uma sessão da Web

Siga estas etapas para criar uma sessão da Web:

1. Navegue até **configurações**  >  **acessar**  >  **sessões da Web**

2. Selecione **Adicionar sessão da Web**

3. Forneça um **nome** para a sessão da Web.

4. Selecione o **tipo de cookie**, **JWT assinado** ou **JWT criptografado**

5. Forneça um valor exclusivo para o **público-alvo**

6. No campo **ID do cliente** , insira a **ID do aplicativo do Azure ad**

7. No campo **segredo do cliente** , insira a **chave** que você gerou para o aplicativo no Azure AD.

8. Opcional – você pode criar e usar declarações personalizadas com a API Microsoft Graph. Se você optar por fazer isso, selecione **avançado** e desmarque as opções **perfil de solicitação** e **Atualizar atributos de usuário** . Para obter mais informações sobre como usar declarações personalizadas, consulte [usar uma declaração personalizada](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-headers.md).

9. Selecione **Salvar**

#### <a name="step-3--create-identity-mapping"></a>Etapa 3 – criar mapeamento de identidade

>[!NOTE]
>O mapeamento de identidade pode ser usado com mais de um aplicativo se mais de um aplicativo estiver esperando os mesmos dados no cabeçalho.

Siga estas etapas para criar o mapeamento de identidade:

1. Vá para **configurações**  >  **acesso**  >  **mapeamentos de identidade**

2. Selecione **Adicionar mapeamento de identidade**

3. Especifique um **nome**

4. Selecione o **tipo de mapeamento de identidade de mapeamento de identidade de cabeçalho**

5. Na tabela de **mapeamento de atributo** , especifique os mapeamentos necessários. Por exemplo,

   Nome do atributo | Nome do cabeçalho |
   |-------|--------|
   |upn | x-userprincípioname |
   |email   |    email x  |
   |oid   | OID x  |
   |scp   |     escopo x |
   |amr    |    x-Amr    |

6. Selecione **Salvar**

#### <a name="step-4--create-a-site"></a>Etapa 4 – criar um site

>[!NOTE]
>Em algumas configurações, é possível que um site possa conter mais de um aplicativo. Um site pode ser usado com mais de um aplicativo, quando apropriado.

Siga estas etapas para criar um site:

1. Ir para   >  **sites** principais

2. Selecione **Adicionar site**

3. Especifique um **nome** para o site

4. Insira o **destino** do site. O destino é o par hostname: Port para o servidor que hospeda o aplicativo. Não insira o caminho para o aplicativo neste campo. Por exemplo, um aplicativo em https://mysite:9999/AppName terá um valor de destino de meusite: 9999

5. Indique se o destino está esperando ou não conexões seguras.

6. Se o destino estiver esperando conexões seguras, defina o grupo de certificados confiáveis para **confiar em qualquer**.

7. Selecione **Salvar**

#### <a name="step-5--create-an-application"></a>Etapa 5 – criar um aplicativo

Siga estas etapas para criar um aplicativo no PingAccess para cada aplicativo no Azure que você deseja proteger.

1. Ir para   >  **aplicativos** principais

2. Selecione **Adicionar aplicativo**

3. Especifique um **nome** para o aplicativo

4. Opcionalmente, insira uma **Descrição** para o aplicativo

5. Especifique a **raiz de contexto** para o aplicativo. Por exemplo, um aplicativo em https://mysite:9999/AppName terá uma raiz de contexto de/AppName. A raiz de contexto deve começar com uma barra (/), não deve terminar com uma barra (/) e pode ser mais de uma camada de profundidade, por exemplo,/Apps/MyApp.

6. Selecione o **host virtual** que você criou

   >[!NOTE]
   >A combinação de host virtual e raiz de contexto deve ser exclusiva em PingAccess.

7. Selecione a **sessão Web** que você criou

8. Selecione o **site** que você criou que contém o aplicativo

9. Selecione o **mapeamento de identidade** que você criou

10. Selecione **habilitado** para habilitar o site quando você salvar

11. Selecione **Salvar**

### <a name="configure-the-pingfederate-authentication-policy"></a>Configurar a política de autenticação PingFederate

Configurar a política de autenticação PingFederate para federar a vários IdPs fornecidos pelos locatários Azure AD B2C

1. Crie um contrato para ligar os atributos entre o IdPs e o SP. Para obter mais informações, consulte [Hub de Federação e contratos de política de autenticação](https://docs.pingidentity.com/bundle/pingfederate-101/page/ope1564002971971.html). É provável que você precise apenas de um contrato, a menos que o SP exija um conjunto diferente de atributos de cada IdP.

2. Para cada IdP, crie uma conexão IdP entre o IdP e o PingFederate, o Hub de Federação como o SP.

3. Na janela **mapeamento de sessão de destino** , adicione os contratos de política de autenticação aplicáveis à conexão IDP.

4. Na janela **seletores** , configure um seletor de autenticação. Por exemplo, consulte uma instância do **adaptador do identificador primeiro** para mapear cada IDP para a conexão IDP correspondente em uma política de autenticação.

5. Crie uma conexão de SP entre PingFederate, o Hub de Federação como o IdP e o SP.

6. Adicione o contrato de política de autenticação correspondente à conexão SP na janela **mapeamento de origem de autenticação** .

7. Trabalhe com cada IdP para se conectar ao PingFederate, o Hub de Federação como o SP.

8. Trabalhe com o SP para se conectar ao PingFederate, o Hub de Federação como o IdP.

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os artigos a seguir

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Introdução às políticas personalizadas no Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
