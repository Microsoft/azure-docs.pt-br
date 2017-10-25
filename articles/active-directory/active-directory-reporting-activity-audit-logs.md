---
title: "Auditoria de relatórios de atividade no portal do Azure Active Directory | Microsoft Docs"
description: "Introdução à auditoria de relatórios de atividade no portal do Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: f2d0332d815c82d7d47625e020de2e9c5099deeb
ms.contentlocale: pt-br
ms.lasthandoff: 07/20/2017

---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Relatórios de atividades de auditoria no portal do Azure Active Directory 

Com os relatórios no Azure Active Directory (Azure AD) você obtém todas as informações de que precisa para determinar como seu ambiente está se comportando.

A arquitetura de relatório no Azure AD consiste nos seguintes componentes:

- **Atividade** 
    - **Atividades de entrada** – informações sobre o uso de aplicativos gerenciados e de atividades de entrada do usuário
    - **Logs de auditoria** – informações de atividade do sistema sobre o gerenciamento de usuários e de grupos, sobre os aplicativos gerenciados e sobre as atividades de diretório.
- **Segurança** 
    - **Entradas arriscadas** - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário. Para obter mais detalhes, veja Entradas de risco.
    - **Usuários sinalizados para riscos** - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida. Para obter mais detalhes, consulte Usuários sinalizados para risco.

Este tópico fornece uma visão geral das atividades de auditoria.
 
## <a name="who-can-access-the-data"></a>Quem pode acessar os dados?
* Usuários na função de Administrador de segurança ou Leitor de segurança
* Administradores globais
* Os usuários individuais (não administradores) podem ver suas próprias atividades


## <a name="audit-logs"></a>Logs de auditoria

Os logs de auditoria no Azure Active Directory fornecem registros de atividades do sistema para fins de conformidade.  
O primeiro ponto de entrada para todos os dados de auditoria é **Logs de auditoria**, na seção **Atividade** do **Azure Active Directory**.

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/61.png "Logs de auditoria")

Um log de auditoria tem um modo de exibição de lista padrão que mostra:

- a data e a hora da ocorrência
- o iniciador/ator (*quem*) de uma atividade 
- a atividade (*o quê*) 
- o destino

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/18.png "Logs de auditoria")

Você pode personalizar o modo de exibição de lista clicando em **Colunas** na barra de ferramentas.

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/19.png "Logs de auditoria")

Isso permite a você exibir campos adicionais ou remover campos que já estão exibidos.

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/21.png "Logs de auditoria")


Ao clicar em um item na exibição de lista, você obterá mais detalhes sobre ele.

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/22.png "Logs de auditoria")


## <a name="filtering-audit-logs"></a>Filtragem de logs de auditoria

Para restringir os dados relatados a um nível que funciona para você, filtre os dados de auditoria usando os seguintes campos:

- Intervalo de datas
- Iniciado por (ator)
- Categoria
- Tipo de recurso de atividade
- Atividade

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/23.png "Logs de auditoria")


O filtro **intervalo de datas** permite definir um período de tempo para os dados retornados.  
Os valores possíveis são:

- 1 mês
- 7 dias
- 24 horas
- Personalizado

Quando você seleciona um período de tempo personalizado, pode configurar uma hora de início e uma hora de término.

O filtro **iniciado por** permite que você defina o nome do ator ou seu nome principal universal (UPN).

O filtro **categoria** permite que você selecione um dos seguintes filtros:

- Todos
- Categoria principal
- Diretório principal
- Gerenciamento de senhas de autoatendimento
- Gerenciamento de grupo de autoatendimento
- Provisionamento de conta - Substituição de senha automática
- Usuários convidados
- Serviço MIM
- Identity Protection
- B2C

O filtro **tipo de recurso de atividade** permite que você selecione um dos seguintes filtros:

- Todos 
- Agrupar
- Diretório
- Usuário
- Aplicativo
- Política
- Dispositivo
- outro

Ao selecionar **Grupo** como **tipo de recurso de atividade**, você obtém uma categoria de filtro adicional que permite que você forneça também uma **Origem**:

- AD do Azure
- O365


O filtro **atividade** se baseia na categoria e na seleção do tipo de recurso de atividade que você faz. Você pode selecionar uma atividade específica que deseja exibir ou escolher todas. 

Você pode obter a lista de todas as Atividades de Auditoria usando a API do Graph https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta, onde $tenantdomain = seu nome de domínio ou consulte o artigo [eventos do relatório de auditoria](active-directory-reporting-audit-events.md).


## <a name="audit-logs-shortcuts"></a>Atalhos de logs de auditoria

Além do **Azure Active Directory**, o portal do Azure fornece dois pontos de entrada adicionais para dados de auditoria:

- Usuários e grupos
- Aplicativos empresariais

### <a name="users-and-groups-audit-logs"></a>Logs de auditoria de usuários e grupos

Com relatórios de auditoria baseados em grupos e usuários, você pode obter respostas a perguntas como:

- Que tipos de atualizações foram aplicadas os usuários?

- Quantos usuários foram alterados?

- Quantas senhas foram alteradas?

- O que um administrador fez em um diretório?

- Quais são os grupos que foram adicionados?

- Existem grupos com alterações de associação?

- Os proprietários do grupo foram alterados?

- Quais licenças foram atribuídas a um grupo ou a um usuário?

Se quiser examinar os dados de auditoria relacionados aos usuários e aos grupos, você poderá encontrar uma exibição filtrada em **Logs de auditoria** na seção **Atividade** de **Usuários e Grupos**. Esse ponto de entrada tem **Usuários e grupos** como **Tipo de Recurso de Atividade** pré-selecionado.

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/93.png "Logs de auditoria")

### <a name="enterprise-applications-audit-logs"></a>Logs de auditoria de aplicativos empresariais

Com relatórios de auditoria baseados em aplicativos, você pode obter respostas a perguntas como:

* Quais aplicativos foram adicionados ou atualizados?
* Quais aplicativos foram removidos?
* Um princípio de serviço para um aplicativo foi alterado?
* Os nomes de aplicativos foram alterados?
* Quem deu permissão a um aplicativo?

Se quiser examinar os dados de auditoria relacionados aos aplicativos, você poderá encontrar uma exibição filtrada em **Logs de auditoria** na seção **Atividade** da folha **Aplicativos empresariais**. Esse ponto de entrada tem **aplicativos empresariais** como **tipo de recurso de atividade** pré-selecionado.

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/134.png "Logs de auditoria")

Você pode filtrar essa exibição para apenas **grupos** ou apenas **usuários**.

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/25.png "Logs de auditoria")


## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral dos relatórios, confira os [Relatórios do Azure Active Directory](active-directory-reporting-azure-portal.md).


