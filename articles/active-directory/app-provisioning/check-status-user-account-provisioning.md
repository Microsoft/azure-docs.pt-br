---
title: Relatar o provisionamento automático de conta de usuário para aplicativos SaaS
description: Saiba como verificar o status dos trabalhos de provisionamento de automático de conta de usuário e como solucionar problemas com o provisionamento de usuários individuais.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/09/2018
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: e6839026c83a10a4acebd7b7a6ab55b210e7ef14
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256670"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Tutorial: Relatórios sobre o provisionamento automático de conta de usuário

O Azure Active Directory (AD do Azure) inclui um [serviço de provisionamento de conta de usuário](user-provisioning.md) que ajuda a automatizar o provisionamento de desprovisionamento de contas de usuário em aplicativos SaaS e outros sistemas, para fins de gerenciamento de ciclo de vida de identidade de ponta a ponta. O Azure AD dá suporte a conectores de provisionamento de usuário pré-integrados para todos os aplicativos e sistemas com tutoriais de provisionamento de usuário [aqui](../saas-apps/tutorial-list.md).

Este artigo descreve como verificar o status de trabalhos de provisionamento depois de eles terem sido configurados e como solucionar problemas com o provisionamento de usuários e grupos individuais.

## <a name="overview"></a>Visão geral

Conectores de provisionamento são configurados e definidos usando o [Portal do Azure](https://portal.azure.com), seguindo a [documentação fornecida](../saas-apps/tutorial-list.md) para o aplicativo suportado. Depois de estarem configurados e em execução, os trabalhos de provisionamento podem ser informados usando um dos dois métodos:

* **Portal do Azure** -este artigo descreve principalmente a recuperação de informações de relatório do [portal do Azure](https://portal.azure.com), que fornece um relatório de Resumo de provisionamento, bem como os logs de auditoria de provisionamento detalhado para um determinado aplicativo.
* **API de auditoria** – o Azure Active Directory também fornece uma API de auditoria que permite a recuperação programática dos logs de auditoria de provisionamento detalhados. Consulte a [referência da API de auditoria do Azure Active Directory](/graph/api/resources/directoryaudit) para a documentação específica para usar essa API. Embora este artigo não cubra especificamente como usar a API, ele detalha os tipos de eventos de provisionamento que são registrados no log de auditoria.

### <a name="definitions"></a>Definições

Este artigo usa os seguintes termos, definidos a seguir:

* **Sistema de origem** – o repositório de usuários do qual o serviço de provisionamento do Azure AD faz a sincronização. O Azure Active Directory é o sistema de origem para a maioria dos conectores de provisionamento previamente integrados, no entanto, há algumas exceções (exemplo: Sincronização de Entrada do Workday).
* **Sistema de destino** – o repositório de usuários ao qual o serviço de provisionamento do Azure AD faz a sincronização. Esse é normalmente um aplicativo SaaS (exemplos: Salesforce, ServiceNow, G Suite, Dropbox for Business), mas, em alguns casos, pode ser um sistema local, como Active Directory (exemplo: sincronização de entrada de WORKDAY para Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Obtendo relatórios de provisionamento do portal do Azure

Para obter informações de relatório de provisionamento para um determinado aplicativo, comece iniciando o [portal do Azure](https://portal.azure.com) e **Azure Active Directory** &gt; os logs de provisionamento de **aplicativos empresariais** &gt; **(versão prévia)** na seção **atividade** . Você também pode navegar até o aplicativo empresarial para o qual o provisionamento está configurado. Por exemplo, se você estiver provisionando usuários para o LinkedIn Elevate, o caminho de navegação para os detalhes do aplicativo será:

**Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos > LinkedIn Elevate**

A partir daqui, você pode acessar a barra de progresso de provisionamento e os logs de provisionamento, descritos abaixo.

## <a name="provisioning-progress-bar"></a>Barra de progresso do provisionamento

A [barra de progresso de provisionamento](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) fica visível na guia **provisionamento** para determinado aplicativo. Ele está localizado na seção **status atual** abaixo de **configurações** e mostra o status do ciclo inicial ou incremental atual. Esta seção também mostra:

* O número total de usuários e grupos que foram sincronizados e estão atualmente no escopo para provisionamento entre o sistema de origem e o sistema de destino.
* A última vez em que a sincronização foi executada. As sincronizações normalmente ocorrem a cada 20-40 minutos, após a conclusão de um [ciclo inicial](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) .
* Se um [ciclo inicial](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) foi concluído ou não.
* Se o processo de provisionamento foi colocado em quarentena ou não e qual o motivo do status de quarentena (por exemplo, falha na comunicação com o sistema de destino devido a credenciais inválidas do administrador).

O **status atual** deve ser o primeiro lugar em que os administradores procuram verificar a integridade operacional do trabalho de provisionamento.

 ![Relatório resumido](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Provisionando logs (versão prévia)

Todas as atividades executadas pelo serviço de provisionamento são registradas nos [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)do Azure AD. Você pode acessar os logs de provisionamento no portal do Azure selecionando **Azure Active Directory** &gt; **aplicativos empresariais** &gt; **logs de provisionamento (versão prévia)** na seção **atividade** . Você pode pesquisar os dados de provisionamento com base no nome do usuário ou no identificador no sistema de origem ou no sistema de destino. Para obter detalhes, consulte [Provisionando logs (versão prévia)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Alguns dos tipos de eventos de atividade incluídos no log são:

## <a name="troubleshooting"></a>Solução de problemas

O relatório de Resumo de provisionamento e os logs de provisionamento desempenham uma função importante que ajuda os administradores a solucionar problemas de provisionamento de várias contas de usuário.

Para obter diretrizes baseada em cenário sobre como solucionar problemas de provisionamento automático de usuário, consulte [Problemas ao configurar e provisionar usuários para um aplicativo](../app-provisioning/application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)