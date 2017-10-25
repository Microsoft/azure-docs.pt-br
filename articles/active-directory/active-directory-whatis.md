---
title: "O que é o Active Directory do Azure?"
description: Use o Active Directory do Azure para estender suas identidades locais existentes para a nuvem ou desenvolva aplicativos integrados do AD do Azure.
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
ms.reviewer: jsnow
ms.author: jeffgilb
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.custom: it-pro
ms.openlocfilehash: b6746afd508832afbd54153851b6f2ae404af147
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-active-directory"></a>O que é o Active Directory do Azure?
O Azure AD (Azure Active Directory) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. O Azure AD combina serviços de diretório principais, governança avançada de identidade e gerenciamento de acesso a aplicativos. O Azure AD também oferece uma plataforma avançada, baseada em padrões, que permite que os desenvolvedores ofereçam controle de acesso aos aplicativos com base em regras e políticas centralizadas. 

Para administradores de TI, o AD do Azure oferece uma solução acessível e fácil de usar para fornecer a funcionários e parceiros de negócios acesso de logon único (SSO) a [milhares de aplicativos SaaS na nuvem](active-directory-saas-tutorial-list.md) , como Office365, Salesforce.com, DropBox e Concur.

Para desenvolvedores de aplicativos, o AD do Azure permite que você se concentre na criação de seu aplicativo, tornando rápida e simples a integração de uma solução de gerenciamento de identidade da mais alta qualidade e utilizada por milhões de empresas em todo o mundo.

O AD do Azure também inclui um conjunto completo de recursos de gerenciamento de identidade, incluindo autenticação multifator, registro de dispositivos, gerenciamento de senhas de autoatendimento, gerenciamento de grupos de autoatendimento, gerenciamento de contas com privilégios, controle de acesso com base em função, monitoramento de uso de aplicativos, auditoria avançada e alertas e monitoramento de segurança. Esses recursos podem ajudar a proteger aplicativos com base na nuvem, simplificar os processos de TI, cortar custos e ajudar a garantir que as metas de conformidade corporativa sejam atingidas.

Além disso, com apenas [quatro cliques](./connect/active-directory-aadconnect-get-started-express.md), o AD do Azure pode ser integrado a um Active Directory do Windows Server existente, fornecendo às organizações a capacidade de aproveitar os investimentos locais de identidade existentes para gerenciar o acesso à nuvem com base em aplicativos SaaS.

Se você for um cliente do Office 365, Azure ou Dynamics CRM Online, talvez não tenha percebido que já está usando o Azure AD. Todo locatário do Office 365, Azure e Dynamics CRM já é, na verdade, um locatário do Azure AD. Quando desejar, você poderá começar a usar esse locatário para gerenciar o acesso a milhares de outros aplicativos na nuvem aos quais o AD do Azure se integra!

![Pilha do Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="how-reliable-is-azure-ad"></a>O AD do Azure é confiável?
O projeto multilocatário, distribuído geograficamente e de alta disponibilidade do AD do Azure significa que você pode confiar nele para suas necessidades comerciais mais críticas. Com execução em 28 data centers em todo o mundo com failover automático, você terá o conforto de saber que o AD do Azure é altamente confiável e que, mesmo se um data center ficar inativo, cópias de seu diretório de dados estarão ativas em pelo menos dois data centers mais dispersos regionalmente e disponíveis para acesso instantâneo.

Para obter mais detalhes, consulte [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Escolher uma edição
Todos os serviços comerciais do Microsoft Online dependem do Azure Active Directory (Azure AD) para entrada e outras necessidades de identidade. Se você assinar qualquer um dos serviços comerciais do Microsoft Online (por exemplo, Office 365 ou Microsoft Azure), obterá o Azure AD com acesso a todos os recursos gratuitos. Com a edição Gratuita do Active Directory do Azure, você pode gerenciar usuários e grupos, sincronizar com diretórios locais, obter logon único no Azure, Office 365 e em milhares de aplicativos SaaS populares como Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox e muito mais. 

Para aprimorar seu Active Directory do Azure, é possível adicionar recursos pagos usando as edições do Azure Active Directory Basic, Premium P1 e Premium P2. As edições pagas do Active Directory do Azure são criadas em seu diretório gratuito existente, fornecendo recursos corporativos que abrangem autoatendimento, monitoramento avançado, relatórios de segurança, MFA (Autenticação Multifator) e acesso seguro para a sua força de trabalho móvel.

> [!NOTE]
> Para obter as opções de preço dessas edições, confira [Preço do Active Directory do Azure](https://azure.microsoft.com/pricing/details/active-directory/). O Azure Active Directory Premium P1, Premium P2 e Azure Active Directory Basic não têm suporte atualmente na China. Entre em contato conosco no Fórum do Azure Active Directory para obter mais informações.
>

* **Azure Active Directory Basic** - Projetada para profissionais de tarefas com necessidades que priorizam a nuvem, esta edição fornece soluções de gerenciamento de identidades por autoatendimento e de acesso a aplicativos centrados na nuvem. Com a Basic edition do Active Directory do Azure, você obtém recursos que aumentam sua produtividade e reduzem os custos, como o gerenciamento de acesso baseado em grupo, redefinição de senha por autoatendimento para aplicativos em nuvem e o Proxy de Aplicativo do Active Directory do Azure (para publicar aplicativos web locais usando o Active Directory do Azure), tudo com o apoio de um SLA de nível empresarial de 99,9% de tempo de atividade.
* **Azure Active Directory Premium P1** – Projetado para capacitar as organizações com necessidades mais exigentes de gerenciamento de identidades e acesso, a Active Directory Premium edition do Azure adiciona funcionalidades de gerenciamento de identidades de nível empresarial rica em recursos e permite que os usuários híbridos acessem diretamente as funcionalidades locais e na nuvem. Esta edição inclui tudo o que necessário para o profissional de informações e os administradores de identidades em ambientes híbridos no acesso a aplicativos, IAM (gerenciamento de identidades e acesso) por autoatendimento, proteção de identidade e segurança na nuvem. Ela dá suporte a recursos avançados de administração e delegação, como grupos dinâmicos e gerenciamento de grupos por autoatendimento. Inclui o Microsoft Identity Manager (um pacote de gerenciamento de identidades e acesso local) e fornece recursos de write-back na nuvem, possibilitando soluções como a redefinição de senha por autoatendimento para seus usuários locais.
* **Azure Active Directory Premium P2** – Projetado com proteção avançada para todos os usuários e administradores, essa nova oferta inclui todos os recursos no Azure AD Premium P1, bem como nossa nova Identity Protection e Privileged Identity Management. O Azure Active Directory Identity Protection utiliza bilhões de sinais para fornecer acesso condicional com base em risco aos seus aplicativos e dados importantes da empresa. Podemos também ajudar a gerenciar e proteger contas privilegiadas com o Azure Active Directory Privileged Identity Management para descobrir, restringir e monitorar os administradores e o acesso a recursos e fornecer acesso just-in-time quando necessário.  

> [!NOTE]
> Uma série de recursos do Active Directory do Azure está disponível por meio de edições “pré-pagas”:
>
> * O Active Directory B2C é a solução de gerenciamento de identidades e acesso para seus aplicativos voltados para o consumidor. Para obter mais detalhes, veja [Active Directory B2C do Azure](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * A Autenticação Multifator do Azure pode ser usado por usuário ou por provedores de autenticação. Para obter mais detalhes, veja [O que é a Autenticação Multifator do Azure?](../multi-factor-authentication/multi-factor-authentication.md)
>

## <a name="how-can-i-get-started"></a>Como posso começar?

**Se você for administrador de TI:**

* [Experimente!](https://azure.microsoft.com/trial/get-started-active-directory/) - inscreva-se hoje mesmo em uma avaliação gratuita de 30 dias e implante sua primeira solução em nuvem em menos de 5 minutos usando este link

* Leia [Introdução ao Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium) para obter dicas e truques sobre como colocar um locatário do Azure AD em funcionamento rapidamente

**Se você for um desenvolvedor:**
 
* Confira nosso [Guia de desenvolvedores](active-directory-developers-guide.md) para o Active Directory do Azure

* [Inicie uma avaliação](https://azure.microsoft.com/trial/get-started-active-directory/): inscreva-se para uma avaliação gratuita de 30 dias hoje mesmo e comece a integrar seus aplicativos ao Azure AD

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre os conceitos básicos do gerenciamento de identidade e acesso do Azure](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
