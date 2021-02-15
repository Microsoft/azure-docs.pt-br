---
title: Acesso híbrido seguro do Azure AD com F5 | Microsoft Docs
description: F5 BIG-IP Access Policy Manager e Azure Active Directory integração para acesso híbrido seguro
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8210e00824d7680f4eecde2f0b299dfcdc93b90
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730564"
---
# <a name="f5-big-ip-access-policy-manager-and-azure-active-directory-integration-for-secure-hybrid-access"></a>F5 BIG-IP Access Policy Manager e Azure Active Directory integração para acesso híbrido seguro

A proliferação da mobilidade e do panorama de ameaças em evolução é colocar uma investigação extra sobre o acesso e a governança de recursos, colocando [zero](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/) a frente e o centro de todos os programas de modernização.
Na Microsoft e F5, percebemos que essa transformação digital normalmente é uma jornada de vários anos para qualquer empresa, potencialmente deixando os recursos críticos expostos até modernizados. O Genesis por trás do F5 BIG-IP e Azure Active Directory o acesso híbrido seguro (SHA) visa não apenas a melhorar o acesso remoto a aplicativos locais, mas também ao reforçar a postura geral de segurança desses serviços vulneráveis.

Para o contexto, as estimativas de pesquisa de 60-80% dos aplicativos locais são herdadas por natureza ou, em outras palavras, não são capazes de ser integradas diretamente com o Azure Active Directory (AD). O mesmo estudo também indicou uma grande proporção desses sistemas em versões de nível inferior do SAP, Oracle, SAGE e outras cargas de trabalho conhecidas que fornecem serviços críticos.

O SHA aborda esse ponto cego, permitindo que as organizações continuem usando seus investimentos em F5 para a entrega de aplicativos e redes superiores. Combinado com o Azure AD, ele preenche o cenário de aplicativo heterogêneo com o plano de controle de identidade moderno.

Ter o acesso de pré-autenticação do Azure AD aos serviços publicados BIG-IP fornece muitos benefícios:

- Autenticação sem senha por meio do [Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview), [MS Authenticator](../user-help/user-help-auth-app-download-install.md), [chaves de Fido (Fast Identity online)](../authentication/howto-authentication-passwordless-security-key.md)e [autenticação baseada em certificado](../authentication/active-directory-certificate-based-authentication-get-started.md)

- [Acesso condicional](../conditional-access/overview.md) preemptivo e [autenticação multifator (MFA)](../authentication/concept-mfa-howitworks.md)

- [Proteção de identidade](../identity-protection/overview-identity-protection.md) – controle adaptável por meio da criação de perfil de risco de usuário e sessão


- [Detecção de credenciais vazadas](../identity-protection/concept-identity-protection-risks.md)

- [SSPR (redefinição de senha de autoatendimento)](../authentication/tutorial-enable-sspr.md)

- [Colaboração do parceiro](../governance/entitlement-management-external-users.md) – gerenciamento de direitos para acesso de convidado controlado

- [Cloud app Security (CASB)](/cloud-app-security/what-is-cloud-app-security) -para descoberta e controle completos do aplicativo

- Monitoramento de ameaças – [Sentinela do Azure](https://azure.microsoft.com/services/azure-sentinel/) para análise avançada de ameaças

- O [portal do AD do Azure](https://azure.microsoft.com/features/azure-portal/) -um único plano de controle para controlar a identidade e o acesso

## <a name="scenario-description"></a>Descrição do cenário

Como um ADC (controlador de entrega de aplicativos) e SSL-VPN, um sistema de BIG-IP fornece acesso local e remoto a todos os tipos de serviços, incluindo:

- Aplicativos Web modernos e herdados

- Aplicativos não baseados na Web

- Serviços de API Web REST e SOAP

Seu Gerenciador de tráfego local (LTM) permite a publicação segura de serviços por meio da funcionalidade de proxy reverso. Ao mesmo tempo, um APM (Gerenciador de políticas de acesso) sofisticado estende o BIG-IP com um conjunto mais rico de recursos, habilitando a Federação e o SSO (logon único).

A integração é baseada em uma relação de confiança de Federação padrão entre o APM e o Azure AD, comum para a maioria dos casos de uso de SHA que inclui o [cenário SSL-VPN](f5-aad-password-less-vpn.md). Os recursos de Security Assertion Markup Language (SAML), OAuth e Open ID Connect (OIDC) não são uma exceção, pois eles também podem ser protegidos para acesso remoto. Também pode haver cenários em que um BIG-IP se torna um ponto de restrição para o acesso de zero confiança a todos os serviços, incluindo aplicativos SaaS.

A capacidade de um grande IP de integrar-se ao Azure AD é o que permite a transição de protocolo necessária para proteger serviços herdados ou não integrados ao AD do Azure com controles modernos, como [autenticação sem senha](https://www.microsoft.com/security/business/identity/passwordless) e [acesso condicional](../conditional-access/overview.md). Nesse cenário, um BIG-IP continua a cumprir sua função como um proxy reverso, enquanto entrega a pré-autenticação e a autorização para o Azure AD, de acordo com cada serviço.

As etapas 1-4 no diagrama ilustram a troca de pré-autenticação de front-end entre um usuário, um BIG IP e o Azure AD, em um fluxo iniciado pelo provedor de serviços. As etapas 5-6 mostram o aprimoramento da sessão APM subsequente e o SSO para serviços de back-end individuais.

![Imagem mostra a arquitetura de alto nível](./media/f5-aad-integration/integration-flow-diagram.png)

| Etapa | Descrição |
|:------|:-----------|
| 1. | O usuário seleciona um ícone de aplicativo no portal, resolvendo a URL para o SP SAML (BIG-IP) |
| 2. | O BIG-IP redireciona o usuário para o IDP SAML (Azure AD) para pré-autenticação|
| 3. | O Azure AD processa políticas de acesso condicional e [controles de sessão](../conditional-access/concept-conditional-access-session.md) para autorização|
| 4. | O usuário redireciona de volta para BIG-IP apresentando as declarações SAML emitidas pelo Azure AD |
| 5. | BIG-IP solicita quaisquer informações adicionais de sessão a serem incluídas no [SSO](../hybrid/how-to-connect-sso.md) e no [RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md) para o serviço publicado |
| 6. | BIG-IP encaminha a solicitação do cliente para o serviço de back-end

## <a name="user-experience"></a>Experiência do usuário

Seja um funcionário, afiliado ou consumidor direto, a maioria dos usuários já está familiarizada com a experiência de logon do Office 365, portanto, o acesso a serviços de BIG-IP via SHA permanece amplamente familiar.

Agora, os usuários encontram seus serviços publicados de BIG IP consolidados nos [launchpads](https://o365pp.blob.core.windows.net/media/Resources/Microsoft%20365%20Business/Launchpad%20Overview_for%20Partners_10292019.pdf) de [myapps](../user-help/my-apps-portal-end-user-access.md) ou O365, juntamente com os recursos de autoatendimento, para um conjunto mais amplo de serviços, independentemente do tipo de dispositivo ou local. Os usuários podem até continuar acessando os serviços publicados diretamente por meio da BIG-IPs portal do Webtop proprietário, se for preferencial. Ao fazer logoff, o SHA garante que a sessão de um usuário seja encerrada em ambas as extremidades, o BIG-IP e o Azure AD, garantindo que os serviços permaneçam totalmente protegidos contra acesso não autorizado.  

As capturas de tela fornecidas são do portal de aplicativos do Azure AD que os usuários acessam com segurança para localizar seus serviços publicados de BIG-IP e para gerenciar suas propriedades de conta.  

![A captura de tela mostra a galeria do Woodgrove myapps](media/f5-aad-integration/woodgrove-app-gallery.png)

![A captura de tela mostra a página de autoatendimento do Woodgrove accounts](media/f5-aad-integration/woodgrove-myaccount.png)

## <a name="insights-and-analytics"></a>Informações e análises

Uma função de BIG-IP é essencial para qualquer empresa, portanto, implantadas instâncias BIG-IP devem ser monitoradas para garantir que os serviços publicados estejam altamente disponíveis, tanto em nível de SHA quanto em operação também.

Existem várias opções para registrar eventos localmente ou remotamente por meio de uma solução SIEM (gerenciamento de informações e eventos de segurança), permitindo o armazenamento e o processamento de telemetria integrados. Uma solução altamente eficaz para monitorar a atividade específica do Azure AD e do SHA é usar o [Azure monitor](../../azure-monitor/overview.md) e o [Azure Sentinel](../../sentinel/overview.md), oferecendo uma oferta:

- Visão geral detalhada da sua organização, potencialmente em várias nuvens e locais no local, incluindo a infraestrutura de BIG IP

- Plano de controle único fornecendo uma exibição combinada de todos os sinais, evitando a dependência de ferramentas complexas e diferentes

![A imagem mostra o fluxo de monitoramento](media/f5-aad-integration/azure-sentinel.png)

## <a name="prerequisites"></a>Pré-requisitos

A integração do F5 BIG-IP com o Azure AD para SHA tem os seguintes pré-requisitos:

- Uma instância BIG-IP do F5 em execução em qualquer uma das seguintes plataformas:

  - Dispositivo físico

  - Edição virtual do hipervisor, como Microsoft Hyper-V, VMware ESXi, KVM Linux e Citrix hypervisor

  - Cloud Virtual Edition, como Azure, VMware, KVM, Community Xen, MS Hyper-V, AWS, OpenStack e Google Cloud

    O local real de uma instância BIG-IP pode estar no local ou em qualquer plataforma de nuvem com suporte, incluindo o Azure, desde que ela tenha conectividade com a Internet, recursos sendo publicados e quaisquer outros serviços necessários, como Active Directory.  

- Uma licença de APM BIG-IP ativo F5, por meio de uma das seguintes opções:

   - Pacote BIG-IP® Best da F5 (ou)

   - F5 BIG-IP Access Policy Manager™ licença autônoma

   - A licença de complemento do Gerenciador de políticas de acesso do BIG-IP™ (APM) em um BIG-IP F5 existente® o Gerenciador de tráfego local™ (LTM)

   - Uma [licença de avaliação](https://www.f5.com/trial/big-ip-trial.php) do Gerenciador de políticas de acesso™ (APM) de 90 dias

- Licenciamento do Azure AD por meio de uma das seguintes opções:

   - Uma [assinatura gratuita](/windows/client-management/mdm/register-your-free-azure-active-directory-subscription#:~:text=%20Register%20your%20free%20Azure%20Active%20Directory%20subscription,will%20take%20you%20to%20the%20Azure...%20More%20) do Azure ad fornece os requisitos básicos mínimos para implementar o Sha com autenticação sem senha

   - Uma [assinatura premium](https://azure.microsoft.com/pricing/details/active-directory/) fornece todas as adições adicionais de valor descritas no prefácio, incluindo [acesso condicional](../conditional-access/overview.md), [MFA](../authentication/concept-mfa-howitworks.md)e [proteção de identidade](../identity-protection/overview-identity-protection.md)

Nenhuma experiência anterior ou o conhecimento de BIG-IP é necessário para implementar o SHA, mas recomendamos familiarizar-se com a terminologia de BIG-IP F5. A [base de conhecimento](https://www.f5.com/services/resources/glossary) avançada do se BigIP também é um bom lugar para começar a criar o conhecimento de Big-IP.

## <a name="deployment-scenarios"></a>Cenários de implantação

Os tutoriais a seguir fornecem orientação detalhada sobre como implementar alguns dos padrões mais comuns para o BIG-IP e o Azure AD SHA:

- [F5 BIG-IP no passo a passo de implantação do Azure](f5-bigip-deployment-guide.md)

- [F5 BIG-IP APM e SSO do Azure AD para aplicativos Kerberos](../saas-apps/kerbf5-tutorial.md#configure-f5-single-sign-on-for-kerberos-application)

- [F5 BIG-IP APM e SSO do Azure AD para aplicativos baseados em cabeçalho](../saas-apps/headerf5-tutorial.md#configure-f5-single-sign-on-for-header-based-application)

- [Protegendo o F5 BIG-IP SSL-VPN com o Azure AD SHA](f5-aad-password-less-vpn.md)

## <a name="additional-resources"></a>Recursos adicionais

- [O fim das senhas, vá para senha](https://www.microsoft.com/security/business/identity/passwordless)

- [Acesso híbrido seguro Azure Active Directory](https://azure.microsoft.com//services/active-directory/sso/secure-hybrid-access/)

- [Estrutura de confiança zero da Microsoft para habilitar o trabalho remoto](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Introdução ao Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/?&OCID=AID2100131_SEM_XfknpgAAAHoVMTvh:20200922160358:s&msclkid=5e0e022409fc1c94dab85d4e6f4710e3&ef_id=XfknpgAAAHoVMTvh:20200922160358:s&dclid=CJnX6vHU_esCFUq-ZAod1iQF6A)

## <a name="next-steps"></a>Próximas etapas

Considere a execução de um POC (prova de conceito de SHA) usando sua infraestrutura BIG-IP existente ou implantando uma instância de avaliação. A [implantação de uma VM do ve (Virtual Edition) Big-IP no Azure](f5-bigip-deployment-guide.md) leva aproximadamente 30 minutos. nesse ponto, você terá:

- Uma plataforma totalmente protegida para modelar uma prova de conceito de SHA

- Uma instância de pré-produção, plataforma totalmente protegida a ser usada para testar novas atualizações e hotfixes do sistema BIG-IP

Ao mesmo tempo, você deve identificar um ou dois aplicativos que podem ser direcionados para publicação por meio do BIG IP e protegendo com o SHA.  

Nossa recomendação é começar com um aplicativo que ainda não foi publicado por meio de um BIG-IP, para evitar possíveis interrupções nos serviços de produção. As diretrizes mencionadas neste artigo ajudarão você a se familiarizar com o procedimento geral para criar os vários objetos de configuração de BIG-IP e configurar o SHA. Depois de concluir, você deve ser capaz de fazer o mesmo com qualquer outro serviço novo, além de ter conhecimento suficiente para converter os serviços publicados de BIG-IP existentes no SHA com esforço mínimo.

O guia interativo abaixo percorre o procedimento de alto nível para implementar o SHA e ver a experiência do usuário final.

[![A imagem mostra a capa do guia interativo](media/f5-aad-integration/interactive-guide.png)](https://aka.ms/Secure-Hybrid-Access-F5-Interactive-Guide)
