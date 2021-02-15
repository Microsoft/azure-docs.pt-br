---
title: Solucionar problemas de alta utilização de CPU para máquinas virtuais do Windows do Azure
description: .
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: mnanda
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: 9971cc26-916f-4e49-83cd-71eca74804f0
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/24/2020
ms.author: mnanda
ms.openlocfilehash: ffac5ac4d1a8143590e1d72aaafc8a02d6ab04ca
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977248"
---
# <a name="troubleshoot-high-cpu-issues-for-azure-windows-virtual-machines"></a>Solucionar problemas de alta utilização de CPU para máquinas virtuais do Windows do Azure

## <a name="summary"></a>Resumo

Os problemas de desempenho ocorrem em diferentes sistemas operacionais ou aplicativos, e cada problema requer uma abordagem exclusiva para solucionar problemas. A maioria desses problemas gira em volta de CPU, memória, rede e e/s (entrada/saída) como locais-chave onde o problema ocorre. Cada uma dessas áreas gera sintomas diferentes (às vezes, simultaneamente) e requer um diagnóstico e uma solução diferentes.

Este artigo aborda problemas de uso de alta CPU que ocorrem em VMs (máquinas virtuais) do Azure que executam o sistema operacional Windows.

### <a name="high-cpu-issues-on-azure-windows-vms"></a>Problemas de alta utilização de CPU em VMs do Windows do Azure

Além dos problemas de latência de rede e e/s, a solução de problemas de CPU e memória requer as mesmas ferramentas e etapas que os servidores locais fazem. Uma das ferramentas que a Microsoft normalmente suporta é o PerfInsights (disponível para Windows e Linux). O PerfInsights pode fornecer um diagnóstico de práticas recomendadas de VM do Azure em um relatório amigável. O PerfInsights também é uma ferramenta de wrapper que pode ajudar a coletar dados Perfmon, Xperf e Netmon, dependendo dos sinalizadores selecionados dentro da ferramenta.

A maioria das suas ferramentas de solução de problemas de desempenho existentes, como PerfMon ou Procmon, que são usadas para servidores locais funcionarão em VMs do Windows do Azure. No entanto, o PerfInsights é explicitamente projetado para VMs do Azure para fornecer mais informações, incluindo práticas recomendadas do Azure, práticas recomendadas de SQL, gráficos de latência de e/s de alta resolução, guias de CPU e memória e assim por diante.

Independentemente de ser executado como User-Mode ou modo kernel, qualquer thread de um processo ativo requer ciclos de CPU para executar o código do qual ele foi criado. Muitos problemas estão diretamente relacionados à carga de trabalho. O tipo de carga de trabalho que existe no servidor orienta o consumo de recursos, incluindo a CPU.

#### <a name="common-factors"></a>Fatores comuns

Os seguintes fatores são comuns em uma situação de alta CPU:

- Uma alteração ou implantação de código recente que se aplica principalmente a aplicativos como Serviços de Informações da Internet (IIS), Microsoft SharePoint, Microsoft SQL Server ou aplicativos de terceiros.

- Uma atualização recente que pode estar relacionada a uma atualização no nível do sistema operacional ou a correções e atualizações cumulativas no nível do aplicativo.

- Uma consulta altera ou índices desatualizados. Os aplicativos da camada de dados SQL Server e Oracle também têm uma otimização do plano de consulta como outro fator. As alterações de dados ou a falta de índices apropriados podem fazer com que várias consultas se tornem mais intensivas de computação.

- Específico da VM do Azure. Há certos processos, como RDAgent, e processos específicos de extensão, como agente de monitoramento, agente MMA ou cliente de segurança, que podem causar consumo de alta CPU. Esses processos devem ser exibidos de uma perspectiva de configuração ou de problemas conhecidos.

## <a name="troubleshoot-the-issue"></a>Solucionar o problema

Este artigo se concentra no isolamento do processo problemático. A análise adicional será específica para o processo que está impulsionando o consumo de alta CPU.

Por exemplo, se o processo for SQL Server (sqlservr.exe), as próximas etapas serão analisar qual consulta estava usando a maioria dos ciclos de CPU em um período de tempo específico.

### <a name="scope-the-issue"></a>Escopo do problema

Aqui estão algumas perguntas a serem feitas quando você solucionar o problema:

- Há um padrão para o problema? Por exemplo, o problema de alta CPU ocorre em uma determinada hora todos os dias, semana ou mês? Nesse caso, você pode correlacionar esse problema a um trabalho, relatório ou logon de usuário?

- O problema de alta CPU foi iniciado após uma alteração de código recente? Você aplicou uma atualização no Windows ou em um aplicativo?

- O problema de alta CPU foi iniciado após uma alteração na carga de trabalho, como um aumento no número de usuários, um influxo de dados maior ou um número maior de relatórios?

- Para o Azure, o problema de alta CPU foi iniciado em qualquer uma das seguintes condições?

  - Após uma reimplantação ou reinicialização recente
  - Quando um tipo de SKU ou VM é alterado
  - Quando uma nova extensão foi adicionada
  - Após as alterações do balanceador de carga serem feitas

### <a name="azure-caveats"></a>Limitações do Azure

Compreenda sua carga de trabalho. Ao selecionar uma VM, você pode subestimar as contagens de CPU virtual (vCPU) ao examinar o custo geral de hospedagem mensal. Se sua carga de trabalho for de computação intensiva, selecionar um SKU de VM menor que tenha um ou dois vCPUs poderá causar problemas de carga de trabalho. Teste configurações diferentes para sua carga de trabalho para determinar o melhor recurso de computação necessário.

Há certas séries de VMs, como a série B (modo de intermitência), que são recomendadas para controle de qualidade (QA) e teste. O uso dessas séries no ambiente de produção limita a capacidade de computação depois que os créditos da CPU são esgotados.

Para aplicativos conhecidos como SQL Server, Oracle, RDS (Serviços de Área de Trabalho Remota), área de trabalho virtual do Windows, IIS ou SharePoint, há artigos de práticas recomendadas do Azure que incluem recomendações para configuração mínima para essas cargas de trabalho.

### <a name="ongoing-high-cpu-issues"></a>Problemas de alta CPU em andamento

Se o problema estiver ocorrendo no momento, essa é a melhor oportunidade de capturar o rastreamento de processo para determinar o que está causando o problema. Você pode usar as ferramentas existentes que você esteve usando para servidores Windows locais para localizar o processo. As ferramentas a seguir são recomendadas pelo suporte do Azure para VMs do Azure.

### <a name="perfinsights"></a>PerfInsights

PerfInsights é a ferramenta recomendada do suporte do Azure para problemas de desempenho de VM. Ele foi projetado para abordar as práticas recomendadas e guias de análise dedicadas para gráficos de CPU, memória e e/s de alta resolução. Você pode executá-lo OnDemand por meio do portal do Azure ou de dentro da VM. Você pode compartilhar os dados com a equipe de suporte do Azure.

#### <a name="run-perfinsights"></a>Executar PerfInsights

O PerfInsights está disponível para o sistema operacional [Windows](./how-to-use-perfinsights.md) e [Linux](./how-to-use-perfinsights-linux.md) . Para o Windows, aqui estão as opções.

#### <a name="run-and-analyze-reports-through-azure-portal"></a>Executar e analisar relatórios por meio de portal do Azure

Quando [instalado por meio da portal do Azure](./performance-diagnostics.md), na verdade, instala uma extensão na VM. Os usuários também podem instalar o PerfInsights como uma extensão indo diretamente para [extensões na folha da VM](./performance-diagnostics-vm-extension.md)e, em seguida, escolhendo uma opção de diagnóstico de desempenho.

#### <a name="azure-portal-option-1"></a>Opção de portal do Azure 1

Procure a folha da VM e selecione a opção **diagnóstico de desempenho** . Você será solicitado a instalar a opção (usa extensões) na VM para a qual você a selecionou.

  ![Instalar o diagnóstico de desempenho](./media/troubleshoot-high-cpu-issues-azure-windows-vm/1-install-performance-diagnostics.png)

#### <a name="azure-portal-option-2"></a>Opção de portal do Azure 2

Navegue para **diagnosticar e resolver problemas** na folha da VM e procure **problemas de desempenho da VM**.

  ![Solucionar problemas de desempenho de VM](./media/troubleshoot-high-cpu-issues-azure-windows-vm/2-troubleshoot-vm-performance-issues.png)

Se você selecionar **solucionar problemas**, a tela de instalação do PerfInsights será carregada.

Se você selecionar **instalar**, a instalação fornecerá opções de coleção diferentes.

  ![Análise de desempenho](./media/troubleshoot-high-cpu-issues-azure-windows-vm/3-performance-analysis.png)

As opções numeradas na captura de tela estão relacionadas aos seguintes comentários:

1. Para a opção de **alta CPU**, selecione **análise de desempenho** ou **avançado**.

2. Quando você adicionar sintomas aqui, eles serão adicionados ao relatório, o que ajuda você a compartilhar informações com o suporte do Azure.

3. Selecione a duração da coleta de dados. Para a opção de alta CPU, selecione pelo menos 15 minutos ou mais. No modo de portal do Azure, você pode coletar até 15 minutos de dados. Para períodos mais longos de coleta, você deve executar o programa como um executável na VM.

4. Se for solicitado pelo suporte do Azure para coletar esses dados, você poderá adicionar o número do tíquete aqui. Esse campo é opcional.

5. Selecione este campo para aceitar o contrato de licença de usuário final (EULA).

6. Selecione este campo se você pretende tornar esse relatório disponível para a equipe de suporte do Azure ajudando nesse caso.

O relatório é armazenado em uma das contas de armazenamento em sua assinatura. Ele está disponível para exibição e download mais tarde.

#### <a name="run-perfinsights-from-within-the-vm"></a>Executar PerfInsights de dentro da VM

Esse método pode ser usado se você pretende executar PerfInsights por durações mais longas. O [artigo PerfInsights](./how-to-use-perfinsights.md#how-do-i-run-perfinsights) fornece uma explicação detalhada dos diferentes comandos e sinalizadores necessários para executar o PerfInsights como um executável. Para fins de uso de alta utilização de CPU, você precisará de um dos seguintes modos:

- Cenário avançado

  - `PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics`

- Cenário de VM lenta (desempenho)

  - `PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>`

A saída do comando estará na mesma pasta em que você salvou o executável PerfInsights.

#### <a name="what-to-look-for-in-the-report"></a>O que procurar no relatório

Depois de executar o relatório, o local do conteúdo depende de se ele foi executado por meio da portal do Azure ou como um executável. Para qualquer opção, acesse a pasta de log gerada ou baixe (se portal do Azure) localmente para análise.

### <a name="run-through-the-azure-portal"></a>Executar o portal do Azure

  ![Diagnóstico de desempenho de alto impacto](./media/troubleshoot-high-cpu-issues-azure-windows-vm/4-high-impact-performance-diagnostics.png)

  ![Baixar relatório](./media/troubleshoot-high-cpu-issues-azure-windows-vm/5-download-report.png)

#### <a name="run-from-within-vm"></a>Executar de dentro da VM

Sua estrutura de pastas deve se parecer com as seguintes imagens:

  ![Selecionar saída](./media/troubleshoot-high-cpu-issues-azure-windows-vm/6-select-output.png)

  ![Estrutura de pastas](./media/troubleshoot-high-cpu-issues-azure-windows-vm/7-folder-structure.png)

1. Todas as coleções adicionais, como PerfMon, XPerf, Netmon, logs SMB, logs de eventos e assim por diante, podem ser encontradas na pasta de saída.

1. O relatório real junto com a análise e as recomendações.

1. Para desempenho (VMslow) e avançado, o relatório coleta informações de **Perfmon** pela duração da execução do PerfInsights.

1. Os logs de eventos mostram uma exibição rápida de detalhes úteis de falha no nível do sistema ou do processo.

#### <a name="where-to-start"></a>Onde começar

Abra o relatório PerfInsights. A guia **conclusões** registra as exceções em termos de consumo de recursos. Se houver instâncias de uso de alta utilização de CPU, a guia **conclusões** irá categorizá-la como impacto alto ou impacto médio.

  ![Recursos de nível de impacto](./media/troubleshoot-high-cpu-issues-azure-windows-vm/8-impact-level-resources.png)

Semelhante ao exemplo anterior, PerfInsights foi executado por 30 minutos. Por metade desse tempo, o processo realçado estava esgotando a CPU no lado superior. Se o mesmo processo estava em execução durante o tempo de coleta, o nível de impacto teria mudado para **alto**.

Se você expandir o evento de **conclusões** , verá vários detalhes importantes. A guia lista os processos em ordem decrescente, por média de consumo de **CPU** e mostra se o processo foi relacionado ao sistema, a um aplicativo de propriedade da Microsoft (SQL, IIS) ou a um processo de terceiros.

#### <a name="more-details"></a>Mais detalhes

Há um subtab dedicado sob **CPU** que pode ser usado para análise de padrão detalhada, por núcleo ou por processo.

A guia **principais consumidores de CPU** tem duas seções separadas de interesse e você pode exibir as estatísticas por processador aqui. O design do aplicativo frequentemente é Single-Threaded ou se fixa a um único processador. Nesse cenário, um ou alguns núcleos são executados às 100 por cento, enquanto outros núcleos são executados nos níveis esperados. Esses cenários são mais complexos porque a média de CPU no servidor parece ser executada conforme o esperado, mas os processos que são fixados em núcleos com alto uso serão mais lentos do que o esperado.

  ![uso de alta utilização de CPU](./media/troubleshoot-high-cpu-issues-azure-windows-vm/9-high-cpu-usage.png)

A segunda seção (igualmente importante) é a **maior parte dos consumidores de CPU de longa duração**. Esta seção mostra os detalhes do processo e seu padrão de uso da CPU. A lista é classificada com a alta média de consumidores de CPU na parte superior.

  ![Tom de CPU de execução demorada](./media/troubleshoot-high-cpu-issues-azure-windows-vm/10-top-long-running-cpu-consumers.png)

Essas duas guias serão suficientes para definir o caminho para as próximas etapas de solução de problemas. Dependendo do processo que está impulsionando a condição de alta CPU, você precisará abordar as perguntas que foram feitas anteriormente. Processos como SQL Server (sqlservr.exe) ou IIS (w3wp.exe) exigem um detalhamento específico nas alterações de consulta ou código que estão causando essa condição. Para processos do sistema, como WMI ou Lsass.exe, você precisa seguir um caminho diferente.

Para processos relacionados à VM do Azure, como RDAgent, OMS e os executáveis de extensão de monitoramento, talvez seja necessário corrigir uma nova compilação ou versão Obtendo ajuda da equipe de suporte do Azure.

### <a name="perfmon"></a>Perfmon

O **Perfmon** é uma das ferramentas mais recentes para solução de problemas de um problema de recurso no Windows Server. Ele não dá um relatório claro com recomendações ou descobertas. Em vez disso, ele requer que o usuário Explore os dados coletados e use um filtro específico nas diferentes categorias de contador.

O PerfInsights coleta o PerfMon como um log extra para cenários VMSlow e avançados. No entanto, o PerfMon pode ser coletado de forma independente e tem estes benefícios adicionais:

- Ele pode ser coletado remotamente.

- Ele pode ser agendado por meio de **tarefas**.

- Ele pode ser coletado por durações mais longas ou em modo contínuo usando o recurso de lançamento.

Considere o mesmo exemplo mostrado em PerfInsights para ver como o PerfMon mostra esses dados. As categorias de contador necessárias são as seguintes:

- Informações do processador >% tempo de processador > _Total

- Processar >% Processortime > todas as instâncias

#### <a name="where-to-start"></a>Onde começar

Os nomes de arquivo de saída do Perfmon têm uma `.blg` extensão. Você pode coletar esses arquivos independentemente ou usando PerfInsights. Para esta discussão, você usará o PerfMon `.blg` que está incluído nos dados PerfInsights e que foram coletados de acordo com o exemplo anterior.

Não há relatórios padrão prontos para o usuário disponíveis no perfmon. Há diferentes exibições que alteram o tipo de grafo, mas o processo filtragem (ou o trabalho necessário para identificar os processos culpado) é manual.

> [!NOTE]
> A [ferramenta PAL](https://github.com/clinthuffman/PAL) pode consumir `.blg` arquivos e gerar relatórios detalhados.

Para iniciar, selecione a categoria **Adicionar contadores** .

1. Em **contadores disponíveis**, selecione o contador **% Processortime** na categoria **informações do processador** .

1. Selecione **_Total**, que fornece as estatísticas de todos os núcleos combinados.

1. Selecione **Adicionar**. A janela mostra **% processortime** em  **contadores adicionados**.

  ![Adicionar tempo do processador](./media/troubleshoot-high-cpu-issues-azure-windows-vm/11-add-processor-time.png)

Depois que os contadores forem carregados, você verá gráficos de tendência de linha no período de coleta. Você pode selecionar ou limpar os contadores. Até agora, você adicionou apenas um contador.

  ![Configurações do monitor de desempenho](./media/troubleshoot-high-cpu-issues-azure-windows-vm/12-performance-monitor-1.png)

Cada contador terá os valores **médio**, **mínimo**e **máximo** . Concentre-se nos valores **médio** e **máximo** , pois o valor médio pode variar dependendo da duração da coleta de dados. Se a atividade de alta CPU tiver sido vista por 10 minutos enquanto a coleção geral tiver 40 minutos, os valores médios serão muito menores.

O grafo de tendência anterior mostra que o **processador total** estava chegando perto de 80% por aproximadamente 15 minutos.

#### <a name="identify-the-process"></a>Identificar o processo

Identificamos que o servidor tinha alto consumo de CPU por um período de tempo especificado, mas ainda não identificamos o driver. Ao contrário do uso de PerfInsights, você deve pesquisar manualmente o processo culpado nesse caso.

Para essa tarefa, você deve limpar ou remover os contadores **% processortime** adicionados anteriormente e, em seguida, adicionar uma nova categoria:

- Processar >% Processortime > todas as instâncias

Essa categoria carregará contadores para todos os processos em execução naquele momento.

  ![Adicionar contadores](./media/troubleshoot-high-cpu-issues-azure-windows-vm/13-add-counters.png)

Em um computador de produção típico, centenas ou processos podem ser executados. Portanto, pode levar algum tempo para limpar todos os contadores que parecem ter um gráfico de tendência baixo ou simples.

Para acelerar esse processo, use o modo de exibição de **histograma** e altere o tipo de exibição de **linha** para **histograma**, que lhe dará um gráfico de barras. Você descobrirá que é mais fácil escolher os processos que apresentam uso de alta utilização de CPU durante o tempo de coleta.

Como sempre haverá uma barra para o **total**, concentre-se nas barras que mostram uma alta taxa de esgotamento. Você pode excluir as outras barras para limpar o modo de exibição. Agora, mude de volta para a exibição de **linha** .

  ![Indicadores do monitor de desempenho](./media/troubleshoot-high-cpu-issues-azure-windows-vm/14-performance-monitor-2.png)

Agora é mais fácil detectar o processo culpado. Por padrão, os valores **Max** e **min** são múltiplos do número de núcleos no servidor ou threads do processo.

  ![Resultados do monitor de desempenho](./media/troubleshoot-high-cpu-issues-azure-windows-vm/15-performance-monitor-3.png)

A lista de ferramentas disponíveis não termina em PerfInsights para Perfmon. Você tem acesso a outras ferramentas, como **ProcessMonitor** (ProcMon) ou **Xperf**. Há muitas ferramentas de terceiros disponíveis para uso, conforme necessário.

### <a name="azure-monitoring-tools"></a>Ferramentas de monitoramento do Azure

As VMs do Azure têm métricas confiáveis que incluem informações básicas, como CPU, e/s de rede e bytes de e/s. Para métricas avançadas, como Azure Monitor, você terá que fazer apenas algumas seleções para configurar e usar uma conta de armazenamento que você especificar.

#### <a name="basic-default-counters"></a>Contadores básicos (padrão)

  ![Métricas do gráfico](./media/troubleshoot-high-cpu-issues-azure-windows-vm/16-chart-metrics.png)

#### <a name="enabling-azure-monitor"></a>Habilitando Azure Monitor

Depois de habilitar Azure Monitor métricas, o software instala uma extensão na VM e, em seguida, inicia a coleta de métricas granulares, que inclui contadores Perfmon.

  ![Conta de armazenamento de diagnóstico](./media/troubleshoot-high-cpu-issues-azure-windows-vm/17-diagnostics-storage-account.png)

As categorias de contador **básicas** são definidas como **padrão**. No entanto, você também pode definir uma coleção **personalizada** .

  ![Contadores de desempenho](./media/troubleshoot-high-cpu-issues-azure-windows-vm/18-performance-counters.png)

Depois que as configurações estiverem habilitadas, você poderá exibir esses contadores **convidados** na seção **métricas** . Você também pode definir **alertas** (incluindo mensagens de email) se as métricas atingirem um determinado limite.

  ![Namespace de métricas](./media/troubleshoot-high-cpu-issues-azure-windows-vm/19-metrics-namespace.png)

Para obter mais informações sobre como usar o Azure monitor para gerenciar VMs do Azure, consulte [monitorando máquinas virtuais do Azure com Azure monitor](../../azure-monitor/insights/monitor-vm-azure.md).

### <a name="reactive-troubleshooting"></a>Solução de problemas reativa

Se o problema já tiver ocorrido, você deverá descobrir o que causou o problema de alta CPU em primeiro lugar. A postura reativa pode ser complicada. O modo de coleta de dados não será tão útil porque o problema já ocorreu.

Se esse problema fosse uma ocorrência única, pode ser difícil determinar qual aplicativo o causou. Se a VM do Azure foi configurada para usar o OMS ou outro controle de diagnóstico, você ainda poderá obter informações sobre o que causou o problema.

Se você estiver lidando com um padrão de repetição, colete os dados durante o tempo que o problema provavelmente irá ocorrer em seguida.

PerfInsights ainda não tem um recurso de **execução agendado** . No entanto, o PerfMon pode ser executado e agendado por meio da linha de comando.

### <a name="logman-command"></a>Comando logman

O comando do **contador Create do logman** é usado para executar a coleta Perfmon por meio da linha de comando, para agendá-lo por meio do **Gerenciador de tarefas**ou para executá-lo remotamente.

**Exemplo** (inclui o modo de coleta remota)

`Logman create counter LOGNAME -u DOMAIN\USERNAME * -f bincirc -v mmddhhmm -max 300 -c "\\SERVERNAME\LogicalDisk(*)\*" "\\SERVERNAME\Memory\*" "\\SERVERNAME\Network Interface(*)\*" "\\SERVERNAME\Paging File(*)\*" "\\SERVERNAME\PhysicalDisk(*)\*" "\\SERVERNAME\Process(*)\*" "\\SERVERNAME\Redirector\*" "\\SERVERNAME\Server\*" "\\SERVERNAME\System\*" "\\SERVERNAME\Terminal Services\*" "\\SERVERNAME\Processor(*)\*" "\\SERVERNAME\Cache\*" -si 00:01:00`

Logman.exe também pode ser iniciado em um computador de VM do Azure par na mesma VNET.

Para saber mais sobre esses parâmetros, consulte [logman Create Counter](/windows-server/administration/windows-commands/logman-create-counter).

Depois que os dados de Perfmon são coletados enquanto o problema está ocorrendo, as etapas restantes para analisar os dados são as mesmas descritas anteriormente.

## <a name="conclusion"></a>Conclusão

Para qualquer problema de desempenho, compreender sua carga de trabalho é a chave para resolver o problema. As opções em SKUs de VM diferentes e opções de armazenamento em disco diferentes devem ser avaliadas mantendo o foco na carga de trabalho de produção. O processo de testar soluções em VMs diferentes pode ajudá-lo a tomar a melhor decisão.

Como as operações de usuário e a quantidade de dados variam, sempre mantenha um buffer na computação, na rede e nos recursos de e/s da VM. Agora, qualquer alteração repentina na carga de trabalho não tem um efeito muito grande.

Se você prever a carga de trabalho aumentando em breve, vá para uma SKU superior com mais capacidade computacional. Se a carga de trabalho for de computação intensiva, escolha os SKUs da VM com sabedoria.