---
title: Implantar Privileged Identity Management (PIM) – Azure AD | Microsoft Docs
description: Descreve como planejar a implantação do Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/27/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b1d18982a4f2a9ee8ba585af56a5e9ded7c1c62
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036819"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Implantar o Azure AD Privileged Identity Management (PIM)

Este artigo é um guia passo a passo que descreve como planejar a implantação do Privileged Identity Management (PIM) em sua organização do Azure Active Directory (AD do Azure). Você reatribuirá usuários em funções com altos privilégios a funções internas ou personalizadas menos poderosas sempre que possível e planejar atribuições de função just-in-time para suas funções mais privilegiadas. Neste artigo, fazemos recomendações para o planejamento e a implementação da implantação.

> [!TIP]
> Ao longo deste artigo, você verá itens marcados como:
>
> : heavy_check_mark: a **Microsoft recomenda**
>
> Essas são recomendações gerais e você deve implementá-las somente quando elas se aplicarem às suas necessidades empresariais específicas.

## <a name="licensing-requirements"></a>Requisitos de licenciamento

Para usar Privileged Identity Management, seu diretório deve ter uma das licenças pagas ou de avaliação a seguir. Para obter mais informações, consulte [requisitos de licença para usar Privileged Identity Management](subscription-requirements.md).

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 Education A5
- Microsoft 365 Enterprise e5

## <a name="how-pim-works"></a>Como o PIM funciona

Esta seção fornece uma análise para fins de planejamento das partes relevantes do processo de Privileged Identity Management. Para obter mais informações, consulte [o que é Azure ad Privileged Identity Management?](pim-configure.md)

1. Comece a usar Privileged Identity Management para que os usuários estejam qualificados para funções privilegiadas.
1. Quando um usuário elegível precisa usar sua função privilegiada, ele ativa a função usando Privileged Identity Management.
1. O usuário pode ser necessário em configurações para:

    - Usar autenticação multifator
    - Solicitar aprovação para ativação
    - Fornecer um motivo comercial para a ativação

1. Depois que o usuário ativar a função com êxito, ele terá as permissões de função para uma duração definida.
1. Os administradores podem exibir um histórico de todas as atividades de Privileged Identity Management no log de auditoria. Eles também podem proteger ainda mais suas organizações do Azure AD e atender à conformidade usando recursos de Privileged Identity Management, como revisões de acesso e alertas.

## <a name="roles-that-can-be-managed-by-pim"></a>Funções que podem ser gerenciadas pelo PIM

As **funções do Azure ad** estão todas em Azure Active Directory (como administrador global, administrador do Exchange e administrador de segurança). Você pode ler mais sobre as funções e suas funcionalidades em [Permissões da função de administrador no Azure Active Directory](../roles/permissions-reference.md). Para obter ajuda sobre como determinar quais funções devem ser atribuídas aos administradores, confira [funções com menos privilégios por tarefa](../roles/delegate-by-task.md).

As **funções do Azure** são funções vinculadas a um recurso do Azure, grupo de recursos, assinatura ou grupo de gerenciamento. Você pode usar o PIM para fornecer acesso just-in-time a funções internas do Azure, como proprietário, administrador de acesso do usuário e colaborador, e também para [funções personalizadas](../../role-based-access-control/custom-roles.md). Para obter mais informações sobre as funções do Azure, consulte [controle de acesso baseado em função do Azure](../../role-based-access-control/overview.md).

Para obter mais informações, consulte [funções que você não pode gerenciar em Privileged Identity Management](pim-roles.md).

## <a name="deployment-plan"></a>Plano de implantação

Antes de implantar Privileged Identity Management em sua organização, siga as instruções e entenda os conceitos nesta seção para ajudá-lo a criar um plano adaptado aos requisitos de identidade privilegiada de sua organização.

### <a name="identify-your-stakeholders"></a>Identificar os participantes

A seção a seguir ajuda a identificar todos os participantes envolvidos no projeto e precisar sair, revisar ou se manter informado. Ele inclui tabelas separadas para implantar o PIM para funções do Azure AD e PIM para funções do Azure. Adicione os participantes à tabela a seguir, conforme apropriado para sua organização.

- SO = Confirmar este projeto
- R = Revisar este projeto e fornecer comentários
- I = Informado sobre este projeto

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Participantes: Privileged Identity Management para funções do Azure AD

| Nome | Função | Ação |
| --- | --- | --- |
| Nome e email | **Arquiteto de identidade ou de Administrador Global do Azure**<br/>Um representante da equipe de gerenciamento de identidades responsável por definir como alinhar essa alteração com a principal infra-estrutura de gerenciamento de identidades em sua organização. | SO/R/I |
| Nome e email | **Proprietário do serviço / gerente de linha**<br/>Um representante dos proprietários de TI de um serviço ou um grupo de serviços. Eles são fundamentais para tomar decisões e ajudar a distribuir Privileged Identity Management para sua equipe. | SO/R/I |
| Nome e email | **Proprietário de segurança**<br/>Um representante da equipe de segurança que pode se desconectar que o plano atende aos requisitos de segurança de sua organização. | SO/R |
| Nome e email | **Suporte técnico / gerente de suporte de TI**<br/>Um representante da organização de suporte de ti que pode fornecer comentários sobre a compatibilidade dessa mudança de uma perspectiva de assistência técnica. | R/I |
| Nome e email de usuários piloto | **Usuários de função com privilégios**<br/>O grupo de usuários para o qual o gerenciamento de identidades com privilégios é implementado. Eles precisarão saber como ativar suas funções quando Privileged Identity Management for implementado. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-roles"></a>Participantes: Privileged Identity Management para funções do Azure

| Nome | Função | Ação |
| --- | --- | --- |
| Nome e email | **Proprietário da assinatura / recurso**<br/>Um representante dos proprietários de ti de cada assinatura ou recurso que você deseja implantar Privileged Identity Management | SO/R/I |
| Nome e email | **Proprietário de segurança**<br/>Um representante da equipe de segurança que pode confirmar que o plano atende aos requisitos de segurança da organização. | SO/R |
| Nome e email | **Suporte técnico / gerente de suporte de TI**<br/>Um representante da organização de suporte de ti que pode fornecer comentários sobre a compatibilidade dessa mudança de uma perspectiva de assistência técnica. | R/I |
| Nome e email de usuários piloto | **Usuários de função do Azure**<br/>O grupo de usuários para o qual o gerenciamento de identidades com privilégios é implementado. Eles precisarão saber como ativar suas funções quando Privileged Identity Management for implementado. | I |

### <a name="start-using-privileged-identity-management"></a>Começar usando o Privileged Identity Management

Como parte do processo de planejamento, você deve preparar Privileged Identity Management seguindo nosso artigo [começar a usar Privileged Identity Management](pim-getting-started.md) . Privileged Identity Management fornece acesso a alguns recursos que foram criados para ajudar na implantação do.

Se seu objetivo é implantar Privileged Identity Management para recursos do Azure, você deve seguir nosso artigo [descobrir recursos do Azure para gerenciar no Privileged Identity Management](pim-resource-roles-discover-resources.md) . Somente os proprietários de assinaturas e grupos de gerenciamento podem trazer esses recursos sob gerenciamento pelo Privileged Identity Management. Depois de estar sob gerenciamento, a funcionalidade PIM está disponível para proprietários em todos os níveis, incluindo grupo de gerenciamento, assinatura, grupo de recursos e recurso. Se você for um administrador global tentando implantar Privileged Identity Management para seus recursos do Azure, você pode [elevar o acesso para gerenciar todas as assinaturas do Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) para conceder acesso a todos os recursos do Azure no diretório para descoberta. No entanto, aconselhamos que você obtenha aprovação de cada um dos seus proprietários de assinatura antes de gerenciar seus recursos com o Privileged Identity Management.

### <a name="enforce-principle-of-least-privilege"></a>Aplicar o princípio de privilégios mínimos

É importante garantir que você tenha imposto sobre o princípio de menos privilégios em sua organização para o Azure AD e suas funções do Azure.

#### <a name="plan-least-privilege-delegation"></a>Planejar delegação de privilégios mínimos

Para funções do Azure AD, é comum que as organizações atribuam a função de administrador global a vários administradores quando a maioria dos administradores precisar apenas de uma ou duas funções de administrador específicas e menos poderosas. Com um grande número de administradores globais ou outras funções de alto privilégio, é difícil acompanhar as atribuições de função privilegiadas de forma bastante intensa.

Siga estas etapas para implementar o princípio de privilégios mínimos para suas funções do Azure AD.

1. Entenda a granularidade das funções lendo e compreendendo as [funções internas do Azure ad](../roles/permissions-reference.md)disponíveis. Você e sua equipe também devem fazer referência às [funções de administrador por tarefa de identidade no Azure AD](../roles/delegate-by-task.md), o que explica a função de privilégios mínimos de tarefas específicas.

1. Liste quem tem função com privilégios em sua organização. Você pode usar a descoberta de Privileged Identity Management [e informações (versão prévia)](pim-security-wizard.md) para reduzir sua exposição.

    ![Página de descoberta e informações (versão prévia) para reduzir a exposição por meio de funções privilegiadas](./media/pim-deployment-plan/new-preview-page.png)

1. Para todos os Administradores Globais da organização, descubra por que eles precisam da função. Em seguida, remova-os da função de administrador global e atribua funções internas ou funções personalizadas com privilégio inferior dentro de Azure Active Directory. INFO, a Microsoft atualmente tem apenas 10 administradores com a função de administrador global. Saiba mais em [como a Microsoft usa Privileged Identity Management](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access).

1. Para todas as outras funções do Azure AD, examine a lista de atribuições, identifique os administradores que já não precisam da função e remova-os de suas atribuições.

Para automatizar as duas últimas etapas, você pode usar as revisões de acesso no Privileged Identity Management. Seguindo as etapas em [iniciar uma revisão de acesso para funções do Azure AD no Privileged Identity Management](pim-how-to-start-security-review.md), você pode configurar uma revisão de acesso para cada função do Azure AD que tenha um ou mais membros.

![Criar um painel de revisão de acesso para funções do Azure AD](./media/pim-deployment-plan/create-access-review.png)

Defina os revisores como **Membros (próprio)**. Todos os usuários na função receberão um email solicitando que eles confirmem que precisam do acesso. Além disso, ative o **motivo necessário na aprovação** nas configurações avançadas para que os usuários devam declarar por que precisam da função. Com base nessas informações, você pode remover usuários de funções desnecessárias ou delegá-las a funções de administrador mais granulares.

As revisões de acesso dependem de emails para notificar as pessoas sobre o acesso às funções. Se você tiver contas que não têm emails vinculados com privilégios, lembre-se de preencher o campo de email secundário nessas contas. Para saber mais, confira [Atributo proxyAddresses no Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="plan-azure-resource-role-delegation"></a>Planejar delegação de função de recurso do Azure

Para assinaturas do Azure e recursos, você pode configurar um processo de revisão de acesso semelhante para revisar as funções em cada assinatura ou recurso. O objetivo desse processo é minimizar as atribuições de administrador de acesso de usuário e proprietário anexadas a cada assinatura ou recurso e remover atribuições desnecessárias. No entanto, as organizações geralmente delegam essas tarefas ao proprietário de cada assinatura ou recurso porque eles têm um melhor entendimento de funções específicas (especialmente funções personalizadas).

Se você estiver na função de administrador global tentando implantar o PIM para funções do Azure em sua organização, poderá [elevar o acesso para gerenciar todas as assinaturas do Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) para obter acesso a cada assinatura. Em seguida, é possível localizar o proprietário de cada assinatura e trabalhar com ele para remover atribuições desnecessárias e minimizar a atribuição de funções do proprietário.

Os usuários com a função de proprietário de uma assinatura do Azure também podem usar [revisões de acesso para recursos do Azure](pim-resource-roles-start-access-review.md) para auditar e remover atribuições de função desnecessárias semelhantes ao processo descrito anteriormente para funções do Azure AD.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Decida quais atribuições de função devem ser protegidas por Privileged Identity Management

Depois de limpar as atribuições de função com privilégios em sua organização, você precisará decidir quais funções proteger com Privileged Identity Management.

Se uma função for protegida por Privileged Identity Management, os usuários qualificados atribuídos a ela deverão elevar para usar os privilégios concedidos pela função. O processo de elevação também pode incluir a obtenção de aprovação, o uso da autenticação multifator e o fornecimento de um motivo pelo qual eles estão sendo ativados. Privileged Identity Management também pode controlar as elevações por meio de notificações e os logs de eventos de auditoria do Privileged Identity Management e do Azure AD.

Escolher quais funções proteger com Privileged Identity Management pode ser difícil e será diferente para cada organização. Esta seção fornece nosso Conselho de práticas recomendadas para Azure AD e funções do Azure.

#### <a name="azure-ad-roles"></a>Funções do Azure AD

É importante priorizar a proteção de funções do Azure AD que têm mais permissões. Com base nos padrões de uso entre todos os clientes Privileged Identity Management, as 10 principais funções do Azure AD gerenciadas pelo Privileged Identity Management são:

1. Administrador global
1. Administrador de segurança
1. Administrador de usuários
1. Administradores do Exchange
1. Administrador do SharePoint
1. Administrador do Intune
1. Leitor de segurança
1. Administrador de serviço
1. Administrador de cobrança
1. Administrador do Skype for Business

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você gerencie todos os seus administradores globais e administradores de segurança usando Privileged Identity Management como uma primeira etapa, pois eles são os usuários que podem fazer mais danos quando comprometidos.

É importante considerar quais dados e permissões são mais confidenciais para sua organização. Por exemplo, algumas organizações podem querer proteger sua função de administrador de Power BI ou sua função de administrador de equipes usando Privileged Identity Management, já que eles podem acessar dados e alterar fluxos de trabalho de núcleo.

Se houver funções com usuários convidados atribuídos, elas ficarão vulneráveis a ataques.

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você gerencie todas as funções com usuários convidados usando Privileged Identity Management para reduzir o risco associado a contas de usuário convidado comprometidas.

As funções de leitor, como o leitor de diretório, o leitor do centro de mensagens e o leitor de segurança, às vezes são consideradas menos importantes do que outras funções porque não têm permissão de gravação. No entanto, temos alguns clientes que também protegem essas funções porque os invasores com acesso a essas contas podem ser capazes de ler dados confidenciais, como dados pessoais. Leve esse risco em consideração ao decidir se deseja que as funções de leitor em sua organização sejam gerenciadas usando Privileged Identity Management.

#### <a name="azure-roles"></a>Funções do Azure

Ao decidir quais atribuições de função devem ser gerenciadas usando Privileged Identity Management para o recurso do Azure, você deve primeiro identificar as assinaturas/recursos que são mais vitais para sua organização. Exemplos dessas assinaturas/recursos são:

- Recursos que hospedam os dados mais confidenciais
- Recursos dos quais os principais aplicativos voltados ao cliente dependem

Se você for um administrador global com problemas para decidir quais assinaturas e recursos são mais importantes, entre em contato com os proprietários da assinatura em sua organização para reunir uma lista de recursos gerenciados por cada assinatura. Em seguida, trabalhe com os proprietários da assinatura para agrupar os recursos com base no nível de severidade, caso eles estejam comprometidos (baixo, médio, alto). Priorize o gerenciamento de recursos com Privileged Identity Management com base nesse nível de severidade.

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você trabalhe com os proprietários de assinatura/recurso dos serviços críticos para configurar Privileged Identity Management fluxo de trabalho para todas as funções dentro de assinaturas/recursos confidenciais.

Privileged Identity Management para recursos do Azure dá suporte a contas de serviço com limite de tempo. Você deve tratar as contas de serviço exatamente da mesma forma como você trataria uma conta de usuário normal.

Para assinaturas/recursos que não são tão críticos, você não precisará configurar Privileged Identity Management para todas as funções. No entanto, você ainda deve proteger as funções proprietário e administrador de acesso do usuário com Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você gerencie funções de proprietário e funções de administrador de acesso de usuário de todas as assinaturas/recursos usando Privileged Identity Management.

### <a name="decide-whether-to-use-a-group-to-assign-roles"></a>Decida se deseja usar um grupo para atribuir funções

A atribuição de uma função a um grupo em vez de a usuários individuais é uma decisão estratégica. Ao planejar, considere atribuir uma função a um grupo para gerenciar atribuições de função quando:

- Muitos usuários são atribuídos a uma função
- Você deseja delegar a atribuição da função

#### <a name="many-users-are-assigned-to-a-role"></a>Muitos usuários são atribuídos a uma função

Manter o controle de quem é atribuído a uma função e gerenciar suas atribuições com base em quando eles precisam de ti pode levar algum tempo quando feito manualmente. Para atribuir um grupo a uma função, primeiro [crie um grupo de funções atribuíveis](../roles/groups-create-eligible.md) e, em seguida, atribua o grupo como qualificado para uma função. Essa ação é sujeita a todos no grupo para o mesmo processo de ativação que os usuários individuais que estão qualificados a elevar a função. Os membros do grupo ativam suas atribuições ao grupo individualmente usando o processo de aprovação e solicitação de ativação Privileged Identity Management. O grupo não está ativado, apenas a associação de grupo do usuário.

#### <a name="you-want-to-delegate-assigning-the-role"></a>Você deseja delegar a atribuição da função

Um proprietário de grupo pode gerenciar a associação de um grupo. Para grupos de funções do Azure AD atribuíveis, somente o administrador de função com privilégios, o administrador global e os proprietários do grupo podem gerenciar a associação de grupo. Ao adicionar novos membros ao grupo, o membro obtém acesso às funções às quais o grupo é atribuído, independentemente de a atribuição estar qualificada ou ativa. Use os proprietários do grupo para delegar o gerenciamento de associação de grupo para uma função atribuída a fim de reduzir a amplitude do privilégio necessário. Para obter mais informações sobre como atribuir um proprietário a um grupo ao criar o grupo, consulte [criar um grupo de funções atribuídas no Azure ad](../roles/groups-create-eligible.md).

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você coloque grupos de funções do Azure ad atribuíveis sob gerenciamento pelo Privileged Identity Management. Depois que um grupo de atribuição de função é trazido sob gerenciamento pelo PIM, ele é chamado de grupo de acesso privilegiado. Use o PIM para exigir que os proprietários do grupo ativem sua atribuição de função de proprietário antes de poderem gerenciar a associação de grupo. Para obter mais informações sobre como colocar grupos sob o gerenciamento do PIM, consulte [colocar grupos de acesso privilegiado (versão prévia) em Privileged Identity Management](groups-discover-groups.md).

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Decidir quais atribuições de função devem ser permanentes ou qualificáveis

Depois de decidir a lista de funções a serem gerenciadas pelo Privileged Identity Management, você deve decidir quais usuários devem obter a função qualificada versus a função ativa permanentemente. Funções ativas permanentemente são as funções normais atribuídas por meio de Azure Active Directory e recursos do Azure, enquanto as funções qualificadas só podem ser atribuídas em Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você tenha zero atribuições permanentemente ativas para funções do Azure AD e funções do Azure além das [duas contas de acesso de emergência de interrupção](../roles/security-emergency-access.md)recomendadas, que devem ter a função de administrador global permanente.

Apesar de recomendarmos um administrador permanente, às vezes é difícil para as organizações conseguirem isso imediatamente. Veja alguns pontos a considerar ao tomar esta decisão:

- Frequência de elevação - se o usuário precisar da atribuição com privilégios apenas uma vez, ele não deverá ter a atribuição permanente. Por outro lado, se o usuário precisar da função de seu trabalho diário e o uso de Privileged Identity Management reduziria muito sua produtividade, eles poderão ser considerados para a função permanente.
- Casos específicos para sua organização – se a pessoa que está sendo dada a função qualificada for de uma equipe distante ou um executivo de alta classificação até o ponto de comunicação e imposição do processo de elevação for difícil, eles poderão ser considerados para a função permanente.

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você configure revisões de acesso recorrente para usuários com atribuições de função permanentes (caso você tenha algum). Saiba mais sobre a revisão de acesso recorrente na seção final deste plano de implantação

### <a name="draft-your-privileged-identity-management-settings"></a>Rascunhar suas configurações de Privileged Identity Management

Antes de implementar sua solução de Privileged Identity Management, é uma prática recomendada rascunhar suas configurações de Privileged Identity Management para cada função privilegiada que sua organização usa. Esta seção tem alguns exemplos de configurações de Privileged Identity Management para funções específicas (elas são apenas para referência e podem ser diferentes para sua organização). Cada uma dessas configurações é explicada em detalhes com as recomendações da Microsoft após as tabelas.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Configurações de Privileged Identity Management para funções do Azure AD

| Função | Exigir MFA | Notificação | Tíquete de incidente | Exigir aprovação | Aprovador | Duração da ativação | Administrador permanente |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Administrador Global | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Outros administradores globais | 1 hora | Contas de acesso de emergência |
| Administrador do Exchange | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | Nenhum | 2 horas | Nenhum |
| Administrador de assistência técnica | :x: | :x: | :heavy_check_mark: | :x: | Nenhum | 8 horas | Nenhum |

#### <a name="privileged-identity-management-settings-for-azure-roles"></a>Configurações de Privileged Identity Management para funções do Azure

| Função | Exigir MFA | Notificação | Exigir aprovação | Aprovador | Duração da ativação | Administradores ativos | Expiração ativa | Expiração qualificada |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Proprietário de assinaturas críticas | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Outros proprietários da assinatura | 1 hora | Nenhum | N/D | 3 meses |
| Administrador de Acesso do Usuário de assinaturas menos críticas | :heavy_check_mark: | :heavy_check_mark: | :x: | Nenhum | 1 hora | Nenhum | N/D | 3 meses |
| Colaborador de Máquina Virtual | :x: | :heavy_check_mark: | :x: | Nenhum | 3 horas | Nenhum | N/D | 6 meses |

A tabela a seguir descreve cada configuração.

| Configuração | Descrição |
| --- | --- |
| Função | Nome da função para a qual você está definindo as configurações. |
| Exigir MFA | Se o usuário qualificado precisa executar a MFA antes de ativar a função.<br/><br/> : heavy_check_mark: a **Microsoft recomenda** que você aplique a MFA para todas as funções de administrador, especialmente se as funções tiverem usuários convidados. |
| Notificação | Se configurado como true, o Administrador Global, o Administrador de Função com Privilégios e o Administrador de Segurança da organização receberão uma notificação por email quando um usuário qualificado ativar a função.<br/><br/>**Observação:** Algumas organizações não têm um endereço de email vinculado a suas contas de administrador, para obter essas notificações por email, você deve definir um endereço de email alternativo para que os administradores recebam esses emails. |
| Tíquete de incidente | Se o usuário qualificado precisa registrar um número de tíquete de incidente ao ativar sua função. Essa configuração ajuda uma organização a identificar cada ativação com um número de incidente interno para atenuar ativações indesejadas.<br/><br/> : heavy_check_mark: a **Microsoft recomenda** aproveitar os números de tíquetes de incidentes para vincular Privileged Identity Management ao seu sistema interno. Esse método pode ser útil para Aprovadores que precisam de contexto para a ativação. |
| Exigir aprovação | Se o usuário qualificado precisa obter aprovação para ativar a função.<br/><br/> : heavy_check_mark: a **Microsoft recomenda** que você configure a aprovação para funções com a maior permissão. Com base nos padrões de uso de todos os Privileged Identity Management clientes, administrador global, administrador de usuário, administrador do Exchange, administrador de segurança e administrador de senha são as funções mais comuns com a configuração de aprovação. |
| Aprovador | Se a aprovação for necessária para ativar a função qualificada, liste as pessoas que deverão aprovar a solicitação. Por padrão, Privileged Identity Management define o aprovador para ser todos os usuários que são um administrador de função com privilégios, sejam eles permanentes ou qualificados.<br/><br/>**Observação:** Se um usuário estiver qualificado para uma função do Azure AD e um Aprovador da função, ele não poderá se aprovar.<br/><br/> : heavy_check_mark: a **Microsoft recomenda** que você escolha aprovadores para serem usuários mais especializados sobre a função e seus usuários frequentes, em vez de um administrador global. |
| Duração da ativação | O período de tempo que um usuário será ativado na função antes da expiração. |
| Administrador permanente | Lista de usuários que serão administradores permanentes da função (nunca precisarão ser ativados).<br/><br/> : heavy_check_mark: a **Microsoft recomenda** que você tenha zero administrador em todas as funções, exceto os administradores globais. Leia mais sobre isso na seção deste plano sobre quem deve ser qualificado e quem deve estar permanentemente ativo. |
| Administradores ativos | Para recursos do Azure, o administrador ativo é a lista de usuários que nunca precisarão ser ativados para usar a função. Esta lista não é conhecida como administrador permanente, como nas funções do Azure AD, pois você pode definir um tempo de expiração para quando o usuário perder essa função. |
| Expiração ativa | As atribuições de função ativas para as funções do Azure expiram após a duração configurada. Você pode escolher entre 15 dias, 1 mês, 3 meses, 6 meses, 1 ano ou permanentemente ativa. |
| Expiração qualificada | As atribuições de função qualificadas para funções do Azure expiram após essa duração. Você pode escolher entre 15 dias, 1 mês, 3 meses, 6 meses, 1 ano ou permanentemente qualificada. |

## <a name="implementation-plan"></a>Plano de implementação

A base do planejamento adequado é a base sobre a qual você pode implantar um aplicativo com êxito com o Azure Active Directory.  Ele fornece segurança e integração inteligentes que simplificam a integração, reduzindo o tempo para implantações eficazes.  Essa combinação garante que seu aplicativo seja integrado com facilidade, ao mesmo tempo que reduz o tempo de inatividade dos usuários finais.

### <a name="identify-test-users"></a>Identificar usuários de teste

Use esta seção para identificar um conjunto de usuários e/ou grupos de usuários para validar a implementação. Com base nas configurações escolhidas na seção de planejamento, identifique os usuários que você deseja testar para cada função.

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você faça com que os proprietários de serviço de cada função do Azure ad sejam os usuários de teste para que possam se familiarizar com o processo e se tornar um defensor interno para a distribuição.

Nesta tabela, identifique os usuários de teste que verificarão se as configurações das funções estão funcionando.

| Nome da função | Usuários de teste |
| --- | --- |
| &lt;Nome da função&gt; | &lt;Usuários para testar a função&gt; |
| &lt;Nome da função&gt; | &lt;Usuários para testar a função&gt; |

### <a name="test-implementation"></a>Implementação de teste

Agora que você identificou os usuários de teste, use esta etapa para configurar Privileged Identity Management para os usuários de teste. Se sua organização quiser incorporar Privileged Identity Management fluxo de trabalho em seu próprio aplicativo interno, em vez de usar Privileged Identity Management na portal do Azure, todas as operações no Privileged Identity Management também têm suporte por meio da nossa [API do Graph](/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Configurar Privileged Identity Management para funções do Azure AD

1. [Defina as configurações de função do Azure ad](pim-how-to-change-default-settings.md) com base no que você planejou.

1. Navegue até **funções do Azure ad**, selecione **funções** e, em seguida, selecione a função configurada.

1. Para o grupo de usuários de teste, se eles já forem um administrador permanente, você poderá torná-los qualificados pesquisando-os e convertendo-os de permanente para qualificado selecionando os três pontos em sua linha. Se eles ainda não tiverem as atribuições de função, [crie uma nova atribuição qualificada](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Repita as etapas de 1 a 3 para todas as funções que você deseja testar.

1. Depois de configurar os usuários de teste, você deve enviar o link sobre a [ativação de sua função do Azure AD](pim-how-to-activate-role.md).

#### <a name="configure-privileged-identity-management-for-azure-roles"></a>Configurar Privileged Identity Management para funções do Azure

1. [Defina as configurações da função de recurso do Azure](pim-resource-roles-configure-role-settings.md) para uma função dentro de uma assinatura ou recurso que você deseja testar.

1. Navegue até **recursos do Azure** para essa assinatura e selecione **funções**, selecione a função que você configurou.

1. Para o grupo de usuários de teste, se eles já forem administradores ativos, você poderá torná-los qualificados pesquisando-os e [atualize sua atribuição de função](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Se eles ainda não tiverem a função, você pode [atribuir uma nova função](pim-resource-roles-assign-roles.md#assign-a-role).

1. Repita as etapas de 1 a 3 para todas as funções que você deseja testar.

1. Depois de configurar os usuários de teste, você deve enviar o link sobre a [ativação de sua função de recurso do Azure](pim-resource-roles-activate-your-roles.md).

Você deve usar este estágio para verificar se todas as configurações configuradas para as funções estão funcionando corretamente. Use a tabela a seguir para documentar seus testes. Você também deve usar esse estágio para otimizar a comunicação com os usuários afetados.

| Função | Comportamento esperado durante a ativação | Resultados reais |
| --- | --- | --- |
| Administrador Global | (1) Exigir o MFA<br/>(2) Exigir aprovação<br/>(3) O aprovador recebe a notificação e pode aprová-la<br/>(4) A função expira após o horário predefinido |  |
| Proprietário da assinatura *X* | (1) Exigir o MFA<br/>(2) A atribuição qualificada expira após o período de tempo configurado |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Comunicar Privileged Identity Management com os participantes afetados

A implantação de Privileged Identity Management apresentará etapas adicionais para usuários de funções com privilégios. Embora Privileged Identity Management reduza significativamente os problemas de segurança associados a identidades com privilégios, a alteração precisa ser efetivamente comunicada antes da implantação em toda a organização. Dependendo do número de administradores afetados, as organizações geralmente optam por criar um documento interno, um vídeo ou um email sobre a alteração. Frequentemente incluídos nessas comunicações:

- O que é o PIM
- Qual é o benefício para a organização
- Quem será afetado
- Quando o PIM será implementado
- Quais etapas adicionais serão necessárias para que os usuários ativem suas funções
    - Você deve enviar links para a documentação:
    - [Ativar funções do Azure AD](pim-how-to-activate-role.md)
    - [Ativar funções do Azure](pim-resource-roles-activate-your-roles.md)
- Informações de contato ou link do suporte técnico para quaisquer problemas associados ao PIM

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você configure o tempo com sua equipe de suporte/assistência técnica para orientá-las no fluxo de trabalho de Privileged Identity Management (se sua organização tiver uma equipe de suporte de ti interna). Forneça à equipe as documentações apropriadas e as informações de contato.

### <a name="move-to-production"></a>Mover para ambiente de produção

Depois que o teste for concluído e bem-sucedido, mova Privileged Identity Management para produção repetindo todas as etapas nas fases de teste para todos os usuários de cada função que você definiu em sua configuração de Privileged Identity Management. Para Privileged Identity Management para funções do Azure AD, as organizações geralmente testam e distribuem Privileged Identity Management para administradores globais antes de testar e distribuir Privileged Identity Management para outras funções. Enquanto isso, para o recurso do Azure, as organizações normalmente testam e distribuem Privileged Identity Management uma assinatura do Azure por vez.

### <a name="if-a-rollback-is-needed"></a>Se uma reversão for necessária

Se Privileged Identity Management não funcionar conforme desejado no ambiente de produção, as etapas de reversão a seguir podem ajudá-lo a reverter para um estado válido conhecido antes de configurar Privileged Identity Management:

#### <a name="azure-ad-roles"></a>Funções do Azure AD

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Abra **Azure ad Privileged Identity Management**.
1. Selecione **funções do Azure ad** e, em seguida, selecione **funções**.
1. Para cada função configurada, selecione as reticências (**...**) para todos os usuários com uma atribuição qualificada.
1. Selecione a opção **tornar permanente** para tornar a atribuição de função permanente.

#### <a name="azure-roles"></a>Funções do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Abra **Azure ad Privileged Identity Management**.
1. Selecione **recursos do Azure** e, em seguida, selecione uma assinatura ou um recurso que você deseja reverter.
1. Selecione **funções**.
1. Para cada função configurada, selecione as reticências (**...**) para todos os usuários com uma atribuição qualificada.
1. Selecione a opção **tornar permanente** para tornar a atribuição de função permanente.

## <a name="next-steps-after-deploying"></a>Próximas etapas após a implantação

A implantação bem-sucedida de Privileged Identity Management em produção é um avanço significativo em termos de proteção das identidades privilegiadas de sua organização. Com a implantação do Privileged Identity Management vem com recursos de Privileged Identity Management adicionais que você deve usar para segurança e conformidade.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Use Privileged Identity Management alertas para proteger seu acesso privilegiado

Para obter mais informações sobre como usar a funcionalidade interna de alerta do Privileged Identity Management para proteger sua organização, consulte [alertas de segurança](pim-how-to-configure-security-alerts.md#security-alerts). Esses alertas incluem: os administradores não estão usando funções privilegiadas, as funções estão sendo atribuídas fora do Privileged Identity Management, as funções estão sendo ativadas com muita frequência e muito mais. Para proteger totalmente sua organização, confira regularmente sua lista de alertas e corrija os problemas. Você pode exibir e corrigir os alertas da seguinte maneira:

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Abra **Azure ad Privileged Identity Management**.
1. Selecione **funções do Azure ad** e, em seguida, selecione **alertas**.

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você lide com todos os alertas marcados com alta gravidade imediatamente. Para alertas de gravidade média e baixa, você deve se manter informado e fazer alterações se achar que existe uma ameaça à segurança.

Se algum dos alertas específicos não for útil ou não se aplicar à sua organização, você poderá descartar o alerta sempre que desejar na página de alertas. Sempre que desejar, você pode reverter esse descarte posteriormente na página de configurações do Azure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Configurar revisões de acesso recorrentes para auditar regularmente as identidades com privilégios da organização

As revisões de acesso são a melhor maneira de solicitar aos usuários designados com funções com privilégios ou revisores específicos se cada usuário precisa da identidade com privilégios. As revisões de acesso são ótimas se você quiser reduzir a superfície de ataque e manter a conformidade. Para obter mais informações sobre como iniciar uma revisão de acesso, consulte [revisões de acesso de funções do Azure ad](pim-how-to-start-security-review.md) e [revisões de acesso de funções do Azure](pim-resource-roles-start-access-review.md). Para algumas organizações, a revisão periódica do acesso é necessária para manter a conformidade com as leis e regulamentações, enquanto para outras, a revisão de acesso é a melhor maneira de impor o princípio do privilégio mínimo em toda a organização.

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você configure as revisões de acesso trimestral para todas as suas funções do Azure AD e do Azure.

Na maioria dos casos, o revisor de funções do Azure AD é o proprietário dos próprios usuários, enquanto o revisor para funções do Azure é a proprietária da assinatura, na qual a função está. No entanto, geralmente é o caso em que as empresas têm contas com privilégios que não estão vinculadas ao endereço de email de uma pessoa em particular. Nesses casos, ninguém lê e analisa o acesso.

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você adicione um endereço de email secundário para todas as contas com atribuições de função com privilégios que não estejam vinculadas a um endereço de email marcado regularmente

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Obter o máximo proveito de seu log de auditoria para aprimorar a segurança e a conformidade

O registro de auditoria é o lugar onde você pode se atualizar e estar em conformidade com os regulamentos. O Privileged Identity Management atualmente armazena um histórico de 30 dias de todo o histórico de sua organização dentro de seu log de auditoria, incluindo:

- Ativação/desativação de funções qualificadas
- Atividades de atribuição de função dentro e fora do Privileged Identity Management
- Alterações nas configurações de funções
- Solicitar/aprovar/negar atividades para ativação de funções com configuração de aprovação
- Atualizar para alertas

Você pode acessar os logs de auditoria se for um administrador global ou um administrador de função com privilégios. Para obter mais informações, consulte [histórico de auditoria para funções do Azure ad](pim-how-to-use-audit-log.md) e [histórico de auditoria para funções do Azure](azure-pim-resource-rbac.md).

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que pelo menos um administrador Leia todos os eventos de auditoria semanalmente e exporte seus eventos de auditoria mensalmente.

Se você quiser armazenar automaticamente seus eventos de auditoria por um período de tempo maior, Privileged Identity Management log de auditoria será sincronizado automaticamente nos [logs de auditoria do Azure ad](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você configure o [monitoramento de log do Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) para arquivar eventos de auditoria em uma conta de armazenamento do Azure para maior segurança e conformidade.
