---
title: Planos de implantação - Azure Active Directory | Microsoft Docs
description: Fornece orientação de ponta a ponta sobre como implantar recursos do Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4185ffd644d54c419f42c78326ca10bf100443c3
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051423"
---
# <a name="azure-active-directory-deployment-plans"></a>Planos de implantação do Azure Active Directory
Procurando orientação de ponta a ponta sobre a implantação de recursos do Azure Active Directory (Azure AD)? Os planos de implantação do Azure AD orientam você pelo valor de negócios, considerações de planejamento e procedimentos operacionais necessários para implantar com êxito recursos comuns do Azure AD.

Em qualquer uma das páginas do plano, use a funcionalidade imprimir para PDF do navegador para criar uma versão offline atualizada da documentação.


## <a name="deploy-authentication"></a>Implantar autenticação

| Funcionalidade | Descrição|
| -| -|
| [Autenticação Multifator](../authentication/howto-mfa-getstarted.md)| A MFA (Autenticação Multifator) do Azure AD é uma solução de verificação em duas etapas da Microsoft. Usando métodos de autenticação aprovados pelo administrador, a MFA do Azure AD ajuda a proteger o acesso a seus dados e aplicativos, ao mesmo tempo em que atende à demanda por um processo de logon simples. Assista a este vídeo sobre [como configurar e impor a autenticação multifator em seu locatário](https://www.youtube.com/watch?v=qNndxl7gqVM)|
| [Acesso Condicional](../conditional-access/plan-conditional-access.md)| Com o Acesso Condicional, você pode implementar decisões de controle de acesso automatizado sobre quem pode acessar os aplicativos de nuvem com base em condições. |
| [Redefinição de senha de autoatendimento](../authentication/howto-sspr-deployment.md)| A redefinição de senha de autoatendimento ajuda os usuários a redefinir suas senhas sem a intervenção do administrador, quando e onde eles precisam. |
| [Sem senha](../authentication/howto-authentication-passwordless-deployment.md) | Implementar a autenticação com senha usando o aplicativo Microsoft Authenticator ou as chaves de segurança FIDO2 em sua organização |

## <a name="deploy-application-and-device-management"></a>Implantar o gerenciamento de aplicativos e dispositivos

| Funcionalidade | Descrição|
| -| - |
| [Logon Único](../manage-apps/plan-sso-deployment.md)| O logon único ajuda os usuários a acessar os aplicativos e recursos de que precisam para fazer negócios ao entrar apenas uma vez. Depois que eles tiverem entrado, eles poderão ir de Microsoft Office para o SalesForce para o box para aplicativos internos sem precisar inserir as credenciais uma segunda vez. |
| [Meus aplicativos](../manage-apps/my-apps-deployment-plan.md)| Oferece a seus usuários um hub simples para descobrir e acessar todos os seus aplicativos. Possibilite que eles sejam mais produtivos com os recursos de autoatendimento, como a solicitação de acesso a novos aplicativos e grupos ou o gerenciamento do acesso a recursos em nome de outras pessoas. |
| [Dispositivos](../devices/plan-device-deployment.md) | Este artigo ajuda você a avaliar os métodos para integrar seu dispositivo ao Azure AD, escolher o plano de implementação e fornece links de chave para as ferramentas de gerenciamento de dispositivos com suporte. |


## <a name="deploy-hybrid-scenarios"></a>Implantar cenários híbridos

| Funcionalidade | Descrição|
| -| -|
| [ADFS para sincronização de hash de senha](../hybrid/plan-migrate-adfs-password-hash-sync.md)| Com a sincronização de hash de senha, os hashes de senhas de usuário são sincronizados do Active Directory local para o Azure AD, permitindo que o Azure AD autentique usuários sem interação com o Active Directory local |
| [ADFS para autenticação de passagem](../hybrid/plan-migrate-adfs-pass-through-authentication.md)| A autenticação de passagem do Azure AD ajuda os usuários a entrar em aplicativos locais e baseados em nuvem usando as mesmas senhas. Esse recurso fornece aos usuários uma experiência melhor-uma senha a menos para lembrar e reduz os custos de assistência técnica de ti, pois os usuários têm menos probabilidade de se esquecer de como entrar. Quando as pessoas entram usando o Azure AD, esse recurso valida as senhas dos usuários diretamente no Active Directory local. |
| [Proxy de Aplicativo do AD do Azure](../manage-apps/application-proxy-deployment-plan.md) |Os funcionários de hoje querem ser produtivos em qualquer lugar, a qualquer momento e com qualquer dispositivo. Eles precisam acessar aplicativos SaaS na nuvem e aplicativos corporativos no local. O proxy de Aplicativo do Azure AD habilita esse acesso robusto sem VPNs (redes virtuais privadas) nem DMZs (redes de perímetro) dispendiosas e complexas. |
| [SSO contínuo](../hybrid/how-to-connect-sso-quick-start.md)| O SSO Contínuo do Azure AD (Logon Único Contínuo do Azure Active Directory) conecta usuários automaticamente quando estiverem nos respectivos dispositivos corporativos conectados à sua rede corporativa. Com esse recurso, os usuários não precisarão digitar suas senhas para entrar no Azure AD e, em geral, não precisarão inserir seus nomes de usuário. Esse recurso fornece aos usuários autorizados acesso fácil aos seus aplicativos baseados em nuvem sem a necessidade de componentes locais adicionais. |

## <a name="deploy-user-provisioning"></a>Implantar provisionamento de usuário

| Funcionalidade | Descrição|
| -| -|
| [Provisionamento do usuário](../app-provisioning/plan-auto-user-provisioning.md)| O Azure AD ajuda a automatizar a criação, a manutenção e a remoção de identidades de usuário em aplicativos em nuvem (SaaS) como Dropbox, Salesforce, ServiceNow e outros. |
| [Provisionamento de usuário de RH na nuvem](../app-provisioning/plan-cloud-hr-provision.md)| O provisionamento de usuário de RH na nuvem para Active Directory cria uma base para governança de identidade contínua e aprimora a qualidade dos processos de negócios que dependem de dados de identidade autoritativa. Usando esse recurso com seu produto de RH de nuvem, como workday ou Successfactors, você pode gerenciar o ciclo de vida de identidade de funcionários e trabalhos contingentes Configurando regras que mapeiam processos de movimentação de União-movimento (como novas contratações, término e transferência) para ações de provisionamento de ti (como criar, habilitar, desabilitar) |

## <a name="deploy-governance-and-reporting"></a>Implantar governança e relatórios

| Funcionalidade | Descrição|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| O Azure AD Privileged Identity Management (PIM) ajuda você a gerenciar funções administrativas com privilégios entre o Azure AD, recursos do Azure e outros Serviços Online da Microsoft. O PIM fornece soluções como acesso just-in-time, solicitação de fluxos de trabalho de aprovação e revisões de acesso totalmente integradas para que você possa identificar, descobrir e impedir atividades maliciosas de funções com privilégios em tempo real. |
| [Relatórios e monitoramento](../reports-monitoring/plan-monitoring-and-reporting.md)| O design de sua solução de monitoramento e relatório do Azure AD depende dos seus requisitos legais, de segurança e operacionais, bem como do ambiente e dos processos existentes. Este artigo apresenta as várias opções de design e orienta você para a estratégia de implantação certa. |
| [Revisões de acesso](../governance/deploy-access-reviews.md) | As revisões de acesso são uma parte importante da sua estratégia de governança, permitindo que você conheça e gerencie quem tem acesso e ao que eles têm acesso. Este artigo ajuda você a planejar e implantar revisões de acesso para atingir suas condições de segurança e colaboração desejadas. |

## <a name="include-the-right-stakeholders"></a>Incluir os participantes certos

Ao iniciar o planejamento da implantação para um novo recurso, é importante incluir os principais acionistas em sua organização. Recomendamos que você identifique e documente a pessoa ou as pessoas que atendem a cada uma das funções a seguir e trabalhe com elas para determinar seu envolvimento no projeto.  

As funções podem incluir o seguinte 

|Função |Descrição |
|-|-|
|Usuário final|Um grupo representativo de usuários para os quais a funcionalidade será implementada. Geralmente, visualiza as alterações em um programa piloto.
|Gerente de suporte de ti|O departamento de ti oferece suporte à organização que pode fornecer informações sobre a compatibilidade dessa mudança de uma perspectiva de assistência técnica.  
|Arquiteto de identidade ou administrador global do Azure|Representante da equipe de gerenciamento de identidades responsável pela definição de como essa alteração é alinhada com a infra-estrutura principal de gerenciamento de identidades em sua organização.|
|Proprietário da empresa do aplicativo |O proprietário geral da empresa dos aplicativos afetados, que podem incluir o gerenciamento de acesso.Também pode fornecer informações sobre a experiência do usuário e a utilidade dessa alteração da perspectiva de um usuário final.
|Proprietário da segurança|Um representante da equipe de segurança que pode se desconectar que o plano atenderá aos requisitos de segurança de sua organização.|
|Gerenciador de Conformidade|A pessoa na sua organização responsável por garantir a conformidade com requisitos corporativos, industriais ou governamentais.|

**Os níveis de envolvimento podem incluir:**

- **R** esponsible para implementar o plano e o resultado do projeto 

- **Um** pproval do plano e do resultado do projeto 

- **C** para o plano e o resultado do projeto 

- **Eu** nformed o plano e o resultado do projeto


## <a name="best-practices-for-a-pilot"></a>Práticas recomendadas para um piloto
Um piloto permite que você teste com um pequeno grupo antes de ativar um recurso para todos. Certifique-se de que, como parte de seu teste, cada caso de uso em sua organização seja totalmente testado. É melhor ter como alvo um grupo específico de usuários piloto antes de redistribuir para sua organização como um todo.

Na sua primeira onda, destinada a ti, usabilidade e outros usuários apropriados que podem testar e fornecer comentários. Esses comentários devem ser usados para desenvolver ainda mais as comunicações e as instruções que você envia para os usuários e fornecer informações sobre os tipos de problemas que sua equipe de suporte pode ver. 

Ampliar a distribuição para grupos maiores de usuários deve ser executado aumentando o escopo dos grupos de destino. Isso pode ser feito por meio de [Associação de grupo dinâmico](../enterprise-users/groups-dynamic-membership.md)ou adicionando usuários manualmente aos grupos de destino.
