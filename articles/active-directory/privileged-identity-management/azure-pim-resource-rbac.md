---
title: "Visão geral do recurso de RBAC do Azure PIM | Microsoft Docs"
description: "Tenha uma visão geral do recurso de RBAC no PIM incluindo terminologia e notificações"
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: barclayn
ms.openlocfilehash: abaf78428cb12235a659bad5c13d64e8e51f4ba6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="pim-for-azure-resources-preview"></a>PIM recursos do Azure (Visualização)

Com o Azure Active Directory Privileged Identity Management (PIM), você pode gerenciar, controlar e monitorar aos Recursos do Azure (Visualização) em sua organização. Isso inclui assinaturas, grupos de recursos e até mesmo máquinas virtuais. Qualquer recurso dentro do portal do Azure que aproveita a funcionalidade de RBAC do Azure pode tirar proveito de todos as excelentes capacidades de segurança e gerenciamento do ciclo de vida que o Azure AD PIM tem a oferecer e alguns excelentes novos recursos que estamos planejando lançar em breve para as funções do Azure AD. 

## <a name="pim-for-azure-resources-preview-helps-resource-administrators"></a>O PIM para o Azure Resources (Visualização) ajuda os administradores de recursos

- Veja quais usuários e grupos são atribuídos a funções para os recursos do Azure que você administra
- Habilite acesso Just in Time para gerenciar recursos, como assinaturas, grupos de recursos e muito mais
- Cancele o acesso aos recursos por usuários/grupos atribuídos automaticamente com as novas configurações de atribuição com limite de tempo
- Atribua o acesso temporário aos recursos para tarefas rápidas ou escalas de plantão
- Imponha autenticação multifator para acesso a recursos em qualquer função interna ou personalizada 
- Obtenha relatórios sobre a atividade de recursos relacionada ao acesso de recursos durante uma sessão de usuário ativa
- Obtenha alertas quando novos usuários ou grupos recebem o acesso aos recursos e quando eles ativam atribuições qualificadas

O Azure AD PIM pode gerenciar as funções internas do Azure Resource, bem como funções (RBAC) personalizadas, incluindo (mas não limitado a):

- Proprietário
- Administrador de Acesso do Usuário
- Colaborador
- Administrador de Segurança
- Gerenciador de Segurança e muito mais

>[!NOTE]
Os usuários ou membros de um grupo atribuído às funções de Administrador de Acesso de Usuário ou Proprietário e Administradores Globais que permitem o gerenciamento de assinatura no Azure AD são Administradores de Recursos. Esses administradores podem atribuir funções, definir configurações de função e revisar o acesso ao usar o PIM para recursos do Azure. Exibir a lista de [funções internas para recursos do Azure](../role-based-access-built-in-roles.md)

## <a name="tasks"></a>Tarefas

O PIM fornece acesso conveniente para ativar funções, exibir ativações/solicitações pendentes, aprovações pendentes (para [funções de diretório do Azure AD](azure-ad-pim-approval-workflow.md)), e às revisões com resposta pendente na seção Tarefas do menu de navegação esquerdo.
Ao acessar qualquer um dos itens do menu Tarefas no ponto de entrada Visão Geral, a exibição resultante contém os resultados de funções de diretório do Azure AD e de funções dos Recursos do Azure (versão prévia). 

![](media/azure-pim-resource-rbac/role-settings-details.png)

Minhas funções contêm uma lista das suas atribuições de função ativas e qualificadas para funções de diretório do Azure AD, e funções do Azure Resources (Visualização).

## <a name="activate-roles"></a>Ativar funções

A ativação de funções para o Azure Resources (Visualização) apresenta uma nova experiência que permite aos membros qualificados de uma função agendar a ativação para uma data/hora futura e selecionar uma duração de ativação específica dentro do limite máximo (configurado pelos administradores). Saiba mais sobre como [ativar funções do Azure AD aqui](../active-directory-privileged-identity-management-how-to-activate-role.md)

![](media/azure-pim-resource-rbac/contributor.png)

No menu de Ativações, insira a data de início desejada e a hora para ativar a função. Você pode optar por diminuir a duração da ativação (o período de tempo durante o qual a função está ativa) e inserir uma justificativa se necessário; clique em Ativar.

Se a data de início e a hora não forem modificadas, a função será ativada após alguns segundos. Você verá uma mensagem de notificação de função na fila para ativação na página Minhas funções. Clique no botão para atualizar e essa mensagem será eliminada.

![](media/azure-pim-resource-rbac/my-roles.png)

Se a ativação for agendada para uma data e hora futuras, a solicitação pendente será exibida na guia Solicitações pendentes do menu de navegação esquerdo. Caso a ativação de função não seja mais necessária, o usuário pode cancelar a solicitação clicando no botão Cancelar no lado direito da página.

![](media/azure-pim-resource-rbac/pending-requests.png)

## <a name="discover-and-manage-azure-resources"></a>Descobrir e gerenciar os recursos do Azure

Para localizar e gerenciar funções para um recurso do Azure, selecione Azure Resources (Visualização) na guia Gerenciar no menu de navegação esquerdo. Use os filtros ou a barra de pesquisa na parte superior da página para encontrar um recurso.

![](media/azure-pim-resource-rbac/azure-resources.png)

## <a name="resource-dashboards"></a>Painéis de controle de recursos

O painel de Modo de exibição do administrador possui quatro componentes principais. Uma representação gráfica das ativações de função de recurso dos últimos sete dias. Esses dados estão no escopo das ativações de telas e recursos selecionados para as funções mais comuns (Proprietário, Colaborador, Administrador de Acesso do Usuário) e todas as funções combinadas.

No lado direito do gráfico ativações, há dois gráficos que exibem a distribuição de atribuições de função por tipo de atribuição, para os usuários e para os grupos. Selecionar uma fatia do gráfico altera o valor para uma porcentagem (ou vice-versa).

![](media/azure-pim-resource-rbac/admin-view.png)

Abaixo dos gráficos, você pode ver o número de usuários e grupos com novas atribuições de função dos últimos 30 dias (lado esquerdo) e uma lista de funções, classificadas por atribuições totais (decrescente).

![](media/azure-pim-resource-rbac/role-settings.png)

## <a name="manage-role-assignments"></a>Gerenciar atribuições de função

Os administradores podem gerenciar atribuições de função selecionando Funções ou Membros do painel de navegação esquerdo. Selecionar funções permite que os administradores definam o escopo de suas tarefas de gerenciamento para uma função específica, enquanto Membros exibe todas as atribuições de função de usuário e grupo para o recurso.

![](media/azure-pim-resource-rbac/roles.png)

![](media/azure-pim-resource-rbac/members.png)

>[!NOTE]
Se você tiver uma ativação de função pendente, uma faixa de notificação é exibida na parte superior da página, ao exibir a associação.

## <a name="assign-roles"></a>Atribuir funções

Para atribuir um usuário ou grupo a uma função, selecione a função (na exibição de Funções), ou clique em Adicionar na barra de ação (se estiver no modo de exibição de Membros).

![](media/azure-pim-resource-rbac/members2.png)

>[!NOTE]
Se estiver adicionando um usuário ou grupo a partir da guia Membros, você precisará selecionar uma função no menu Adicionar antes de selecionar um usuário ou grupo.

![](media/azure-pim-resource-rbac/select-role.png)

Escolher um usuário ou grupo no diretório.

![](media/azure-pim-resource-rbac/choose.png)

Escolha o tipo de atribuição adequado no menu suspenso. 

**Atribuição Just In Time:** Fornece os membros de usuário ou grupo com acesso qualificado, mas não persistente para a função por um determinado período de tempo ou indefinidamente (se assim definido nas configurações de função). 

**Atribuição direta:** Não requer que os membros de grupo ou usuário ativem a atribuição de função (conhecida como acesso persistente). A Microsoft recomenda usar a atribuição direta para uso de curto prazo, como em turnos de plantão ou atividades dependentes do fator tempo, onde o acesso não será necessário após a tarefa ser concluída.

![](media/azure-pim-resource-rbac/membership-settings.png)

Uma caixa de seleção abaixo da lista suspensa do tipo de atribuição permite especificar se a atribuição deve ser permanente (permanentemente qualificada para ativação Just in Time/permanentemente ativa para Atribuição direta). Para especificar uma duração de atribuição específica, desmarque a caixa de seleção e modifique a data de início e/ou término campos de data e hora.

>[!NOTE]
Pode ser que não seja possível modificar a caixa de seleção se outro administrador tiver especificado a duração máxima de atribuição para cada tipo de atribuição nas configurações de função.

![](media/azure-pim-resource-rbac/calendar.png)

## <a name="view-activation-and-azure-resource-activity"></a>Exibir ativação e atividade de recurso do Azure

No caso de você precisar ver as ações que um usuário específico realizou em vários recursos, você pode analisar a atividade do recurso do Azure associada a um período de ativação específico (para usuários qualificados). Comece selecionando um usuário no modo de exibição de Membros ou na lista de membros em uma função específica. O resultado mostra uma exibição gráfica das ações do usuário no Azure Resources por data e as ativações de função recentes durante esse mesmo período de tempo.

![](media/azure-pim-resource-rbac/user-details.png)

Selecionar a ativação de uma função específica mostrará os detalhes de ativação da função e atividade correspondente do recurso do Azure que ocorreu enquanto o usuário estava ativo.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="modify-existing-assignments"></a>Modificar as atribuições existentes

Para modificar atribuições existentes a partir do modo de exibição de detalhes do usuário/grupo, selecione Alterar configurações na barra de ação na parte superior da página. Altere o tipo de atribuição para Atribuição Just in Time ou Atribuição direta.

![](media/azure-pim-resource-rbac/change-settings.png)

## <a name="review-who-has-access-in-a-subscription"></a>Revisar quem tem acesso em uma assinatura

Para revisar as atribuições de função em sua Assinatura, selecione a guia Membros no painel de navegação esquerdo, ou selecione Funções e escolha uma função específica para revisar os membros. 

Selecione Revisar na barra de ação para exibir as revisões de acesso existentes e selecione Adicionar para criar uma nova revisão.

![](media/azure-pim-resource-rbac/owner.png)

[Saiba mais sobre as revisões de acesso](../active-directory-privileged-identity-management-how-to-perform-security-review.md)

>[!NOTE]
Atualmente as revisões são compatíveis somente com tipos de recurso de Assinatura.

## <a name="configure-role-settings"></a>Definir configurações de função

Definir as configurações de função define os padrões aplicados às atribuições no ambiente de PIM. Para defini-los para o seu recurso, selecione a guia Configurações de função do painel de navegação esquerdo ou no botão de configurações de função na barra de ação em qualquer função para exibir as opções atuais.

Clicar em Editar na barra de ação na parte superior da página permite que você modifique cada configuração.

![](media/azure-pim-resource-rbac/owner.png)

![](media/azure-pim-resource-rbac/owner02.png)

As alterações nas configurações são registradas na página de configurações de função, incluindo a última atualização de data e hora e o administrador que alterou as configurações.

![](media/azure-pim-resource-rbac/role-settings-02.png)

## <a name="resource-audit"></a>Auditoria de recurso

A auditoria de recurso fornece uma exibição de toda a atividades da função para o recurso. Você pode filtrar as informações usando uma data predefinida ou um intervalo personalizado.
![](media/azure-pim-resource-rbac/last-day.png) A auditoria de recursos também fornece acesso rápido à visualização dos detalhes da atividade do usuário. No modo de exibição, todas as Ações para "Ativar função" são links para a atividade de recurso específico do solicitante.
![](media/azure-pim-resource-rbac/resource-audit.png)

## <a name="just-enough-administration"></a>Administração Just Enough

Usar as práticas recomendadas da Administração Just Enough (JEA) nas suas atribuições de função de recurso é simples com o PIM para Azure Resources. Usuários e membros de grupo com atribuições nas Assinaturas ou Grupos de recursos do Azure podem ativar sua atribuição de função existente em escopo reduzido. 

Na página de pesquisa, localize o recurso subordinado que você precisa gerenciar.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Selecione Minhas funções no menu de navegação esquerdo e escolha a função apropriada para ativação. Observe que o tipo de atribuição é Herdada, já que a função foi atribuída para a assinatura, e não para o grupo de recursos, conforme mostrado abaixo.

![](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>Próximas etapas

- [Funções internas para recursos do Azure](../role-based-access-built-in-roles.md)
- Saiba mais sobre como [ativar funções do Azure AD aqui](../active-directory-privileged-identity-management-how-to-activate-role.md)
- [Fluxos de trabalho de aprovação do PIM](azure-ad-pim-approval-workflow.md)