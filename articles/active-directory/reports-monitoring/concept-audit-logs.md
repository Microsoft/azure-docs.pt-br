---
title: Auditoria de relatórios de atividade no portal do Azure Active Directory | Microsoft Docs
description: Introdução à auditoria de relatórios de atividade no portal do Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/17/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5aa8f675e3fd36fbebfecf42db0f02b0f0f00115
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995974"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Relatórios de atividades de auditoria no portal do Azure Active Directory 

Com os relatórios do Azure AD (Azure Active Directory), é possível obter as informações necessárias para determinar o desempenho do ambiente.



A arquitetura de relatórios consiste nos seguintes componentes:

- **Atividade** 
    - **Entradas** – o [relatório](concept-sign-ins.md) de entradas fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
    - **Logs de auditoria** – fornece rastreamento por meio de logs para todas as alterações feitas por vários recursos no Azure AD. Exemplos de logs de auditoria incluem alterações feitas em quaisquer recursos no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.
    - **Logs**  -  de provisionamento Os [logs de provisionamento](./concept-provisioning-logs.md) permitem que os clientes monitorem a atividade pelo serviço de provisionamento, como a criação de um grupo no ServiceNow ou um usuário importado do workday. 
- **Segurança** 
    - **Entradas arriscadas** -uma [entrada arriscada](../identity-protection/overview-identity-protection.md) é um indicador de uma tentativa de entrada que pode ter sido executada por alguém que não seja o proprietário legítimo de uma conta de usuário. 
    - **Usuários sinalizados para risco** – um [usuário arriscado](../identity-protection/overview-identity-protection.md) é um indicador para uma conta de usuário que pode ter sido comprometida.

Este artigo fornece uma visão geral do relatório de auditoria.
 
## <a name="who-can-access-the-data"></a>Quem pode acessar os dados?

* Usuários nas funções **administrador de segurança**, **leitor de segurança**, leitor de **relatório** , **leitor global** ou **administrador global**

## <a name="audit-logs"></a>Logs de auditoria

Os logs de auditoria do Azure AD fornecem registros das atividades do sistema para fins de conformidade. Para acessar o relatório de auditoria, selecione **logs de auditoria** na seção **monitoramento** de **Azure Active Directory**. 



Um log de auditoria tem um modo de exibição de lista padrão que mostra:

- a data e a hora da ocorrência
- o serviço que registrou a ocorrência
- a categoria e o nome da atividade (*o que*) 
- o status da atividade (êxito ou falha)
- o destino
- o iniciador/ator (quem) de uma atividade

![Logs de auditoria](./media/concept-audit-logs/listview.png "Logs de auditoria")

Você pode personalizar o modo de exibição de lista clicando em **colunas** na barra de ferramentas.

![Colunas de auditoria](./media/concept-audit-logs/columns.png "Colunas de auditoria")

Isso permite a você exibir campos adicionais ou remover campos que já estão exibidos.

![Remover campos](./media/concept-audit-logs/columnselect.png "Remover campos")

Selecione um item na exibição de lista para obter informações mais detalhadas.

![selecionar item](./media/concept-audit-logs/details.png "Selecionar item")


## <a name="filtering-audit-logs"></a>Filtragem de logs de auditoria

É possível filtrar os dados de auditoria nos seguintes campos:

- Serviço
- Categoria
- Atividade
- Status
- Destino
- Iniciado por (ator)
- Intervalo de datas

![Objeto de filtro](./media/concept-audit-logs/filter.png "Objeto Filter")

O filtro de **serviço** permite que você selecione em uma lista suspensa dos seguintes serviços:

- Tudo
- UX de gerenciamento do AAD
- Revisões de acesso
- Provisionamento de conta de usuário
- Proxy do Aplicativo
- Métodos de autenticação
- B2C
- Acesso Condicional
- Diretório principal
- Gerenciamento de direitos
- Autenticação híbrida
- Identity Protection
- Usuários Convidados
- Serviço MIM
- MyApps
- PIM
- Gerenciamento de grupos de autoatendimento
- Gerenciamento de senhas de auto-atendimento
- Termos de Uso

O filtro **categoria** permite que você selecione um dos seguintes filtros:

- Tudo
- AdministrativeUnit
- ApplicationManagement
- Autenticação
- Autorização
- Contact
- Dispositivo
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- KerberosDomain
- KeyManagement
- Rotular
- Outro
- PermissionGrantPolicy
- Política
- ResourceManagement
- RoleManagement
- UserManagement

O filtro de **atividade** é baseado na categoria e na seleção do tipo de recurso de atividade que você faz. Você pode selecionar uma atividade específica que deseja exibir ou escolher todas. 

Você pode obter a lista de todas as atividades de auditoria usando o API do Graph: `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

O filtro de **status** permite filtrar com base no status de uma operação de auditoria. O status pode ser um dos seguintes:

- Tudo
- Sucesso
- Falha

O filtro de **destino** permite que você procure um destino específico pelo início do nome ou UPN (nome principal do usuário). O nome de destino e o UPN diferenciam maiúsculas de minúsculas. 

O filtro **iniciado por** permite que você defina o que o nome de um ator ou um nome de entidade universal (UPN) começa com. O nome e o UPN diferenciam maiúsculas de minúsculas.

O filtro de **intervalo de datas** permite que você defina um período de tempo para os dados retornados.  
Os valores possíveis são:

- 7 dias
- 24 horas
- Personalizado

Quando você seleciona um período de tempo personalizado, pode configurar uma hora de início e uma hora de término.

Você também pode optar por baixar os dados filtrados, até 250.000 registros, selecionando o botão **baixar** . Você pode baixar os logs no formato CSV ou JSON. O número de registros que podem ser baixados é restringido pelas [políticas de retenção de relatórios do Azure Active Directory](reference-reports-data-retention.md).

![Baixar dados](./media/concept-audit-logs/download.png "Baixar dados")

## <a name="audit-logs-shortcuts"></a>Atalhos de logs de auditoria

Além do **Azure Active Directory**, o portal do Azure fornece dois pontos de entrada adicionais para dados de auditoria:

- Usuários e grupos
- Aplicativos empresariais

### <a name="users-and-groups-audit-logs"></a>Logs de auditoria de usuários e grupos

Com relatórios de auditoria baseados em grupos e usuários, você pode obter respostas a perguntas como:

- Quais tipos de atualizações foram aplicadas aos usuários?

- Quantos usuários foram alterados?

- Quantas senhas foram alteradas?

- O que um administrador fez em um diretório?

- Quais são os grupos que foram adicionados?

- Existem grupos com alterações de associação?

- Os proprietários do grupo foram alterados?

- Quais licenças foram atribuídas a um grupo ou a um usuário?

Se você quiser revisar apenas os dados de auditoria relacionados aos usuários, poderá encontrar uma exibição filtrada em **logs de auditoria** na seção **monitoramento** da guia **usuários** . Este ponto de entrada tem **usermanagement** como categoria preselecionada.

![Usuário](./media/concept-audit-logs/users.png "Usuário")

Se você quiser revisar apenas os dados de auditoria relacionados a grupos, poderá encontrar uma exibição filtrada em **logs de auditoria** na seção **monitoramento** da guia **grupos** . Este ponto de entrada tem **GroupManagement** como categoria preselecionada.

![Filtrar grupos](./media/concept-audit-logs/groups.png "Filtrar grupos")

### <a name="enterprise-applications-audit-logs"></a>Logs de auditoria de aplicativos empresariais

Com relatórios de auditoria baseados em aplicativos, você pode obter respostas a perguntas como:

* Quais aplicativos foram adicionados ou atualizados?
* Quais aplicativos foram removidos?
* Uma entidade de serviço para um aplicativo foi alterada?
* Os nomes de aplicativos foram alterados?
* Quem deu permissão a um aplicativo?

Se você quiser revisar os dados de auditoria relacionados aos aplicativos, poderá encontrar uma exibição filtrada em **Logs de auditorias** na seção **Atividade** da folha **Aplicativos empresariais**. Esse ponto de entrada tem **aplicativos empresariais** selecionados como o **tipo de aplicativo**.

![Aplicativos empresariais](./media/concept-audit-logs/enterpriseapplications.png "Aplicativos empresariais")

## <a name="microsoft-365-activity-logs"></a>Logs de atividades Microsoft 365

Você pode exibir Microsoft 365 logs de atividades do [centro de administração do Microsoft 365](/office365/admin/admin-overview/about-the-admin-center). Embora Microsoft 365 atividade e os logs de atividades do Azure AD compartilhem muitos recursos de diretório, somente o centro de administração de Microsoft 365 fornece uma visão completa dos logs de atividades do Microsoft 365. 

Você também pode acessar os logs de atividades de Microsoft 365 programaticamente usando as [APIs de gerenciamento do Office 365](/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Próximas etapas

- [Referência das atividades de auditoria do Azure AD](reference-audit-activities.md)
- [Referência de retenção de relatórios do Azure AD](reference-reports-data-retention.md)
- [Referência de latências de log do Azure AD](reference-reports-latencies.md)