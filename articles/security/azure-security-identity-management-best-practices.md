---
title: "Práticas recomendadas de segurança de identidade e acesso do Azure | Microsoft Docs"
description: "Este artigo fornece um conjunto de práticas recomendadas para gerenciamento de identidade e controle de acesso usando recursos internos do Azure."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: yurid
ms.openlocfilehash: 50f9073d3c35bd9dcfd826ff44e767fb69558757
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Práticas recomendadas de Gerenciamento de Identidade do Azure e segurança de controle de acesso
Muitos consideram a identidade como a nova camada de limite para segurança, assumindo a função antes exercida pela perspectiva centrada em rede tradicional. Essa evolução do eixo primário para a atenção e investimentos em relação à segurança decorre do fato de que os perímetros de rede estão se tornando cada vez mais porosos e a defesa do perímetro não pode ser tão eficiente quanto era antes da explosão de dispositivos [BYOD](http://aka.ms/byodcg) e aplicativos de nuvem.

Neste artigo, discutiremos um conjunto de práticas recomendadas de segurança de controle de acesso e gerenciamento de identidade do Azure. Essas práticas recomendadas derivam da nossa experiência com o [Azure AD](../active-directory/active-directory-whatis.md) e da experiência de clientes como você.

Para cada prática recomendada, vamos explicar:

* O que é a prática recomendada
* Por que é ideal habilitar essa prática recomendada
* O que poderá acontecer se você não habilitar a prática recomendada
* Possíveis alternativas à prática recomendada
* Como você pode aprender a habilitar a prática recomendada

Este artigo sobre práticas recomendadas de segurança de controle de acesso e gerenciamento de identidade do Azure se baseia em um consenso e nos recursos e conjuntos de recursos da plataforma Azure existentes quando este artigo foi escrito. As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

As práticas recomendadas de segurança de controle de acesso e gerenciamento de identidade do Azure discutidas neste artigo incluem:

* Centralizar o gerenciamento de identidade
* Habilitar SSO (Logon Único)
* Implantar o gerenciamento de senhas
* Impor MFA (autenticação multifator) para usuários
* Usar RBAC (controle de acesso baseado em função)
* Controlar os locais em que os recursos são criados usando o gerenciador de recursos
* Guiar os desenvolvedores para aproveitar os recursos de identidade para aplicativos SaaS
* Monitorar ativamente as atividades suspeitas

## <a name="centralize-your-identity-management"></a>Centralizar o gerenciamento de identidade
Uma etapa importante para proteger sua identidade é garantir que a TI possa gerenciar contas de um único local em relação a onde a conta foi criada. Embora a maioria das organizações de TI tenha seu diretório de contas primário no local, as implantações de nuvem híbridas estão se tornando mais comuns, e é importante entender como integrar os diretórios locais e na nuvem e fornecer uma experiência perfeita ao usuário final.

Para obter esse cenário de [identidade híbrida](../active-directory/active-directory-hybrid-identity-design-considerations-overview.md), recomendamos duas opções:

* Sincronizar seu diretório local com seu diretório na nuvem usando o Azure AD Connect
* Federar sua identidade local com seu diretório de nuvem usando [Serviços de Federação do Active Directory](https://msdn.microsoft.com/library/bb897402.aspx) (AD FS)

As organizações que não integrarem suas identidades locais a sua identidade de nuvem terão maior sobrecarga administrativa no gerenciamento de contas, o que aumenta a probabilidade de erros e violações de segurança.

Para saber mais sobre a sincronização do Azure AD, leia o artigo [Integração de suas identidades locais ao Azure Active Directory](../active-directory/active-directory-aadconnect.md).

## <a name="enable-single-sign-on-sso"></a>Habilitar SSO (Logon Único)
Quando você tem vários diretórios para gerenciar, isso se torna um problema administrativo não apenas para a TI, mas também para os usuários finais, que precisam se lembrar de várias senhas. Usando o [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/), você dará aos usuários a capacidade de usar o mesmo conjunto de credenciais para entrar e acessar os recursos de que precisam, independentemente de ser um recurso local ou estar localizado na nuvem.

Use o SSO para habilitar os usuários a acessar seus [aplicativos de SaaS](../active-directory/active-directory-appssoaccess-whatis.md) com base em sua conta organizacional no Azure AD. Isso é aplicável não apenas a aplicativos de SaaS da Microsoft, mas também a outros aplicativos, como [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) e [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). O aplicativo pode ser configurado para usar o Azure AD como um provedor de [identidade baseada em SAML](../active-directory/fundamentals-identity.md). Como controle de segurança, o AD do Azure somente emitirá um token permitindo que eles entrem no aplicativo se receberem acesso usando o AD do Azure. Você pode conceder acesso diretamente ou por meio de um grupo do qual eles fazem parte.

> [!NOTE]
> a decisão de usar o SSO terá impacto sobre como você integra seu diretório local a seu diretório na nuvem. Se quiser o SSO, você precisará usar a federação, pois a sincronização de diretórios só fornecerá a [mesma experiência de logon](../active-directory/active-directory-aadconnect.md).
>
>

As organizações que não impõem o SSO para seus usuários e aplicativos estão mais expostas a cenários em que os usuários terão várias senhas, o que aumenta diretamente a probabilidade de reutilização de senhas ou o uso de senhas de fracas.

Você pode saber mais sobre o SSO do Azure AD lendo o artigo [Gerenciamento e personalização do AD FS com o Azure AD Connect](../active-directory/active-directory-aadconnect-federation-management.md).

## <a name="deploy-password-management"></a>Implantar o gerenciamento de senhas
Em cenários em que você tem vários locatários ou deseja habilitar os usuários a [redefinir suas próprias senhas](../active-directory/active-directory-passwords-update-your-own-password.md), é importante usar políticas de segurança apropriadas para evitar abuso. No Azure, você pode aproveitar o recurso de redefinição de senha de autoatendimento e personalizar as opções de segurança para atender às necessidades de negócios.

É particularmente importante obter comentários desses usuários e aprender com suas experiências quando tentarem executar essas etapas. Com base nessas experiências, elabore um plano para reduzir os problemas potenciais que podem ocorrer durante a implantação para um grupo maior. Também é recomendável que você use o [relatório de Atividade de registro de redefinição de senha](../active-directory/active-directory-passwords-get-insights.md) para monitorar os usuários que estão se registrando.

As organizações que desejam evitar chamadas de suporte de alteração de senha, mas habilitam os usuários a redefinir suas próprias senhas são mais suscetíveis a um maior volume de chamadas à central de serviços devido a problemas de senha. Em organizações com vários locatários, é imperativo que você implemente esse tipo de recurso e habilite os usuários a realizar a redefinição de senha dentro dos limites de segurança estabelecidos na política de segurança.

Você pode saber mais sobre a redefinição de senha lendo o artigo [Implantação de Gerenciamento de Senhas e treinando usuários para utilizar](../active-directory/active-directory-passwords-best-practices.md).

## <a name="enforce-multi-factor-authentication-mfa-for-users"></a>Impor MFA (autenticação multifator) para usuários
Para organizações que precisam estar em conformidade com padrões do setor, como [PCI DSS versão 3.2](http://blog.pcisecuritystandards.org/preparing-for-pci-dss-32), a autenticação multifator é uma capacidade essencial para autenticar os usuários. Além de ser compatível com os padrões do setor, a imposição da MFA para autenticar os usuários também pode ajudar as organizações a reduzir o tipo de ataque de roubo de credenciais, como [PtH (Pass-the-Hash)](http://aka.ms/PtHPaper).

Ao habilitar o Azure MFA para seus usuários, você estará adicionando uma segunda camada de segurança nas entradas de usuário e transações. Nesse caso, uma transação pode ser o acesso a um documento localizado em um servidor de arquivos ou no SharePoint Online. O Azure MFA também ajuda o departamento de TI a reduzir a probabilidade de que uma credencial comprometida tenha acesso aos dados da organização.

Por exemplo: você impõe a MFA do Azure a seus usuários e a configura para usar uma chamada telefônica ou uma mensagem de texto como verificação. Se as credenciais do usuário estiverem comprometidas, o invasor não poderá acessar recurso algum, pois não terá acesso ao telefone do usuário. As organizações que não adicionam camadas adicionais de proteção de identidade são mais suscetíveis a ataques de roubo de credenciais, que podem levar ao comprometimento dos dados.

Uma alternativa para as organizações que desejam manter todo o controle da autenticação localmente é usar o [Servidor de Autenticação Multifator do Azure](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), também chamado de MFA local. Usando esse método você ainda poderá impor a autenticação multifator, mantendo o servidor MFA local.

Para saber mais sobre a MFA do Azure, leia o artigo [Introdução à Autenticação Multifator do Azure na nuvem](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="use-role-based-access-control-rbac"></a>Usar RBAC (controle de acesso baseado em função)
Restringir o acesso com base nos princípios de segurança de [divulgação restrita àqueles diretamente interessados](https://en.wikipedia.org/wiki/Need_to_know) e no [privilégio mínimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege) é fundamental para as organizações que desejam impor políticas de segurança para acesso a dados. O RBAC (Controle de Acesso Baseado em Função) pode ser usado para atribuir permissões a usuários, grupos e aplicativos em um determinado escopo. O escopo de uma atribuição de função pode ser uma assinatura, um grupo de recursos ou um único recurso.

Você pode aproveitar funções [RBAC internas](../active-directory/role-based-access-built-in-roles.md) no Azure para atribuir privilégios aos usuários. Considere usar a função *Colaborador da Conta de Armazenamento* para operadores de nuvem que precisam gerenciar contas de armazenamento e a função *Colaborador da Conta de Armazenamento Clássica* para gerenciar contas de armazenamento clássico. Para operadores de nuvem que precisam gerenciar VMs e contas de armazenamento, considere adicioná-los à função *Colaborador da Máquina Virtual*.

As organizações que não impõem o controle de acesso de dados utilizando recursos como o RBAC podem estar dando mais privilégios do que o necessário para seus usuários. Isso pode levar ao comprometimento de dados permitindo que os usuários acessem certos tipos de dados (por exemplo, com alto impacto nos negócios) que não deveriam acessar.

Você pode aprender mais sobre o RBAC do Azure lendo o artigo [Controle de Acesso Baseado em Função do Azure](../active-directory/role-based-access-control-configure.md).

## <a name="control-locations-where-resources-are-created-using-resource-manager"></a>Controlar os locais em que os recursos são criados usando o gerenciador de recursos
É muito importante habilitar os operadores de nuvem a executar tarefas, mas impedi-los de romper convenções que são necessárias para gerenciar recursos de sua organização. As organizações que desejam controlar os locais em que os recursos são criados devem codificar esses locais.

Para fazer isso, as organizações podem criar políticas de segurança com definições que descrevem as ações ou os recursos que são especificamente negados. Atribua essas definições de política no escopo desejado, como a assinatura, grupo de recursos ou um recurso individual.

> [!NOTE]
> isso não é o mesmo que o RBAC; na verdade, tira proveito do RBAC para autenticar os usuários que têm o privilégio para criar esses recursos.
>
>

Aproveite o [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) para criar políticas personalizadas também para cenários em que a organização deseja permitir operações apenas quando o centro de custo apropriado estiver associado; caso contrário, elas negarão a solicitação.

As organizações que não estão controlando como os recursos são criados são mais suscetíveis a usuários que podem usar o serviço de maneira imprópria, criando mais recursos do que o necessário. Proteger o processo de criação de recursos é uma etapa importante para proteger um cenário de multilocatário.

Você pode saber mais sobre como criar políticas com o Azure Resource Manager lendo o artigo [Usar Política para gerenciar recursos e controlar o acesso](../azure-resource-manager/resource-manager-policy.md).

## <a name="guide-developers-to-leverage-identity-capabilities-for-saas-apps"></a>Guiar os desenvolvedores para aproveitar os recursos de identidade para aplicativos SaaS
A identidade do usuário será aproveitada em muitos cenários, quando os usuários acessam [aplicativos de SaaS](https://azure.microsoft.com/marketplace/active-directory/all/) que pode ser integrados ao diretório local ou na nuvem. Em primeiro lugar, é recomendável que os desenvolvedores usem uma metodologia segura para desenvolver esses aplicativos, como o [Microsoft SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl/default.aspx). O Azure AD simplifica a autenticação para os desenvolvedores fornecendo identidade como um serviço, com suporte para protocolos de padrão do setor, como [OAuth 2.0](http://oauth.net/2/) e [OpenID Connect](http://openid.net/connect/), bem como bibliotecas de software livre para plataformas diferentes.

Registre qualquer aplicativo que terceiriza a autenticação no Azure AD; esse é um procedimento obrigatório. O motivo por trás disso é que o Azure AD precisa coordenar a comunicação com o aplicativo ao lidar com o SSO (entrada) ou trocar tokens. A sessão do usuário expira quando a vida útil do token emitido pelo Azure AD expira. Sempre avalie se o aplicativo deve usar esse tempo ou se você pode reduzir o tempo. A redução do tempo de vida pode agir como uma medida de segurança que forçará os usuários a sair com base em um período de inatividade.

As organizações que não impõem o controle de identidade para acessar aplicativos e não orientam seus desenvolvedores sobre como integrar com segurança os aplicativos ao sistema de gerenciamento de identidade podem ser mais suscetíveis ao tipo de ataque de roubo de credenciais, como o [gerenciamento de sessão e autenticação fraco descrito nos 10 melhores do OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/OWASP_Top_Ten_Cheat_Sheet).

Você pode saber mais sobre cenários de autenticação para aplicativos de SaaS lendo [Cenários de autenticação do Azure AD](../active-directory/active-directory-authentication-scenarios.md).

## <a name="actively-monitor-for-suspicious-activities"></a>Monitorar ativamente as atividades suspeitas
De acordo com o [relatório de violação de dados de 2016 da Verizon](http://www.verizonenterprise.com/verizon-insights-lab/dbir/2016/), o comprometimento de credenciais ainda está em crescimento e está se tornando um dos negócios mais lucrativos para os criminosos cibernéticos. Por esse motivo, é importante ter um sistema de monitoramento de identidade ativo em vigor que possa detectar rapidamente atividades de comportamento suspeito e disparar um alerta para a realização de mais investigações. O Azure AD tem dois recursos principais que podem ajudar as organizações a monitorar suas identidades: os [relatórios de anomalias](../active-directory/active-directory-view-access-usage-reports.md) do Azure AD Premium e a funcionalidade de [proteção de identidade](../active-directory/active-directory-identityprotection.md) do Azure AD.

Use os relatórios de anomalias para identificar tentativas de entrada [sem rastreamento](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md), ataques de [força bruta](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) contra uma conta específica, tentativas de entrar de várias localizações, entrada de [dispositivos infectados](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md) e endereços IP suspeitos. Lembre-se de que esses são relatórios. Em outras palavras, você deve ter processos e procedimentos em vigor para que os administradores de TI executem esses relatórios diariamente ou sob demanda (geralmente em um cenário de resposta a incidentes).

Em contraste, a proteção de identidade do Azure AD é um sistema de monitoramento ativo e sinalizará os riscos atuais em seu próprio painel. Além disso, você também receberá notificações diárias de resumo por email. É recomendável que você ajuste o nível de risco de acordo com seus requisitos de negócios. O nível de risco de um evento de risco é uma indicação (Alta, Média ou Baixa) da severidade do evento de risco. O nível de risco ajuda os usuários do Identity Protection a priorizarem as ações que devem ser executadas para reduzir os riscos para sua organização.

As organizações que não monitoram ativamente os seus sistemas de identidade estão em risco de ter as credenciais de usuários comprometidas. Sem o conhecimento de que atividades suspeitas estão ocorrendo usando essas credenciais, as organizações não serão capazes de reduzir esse tipo de ameaça.
Você pode saber mais sobre a proteção de identidade do Azure lendo [Proteção de Identidade do Azure Active Directory](../active-directory/active-directory-identityprotection.md).
