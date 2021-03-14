---
title: Sistema de Proteção de Dados do Cliente para Microsoft Azure
description: Visão geral técnica do Sistema de Proteção de Dados do Cliente para Microsoft Azure, que fornece controle sobre o acesso do provedor de nuvem quando a Microsoft pode precisar acessar os dados do cliente.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 02/19/2021
ms.openlocfilehash: b39b1d33885de46607cd55acee9919473fab7733
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051422"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Sistema de Proteção de Dados do Cliente para Microsoft Azure

> [!NOTE]
> Para usar esse recurso, sua organização deve ter um [plano de suporte do Azure](https://azure.microsoft.com/support/plans/) com um nível mínimo de **desenvolvedor**.

O Sistema de Proteção de Dados do Cliente para Microsoft Azure fornece uma interface para os clientes revisarem e aprovarem ou rejeitarem solicitações de acesso a dados do cliente. Ele é usado quando um engenheiro da Microsoft precisa acessar os dados do cliente durante uma solicitação de suporte.

Este artigo aborda como habilitar Sistema de Proteção de Dados do Cliente e como as solicitações de lockbox são iniciadas, controladas e armazenadas para revisões e auditorias posteriores.

<a name='supported-services-and-scenarios-in-general-availability'><a name='supported-services-and-scenarios-in-preview'>
## <a name="supported-services-and-scenarios-general-availability"></a>Serviços e cenários com suporte (disponibilidade geral)

Os serviços a seguir agora estão disponíveis para o público geral para Sistema de Proteção de Dados do Cliente:

- Gerenciamento de API do Azure
- Serviço de aplicativo do Azure
- Serviços Cognitivos do Azure
- Registro de Contêiner do Azure
- Banco de Dados do Azure para MySQL
- Azure Databricks
- Azure Data Box
- Azure Data Explorer
- Fábrica de dados do Azure
- Banco de Dados do Azure para PostgreSQL
- Funções do Azure
- Azure HDInsight
- Serviço de Kubernetes do Azure
- Azure Monitor
- Armazenamento do Azure
- Banco de Dados SQL do Azure
- Transferências de assinatura do Azure
- Azure Synapse Analytics
- Máquinas virtuais no Azure (cobrindo acesso à área de trabalho remota, acesso a despejos de memória e discos gerenciados)

## <a name="enable-customer-lockbox"></a>Habilitar Sistema de Proteção de Dados do Cliente

Agora você pode habilitar Sistema de Proteção de Dados do Cliente do [módulo administração](https://aka.ms/customerlockbox/administration) na folha sistema de proteção de dados do cliente.  

> [!NOTE]
> Para habilitar Sistema de Proteção de Dados do Cliente, a conta de usuário precisa ter a [função de administrador global atribuída](../../active-directory/roles/manage-roles-portal.md).

## <a name="workflow"></a>Fluxo de trabalho

As etapas a seguir descrevem um fluxo de trabalho típico para uma solicitação de Sistema de Proteção de Dados do Cliente.

1. Alguém de uma organização tem um problema com sua carga de trabalho do Azure.

2. Depois que essa pessoa solucionar o problema, mas não puder corrigi-lo, ele abrirá um tíquete de suporte da [portal do Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). O tíquete é atribuído a um engenheiro de suporte ao cliente do Azure.

3. Um engenheiro de suporte do Azure revisa a solicitação de serviço e determina as próximas etapas para resolver o problema.

4. Se o engenheiro de suporte não puder solucionar o problema usando ferramentas e telemetria padrão, a próxima etapa será solicitar permissões elevadas usando um serviço de acesso JIT (just-in-time). Essa solicitação pode ser do engenheiro de suporte original ou de um engenheiro diferente, pois o problema é escalonado para a equipe do Azure DevOps.

5. Depois que a solicitação de acesso é enviada pelo engenheiro do Azure, o serviço just-in-time avalia a solicitação levando em consideração os fatores como:
    - O escopo do recurso
    - Se o solicitante é uma identidade isolada ou usando a autenticação multifator
    - Níveis de permissões

    Com base na regra JIT, essa solicitação também pode incluir uma aprovação de aprovadores internos da Microsoft. Por exemplo, o aprovador pode ser o líder de atendimento ao cliente ou o gerente DevOps.

6. Quando a solicitação requer acesso direto aos dados do cliente, uma solicitação de Sistema de Proteção de Dados do Cliente é iniciada. Por exemplo, acesso à área de trabalho remota à máquina virtual de um cliente.

    A solicitação está agora em um estado **notificado do cliente** , aguardando a aprovação do cliente antes de conceder acesso.

7. Na organização do cliente, o usuário que tem a [função proprietário](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) para a assinatura do Azure recebe um email da Microsoft para notificá-los sobre a solicitação de acesso pendente. Para solicitações de Sistema de Proteção de Dados do Cliente, essa pessoa é o aprovador designado.

    Email de exemplo:

    ![Sistema de Proteção de Dados do Cliente do Azure-notificação por email](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. A notificação por email fornece um link para a folha **sistema de proteção de dados do cliente** no módulo administração. Usando esse link, o aprovador designado entra no portal do Azure para exibir as solicitações pendentes que sua organização tem para Sistema de Proteção de Dados do Cliente:

    ![Sistema de Proteção de Dados do Cliente do Azure – página inicial](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   A solicitação permanece na fila de clientes por quatro dias. Após esse período, a solicitação de acesso expira automaticamente e nenhum acesso é concedido aos engenheiros da Microsoft.

9. Para obter os detalhes da solicitação pendente, o aprovador designado pode selecionar a solicitação de lockbox de **solicitações pendentes**:

    ![Sistema de Proteção de Dados do Cliente do Azure – exibir a solicitação pendente](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. O aprovador designado também pode selecionar a **ID de solicitação de serviço** para exibir a solicitação de tíquete de suporte que foi criada pelo usuário original. Essas informações fornecem contexto sobre por que Suporte da Microsoft está envolvido e o histórico do problema relatado. Por exemplo: 

    ![Sistema de Proteção de Dados do Cliente do Azure – exibir a solicitação de tíquete de suporte](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. Depois de revisar a solicitação, o aprovador designado seleciona **aprovar** ou **negar**:

    ![Sistema de Proteção de Dados do Cliente do Azure – selecione aprovar ou negar](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    Como resultado da seleção:
    - **Aprovar**: o acesso é concedido ao engenheiro da Microsoft. O acesso é concedido por um período padrão de oito horas.
    - **Deny**: a solicitação de acesso elevado pelo engenheiro da Microsoft é rejeitada e nenhuma ação adicional é executada.

Para fins de auditoria, as ações executadas nesse fluxo de trabalho são registradas [sistema de proteção de dados do cliente logs de solicitação](#auditing-logs).

## <a name="auditing-logs"></a>Logs de auditoria

Os logs do Sistema de Proteção de Dados do Cliente são armazenados nos logs de atividade. No portal do Azure, selecione **logs de atividade** para exibir informações de auditoria relacionadas a sistema de proteção de dados do cliente solicitações. Você pode filtrar por ações específicas, como:
- **Negar solicitação do sistema de proteção de dados**
- **Criar solicitação do sistema de proteção de dados**
- **Aprovar solicitação do sistema de proteção de dados**
- **Expiração da solicitação de lockbox**

Por exemplo:

![Logs de atividades de Sistema de Proteção de Dados do Cliente do Azure](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="customer-lockbox-integration-with-azure-security-benchmark"></a>Integração do Sistema de Proteção de Dados do Cliente com o Azure Security Benchmark

Apresentamos um novo controle de linha de base ([3,13](../benchmarks/security-control-identity-access-control.md#313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios)) no benchmark de segurança do Azure que cobre sistema de proteção de dados do cliente aplicabilidade. Agora, os clientes podem aproveitar o parâmetro de comparação para examinar Sistema de Proteção de Dados do Cliente aplicabilidade para um serviço.

## <a name="exclusions"></a>Exclusões

Sistema de Proteção de Dados do Cliente solicitações não são disparadas nos seguintes cenários de suporte de engenharia:

- Um engenheiro da Microsoft precisa fazer uma atividade que está fora dos procedimentos operacionais padrão. Por exemplo, para recuperar ou restaurar serviços em cenários inesperados ou imprevisíveis.
- Um engenheiro da Microsoft acessa a plataforma do Azure como parte da solução de problemas e, inadvertidamente, tem acesso aos dados do cliente. Por exemplo, a equipe de rede do Azure executa a solução de problemas que resulta em uma captura de pacote em um dispositivo de rede. Nesse cenário, se o cliente criptografar os dados enquanto estiver em trânsito, o engenheiro não poderá ler os dados.

## <a name="next-steps"></a>Próximas etapas

Sistema de Proteção de Dados do Cliente está disponível para todos os clientes que têm um [plano de suporte do Azure](https://azure.microsoft.com/support/plans/) com um nível mínimo de **desenvolvedor**. Você pode habilitar Sistema de Proteção de Dados do Cliente do [módulo administração](https://aka.ms/customerlockbox/administration) na folha sistema de proteção de dados do cliente.

Sistema de Proteção de Dados do Cliente solicitações são iniciadas por um engenheiro da Microsoft se essa ação for necessária para o andamento de um caso de suporte.
