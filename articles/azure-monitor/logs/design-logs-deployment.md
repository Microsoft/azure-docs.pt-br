---
title: Criando sua implantação de logs de Azure Monitor | Microsoft Docs
description: Este artigo descreve as considerações e recomendações para os clientes se preparando para implantar um espaço de trabalho no Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/20/2019
ms.openlocfilehash: a889275782388781eadffb7cf0a24771bf6e9e4f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030827"
---
# <a name="designing-your-azure-monitor-logs-deployment"></a>Projetando a implantação de logs do Azure Monitor

O Azure Monitor armazena dados de [log](data-platform-logs.md) em um espaço de trabalho log Analytics, que é um recurso do Azure e um contêiner em que os dados são coletados, agregados e servem como um limite administrativo. Embora seja possível implantar um ou mais espaços de trabalho em sua assinatura do Azure, há várias considerações que você deve entender para garantir que a implantação inicial esteja seguindo nossas diretrizes para fornecer uma implantação econômica, gerenciável e escalonável para atender às necessidades da sua organização.

Os dados em um espaço de trabalho são organizados em tabelas, cada um dos quais armazena diferentes tipos de dados e tem seu próprio conjunto exclusivo de propriedades com base no recurso que gera os dados. A maioria das fontes de dados será gravada em suas próprias tabelas em um espaço de trabalho Log Analytics.

![Exemplo de modelo de dados de espaço de trabalho](./media/design-logs-deployment/logs-data-model-01.png)

Um espaço de trabalho do Log Analytics fornece:

* Uma localização geográfica para armazenamento de dados.
* Isolamento de dados concedendo direitos de acesso de usuários diferentes após uma das nossas estratégias de design recomendadas.
* Escopo para configuração de configurações, como [tipo de preço](./manage-cost-storage.md#changing-pricing-tier), [retenção](./manage-cost-storage.md#change-the-data-retention-period)e limitação de [dados](./manage-cost-storage.md#manage-your-maximum-daily-data-volume).

Os espaços de trabalho são hospedados em clusters físicos. Por padrão, o sistema está criando e gerenciando esses clusters. Os clientes que ingerirem mais de 4 TB/dia devem criar seus próprios clusters dedicados para seus espaços de trabalho – ele permite um controle melhor e uma taxa de ingestão mais alta.

Este artigo fornece uma visão geral detalhada das considerações de design e migração, visão geral do controle de acesso e uma compreensão das implementações de design recomendadas para sua organização de ti.



## <a name="important-considerations-for-an-access-control-strategy"></a>Considerações importantes para uma estratégia de controle de acesso

Identificar o número de espaços de trabalho de que você precisa é influenciado por um ou mais dos seguintes requisitos:

* Você é uma empresa global e precisa de dados de log armazenados em regiões específicas por motivos de soberania de dados ou conformidade.
* Você usa o Azure e deseja evitar encargos de transferência de dados de saída com um workspace na mesma região que os recursos do Azure que ele gerencia.
* Você gerencia vários departamentos ou grupos de negócios e deseja que cada um deles veja seus próprios dados, mas não dados de outros. Além disso, não há nenhum requisito comercial para uma exibição consolidada entre departamentos ou de grupos de negócios.

Hoje, as organizações de ti são modeladas seguindo um híbrido centralizado, descentralizado ou um entre as duas estruturas. Como resultado, os seguintes modelos de implantação de espaço de trabalho foram comumente usados para mapear para uma dessas estruturas organizacionais:

* **Centralizado**: todos os logs são armazenados em um espaço de trabalho central e administrados por uma única equipe, com Azure monitor fornecendo acesso diferenciado por equipe. Nesse cenário, é fácil de gerenciar, Pesquisar entre recursos e correlacionar logs de forma cruzada. O espaço de trabalho pode crescer significativamente dependendo da quantidade de dados coletados de vários recursos em sua assinatura, com sobrecarga administrativa adicional para manter o controle de acesso a diferentes usuários. Esse modelo é conhecido como "Hub e spoke".
* **Descentralizado**: cada equipe tem seu próprio espaço de trabalho criado em um grupo de recursos que ele possui e gerencia, e os dados de log são separados por recurso. Nesse cenário, o espaço de trabalho pode ser mantido seguro e o controle de acesso é consistente com o acesso aos recursos, mas é difícil correlacionar os logs. Os usuários que precisam de uma visão ampla de muitos recursos não podem analisar os dados de uma maneira significativa.
* **Híbrido**: os requisitos de conformidade de auditoria de segurança complicam ainda mais esse cenário porque muitas organizações implementam ambos os modelos de implantação em paralelo. Isso normalmente resulta em uma configuração complexa, cara e difícil de manter com lacunas na cobertura de logs.

Ao usar os Agentes do Log Analytics para coletar dados, você precisará entender o seguinte para planejar a implantação do agente:

* Para coletar dados de agentes do Windows, você poderá [configurar cada agente para relatar a um ou mais espaços de trabalho](./../agents/agent-windows.md), mesmo que estejam relatando a um grupo de gerenciamento do System Center Operations Manager. O agente do Windows pode relatar a até quatro espaços de trabalho.
* Não há suporte à hospedagem múltipla em um agente do Linux e ele somente pode relatar a um único espaço de trabalho.

Se você estiver usando o System Center Operations Manager 2012 R2 ou posterior:

* cada grupo de gerenciamento do Operations Manager pode ser [conectado a apenas um espaço de trabalho](../agents/om-agents.md). 
* Os computadores Linux que relatam a um grupo de gerenciamento devem ser configurados para relatar diretamente a um workspace do Log Analytics. Se os computadores Linux já estiverem se reportando diretamente a um espaço de trabalho e você quiser monitorá-los com o Operations Manager, siga estas etapas para [Relatar a um grupo de gerenciamento do Operations Manager](../agents/agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group). 
* Você pode instalar o Agente do Windows do Log Analytics no computador Windows e fazer com que ele se reporte tanto ao Operations Manager integrado a um espaço de trabalho quanto a outro espaço de trabalho.

## <a name="access-control-overview"></a>Visão geral do controle de acesso

Com o Azure RBAC (controle de acesso baseado em função), você pode conceder aos usuários e grupos apenas a quantidade de acesso de que eles precisam para trabalhar com dados de monitoramento em um espaço de trabalho. Isso permite que você se alinhe com o modelo operacional da organização de ti usando um único espaço de trabalho para armazenar os dados coletados habilitados em todos os seus recursos. Por exemplo, você concede acesso à sua equipe responsável pelos serviços de infraestrutura hospedados em VMs (máquinas virtuais) do Azure e, como resultado, eles terão acesso apenas aos logs gerados pelas VMs. Isso está seguindo nosso novo modelo de log de contexto de recurso. A base desse modelo é para cada registro de log emitido por um recurso do Azure, ele é automaticamente associado a esse recurso. Os logs são encaminhados para um espaço de trabalho central que respeita o escopo e o RBAC do Azure com base nos recursos.

Os dados aos quais um usuário tem acesso são determinados por uma combinação de fatores listados na tabela a seguir. Cada um é descrito nas seções a seguir.

| Fator | Descrição |
|:---|:---|
| [Modo de acesso](#access-mode) | Método usado pelo usuário para acessar o espaço de trabalho.  Define o escopo dos dados disponíveis e o modo de controle de acesso que é aplicado. |
| [Modo de controle de acesso](#access-control-mode) | Configuração no espaço de trabalho que define se as permissões são aplicadas no nível de espaço de trabalho ou de recurso. |
| [Permissões](./manage-access.md) | Permissões aplicadas a indivíduo ou grupos de usuários para o espaço de trabalho ou recurso. Define a quais dados o usuário terá acesso. |
| [RBAC do Azure no nível de tabela](./manage-access.md#table-level-azure-rbac) | Permissões granulares opcionais que se aplicam a todos os usuários, independentemente do modo de acesso ou do modo de controle de acesso. Define quais tipos de dados um usuário pode acessar. |

## <a name="access-mode"></a>Modo de acesso

O *modo de acesso* refere-se a como um usuário acessa um espaço de trabalho log Analytics e define o escopo dos dados que eles podem acessar. 

Os usuários têm duas opções para acessar os dados:

* **Espaço de trabalho-contexto**: você pode exibir todos os logs no espaço de trabalho para o qual você tem permissão. As consultas nesse modo têm o escopo definido para todos os dados em todas as tabelas no espaço de trabalho. Esse é o modo de acesso usado quando os logs são acessados com o espaço de trabalho como o escopo, como quando você seleciona **logs** no menu **Azure monitor** na portal do Azure.

    ![Log Analytics contexto do espaço de trabalho](./media/design-logs-deployment/query-from-workspace.png)

* **Recurso-contexto**: quando você acessa o espaço de trabalho para um recurso específico, grupo de recursos ou assinatura, como quando você seleciona **logs** de um menu de recursos no portal do Azure, você pode exibir logs somente para recursos em todas as tabelas às quais você tem acesso. As consultas neste modo têm o escopo para apenas os dados associados a esse recurso. Esse modo também habilita o RBAC do Azure granular.

    ![Log Analytics contexto do recurso](./media/design-logs-deployment/query-from-resource.png)

    > [!NOTE]
    > Os logs estarão disponíveis para consultas de contexto de recurso somente se estiverem corretamente associados ao recurso relevante. Atualmente, os seguintes recursos têm limitações:
    > - Computadores fora do Azure-com suporte para o contexto de recurso somente por meio do [Azure ARC para servidores](../../azure-arc/servers/index.yml)
    > - Service Fabric
    > - Application Insights-com suporte para o contexto de recurso somente ao usar o [recurso de Application insights baseado em espaço de trabalho](../app/create-workspace-resource.md)
    >
    > Você pode testar se os logs estão corretamente associados ao recurso executando uma consulta e inspecionando os registros nos quais você está interessado. Se a ID de recurso correta estiver na propriedade [_ResourceId](./log-standard-columns.md#_resourceid) , os dados estarão disponíveis para consultas centradas em recursos.

Azure Monitor determina automaticamente o modo correto dependendo do contexto do qual você executa a pesquisa de log. O escopo é sempre apresentado na seção superior esquerda de Log Analytics.

### <a name="comparing-access-modes"></a>Comparando modos de acesso

A tabela a seguir resume os modos de acesso:

| Problema | Contexto do workspace | Contexto do recurso |
|:---|:---|:---|
| A quem cada modelo se destina? | Administração Central. Os administradores que precisam configurar a coleta de dados e os usuários que precisam de acesso a uma ampla variedade de recursos. Também exigido no momento para os usuários que precisam acessar os logs para recursos fora do Azure. | Equipes de aplicativos. Administradores de recursos do Azure sendo monitorados. |
| O que um usuário precisa para exibir os logs? | Permissões para o espaço de trabalho. Consulte **permissões de espaço de trabalho** em [gerenciar acesso usando permissões de espaço de trabalho](./manage-access.md#manage-access-using-workspace-permissions). | Acesso de leitura ao recurso. Consulte **permissões de recurso** em [gerenciar o acesso usando as permissões do Azure](./manage-access.md#manage-access-using-azure-permissions). As permissões podem ser herdadas (por exemplo, do grupo de recursos que a contém) ou diretamente atribuídas ao recurso. A permissão para os logs do recurso será atribuída automaticamente. |
| Qual é o escopo das permissões? | Espaço. Os usuários com acesso ao espaço de trabalho podem consultar todos os logs no espaço de trabalho de tabelas às quais eles têm permissões. Consulte [controle de acesso à tabela](./manage-access.md#table-level-azure-rbac) | Recurso do Azure. O usuário pode consultar logs de recursos específicos, grupos de recursos ou assinaturas aos quais eles têm acesso de qualquer espaço de trabalho, mas não podem consultar logs para outros recursos. |
| Como o usuário pode acessar os logs? | <ul><li>Inicie **os logs** no menu **Azure monitor** .</li></ul> <ul><li>Iniciar **logs** de **log Analytics espaços de trabalho**.</li></ul> <ul><li>De [pastas de trabalho](../visualizations.md#workbooks)do Azure monitor.</li></ul> | <ul><li>Iniciar **logs** no menu do recurso do Azure</li></ul> <ul><li>Inicie **os logs** no menu **Azure monitor** .</li></ul> <ul><li>Iniciar **logs** de **log Analytics espaços de trabalho**.</li></ul> <ul><li>De [pastas de trabalho](../visualizations.md#workbooks)do Azure monitor.</li></ul> |

## <a name="access-control-mode"></a>Modo de controle de acesso

O *modo de controle de acesso* é uma configuração em cada espaço de trabalho que define como as permissões são determinadas para o espaço de trabalho.

* **Exigir permissões de espaço de trabalho**: este modo de controle não permite o RBAC do Azure granular. Para que um usuário acesse o espaço de trabalho, ele deve receber permissões para o espaço de trabalho ou para tabelas específicas.

    Se um usuário acessar o espaço de trabalho após o modo de contexto de espaço de trabalho, ele terá acesso a todos os dados em qualquer tabela à qual tenha sido concedido acesso. Se um usuário acessar o espaço de trabalho após o modo de contexto de recurso, ele terá acesso a apenas dados para esse recurso em qualquer tabela à qual tenha sido concedido acesso.

    Essa é a configuração padrão para todos os espaços de trabalho criados antes de março de 2019.

* **Usar permissões de recurso ou espaço de trabalho**: esse modo de controle permite o RBAC do Azure granular. Os usuários podem receber acesso a apenas dados associados aos recursos que podem exibir atribuindo permissão do Azure `read` . 

    Quando um usuário acessa o espaço de trabalho no modo de contexto de espaço de trabalho, as permissões de espaço de trabalho se aplicam. Quando um usuário acessa o espaço de trabalho no modo de contexto de recurso, somente as permissões de recurso são verificadas e as permissões de espaço de trabalho são ignoradas. Habilite o RBAC do Azure para um usuário removendo-os das permissões do espaço de trabalho e permitindo que suas permissões de recurso sejam reconhecidas.

    Essa é a configuração padrão para todos os espaços de trabalho criados depois de março de 2019.

    > [!NOTE]
    > Se um usuário tiver apenas permissões de recurso para o espaço de trabalho, ele só poderá acessar o espaço de trabalho usando o modo de contexto de recurso, supondo que o modo de acesso do espaço de trabalho esteja definido para **usar permissões de recurso ou espaço de trabalho**.

Para saber como alterar o modo de controle de acesso no portal, com o PowerShell ou usando um modelo do Resource Manager, consulte [Configurar o modo de controle de acesso](./manage-access.md#configure-access-control-mode).

## <a name="scale-and-ingestion-volume-rate-limit"></a>Limite de taxa de volume de escala e ingestão

Azure Monitor é um serviço de dados de alta escala que atende a milhares de clientes enviando petabytes de dados a cada mês em um ritmo crescente. Os espaços de trabalho não são limitados em seu espaço de armazenamento e podem crescer até petabytes de dados. Não é necessário dividir espaços de trabalho devido à escala.

Para proteger e isolar Azure Monitor clientes e sua infraestrutura de back-end, há um limite de taxa de ingestão padrão projetado para proteger contra situações de picos e inundações. O padrão de limite de taxa é de cerca de **6 GB/minuto** e é projetado para habilitar a ingestão normal. Para obter mais detalhes sobre a medição do limite de volume de ingestão, consulte [Azure monitor limites de serviço](../service-limits.md#data-ingestion-volume-rate).

Os clientes que ingerirem menos de 4 TB/dia normalmente não atenderão a esses limites. Os clientes que ingerirem volumes maiores ou que tenham picos como parte de suas operações normais devem considerar a migração para [clusters dedicados](./logs-dedicated-clusters.md) em que o limite da taxa de ingestão pode ser gerado.

Quando o limite da taxa de ingestão for ativado ou chegar a 80% do limite, um evento será adicionado à tabela de *operações* no seu espaço de trabalho. É recomendável monitorá-lo e criar um alerta. Veja mais detalhes na [taxa de volume de ingestão de dados](../service-limits.md#data-ingestion-volume-rate).


## <a name="recommendations"></a>Recomendações

![Exemplo de design de contexto de recurso](./media/design-logs-deployment/workspace-design-resource-context-01.png)

Esse cenário abrange um design de espaço de trabalho único na assinatura de sua organização de ti que não é restrita pela soberania de dados ou conformidade regulatória, ou que precisa mapear para as regiões em que os recursos são implantados. Ele permite que as equipes de segurança e administrador de ti da sua organização possam aproveitar a integração aprimorada com o gerenciamento de acesso do Azure e um controle de acesso mais seguro.

Todos os recursos, soluções de monitoramento e informações como Application Insights e informações de VM, suporte à infraestrutura e aos aplicativos mantidos pelas diferentes equipes são configuradas para encaminhar seus dados de log coletados para o espaço de trabalho compartilhado centralizado da organização de ti. Os usuários em cada equipe recebem acesso a logs para recursos aos quais eles receberam acesso.

Depois de implantar a arquitetura do espaço de trabalho, você pode impor isso nos recursos do Azure com [Azure Policy](../../governance/policy/overview.md). Ele fornece uma maneira de definir políticas e garantir a conformidade com os recursos do Azure para que eles enviem todos os seus logs de recursos para um espaço de trabalho específico. Por exemplo, com máquinas virtuais do Azure ou conjuntos de dimensionamento de máquinas virtuais, você pode usar políticas existentes que avaliam a conformidade do espaço de trabalho e os resultados do relatório, ou personalizar para corrigir se não estiver em conformidade.  

## <a name="workspace-consolidation-migration-strategy"></a>Estratégia de migração de consolidação de espaço de trabalho

Para clientes que já implantaram vários espaços de trabalho e estão interessados em consolidar para o modelo de acesso de contexto de recurso, recomendamos que você use uma abordagem incremental para migrar para o modelo de acesso recomendado e não tente fazer isso de forma rápida ou agressiva. Seguir uma abordagem em fases para planejar, migrar, validar e desativar seguindo uma linha do tempo razoável ajudará a evitar incidentes não planejados ou um impacto inesperado em suas operações de nuvem. Se você não tiver uma política de retenção de dados para fins de conformidade ou de negócios, você precisará avaliar o tempo apropriado para manter os dados no espaço de trabalho que está migrando durante o processo. Enquanto você estiver reconfigurando os recursos para relatar para o espaço de trabalho compartilhado, você ainda poderá analisar os dados no espaço de trabalho original, conforme necessário. Quando a migração for concluída, se você for regido a manter os dados no espaço de trabalho original antes do final do período de retenção, não o exclua.

Ao planejar a migração para esse modelo, considere o seguinte:

* Entenda quais são os regulamentos do setor e as políticas internas relacionadas à retenção de dados que você deve obedecer.
* Verifique se as equipes de aplicativo podem trabalhar na funcionalidade de contexto de recurso existente.
* Identifique o acesso concedido aos recursos para suas equipes de aplicativo e teste em um ambiente de desenvolvimento antes de implementar em produção.
* Configure o espaço de trabalho para habilitar **as permissões usar recurso ou espaço de trabalho**.
* Remova a permissão de equipes de aplicativo para ler e consultar o espaço de trabalho.
* Habilite e configure qualquer solução de monitoramento, informações como informações de contêiner e/ou Azure Monitor para VMs, suas contas de automação e soluções de gerenciamento, como Gerenciamento de Atualizações, iniciar/parar VMs, etc., que foram implantadas no espaço de trabalho original.

## <a name="next-steps"></a>Próximas etapas

Para implementar as permissões de segurança e os controles recomendados neste guia, examine [gerenciar o acesso aos logs](./manage-access.md).
