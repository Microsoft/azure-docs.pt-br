---
title: Entender a integridade de suas máquinas virtuais do Azure | Microsoft Docs
description: Este artigo descreve como entender a integridade das máquinas virtuais e sistemas operacionais subjacentes com o Azure Monitor para VMs.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: 2bf891f8cfecbb9e78e511dcee7ed1c61c170016
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340137"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Entender a integridade de suas máquinas virtuais do Azure

O Azure inclui serviços para funções específicas ou tarefas no espaço de monitoramento, mas ele não fornece perspectivas de integridade detalhado dos sistemas operacionais (OSs) hospedados em máquinas virtuais (VMs). Embora você possa usar o Azure Monitor para condições diferentes, ele não foi projetado para modelar e representam a integridade dos componentes principais, ou a integridade geral de máquinas virtuais.

Usando o Azure Monitor de integridade de VMs, você pode monitorar ativamente a disponibilidade e desempenho de um sistema operacional de convidado Windows ou Linux. O recurso de integridade usa um modelo que representa os principais componentes e suas relações, fornece critérios que especifica como medir a integridade dos componentes e envia um alerta quando detecta uma condição de não íntegro.

Exibir o estado de integridade geral de uma VM do Azure e o sistema operacional subjacente pode ser observado de duas perspectivas: diretamente de uma VM ou em todas as VMs em um grupo de recursos do Azure Monitor.

Este artigo mostra como avaliar rapidamente, investigar e resolver problemas de integridade quando eles são detectados pelo Monitor do Azure para o recurso de integridade de VMs.

Para obter informações sobre como configurar o Monitor do Azure para VMs, consulte [Ativar o Monitor do Azure para VMs](vminsights-enable-overview.md).

## <a name="monitoring-configuration-details"></a>Detalhes de configuração de monitoramento

Esta seção descreve os critérios de integridade padrão para monitorar as VMs do Linux e Windows Azure. Todos os critérios de integridade são pré-configurados para enviar um alerta quando detecta uma condição de não íntegro.

### <a name="windows-vms"></a>VMs Windows

- Megabytes Disponíveis de Memória
- Média de Segundos de Disco por Gravação (Disco Lógico)
- Média de Segundos de Disco por Gravação (Disco)
- Média de Segundos de Disco por Gravação
- Média de segundos de disco lógico por transferência
- Média de segundos de disco por leitura
- Média de segundos de disco por transferência
- Comprimento atual da fila de disco (disco lógico)
- Comprimento atual da fila de disco (disco)
- Tempo ocioso percentual do disco
- Erro ou corrupção do sistema de arquivos
- Espaço livre em disco lógico (%) baixo
- Espaço livre em disco lógico (MB) baixo
- Porcentagem de Tempo Ocioso do Disco Lógico
- Páginas de memória por segundo
- Porcentagem de largura de banda usada
- Porcentagem de largura de banda total utilizada
- Porcentagem de largura de banda usada
- Porcentagem de memória confirmada em uso
- Tempo ocioso percentual do disco
- Integridade do serviço de cliente DHCP
- Integridade do serviço de cliente DNS
- Integridade do serviço RPC
- Integridade do serviço de servidor
- Porcentagem de utilização de CPU total
- Integridade de serviço de Log de eventos do Windows
- Integridade de serviço de Firewall do Windows
- Integridade de serviço de gerenciamento remoto do Windows

### <a name="linux-vms"></a>VMs Linux

- Disk Avg. de segundos/Transferência do Disco
- Disk Avg. de segundos/Leitura do Disco
- Disk Avg. de segundos/Gravação do Disco
- Integridade do disco
- Espaço livre em disco lógico
- % de espaço livre no Disco lógico
- % de Inodes livres do disco lógico
- Saúde do adaptador de rede
- Percentual Total do Processador
- Sistema Operacional Megabytes Disponíveis de Memória

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Para entrar, vá para o [portal do Azure](https://portal.azure.com).

## <a name="introduction-to-azure-monitor-for-vms-health"></a>Introdução ao Azure Monitor para integridade de VMs

Antes de usar o recurso de integridade para uma única VM ou grupo de VMs, é importante entender como as informações são apresentadas e o que representam as visualizações.

### <a name="view-health-directly-from-a-vm"></a>Exibir a integridade diretamente de uma VM

Para exibir a integridade de uma VM do Azure, selecione **Insights (visualização)** no painel esquerdo da VM. Na página de informações da VM, o **integridade** guia é aberto por padrão e mostra a exibição de integridade da VM.

![Visão geral de integridade do Monitor do Azure para VMs de uma máquina virtual do Azure selecionada](./media/vminsights-health/vminsights-directvm-health.png)

No **integridade** guia, em **integridade da VM convidada**, a tabela mostra o estado de integridade da VM e o número total de alertas de integridade da VM gerado por um componente não íntegro.

Para obter mais informações, consulte [Alertas](#alerts).

Os estados de integridade definidos para uma VM estão descritos na seguinte tabela:

|ícone |Estado de integridade |Significado |
|-----|-------------|---------------|
| |Healthy |A VM está em condições de integridade definidas. Esse estado indica que existem é nenhum problema detectado e a VM está funcionando normalmente. Com um monitor de acumulação pai, a integridade acumula e reflete o estado mais favorável, ou pior do filho.|
| |Crítico |O estado que não esteja dentro da condição de integridade definidas, indicando que um ou os problemas mais críticos foram detectados. Esses problemas devem ser resolvidos para restaurar a funcionalidade normal. Com um monitor de acumulação pai, o estado de integridade se acumula e reflete o estado mais favorável, ou pior do filho.|
| |Aviso |O estado estiver entre dois limites para a condição de integridade definidas, onde um indica um estado de aviso e a outra indica um estado crítico (três limites de estado de integridade podem ser configurados), ou quando um problema não crítico pode causar problemas críticos se não resolvido. Com um monitor de acumulação pai, se um ou mais filhos está em um estado de aviso, o pai apresentará um estado de aviso. Se for um filho em um estado crítico e outro filho em um estado de aviso, o pacote cumulativo de pai mostrará o estado de integridade, como críticas.|
| |Desconhecido |O estado não pode ser calculado por vários motivos. A seção a seguir fornece detalhes adicionais e possíveis soluções. |

Um estado de integridade desconhecido pode ser causado pelos seguintes problemas:

- O agente foi reconfigurado e não há mais relatórios no espaço de trabalho especificado quando o Azure Monitor para VMs foi habilitado. Para configurar o agente para relatar para o espaço de trabalho, consulte [adição ou remoção de um espaço de trabalho](../platform/agent-manage.md#adding-or-removing-a-workspace).
- A VM foi excluída.
- O espaço de trabalho associado com o Azure Monitor para máquinas virtuais foi excluído. Você pode recuperar o espaço de trabalho se tiver benefícios de suporte Premier. Vá para [Premier](https://premier.microsoft.com/) e abra uma solicitação de suporte.
- As dependências de solução foram excluídas. Para reabilitar as soluções do ServiceMap e InfrastructureInsights em seu espaço de trabalho do Log Analytics, reinstalar essas soluções usando o [modelo do Resource Manager](vminsights-enable-at-scale-powershell.md#install-the-servicemap-and-infrastructureinsights-solutions). Ou, use a opção de configurar o espaço de trabalho encontrada na guia de Introdução.
- A VM foi desligada.
- O serviço de VM do Azure está disponível ou manutenção está sendo executada.
- O espaço de trabalho [diária ou limite de retenção](../platform/manage-cost-storage.md) foi atendida.

Selecione **exibir diagnóstico de integridade** abrir uma página mostrando todos os componentes de uma VM, associado a outros problemas detectados pelo monitoramento de componentes relacionados à VM, as alterações de estado e critérios de integridade.

Para saber mais, consulte [Diagnóstico de integridade](#health-diagnostics).

No **integridade** seção, uma tabela mostra o acúmulo de integridade dos componentes de desempenho monitoradas pelos critérios de integridade. Esses componentes incluem **CPU**, **memória**, **disco**, e **rede**. Selecionar um componente abre uma página que lista todos os critérios de monitoramento e o estado de integridade desse componente.

Quando você acessar a integridade de uma VM do Azure que executa o Windows, o estado de integridade dos serviços Windows cinco principais é mostrado em **Core services integridade**. Selecionar qualquer um dos serviços abre uma página que lista os critérios de integridade de monitoramento desse componente, juntamente com seu estado de integridade.

Selecionando o nome dos critérios de integridade abre o painel de propriedade. Nesse painel, você pode examinar os detalhes de configuração, inclusive se os critérios de integridade tem um alerta correspondente do Azure Monitor.

Para obter mais informações, consulte [diagnóstico de integridade e como trabalhar com os critérios de integridade](#health-diagnostics).

### <a name="aggregate-vm-perspective"></a>Perspectiva VM de agregação

Para exibir a coleção de integridade para todas as suas VMs em um grupo de recursos, selecione **do Azure Monitor** da lista de navegação no portal e, em seguida, selecione **máquinas virtuais (versão prévia)** .

![Exibição do Azure Monitor de monitoramento de Insights de VM](./media/vminsights-health/vminsights-aggregate-health.png)

No **assinatura** e **grupo de recursos** listas suspensas, selecione o grupo de recursos apropriado que inclui as VMs relacionadas ao grupo, para exibir seu estado de integridade relatados. Sua seleção somente se aplica ao recurso de integridade e não reporta **desempenho** ou **mapa** guias.

O **integridade** guia fornece as seguintes informações:

* Quantas VMs estão em um estado crítico ou não íntegro, versus quantos estão íntegros ou não estiver enviando dados (conhecidos como um estado desconhecido).
* Quais e quantas VMs pelo sistema operacional estão relatando um estado não íntegro.
* Quantas VMs não estão íntegros devido a um problema detectado com um processador, disco, memória ou adaptador de rede, categorizadas por estado de integridade.
* Quantas VMs não estão íntegros devido a um problema detectado com um serviço de sistema operacional principal, categorizado por estado de integridade.

Sobre o **integridade** guia, você pode identificar os problemas críticos detectados pelos critérios de integridade de monitoramento da VM e detalhes do alerta de revisão e artigos de conhecimento associados. Esses artigos podem ajudar no diagnóstico e solução de problemas. Selecione qualquer uma das gravidades para abrir a página [Todos os Alertas](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) filtrada por tal gravidade.

A lista da **distribuição da VM por sistema operacional** mostra as VMs listadas pela edição do Windows ou pela distribuição do Linux, junto com sua versão. Em cada categoria de sistema operacional, as VMs são mais divididas com base na integridade da VM.

![Perspectiva de distribuição de máquinas virtuais do VM Insights](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Selecione qualquer coluna, incluindo **contagem de VM**, **crítico**, **aviso**, **Íntegro**, ou **desconhecido**. Exibir a lista de resultados filtrados na **máquinas virtuais** página que correspondem a coluna selecionada.

Por exemplo, para examinar todas as VMs que executam o Red Hat Enterprise Linux versão 7.5, selecione a **contagem de VM** valor para que o sistema operacional e ele listará as VMs de correspondência que o filtro e seu estado de integridade atual.

![Exemplo de acumulação de VMs do Red Hat Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

No **máquinas virtuais** página, se você selecionar o nome de uma VM sob a coluna **nome da VM**, você será direcionado para o **instância VM** página. Esta página fornece mais detalhes de alertas e problemas de critérios de integridade que estão afetando a VM selecionada. Filtrar os detalhes do estado de integridade, selecionando **estado de integridade** ícone no canto superior esquerdo da página para ver quais componentes estão íntegros. Você também pode exibir alertas de integridade da VM geradas por um componente não íntegro, categorizado por gravidade do alerta.

Dos **lista de VMs** exibir, selecione o nome de uma VM para abrir o **integridade** página para a VM, da mesma forma como se você selecionou **Insights (visualização)** da VM diretamente.

![Insights VM de uma máquina virtual do Azure selecionada](./media/vminsights-health/vminsights-directvm-health.png)

O **Insights (visualização)** página mostra um status de integridade do pacote cumulativo de atualizações para a VM e alertas. Esse status de integridade é categorizada por gravidade, que representa os alertas de integridade do VM geradas quando o estado de integridade é alterado de Íntegro para não íntegro, com base em critérios. Selecionando **VMs na condição crítica** abre uma página com uma lista de uma ou mais VMs em um estado de integridade crítico.

Selecionando o status de integridade para um dos programas de VMs a **diagnóstico de integridade** exibição da VM. Nessa exibição, você pode determinar quais critérios de integridade é refletindo um problema do estado de integridade. Quando o **diagnóstico de integridade** página é aberta, ela mostra todos os componentes VM e seus critérios de integridade associada com o estado de integridade atual.

Para saber mais, consulte [Diagnóstico de integridade](#health-diagnostics).

Selecionar **Visualizar todos os critérios de integridade** abre uma página mostrando uma lista de todos os critérios de integridade disponíveis com esse recurso. As informações podem ser filtradas com base nas seguintes opções:

* **Tipo**. Há três tipos de critérios de integridade para avaliar condições e acumular o estado de integridade geral de uma VM monitorada:
    - **Unit**. Medem alguns aspectos de uma VM. Esse tipo de critérios de integridade pode incluir verificar um contador de desempenho para determinar o desempenho do componente, como executar um script para executar uma transação sintética, ou assistir a um evento que indica um erro. Por padrão, o filtro está definido para a unidade.
    - **Dependência**. Fornece um rollup de integridade entre diferentes entidades. Esses critérios de integridade permite que a integridade de uma entidade para depender da integridade de outro tipo de entidade que ele depende para operação com êxito.
    - **Agregação**. Fornece um estado de integridade combinado dos critérios de integridade semelhante. Critério de integridade de unidade e dependência geralmente são configurados em um critério de integridade agregado. Além de fornecer uma melhor organização geral dos diversos critérios de integridade direcionados a uma entidade, o critério de integridade agregado fornece um estado de saúde exclusivo para categorias distintas das entidades.

* **Categoria**. O tipo de critérios de integridade usadas para agrupar critérios semelhantes para fins de relatório. Essas categorias são **disponibilidade** e **desempenho**.

Para ver quais instâncias estão íntegras, selecione um valor sob a **componente não íntegro** coluna. Nessa página, uma tabela lista os componentes que estão em um estado de integridade crítico.

## <a name="health-diagnostics"></a>Diagnóstico de integridade

O **diagnóstico de integridade** página permite que você visualize o modelo de integridade de uma VM. Esta página lista todos os componentes VM, critérios de integridade associada, alterações de estado e outros problemas significativos identificados por componentes monitorados relacionados à VM.

![Exemplo de página de diagnóstico de integridade para uma VM](./media/vminsights-health/health-diagnostics-page-01.png)

Inicie o diagnóstico de integridade usando os seguintes métodos:

* Por estado de integridade de rollup para todas as VMs da agregação perspectiva VM no Azure Monitor:

    1. Sobre o **integridade** , selecione o ícone para **crítico**, **aviso**, **Íntegro**, ou **desconhecido** estado de integridade sob a seção **integridade da VM convidada**.
    2. Vá para a página que lista todas as VMs correspondentes a essa categoria filtrada.
    3. Selecione o valor na **estado de integridade** coluna para abrir o diagnóstico de integridade no escopo para essa VM.

* Pelo sistema operacional da agregação perspectiva VM no Azure Monitor. Em **distribuição da VM**, a seleção de qualquer um dos valores da coluna abrirá a página **Máquinas Virtuais** e retornará uma lista na tabela correspondente à categoria filtrada. Selecionar este valor sob **estado de integridade** coluna abre o diagnóstico de integridade para a VM selecionada.
 
* A VM no Azure Monitor para VMs convidada **integridade** guia, selecionando **exibir diagnóstico de integridade**.

Diagnóstico de integridade organiza as informações de integridade em duas categorias: desempenho e disponibilidade.
 
Todos os critérios de integridade definidos para um componente, como o disco lógico, CPU e assim por diante, podem ser exibidos sem filtragem em duas categorias. Essas exibições podem estar em um modo de exibição de todo o de critérios ou por meio de filtrar os resultados por qualquer categoria quando você seleciona **disponibilidade** ou **desempenho**.

Além disso, a categoria de critérios pode ser vista ao lado de **critérios de integridade** coluna. Se os critérios não corresponderem a categoria selecionada, uma mensagem dizendo **sem critérios de integridade disponíveis para a categoria selecionada** aparece na **critérios de integridade** coluna.

O estado de um critério de integridade é definido por um dos quatro tipos: **Críticos**, **aviso**, **Íntegro**, e **desconhecido**. As três primeiras são configuráveis, o que significa que você pode modificar os valores de limite dos monitores diretamente na **critérios de integridade** painel de configuração. Isso também é possível usando a API REST do Azure Monitor [monitor de operação de atualização](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update). **Desconhecido** não é configurável e é reservada para cenários específicos.

O **diagnóstico de integridade** página possui três seções principais:

* Modelo de componente
* Critérios de integridade
* Alterações de estado

![Seções da página de diagnóstico de integridade](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Modelo de componente

A coluna mais à esquerda na **diagnóstico de integridade** página está **modelo de componente**. Todos os componentes, que são associados com a VM, são exibidos nesta coluna, juntamente com seu estado de integridade atual.

No exemplo a seguir, estão os componentes descobertos **disco**, **disco lógico**, **processador**, **memória**, e  **Sistema operacional**. Várias instâncias desses componentes são descobertas e exibidas nessa coluna.

Por exemplo, a figura a seguir mostra que a VM tem duas instâncias de discos lógicos, **c:** e **unidade d:** , que estão em um estado íntegro:

![Exemplo de modelo de componente apresentado em Diagnósticos de integridade](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Critérios de integridade

É a coluna do centro na página de diagnóstico de integridade **critérios de integridade**. O modelo de integridade definido para a VM é exibido em uma árvore hierárquica. O modelo de integridade de uma VM consiste em critérios de integridade agregada e da unidade.

![Exemplos de critérios de integridade apresentados em Diagnósticos de saúde](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Um critério de integridade mede a integridade de uma instância monitorada, que pode ser um valor de limite, o estado de uma entidade e assim por diante. Um critério de integridade tem dois ou três limites do estado de integridade configurável, conforme descrito anteriormente. Em qualquer momento, o critério de integridade pode ser em apenas um de seus possíveis estados.

O modelo de integridade define os critérios que determinam a integridade do destino geral e componentes de destino. A hierarquia dos critérios é mostrada na **critérios de integridade** seção o **diagnóstico de integridade** página.

A diretiva de acumulação de integridade faz parte da configuração de critérios de integridade agregada (o padrão é definido como **pior do**). Você pode encontrar um conjunto de critérios de integridade em execução como parte desse recurso no padrão de [detalhes de configuração de monitoramento](#monitoring-configuration-details) seção deste artigo.

Você também pode usar a API REST do Azure Monitor [lista de instâncias de monitor por recurso](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) para obter uma lista de todos os critérios de integridade. Esse critério inclui detalhes de configuração em execução em relação ao recurso de VM do Azure.

O **unidade** tipo de critérios de integridade pode ter sua configuração modificada, selecionando o link de reticências ao lado direito. Selecione **Mostrar detalhes** para abrir o painel de configuração.

![Configurando um exemplo de critérios de integridade](./media/vminsights-health/health-diagnostics-vm-example-02.png)

No painel de configuração para os critérios de integridade selecionado, se você usar o exemplo **média segundos de disco por gravação**, o limite pode ser configurado com um valor numérico diferente. É um monitor de dois estados, o que significa que ele pode alterar somente a partir **Íntegro** à **aviso**.

Às vezes, outros critérios de integridade usam três estados, onde você pode configurar o valor para os limites de estado de integridade de aviso e críticos. Você também pode modificar um limite usando a API de REST do Azure Monitor [configuração do monitor](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update).

>[!NOTE]
>Aplicar alterações de configuração de critérios de integridade para uma instância aplica a todas as instâncias monitoradas. Por exemplo, se você selecionar **disco d: de-1** e, em seguida, modifique o **média segundos de disco por gravação** limite, a alteração se aplica a todas as instâncias descobertas e monitoradas na VM.


![Configurando um critério de integridade de um exemplo de monitor de unidade](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Se você quiser saber mais sobre critérios de integridade, incluímos os artigos de conhecimento para ajudar a identificar problemas, causas e resoluções. Selecione **exibir informações** na página, consulte o artigo de Conhecimento relacionados.

Para examinar todos os artigos de Conhecimento incluídos com o Azure Monitor de integridade de VMs, consulte [documentação do Azure Monitor integridade conhecimento](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).

### <a name="state-changes"></a>Alterações de estado

A coluna à direita do **diagnóstico de integridade** página está **alterações de estado**. Esta coluna lista todas as alterações de estado associadas com os critérios de integridade selecionados na **critérios de integridade** seção, ou a alteração de estado da VM se uma VM foi selecionada na **modelo de componente** ou  **Critérios de integridade** coluna da tabela.

![Exemplo de alterações de estado apresentadas no Diagnóstico de integridade](./media/vminsights-health/health-diagnostics-page-statechanges.png)

A seção a seguir mostra o estado de critérios de integridade e o tempo associado. Essas informações mostram o estado mais recente na parte superior da coluna.

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>Associação de colunas do modelo de componente, critérios de integridade e as alterações de estado

As três colunas são interconectadas uns com os outros. Quando você seleciona uma instância na **modelo de componente** coluna, o **critérios de integridade** coluna é filtrada para essa exibição de componente. Do mesmo modo, o **alterações de estado** coluna é atualizada com base nos critérios de integridade selecionado.

![Exemplo de seleção de instância monitorada e resultados](./media/vminsights-health/health-diagnostics-vm-example-01.png)

Por exemplo, se você selecionar *Disk - 1 d* na lista sob **modelo de componente**, **critérios de integridade** filtra *disco - 1 D:* e  **Alterações de estado** mostra a alteração de estado com base na disponibilidade dos *Disk - 1 d*.

Para ver um estado de integridade atualizada, você pode atualizar a página de diagnóstico de integridade, selecionando o **Refresh** link. Se houver uma atualização no estado de integridade do critério de integridade com base no intervalo de pesquisa predefinido, essa tarefa permitirá que você evite esperar e reflita o estado de integridade mais recente. O **estado de integridade de critérios** é um filtro que permite que você definir o escopo os resultados de acordo com o estado de integridade selecionado: Íntegro, aviso, crítico, desconhecido e tudo. O **última atualização** tempo no canto superior direito representa a última vez em que a página de diagnóstico de integridade foi atualizada.

## <a name="alerts"></a>Alertas

Integra-se com o Azure Monitor de integridade de VMs [alertas do Azure](../../azure-monitor/platform/alerts-overview.md). Ele gera um alerta quando critérios predefinidos, quando detectada, alterar de um estado íntegro para um estado não íntegro. Os alertas são categorizados por severidade, de severidades de 0 a 4 de gravidade, com severidades de 0 como o nível mais alto.

Alertas não estão associados um grupo de ações para notificá-lo quando o alerta foi disparado. O proprietário da assinatura deve configurar notificações, seguindo as etapas a [configurar alertas](#configure-alerts) seção.

O número total de alertas de integridade da VM categorizados por severidade está disponível na **integridade** painel sob o **alertas** seção. Quando você seleciona o número total de alertas ou o número correspondente a um nível de gravidade, a página **Alertas** é aberta e lista todos os alertas correspondentes à sua seleção.

Por exemplo, se você selecionar a linha correspondente à **nível de gravidade 1**, você verá a seguinte exibição:

![Exemplo de todos os alertas de severidade de nível 1](./media/vminsights-health/vminsights-sev1-alerts-01.png)

O **todos os alertas** página não está no escopo para mostrar apenas os alertas correspondentes sua seleção. Ele também seja filtrado por **tipo de recurso** para mostrar somente os alertas de integridade gerados por um recurso VM. Esse formato é refletido na lista de alertas, sob a coluna **recurso de destino**, onde ele mostra a VM do Azure alerta gerado quando uma condição de não íntegro foi atendida.

Alertas de outros tipos de recursos ou serviços não devem ser incluídos nessa exibição. Esses alertas incluem alertas de log, que se baseiam em consultas de log ou alertas de métrica que normalmente seriam exibidos do padrão do Azure Monitor [todos os alertas](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) página.

Você pode filtrar essa exibição, selecionando valores nos menus suspensos na parte superior da página.

|Coluna |DESCRIÇÃO |
|-------|------------|
|Assinatura |Selecione uma assinatura do Azure. Apenas alertas na assinatura selecionada são incluídos na exibição. |
|Grupo de recursos |Selecione um único grupo de recursos. Somente alertas com destinos no grupo de recursos selecionado são incluídos na exibição. |
|Tipo de recurso |Selecione um ou mais tipos de recurso. Por padrão, somente os alertas de destino **Máquinas virtuais** estão selecionados e incluídos nessa exibição. Essa coluna somente estará disponível depois que um grupo de recursos for especificado. |
|Resource |Selecione um recurso. Apenas alertas com esse recurso como um destino são incluídos na exibição. Esta coluna está disponível somente depois que um tipo de recurso foi especificado. |
|Severity |Selecione a gravidade do alerta ou selecione **Tudo** para incluir alertas de todas as gravidades. |
|Monitorar condição |Selecione uma condição de monitor para alertas de filtro se tiver sido acionados ou resolvidos pelo sistema se a condição não está mais ativa. Ou, selecione **todos os** para incluir alertas de todas as condições. |
|Estado de alerta |Selecione um estado de alerta **New**, **confirmação**, **fechado**, ou **todos os** para incluir alertas de todos os estados. |
|Monitorar serviço |Selecione um serviço ou selecione **Tudo** para incluir todos os serviços. Somente os alertas do Insights VM têm suporte para esse recurso.|
|Intervalo de tempo| Apenas alertas acionados dentro da janela de tempo selecionada são incluídos na exibição. Os valores com suporte são a última hora, as últimas 24 horas, os últimos 7 dias e os últimos 30 dias. |

Quando você seleciona um alerta, o **detalhes de alerta** página é exibida. Esta página fornece detalhes do alerta e permite que você altere seu estado.

Para saber mais sobre como gerenciar alertas, confira [Criar, exibir e gerenciar alertas usando o Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

>[!NOTE]
>Atualmente não tem suporte a criação de novos alertas com base em critérios de integridade ou modificação integridade regras de alerta existentes no Azure Monitor no portal.


![Painel de detalhes do alerta para um alerta selecionado](./media/vminsights-health/alert-details-pane-01.png)

Você pode alterar um estado de alerta para um ou vários alertas selecionando-os e, em seguida, selecionando **alterar estado** da **todos os alertas** página no canto superior esquerdo. Selecione um dos estados na **alterar estado de alerta** painel, adicione uma descrição da alteração na **comentário** campo e, em seguida, selecione **Okey** para confirmar as alterações. Quando as informações são verificadas e as alterações são aplicadas, acompanhar o andamento em **notificações** no menu.

### <a name="configure-alerts"></a>Configurar alertas
Você não pode gerenciar determinadas tarefas de gerenciamento de alertas do portal do Azure. Essas tarefas devem ser executadas usando o [API de REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Especificamente:

- Habilitar ou desabilitar um alerta para os critérios de integridade
- Como configurar notificações para alertas de critérios de integridade

Cada exemplo utilize [ARMClient](https://github.com/projectkudu/armclient) em seu computador Windows. Se você não estiver familiarizado com esse método, consulte [usando o ARMClient](../platform/rest-api-walkthrough.md#use-armclient).

#### <a name="enable-or-disable-an-alert-rule"></a>Habilitar ou desabilitar uma regra de alerta

Para habilitar ou desabilitar um alerta para os critérios de integridade específico, a propriedade **alertGeneration** deve ser modificado com um valor de **desabilitado** ou **habilitado**.

Para identificar o *monitorId* para os critérios de integridade específico, o exemplo a seguir mostra como consultar esse valor para os critérios **lógico \ média de disco segundos por transferência**:

1. Em uma janela de terminal, digite **armclient.exe login**. Isso solicita que você entrar no Azure.

2. Digite o seguinte comando para recuperar todos os critérios de integridade ativos em uma VM específica e identificar o valor de *monitorId* propriedade:

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    O exemplo a seguir mostra a saída do *armclient GET* comando. Anote o valor de *MonitorId*. Esse valor é necessário para a próxima etapa, onde devemos especificar a ID dos critérios de integridade e modificar sua propriedade para criar um alerta.

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. Digite o seguinte comando para modificar o *alertGeneration* propriedade:

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Digite o comando GET usado na etapa 2 para verificar se o valor da propriedade é definido como **desabilitado**.

#### <a name="associate-an-action-group-with-health-criteria"></a>Associar um grupo de ação com critérios de integridade

O Azure Monitor de integridade de VMs dá suporte a notificações de SMS e email quando os alertas são gerados de critérios de integridade não íntegro. Para configurar notificações, anote o nome do grupo de ação configurada para enviar notificações por email ou SMS.

>[!NOTE]
>Essa ação deve ser executada em relação a cada VM monitorado que você deseja receber uma notificação para. Ele não se aplica a todas as VMs em um grupo de recursos.

1. Em uma janela de terminal, digite *armclient.exe logon*. Isso solicita que você entrar no Azure.

2. Insira o comando a seguir para associar um grupo de ações a regras de alerta:
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Para verificar se o valor da propriedade **actionGroupResourceIds** com êxito atualizado, digite o seguinte comando:

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    A saída deve parecer com os seguintes critérios:
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>Próximas etapas

- Para identificar as limitações e o desempenho de VM, consulte [desempenho da VM do Azure de modo de exibição](vminsights-performance.md).
- Para saber mais sobre as dependências do aplicativo descoberto, consulte [modo de exibição do Azure Monitor para VMs mapa](vminsights-maps.md).
