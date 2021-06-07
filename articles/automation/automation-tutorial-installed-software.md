---
title: Descobrir qual software está instalado em suas VMs com a Automação do Azure | Microsoft Docs
description: Este artigo descreve o software instalado nas VMs em seu ambiente.
services: automation
keywords: inventário, automação, controle de alterações
ms.date: 04/11/2018
ms.topic: tutorial
ms.subservice: change-inventory-management
ms.custom: mvc
ms.openlocfilehash: 8451067ae86e95269c0c2f22554e1654ac64c1e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593775"
---
# <a name="discover-what-software-is-installed-on-your-vms"></a>Descobrir qual software está instalado em suas VMs

Neste tutorial, você aprenderá a usar o recurso de Controle de Alterações e Inventário da Automação do Azure para descobrir qual software está instalado em seu ambiente. É possível coletar e exibir inventário para software, arquivos, daemons do Linux, serviços do Windows e chaves de Registro do Windows em seus computadores. Acompanhar as configurações de seus computadores pode ajudar a detectar problemas operacionais em seu ambiente e entender melhor o estado dos seus computadores.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Habilitar Controle de Alterações e Inventário
> * Habilitar uma VM do Azure
> * Habilitar uma VM não Azure
> * Exibir software instalado
> * Pesquisar software instalado nos logs de inventário

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Uma assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma [Conta de Automação](./index.yml) para manter os runbooks inspetor e de ação e a tarefa do Observador.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) a ser habilitada para o recurso.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Habilitar Controle de Alterações e Inventário

Primeiro, é necessário habilitar o Controle de alterações e Inventário para este tutorial. Se você já habilitou o recurso anteriormente, esta etapa não é necessária.

>[!NOTE]
>Caso os campos estejam esmaecidos, outra solução de Automação está habilitada para a VM e o mesmo workspace e conta de Automação devem ser usados.

1. Navegue até sua conta de Automação e selecione **Inventário** ou **Controle de alterações** em **Gerenciamento de Configuração**.

2. Escolha o workspace do [Log Analytics](../azure-monitor/logs/log-query-overview.md). Esse workspace coleta dados gerados por recursos como o Controle de Alterações e o Inventário. O workspace fornece um único local para examinar e analisar dados de várias fontes.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

3. Selecione a conta de Automação a ser usada.

4. Configure o local da implantação.

5. Clique em **Habilitar** para implantar o recurso para sua VM. 

    ![Faixa de configuração do inventário](./media/automation-tutorial-installed-software/enableinventory.png)

Durante a instalação, a VM é provisionada com o agente do Log Analytics para Windows e um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Pode levar até 15 minutos para habilitação o Controle de Alterações e o Inventário. Durante esse tempo, não feche a janela do navegador.

Depois que o recurso é habilitado, as informações sobre o software instalado e as alterações na VM fluem para os logs do Azure Monitor. Pode levar entre 30 minutos e 6 horas para que os dados fiquem disponíveis para análise.

## <a name="add-an-azure-vm-to-change-tracking-and-inventory"></a>Adicionar uma VM do Azure ao Controle de Alterações e Inventário

1. Em sua conta de Automação, navegue até **Inventário** ou **Controle de Alterações** em **Gerenciamento de Configuração**.

2. Selecione **+ Adicionar VM do Azure**.

3. Selecione sua VM na lista de VMs. 

4. Clique em **Habilitar** para habilitar o Controle de Alterações e o Inventário na VM. O agente do Log Analytics para Windows é implantado na VM e configura a VM para se comunicar com o workspace do Log Analytics. A operação de instalação pode levar alguns minutos. 

5. Neste ponto, se desejar, você poderá selecionar uma nova VM na lista a ser habilitada para o recurso.

## <a name="add-a-non-azure-machine-to-change-tracking-and-inventory"></a>Adicionar um computador não Azure ao Controle de Alterações e Inventário

Para habilitar computadores não Azure para o recurso:

1. Instale o [Agente do Log Analytics para Windows](../azure-monitor/agents/agent-windows.md) ou o [Agente do Log Analytics para Linux](automation-linux-hrw-install.md), dependendo do seu sistema operacional. 

2. Navegue até sua conta de Automação e vá até **Inventário** ou **Controle de alterações** em **Gerenciamento de Configuração**. 

3. Clique em **Gerenciar Computadores**. Você vê uma lista dos computadores que apresentam relatórios para seu workspace do Log Analytics e que não têm o Controle de Alterações e o Inventário habilitados. Selecione a opção apropriada para seu ambiente:

    * **Habilitar em todos os computadores disponíveis** – Esta opção habilita o recurso em todos os computadores que enviam relatórios para seu workspace do Log Analytics.
    * **Habilitar em todos os computadores disponíveis e futuros computadores** – Esta opção habilita o recurso em todos os computadores que enviam relatórios para seu workspace do Log Analytics e, posteriormente, em todos os futuros computadores adicionados ao espaço de trabalho.
    * **Habilitar em computadores selecionados** – Esta opção habilita o recurso somente nos computadores que você selecionou.

    ![Gerenciar computadores](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Exibir software instalado

Depois que o recurso Controle de Alterações e Inventário estiver habilitada, será possível exibir os resultados na página Inventário.

1. Na sua conta de Automação, selecione **Inventário** em **Gerenciamento de Configuração**.

2. Na página Inventário, clique na guia **Software**.

3. Observe que há uma tabela que lista o software que havia sido encontrado. O software é agrupado por nome e versão do software. Os detalhes de alto nível de cada registro de software estão visíveis na tabela. Esses detalhes incluem o nome, versão e editor do software, hora da última atualização (a atualização mais recente relatada por um computador no grupo) e computadores (a contagem de computadores com esse software).

    ![Inventário de software](./media/automation-tutorial-installed-software/inventory-software.png)

4. Clique em uma linha para exibir as propriedades do registro de software e os nomes dos computadores com esse software.

5. Para procurar um software ou grupo de software específico, é possível pesquisar na caixa de texto diretamente acima dessa lista de software.
O filtro permite pesquisar com base no nome, versão ou editor do software. Por exemplo, pesquisar **Contoso** retorna todos os softwares com um nome, um editor ou versão que contém **Contoso**.

## <a name="search-inventory-logs-for-installed-software"></a>Pesquisar software instalado nos logs de inventário

O Controle de Alterações e Inventário gera dados de log que são enviados para os logs do Azure Monitor. Para pesquisar os logs executando consultas, selecione **Log Analytics** na parte superior da página Inventário. Os dados de inventário são armazenados com o tipo `ConfigurationData`.

A consulta de exemplo a seguir do Log Analytics retorna os resultados de inventário do publicador Microsoft Corporation.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Para saber mais sobre como executar e pesquisar arquivos de log nos logs do Azure Monitor, veja [logs do Azure Monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="see-the-software-inventory-for-a-single-machine"></a>Consultar o inventário de software de um computador

Para ver o inventário de software para um único computador, é possível acessar o Inventário da página de recursos de VM do Azure ou usar os logs do Azure Monitor para filtrar até o computador correspondente. A consulta de exemplo do Log Analytics a seguir retorna a lista de software para um computador chamado **ContosoVM**.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a exibir o inventário de software:

> [!div class="checklist"]
> * Habilitar Controle de Alterações e Inventário
> * Habilitar uma VM do Azure
> * Habilitar uma VM não Azure
> * Exibir software instalado
> * Pesquisar software instalado nos logs de inventário

Passe para a visão geral do recurso de Controle de Alterações e Inventário para saber mais sobre ela.

> [!div class="nextstepaction"]
> [Visão geral do Controle de Alterações e Inventário](change-tracking/overview.md)
