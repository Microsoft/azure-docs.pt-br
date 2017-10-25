---
title: Fundamentos do gerenciamento de identidades do Azure | Microsoft Docs
description: "Identidades baseadas em nuvem agora são a melhor maneira de manter o controle e a visibilidade de como e quando os usuários acessam dados e aplicativos corporativos."
keywords: 
author: jeffgilb
manager: femila
ms.reviewr: jsnow
ms.author: jeffgilb
ms.date: 07/05/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: 52f05ee8a5c07fc008da40aef12d1ad8e8136429
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="fundamentals-of-azure-identity-management"></a>Fundamentos do gerenciamento de identidades do Azure
Conforme cada vez mais recursos digitais residem fora da rede corporativa, na nuvem e em dispositivos, uma excelente solução de gerenciamento de identidade e acesso baseada em nuvem está se tornando uma necessidade. Identidades baseadas em nuvem agora são a melhor maneira de manter o controle e a visibilidade de como e quando os usuários acessam dados e aplicativos corporativos.

Microsoft tem sido protegendo identidades baseados em nuvem para mais de uma década e agora, com [Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), esses mesmos sistemas de proteção estão disponíveis para você. Com o Azure AD, os administradores de empresa podem garantir facilmente a responsabilidade do usuário e do administrador com mais segurança e controle do que nunca.

O Azure AD Premium é uma solução de gerenciamento de acesso e de identidades baseada em nuvem com recursos avançados de proteção que habilita uma identidade segura para todos os aplicativos, a proteção de identidades (aprimorada pelo [gráfico de segurança de inteligência da Microsoft](https://www.microsoft.com/en-us/security/intelligence)) e o Privileged Identity Management. Não é apenas outra monitoramento ou ferramenta de emissão de relatórios do Azure AD Premium pode proteger as identidades do usuário em tempo real e permitem que você crie políticas de acesso baseada em riscos, adaptável para proteger os dados da sua organização.

Assista a este vídeo curto para obter uma visão geral da proteção e do gerenciamento de identidades do Azure AD:
<iframe width="560" height="315" src="https://www.youtube.com/embed/9LGIJ2-FKIM" frameborder="0" allowfullscreen></iframe>

A Microsoft fornece não apenas a identidade que você usa em todos os lugares, mas também um conjunto de ferramentas para automatizar, ajudar a proteger e gerenciar TI em sua organização. Mesmo após o advento da computação em nuvem, ainda há demanda de gerenciar e controlar tarefas de TI como chamadas de assistência técnica para redefinir senhas de usuário, gerenciamento de grupo de usuários e solicitações do aplicativo. O que complica ainda mais as coisas, os funcionários estão agora levando seus dispositivos pessoais para o trabalho e usar aplicativos de SaaS prontamente disponíveis. Isso torna mantêm o controle sobre seus aplicativos em plataformas de nuvem pública e data centers corporativos um desafio significativo.

[!INCLUDE [identity](../../includes/azure-ad-licenses.md)]

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>Conectar-se o Active Directory no local com o Azure AD e Office 365
As organizações que fizeram grandes investimentos no Active Directory local podem estender esses investimentos para a nuvem, integrar seus diretórios locais ao Azure AD em [gerenciamento de identidade híbrida](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Isso torna os usuários mais produtivos fornecendo uma identidade comum para acessar recursos, independentemente do local. Usuários e organizações podem usar o logon único (SSO) para acessar os recursos locais e serviços de nuvem como o Office 365.

O [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) é a única ferramenta necessária para fazer integração. O Azure AD Connect fornece os recursos para dar suporte às suas necessidades de sincronização de identidades e substitui as versões mais antigas das ferramentas de integração de identidade, como DirSync e Azure AD Sync. Com o Azure AD Connect, gerenciamento de identidade e a sincronização entre local e o Azure AD é habilitado por meio de:

- Sincronização - esse componente é responsável pela criação de usuários, grupos e outros objetos. Também é responsável por garantir que as informações de identidade dos usuários e grupos locais correspondam às da nuvem. Write-back de senha também pode ser habilitado para a manutenção da sincronização de diretórios locais quando um usuário atualiza sua senha no AD do Azure.
- AD FS – a federação é uma capacidade opcional oferecida pelo Azure AD Connect que pode ser usada para configurar um ambiente híbrido usando uma infraestrutura do AD FS local. A federação pode ser usada pelas organizações para lidar com implantações complexas, como logon único, imposição de política de conexão do AD e cartão inteligente ou MFA de terceiros.
- Monitoramento de Integridade - o [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health) pode fornecer monitoramento robusto e fornecer um local central no portal do Azure para exibir essa atividade.

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>Aumentar a produtividade e reduzir os custos de assistência técnica com um únicos e autoatendimento experiências de logon

Os funcionários são mais produtivos quando têm um único nome de usuário e senha para se lembrar e uma experiência consistente de cada dispositivo. Também ganham tempo quando podem executar tarefas de autoatendimento como [redefinir uma senha esquecida](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) ou solicitar acesso a um aplicativo sem aguardar a assistência técnica.

O Azure AD [estende o Active Directory local](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) para a nuvem, permitindo que os usuários usem suas contas organizacionais primárias para seus dispositivos ingressados em domínio, recursos da empresa e todos os aplicativos SaaS e Web necessários para trabalhar. Além de fazer com que não seja necessário lembrar de vários conjuntos de nomes de usuário e de senhas, o acesso a aplicativos dos usuários também pode ser automaticamente provisionado (ou desprovisionado) com base na associação ao grupo da organização e no status como funcionário. E você pode controlar o acesso para aplicativos da galeria ou para seus próprios aplicativos locais que você já desenvolveu e publicado por meio de [com o Proxy de aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

## <a name="manage-and-control-access-to-corporate-resources"></a>Gerenciar e controlar o acesso aos recursos corporativos
As soluções de gerenciamento de acesso e identidade da Microsoft ajudam a TI a proteger o acesso a aplicativos e recursos no datacenter corporativo e na nuvem, permitindo níveis de validação adicionais, como a [autenticação multifator](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) e as [políticas de acesso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). O monitoramento de atividade suspeita por meio de alertas, auditoria e relatórios de segurança avançados ajuda a reduzir potenciais problemas de segurança.

Políticas de acesso condicional no Azure AD Premium dão a você, o admin corporativo, a capacidade de criar regras de acesso baseadas em políticas para qualquer aplicativo conectado ao Azure AD (aplicativos SaaS, aplicativos personalizados em execução na nuvem ou aplicativos Web locais). O Azure AD avalia essas políticas em tempo real e as impõe sempre que um usuário tenta acessar um aplicativo. As políticas de proteção de identidade do Azure permitem que você tome providências automaticamente se uma atividade suspeita for descoberta. Essas ações podem incluir bloquear o acesso a usuários em risco, impor autenticação multifator e redefinir senhas de usuário se parecer que as credenciais foram comprometidas.


## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management

[Com o Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started), incluído com a oferta do Azure Active Directory Premium P2, permite que você descubra, restringir e monitorar contas administrativas e o acesso a recursos no Active Directory do Azure e outros serviços online da Microsoft. Ele também ajuda a administrar o acesso administrativo sob demanda para o período exato que é necessário.

Privileged Identity Management pode impor os direitos de administrador sob demanda para que os administradores podem solicitar elevação de autenticado, temporária multifator de seus privilégios para pré-configurado períodos de tempo antes que suas contas retornam a um estado de usuário normal.

## <a name="benefits-of-azure-identity"></a>Benefícios da Identidade do Azure

Com o gerenciamento de identidades do Azure, você pode:

-   Criar e gerenciar uma identidade única para cada usuário em toda a sua empresa, mantendo os usuários, grupos e dispositivos em sincronia com o [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

-   Fornecer acesso de logon único para seus aplicativos incluindo milhares de aplicativos SaaS pré-integrados ou fornecer acesso remoto seguro para aplicativos SaaS de locais usando o [com o Proxy de aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

-   Habilitar segurança de acesso do aplicativo por meio da aplicação da [autenticação multifator](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) com base em regras para aplicativos locais e na nuvem.

-   Melhorar a produtividade do usuário com [de redefinição de senha de autoatendimento](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)e o grupo e o aplicativo acessar solicitações usando o [portal MyApps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-user-help).

-   Aproveitar o [alta disponibilidade e confiabilidade](https://docs.microsoft.com/azure/architecture/resiliency/high-availability-azure-applications) de um empresariais em todo o mundo, solução de gerenciamento de identidades e acesso baseado em nuvem.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre as soluções de identidade do Azure](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions)