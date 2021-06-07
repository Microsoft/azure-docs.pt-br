---
title: Resiliência por meio de práticas recomendadas para desenvolvedores usando o Azure AD B2C | Microsoft Docs
description: Resiliência por meio de práticas recomendadas para desenvolvedores em gerenciamento de acesso e identidade do cliente usando o Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff7505e7c47b93f32efd9de60463873026247329
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724918"
---
# <a name="resilience-through-developer-best-practices"></a>Resiliência por meio de práticas recomendadas para desenvolvedores

Neste artigo, compartilhamos alguns aprendizados com base em nossa experiência de trabalhar com grandes clientes. Você pode considerar essas recomendações no design e na implementação de seus serviços.

![Imagem mostra os componentes da experiência do desenvolvedor](media/resilience-b2c-developer-best-practices/developer-best-practices-architecture.png)

## <a name="use-the-microsoft-authentication-library-msal"></a>Usar a MSAL (biblioteca de autenticação da Microsoft)

A [MSAL (biblioteca de autenticação da Microsoft)](../develop/msal-overview.md) e a [biblioteca de autenticação na Web do microsoft Identity para ASP.net](../develop/reference-v2-libraries.md) simplificam a aquisição, o gerenciamento, o armazenamento em cache e a atualização dos tokens exigidos por um aplicativo. Essas bibliotecas são otimizadas especificamente para oferecer suporte à identidade da Microsoft, incluindo recursos que melhoram a resiliência do aplicativo.

Os desenvolvedores devem adotar as versões mais recentes do MSAL e manter-se atualizado. Veja [como aumentar a resiliência de autenticação e autorização](resilience-app-development-overview.md) em seus aplicativos. Sempre que possível, evite implementar sua própria pilha de autenticação e use bibliotecas bem estabelecidas.

## <a name="optimize-directory-reads-and-writes"></a>Otimizar leituras e gravações de diretório

O serviço de diretório Microsoft Azure AD B2C dá suporte a bilhões de autenticações por dia. Ele foi projetado para uma alta taxa de leituras por segundo. Otimize suas gravações para minimizar dependências e aumentar a resiliência.

### <a name="how-to-optimize-directory-reads-and-writes"></a>Como otimizar leituras e gravações de diretório

- **Evite funções de gravação no diretório na entrada**: nunca execute uma gravação na entrada sem uma pré-condição (cláusula If) em suas políticas personalizadas. Um caso de uso que requer uma gravação em uma entrada é a [migração just-in-time de senhas de usuário](https://github.com/azure-ad-b2c/user-migration/tree/master/seamless-account-migration). Evite qualquer cenário que exija uma gravação em cada entrada.

  - As [pré-condições](../../active-directory-b2c/userjourneys.md) em uma jornada do usuário terão a seguinte aparência:

  ``
  <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 
  <Value>requiresMigration</Value>
  ...
  < Precondition/>
  ``
  - Crie resistência aos [inscrições orientadas por bot integrando-se a um sistema captcha](https://github.com/azure-ad-b2c/samples/tree/master/policies/captcha-integration).

  - Use um [exemplo de teste de carga](../../active-directory-b2c/best-practices.md#testing) para simular a inscrição e a entrada. 

- **Entender a limitação**: o diretório implementa as regras de limitação de nível de aplicativo e de locatário. Há limites de taxa adicionais para operações de leitura/obtenção, gravação/POSTAgem, atualização/PUT e exclusão/exclusão, e cada operação tem limites diferentes.

  - Uma gravação no momento da entrada se enquadrará em uma POSTAgem para novos usuários ou COLOCARá os usuários existentes.

  - Uma política personalizada que cria ou atualiza um usuário em todas as entradas, pode potencialmente atingir um limite de nível de aplicativo ou uma taxa de POST. Os mesmos limites se aplicam ao atualizar objetos de diretório por meio do Azure AD ou Microsoft Graph. Da mesma forma, examine as leituras para manter o número de leituras em cada entrada no mínimo.

  - Estimar a carga de pico para prever a taxa de gravações de diretório e evitar a limitação. As estimativas de tráfego de pico devem incluir estimativas de ações como inscrição, entrada e autenticação multifator (MFA). Certifique-se de testar o sistema de Azure AD B2C e seu aplicativo para o tráfego de pico. É possível que Azure AD B2C possa lidar com a carga sem limitação, quando seus aplicativos ou serviços downstream não vão.

  - Entenda e planeje sua linha do tempo de migração. Ao planejar a migração de usuários para Azure AD B2C usando Microsoft Graph, considere os limites de aplicativo e locatário para calcular o tempo necessário para concluir a migração de usuários. Se você dividir seu trabalho de criação de usuário ou script usando dois aplicativos, poderá usar o limite por aplicativo. Ele ainda precisaria permanecer abaixo do limite por locatário.

  - Entenda os efeitos do seu trabalho de migração em outros aplicativos. Considere o tráfego ao vivo servido por outros aplicativos confiáveis para garantir que você não cause limitação no nível do locatário e na privação de recursos para seu aplicativo em tempo real. Para obter mais informações, consulte a [diretriz de limitação de Microsoft Graph](/graph/throttling).
  
## <a name="extend-token-lifetimes"></a>Estender tempos de vida do token

Em um evento improvável, quando o serviço de autenticação Azure AD B2C não consegue concluir novas entradas e entradas, você ainda pode fornecer mitigação para usuários que estão conectados. Com a [configuração](../../active-directory-b2c/configure-tokens.md), você pode permitir que usuários que já tenham entrado para continuar usando o aplicativo sem qualquer interrupção percebida até que o usuário saia do aplicativo ou que o tempo limite da [sessão](../../active-directory-b2c/session-behavior.md) expire devido à inatividade.

Seus requisitos de negócios e a experiência do usuário final desejada determinarão sua frequência de atualização de token para aplicativos Web e de página única (SPAs).

### <a name="how-to-extend-token-lifetimes"></a>Como estender tempos de vida de token

- **Aplicativos Web**: para aplicativos Web em que o token de autenticação é validado no início da entrada, o aplicativo depende do cookie de sessão para continuar a estender a validade da sessão.

  - Permita que os usuários permaneçam conectados implementando tempos de sessão sem interrupção que continuarão a renovar sessões com base na atividade do usuário. Se houver uma interrupção de emissão de token de longo prazo, esses tempos de sessão poderão ser aumentados como uma configuração de OneTime no aplicativo. Mantenha o tempo de vida da sessão para o máximo permitido.

- **Spas**: um spa pode depender de tokens de acesso para fazer chamadas para as APIs. Um SPA tradicionalmente usa o fluxo implícito que não resulta em um token de atualização. O SPA pode usar um iframe oculto para executar novas solicitações de token no ponto de extremidade de autorização se o navegador ainda tiver uma sessão ativa com o Azure AD B2C. Para o SPAs, há algumas opções disponíveis para permitir que o usuário continue a usar o aplicativo.

  - Estenda a duração da validade do token de acesso para atender às suas necessidades de negócios.

  - Crie seu aplicativo para usar um gateway de API como o proxy de autenticação. Nessa configuração, as cargas de SPA sem qualquer autenticação e as chamadas à API são feitas no gateway de API. O gateway de API envia o usuário por meio de um processo de entrada usando uma [concessão de código de autorização](https://oauth.net/2/grant-types/authorization-code/) com base em uma política e autentica o usuário. Subsequentemente, a sessão de autenticação entre o gateway de API e o cliente é mantida usando um cookie de autenticação. As APIs são atendidas pelo gateway de API usando o token obtido pelo gateway de API ou por algum outro método de autenticação direta, como certificados, credenciais de cliente ou chaves de API.

  - [Migre seu spa da concessão implícita](https://developer.microsoft.com/identity/blogs/msal-js-2-0-supports-authorization-code-flow-is-now-generally-available/) ao [código de autorização de concessão](../../active-directory-b2c/implicit-flow-single-page-application.md) com chave de prova de troca de código (PKCE) e suporte ao compartilhamento de recursos entre origens (CORS). Migre seu aplicativo do MSAL.js 1. x para MSAL.js 2. x para obter a resiliência dos aplicativos Web.

  - Para aplicativos móveis, é recomendável estender os tempos de vida de token de atualização e de acesso.

- **Aplicativos de back-end ou de microserviço**: como os aplicativos de back-end (daemon) não são interativos e não estão em um contexto de usuário, o potencial do roubo de token é bastante reduzido. A recomendação é fazer um equilíbrio entre a segurança e o tempo de vida e definir um tempo de vida de token longo.

## <a name="configure-single-sign-on"></a>Configurar logon único

Com o [SSO (logon único)](../manage-apps/what-is-single-sign-on.md), os usuários entram uma vez com uma única conta e obtêm acesso a vários aplicativos. O aplicativo pode ser um aplicativo Web, móvel ou de página única (SPA), independentemente da plataforma ou do nome de domínio. Quando o usuário entra inicialmente em um aplicativo, Azure AD B2C persiste uma [sessão baseada em cookie](../../active-directory-b2c/session-behavior.md).

Nas solicitações de autenticação subsequentes, o Azure AD B2C lê e valida a sessão baseada em cookie e emite um token de acesso sem solicitar que o usuário entre novamente. Se o SSO estiver configurado com um escopo limitado em uma política ou um aplicativo, o acesso posterior a outras políticas e aplicativos exigirá uma nova autenticação.

### <a name="how-to-configure-sso"></a>Como configurar o SSO

[Configure o SSO](../hybrid/how-to-connect-sso-quick-start.md) para todo o locatário (padrão) para permitir que vários aplicativos e fluxos de usuário em seu locatário compartilhem a mesma sessão de usuário. A configuração de todo o locatário fornece a maior resiliência para a nova autenticação.  

## <a name="safe-deployment-practices"></a>Práticas de implantação segura

Os interrupções de serviço mais comuns são as alterações de código e configuração. A adoção de processos e ferramentas de CICD (integração contínua e entrega contínua) ajuda na implantação rápida em grande escala e reduz erros humanos durante o teste e a implantação na produção. Adote CICD para redução de erros, eficiência e consistência. [Azure pipelines](/azure/devops/pipelines/apps/cd/azure/cicd-data-overview) é um exemplo de CICD.

## <a name="web-application-firewall"></a>Firewall do aplicativo Web

Proteja seus aplicativos contra vulnerabilidades conhecidas, como ataques de DDoS (negação de serviço distribuído), injeções de SQL, scripts entre sites, execução remota de código e muitos outros, conforme documentado nos [10 principais OWASP](https://owasp.org/www-project-top-ten/). A implantação de um WAF (firewall do aplicativo Web) pode se defender contra explorações e vulnerabilidades comuns.

- Use o Azure [WAF](../../web-application-firewall/overview.md), que fornece proteção centralizada contra ataques.

- Use o WAF com o Azure AD [Identity Protection e acesso condicional para fornecer proteção de várias camadas](../../active-directory-b2c/conditional-access-identity-protection-overview.md) ao usar o Azure ad B2C.  

## <a name="secrets-rotation"></a>Rotação de segredos

Azure AD B2C usa segredos para aplicativos, APIs, políticas e criptografia. Os segredos autenticação segura, interações externas e armazenamento. O National Institute of Standards and Technology (NIST) chama o período de tempo durante o qual uma chave específica é autorizada para uso por entidades legítimas um período. Escolha o comprimento certo de [período](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final) para atender às suas necessidades de negócios. Os desenvolvedores precisam definir manualmente a expiração e girar os segredos com antecedência de sua expiração.

### <a name="how-to-implement-secret-rotation"></a>Como implementar a rotação secreta

- Use [identidades gerenciadas](../managed-identities-azure-resources/overview.md) para recursos com suporte para autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD. Ao usar identidades gerenciadas, você pode gerenciar recursos automaticamente, incluindo a rotação de credenciais.

- Faça um inventário de todas as [chaves e certificados configurados](../../active-directory-b2c/policy-keys-overview.md) no Azure ad B2C. É provável que essa lista inclua chaves usadas em políticas personalizadas, [APIs](../../active-directory-b2c/secure-rest-api.md), token de ID de assinatura e certificados para SAML.

- Usando o CICD, gire os segredos que estão prestes a expirar dentro de dois meses a partir da época de pico prevista. O período máximo recomendado de chaves privadas associadas a um certificado é de um ano.

- Monitore e gire proativamente as credenciais de acesso à API, como senhas e certificados.

## <a name="test-rest-apis"></a>Testar APIs REST

No contexto de resiliência, o teste de APIs REST precisa incluir a verificação de – códigos HTTP, conteúdo de resposta, cabeçalhos e desempenho. Os testes não devem incluir apenas testes de caminho felizes, mas também verificar se a API trata os cenários de problemas normalmente.

### <a name="how-to-test-apis"></a>Como testar APIs

Recomendamos que seu plano de teste inclua [testes de API abrangentes](../../active-directory-b2c/best-practices.md#testing). Se estiver planejando um surto futuro devido ao tráfego de promoção ou de feriados, você precisará revisar o teste de carga com as novas estimativas. Realize testes de carga de suas APIs e da CDN (rede de distribuição de conteúdo) em um ambiente de desenvolvedor e não em produção.

## <a name="next-steps"></a>Próximas etapas

- [Recursos de resiliência para desenvolvedores de Azure AD B2C](resilience-b2c.md)
  - [Experiência do usuário final resiliente](resilient-end-user-experience.md)
  - [Interfaces resilientes com processos externos](resilient-external-processes.md)
  - [Resiliência por meio de monitoramento e análise](resilience-with-monitoring-alerting.md)
- [Crie resiliência em sua infraestrutura de autenticação](resilience-in-infrastructure.md)
- [Aumentar a resiliência de autenticação e autorização em seus aplicativos](resilience-app-development-overview.md)