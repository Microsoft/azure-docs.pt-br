---
title: O que é o provisionamento de usuários automatizado no aplicativo SaaS no Azure AD
description: Uma introdução a como você pode usar o Azure AD para provisionar, desprovisionar e atualizar contínua e automaticamente as contas de usuário em vários aplicativos SaaS de terceiros.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: overview
ms.workload: identity
ms.date: 02/08/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: f899c4d67321482dab62729632fe031bb23f71ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99830661"
---
# <a name="what-is-automated-saas-app-user-provisioning-in-azure-ad"></a>O que é o provisionamento de usuários automatizado no aplicativo SaaS no Azure AD?

No Azure AD (Azure Active Directory), o termo **provisionamento de aplicativo** refere-se à criação automática de identidades e funções de usuário nos aplicativos de nuvem ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) aos quais os usuários precisam ter acesso. Além de criar identidades de usuário, o provisionamento automático inclui a manutenção e a remoção de identidades de usuário à medida que o status ou as funções mudam. Os cenários comuns incluem o provisionamento de um usuário do Azure AD em aplicativos como [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md), entre outros.

Apenas começando com o gerenciamento de aplicativos e o SSO (logon único) no Azure AD? Confira a [série de guias de início rápido](../manage-apps/view-applications-portal.md).

Para saber mais sobre o SCIM e participar da conversa da Tech Community, confira [Provisionamento com a Tech Community do SCIM](https://aka.ms/scimoverview).

![Diagrama de visão geral de provisionamento](./media/user-provisioning/provisioning-overview.png)

Esse recurso permite a você:

- **Automatizar o provisionamento**: Crie automaticamente novas contas nos sistemas certos quando novas pessoas entrarem em sua equipe ou organização.
- **Automatizar o desprovisionamento:** Desative automaticamente as contas nos sistemas certos quando as pessoas deixarem a equipe ou organização.
- **Sincronizar dados entre sistemas:** mantenha as identidades em seus aplicativos e sistemas atualizadas com base nas alterações no diretório ou em seu sistema de recursos humanos.
- **Provisionar grupos:** provisione grupos a aplicativos compatíveis com eles.
- **Controlar o acesso:** monitore e audite quem foi provisionado em seus aplicativos.
- **Implantar diretamente em cenários de campo marrom:** combine identidades existentes entre sistemas diferentes e permita uma integração fácil, mesmo quando os usuários já existem no sistema de destino.
- **Usar personalização avançada:** aproveite os mapeamentos personalizáveis de atributos que definem quais dados do usuário devem fluir do sistema de origem para o sistema de destino.
- **Obter alertas para eventos críticos:** o serviço de provisionamento fornece alertas para eventos críticos e permite a integração do Log Analytics, em que é possível definir alertas personalizados para atender às suas necessidades de negócios.

## <a name="benefits-of-automatic-provisioning"></a>Benefícios do provisionamento automático

Conforme o número de aplicativos usados em organizações modernas continua a crescer, os administradores de TI têm a tarefa de gerenciar o acesso em escala. Padrões como o SAML (Security Assertions Markup Language) ou o OIDC (Open ID Connect) permitem que os administradores configurem rapidamente o SSO (logon único), mas o acesso também exige que os usuários sejam provisionados no aplicativo. Para muitos administradores, o provisionamento significa criar manualmente todas as contas de usuário ou carregar arquivos CSV a cada semana, mas esses processos são demorados, caros e sujeitos a erros. Soluções como o JIT (just-in-time) do SAML foram adotadas para automatizar o provisionamento, mas as empresas também precisam de uma solução para desprovisionar usuários quando deixam a organização ou não precisam mais de acesso a determinados aplicativos com base na alteração de função.

Algumas motivações comuns para usar o provisionamento automático incluem:

- Maximizar a eficiência e a precisão dos processos de provisionamento.
- Economizar em custos associados à hospedagem e à manutenção dos scripts e das soluções de provisionamento personalizadas.
- Proteger sua organização removendo instantaneamente as identidades dos usuários dos principais aplicativos SaaS quando eles saírem da organização.
- Importar facilmente uma grande quantidade de usuários em um determinado aplicativo SaaS ou sistema.
- Ter um único conjunto de políticas para determinar quem é provisionado e quem pode entrar um aplicativo.

O provisionamento de usuários do Azure AD pode ajudar a vencer esses desafios. Para saber mais sobre como os clientes usam o provisionamento de usuários do Azure AD, leia o [estudo de caso da ASOS](https://aka.ms/asoscasestudy). O vídeo abaixo fornece uma visão geral do provisionamento de usuários no Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quais aplicativos e sistemas posso usar com o provisionamento de usuário automático do Azure AD?

O Azure AD apresenta suporte pré-integrado para vários sistemas de recursos humanos e aplicativos SaaS populares, bem como suporte genérico para aplicativos que implementam partes específicas do [padrão SCIM 2.0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

* **Aplicativos pré-integrados (aplicativos SaaS da galeria)** . É possível encontrar todos os aplicativos para os quais o Azure AD é compatível com um conector de provisionamento pré-integrado na [lista de tutoriais de aplicativo para provisionamento de usuários](../saas-apps/tutorial-list.md). Os aplicativos pré-integrados listados na galeria geralmente usam APIs de gerenciamento de usuários com base em SCIM 2.0 para provisionamento. 

   ![Logotipo do Salesforce](./media/user-provisioning/gallery-app-logos.png)

   Se você quiser solicitar um novo aplicativo para provisionamento, poderá [solicitar que seu aplicativo seja integrado à nossa galeria de aplicativos](../develop/v2-howto-app-gallery-listing.md). Para uma solicitação de provisionamento de usuários, exigimos que o aplicativo tenha um ponto de extremidade em conformidade com SCIM. Solicite que o fornecedor do aplicativo siga o padrão SCIM para que possamos integrar o aplicativo à nossa plataforma rapidamente.

* **Aplicativos compatíveis com SCIM 2.0**. Para obter informações sobre como se conectar genericamente a aplicativos que implementam APIs de gerenciamento de usuários com base em SCIM 2.0, confira [Criar um ponto de extremidade SCIM e configurar o provisionamento de usuários](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>O que é o SCIM (Sistema de Gerenciamento de Usuários entre Domínios)?

Para ajudar a automatizar o provisionamento e o desprovisionamento, os aplicativos expõem APIs de usuário e de grupo proprietárias. No entanto, qualquer pessoa que tenha tentado gerenciar usuários em mais de um aplicativo informará a você que cada aplicativo tenta executar as mesmas ações simples, como criar ou atualizar usuários, adicionar usuários a grupos ou desprovisionar usuários. Ainda assim, todas essas ações simples são implementadas de maneira um pouco diferente, usando caminhos de ponto de extremidade diferentes, métodos diferentes para especificar informações de usuário e um esquema diferente para representar cada elemento dessas informações.

Para resolver esses desafios, a especificação SCIM fornece um esquema de usuário comum para ajudar os usuários a migrar dos aplicativos e para eles, bem como administrá-los. O SCIM está se tornando o padrão verdadeiro para provisionamento e, quando usado em conjunto com padrões de federação como SAML ou OpenID Connect, ele fornece aos administradores uma solução de ponta a ponta baseada em padrões para o gerenciamento de acesso.

Para obter diretrizes detalhadas sobre como desenvolver um ponto de extremidade SCIM para automatizar o provisionamento e o desprovisionamento de usuários e grupos para um aplicativo, confira [Criar um ponto de extremidade SCIM e configurar o provisionamento de usuários](use-scim-to-provision-users-and-groups.md). Para aplicativos pré-integrados na galeria (Slack, Azure Databricks, Snowflake etc.), você pode ignorar a documentação do desenvolvedor e usar os tutoriais fornecidos [aqui](../saas-apps/tutorial-list.md).

## <a name="manual-vs-automatic-provisioning"></a>Provisionamento manual versus automático

Os aplicativos na galeria do Azure AD são compatíveis com um de dois modos de provisionamento:

* O provisionamento **manual** significa que ainda não há nenhum conector de provisionamento automático do Azure AD para o aplicativo. As contas de usuário devem ser criadas manualmente, por exemplo, adicionando usuários diretamente no portal administrativo do aplicativo ou carregando uma planilha com detalhes da conta de usuário. Consulte a documentação fornecida pelo aplicativo ou entre em contato com o desenvolvedor do aplicativo para determinar quais mecanismos estão disponíveis.

* **Automático** significa que o conector de provisionamento do Azure AD foi desenvolvido para este aplicativo. Você deve seguir o tutorial de instalação específico para a configuração de provisionamento para o aplicativo. Tutoriais de aplicativos podem ser encontrados em [lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](../saas-apps/tutorial-list.md).

Na galeria do Azure AD, os aplicativos compatíveis com o provisionamento automático são designados por um ícone de **Provisionamento**. Alterne para a nova experiência de versão prévia da galeria para ver esses ícones (na faixa localizada na parte superior da **página Adicionar um aplicativo**, selecione o link que diz **Clique aqui para experimentar a galeria de aplicativos nova e aprimorada**).

![Ícone de provisionamento na galeria de aplicativos](./media/user-provisioning/browse-gallery.png)

O modo de provisionamento compatível com um aplicativo também ficará visível na guia **Provisionamento** depois que você tiver adicionado o aplicativo aos seus **Aplicativos Enterprise**.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Como configurar o provisionamento automático para um aplicativo?

Para aplicativos pré-integrados listados na galeria, diretrizes passo a passo estão disponíveis para configuração do provisionamento automático. Confira a [lista de tutoriais para aplicativos integrados da galeria](../saas-apps/tutorial-list.md). O vídeo a seguir demonstra como configurar o provisionamento automático de usuários para o SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Para outros aplicativos que dão suporte ao SCIM 2.0, siga as etapas no artigo [Criar um ponto de extremidade SCIM e configurar o provisionamento de usuários](use-scim-to-provision-users-and-groups.md).


## <a name="next-steps"></a>Próximas etapas

- [Lista de tutoriais sobre como integrar aplicativos SaaS](../saas-apps/tutorial-list.md)
- [Como personalizar os mapeamentos de atributos para provisionamento de usuários](customize-application-attributes.md)
- [Filtros de escopo para provisionamento de usuários](define-conditional-rules-for-provisioning-user-accounts.md)
