---
title: Gerenciamento governado da nuvem do Azure AD para cargas de trabalho locais – Azure
description: Este tópico descreve o gerenciamento governado na nuvem para cargas de trabalho locais.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b4d1041b9d330227fadf31f6afc1804174ea2ad
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340842"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Como o Azure AD fornece gerenciamento regido na nuvem para cargas de trabalho locais

O Azure Active Directory (Azure AD) é uma solução de IDaaS (identidade como serviço) abrangente usada por milhões de organizações que abrangem todos os aspectos de identidade, gerenciamento de acesso e segurança. O Azure AD tem mais de um bilhão de identidades de usuário e ajuda os usuários a entrar e acessar os dois com segurança:

* Recursos externos, como Microsoft 365, o portal do Azure e milhares de outros aplicativos SaaS (software como serviço).
* Recursos internos, como aplicativos na rede corporativa e na intranet de uma organização, juntamente com qualquer aplicativo de nuvem desenvolvido por essa organização.

As organizações podem usar o Azure AD se forem ' nuvem pura ' ou como uma implantação ' híbrida ' se tiverem cargas de trabalho locais. Uma implantação híbrida do Azure AD pode fazer parte de uma estratégia para uma organização migrar seus ativos de ti para a nuvem ou para continuar a integrar a infraestrutura local existente junto com os novos serviços de nuvem.

Historicamente, as organizações ' híbridas ' viram o Azure AD como uma extensão de sua infraestrutura local existente. Nessas implantações, a administração de governança de identidade local, o Windows Server Active Directory ou outros sistemas de diretório internos, são os pontos de controle e os usuários e grupos são sincronizados a partir desses sistemas para um diretório de nuvem, como o Azure AD. Depois que essas identidades estiverem na nuvem, elas poderão ser disponibilizadas para Microsoft 365, Azure e outros aplicativos.

![Ciclo de vida de identidade](media/cloud-governed-management-for-on-premises//image1.png)

À medida que as organizações movem mais de sua infraestrutura de ti junto com seus aplicativos para a nuvem, muitos estão procurando a segurança aprimorada e os recursos de gerenciamento simplificados do gerenciamento de identidade como um serviço. Os recursos do IDaaS fornecidos pela nuvem no Azure AD aceleram a transição para o gerenciamento regido pela nuvem, fornecendo as soluções e os recursos que permitem às organizações adotar e mover rapidamente mais de seu gerenciamento de identidades de sistemas locais tradicionais para o Azure AD, ao mesmo tempo em que continua oferecendo suporte a aplicativos existentes e novos.

Este documento descreve a estratégia da Microsoft para o IDaaS híbrido, além de descrever como as organizações podem usar o Azure AD para seus aplicativos existentes.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>A abordagem do Azure AD para o gerenciamento de identidades governada na nuvem

À medida que as organizações fazem a transição para a nuvem, elas precisam de garantias de que têm controles sobre o seu ambiente completo-mais segurança e mais visibilidade em atividades, com suporte pela automação e por percepções proativas. "**Gerenciamento regido na nuvem**" descreve como as organizações gerenciam e regem seus usuários, aplicativos, grupos e dispositivos da nuvem.

Neste mundo moderno, as organizações precisam ser capazes de gerenciar efetivamente em escala, devido à proliferação de aplicativos SaaS e à crescente função de colaboração e identidades externas. O novo cenário de risco da nuvem significa que uma organização deve ser mais responsiva – um ator mal-intencionado que compromete um usuário de nuvem a afetar os aplicativos locais e de nuvem.

Em particular, as organizações híbridas precisam ser capazes de delegar e automatizar tarefas, que historicamente fizeram isso manualmente. Para automatizar tarefas, elas precisam de APIs e processos que orquestram o ciclo de vida dos diferentes recursos relacionados à identidade (usuários, grupos, aplicativos, dispositivos), para que possam delegar o gerenciamento diário desses recursos a mais indivíduos fora da equipe de ti central. O Azure AD atende a esses requisitos por meio do gerenciamento de conta de usuário e da autenticação nativa para usuários sem a necessidade de infraestrutura de identidade local. A não criação de infraestruturas locais pode beneficiar as organizações que têm novas comunidades de usuários, como parceiros de negócios, que não se originaram em seu diretório local, mas cujo gerenciamento de acesso é essencial para alcançar resultados de negócios.

Além disso, o gerenciamento não está completo sem a governança---e a governança nesse novo mundo é uma parte integrada do sistema de identidade em vez de um complemento. A governança de identidade fornece às organizações a capacidade de gerenciar o ciclo de vida de identidade e acesso entre funcionários, parceiros de negócios e fornecedores, além de serviços e aplicativos.

A incorporação da governança de identidade facilita a habilitação da organização para a transição para o gerenciamento regido na nuvem, permite que ela seja dimensionada, atenda a novos desafios com convidados e fornece informações mais aprofundadas e automação do que os clientes tinham com a infraestrutura local. A governança nesse novo mundo significa a capacidade de uma organização de ter transparência, visibilidade e controles adequados sobre o acesso aos recursos dentro da organização. Com o Azure AD, as equipes de operações de segurança e auditoria têm visibilidade sobre quem tem---e quem deve ter acesso a quais recursos na organização (em quais dispositivos), o que esses usuários estão fazendo com esse acesso e se a organização tem e usa os controles apropriados para remover ou restringir o acesso de acordo com as políticas normativas ou da empresa.

O novo modelo de gerenciamento beneficia organizações com aplicativos SaaS e LOB (linha de negócios), pois eles podem facilmente gerenciar e proteger o acesso a esses aplicativos. Integrando aplicativos com o Azure AD, as organizações poderão usar e gerenciar o acesso entre as identidades originadas na nuvem e no local de forma consistente. O gerenciamento do ciclo de vida do aplicativo se torna mais automatizado e o Azure AD fornece informações detalhadas sobre o uso do aplicativo que não era facilmente viável no gerenciamento de identidades local. Por meio do Azure AD, Microsoft 365 recursos de autoatendimento de grupos e equipes, as organizações podem criar facilmente grupos para gerenciamento de acesso e colaboração, adicionar ou remover usuários na nuvem para habilitar os requisitos de colaboração e gerenciamento de acesso.

Selecionar os recursos corretos do Azure AD para gerenciamento regido na nuvem depende dos aplicativos a serem usados e de como esses aplicativos serão integrados ao Azure AD. As seções a seguir descrevem as abordagens a serem tomadas para aplicativos integrados ao AD e aplicativos que usam protocolos de Federação (por exemplo, SAML, OAuth ou OpenID Connect).

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Gerenciamento regido pela nuvem para aplicativos integrados ao AD

O Azure AD melhora o gerenciamento de aplicativos integrados de Active Directory locais de uma organização por meio de acesso remoto seguro e acesso condicional a esses aplicativos. Além disso, o Azure AD também fornece gerenciamento de ciclo de vida da conta e gerenciamento de credenciais para as contas existentes do AD do usuário, incluindo:

* **Acesso remoto seguro e acesso condicional para aplicativos locais**

Para muitas organizações, a primeira etapa no gerenciamento do acesso da nuvem para aplicativos Web e remotos baseados em área de trabalho do AD local integrado é implantar o [proxy de aplicativo](../manage-apps/application-proxy.md) na frente desses aplicativos para fornecer acesso remoto seguro.

Após um logon único para o Azure AD, os usuários podem acessar aplicativos locais e de nuvem por meio de uma URL externa ou um portal interno do aplicativo. Por exemplo, o proxy de aplicativo fornece acesso remoto e logon único para Área de Trabalho Remota, SharePoint, bem como aplicativos como tableau e Qlik Sense, e aplicativos LOB (linha de negócios). Além disso, as políticas de acesso condicional podem incluir a exibição dos [termos de uso](../conditional-access/terms-of-use.md) e [a garantia de que o usuário concordou com eles](../conditional-access/require-tou.md) antes de poder acessar um aplicativo.

![Arquitetura do proxy de aplicativo](media/cloud-governed-management-for-on-premises/image2.png)

* **Gerenciamento automático do ciclo de vida para contas Active Directory**

A governança de identidade ajuda as organizações a alcançar um equilíbrio entre a *produtividade* ---com que rapidez uma pessoa pode ter acesso aos recursos de que precisam, como quando elas se unem à organização? ---e *segurança* ---como o acesso deve ser alterado ao longo do tempo, como quando o status de emprego dessa pessoa é alterado? O gerenciamento do ciclo de vida de identidades é a base para governança de identidade, e governança efetiva em escala requer modernizar a infraestrutura de gerenciamento do ciclo de vida de identidades para aplicativos.

Para muitas organizações, o ciclo de vida da identidade para os funcionários está vinculado à representação desse usuário em um sistema HCM (gerenciamento de capital humano). Para organizações que usam o workday como seu sistema HCM, o Azure AD pode garantir que as contas de usuário no AD sejam [automaticamente provisionadas e desprovisionadas para trabalhadores no workday](../saas-apps/workday-inbound-tutorial.md). Isso leva a uma produtividade de usuário aprimorada por meio da automação de contas do Birthright e gerencia o risco, garantindo que o acesso ao aplicativo seja atualizado automaticamente quando um usuário alterar funções ou sair da organização. O [plano de implantação](https://aka.ms/WorkdayDeploymentPlan) de provisionamento de usuário controlado por workday é um guia passo a passo que orienta as organizações por meio da implementação de práticas recomendadas do Workday para Active Directory solução de provisionamento de usuário em um processo de cinco etapas.

Azure AD Premium também inclui Microsoft Identity Manager, que pode importar registros de outros sistemas HCM locais, incluindo SAP, Oracle eBusiness e Oracle PeopleSoft.

A colaboração entre empresas exige cada vez mais a concessão de acesso a pessoas fora da sua organização. A colaboração [B2B do Azure ad](/azure/active-directory/b2b/) permite que as organizações compartilhem com segurança seus aplicativos e serviços com usuários convidados e parceiros externos, mantendo o controle sobre seus próprios dados corporativos.

O Azure AD pode [criar automaticamente contas no AD para usuários convidados](../external-identities/hybrid-cloud-to-on-premises.md) , conforme necessário, permitindo que os convidados de negócios acessem aplicativos integrados do AD local sem precisar de outra senha. As organizações podem configurar [políticas de MFA (autenticação multifator) para os usuários convidados](../external-identities/conditional-access.md)para que as verificações de MFA sejam feitas durante a autenticação do proxy de aplicativo. Além disso, todas as [revisões de acesso](../governance/manage-guest-access-with-access-reviews.md) feitas em usuários B2B de nuvem se aplicam a usuários locais. Por exemplo, se o usuário de nuvem for excluído por meio de políticas de gerenciamento do ciclo de vida, o usuário local também será excluído.

**Gerenciamento de credenciais para contas de Active Directory** A redefinição de senha de autoatendimento do Azure AD permite que os usuários que esqueceram suas senhas sejam reautenticados e redefinam suas senhas, com as senhas alteradas [gravadas no local Active Directory](../authentication/concept-sspr-writeback.md). O processo de redefinição de senha também pode usar as políticas de senha de Active Directory local: quando um usuário redefine sua senha, ele é verificado para garantir que ele atenda à política de Active Directory local antes de confirmá-la para esse diretório. O [plano de implantação](../authentication/howto-sspr-deployment.md) de redefinição de senha de autoatendimento descreve as práticas recomendadas para distribuir a redefinição de senha de autoatendimento aos usuários por meio de experiências integradas à Web e ao Windows.

![Arquitetura de SSPR do Azure AD](media/cloud-governed-management-for-on-premises/image3.png)

Por fim, para organizações que permitem que os usuários alterem suas senhas no AD, o AD pode ser configurado para usar a mesma política de senha que a organização está usando no Azure AD por meio do [recurso de proteção de senha do Azure ad](../authentication/concept-password-ban-bad-on-premises.md), atualmente em visualização pública.

Quando uma organização está pronta para mover um aplicativo integrado ao AD para a nuvem movendo o sistema operacional que hospeda o aplicativo para o Azure, [Azure AD Domain Services](../../active-directory-domain-services/overview.md) fornece serviços de domínio compatíveis com o AD (como ingresso no domínio, diretiva de grupo, LDAP e autenticação Kerberos/NTLM). O Azure AD Domain Services integra-se com o locatário do Azure AD existente da organização, possibilitando que os usuários entrem usando suas credenciais corporativas. Além disso, grupos existentes e contas de usuário podem ser usados para proteger o acesso aos recursos, garantindo um "deslocamento" mais suave de recursos locais para os serviços de infraestrutura do Azure.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Gerenciamento regido pela nuvem para aplicativos baseados em Federação local

Para uma organização que já usa um provedor de identidade local, a movimentação de aplicativos para o Azure AD permite um acesso mais seguro e uma experiência administrativa mais fácil para o gerenciamento de Federação. O Azure AD permite configurar controles de acesso por aplicativo granulares, incluindo a autenticação multifator do Azure AD, usando o acesso condicional do Azure AD. O Azure AD dá suporte a mais recursos, incluindo certificados de assinatura de token específicos do aplicativo e datas de expiração de certificado configurável. Esses recursos, ferramentas e diretrizes permitem que as organizações desativem seus provedores de identidade locais. A própria ti da Microsoft, por exemplo, moveu 17.987 aplicativos do Serviços de Federação do Active Directory (AD FS) interno da Microsoft (AD FS) para o Azure AD.

![Evolução do Azure AD](media/cloud-governed-management-for-on-premises/image5.png)

Para começar a migrar aplicativos federados para o Azure AD como o provedor de identidade, consulte https://aka.ms/migrateapps isso inclui links para:

* O white paper [migrar seus aplicativos para Azure Active Directory](https://aka.ms/migrateapps/whitepaper), que apresenta os benefícios da migração e descreve como planejar a migração em quatro fases claramente destacadas: descoberta, classificação, migração e gerenciamento contínuo. Você será orientado por como pensar sobre o processo e dividir seu projeto em pedaços fáceis de consumir. Ao longo do documento, haverá links para recursos importantes que irão ajudá-lo durante o processo.

* O guia de solução [que migra a autenticação de aplicativos de serviços de Federação do Active Directory (AD FS) para Azure Active Directory](../manage-apps/migrate-adfs-apps-to-azure.md) explora mais detalhadamente as mesmas quatro fases de planejamento e execução de um projeto de migração de aplicativos. Neste guia, você aprenderá a aplicar essas fases ao objetivo específico de mover um aplicativo de Serviços de Federação do Active Directory (AD FS) (AD FS) para o Azure AD.

* O [script de preparação para migração de serviços de Federação do Active Directory (AD FS)](https://aka.ms/migrateapps/adfstools) pode ser executado em servidores de Serviços de Federação do Active Directory (AD FS) (AD FS) locais existentes para determinar a prontidão dos aplicativos para migração para o Azure AD.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Gerenciamento de acesso contínuo em aplicativos de nuvem e locais

As organizações precisam de um processo para gerenciar o acesso que é escalonável. Os usuários continuam acumulando os direitos de acesso e acabam além do que foi inicialmente provisionado para eles. Além disso, as organizações empresariais precisam ser capazes de dimensionar com eficiência para desenvolver e impor a política de acesso e os controles de forma contínua.

Em geral, a IT delega as decisões de aprovação de acesso aos tomadores de decisão empresariais. Além disso, a IT pode envolver os próprios usuários. Por exemplo, os usuários que acessam os dados confidenciais do cliente no aplicativo de marketing da empresa na Europa precisam conhecer as políticas da empresa. Os usuários convidados também podem estar cientes dos requisitos de manipulação de dados em uma organização para a qual foram convidados.

As organizações podem automatizar o processo de ciclo de vida de acesso por meio de tecnologias como [grupos dinâmicos](../enterprise-users/groups-dynamic-membership.md), juntamente com o provisionamento de usuário para [aplicativos SaaS](../saas-apps/tutorial-list.md)ou [aplicativos integrados usando o sistema para o padrão scim (gerenciamento de identidade entre domínios](../app-provisioning/use-scim-to-provision-users-and-groups.md)). As organizações também podem controlar quais [usuários convidados têm acesso a aplicativos locais](../external-identities/hybrid-cloud-to-on-premises.md). Esses direitos de acesso podem então ser revisados regularmente usando [revisões de acesso do Azure AD](../governance/access-reviews-overview.md) recorrentes.

## <a name="future-directions"></a>Direções futuras

Em ambientes híbridos, a estratégia da Microsoft é habilitar implantações em que a **nuvem é o plano de controle para identidade**, e diretórios locais e outros sistemas de identidade, como Active Directory e outros aplicativos locais, são o destino para provisionar usuários com acesso. Essa estratégia continuará a garantir os direitos, identidades e acesso nesses aplicativos e cargas de trabalho que dependem deles. Nesse estado final, as organizações poderão impulsionar totalmente a produtividade do usuário final da nuvem.

![Arquitetura do Azure AD](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como começar a usar essa jornada, consulte os planos de implantação do Azure AD, localizados em <https://aka.ms/deploymentplans> . Eles fornecem orientação de ponta a ponta sobre como implantar recursos do Azure Active Directory (Azure AD). Cada plano explica o valor comercial, as considerações de planejamento, o design e os procedimentos operacionais necessários para distribuir com êxito os recursos comuns do Azure AD. A Microsoft atualiza continuamente os planos de implantação com as práticas recomendadas aprendidas com implantações de clientes e outros comentários quando adicionamos novos recursos para gerenciar a partir da nuvem com o Azure AD.