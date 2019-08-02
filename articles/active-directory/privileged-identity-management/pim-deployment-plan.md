---
title: Implantar o Privileged Identity Management (PIM) - Azure Active Directory | Microsoft Docs
description: Descreve como planejar a implantação do Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/08/2019
ms.author: rolyon
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7413fcf7992195753cba86a50b7d53a144b36023
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476434"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Implantar o Azure AD Privileged Identity Management (PIM)

Este guia passo a passo descreve como planejar a implantação do Azure Active Directory (Azure AD) PIM Privileged Identity Management () em sua organização.

> [!TIP]
> Ao longo deste documento, você verá itens marcados como:
> 
> :heavy_check_mark: A **Microsoft recomenda**
> 
> Essas são recomendações gerais e você só deverá implementá-las se elas se aplicarem às suas necessidades corporativas específicas.

## <a name="step-1-learn-about-pim"></a>Etapa 1. Saiba mais sobre o PIM

O Azure AD PIM (Privileged Identity Management) ajuda você a gerenciar funções administrativas com privilégios entre o Azure AD, recursos do Azure e outros Serviços Online da Microsoft. Em um mundo onde identidades com privilégios são atribuídas e esquecidas, o PIM fornece soluções como acesso just-in-time, solicitação de fluxos de trabalho de aprovação e revisões de acesso totalmente integradas para que você possa identificar, descobrir e impedir atividades maliciosas de funções com privilégios em tempo real. Implantar o PIM para gerenciar suas funções com privilégios em toda a organização reduzirá bastante os riscos, ao mesmo tempo em que revela informações valiosas sobre as atividades de suas funções com privilégios.

### <a name="business-value-of-pim"></a>Valor comercial do PIM

**Gerenciar riscos** - proteja sua organização aplicando o princípio do [acesso de privilégios mínimos](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) e do acesso just-in-time. Ao minimizar o número de atribuições permanentes de usuários para funções com privilégios e impor aprovações e MFA para elevação, você poderá reduzir bastante os riscos de segurança relacionados ao acesso com privilégios em sua organização. A aplicação de privilégios mínimos e acesso just-in-time também permitirá que você visualize um histórico de acesso a funções com privilégios e rastreie problemas de segurança à medida que eles ocorrem.

**Governança e conformidade de endereço** - implantar o PIM cria um ambiente para a governança contínua da identidade. A elevação just-in-time de identidades com privilégios fornece uma forma para o PIM acompanhar as atividades de acesso com privilégios em sua organização. Você também poderá exibir e receber notificações de todas as atribuições de funções permanentes e qualificadas dentro de sua organização. Com a revisão de acesso, você pode auditar e remover regularmente identidades com privilégios desnecessárias e garantir que sua organização esteja em conformidade com os mais rigorosos padrões de identidade, acesso e segurança.

**Reduzir custos** - implante o PIM corretamente para reduzir custos eliminando ineficiências, erros humanos e problemas de segurança. O resultado líquido é uma redução de crimes cibernéticos associados a identidades com privilégios que são caras e difíceis de recuperar. O PIM também ajudará sua organização a reduzir os custos associados à auditoria das informações de acesso quando se trata de cumprir os regulamentos e os padrões.

Para obter mais informações, confira [O que é o Privileged Identity Management do Azure AD?](pim-configure.md).

### <a name="licensing-requirements"></a>Requisitos de licenciamento

Para usar o PIM, o diretório precisa ter uma das seguintes licenças pagas ou de avaliação:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Para obter mais informações, veja [Requisitos de licença para usar o PIM](subscription-requirements.md).

### <a name="key-pim-terminology"></a>Terminologia principal do PIM

| Termo ou conceito | DESCRIÇÃO |
| --- | --- |
| qualificado | Uma atribuição de função que requer que um usuário execute uma ou mais ações para usá-la. Se um usuário se qualificou para uma função, isso significa que ele poderá ativá-la quando precisar executar tarefas privilegiadas. Não há nenhuma diferença no modo de acesso concedido a uma pessoa com uma atribuição de função permanente em comparação com uma qualificada. A única diferença é que algumas pessoas não precisam desse acesso o tempo todo. |
| ativar | O processo de execução de uma ou mais ações a fim de usar uma função para a qual um usuário está qualificado. As ações podem incluir a execução de uma verificação de MDA (Autenticação Multifator), fornecimento de uma justificativa comercial ou solicitação de aprovação dos aprovadores designados. |
| acesso JIT (just-in-time) | Um modelo no qual os usuários recebem permissões temporárias para executar tarefas privilegiadas, o que impede que usuários mal-intencionados ou não autorizados obtenham acesso após a expiração das permissões. O acesso é concedido somente quando os usuários precisam dele. |
| princípio de acesso de privilégios mínimos | Uma prática de segurança recomendada na qual todos os usuários recebem apenas os privilégios mínimos necessários para realizar as tarefas que estão autorizados a executar. Essa prática minimiza o número de Administradores Globais usando funções de administrador específicas para determinados cenários. |

Para saber mais, confira a [Terminologia](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-pim-works"></a>Visão geral de alto nível de como o PIM funciona

1. O PIM é configurado para que os usuários se qualifiquem para funções com privilégios.
1. Quando um usuário qualificado precisa usar sua função com privilégios, ele ativa-a no PIM.
1. Dependendo das configurações do PIM definidas para a função, o usuário deve concluir determinadas etapas (como executar a autenticação multifator, obter aprovação ou especificar um motivo).
1. Depois que o usuário ativar sua função com sucesso, ele receberá a função por um período de tempo predefinido.
1. Os administradores podem visualizar um histórico de todas as atividades do PIM no log de auditoria. Eles também podem proteger ainda mais seus locatários e atender aos requisitos de conformidade usando recursos do PIM, como revisões de acesso e alertas.

Para obter mais informações, confira [O que é o Privileged Identity Management do Azure AD?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-pim"></a>Funções que podem ser gerenciadas pelo PIM

**Funções do Azure AD** – essas funções estão todos em Azure Active Directory (como Administrador Global, administrador do Exchange e o administrador de segurança). Você pode ler mais sobre as funções e suas funcionalidades em [Permissões da função de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Para obter ajuda sobre como determinar quais funções devem ser atribuídas aos administradores, confira [funções com menos privilégios por tarefa](../users-groups-roles/roles-delegate-by-task.md).

**Funções de recurso do Azure** - essas funções são vinculadas a um recurso, grupo de recursos, assinatura ou grupo de gerenciamento do Azure. O PIM fornece acesso just-in-time a funções internas, como Proprietário, Administrador de Acesso do Usuário e Colaborador, além de [funções personalizadas](../../role-based-access-control/custom-roles.md). Para saber mais sobre funções de recurso do Azure, confira [Controle de acesso baseado em função (RBAC)](../../role-based-access-control/overview.md).

Para saber mais, confira [Funções que você não pode gerenciar no PIM](pim-roles.md).

## <a name="step-2-plan-your-deployment"></a>Etapa 2. Planejar sua implantação

Esta seção aborda o que você precisa fazer antes de implantar o PIM em sua organização. É fundamental seguir as instruções e entender os conceitos nesta seção, pois eles orientarão você na criação do melhor plano personalizado para as identidades com privilégios de sua organização.

### <a name="identify-your-stakeholders"></a>Identificar os participantes

A seção a seguir ajuda você a identificar todos os participantes envolvidos no projeto e a necessidade de confirmar, revisar ou manter-se informado sobre o projeto. Ela inclui tabelas separadas para implantar o PIM para funções do Azure AD e PIM para funções de recurso do Azure. Adicione os participantes à tabela a seguir, conforme apropriado para sua organização.

- SO = Confirmar este projeto
- R = Revisar este projeto e fornecer comentários
- I = Informado sobre este projeto

#### <a name="stakeholders-pim-for-azure-ad-roles"></a>Participantes: PIM para funções do Azure AD

| NOME | Role | Ação |
| --- | --- | --- |
| Nome e email | **Arquiteto de identidade ou de Administrador Global do Azure**<br/>Um representante da equipe de gerenciamento de identidade encarregado de definir como essa alteração está alinhada com a infraestrutura de gerenciamento de identidade principal em sua organização. | SO/R/I |
| Nome e email | **Proprietário do serviço / gerente de linha**<br/>Um representante dos proprietários de TI de um serviço ou um grupo de serviços. Eles são essenciais na tomada de decisões e ajudam a implantar o PIM para sua equipe. | SO/R/I |
| Nome e email | **Proprietário de segurança**<br/>Um representante da equipe de segurança que pode confirmar que o plano atende aos requisitos de segurança da organização. | SO/R |
| Nome e email | **Suporte técnico / gerente de suporte de TI**<br/>Um representante da organização de suporte de TI que pode fornecer informações sobre a capacidade de suporte dessa mudança a partir da perspectiva da assistência técnica. | R/I |
| Nome e email de usuários piloto | **Usuários de função com privilégios**<br/>O grupo de usuários para o qual o gerenciamento de identidades com privilégios é implementado. Eles precisam saber como ativar suas funções depois que o PIM é implementado. | I |

#### <a name="stakeholders-pim-for-azure-resource-roles"></a>Participantes: Funções de recurso do PIM para Azure

| NOME | Role | Ação |
| --- | --- | --- |
| Nome e email | **Proprietário da assinatura / recurso**<br/>Um representante dos proprietários de TI de cada assinatura ou recurso no qual você deseja implantar o PIM | SO/R/I |
| Nome e email | **Proprietário de segurança**<br/>Um representante da equipe de segurança que pode confirmar que o plano atende aos requisitos de segurança da organização. | SO/R |
| Nome e email | **Suporte técnico / gerente de suporte de TI**<br/>Um representante da organização de suporte de TI que pode fornecer informações sobre a capacidade de suporte dessa mudança a partir da perspectiva da assistência técnica. | R/I |
| Nome e email de usuários piloto | **Usuários da função RBAC**<br/>O grupo de usuários para o qual o gerenciamento de identidades com privilégios é implementado. Eles precisam saber como ativar suas funções depois que o PIM é implementado. | I |

### <a name="enable-pim"></a>Habilita o PIM

Como parte do processo de planejamento, primeiro, você deve consentir e ativar o PIM seguindo nossa [introdução ao uso do documento do PIM](pim-getting-started.md). A ativação do PIM fornece acesso a alguns recursos especificamente desenvolvidos para auxiliar a implantação.

Se seu objetivo é implantar o PIM para recursos do Azure, siga o artigo [Descobrir os recursos do Azure para gerenciar no PIM](pim-resource-roles-discover-resources.md). Somente proprietários de cada recurso, grupo de recursos e assinatura poderão descobri-los dentro do PIM. Se você for um Administrador Global tentar implantar o PIM para recursos do Azure, você pode [elevar o acesso para gerenciar todas as assinaturas do Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) para lhe dar acesso a todos os recursos do Azure no diretório para a descoberta. No entanto, recomendamos que você obtenha aprovação de cada um dos proprietários da assinatura antes de gerenciar seus recursos com o PIM.

### <a name="enforce-principle-of-least-privilege"></a>Aplicar o princípio de privilégios mínimos

É importante ter certeza de que você aplicou o princípio de privilégios mínimos em sua organização para o Azure AD e suas funções de recurso do Azure.

#### <a name="azure-ad-roles"></a>Funções do Azure AD

Para as funções do Azure AD, é comum que as organizações atribuam a função de Administrador Global a um número significativo de administradores, quando a maioria dos administradores precisa apenas de uma ou duas funções de administrador específicas. As atribuições de funções privilegiadas também tendem a ser deixadas sem gerenciamento.

> [!NOTE]
> Problemas comuns da delegação de funções:
>
> - O administrador responsável pelo Exchange recebe a função de Administrador Global, embora precise apenas da função de Administrador do Exchange para executar o trabalho diário.
> - A função de Administrador Global é atribuída a um administrador do Office porque o administrador precisa das funções de administrador de Segurança e do SharePoint e é mais fácil delegar apenas uma função.
> - O administrador recebeu uma função de Administrador de Segurança para executar uma tarefa há muito tempo, mas ela nunca foi removida.

Siga estas etapas para impor o Princípio de privilégios mínimos para suas funções do Azure AD.

1. Entenda a granularidade das funções lendo e compreendendo as [funções de administrador disponíveis do Azure AD](../users-groups-roles/directory-assign-admin-roles.md#available-roles). Você e sua equipe também devem fazer referência às [funções de administrador por tarefa de identidade no Azure AD](../users-groups-roles/roles-delegate-by-task.md), o que explica a função de privilégios mínimos de tarefas específicas.

1. Liste quem tem função com privilégios em sua organização. Você pode usar o [assistente do PIM](pim-security-wizard.md#run-the-wizard) para acessar uma página semelhante à seguinte.

    ![Descubra o painel de funções privilegiadas que mostra quem tem funções com privilégios](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. Para todos os Administradores Globais da organização, descubra por que eles precisam da função. Com base na leitura da documentação anterior, se a tarefa da pessoa puder ser executada por uma ou mais funções granulares de administrador, você deverá removê-la da função de Administrador Global e fazer atribuições de acordo com o Azure Active Directory (como uma referência: Atualmente, a Microsoft tem apenas cerca de 10 administradores com a função de Administrador Global. Saiba mais em [Como a Microsoft usa o PIM](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)).

1. Para todas as outras funções do Azure AD, examine a lista de atribuições, identifique os administradores que já não precisam da função e remova-os de suas atribuições.

Para automatizar as etapas 3 e 4, você pode utilizar a função de revisão de acesso no PIM. Seguindo as etapas em [Iniciar uma revisão de acesso para funções do Azure AD no PIM](pim-how-to-start-security-review.md), você pode configurar uma revisão de acesso para cada função do Azure AD que tenha um ou mais membros.

![Criar um painel de revisão de acesso para funções do Azure AD](./media/pim-deployment-plan/create-access-review.png)

Os revisores deve ser definidos como **Membros (por conta própria)** . Isso enviará um email para todos os membros na função para que eles confirmem se precisam de acesso. Você também deve ativar **Requer motivo sob aprovação** nas configurações avançadas para que os usuários possam indicar por que precisam da função. Com base nessas informações, você poderá remover usuários de funções desnecessárias e delegar funções de administrador mais granulares no caso de Administradores Globais.

As revisões de acesso dependem de emails para notificar as pessoas sobre o acesso às funções. Se você tiver contas que não têm emails vinculados com privilégios, lembre-se de preencher o campo de email secundário nessas contas. Para saber mais, confira [Atributo proxyAddresses no Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Funções de recurso do Azure

Para assinaturas do Azure e recursos, você pode configurar um processo de revisão de acesso semelhante para revisar as funções em cada assinatura ou recurso. O objetivo desse processo é minimizar as atribuições de Administrador de Acesso do Proprietário e Usuário anexadas a cada assinatura ou recurso e, também, remover atribuições desnecessárias. No entanto, as organizações geralmente delegam essas tarefas ao proprietário de cada assinatura ou recurso porque eles têm um melhor entendimento de funções específicas (especialmente funções personalizadas).

Se você for um administrador de TI com a função de Administrador Global tentar implantar o PIM para recursos do Azure em sua organização, você pode [elevar o acesso para gerenciar todas as assinaturas do Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) para obter acesso a cada assinatura. Em seguida, é possível localizar o proprietário de cada assinatura e trabalhar com ele para remover atribuições desnecessárias e minimizar a atribuição de funções do proprietário.

Os usuários com a função de proprietário de uma assinatura do Azure também podem utilizar [revisões de acesso para recursos do Azure](pim-resource-roles-start-access-review.md) para realizar a auditoria e remover atribuições de função desnecessárias semelhantes ao processo descrito anteriormente para as funções do Azure AD.

### <a name="decide-which-role-assignments-should-be-protected-by-pim"></a>Decidir quais atribuições de função devem ser protegidas pelo PIM

Depois de limpar atribuições de função com privilégios em sua organização, você precisará decidir quais funções proteger com o PIM.

Se uma função for protegida pelo PIM, os usuários qualificados atribuídos a ela deverão ser elevados para usar os privilégios concedidos pela função. O processo de elevação também pode incluir a obtenção de aprovação, a execução da autenticação multifator e/ou o motivo pelo qual eles estão sendo ativados. O PIM também pode controlar as elevações por meio de notificações e logs de eventos de auditoria do PIM e o Azure AD.

A escolha das funções que serão protegidas com o PIM pode ser difícil e diferente para cada organização. Esta seção fornece nossos conselhos sobre práticas recomendadas para as funções de recurso do Azure AD e do Azure.

#### <a name="azure-ad-roles"></a>Funções do Azure AD

É importante priorizar a proteção de funções do Azure AD que têm o maior número de permissões. Com base nos padrões de uso de todos os clientes do PIM, as 10 principais funções do Azure AD gerenciadas pelo PIM são:

1. Administrador global
1. Administrador de segurança
1. Administrador de usuários
1. Administrador do Exchange
1. Administrador do SharePoint
1. Administrador do Intune
1. Leitor de segurança
1. Administrador de serviço
1. Administrador de cobrança
1. Administrador do Skype for Business

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que você gerencie todos os Administradores Globais e Administradores de Segurança usando o PIM como o primeiro passo, pois eles são os que podem causar mais danos caso sejam comprometidos.

É importante considerar quais dados e permissões são mais confidenciais para sua organização. Por exemplo, algumas organizações podem querer proteger sua função de Administrador do Power BI ou sua função de Administrador de Equipes usando o PIM, pois elas têm a capacidade de acessar dados e/ou alterar fluxos de trabalho principais.

Se houver funções com usuários convidados atribuídos, eles estarão particularmente vulneráveis a ataques.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que você gerencie todas as funções com usuários convidados que usam o PIM para reduzir o risco associado a contas de usuários convidados comprometidas.

As funções de leitor, como o Leitor de diretório, o Leitor do centro de mensagens e o Leitor de segurança, são, às vezes, consideradas menos importantes em comparação com outras funções, pois não têm permissão de gravação. No entanto, observamos que alguns clientes também protegem essas funções porque os invasores que obtiveram acesso a essas contas podem ler dados confidenciais, como informações de identificação pessoal (PII). Você deve levar isso em consideração ao decidir se as funções do leitor em sua organização precisam ser gerenciadas com o PIM.

#### <a name="azure-resource-roles"></a>Funções de recurso do Azure

Ao decidir quais atribuições de função devem ser gerenciadas usando o recurso do PIM para Azure, você deve primeiro identificar as assinaturas/recursos que são mais vitais para sua organização. Exemplos dessas assinaturas/recursos são:

- Recursos que hospedam os dados mais confidenciais
- Recursos dos quais os principais aplicativos voltados ao cliente dependem

Se você é Administrador Global e tem problemas para decidir quais assinaturas/recursos são mais importantes, entre em contato com os proprietários da assinatura em sua organização para reunir uma lista de recursos gerenciados por cada assinatura. Em seguida, trabalhe com os proprietários da assinatura para agrupar os recursos com base no nível de gravidade, no caso de eles estarem comprometidos (baixo, médio, alto). Você deve priorizar o gerenciamento de recursos com o PIM com base nesse nível de gravidade.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** trabalhar com os proprietários de recursos/assinatura dos serviços essenciais para definir o fluxo de trabalho do PIM para todas as funções dentro de assinaturas/recursos confidenciais.

O PIM para recursos do Azure oferece suporte a contas de serviço com tempo associado. Você deve tratar as contas de serviço exatamente da mesma forma como você trataria uma conta de usuário normal.

Para assinaturas/recursos que não são tão importantes, não será necessário configurar o PIM para todas as funções. No entanto, você ainda deve proteger funções de Administrador de Acesso do Proprietário e Usuário com o PIM.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** gerenciar funções de Administrador de Acesso do Proprietário e Usuário de todas as assinaturas/recursos com o PIM.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Decidir quais atribuições de função devem ser permanentes ou qualificáveis

Depois de ter decidido a lista de funções que serão gerenciadas pelo PIM, você deve decidir quais usuários devem receber a função qualificada versus a função permanentemente ativa. As funções permanentemente ativas são as funções normais atribuídas pelo Azure Active Directory e pelos recursos do Azure, enquanto as funções qualificadas só podem ser atribuídas no PIM.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que você não tenha quaisquer atribuições permanentemente ativas para funções do Azure AD e funções de recurso do Azure diferentes das recomendadas [duas contas de acesso de emergência imediata](../users-groups-roles/directory-emergency-access.md), que devem ter a função de Administrador Global permanente.

Apesar de recomendarmos um administrador permanente, às vezes é difícil para as organizações conseguirem isso imediatamente. Veja alguns pontos a considerar ao tomar esta decisão:

- Frequência de elevação - se o usuário precisar da atribuição com privilégios apenas uma vez, ele não deverá ter a atribuição permanente. Por outro lado, se o usuário precisar da função para o trabalho diário, e o uso do PIM reduzisse bastante a produtividade, ele poderá ser considerado para a função permanente.
- Casos específicos da organização - se a pessoa que recebe a função qualificada pertencer a uma equipe muito distante ou recebê-la de um executivo de alto escalão, a ponto de a comunicação e o cumprimento do processo de elevação serem difíceis, ela poderá ser considerada para a função permanente.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que você configure revisões de acesso recorrentes para usuários com atribuições de função permanentes (caso você tenha alguma). Saiba mais sobre a revisão de acesso recorrente na seção final deste plano de implantação

### <a name="draft-your-pim-settings"></a>Elaborar suas definições de PIM

Antes de implementar sua solução do PIM, convém elaborar suas configurações do PIM para cada função com privilégios utilizada pela organização. Esta seção tem alguns exemplos de configurações do PIM para funções específicas (elas servem apenas como referência e podem ser diferentes para sua organização). Cada uma dessas configurações é explicada em detalhes com as recomendações da Microsoft após as tabelas.

#### <a name="pim-settings-for-azure-ad-roles"></a>Configurações do PIM para funções do Azure AD

| Role | Exigir MFA | Notificação | Tíquete de incidente | Exigir aprovação | Aprovador | Duração da ativação | Administrador permanente |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Administrador global | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Outros administradores globais | 1 hora | Contas de acesso de emergência |
| Administrador do Exchange | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | Nenhum | 2 horas | Nenhum |
| Administrador de assistência técnica | :x: | :x: | :heavy_check_mark: | :x: | Nenhum | 8 horas | Nenhum |

#### <a name="pim-settings-for-azure-resource-roles"></a>Configurações do PIM para funções de recurso do Azure

| Role | Exigir MFA | Notificação | Exigir aprovação | Aprovador | Duração da ativação | Administradores ativos | Expiração ativa | Expiração qualificada |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Proprietário de assinaturas críticas | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Outros proprietários da assinatura | 1 hora | Nenhum | n/d | 3 meses |
| Administrador de Acesso do Usuário de assinaturas menos críticas | :heavy_check_mark: | :heavy_check_mark: | :x: | Nenhum | 1 hora | Nenhum | n/d | 3 meses |
| Colaborador de Máquina Virtual | :x: | :heavy_check_mark: | :x: | Nenhum | 3 horas | Nenhum | n/d | 6 meses |

A tabela a seguir descreve cada configuração.

| Configuração | DESCRIÇÃO |
| --- | --- |
| Role | Nome da função para a qual você está definindo as configurações. |
| Exigir MFA | Se o usuário qualificado precisa executar a MFA antes de ativar a função.<br/><br/> :heavy_check_mark: A **Microsoft recomenda** aplicar o MFA a todas as funções de administrador, especialmente se as funções tiverem usuários convidados. |
| Notificação | Se configurado como true, o Administrador Global, o Administrador de Função com Privilégios e o Administrador de Segurança da organização receberão uma notificação por email quando um usuário qualificado ativar a função.<br/><br/>**Observação:** Algumas organizações não têm um endereço de email vinculado a suas contas de administrador. Para receber essas notificações por email, você deve configurar um endereço de email alternativo para que os administradores recebam esses emails. |
| Tíquete de incidente | Se o usuário qualificado precisa registrar um número de tíquete de incidente ao ativar sua função. Essa configuração ajuda uma organização a identificar cada ativação com um número de incidente interno para atenuar ativações indesejadas.<br/><br/> :heavy_check_mark: A **Microsoft recomenda** aproveitar os números de tíquetes de incidentes para fazer a ligação entre o PIM e seu sistema interno. Isso é particularmente útil para os aprovadores que precisam de contexto para a ativação. |
| Exigir aprovação | Se o usuário qualificado precisa obter aprovação para ativar a função.<br/><br/> :heavy_check_mark: A **Microsoft recomenda** configurar a aprovação para funções com mais permissão. Com base nos padrões de uso de todos os clientes do PIM, o Administrador Global, o Administrador de usuário, o Administrador do Exchange, o Administrador de segurança e o Administrador de senha são as funções mais comuns com a configuração da aprovação. |
| Aprovador | Se a aprovação for necessária para ativar a função qualificada, liste as pessoas que deverão aprovar a solicitação. Por padrão, o PIM define o aprovador como todos os usuários que são administradores de funções com privilégios, sejam eles permanentes ou qualificados.<br/><br/>**Observação:** Se um usuário estiver qualificado para uma função do Azure AD e como aprovador da função, ele não poderá aprovar a si próprio.<br/><br/> :heavy_check_mark: A **Microsoft recomenda** que você escolha os aprovadores como os mais bem informados sobre a função específica e seus usuários frequentes, em vez de um Administrador Global. |
| Duração da ativação | O período de tempo que um usuário será ativado na função antes da expiração. |
| Administrador permanente | Lista de usuários que serão administradores permanentes da função (nunca precisarão ser ativados).<br/><br/> :heavy_check_mark: A **Microsoft recomenda** que você não tenha administradores para todas as funções, exceto para Administradores Globais. Leia mais sobre isso na seção deste plano sobre quem deve ser qualificado e quem deve estar permanentemente ativo. |
| Administradores ativos | Para recursos do Azure, o administrador ativo é a lista de usuários que nunca precisarão ser ativados para usar a função. Isso não é chamado de administrador permanente, como nas funções do Azure AD, porque você pode definir um tempo de expiração para quando o usuário perder essa função. |
| Expiração ativa | Uma atribuição de função ativa para funções de recurso do Azure expira após esse período de tempo definido. Você pode escolher entre 15 dias, 1 mês, 3 meses, 6 meses, 1 ano ou permanentemente ativa. |
| Expiração qualificada | Uma atribuição de função qualificada para funções de recurso do Azure expira após esse período de tempo definido. Você pode escolher entre 15 dias, 1 mês, 3 meses, 6 meses, 1 ano ou permanentemente qualificada. |

## <a name="step-3-implement-your-solution"></a>Etapa 3. Implementar sua solução

A base do planejamento adequado é a base sobre a qual você pode implantar um aplicativo com êxito com o Azure Active Directory.  Ele fornece segurança e integração inteligentes que simplificam a integração, reduzindo o tempo para implantações eficazes.  Essa combinação garante que seu aplicativo seja integrado com facilidade, ao mesmo tempo que reduz o tempo de inatividade dos usuários finais.

### <a name="identify-test-users"></a>Identificar usuários de teste

Use esta seção para identificar um conjunto de usuários e/ou grupos de usuários para validar a implementação. Com base nas configurações escolhidas na seção de planejamento, identifique os usuários que você deseja testar para cada função.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** fazer com que os proprietários de serviços de cada função do Azure AD sejam os usuários de teste para que eles possam se familiarizar com o processo e se tornarem defensores internos da implementação.

Nesta tabela, identifique os usuários de teste que verificarão se as configurações de cada função estão funcionando.

| Nome da função | Usuários de teste |
| --- | --- |
| &lt;Nome da função&gt; | &lt;Usuários para testar a função&gt; |
| &lt;Nome da função&gt; | &lt;Usuários para testar a função&gt; |

### <a name="test-implementation"></a>Implementação de teste

Agora que você identificou os usuários de teste, use esta etapa para configurar o PIM para eles. Se sua organização quiser incorporar o fluxo de trabalho do PIM ao seu próprio aplicativo interno em vez de usar a interface do usuário do PIM no portal do Azure, todas as operações no PIM também terão suporte através da [API de gráficos](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-pim-for-azure-ad-roles"></a>Configurar o PIM para funções do Azure AD

1. [Definir as configurações de função do Azure AD](pim-how-to-change-default-settings.md) com base no que você planejado.

1. Navegue até **Funções do Azure AD**, clique em **Funções** e escolha a função que você acabou de configurar.

1. Para o grupo de usuários de teste, se eles já forem administradores permanentes, você poderá torná-los qualificados pesquisando-os e convertendo-os de permanentes em qualificados clicando nas reticências da linha. Se eles ainda não tiverem as atribuições de função, [crie uma nova atribuição qualificada](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Repita as etapas de 1 a 3 para todas as funções que você deseja testar.

1. Depois de configurar os usuários de teste, você deve enviar o link sobre a [ativação de sua função do Azure AD](pim-how-to-activate-role.md).

#### <a name="configure-pim-for-azure-resource-roles"></a>Configurar o PIM para funções de recurso do Azure

1. [Defina as configurações da função de recurso do Azure](pim-resource-roles-configure-role-settings.md) para uma função dentro de uma assinatura ou recurso que você deseja testar.

1. Navegue até **Recursos do Azure** da assinatura e clique em **Funções**, escolha a função que você acabou de configurar.

1. Para o grupo de usuários de teste, se eles já forem administradores ativos, você poderá torná-los qualificados pesquisando-os e [atualize sua atribuição de função](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Se eles ainda não tiverem a função, você pode [atribuir uma nova função](pim-resource-roles-assign-roles.md#assign-a-role).

1. Repita as etapas de 1 a 3 para todas as funções que você deseja testar.

1. Depois de configurar os usuários de teste, você deve enviar o link sobre a [ativação de sua função de recurso do Azure](pim-resource-roles-activate-your-roles.md).

Você deve usar este estágio para verificar se todas as configurações configuradas para as funções estão funcionando corretamente. Use a tabela a seguir para documentar seus testes. Você também deve usar esse estágio para otimizar a comunicação com os usuários afetados.

| Role | Comportamento esperado durante a ativação | Resultados reais |
| --- | --- | --- |
| Administrador global | (1) Exigir o MFA<br/>(2) Exigir aprovação<br/>(3) O aprovador recebe a notificação e pode aprová-la<br/>(4) A função expira após o horário predefinido |  |
| Proprietário da assinatura *X* | (1) Exigir o MFA<br/>(2) A atribuição qualificada expira após o período de tempo configurado |  |

### <a name="communicate-pim-to-affected-stakeholders"></a>Comunicar o PIM aos participantes afetados

A implantação do PIM apresentará etapas adicionais para usuários de funções com privilégios. Embora o PIM reduza bastante os problemas de segurança associados a identidades com privilégios, a alteração precisa ser efetivamente comunicada antes da implantação em todo o locatário. Dependendo do número de administradores afetados, as organizações geralmente optam por criar um documento interno, um vídeo ou um email sobre a alteração. Frequentemente incluídos nessas comunicações:

- O que é o PIM
- Qual é o benefício para a organização
- Quem será afetado
- Quando o PIM será implementado
- Quais etapas adicionais serão necessárias para que os usuários ativem suas funções
    - Você deve enviar links para a documentação:
    - [Ativar funções do Azure AD](pim-how-to-activate-role.md)
    - [Ativar funções de recurso do Azure](pim-resource-roles-activate-your-roles.md)
- Informações de contato ou link do suporte técnico para quaisquer problemas associados ao PIM

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** configurar o tempo com sua equipe de suporte técnico para orientá-los no fluxo de trabalho do PIM (se sua organização tiver uma equipe interna de suporte de TI). Forneça à equipe as documentações apropriadas e as informações de contato.

### <a name="move-to-production"></a>Mover para ambiente de produção

Quando o teste estiver eficazmente concluído, migre o PIM para a produção, repetindo todas as etapas das fases de teste para todos os usuários de cada função que você definiu na configuração do PIM. Para funções do PIM para Azure AD, as organizações geralmente testam e implantam o PIM para Administradores Globais antes de testar e implantar o PIM para outras funções. Enquanto isso, para o recurso do Azure, as organizações normalmente testam e implementam o PIM em uma assinatura do Azure por vez.

### <a name="in-the-case-a-rollback-is-needed"></a>No caso de uma reversão ser necessária

Se o PIM não funcionar como desejado no ambiente de produção, as etapas de reversão a seguir poderão ajudá-lo a reverter para um estado adequado e conhecido antes da configuração do PIM:

#### <a name="azure-ad-roles"></a>Funções do Azure AD

1. Entre no [Portal do Azure](https://portal.azure.com/).
1. Abra o **Azure AD Privileged Identity Management**.
1. Clique em **Funções do Azure AD** e, em seguida, clique em **Funções**.
1. Para cada função configurada, clique nas reticências ( **...** ) para todos os usuários com uma atribuição qualificada.
1. Clique na opção **Tornar permanente** para tornar a atribuição de função permanente.

#### <a name="azure-resource-roles"></a>Funções de recurso do Azure

1. Entre no [Portal do Azure](https://portal.azure.com/).
1. Abra o **Azure AD Privileged Identity Management**.
1. Clique em **Recursos do Azure** e, em seguida, clique em uma assinatura ou recurso que deseja reverter.
1. Clique em **Funções**.
1. Para cada função configurada, clique nas reticências ( **...** ) para todos os usuários com uma atribuição qualificada.
1. Clique na opção **Tornar permanente** para tornar a atribuição de função permanente.

## <a name="step-4-next-steps-after-deploying-pim"></a>Etapa 4. Próximas etapas após a implantação do PIM

A implantação bem-sucedida do PIM na produção é um avanço significativo em termos de proteção das identidades com privilégios de sua organização. Com a implantação do PIM, há recursos adicionais do PIM que você deve usar para a segurança e conformidade.

### <a name="use-pim-alerts-to-safeguard-your-privileged-access"></a>Usar alertas do PIM para proteger o acesso com privilégios

Você deve utilizar a funcionalidade interna de alerta do PIM para proteger seu locatário da melhor forma. Para saber mais, confira [Alertas de segurança](pim-how-to-configure-security-alerts.md#security-alerts). Esses alertas incluem: administradores não estão usando funções com privilégios, funções estão sendo atribuídas fora do PIM, funções estão sendo ativadas com muita frequência, entre outros. Para proteger totalmente sua organização, confira regularmente sua lista de alertas e corrija os problemas. Você pode exibir e corrigir os alertas da seguinte maneira:

1. Entre no [Portal do Azure](https://portal.azure.com/).
1. Abra o **Azure AD Privileged Identity Management**.
1. Clique em **Funções do Azure AD** e, em seguida, clique em **Alertas**.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** tratar imediatamente de todos os alertas marcados com severidade alta. Para alertas de gravidade média e baixa, você deve se manter informado e fazer alterações se achar que existe uma ameaça à segurança.

Se algum dos alertas específicos não for útil ou não se aplicar à sua organização, você poderá descartar o alerta sempre que desejar na página de alertas. Sempre que desejar, você pode reverter esse descarte posteriormente na página de configurações do Azure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Configurar revisões de acesso recorrentes para auditar regularmente as identidades com privilégios da organização

As revisões de acesso são a melhor maneira de solicitar aos usuários designados com funções com privilégios ou revisores específicos se cada usuário precisa da identidade com privilégios. As revisões de acesso são ótimas se você quiser reduzir a superfície de ataque e manter a conformidade. Para saber mais sobre como iniciar uma revisão de acesso, confira as [revisões de acesso das funções do Azure AD](pim-how-to-start-security-review.md) e as [revisões de acesso de funções de recurso do Azure](pim-resource-roles-start-access-review.md). Para algumas organizações, a revisão periódica do acesso é necessária para manter a conformidade com as leis e regulamentações, enquanto para outras, a revisão de acesso é a melhor maneira de impor o princípio do privilégio mínimo em toda a organização.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** configurar trimestralmente as revisões de acesso para o Azure AD e funções de recurso do Azure.

Na maioria dos casos, o revisor das funções do Azure AD é o próprio usuário, enquanto o revisor das funções de recurso do Azure é o proprietário da assinatura na qual a função está. No entanto, geralmente é o caso em que as empresas têm contas com privilégios que não estão vinculadas ao endereço de email de uma pessoa em particular. Nesses casos, ninguém lê e analisa o acesso.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que você adicione um endereço de email secundário para todas as contas com atribuições de funções com privilégios que não estejam vinculadas a um endereço de email verificado regularmente

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Obter o máximo proveito de seu log de auditoria para aprimorar a segurança e a conformidade

O registro de auditoria é o lugar onde você pode se atualizar e estar em conformidade com os regulamentos. Atualmente, o PIM armazena um histórico de 30 dias de todo o histórico da organização em seu registro de auditoria, incluindo:

- Ativação/desativação de funções qualificadas
- Atividades de atribuição de função dentro e fora do PIM
- Alterações nas configurações de funções
- Solicitar/aprovar/negar atividades para ativação de funções com configuração de aprovação
- Atualizar para alertas

Se você for um Administrador Global ou um administrador de função com privilégios, poderá acessar esses logs de auditoria. Para saber mais, confira o [histórico de auditoria para funções do Azure AD](pim-how-to-use-audit-log.md) e o [histórico de auditoria para funções de recurso do Azure](azure-pim-resource-rbac.md).

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que você tenha pelo menos um administrador para ler todos os eventos de auditoria semanalmente e exportá-los mensalmente.

Se você quiser armazenar automaticamente seus eventos de auditoria por um longo período de tempo, o log de auditoria do PIM será automaticamente sincronizado com os [logs de auditoria do Azure AD](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que você configure o [monitoramento de logs do Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) para arquivar eventos de auditoria em uma conta de armazenamento do Azure para satisfazer as necessidades de segurança e conformidade.
