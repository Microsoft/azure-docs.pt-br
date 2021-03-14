---
title: Planejar uma implantação de provisionamento de usuário automático para Azure Active Directory
description: Diretrizes para planejar e executar o provisionamento automático de usuário
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 12/31/2020
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: 9f2b5bf1195682b200b5840459194506e29bcdc6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645081"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Planejar uma implantação de provisionamento do usuário automática

Muitas organizações contam com aplicativos SaaS (software como serviço), como ServiceNow, Zscaler e margem de atraso para produtividade do usuário final. Historicamente, a equipe de ti conta com métodos de provisionamento manual, como carregar arquivos CSV, ou usar scripts personalizados para gerenciar identidades de usuário com segurança em cada aplicativo SaaS. Esses processos são propensos a erros, inseguros e difíceis de gerenciar.

O provisionamento automático de usuário do Azure Active Directory (AD do Azure) simplifica esse processo ao automatizar com segurança a criação, a manutenção e a remoção de identidades de usuário em aplicativos SaaS com base em regras de negócio. Essa automação permite que você dimensione efetivamente seus sistemas de gerenciamento de identidades tanto em ambientes híbridos quanto em nuvem quanto você expande sua dependência em soluções baseadas em nuvem.

Consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../app-provisioning/user-provisioning.md) para entender melhor a funcionalidade.

## <a name="learn"></a>Learn

O provisionamento de usuário cria uma base para o controle de identidade contínuo e aprimora a qualidade dos processos de negócios que dependem de dados de identidade autoritativa.

### <a name="key-benefits"></a>Principais benefícios

Os principais benefícios de habilitar o provisionamento automático de usuário são:

* **Maior produtividade**. Você pode gerenciar identidades de usuário em aplicativos SaaS com uma única interface de gerenciamento de provisionamento de usuário. Essa interface tem um único conjunto de políticas de provisionamento.

* **Gerencie riscos**. Você pode aumentar a segurança automatizando as alterações com base no status do funcionário ou em associações de grupo que definem funções e/ou acesso.

* **Resolva a conformidade e a governança**. O Azure AD dá suporte a logs de auditoria nativos para cada solicitação de provisionamento de usuário. As solicitações são executadas nos sistemas de origem e de destino. Isso permite que você controle quem tem acesso a aplicativos de uma única tela.

* **Reduza o custo**. O provisionamento automático de usuário reduz os custos, evitando ineficiências e erros humanos associados ao provisionamento manual. Ele reduz a necessidade de soluções de provisionamento de usuário, scripts e logs de auditoria desenvolvidos de fato personalizado.

### <a name="licensing"></a>Licenças

O Azure AD fornece integração de autoatendimento de qualquer aplicativo usando modelos fornecidos no menu da Galeria de aplicativos. Para obter uma lista completa dos requisitos de licença, consulte a [página de licenciamento do Azure ad](https://azure.microsoft.com/pricing/details/active-directory/).

#### <a name="application-licensing"></a>Licenciamento de aplicativos

Você precisará das licenças apropriadas para os aplicativos que deseja provisionar automaticamente. Discuta com os proprietários do aplicativo se os usuários atribuídos ao aplicativo têm as licenças apropriadas para suas funções de aplicativo. Se o Azure AD gerencia o provisionamento automático com base em funções, as funções atribuídas no Azure AD devem ser alinhadas às licenças de aplicativo. Licenças incorretas de Propriedade do aplicativo podem levar a erros durante o provisionamento/atualização de um usuário.

### <a name="terms"></a>Termos

Este artigo usa os seguintes termos:

* Operações CRUD – ações executadas em contas de usuário: criar, ler, atualizar, excluir.

* SSO (logon único) – a capacidade de um usuário entrar uma vez e acessar todos os aplicativos habilitados para SSO. No contexto do provisionamento do usuário, o SSO é um resultado dos usuários que têm uma única conta para acessar todos os sistemas que usam o provisionamento automático de usuário.

* Sistema de origem-o repositório de usuários do qual o Azure AD provisiona. O Azure AD é o sistema de origem para a maioria dos conectores de provisionamento previamente integrados. No entanto, há algumas exceções para aplicativos de nuvem, como SAP, workday e AWS. Por exemplo, consulte [provisionamento de usuário do WORKDAY para o AD](../saas-apps/workday-inbound-tutorial.md).

* Sistema de destino – o repositório de usuários ao qual o Azure AD provisiona. O sistema de destino é normalmente um aplicativo SaaS, como ServiceNow, Zscaler e margem de atraso. O sistema de destino também pode ser um sistema local, como o AD.

* [Sistema para scim (gerenciamento de identidade entre domínios)](https://aka.ms/scimoverview) – um padrão aberto que permite a automação do provisionamento do usuário. O SCIM comunica dados de identidade do usuário entre provedores de identidade, como a Microsoft, e provedores de serviço como o Salesforce ou outros aplicativos SaaS que exigem informações de identidade do usuário.

### <a name="training-resources"></a>Recursos de treinamento

| Recursos| Link e descrição |
| - | - |
| Webinars sob demanda| [Gerenciar seus aplicativos empresariais com o Azure AD](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Saiba como o Azure AD pode ajudá-lo a obter o SSO para seus aplicativos SaaS corporativos e as práticas recomendadas para controlar o acesso. |
| vídeos| [O que é o provisionamento de usuário no diretório ativo do Azure?](https://youtu.be/_ZjARPpI6NI) <br> [Como implantar o provisionamento de usuário no diretório ativo do Azure?](https://youtu.be/pKzyts6kfrw) <br> [Integrando o Salesforce ao Azure AD: como automatizar o provisionamento de usuários](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Cursos online| SkillUp online:  [Gerenciando identidades](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Saiba como integrar o Azure AD com muitos aplicativos SaaS e proteger o acesso do usuário a esses aplicativos. |
| Manuais| [Autenticação moderna com Azure Active Directory para aplicativos Web (referência do desenvolvedor) 1ª edição](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0).  <br> Este é um guia autoritativo e aprofundado para a criação de soluções de autenticação Active Directory para esses novos ambientes. |
| Tutoriais| Consulte a [lista de tutoriais sobre como integrar aplicativos SaaS com o Azure ad](../saas-apps/tutorial-list.md). |
| Perguntas frequentes| [Perguntas](../app-provisioning/user-provisioning.md) frequentes sobre o provisionamento automatizado de usuários |

### <a name="solution-architectures"></a>Arquiteturas de solução

O serviço de provisionamento do Azure AD provisiona usuários para aplicativos SaaS e outros sistemas conectando-se aos pontos de extremidade da API de gerenciamento de usuários fornecidos por cada fornecedor de aplicativos. Esses pontos de extremidade de API de gerenciamento permitem que o Azure AD crie, atualize e remova usuários por meio de programação.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Provisionamento automático de usuário para empresas híbridas

Neste exemplo, os usuários e grupos são criados em um banco de dados de RH conectado a um diretório local. O serviço de provisionamento do Azure AD gerencia o provisionamento automático de usuário para os aplicativos SaaS de destino.

 ![provisionamento de usuário](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**Descrição do fluxo de trabalho:**

1. Os usuários/grupos são criados em um aplicativo/sistema de RH local, como o SAP. 

1. **Azure ad Connect Agent** executa sincronizações agendadas de identidades (usuários e grupos) do AD local para o Azure AD.

1. O **serviço de provisionamento do Azure ad** inicia um [ciclo inicial](../app-provisioning/user-provisioning.md) no sistema de origem e no sistema de destino. 

1. O **serviço de provisionamento do Azure ad** consulta o sistema de origem para todos os usuários e grupos alterados desde o ciclo inicial e envia por push as alterações em [ciclos incrementais](../app-provisioning/user-provisioning.md).

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Provisionamento automático de usuário para empresas somente em nuvem

Neste exemplo, a criação de usuário ocorre no Azure AD e o serviço de provisionamento do Azure AD gerencia o provisionamento automático de usuário para os aplicativos de destino (SaaS).

![Diagrama que mostra o processo de criação de usuário/grupo de um aplicativo H R local por meio do serviço de provisionamento do Azure a D para os aplicativos de destino a s.](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**Descrição do fluxo de trabalho:**

1. Usuários/grupos são criados no Azure AD.

1. O **serviço de provisionamento do Azure ad** inicia um [ciclo inicial](../app-provisioning/user-provisioning.md) no sistema de origem e no sistema de destino. 

1. O **serviço de provisionamento do Azure ad** consulta o sistema de origem para todos os usuários e grupos atualizados desde o ciclo inicial e executa quaisquer [ciclos incrementais](../app-provisioning/user-provisioning.md).

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>Provisionamento automático de usuário para aplicativos de RH na nuvem 

Neste exemplo, os usuários e ou grupos são criados em um aplicativo de RH de nuvem como o workday e o SuccessFactors. O serviço de provisionamento do Azure AD e o agente de provisionamento do Azure AD Connect provisiona os dados do usuário do locatário do aplicativo de RH na nuvem para o AD. Depois que as contas são atualizadas no AD, elas são sincronizadas com o Azure AD por meio do Azure AD Connect, e os endereços de email e os atributos de nome de usuário podem ser gravados no locatário do aplicativo de RH na nuvem.

![Figura 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  A **equipe de RH** executa as transações no locatário do aplicativo de RH na nuvem.
2.  O **serviço de provisionamento do Azure ad** executa os ciclos agendados do locatário do aplicativo de RH na nuvem e identifica as alterações que precisam ser processadas para sincronização com o AD.
3.  O **serviço de provisionamento do Azure ad** invoca o agente de provisionamento do Azure ad Connect com uma carga de solicitação que contém as operações criar/atualizar/habilitar/desabilitar conta do AD.
4.  **Azure ad Connect agente de provisionamento** usa uma conta de serviço para gerenciar os dados da conta do AD.
5.  **Azure ad Connect** executa a sincronização Delta para efetuar pull de atualizações no AD.
6.  As atualizações do **ad** são sincronizadas com o Azure AD. 
7.  O **serviço de provisionamento do Azure ad** write-backs o atributo de email e o nome de usuário do Azure ad para o locatário do aplicativo de RH na nuvem.

## <a name="plan-the-deployment-project"></a>Planejar o projeto de implantação

Considere suas necessidades organizacionais para determinar a estratégia para implantar o provisionamento de usuários em seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolva os participantes certos

Quando os projetos de tecnologia falham, normalmente é devido a expectativas incompatíveis com o impacto, os resultados e as responsabilidades. Para evitar essas armadilhas, [certifique-se de que você esteja participando dos participantes certos](../fundamentals/active-directory-deployment-plans.md) e que as funções de Stakeholder no projeto sejam bem compreendidas ao documentar os participantes e sua entrada e responsabilidades de projeto.

### <a name="plan-communications"></a>Planejar comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunique de forma proativa a seus usuários como e quando sua experiência será alterada e como obter suporte em caso de problemas.

### <a name="plan-a-pilot"></a>Planejar um piloto

Recomendamos que a configuração inicial do provisionamento de usuário automático esteja em um ambiente de teste com um pequeno subconjunto de usuários antes de dimensioná-lo para todos os usuários em produção. Consulte [práticas recomendadas](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) para executar um piloto.

#### <a name="best-practices-for-a-pilot"></a>Práticas recomendadas para um piloto  

Um piloto permite que você teste com um pequeno grupo antes de implantar um recurso para todos. Certifique-se de que, como parte de seu teste, cada caso de uso em sua organização seja totalmente testado.

Na sua primeira onda, destinada a ti, usabilidade e outros usuários apropriados que podem testar e fornecer comentários. Use esses comentários para desenvolver ainda mais as comunicações e as instruções que você envia para os usuários e para fornecer informações sobre os tipos de problemas que sua equipe de suporte pode ver.

Amplie a distribuição para grupos maiores de usuários aumentando o escopo dos grupos de destino. Isso pode ser feito por meio de [Associação de grupo dinâmico](../enterprise-users/groups-dynamic-membership.md)ou adicionando usuários manualmente aos grupos de destino.

## <a name="plan-application-connections-and-administration"></a>Planejar conexões e administração de aplicativos

Use o portal do AD do Azure para exibir e gerenciar todos os aplicativos que dão suporte ao provisionamento. Consulte [localizando seus aplicativos no portal](../app-provisioning/configure-automatic-user-provisioning-portal.md).

### <a name="determine-the-type-of-connector-to-use"></a>Determinar o tipo de conector a ser usado

As etapas reais necessárias para habilitar e configurar o provisionamento automático variam dependendo do aplicativo. Se o aplicativo que você deseja provisionar automaticamente estiver listado na [Galeria de aplicativos SaaS do Azure ad](../saas-apps/tutorial-list.md), você deverá selecionar o [tutorial de integração específica do aplicativo](../saas-apps/tutorial-list.md) para configurar seu conector de provisionamento de usuário previamente integrado.

Caso contrário, siga as etapas abaixo:

1. [Crie uma solicitação](../develop/v2-howto-app-gallery-listing.md) para um conector de provisionamento de usuário previamente integrado. Nossa equipe trabalhará com você e o desenvolvedor de aplicativos para integrar seu aplicativo à nossa plataforma se ele der suporte a SCIM.

1. Use o suporte de provisionamento de usuário genérico [BYOA scim](../app-provisioning/use-scim-to-provision-users-and-groups.md) para o aplicativo. Esse é um requisito para o Azure AD provisionar usuários para o aplicativo sem um conector de provisionamento previamente integrado.

1. Se o aplicativo for capaz de utilizar o conector BYOA SCIM, consulte o [tutorial de integração do BYOA scim](../app-provisioning/use-scim-to-provision-users-and-groups.md) para configurar o conector BYOA scim para o aplicativo.

Para obter mais informações, consulte [quais aplicativos e sistemas posso usar com o provisionamento automático de usuário do Azure ad?](../app-provisioning/user-provisioning.md)

### <a name="collect-information-to-authorize-application-access"></a>Coletar informações para autorizar o acesso ao aplicativo

A configuração do provisionamento automático de usuário é um processo por aplicativo. Para cada aplicativo, você precisa fornecer [credenciais de administrador](../app-provisioning/configure-automatic-user-provisioning-portal.md) para se conectar ao ponto de extremidade de gerenciamento de usuário do sistema de destino.

A imagem abaixo mostra uma versão das credenciais de administrador necessárias:

![Tela Provisionamento para gerenciar as configurações de provisionamento de conta do usuário](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

Embora alguns aplicativos exijam o nome de usuário e a senha do administrador, outros podem exigir um token de portador.

## <a name="plan-user-and-group-provisioning"></a>Planejar o provisionamento de usuários e grupos

Se você habilitar o provisionamento de usuário para aplicativos empresariais, o [portal do Azure](https://portal.azure.com/) controlará seus valores de atributo por meio do mapeamento de atributos.

### <a name="determine-operations-for-each-saas-app"></a>Determinar as operações para cada aplicativo SaaS

Cada aplicativo pode ter atributos exclusivos de usuário ou grupo que devem ser mapeados para os atributos no Azure AD. O aplicativo pode ter apenas um subconjunto de operações CRUD disponíveis.

Para cada aplicativo, documente as seguintes informações:

* As operações de provisionamento CRUD a serem executadas nos objetos de usuário e grupo para os sistemas de destino. Por exemplo, cada proprietário de negócios de aplicativo SaaS pode não querer todas as operações possíveis.

* Atributos disponíveis no sistema de origem

* Atributos disponíveis no sistema de destino

* Mapeamento de atributos entre sistemas.

### <a name="choose-which-users-and-groups-to-provision"></a>Escolha quais usuários e grupos provisionar

Antes de implementar o provisionamento automático de usuário, você deve determinar os usuários e grupos a serem provisionados para seu aplicativo.

* Use [filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) para definir regras baseadas em atributo que determinam quais usuários são provisionados para um aplicativo.

* Em seguida, use as [atribuições de usuário e grupo](../manage-apps/assign-user-or-group-access-portal.md) conforme necessário para filtragem adicional.

### <a name="define-user-and-group-attribute-mapping"></a>Definir mapeamento de atributo de usuário e grupo

Para implementar o provisionamento automático de usuário, você precisa definir os atributos de usuário e grupo que são necessários para o aplicativo. Há um conjunto pré-configurado de atributos e [mapeamentos de atributo](../app-provisioning/configure-automatic-user-provisioning-portal.md) entre os objetos de usuário do Azure AD e os objetos de usuário de cada aplicativo SaaS. Nem todos os aplicativos SaaS habilitam atributos de grupo.

O Azure AD dá suporte ao mapeamento direto de atributo para atributo, fornecendo valores constantes ou [gravando expressões para mapeamentos de atributo](../app-provisioning/functions-for-customizing-application-data.md). Essa flexibilidade lhe dá um controle fino do que será populado no atributo do sistema de destino. Você pode usar [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) e o explorador do Graph para exportar os mapeamentos e o esquema de atributo de provisionamento de usuário para um arquivo JSON e importá-los de volta para o Azure AD.

Para obter mais informações, consulte [Personalizando o provisionamento de usuário Attribute-Mappings para aplicativos SaaS no Azure Active Directory](../app-provisioning/customize-application-attributes.md).

### <a name="special-considerations-for-user-provisioning"></a>Considerações especiais para provisionamento de usuário

Considere o seguinte para reduzir os problemas após a implantação:

* Verifique se os atributos usados para mapear objetos de usuário/grupo entre os aplicativos de origem e de destino são resilientes. Eles não devem fazer com que os usuários/grupos sejam provisionados incorretamente se os atributos forem alterados (por exemplo, um usuário se mover para uma parte diferente da empresa).

* Os aplicativos podem ter restrições e/ou requisitos específicos que precisam ser atendidos para que o provisionamento de usuário funcione corretamente. Por exemplo, a margem de atraso trunca valores para determinados atributos. Consulte os [tutoriais de provisionamento automático de usuário](../saas-apps/tutorial-list.md) específicos para cada aplicativo.

* Confirme a consistência do esquema entre os sistemas de origem e de destino. Problemas comuns incluem atributos como UPN ou email não correspondentes. Por exemplo, o UPN no Azure AD definido como *john_smith@contoso.com* e no aplicativo é *jsmith@contoso.com* . Para obter mais informações, consulte a [referência de esquema de usuário e grupo](../app-provisioning/use-scim-to-provision-users-and-groups.md).

## <a name="plan-testing-and-security"></a>Teste e segurança de planos

Em cada estágio de sua implantação, verifique se você está testando se os resultados estão conforme o esperado e auditando os ciclos de provisionamento.

### <a name="plan-testing"></a>Teste de plano

Depois de configurar o provisionamento automático de usuário para o aplicativo, você executará casos de teste para verificar se essa solução atende aos requisitos da sua organização.

| Cenários| Resultados esperados |
| - | - |
| O usuário é adicionado a um grupo atribuído ao sistema de destino | O objeto de usuário é provisionado no sistema de destino. <br>O usuário pode entrar no sistema de destino e executar as ações desejadas. |
| O usuário é removido de um grupo atribuído ao sistema de destino | O objeto de usuário é desprovisionado no sistema de destino.<br>O usuário não pode entrar no sistema de destino. |
| As informações do usuário são atualizadas no Azure AD por qualquer método | Os atributos de usuário atualizados são refletidos no sistema de destino após um ciclo incremental |
| O usuário está fora do escopo | O objeto de usuário está desabilitado ou excluído. <br>Observação: esse comportamento é substituído para o [provisionamento do workday](skip-out-of-scope-deletions.md). |

### <a name="plan-security"></a>Segurança do plano

É comum que uma revisão de segurança seja exigida como parte de uma implantação. Se você precisar de uma revisão de segurança, confira muitos [White papers](https://www.microsoft.com/download/details.aspx?id=36391) do Azure AD que fornecem uma visão geral da identidade como um serviço.

### <a name="plan-rollback"></a>Reversão do plano

Se a implementação automática de provisionamento de usuário não funcionar conforme desejado no ambiente de produção, as seguintes etapas de reversão abaixo podem ajudá-lo a reverter para um estado válido anterior conhecido:

1. Examine o [relatório de resumo](../app-provisioning/check-status-user-account-provisioning.md) de provisionamento e [Provisione os logs](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) para determinar quais operações incorretas ocorreram nos usuários e/ou grupos afetados.

1. Use o provisionamento de logs de auditoria para determinar o último estado válido conhecido dos usuários e/ou grupos afetados. Examine também os sistemas de origem (Azure AD ou AD).

1. Trabalhe com o proprietário do aplicativo para atualizar os usuários e/ou grupos afetados diretamente no aplicativo usando os últimos valores de estado bom conhecidos.

## <a name="deploy-automatic-user-provisioning-service"></a>Implantar o serviço de provisionamento automático de usuário

Escolha as etapas que se alinham aos seus requisitos de solução.

### <a name="prepare-for-the-initial-cycle"></a>Preparar para o ciclo inicial

Quando o serviço de provisionamento do Azure AD é executado pela primeira vez, o ciclo inicial em relação ao sistema de origem e aos sistemas de destino cria um instantâneo de todos os objetos de usuário para cada sistema de destino.

Ao habilitar o provisionamento automático para um aplicativo, o ciclo inicial pode levar de 20 minutos a várias horas. A duração depende do tamanho do diretório do Azure AD e do número de usuários no escopo para provisionamento.

O serviço de provisionamento armazena o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho de ciclos incrementais subsequentes.

### <a name="configure-automatic-user-provisioning"></a>Configurar o provisionamento automático de usuário

Use o [portal do Azure](https://portal.azure.com/) para gerenciar o provisionamento e desprovisionamento de conta de usuário automático para aplicativos que dão suporte a ele. Siga as etapas em [como fazer configurar o provisionamento automático para um aplicativo?](../app-provisioning/user-provisioning.md)

O serviço de provisionamento de usuário do Azure AD também pode ser configurado e gerenciado usando-se a [API do Microsoft Graph](/graph/api/resources/synchronization-overview).

## <a name="manage-automatic-user-provisioning"></a>Gerenciar o provisionamento automático de usuário

Agora que você implantou o, precisa gerenciar a solução.

### <a name="monitor-user-provisioning-operation-health"></a>Monitorar a integridade da operação de provisionamento do usuário

Após um [ciclo inicial](../app-provisioning/user-provisioning.md)bem-sucedido, o serviço de provisionamento do Azure ad executará atualizações incrementais indefinidamente, em intervalos específicos de cada aplicativo, até que um dos seguintes eventos ocorra:

* O serviço é interrompido manualmente e um novo ciclo inicial é disparado usando o [portal do Azure](https://portal.azure.com/)ou usando o comando da [API Microsoft Graph](/graph/api/resources/synchronization-overview) apropriado.

* Um novo ciclo inicial é disparado por uma alteração nos mapeamentos de atributo ou em filtros de escopo.

* O processo de provisionamento entra em quarentena devido a uma alta taxa de erros e permanece em quarentena por mais de quatro semanas quando ela será desabilitada automaticamente.

Para examinar esses eventos e todas as outras atividades executadas pelo serviço de provisionamento, consulte [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)do Azure AD.

Para entender quanto tempo os ciclos de provisionamento levam e monitoram o progresso do trabalho de provisionamento, você pode [verificar o status do provisionamento do usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="gain-insights-from-reports"></a>Obter informações de relatórios

O Azure AD pode fornecer [informações adicionais](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) sobre o uso de provisionamento de usuário da sua organização e a integridade operacional por meio de relatórios e logs de auditoria.

Os administradores devem verificar o relatório de Resumo de provisionamento para monitorar a integridade operacional do trabalho de provisionamento. Todas as atividades executadas pelo serviço de provisionamento são registradas nos logs de auditoria do Azure AD. Consulte [tutorial: relatórios sobre o provisionamento automático de conta de usuário](../app-provisioning/check-status-user-account-provisioning.md).

Recomendamos que você assuma a propriedade e consuma esses relatórios em uma cadência que atenda aos requisitos da sua organização. O Azure AD retém a maioria dos dados de auditoria por 30 dias.

### <a name="troubleshoot"></a>Solucionar problemas

Consulte os links a seguir para solucionar quaisquer problemas que possam ser reativados durante o provisionamento:

* [Problema na configuração do provisionamento do usuário para um aplicativo de galeria do Azure AD](../app-provisioning/application-provisioning-config-problem.md)

* [Sincronizar um atributo do seu Active Directory local com o Azure AD para provisionamento para um aplicativo](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [Problema para salvar as credenciais de administrador ao configurar o provisionamento do usuário para um aplicativo de galeria do Azure Active Directory](./user-provisioning.md)

* [Nenhum usuário está sendo provisionado para um aplicativo de galeria do Azure AD](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [O conjunto errado de usuários está sendo provisionado para um aplicativo da Galeria do Azure AD](../manage-apps/add-application-portal-assign-users.md)

### <a name="helpful-documentation"></a>Documentação útil

* [Escrevendo expressões para mapeamentos de atributo](../app-provisioning/functions-for-customizing-application-data.md)

* [Visão geral da API de sincronização do Azure AD](/graph/api/resources/synchronization-overview)

* [Ignorar a exclusão de contas de usuário que saem do escopo](skip-out-of-scope-deletions.md)

* [Agente de provisionamento do Azure AD Connect: histórico de lançamento de versão](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>Recursos

* [Fornecer comentários sobre o produto](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Mantenha-se atualizado sobre as novidades do Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

* [Microsoft Q&um fórum do Azure AD](/answers/topics/azure-active-directory.html)

## <a name="next-steps"></a>Próximas etapas
* [Configurar o provisionamento automático de usuário](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Exportar ou importar sua configuração de provisionamento usando Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md)

* [Gravando expressões para mapeamentos de atributo no Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md)