---
title: Descobrir qual software está instalado em seus computadores com a Automação do Azure | Microsoft Docs
description: Use o inventário para descobrir qual software está instalado nos computadores em seu ambiente.
services: automation
keywords: inventário, automação, alteração, acompanhamento
author: jennyhunter-msft
ms.author: jehunte
ms.date: 04/11/2018
ms.topic: tutorial
ms.service: automation
ms.subservice: change-inventory-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 47313781756e460a8c30638661489874481b88a0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476832"
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Descobrir qual software está instalado em computadores Azure e não Azure

Neste tutorial, você aprenderá como descobrir qual software está instalado em seu ambiente. É possível coletar e exibir inventário para software, arquivos, daemons do Linux, serviços do Windows e chaves de Registro do Windows em seus computadores. Acompanhar as configurações de seus computadores pode ajudar a detectar problemas operacionais em seu ambiente e entender melhor o estado dos seus computadores.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Habilitar a solução
> * Integrar uma VM do Azure
> * Integrar uma VM não Azure
> * Exibir software instalado
> * Pesquisar software instalado nos logs de inventário

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Uma assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma [Conta de Automação](automation-offering-get-started.md) para manter os runbooks inspetor e de ação e a tarefa do observador.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) a ser carregada.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Habilitar Controle de alterações e Inventário

Primeiro, é necessário habilitar o Controle de alterações e Inventário para este tutorial. Se você já ativou a solução **Controle de Alterações**, esta etapa não é necessária.

Navegue até sua conta de Automação e selecione **Inventário** em **GERENCIAMENTO DE CONFIGURAÇÃO**.

Escolha o espaço de trabalho do Log Analytics e a conta de automação e clique em **Habilitar** para habilitar a solução. A solução demora até 15 minutos para habilitar.

![Faixa de configuração integrada do inventário](./media/automation-tutorial-installed-software/enableinventory.png)

Para habilitar a solução, configure o local, o espaço de trabalho do Log Analytics e a conta de Automação a serem usados e clique em **Habilitar**. Caso os campos estejam esmaecidos, isso significa que outra solução de automação está habilitada para a VM e o mesmo workspace e conta de Automação devem ser usados.

Um workspace do [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) é usado para coletar dados gerados por recursos e serviços como Inventário.
O workspace fornece um único local para examinar e analisar dados de várias fontes.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

A habilitação da solução pode levar até 15 minutos. Durante esse tempo, não feche a janela do navegador.
Depois que a solução é habilitada, as informações sobre o software instalado e as alterações na VM fluem para os logs do Azure Monitor.
Pode levar entre 30 minutos e 6 horas para que os dados fiquem disponíveis para análise.

## <a name="onboard-a-vm"></a>Integrar uma VM

Na sua Conta de Automação, navegue até **Inventário** em **GERENCIAMENTO DE CONFIGURAÇÃO**.

Selecione **+ Adicionar VM do Azure**, isso abrirá a página de **Máquinas virtuais** e permite que você selecione uma VM existente na lista. Selecione a VM que deseja integrar. Na página que abre clique em **Habilitar** para habilitar a solução na VM. O Agente de Gerenciamento da Microsoft é implantado na VM e configura o agente para se comunicar com o espaço de trabalho do Log Analytics configurado ao habilitar a solução. A conclusão da integração pode levar alguns minutos. Neste ponto, você pode selecionar uma nova VM na lista e integrar outra VM.

## <a name="onboard-a-non-azure-machine"></a>Integrar um computador não Azure

Para adicionar computadores não Azure, instale o agente para [Windows](../azure-monitor/platform/agent-windows.md) ou [Linux](automation-linux-hrw-install.md) dependendo do seu sistema operacional. Após o agente ser instalado, navegue até sua conta de Automação e vá até **Inventário** em **GERENCIAMENTO DE CONFIGURAÇÃO**. Quando você clica em **Gerenciar computadores**, você vê uma lista dos computadores que apresentam relatórios para o seu espaço de trabalho do Log Analytics que não possuem a solução habilitada. Selecione a opção apropriada para seu ambiente.

* **Habilitar em todos os computadores disponíveis** - Esta opção habilita a solução em todos os computadores que enviam relatórios para seu espaço de trabalho do Log Analytics.
* **Habilitar em todos os computadores disponíveis e futuros computadores** - Esta opção habilitam a solução em todos os computadores que enviam relatórios para seu espaço de trabalho do Log Analytics e, posteriormente, em todos os futuros computadores adicionados ao espaço de trabalho.
* **Habilitar em computadores selecionados** - Esta opção habilita a solução somente nos computadores que você selecionou.

![Gerenciar computadores](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Exibir software instalado

Depois que a solução Controle de alterações e Inventário estiver habilitada, será possível exibir os resultados na página **Inventário**.

Na sua conta de Automação, selecione **Inventário** em **GERENCIAMENTO DE CONFIGURAÇÃO**.

Na página **Inventário**, clique na guia **Software**.

Na guia **Software**, há uma tabela que lista o software que havia encontrado. O software é agrupado por nome e versão do software.

Os detalhes de alto nível de cada registro de software estão visíveis na tabela. Esses detalhes incluem o nome, versão e editor do software, hora da última atualização (a atualização mais recente relatada por um computador no grupo) e computadores (a contagem de computadores com esse software).

![Inventário de software](./media/automation-tutorial-installed-software/inventory-software.png)

Clique em uma linha para exibir as propriedades do registro de software e os nomes dos computadores com esse software.

Para procurar um software ou grupo de software específico, é possível pesquisar na caixa de texto diretamente acima dessa lista de software.
O filtro permite pesquisar com base no nome, versão ou editor do software.

Por exemplo, pesquisar "Contoso" retorna todos os softwares com um nome, um editor ou versão que contém "Contoso".

## <a name="search-inventory-logs-for-installed-software"></a>Pesquisar software instalado nos logs de inventário

O inventário gera dados de log que são enviados para os logs do Azure Monitor. Para pesquisar os logs executando consultas, selecione **Log Analytics** na parte superior da janela **Inventário**.

Os dados de inventário são armazenados com o tipo **ConfigurationData**.
A consulta de exemplo a seguir do Log Analytics retorna os resultados de inventário onde o publicador é igual a "Microsoft Corporation".

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Para saber mais sobre como executar e pesquisar arquivos de log nos logs do Azure Monitor, veja [logs do Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

### <a name="single-machine-inventory"></a>Inventário de computador único

Para ver o inventário de software para um único computador, é possível acessar o Inventário da página de recursos de VM do Azure ou usar os logs do Azure Monitor para filtrar até o computador correspondente.
A consulta de exemplo do Log Analytics a seguir retorna a lista de software para um computador chamado ContosoVM.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como exibir o inventário de software, por exemplo, como:

> [!div class="checklist"]
> * Habilitar a solução
> * Integrar uma VM do Azure
> * Integrar uma VM não Azure
> * Exibir software instalado
> * Pesquisar software instalado nos logs de inventário

Passe para a visão geral da solução de Controle de alterações e Inventário para saber mais sobre ela.

> [!div class="nextstepaction"]
> [Solução de gerenciamento de alterações e inventário](automation-change-tracking.md)
