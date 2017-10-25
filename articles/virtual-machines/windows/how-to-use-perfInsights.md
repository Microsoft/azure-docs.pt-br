---
title: Como usar PerfInsights no Microsoft Azure | Microsoft Docs
description: Aprenda a usar o PerfInsights para solucionar problemas de desempenho da VM do Windows.
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/18/2017
ms.author: genli
ms.openlocfilehash: 8d66bbdf6f7153cf59af60051e54377f6eccdc3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-perfinsights"></a>Como usar o PerfInsights 

O [PerfInsights](http://aka.ms/perfinsightsdownload) é um script automatizado que coleta informações de diagnóstico úteis, executa cargas de estresse de E/S e fornece um relatório de análise para ajudar a solucionar problemas de desempenho de VM do Windows no Microsoft Azure. Isso pode ser executado nas máquinas virtuais como um script autônomo ou diretamente do Portal instalando a [Extensão de VM de Diagnóstico de Desempenho do Azure](performance-diagnostics-vm-extension.md).

É recomendável executar esse script antes de abrir um chamado de suporte com a Microsoft para problemas de desempenho da VM.

## <a name="supported-troubleshooting-scenarios"></a>Cenários de solução de problemas com suporte

O PerfInsights pode coletar e analisar vários tipos de informações que são agrupadas em cenários exclusivos.

### <a name="collect-disk-configuration"></a>Coletar configuração de disco 

Esse cenário coleta a configuração do disco e outras informações importantes, incluindo os seguintes itens:

-   Logs de eventos

-   Status da rede para todas as conexões de entrada e saída

-   Definições de configuração de firewall e rede

-   Lista de tarefas para todos os aplicativos que estão sendo executados no sistema no momento

-   Arquivo de informações criado pelo msinfo32 para a VM (máquina virtual)

-   Definições de configuração do banco de dados Microsoft SQL Server (se a VM for identificada como um servidor que execute o SQL Server)

-   Contadores de confiabilidade do armazenamento

-   Hotfixes importantes do Windows

-   Drivers de filtro instalados

Esta é uma coleção de passiva de informações que não devem afetar o sistema. 

>[!Note]
>Esse cenário é incluído automaticamente em cada um dos cenários a seguir.

### <a name="benchmarkstorage-performance-test"></a>Teste de desempenho de armazenamento/parâmetro de comparação

Este cenário executa o teste de parâmetro de comparação [diskspd](https://github.com/Microsoft/diskspd) (IOPS e MBPS) para todas as unidades anexadas à VM. 

> [!Note]
> Esse cenário pode afetar o sistema e não deve ser executado em um sistema de produção ativo. Se necessário, execute este cenário em uma janela de manutenção dedicada para evitar problemas. Uma carga de trabalho maior causada por um teste de parâmetro de comparação ou rastreamento pode afetar adversamente o desempenho da VM.
>

### <a name="general-vm-slow-analysis"></a>Análise geral de VM lenta 

Este cenário executa um rastreamento de [contador de desempenho](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) usando os contadores especificados no arquivo Generalcounters.txt. Se a VM for identificada como um servidor que executa o SQL Server, ela executará um rastreamento do contador de desempenho usando os contadores encontrados no arquivo Sqlcounters.txt. Também inclui dados de Diagnóstico de Desempenho.

### <a name="vm-slow-analysis-and-benchmark"></a>Parâmetro de comparação e análise de VM lenta

Este cenário executa um rastreamento do [contador de desempenho](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) que é seguido pelo teste de parâmetro de comparação [diskspd](https://github.com/Microsoft/diskspd). 

> [!Note]
> Esse cenário pode afetar o sistema e não deve ser executado em um sistema de produção ativo. Se necessário, execute este cenário em uma janela de manutenção dedicada para evitar problemas. Uma carga de trabalho maior causada por um teste de parâmetro de comparação ou rastreamento pode afetar adversamente o desempenho da VM.
>

### <a name="azure-files-analysis"></a>Análise de Arquivos do Azure 

Este cenário executa uma captura de contador de desempenho especial junto com um rastreamento de rede. A captura inclui todos os contadores de "Compartilhamentos de Cliente SMB". A seguir estão alguns contadores de desempenho de compartilhamento de cliente SMB que fazem parte da captura:

| **Tipo**     | **Contador de compartilhamentos de cliente SMB** |
|--------------|-------------------------------|
| IOPS         | Solicitações de dados/s             |
|              | Solicitações de leitura/s             |
|              | Solicitações de gravação/s            |
| Latência      | Média de solicitação de dados/s         |
|              | Média de leitura/s                 |
|              | Média de gravação/s                |
| Tamanho de E/S      | Média Bytes/solicitação de dados       |
|              | Média Bytes/leitura               |
|              | Média Bytes/gravação              |
| Taxa de transferência   | Bytes de dados/s                |
|              | Bytes de leitura/s                |
|              | Bytes de gravação/s               |
| Comprimento da fila | Média Tamanho da fila de leitura        |
|              | Média Tamanho da fila de gravação       |
|              | Média Comprimento da fila de dados        |

### <a name="custom-configuration"></a>Configuração personalizada 

Quando você executa uma configuração personalizada, está executando todos os rastreamentos (diagnóstico de desempenho, contador de desempenho, xperf, rede, storport) em paralelo, dependendo de quantos rastreamentos diferentes são selecionados. Após o rastreamento estar concluído, a ferramenta executará o parâmetro de comparação diskspd, se ele estiver selecionado. 

> [!Note]
> Esse cenário pode afetar o sistema e não deve ser executado em um sistema de produção ativo. Se necessário, execute este cenário em uma janela de manutenção dedicada para evitar problemas. Uma carga de trabalho maior causada por um teste de parâmetro de comparação ou rastreamento pode afetar adversamente o desempenho da VM.
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>Que tipo de informações é coletado pelo script?

Informações sobre VM do Windows, configuração de discos ou de pools de armazenamento, contadores de desempenho, logs e vários rastreamentos são coletadas dependendo do cenário de desempenho usado:

|Dados coletados                              |  |  | Cenários de desempenho |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | Coletar Configuração de Disco | Teste de desempenho de armazenamento/parâmetro de comparação | Análise geral de VM lenta | Parâmetro de comparação e análise de VM lenta | Análise de Arquivos do Azure | Configuração personalizada |
| Informações de logs de eventos      | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Informações do sistema               | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Mapa de volume                       | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Mapa do disco                         | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Tarefas em execução                    | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Contadores de Confiabilidade do Armazenamento     | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Informações de armazenamento              | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Saída do fsutil                    | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Informações do driver de filtro               | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Saída de Netstat                   | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Configuração de rede            | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Configuração do firewall           | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Configuração do SQL Server         | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Rastreamentos de diagnóstico de desempenho * |                            |                                    | Sim                      |                                |                      | Sim                  |
| Rastreamento do contador de desempenho **     |                            |                                    |                          |                                |                      | Sim                  |
| Rastreamento do contador SMB **             |                            |                                    |                          |                                | Sim                  |                      |
| Rastreamento do contador do SQL Server **      |                            |                                    |                          |                                |                      | Sim                  |
| Rastreamento de XPerf                      |                            |                                    |                          |                                |                      | Sim                  |
| Rastreamento de StorPort                   |                            |                                    |                          |                                |                      | Sim                  |
| Rastreamento de rede                    |                            |                                    |                          |                                | Sim                  | Sim                  |
| Rastreamento de parâmetro de comparação de Diskspd ***      |                            | Sim                                |                          | Sim                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Rastreamento de diagnóstico de desempenho (*)

Executa um mecanismo baseado em regras no segundo plano para coletar dados e diagnosticar problemas de desempenho em andamento. No momento, há suporte para regras a seguir:

- Regra de HighCpuUsage: detecta períodos de uso elevado de CPU e mostra os principais consumidores de uso da CPU durante esses períodos.
- Regra de HighDiskUsage: detecta períodos de uso elevado de disco em discos físicos e mostra os principais consumidores de uso de disco durante esses períodos.
- Regra de HighResolutionDiskMetric: mostra IOPS, taxa de transferência e métricas de latência de E/S por 50 milissegundos para cada disco físico. Isso ajuda a identificar rapidamente os períodos de limitação de disco.
- Regra de HighMemoryUsage: detecta os períodos de uso elevado de memória e mostra os principais consumidores de uso de memória durante esses períodos.

> [!NOTE] 
> Atualmente, há suporte para versões do Windows que incluem .NET Framework 3.5 ou versões posteriores.

### <a name="performance-counter-trace-"></a>Rastreamento do contador de desempenho (**)

Coleta os seguintes contadores de desempenho:

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk, \LogicalDisk
- \Cache\Dirty Pages, \Cache\Lazy Write Flushes/sec, \Server\Pool Nonpaged, Failures, \Server\Pool Paged Failures
- Contadores selecionados em \Network Interface, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments,  \Network Adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \Network QoS Policy\Packets, \Per Processor Network Interface Card Activity, \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>Para instâncias do SQL Server
- \SQL Server:Buffer Manager, \SQLServer:Resource Pool Stats, \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General, Statistics
- \SQLServer:Access Methods

#### <a name="for-azure-files"></a>Para Arquivos do Azure
\SMB Client Shares

### <a name="diskspd-benchmark-trace-"></a>Rastreamento de parâmetro de comparação de Diskspd (***)
Testes de carga de trabalho de E/S do Diskspd [disco do SO (gravação) e unidades de pool (leitura/gravação)]

## <a name="run-the-perfinsights-on-your-vm"></a>Execute o PerfInsights na sua VM

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>O que eu preciso saber antes de executar o script? 

**Requisitos de script**

1.  Esse script deve ser executado na VM que tem o problema de desempenho. 

2.  Há suporte para os SOs a seguir: Windows Server 2008 R2, 2012, 2012 R2, 2016; Windows 8.1 e Windows 10.

**Possíveis problemas ao executar o script em VMs de produção:**

1.  O script pode afetar negativamente o desempenho da VM quando ela for usada junto com o cenário de "Parâmetro de Comparação" ou "Personalizado" configurado usando XPerf ou DiskSpd. Tenha cuidado ao executar o script em um ambiente de produção.

2.  Quando você usar o script junto com o cenário "Parâmetro de Comparação" ou "Personalizado" configurado usando o DiskSpd, certifique-se de que nenhuma outra atividade em segundo plano interfira na carga de trabalho de E/S nos discos testados.

3.  Por padrão, o script usa a unidade de armazenamento temporário para coletar dados. Se o rastreamento permanecer habilitado por mais tempo, a quantidade de dados coletados poderá ser relevante. Isso pode reduzir a disponibilidade de espaço no disco temporário, afetando, portanto, qualquer aplicativo que dependa dessa unidade.

### <a name="how-do-i-run-perfinsights"></a>Como fazer para executar o PerfInsights? 

Você pode executar o PerfInsights em uma máquina virtual instalando a [Extensão de VM de Diagnóstico de Desempenho do Azure](performance-diagnostics-vm-extension.md) ou executá-lo como um script autônomo. 

**Instalar e executar o PerfInsights do Portal do Azure**

O PerfInsights agora pode ser executado usando uma extensão de VM chamada de Extensão de Diagnóstico de Desempenho do Azure. Para obter mais informações, consulte [Instalar Extensão de Diagnóstico de Desempenho do Azure](performance-diagnostics-vm-extension.md#install-the-extension).  

**Executar o script do PerfInsights no modo autônomo**

Para executar o script do PerfInsights, siga estas etapas:


1. Baixe [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. Desbloqueie o arquivo PerfInsights.zip. Para fazer isso, clique com o botão direito do mouse no arquivo PerfInsights.zip e selecione **Propriedades**. Na guia **Geral**, selecione **Desbloquear** e, em seguida, selecione **OK**. Isso garantirá que o script seja executado sem prompts de segurança adicionais.  

    ![Desbloquear o arquivo zip](media/how-to-use-perfInsights/unlock-file.png)

3.  Expanda o arquivo PerfInsights.zip compactado na unidade temporária (por padrão, costuma ser a unidade D). O arquivo compactado deve conter os seguintes arquivos e pastas:

    ![arquivos na pasta zip](media/how-to-use-perfInsights/file-folder.png)

4.  Abra o Windows PowerShell como administrador e execute o script PerfInsights.ps1.

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    Pode ser necessário digitar "y" se você for solicitado a confirmar se deseja alterar a política de execução.

    Na caixa de diálogo Aviso de Isenção de Responsabilidade, você terá a opção de compartilhar informações de diagnóstico com o Suporte da Microsoft. Você também deve concordar com o contrato de licença para continuar. Faça suas seleções e, em seguida, clique em **Executar Script**.

    ![Caixa de diálogo Aviso de Isenção de Responsabilidade](media/how-to-use-perfInsights/disclaimer.png)

5.  Envie o número do caso, se ele estiver disponível, ao executar o script (isso é para nossas estatísticas). Em seguida, clique em **OK**.
    
    ![inserir ID de suporte](media/how-to-use-perfInsights/enter-support-number.png)

6.  Selecione a unidade de armazenamento temporário. O Script pode detectar automaticamente a letra da unidade. Se houver problemas nesse estágio, você poderá ser solicitado a selecionar a unidade (a unidade padrão é D). Os logs gerados são armazenados aqui na pasta log\_collection. Depois que você insere ou aceita a letra da unidade, clique em **OK**.

    ![inerir unidade](media/how-to-use-perfInsights/enter-drive.png)

7.  Selecione um cenário de solução de problemas na lista fornecida.

       ![Selecionar cenários de suporte](media/how-to-use-perfInsights/select-scenarios.png)

8.  Você também pode executar PerfInsights sem interface do usuário.

    O comando a seguir executa o cenário de solução de problemas “Análise geral de VM lenta” sem um aviso de interface do usuário ou captura dados por 30 segundos. Ele solicita que você concorde com o mesmo aviso de isenção de responsabilidade e EULA mencionados na etapa 4.

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

    Se você quiser que o PerfInsights seja executado no modo sem confirmação, use o parâmetro **-AcceptDisclaimerAndShareDiagnostics**. Por exemplo, use o seguinte comando:

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>Como fazer para solucionar os problemas durante a execução do script?

Se o script terminar de maneira anormal, você poderá limpar um estado divergente executando o script em conjunto com a opção -Cleanup da seguinte maneira:

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

Se houver problemas durante a detecção automática da unidade temporária, você poderá ser solicitado a selecionar a unidade (a unidade padrão é a D).

![enter-drive](media/how-to-use-perfInsights/enter-drive.png)

O script desinstala as ferramentas do utilitário e remove as pastas temporárias.

### <a name="troubleshooting-other-script-issues"></a>Solução de outros problemas de script 

Se ocorrerem problemas quando você executar o script, pressione Ctrl+C para interromper a execução do script. Para remover objetos temporários, consulte a seção "Limpeza após o encerramento anormal".

Se você continuar a encontrar a falha de script mesmo após várias tentativas, é recomendável executar o script no "modo de depuração" usando a opção de parâmetro "-Debug" na inicialização.

Após a falha ocorrer, copie a saída completa do console do PowerShell e envie-a ao agente de Suporte da Microsoft que o está ajudando a solucionar o problema.

### <a name="how-do-i-run-the-script-in-custom-configuration-mode"></a>Como fazer para executar o script no modo de configuração personalizada?

Ao selecionar a configuração **Personalizado**, você pode habilitar vários rastreamentos em paralelo (use Shift para selecionar vários):

![selecionar cenários](media/how-to-use-perfInsights/select-scenario.png)

Quando você selecionar o cenário Diagnóstico de Desempenho, Rastreamento do Contador de Desempenho, Rastreamento de XPerf, Rastreamento de Rede ou Rastreamento de Storport, siga as instruções nas caixas de diálogo e tente reproduzir o problema de desempenho lento depois de iniciar os rastreamentos.

A caixa de diálogo a seguir permite que você inicie um rastreamento:

![start-trace](media/how-to-use-perfInsights/start-trace-message.png)

Para interromper os rastreamentos, você precisa confirmar o comando em uma segunda caixa de diálogo.

![stop-trace](media/how-to-use-perfInsights/stop-trace-message.png)
![stop-trace](media/how-to-use-perfInsights/ok-trace-message.png)

Quando os rastreamentos ou as operações forem concluídos, um novo arquivo será gerado em D:\\log\_collection (ou unidade temporária) com o nome **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip.** Você pode enviar esse arquivo para o agente de Suporte para análise.

## <a name="review-the-diagnostics-report-created-by-perfinsights"></a>Examine o relatório de diagnóstico criado pelo PerfInsights

No arquivo **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip,** que é gerado pelo PerfInsights, você pode localizar um relatório HTML com detalhes sobre as descobertas do PerfInsights. Para examinar o relatório, expanda o arquivo **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** e, em seguida, abra o arquivo **PerfInsights Report.html**.

Selecione a guia **Descobertas**.

![guia localizar](media/how-to-use-perfInsights/findingtab.png)

**Observações**

-   Mensagens em vermelho são problemas de configuração conhecidos que podem causar problemas de desempenho.

-   Mensagens em amarelo são avisos que representam configurações não ideais que não necessariamente causam problemas de desempenho.

-   As mensagens em azul são somente instruções informativas.

Examine os links HTTP para todas as mensagens de erro em vermelho para obter informações mais detalhadas sobre as descobertas e como elas podem afetar o desempenho ou as práticas recomendadas para configurações otimizadas para desempenho.

### <a name="disk-configuration-tab"></a>Guia Configuração de Disco

A seção **Visão Geral** exibe as exibições da configuração de armazenamento, incluindo informações de Diskpart e Espaços de Armazenamento

As seções **DiskMap** e **VolumeMap** descrevem em uma perspectiva dupla como volumes lógicos e discos físicos estão relacionados uns aos outros.

Na perspectiva PhysicalDisk (DiskMap), a tabela mostra todos os volumes lógicos que estão em execução no disco. No exemplo a seguir, PhysicalDrive2 executa dois Volumes Lógicos criados em várias partições (J e H):

![guia dados](media/how-to-use-perfInsights/disktab.png)

Na perspectiva Volume (*VolumeMap*), as tabelas mostram todos os discos físicos em cada volume lógico. Observe que, para discos RAID/Dinâmicos, você pode executar um volume lógico em vários discos físicos. No exemplo a seguir *C:\\mount* é configurado como um ponto de montagem como *SpannedDisk* em PhysicalDisks \#2 e \#3:

![Guia de volume](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-server-tab"></a>Guia SQL Server

Se a VM de destino hospedar instâncias do SQL Server, você verá uma guia adicional no relatório chamada **SQL Server**:

![guia sql](media/how-to-use-perfInsights/sqltab.png)

Esta seção contém uma "visão geral" e subguias adicionais para cada uma das instâncias do SQL Server hospedadas na VM.

A seção "Visão Geral" contém uma tabela útil que resume todos os discos físicos (discos de sistema e dados) que estão em execução e que contêm uma mistura de arquivos de dados e arquivos de log de transações.

No exemplo a seguir, *PhysicalDrive0* (executando a unidade C) é exibida porque os arquivos *modeldev* e *modellog* estão ambos localizados na unidade C e são de tipos diferentes (como Arquivo de Dados e Log de Transações, respectivamente):

![loginfo](media/how-to-use-perfInsights/loginfo.png)

As guias específicas da instância do SQL Server contêm uma seção geral que exibe informações básicas sobre a instância selecionada e seções adicionais para informações avançadas, incluindo Configurações, Definições e Opções do Usuário.

## <a name="references-to-the-external-tools-used"></a>Referências às ferramentas externas usadas

### <a name="diskspd"></a>Diskspd

DISKSPD é um gerador de carga de armazenamento e uma ferramenta de teste de desempenho do Windows e das equipes de engenharia de Infraestrutura do Windows Server e Cloud Server. Para obter mais informações, consulte [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>XPerf

XPerf é uma ferramenta de linha de comando para capturar rastreamentos do Kit de Ferramentas de Desempenho do Windows.

Para obter mais informações, consulte [Windows Performance Toolkit – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Próximas etapas

### <a name="upload-diagnostics-logs-and-reports-to-microsoft-support-for-further-review"></a>Carregar relatórios e logs de diagnóstico para o Suporte da Microsoft para mais exame

Ao trabalhar com a equipe do Suporte da Microsoft, você pode ser solicitado a transmitir a saída gerada por PerfInsights para auxiliar no processo de solução de problemas.

O agente de suporte criará um espaço de trabalho DTM para você e você receberá uma mensagem de email incluindo um link para o portal DTM (https://filetransfer.support.microsoft.com/EFTClient/Account/Login.htm) e uma ID de usuário e senha únicos.

Esta mensagem será enviada de **Serviços de Diagnóstico Automatizados CTS** (ctsadiag@microsoft.com).

![Exemplo da mensagem](media/how-to-use-perfInsights/supportemail.png)

Para segurança adicional, você será solicitado a alterar sua senha no primeiro uso.

Após fazer logon no DTM, você verá uma caixa de diálogo para carregar o arquivo **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** coletado por PerfInsights.
