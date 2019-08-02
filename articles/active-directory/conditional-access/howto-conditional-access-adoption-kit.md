---
title: Kit de adoção de acesso condicional - Azure Active Directory
description: Adotando o acesso condicional do Azure AD para acessar recursos
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cc4ff5fb528760be8c910f3da7d5691a6aae0d8
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67387567"
---
#  <a name="adopting-azure-ad-conditional-access"></a>Adotando o acesso condicional do Azure AD

Em um mundo de dispositivos móveis e nuvem em primeiro lugar, os usuários podem acessar recursos da sua organização de qualquer lugar usando diferentes tipos de dispositivos e aplicativos. Como resultado, concentrando-se apenas em quem pode acessar um recurso não é mais suficiente. Você pode controlar quem tem acesso e identificar onde o usuário está e qual dispositivo está sendo usado e muito mais.

Para fornecer esse controle **acesso condicional do Azure Active Directory (AD)** permite que você especifique as condições de qualquer usuário deve atender para acessar um aplicativo, como autenticação multifator (MFA). Usando políticas de acesso condicional controla como os usuários autorizados (usuários que receberam acesso a um aplicativo de nuvem) acessar aplicativos na nuvem em condições específicas. Consulte a [o que é acesso condicional no Azure Active Directory](overview.md#conditional-access-policies) para obter mais informações.

## <a name="key-benefits"></a>Principais benefícios

Os principais benefícios do uso de acesso condicional do Azure AD são:

* **Aumente a produtividade:** Políticas de acesso (AC) condicional permitirá que você direcione o ponto em que os usuários serão solicitados a usar o MFA, acesso bloqueado ou são necessárias para usar um dispositivo confiável. Por exemplo, você pode definir políticas, como apenas solicitando que os usuários para MFA em um aplicativo quando estiver fora da rede corporativa. Reduzir as solicitações do MFA mantém os usuários mais produtivo do que se eles têm a MFA toda vez que entrar. Além disso, o acesso condicional do Azure AD permite que você especificar políticas por usuário base e também cria políticas específicas do aplicativo.
* **Gerencie o risco:** Habilitar as políticas de acesso condicional fornece proteção de identidade de escala de nuvem, recursos de controle de acesso com base em risco e suporte nativo a autenticação multifator. O acoplamento de acesso condicional com o identity protection permite que você defina quando o acesso a um aplicativo está bloqueado ou restrito.
* **Governança e conformidade de endereço:** Auditoria de solicitações de acesso e as aprovações para o aplicativo e Noções básicas sobre o uso do aplicativo geral são mais fácil com o Azure AD porque ele dá suporte a logs de auditoria nativo para cada solicitação de acesso do aplicativo executada. A auditoria inclui a identidade do solicitante, data solicitada, justificativa de negócios, status de aprovação e identidade do aprovador. Esses dados também estão disponíveis de uma API, que permite que a importação desses dados em um incidente de segurança e sistema de monitoramento de eventos (SIEM) de escolha.
* **Gerencie o custo:** Mover as políticas de acesso ao AD do Azure reduz a dependência em personalizado ou soluções locais, como o Active Directory Federation Services (ADFS) para acesso condicional, reduzindo o custo de execução que a infraestrutura.

## <a name="customer-case-studies"></a>Estudos de caso de cliente

Descubra como a maioria das organizações usar acesso condicional do Azure AD para definir e implementar as decisões de controle de acesso automatizado para acessar aplicativos na nuvem com base em condições. As histórias em destaque a seguir demonstram como essas necessidades dos clientes são atendidas.

* [**A Wipro** unidades de produtividade móvel com ferramentas de segurança de nuvem da Microsoft para melhorar a contratos com clientes.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) As políticas de acesso condicional no Azure AD tem habilitado a empresa compartilhar documentos, recursos e aplicativos com entidades externas confiáveis---que pode usar suas próprias credenciais---o enquanto mantém o controle sobre seus próprios dados corporativos.
* [**Accenture** protege sua mudança para a nuvem com o Microsoft Cloud App security](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security) Accenture é avaliar o [controle de aplicativo de acesso condicional](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) recurso do Cloud App Security, que usa o Active Directory do Azure Acesso condicional do diretório como aplicativo acesso com base em determinadas condições. LePenske diz que esse recurso pode ser útil para, digamos, habilitando o acesso de arquivo somente leitura ao mesmo tempo, proibindo downloads.
* [**Aramex** entrega limitado - empresa Global de transportes e logística cria office conectados à nuvem com a solução de gerenciamento de identidades e acesso](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Garantindo acesso seguro era especialmente difícil com funcionários remotos do Aramex. A empresa está aplicando acesso condicional para permitir que esses funcionários remotos acessam seus aplicativos SaaS de fora da rede. A regra de acesso condicional decidirá se deseja impor a autenticação multifator, dando apenas as pessoas o acesso correto.

Para saber mais sobre as experiências de clientes e parceiros sobre o acesso condicional do Azure AD, visite - [ver as pessoas coisas incríveis que estão fazendo com o Azure](https://azure.microsoft.com/case-studies/?service=active-directory).

## <a name="announcements"></a>Anúncios

O Azure AD recebe melhorias de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, consulte [o que há de novo no Azure Active Directory?](../fundamentals/whats-new.md)

Blogs recentes pela comunidade de tecnologia e divisão de identidade da Microsoft:

* 24 de setembro de 2018, [acesso condicional do Azure Active Directory no Azure Databricks](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 21 de setembro de 2018, [controles personalizados estão em visualização pública de acesso condicional do Azure AD](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* 21 de setembro de 2018, [suporte a acesso condicional do Azure AD para acesso limitado com o Microsoft Cloud App Security agora está disponível](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 21 de setembro de 2018, [acesso condicional do Azure AD: Suporte a navegador para plataformas iOS/Android agora em visualização gerenciados](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 21 de setembro de 2018, [acesso condicional do Azure AD para códigos de país está em visualização pública](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 21 de setembro de 2018, [do Azure AD termos de uso já está disponível](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>Recursos de aprendizagem

Siga os links abaixo para obter uma visão geral das funções de acesso condicional do AD como o Azure.

* Saiba mais "[o que é acesso condicional no Azure Active Directory?](overview.md)"
* Saber "[quais são as condições no acesso condicional do Azure Active Directory?](conditions.md)"
* Saber "[qual é a condição de localização no acesso condicional do Azure Active Directory?](location-condition.md)"
* Saber "[quais são acesso controla o acesso condicional do Azure Active Directory?](controls.md)"
* Localizar "[o que é o que se ferramenta do Azure Active Directory de acesso condicional?"](what-if-tool.md)
* Siga [práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md)

Além disso, consulte os links a seguir para obter diretrizes para proteger o acesso a todos os serviços que são integrados ao Azure Active Directory.

* [O que é proteção de linha de base (versão prévia)?](baseline-protection.md) Proteção de linha de base garante que você tenha pelo menos o nível de linha de base de segurança habilitado em seu ambiente do Active Directory do Azure.
* [Configurações de acesso do dispositivo e identidade](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations). Descreve como configurar o acesso seguro aos serviços de nuvem por meio do Enterprise Mobility + produtos de segurança com a implementação de um ambiente recomendada e a configuração, incluindo um conjunto prescrito de políticas de acesso condicional e as funcionalidades relacionadas.
* [Referência de configurações de acesso condicional do Active Directory do Azure](technical-reference.md). Saiba mais:
   * Quais aplicativos usam o acesso condicional?
   * Quais serviços são habilitados com acesso condicional?
* [Habilitar o acesso condicional do Azure Active Directory para acesso de usuário segura](https://www.youtube.com/watch?v=eLAYBwjCGoA). Assista a este vídeo para saber como o acesso condicional desempenha um papel nas cargas de trabalho de outros Enterprise e Mobility Suite.

### <a name="training-videos"></a>Vídeos de treinamento

**Acesso condicional no Enterprise Mobility + Security**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**Acesso condicional baseado em dispositivo**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**Habilitar o Azure Active Directory para acesso condicional para acesso de usuário segura**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>Cursos online

Consulte os seguintes cursos de acesso condicional e muito mais sobre [Pluralsight.com](https://www.pluralsight.com/):

* Pluralsight.com: [Gerenciamento de identidade de design no Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * "Este curso explica os principais itens que você precisa saber para projetar sua solução de gerenciamento de identidade com o Azure AD." Acesso condicional do Azure AD é abordado em "Usando funções e controle de acesso com o Azure AD" módulo.

* Pluralsight.com: [Autenticação de design para o Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * "Este curso explica como usar o AD do Azure para resolver todos os seus requisitos de autenticação de nuvem." Acesso condicional do Azure AD é abordado no módulo "Requisitos para diferentes cenários de autenticação".

* Pluralsight.com: [Autorização de design para o Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * "Este curso ensina a usar as opções de autorização disponíveis com o Azure e o Azure AD." Acesso condicional do Azure AD é abordado no módulo "Autorização com o Azure Resource Manager e o Azure AD".

### <a name="books"></a>Manuais

* O ' Reilly - [implementar soluções do Azure - Second Edition.](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * "Entrar em funcionamento com serviços do Azure e saiba como implementá-los em sua organização. Acesso condicional do Azure AD é abordado no capítulo [Implantando e sincronização do Active Directory do Azure](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml). "

* Wiley- [dominar os serviços de infraestrutura do Microsoft Azure](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298)
   * "Here is tudo o que você precisa entender, avaliar, implantar e manter ambientes que utilizam o Microsoft Azure."

## <a name="white-papers"></a>White papers

* Publicado em 18 de dezembro de 2018, [criar uma estratégia de gerenciamento de controle de acesso flexível com o Azure Active Directory](../authentication/concept-resilient-controls.md)
   * Este documento fornece orientação sobre estratégias de uma organização pode adotar para fornecer resiliência para reduzir o risco de bloqueio durante interrupções imprevistas.

* Publicado em 18 de setembro de 2018, [recursos para migrar aplicativos para o Azure Active Directory](../manage-apps/migration-resources.md)
   * Este white paper inclui uma lista de recursos para ajudá-lo a migrar o acesso a aplicativos e autenticação ao Azure Active Directory (Azure AD).

* Publicado em 12 de julho de 2018 [plano gráfico de conformidade e segurança do Azure: Aplicativo Web de PaaS de hospedagem para cargas de trabalho OFICIAL do Reino Unido](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Especificações técnicas do Azure consistem em documentos de diretrizes e modelos de automação que implantam arquiteturas baseado em nuvem para oferecer soluções para cenários que têm requisitos de conformidade ou de capacitação.

## <a name="guidance-for-it-administrators"></a>Diretrizes para os administradores de TI

Entrar para o [portal do Azure](https://portal.azure.com/) como um administrador global, administrador de segurança ou administrador de acesso condicional. Consulte [permissões da função de administrador no Azure Active Directory.](../users-groups-roles/directory-assign-admin-roles.md)

Como um administrador de TI, use [acesso condicional do Azure AD](overview.md) para exigir que os usuários se autentiquem usando a autenticação multifator do Azure, entrar em uma rede confiável ou um dispositivo confiável.

Aqui estão alguns links úteis para ajudá-lo a começar:

* [Práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md)
* [Use o Azure AD as revisões de acesso para gerenciar usuários que foram excluídos de políticas de acesso condicional](../governance/conditional-access-exclusion.md)
* [Como: Planejar a implantação de acesso condicional no Azure Active Directory](plan-conditional-access.md)
* [Início Rápido: Exigir MFA para aplicativos específicos com acesso condicional do Azure Active Directory](app-based-mfa.md)
* [Início Rápido: Exigir a aceitação dos termos de uso antes de acessar os aplicativos de nuvem](require-tou.md)
* [Início Rápido: Bloquear o acesso quando um risco de sessão é detectado com acesso condicional do Azure Active Directory](app-sign-in-risk.md)
* [Perguntas frequentes sobre o acesso condicional do Azure AD](faqs.md)
   * Para outras perguntas, você também pode exibir o [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure).
   * Se você não encontrar a resposta para um problema, nossas equipes de suporte sempre estarão disponíveis para ajudá-lo ainda mais. Use [o suporte da Microsoft entre em contato com](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support).

### <a name="tutorials"></a>Tutoriais

* [**Início Rápido: Exigir MFA para aplicativos específicos com acesso condicional do Azure Active Directory**](app-based-mfa.md)
   * Neste início rápido mostra como configurar uma política de acesso condicional do Azure AD que requer autenticação multifator para um aplicativo de nuvem selecionado em seu ambiente.

* [**Início Rápido: Exigir termos de uso para ser aceito antes de acessar a aplicativos de nuvem**](require-tou.md)
   * Neste início rápido mostra como configurar uma política de acesso condicional do Azure AD que requer um ToU sejam aceitos para um aplicativo de nuvem selecionado em seu ambiente.

* [**Início Rápido: Bloquear o acesso quando um risco de sessão é detectado com acesso condicional do Azure Active Directory**](app-sign-in-risk.md)
   * Neste início rápido mostra como configurar uma política de acesso condicional que bloqueia uma entrada quando um nível de risco de entrada configurado foi detectado.

* [Tutorial: **Migrar uma política clássica que exige autenticação multifator no portal do Azure**](policy-migration-mfa.md)
   * Este tutorial mostra como migrar uma política clássica que exige a autenticação multifator (MFA) para um aplicativo de nuvem.

## <a name="end-user-readiness-and-communication"></a>Comunicação e a preparação do usuário final

Acesso condicional usa outros recursos do Azure AD que podem afetar a experiência do usuário final. Por exemplo, você pode usar a autenticação multifator do Azure para habilitar a autenticação forte para os usuários. Nesse caso, você usará os modelos do usuário final da MFA do Azure.

## <a name="next-steps"></a>Próximas etapas

* Iniciar sua implantação com o [documentação de planejamento de implantação de acesso condicional](plan-conditional-access.md).
