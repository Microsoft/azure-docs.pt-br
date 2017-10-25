---
title: "Compilar uma solução de gerenciamento no OMS | Microsoft Docs"
description: "As soluções de gerenciamento estendem a funcionalidade do OMS (Operations Management Suite), fornecendo cenários de gerenciamento empacotados que os clientes podem adicionar ao seu espaço de trabalho do OMS.  Este artigo fornece detalhes sobre como criar soluções de gerenciamento para usar em seu próprio ambiente ou disponibilizar para os clientes."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ea959f1e95ea46c07eec3afa9d46bfeb72ca3e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="design-and-build-a-management-solution-in-operations-management-suite-oms-preview"></a>Projetar e compilar uma solução de gerenciamento no OMS (Operations Management Suite) (Visualização)
> [!NOTE]
> Esta é uma documentação preliminar para criar soluções de gerenciamento no OMS, que estão atualmente em visualização. Os esquemas descritos a seguir estão sujeitos a alterações.

[As soluções de gerenciamento](operations-management-suite-solutions.md) estendem a funcionalidade do OMS (Operations Management Suite), fornecendo cenários de gerenciamento empacotados que os clientes podem adicionar ao seu espaço de trabalho do OMS.  Este artigo apresenta um processo básico para projetar e compilar uma solução de gerenciamento adequada às necessidades mais comuns.  Se você nunca tiver criado soluções de gerenciamento, use este processo como um ponto de partida e aproveite os conceitos para soluções mais complexas, conforme suas necessidades evoluem.

## <a name="what-is-a-management-solution"></a>O que é uma solução de gerenciamento?

As soluções de gerenciamento contêm recursos do OMS e do Azure que trabalham juntos para atingir um cenário de monitoramento específico.  Eles são implementados como [modelos de Gerenciamento de recursos](../azure-resource-manager/resource-manager-template-walkthrough.md) que contém detalhes sobre como instalar e configurar os recursos contidos quando a solução é instalada.

A estratégia básica é iniciar a solução de gerenciamento compilando os componentes individuais em seu ambiente do Azure.  Assim que as funcionalidades estiverem funcionando corretamente, você poderá começar a agrupá-las em um [arquivo de solução de gerenciamento](operations-management-suite-solutions-solution-file.md). 


## <a name="design-your-solution"></a>Projetar sua solução
O padrão mais comum para uma solução de gerenciamento é mostrado no diagrama a seguir.  Os diferentes componentes nesse padrão são discutidos a seguir.

![Visão geral da solução OMS](media/operations-management-suite-solutions/solution-overview.png)


### <a name="data-sources"></a>Fontes de dados
A primeira etapa na criação de uma solução é determinar os dados necessários do repositório do Log Analytics.  Esses dados podem ser coletados por uma [fonte de dados](../log-analytics/log-analytics-data-sources.md) ou [outra solução](operations-management-suite-solutions.md), ou talvez sua solução precise fornecer o processo para coletá-los.

Há várias maneiras de coletar fontes de dados no repositório do Log Analytics, conforme descrito em [Fontes de dados no Log Analytics](../log-analytics/log-analytics-data-sources.md).  Isso inclui eventos no Log de Eventos do Windows, ou gerados pelo Syslog, além dos contadores de desempenho para clientes Windows e Linux.  Você também pode reunir dados de recursos do Azure coletados pelo Azure Monitor.  

Se você precisar de dados que não podem ser acessados por meio de qualquer uma das fontes de dados disponíveis, use a [API do Coletor de Dados HTTP](../log-analytics/log-analytics-data-collector-api.md) que permite a gravação de dados no repositório do Log Analytics de qualquer cliente que possa chamar uma API REST.  O meio mais comum de coletar dados personalizados em uma solução de gerenciamento é criar um [runbook na Automação do Azure](../automation/automation-runbook-types.md) que coleta os dados necessários de recursos externos ou do Azure e usa a API do Coletor de Dados para gravar no repositório.  

### <a name="log-searches"></a>Pesquisas de log
As [pesquisas de logs](../log-analytics/log-analytics-log-searches.md) são usadas para extrair e analisar dados no repositório do Log Analytics.  Eles são usados pelas exibições e alertas, além de permitir que o usuário execute análise ad hoc de dados no repositório.  

Defina as consultas que você considera úteis para o usuário, mesmo se não forem usadas por modos de exibição ou alertas.  Elas estarão disponíveis como Pesquisas Salvas no portal e você também pode incluí-las em uma [parte de visualização de Lista de Consultas](../log-analytics/log-analytics-view-designer-parts.md#list-of-queries-part) em seu modo de exibição personalizado.

### <a name="alerts"></a>Alertas
[Alertas no Log Analytics](../log-analytics/log-analytics-alerts.md) identificam problemas por meio de [pesquisas de log](#log-searches) nos dados do repositório.  Eles notificam o usuário ou executam automaticamente uma ação como resposta. Identifique as condições de alerta diferentes para seu aplicativo e inclua regras de alerta correspondentes em seu arquivo de solução.

Se o problema puder ser corrigido com um processo automatizado, normalmente criará um runbook na Automação do Azure para executar essa correção.  A maioria dos serviços do Azure pode ser gerenciada com [cmdlets](/powershell/azure/overview), que o runbook aproveitaria para executar essa funcionalidade.

Se a sua solução exigir uma funcionalidade externa em resposta a um alerta, use uma [resposta de webhook](../log-analytics/log-analytics-alerts-actions.md).  Isso permite que você chame um serviço Web externo que envia informações do alerta.

### <a name="views"></a>Modos de exibição
Os modos de exibição no Log Analytics são usados para visualizar dados do repositório do Log Analytics.  Cada solução normalmente conterá um único modo de exibição com um [bloco](../log-analytics/log-analytics-view-designer-tiles.md) que é exibido no painel principal do usuário.  O modo de exibição pode conter qualquer quantidade de [partes de visualização](../log-analytics/log-analytics-view-designer-parts.md) para fornecer visualizações diferentes dos dados coletados para o usuário.

[Crie exibições personalizadas usando o Designer de Exibição](../log-analytics/log-analytics-view-designer.md), que mais tarde você pode exportar para inclusão em seu arquivo de solução.  


## <a name="create-solution-file"></a>Criar arquivo de solução
Depois de configurar e testar os componentes que farão parte de sua solução, [crie o arquivo de solução](operations-management-suite-solutions-solution-file.md).  Você irá implementar os componentes da solução em um [o modelo do Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) que inclui um [recurso de solução](operations-management-suite-solutions-solution-file.md#solution-resource) com relações com outros recursos no arquivo.  


## <a name="test-your-solution"></a>Testar sua solução
Enquanto você estiver desenvolvendo sua solução, precisará instalar e testá-la em seu espaço de trabalho.  Faça isso usando qualquer um dos métodos disponíveis para [testar e instalar os modelos do Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="publish-your-solution"></a>Publicar sua solução
Depois de concluir e testar sua solução, disponibilize-a para clientes usando as seguintes fontes.

- **Modelos de Início Rápido do Azure**.  [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/) é um conjunto de modelos do Resource Manager que contribuem para a comunidade por meio do GitHub.  Você pode disponibilizar sua solução seguindo as informações no [guia de contribuição](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Azure Marketplace**.  O [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) permite distribuir e vender sua solução para outros desenvolvedores, ISVs e profissionais de TI.  Aprenda a publicar sua solução no Azure Marketplace em [Como publicar e gerenciar uma oferta no Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).



## <a name="next-steps"></a>Próximas etapas
* Saiba como [criar um arquivo de solução](operations-management-suite-solutions-solution-file.md) para sua solução de gerenciamento.
* Aprenda os detalhes da [Criação de modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Pesquise entre os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates) para obter exemplos de diferentes modelos do Resource Manager.