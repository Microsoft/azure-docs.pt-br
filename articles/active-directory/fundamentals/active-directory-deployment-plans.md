---
title: Planos de implantação - Azure Active Directory | Microsoft Docs
description: Fornece orientação de ponta a ponta sobre como implantar recursos do Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f695d9f0240f8c27ea0bedba7e532d37a177752
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304752"
---
# <a name="azure-active-directory-deployment-plans"></a>Planos de implantação do Azure Active Directory
Procurando orientação de ponta a ponta sobre como implantar alguns recursos do Azure AD (Azure Active Directory)? Os planos de implantação a seguir explicam o valor comercial, as considerações de planejamento, o design e os procedimentos operacionais necessários para implantar com êxito alguns dos recursos mais comuns do Azure AD. 

Nestes documentos, você encontrará modelos de email, diagramas de arquitetura do sistema, casos de teste comuns e muito mais. 

Adoraríamos receber seus comentários sobre os documentos. Participe desta pequena [pesquisa](https://aka.ms/deploymentplanfeedback) sobre como os documentos serviram para você. 

## <a name="include-the-right-stakeholders"></a>Incluir os participantes certos

Ao iniciar o planejamento da implantação para um novo recurso, é importante incluir os principais acionistas em sua organização. Recomendamos que você identifique e documente a pessoa ou as pessoas que atendem a cada uma das funções a seguir e trabalhe com elas para determinar seu envolvimento no projeto.  

As funções podem incluir o seguinte 

|Role |DESCRIÇÃO |
|-|-|
|Usuário final|Um grupo representativo de usuários para os quais a funcionalidade será implementada. Geralmente, visualiza as alterações em um programa piloto.
|Gerente de suporte de ti|O departamento de ti oferece suporte à organização que pode fornecer informações sobre a compatibilidade dessa mudança de uma perspectiva de assistência técnica.  
|Arquiteto de identidade ou administrador global do Azure|Representante da equipe de gerenciamento de identidades responsável pela definição de como essa alteração é alinhada com a infra-estrutura principal de gerenciamento de identidades em sua organização.|
|Proprietário da empresa do aplicativo |O proprietário geral da empresa dos aplicativos afetados, que podem incluir o gerenciamento de acesso.  Também pode fornecer informações sobre a experiência do usuário e a utilidade dessa alteração da perspectiva de um usuário final.
|Proprietário da segurança|Um representante da equipe de segurança que pode se desconectar que o plano atenderá aos requisitos de segurança de sua organização.|
|Gerenciador de conformidade|A pessoa na sua organização responsável por garantir a conformidade com requisitos corporativos, industriais ou governamentais.|

**Os níveis de envolvimento podem incluir:**

- **R**esponsible para implementar o plano e o resultado do projeto 

- **Um**pproval do plano e do resultado do projeto 

- **C**para o plano e o resultado do projeto 

- **Eu**nformed o plano e o resultado do projeto
 
## <a name="deployment-plans"></a>Planos de implantação



|Cenário |DESCRIÇÃO |
|-|-|
|[Autenticação Multifator](../authentication/howto-mfa-getstarted.md)|A Autenticação Multifator do Azure é uma solução de verificação em duas etapas da Microsoft. Usando métodos de autenticação aprovados pelo administrador, o Azure MFA ajuda a proteger o acesso a dados e a aplicativos, ao mesmo tempo que atende à demanda por um processo de logon simples.|
|[Acesso condicional](https://aka.ms/deploymentplans/ca)|Com o acesso condicional, você pode implementar decisões de controle de acesso automatizadas para quem pode acessar seus aplicativos de nuvem, com base em condições.|
|[Redefinição de senha de autoatendimento](https://aka.ms/deploymentplans/sspr)|A redefinição de senha de autoatendimento fornece aos usuários a capacidade de redefinir sua senha, sem nenhuma intervenção do administrador, quando e onde eles precisarem.|
|[Privileged Identity Management](https://aka.ms/deploymentplans/pim)|O Azure AD Privileged Identity Management (PIM) ajuda você a gerenciar funções administrativas com privilégios entre o Azure AD, recursos do Azure e outros Serviços Online da Microsoft. O PIM fornece soluções como acesso just-in-time, solicitação de fluxos de trabalho de aprovação e revisões de acesso totalmente integradas para que você possa identificar, descobrir e impedir atividades maliciosas de funções com privilégios em tempo real.|
|[Logon Único](https://aka.ms/deploymentplans/sso)|O logon único o ajuda a acessar todos os aplicativos e recursos necessários para fazer negócios fazendo logon uma única vez, usando uma conta de usuário única. Depois de entrar, você pode ir do Microsoft Office para o SalesForce e para o Box sem a exigência de autenticação (por exemplo, digitar uma senha) uma segunda vez.|
|[SSO contínuo](https://aka.ms/SeamlessSSODPDownload)|O SSO Contínuo do Azure AD (Logon Único Contínuo do Azure Active Directory) conecta usuários automaticamente quando estiverem nos respectivos dispositivos corporativos conectados à sua rede corporativa. Depois de ativar esse recurso, os usuários não precisarão digitar suas senhas para entrar no Azure AD e, normalmente, não precisarão nem mesmo digitar os nomes de usuário. Esse recurso fornece aos usuários acesso fácil a seus aplicativos baseados em nuvem sem a necessidade de nenhum componente local adicional.|
|[Painel de Acesso](https://aka.ms/AccessPanelDPDownload)|Oferece a seus usuários um hub simples para descobrir e acessar todos os seus aplicativos. Capacita-os para serem mais produtivos com recursos de autoatendimento, como a capacidade de solicitar acesso a novos aplicativos e grupos ou gerenciar o acesso a esses recursos em nome de outras pessoas.|
|[ADFS para sincronização de hash de senha](https://aka.ms/deploymentplans/adfs2phs)|Com a sincronização de hash de senha, os hashes de senhas de usuário são sincronizados do Active Directory no local para o Azure AD, permitindo que o Azure AD autentique os usuários sem interação com o Active Directory local|
|[ADFS para autenticação de passagem](https://aka.ms/deploymentplans/adfs2pta)|A autenticação de passagem do Azure AD ajuda os usuários a entrar em aplicativos locais e baseados em nuvem usando as mesmas senhas. Esse recurso fornece aos usuários uma experiência melhor – uma senha a menos para se lembrar – e reduz os custos de suporte técnico de TI porque os usuários têm menor probabilidade de se esquecer como entrar. Quando as pessoas entram usando o Azure AD, esse recurso valida as senhas dos usuários diretamente no Active Directory local.|
|[Proxy de Aplicativo do Azure AD](https://aka.ms/deploymentplans/appproxy)|Os funcionários de hoje querem ser produtivos em qualquer lugar, a qualquer momento e com qualquer dispositivo. Eles desejam trabalhar em seus próprios dispositivos, sejam laptops, telefones ou tablets. Além disso, os funcionários esperam poder acessar todos os aplicativos, tanto aplicativos SaaS na nuvem como aplicativos corporativos locais. O fornecimento de acesso a aplicativos locais sempre envolveu VPNs (redes virtuais privadas), redes de perímetro ou proxies reversos locais. Essas soluções são complexas e difíceis de proteger, além de serem caras para configurar e gerenciar. Há uma opção melhor! – Proxy de Aplicativo do Azure AD|
|[Provisionamento do usuário](https://aka.ms/UserProvisioningDPDownload)|O Azure AD ajuda a automatizar a criação, a manutenção e a remoção de identidades de usuário em aplicativos em nuvem (SaaS) como Dropbox, Salesforce, ServiceNow e outros.|
|[Provisionamento de entrada de usuário controlada pelo Workday](https://aka.ms/WorkdayDeploymentPlan)|O provisionamento de entrada de usuário controlada pelo Workday cria uma base para a governança de identidade contínua e melhora a qualidade dos processos de negócios que dependem de dados de identidade autoritativos. Usando esse recurso, você pode gerenciar facilmente o ciclo de vida de identidade de funcionários e trabalhadores contingentes por meio da configuração de regras que mapeiam os processos de ligação/movimentação/saída (como nova contratação, rescisão, transferência) para ações de provisionamento de TI (como criar, habilitar, desabilitar, excluir contas).|
