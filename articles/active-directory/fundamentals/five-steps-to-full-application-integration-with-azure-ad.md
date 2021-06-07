---
title: Cinco etapas para integrar todos os seus aplicativos com o Azure AD
description: Este guia explica como integrar todos os seus aplicativos com o Azure AD. Em cada etapa, explicamos o valor e fornecemos links para recursos que explicam os detalhes técnicos.
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: 7fd9e504448d55b4a2ef8c10b4ba1176cb2e3402
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96172628"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>Cinco etapas para integrar todos os seus aplicativos com o Azure AD

O Azure AD (Azure Active Directory) é o serviço de gerenciamento de acesso e identidades baseado em nuvem da Microsoft. O Azure AD fornece soluções seguras de autenticação e autorização para que clientes, parceiros e funcionários possam acessar os aplicativos de que precisam. Com o Azure AD, o [acesso condicional](../conditional-access/overview.md), [a autenticação multifator](../authentication/concept-mfa-howitworks.md), o [logon único](../hybrid/how-to-connect-sso.md)e o [provisionamento automático de usuário](../app-provisioning/user-provisioning.md) tornam o gerenciamento de identidade e acesso fácil e seguro.

Se sua empresa tiver uma assinatura Microsoft 365, provavelmente você [já usa](/office365/enterprise/about-office-365-identity) o Azure AD. No entanto, o Azure AD pode ser usado para todos os seus aplicativos e [centralizar o gerenciamento de aplicativos](../manage-apps/common-scenarios.md) você pode usar os mesmos recursos, ferramentas e políticas de gerenciamento de identidade em todo o seu portfólio de aplicativos. Isso fornecerá uma solução unificada que melhora a segurança, reduz os custos, aumenta a produtividade e permite garantir a conformidade. E você terá acesso remoto a aplicativos locais.

Este guia explica como integrar todos os seus aplicativos com o Azure AD. Em cada etapa, explicamos o valor e fornecemos links para recursos que explicam os detalhes técnicos. Apresentamos essas etapas em uma ordem que recomendamos. No entanto, você pode ir para qualquer parte do processo para começar com qualquer um que adicione o maior valor para você.

Outros recursos deste tópico, incluindo White papers detalhados do processo de negócios, que podem ser encontrados em nossos [recursos para migrar aplicativos para Azure Active Directory](../manage-apps/migration-resources.md) página.

## <a name="1-use-azure-ad-for-new-applications"></a>1. usar o Azure AD para novos aplicativos

Primeiro, concentre-se nos aplicativos adquiridos recentemente. Quando sua empresa começar a usar um novo aplicativo, [adicione-o ao seu locatário do Azure ad](../manage-apps/add-application-portal.md) imediatamente. Configure uma política da empresa para que a adição de novos aplicativos ao Azure AD seja a prática padrão em sua organização. Isso é minimamente prejudicial aos processos de negócios existentes e permite investigar e provar o valor que você obtém da integração de aplicativos sem alterar a forma como as pessoas fazem negócios em seu ambiente hoje.

O Azure Active Directory (AD do Azure) tem uma galeria que contém milhares de aplicativos previamente integrados para que seja mais fácil começar. Você pode [Adicionar um aplicativo da Galeria à sua organização do Azure ad](../manage-apps/add-application-portal.md) com [tutoriais](../saas-apps/tutorial-list.md) passo a passo para a integração com aplicativos populares, como:

- [ServiceNow](../saas-apps/servicenow-tutorial.md)
- [Workday](../saas-apps/workday-tutorial.md)
- [Salesforce](../saas-apps/salesforce-tutorial.md)
- [AWS](../saas-apps/amazon-web-service-tutorial.md)
- [Margem de atraso](../saas-apps/slack-tutorial.md)

Além disso, você pode [integrar aplicativos que não estão na Galeria](../manage-apps/view-applications-portal.md), incluindo qualquer aplicativo que já exista em sua organização ou qualquer aplicativo de terceiros de um fornecedor que ainda não faz parte da galeria do Azure AD. Você também pode [Adicionar seu aplicativo à Galeria](../develop/v2-howto-app-gallery-listing.md) se ele não estiver lá.

Por fim, você também pode integrar os aplicativos desenvolvidos internamente. Isso é abordado na etapa cinco deste guia.

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. determinar o uso do aplicativo e priorizar o trabalho existente

Em seguida, descubra os aplicativos que os funcionários estão frequentemente usando e Priorize seu trabalho para integrá-los ao Azure AD.

Você pode começar usando as ferramentas de [descoberta de nuvem](/cloud-app-security/tutorial-shadow-it) do Microsoft Cloud app Security para descobrir e gerenciar "sombrear" em sua rede (ou seja, aplicativos não gerenciados pelo departamento de ti). Você pode [usar a ATP (proteção avançada contra ameaças) do Microsoft defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) para simplificar e estender o processo de descoberta.

Além disso, você pode usar o [AD FS relatório de atividade do aplicativo](../manage-apps/migrate-adfs-application-activity.md) no portal do Azure para descobrir todos os aplicativos do AD FS em sua organização, o número de usuários exclusivos que entraram neles e a compatibilidade para integrá-los ao Azure AD.

Depois de descobrir seu panorama existente, você desejará [criar um plano](../manage-apps/migration-resources.md) e priorizar os aplicativos de maior prioridade a serem integrados. Alguns exemplos de perguntas que você pode fazer para orientar esse processo são:

- Quais aplicativos são os mais usados?
- Quais são os difuso?
- Quais aplicativos serão encerrados no futuro, tornando a movimentação desnecessária?
- Quais aplicativos precisam permanecer no local e não podem ser movidos para a nuvem?

Você verá os maiores benefícios e economias de custos quando todos os seus aplicativos estiverem integrados e você não depender mais de várias soluções de identidade. No entanto, você terá um gerenciamento de identidade mais fácil e maior segurança à medida que você move o gradual para esse objetivo. Você deseja usar esse tempo para priorizar seu trabalho e decidir o que faz sentido para sua situação.

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. integrar aplicativos que dependem de outros provedores de identidade

Durante o processo de descoberta, você pode encontrar aplicativos que não foram controlados pelo departamento de ti, o que deixa seus dados e recursos vulneráveis. Você também pode ter aplicativos que usam soluções de identidade alternativas, incluindo Serviços de Federação do Active Directory (AD FS) (ADFS) ou outros provedores de identidade. Considere como você pode consolidar seu gerenciamento de identidade e acesso para economizar dinheiro e aumentar a segurança. Reduzir o número de soluções de identidade que você terá:

- Economize dinheiro eliminando a necessidade de provisionamento e autenticação de usuário local, bem como taxas de licenciamento pagas para outros provedores de identidade de nuvem para o mesmo serviço.
- Reduza a sobrecarga administrativa e habilite uma segurança mais rígida com menos redundância em seu processo de gerenciamento de identidade e acesso.
- Permita que os funcionários obtenham acesso seguro de logon único a todos os aplicativos necessários por meio do [portal do myapps](../manage-apps/access-panel-collections.md).
- Melhore a inteligência dos serviços relacionados à [proteção de identidade](../identity-protection/overview-identity-protection.md) do Azure AD, como o acesso condicional, aumentando a quantidade de dados obtida do uso do aplicativo e amplie seus benefícios para os aplicativos recém-adicionados.

Publicamos diretrizes para gerenciar o processo comercial de integração de aplicativos com o Azure AD, incluindo um [cartaz](https://aka.ms/AppOnePager) e uma [apresentação](https://aka.ms/AppGuideline) que você pode usar para fazer com que os proprietários de negócios e aplicativos saibam e interessados. Você pode modificar esses exemplos com sua própria identidade visual e publicá-los em sua organização por meio do portal da empresa, do boletim informativo ou de outra mídia ao concluir esse processo.

Um bom lugar para começar é avaliar o uso de Serviços de Federação do Active Directory (AD FS) (ADFS). Muitas organizações usam o ADFS para autenticação com aplicativos SaaS, aplicativos de linha de negócios personalizados e Microsoft 365 e aplicativos baseados no Azure AD:

![O diagrama mostra aplicativos locais, aplicativos de linha de negócios, aplicativos SaaS e, por meio do Azure AD, o Office 365 todos conectando com linhas pontilhadas em Active Directory e AD FS.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

Você pode atualizar essa configuração [substituindo o ADFS pelo Azure ad como o centro](../manage-apps/migrate-adfs-apps-to-azure.md) da sua solução de gerenciamento de identidade. Isso habilita o logon para cada aplicativo que seus funcionários desejam acessar e torna mais fácil para os funcionários encontrar qualquer aplicativo de negócios necessário por meio do [portal myapps](../user-help/my-apps-portal-end-user-access.md), além dos outros benefícios mencionados acima.

![O diagrama mostra aplicativos locais por meio de Active Directory e AD FS, aplicativos de linha de negócios, aplicativos SaaS e o Office 365 todos conectando-se a linhas pontilhadas em Azure Active Directory.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

Depois que o Azure AD se tornar o provedor de identidade central, você poderá mudar do ADFS completamente, em vez de usar uma solução federada. Os aplicativos que usavam o ADFS anteriormente para autenticação agora podem usar o Azure AD sozinho.

![O diagrama mostra o local, aplicativos de linha de negócios, aplicativos SaaS e o Office 365 todos conectando-se com linhas pontilhadas em Azure Active Directory. Active Directory e AD FS não estão presentes.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

Você também pode migrar aplicativos que usam um provedor de identidade baseado em nuvem diferente para o Azure AD. Sua organização pode ter várias soluções de IAM (gerenciamento de acesso de identidade) em vigor. A migração para uma infraestrutura do Azure AD é uma oportunidade de reduzir as dependências em licenças IAM (no local ou na nuvem) e nos custos de infraestrutura. Nos casos em que você já deve ter pago pelo Azure AD por meio de licenças do M365, não há motivo para pagar o custo adicional de outra solução IAM.

## <a name="4-integrate-on-premises-applications"></a>4. integrar aplicativos locais

Tradicionalmente, os aplicativos eram mantidos seguros, permitindo o acesso somente enquanto estiverem conectados à rede corporativa. No entanto, em um mundo cada vez mais conectado, queremos permitir o acesso a aplicativos para clientes, parceiros e/ou funcionários, independentemente de onde estão no mundo. O [azure proxy de aplicativo do AD](../manage-apps/what-is-application-proxy.md) (AppProxy) é um recurso do Azure AD que conecta seus aplicativos locais existentes ao Azure AD e não requer que você mantenha servidores de borda ou outras infraestruturas adicionais para fazer isso.

![Um diagrama mostra o serviço de proxy de aplicativo em ação. Um usuário acessa " https://sales.contoso.com " e sua solicitação é redirecionada por meio https://sales-contoso.msappproxy.net de "" em Azure Active Directory ao endereço local " http://sales "](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

Você pode usar [o tutorial: adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md) para habilitar o proxy de aplicativo e adicionar um aplicativo local ao seu locatário do Azure AD.

Além disso, você pode integrar os controladores de entrega de aplicativos como F5 Big-IP APM ou Zscaler Private Access. Integrando-os com o Azure AD, você obtém a autenticação moderna e o gerenciamento de identidade do Azure AD junto com os recursos de gerenciamento e segurança de tráfego do produto de parceiro. Chamamos essa solução de [acesso híbrido seguro](../manage-apps/secure-hybrid-access.md). Se você usar qualquer um dos serviços a seguir hoje, temos tutoriais que o orientarão sobre como integrá-los ao Azure AD.

- [EAA (Akamai Enterprise Application Access)](../saas-apps/akamai-tutorial.md)
- [ADC (controlador de entrega de aplicativos) da Citrix](../saas-apps/citrix-netscaler-tutorial.md) (anteriormente conhecido como Citrix Netscaler)
- [F5 Big-IP APM](../saas-apps/headerf5-tutorial.md)
- [Zscaler Private Access (ZPA)](../saas-apps/zscalerprivateaccess-tutorial.md)

## <a name="5-integrate-apps-your-developers-build"></a>5. integrar aplicativos que seus desenvolvedores criam

Para aplicativos criados em sua empresa, os desenvolvedores podem usar a plataforma de [identidade da Microsoft](../develop/index.yml) para implementar a autenticação e a autorização. Aplicativos integrados à plataforma com o ser [registrado no Azure ad](../develop/quickstart-register-app.md) e gerenciados como qualquer outro aplicativo em seu portfólio.

Os desenvolvedores podem usar a plataforma para aplicativos de uso interno e aplicativos voltados para o cliente, e há outros benefícios que acompanham o uso da plataforma. As [MSAL (bibliotecas de autenticação da Microsoft)](../develop/msal-overview.md), que fazem parte da plataforma, permitem que os desenvolvedores habilitem experiências modernas como a autenticação multifator e o uso de chaves de segurança para acessar seus aplicativos sem a necessidade de implementá-lo por conta própria. Além disso, aplicativos integrados com a plataforma Microsoft Identity podem acessar [Microsoft Graph](../develop/microsoft-graph-intro.md) um ponto de extremidade unificado de API fornecendo os dados de Microsoft 365 que descrevem os padrões de produtividade, identidade e segurança em uma organização. Os desenvolvedores podem usar essas informações para implementar recursos que aumentam a produtividade de seus usuários. Por exemplo, identificando as pessoas com as quais o usuário esteve interagindo recentemente e identificando-las na interface do usuário do aplicativo.

Temos uma [série de vídeos](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX) que fornece uma introdução abrangente à plataforma, bem como [muitos exemplos de código](../develop/sample-v2-code.md) em linguagens e plataformas com suporte.

## <a name="next-steps"></a>Próximas etapas

- [Recursos para migrar aplicativos para o Azure Active Directory](../manage-apps/migration-resources.md)