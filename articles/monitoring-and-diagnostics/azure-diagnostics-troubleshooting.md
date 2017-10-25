---
title: "Solução de problemas do Diagnóstico do Azure | Microsoft Docs"
description: "Solucionar problemas ao usar o diagnóstico do Azure no Service Fabric, Serviços de Nuvem e Máquinas Virtuais do Azure."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 66469bce-d457-4d1e-b550-a08d2be4d28c
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/12/2017
ms.author: robb
ms.openlocfilehash: b03265b52886b30e4b9de0b0293e5dadd6d2413a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-diagnostics-troubleshooting"></a>Solução de problemas do Diagnóstico do Azure
Este artigo descreve informações de solução de problemas relevantes para o uso do Diagnóstico do Azure. Para mais informações sobre o Diagnóstico do Azure, consulte [Visão geral do Diagnóstico do Azure](azure-diagnostics.md).

## <a name="logical-components"></a>Componentes lógicos
**Iniciador do plug-in de diagnóstico (DiagnosticsPluginLauncher.exe)**: inicia a extensão de Diagnóstico do Microsoft Azure. Serve como o processo do ponto de entrada.

**Plug-in de Diagnóstico (DiagnosticsPlugin.exe)**: Configura, inicializa e gerencia o tempo de vida do agente de monitoramento. É o principal processo inicializado pelo inicializador.

**Agente de Monitoramento (MonAgent\*.exe processes)**: Monitora, coleta e transfere os dados de diagnóstico.  

## <a name="logartifact-paths"></a>Caminhos do log/artefato
A seguir, são apresentados os caminhos para alguns logs e artefatos importantes. Estas informações serão referidas ao longo de todo o documento.

### <a name="azure-cloud-services"></a>Serviços de nuvem do Azure
| Artefato | Caminho |
| --- | --- |
| **Arquivo de configuração de Diagnóstico do Microsoft Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **Arquivos de log** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Armazenamento local para dados de diagnóstico** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Tables |
| **Arquivo de configuração do Agente de monitoramento** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Pacote de extensão do Diagnóstico do Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Caminho do utilitário de coleta de log** | %SystemDrive%\Packages\GuestAgent\ |
| **Arquivo de log MonAgentHost** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Máquinas virtuais
| Artefato | Caminho |
| --- | --- |
| **Arquivo de configuração de Diagnóstico do Microsoft Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings |
| **Arquivos de log** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Logs\ |
| **Armazenamento local para dados de diagnóstico** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **Arquivo de configuração do Agente de monitoramento** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Arquivo de status** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Status |
| **Pacote de extensão do Diagnóstico do Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Caminho do utilitário de coleta de log** | C:\WindowsAzure\Packages |
| **Arquivo de log MonAgentHost** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Os dados de métrica não aparecem no Portal do Azure
O Diagnóstico do Azure fornece dados de métrica que podem ser exibidos no Portal do Azure. Se tiver problemas para visualizar os dados no portal, verifique a tabela \* WADMetrics na conta de armazenamento do Diagnóstico do Azure para ver se os registros de métricas correspondentes estão presentes. 

Aqui, o **PartitionKey** da tabela é a ID de recurso, máquina virtual ou conjunto de dimensionamento de máquinas virtuais. **RowKey** é o nome da métrica (também conhecido como o nome do contador de desempenho).

Se a ID de recurso estiver incorreta, verifique **Diagnósticos** **Configuração** > **Métrica** > **ResourceId** para ver se a ID de recurso está definida corretamente.

Se não houver dados para a métrica específica, verifique **Configuração de Diagnóstico** > **PerformanceCounter** para ver se a métrica (contador de desempenho) está incluída. Os seguintes contadores são habilitados por padrão:
- \Processador(_Total)\% Tempo do processador
- \Memória\Bytes Disponíveis
- \Aplicativos ASP.NET (__Total__)\Solicitações/s
- \Aplicativos ASP.NET (__Total__)\Total de Erros/s
- \ASP.NET\Solicitações Enfileiradas
- \ASP.NET\Solicitações Rejeitadas
- \Processador(w3wp)\% Tempo do Processador
- \Processo(w3wp)\Bytes Privados
- \Processo(WaIISHost)\% Tempo do Processador
- \Processo(WaIISHost)\Bytes Privados
- \Processo(WaWorkerHost)\% Tempo do Processador
- \Processo(WaWorkerHost)\Bytes Privados
- \Memória\Falhas de Página/s
- \.Memória CLR NET(_Global_)\% Tempo em GC
- \LogicalDisk(C:)\Bytes de Gravação de Disco/s
- \LogicalDisk(C:)\Bytes de Leitura de Disco/s
- \LogicalDisk(D:)\Bytes de Gravação de Disco/s
- \LogicalDisk(D:)\Bytes de Leitura de Disco/s

Se a configuração estiver configurada corretamente, mas você ainda não pode ver os dados de métrica, utilize as seguintes diretrizes para ajudá-lo a solucionar problemas.


## <a name="azure-diagnostics-isnt-starting"></a>O Diagnóstico do Azure não está iniciando
Para obter informações sobre o motivo do Diagnóstico do Azure falhar ao iniciar, consulte os arquivos **DiagnosticsPluginLauncher.log** e **DiagnosticsPlugin.log** no local dos arquivos de log fornecidos anteriormente. 

Se esses logs indicam `Monitoring Agent not reporting success after launch`, isso significa que houve uma falha ao iniciar MonAgentHost.exe. Examine os logs no local indicado para `MonAgentHost log file` na seção anterior.

A última linha dos arquivos de log contém o código de saída.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Se você encontrar um código de saída **negativo**, consulte a [tabela de código de saída](#azure-diagnostics-plugin-exit-codes) na [ seção Referências](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Os dados de diagnósticos não estão registrados no Armazenamento do Microsoft Azure
Determine se nenhum dado é exibido ou se alguns dados estão sendo exibidos.

### <a name="diagnostics-infrastructure-logs"></a>Logs de infraestrutura de diagnóstico
O diagnóstico registra todos os erros nos logs de infraestrutura de Diagnóstico. Certifique-se de ter habilitado a [captura dos logs de infraestrutura de Diagnóstico na configuração](#how-to-check-diagnostics-extension-configuration). Então, você poderá examinar rapidamente os erros relevantes que aparecem na tabela `DiagnosticInfrastructureLogsTable` na conta de armazenamento configurada.

### <a name="no-data-is-appearing"></a>Nenhum dado é exibido
O motivo mais comum para os dados de evento não serem sempre exibidos é porque as informações da conta de armazenamento estão definidas incorretamente.

Solução: corrija sua configuração do Diagnóstico e o reinstale.

Se a conta de armazenamento estiver configurada corretamente, faça acesso remoto no computador e verifique se DiagnosticsPlugin.exe e MonAgentCore.exe estão executando. Se não estiverem executando, siga as etapas em [Diagnóstico do Azure não está iniciando](#azure-diagnostics-is-not-starting). 

Se os processos estiverem executando, acesse [Os dados estão sendo capturados localmente?](#is-data-getting-captured-locally) e siga as instruções.

### <a name="part-of-the-data-is-missing"></a>Parte dos dados está ausente
Se você estiver obtendo alguns dados, mas não todos, isso significa que a coleta de dados/pipeline de transferência está configurado corretamente. Siga as subseções aqui para restringir o problema.

#### <a name="is-the-collection-configured"></a>A coleção está configurada? 
A configuração de Diagnóstico contém instruções para um determinado tipo de dados a ser coletado. [Revise a configuração](#how-to-check-diagnostics-extension-configuration) para verificar se está examinando apenas os dados configurados para a coleção.

#### <a name="is-the-host-generating-data"></a>O host está gerando dados?
- **Contadores de desempenho**: Abra o perfmon e verifique o contador.

- **Logs de rastreamento**:  Acesso remoto na VM e adicione um TextWriterTraceListener ao arquivo de configuração do aplicativo.  Confira http://msdn.microsoft.com/library/sk36c28t.aspx para configurar o ouvinte de texto.  Verifique se o elemento `<trace>` tem `<trace autoflush="true">`.<br />
Se você não visualizar os logs de rastreamento sendo gerados, consulte [Mais informações sobre logs de rastreamento ausentes](#more-about-trace-logs-missing).

- **Rastreamento de ETW**: Acesso remoto na VM e instale o PerfView.  Em PerfView, execute **Arquivo** > **Comando do Usuário** > **Escutar etwprovder1** > **etwprovider2**, e assim por diante. O comando **Escutar** diferencia letras maiúsculas de minúsculas e não pode haver espaços entre a lista separada por vírgulas dos provedores do ETW. Se o comando falhar na execução, você poderá selecionar o botão **Log**na parte inferior direita da ferramenta Perfview para ver o que tentou executar e qual foi o resultado.  Supondo que a entrada está correta, uma nova janela aparece. Em alguns segundos, você começará a ver o rastreamento de ETW.

- **Logs de vento**: Acesso remoto na VM. Abra `Event Viewer` e, em seguida, assegure-se de que os eventos existem.

#### <a name="is-data-getting-captured-locally"></a>Os dados estão sendo capturados localmente?
Em seguida, certifique-se de que os dados estão sendo capturados localmente.
Os dados são armazenados localmente em arquivos `*.tsf` no [armazenamento local para dados de diagnóstico](#log-artifacts-path). Diferentes tipos de logs coletados em diferentes arquivos `.tsf`. Os nomes são semelhantes aos nomes de tabela no Armazenamento do Microsoft Azure. 

Por exemplo, `Performance Counters` coletado em `PerformanceCountersTable.tsf`. Logs de eventos coletados em `WindowsEventLogsTable.tsf`. Utilize as instruções na seção [Extração de log local](#local-log-extraction) para abrir os arquivos de coleção local e verifique se estão coletados no disco.

Se não for possível visualizar logs sendo coletados localmente e você já verificou que o host está gerando dados, provavelmente há um problema de configuração. Revise a configuração cuidadosamente. 

Revise também a configuração que foi gerada para MonitoringAgent [MaConfig.xml](#log-artifacts-path). Verifique se há uma seção que descreve a origem do log relevante. Em seguida, verifique se não está perdido na translação entre a configuração do Diagnóstico e a configuração do agente de monitoramento.

#### <a name="is-data-getting-transferred"></a>Os dados estão sendo transferidos?
Se você verificou que os dados estão sendo capturados localmente, mas ainda não é possível visualizá-los na conta de armazenamento, siga as seguintes etapas: 

- Verifique se você forneceu uma conta de armazenamento correta e que não substituiu as chaves para a conta de armazenamento fornecida. Para os Serviços de Nuvem do Azure, às vezes observamos que os usuários não atualizam `useDevelopmentStorage=true`.

- Verifique se a conta de armazenamento fornecida está correta. Certifique-se de que você não possui restrições de rede impedindo que os componentes alcancem os pontos de extremidade do armazenamento público. Uma maneira de fazer isso é o acesso remoto no computador e, em seguida, tente gravar algo na mesma conta de armazenamento.

- Finalmente, é possível analisar quais falhas estão sendo relatadas pelo agente de monitoramento. O Agente de monitoramento grava logs no `maeventtable.tsf` que está localizado no [armazenamento local para dados de diagnóstico](#log-artifacts-path). Siga as instruções da seção [Extração de log Local](#local-log-extraction) para abrir esse arquivo. m seguida, tente determinar se há `errors`, que indicam falhas de leitura para arquivos locais gravando no armazenamento.

### <a name="capturing-and-archiving-logs"></a>Capturando e arquivando logs
Caso esteja pensando em contatar o suporte, a primeira ação que poderão solicitar a você é coletar os logs do seu computador. Você pode poupar tempo fazendo isso você mesmo. Execute o `CollectGuestLogs.exe` utilitário no [caminho do utilitário de coleta de log](#log-artifacts-path). Ele gera um arquivo .zip com todos os logs do Azure relevantes na mesma pasta.

## <a name="diagnostics-data-tables-not-found"></a>Tabelas dos dados de diagnósticos não encontradas
As tabelas no armazenamento do Azure que contêm eventos de ETW são nomeadas usando o código a seguir:

```C#
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Aqui está um exemplo:

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
Esse código gera quatro tabelas:

| Evento | Nome da tabela |
| --- | --- |
| provider=”prov1” &lt;Event id=”1” /&gt; |WADEvent+MD5("prov1")+"1" |
| provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt; |WADdest1 |
| provider=”prov1” &lt;DefaultEvents /&gt; |WADDefault+MD5("prov1") |
| provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt; |WADdest2 |

## <a name="references"></a>Referências

### <a name="how-to-check-diagnostics-extension-configuration"></a>Como verificar a configuração da extensão de diagnóstico
A maneira mais fácil de verificar sua configuração de extensão é acessar o [Azure Resource Explorer](http://resources.azure.com) e, em seguida, acessar a máquina virtual ou o serviço de nuvem onde a extensão do Diagnóstico do Azure (IaaSDiagnostics / PaaDiagnostics) está.

Como alternativa, acesse a área de trabalho remota na máquina e examine o arquivo de Configuração de Diagnóstico do Azure descrito na [Seção do caminho de artefatos de log](#log-artifacts-path).

Em ambos os casos, pesquise por **Microsoft.Azure.Diagnostics** e, em seguida, pelo campo **xmlCfg** ou **WadCfg**. 

Se estiver pesquisando em uma máquina virtual e o campo **WadCfg** estiver presente, isso significa que a configuração está no formato JSON. Se o campo **xmlCfg** estiver presente, significa que a configuração está em XML e codificada em Base64. Você precisa [decodificá-la](http://www.bing.com/search?q=base64+decoder) para ver o XML que foi carregado pelo Diagnóstico.

Para a função de serviço de nuvem, se você escolher a configuração a partir do disco, os dados são codificados em Base64, então, será necessário [descodificá-los](http://www.bing.com/search?q=base64+decoder) para ver o XML que foi carregado pelo Diagnóstico.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Códigos de saída do plug-in do Diagnóstico do Microsoft Azure
O plug-in retorna os seguintes códigos de saída:

| Código de saída | Descrição |
| --- | --- |
| 0 |Sucesso. |
| -1 |Erro genérico. |
| -2 |Não foi possível carregar o arquivo rcf.<p>Este erro interno somente deverá ocorrer se o iniciador do plug-in do agente convidado for invocado manualmente e incorretamente na VM. |
| -3 |Não é possível carregar o arquivo de configuração do Diagnóstico.<p><p>Solução: causado por um arquivo de configuração que não passa pela validação do esquema. A solução é fornecer um arquivo de configuração que cumpre com o esquema. |
| -4 |Outra instância do agente de monitoramento do Diagnostics já está usando o diretório de recurso local.<p><p>Solução: especifique um valor diferente para **LocalResourceDirectory**. |
| -6 |O iniciador de plug-in do agente de convidado tentou iniciar o Diagnóstico com uma linha de comando inválida.<p><p>Este erro interno somente deverá ocorrer se o iniciador do plug-in do agente convidado for invocado manualmente e incorretamente na VM. |
| -10 |O plug-in de Diagnostics saiu com uma exceção sem tratamento. |
| -11 |O agente convidado não pôde criar o processo responsável por iniciar e monitorar o agente de monitoramento.<p><p>Solução: verifique se há recursos de sistema suficientes disponíveis para iniciar novos processos.<p> |
| -101 |Argumentos inválidos ao chamar o plug-in de Diagnóstico.<p><p>Este erro interno somente deverá ocorrer se o iniciador do plug-in do agente convidado for invocado manualmente e incorretamente na VM. |
| -102 |O processo do plug-in não consegue inicializar-se sozinho.<p><p>Solução: verifique se há recursos de sistema suficientes disponíveis para iniciar novos processos. |
| -103 |O processo do plug-in não consegue inicializar-se sozinho. Especificamente, não é possível criar o objeto do agente.<p><p>Solução: verifique se há recursos de sistema suficientes disponíveis para iniciar novos processos. |
| -104 |Não foi possível carregar o arquivo rcf fornecido pelo agente convidado.<p><p>Este erro interno somente deverá ocorrer se o iniciador do plug-in do agente convidado for invocado manualmente e incorretamente na VM. |
| -105 |O plug-in de Diagnóstico não consegue abrir o arquivo de configuração do Diagnóstico.<p><p>Este erro interno somente deverá ocorrer se o plug-in do Diagnóstico for invocado manualmente e incorretamente na VM. |
| -106 |Não é possível ler o arquivo de configuração do Diagnóstico.<p><p>Causado por um arquivo de configuração que não passa pela validação de esquema. <br><br>Solução: Forneça um arquivo de configuração que seja compatível com o esquema. Para obter mais informações, consulte [Como verificar a configuração da extensão do diagnóstico](#how-to-check-diagnostics-extension-configuration). |
| -107 |O diretório de recursos que passa para o agente de monitoramento é inválido.<p><p>Este erro interno somente deverá ocorrer se o agente de monitoramento for invocado manualmente e incorretamente na VM.</p> |
| -108 |Não é possível converter o arquivo de configuração de Diagnóstico para o arquivo de configuração do agente de monitoramento.<p><p>Esse erro interno deve acontecer somente se o plug-in de diagnóstico é invocado manualmente com um arquivo de configuração inválido. |
| -110 |Erro de configuração de diagnóstico geral.<p><p>Esse erro interno deve acontecer somente se o plug-in de diagnóstico é invocado manualmente com um arquivo de configuração inválido. |
| -111 |Não foi possível iniciar o agente de monitoramento.<p><p>Solução: verifique se há recursos de sistema suficientes disponíveis para iniciar novos processos. |
| -112 |Erro geral |

### <a name="local-log-extraction"></a>Extração de log local
O agente de monitoramento coleta logs e artefatos como arquivos `.tsf`. O arquivo `.tsf` não está legível, mas você pode convertê-lo em um `.csv` da seguinte maneira: 

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Um novo arquivo chamado `<relevantLogFile>.csv` será criado no mesmo caminho correspondente do arquivo `.tsf`.

>[!NOTE] 
> É necessário somente executar esse utilitário com o arquivo .tsf principal (por exemplo, PerformanceCountersTable.tsf). Os arquivos de acompanhamento (por exemplo, PerformanceCountersTables_\*\*001.tsf, PerformanceCountersTables_\*\*002.tsf, e assim por diante) são processados automaticamente.

### <a name="more-about-missing-trace-logs"></a>Mais informações sobre logs de rastreamento ausentes 

>[!NOTE]
> As informações a seguir são aplicáveis principalmente aos Serviços de Nuvem do Azure, exceto se o DiagnosticsMonitorTraceListener foi configurado em um aplicativo que está executando na VM da IaaS. 

- Certifique-se de que o **DiagnosticMonitorTraceListener** está configurado no web.config ou app.config.  Isso é configurado por padrão em projetos de serviços de nuvem. No entanto, alguns clientes comentam que as instruções de rastreamento não são coletadas pelos diagnósticos. 

- Se os logs não estiverem sendo gravados a partir do **OnStart** ou do método **Executar**, certifique-se de que o **DiagnosticMonitorTraceListener** está no app.config.  Por padrão, ele está no web.config, mas isso somente é aplicável ao código executando no w3wp.exe. Portanto, é necessário que esteja no app.config para capturar rastreamentos executando no WaIISHost.exe.

- Certifique-se de que está utilizando o **Diagnostics.Trace.TraceXXX** em vez do **Diagnostics.Debug.WriteXXX.** As instruções de depuração são removidas a partir de uma compilação da versão.

- Certifique-se de que o código compilado realmente tenha **linhas Diagnostics.Trace** (utilize Reflector, ildasm ou ILSpy para verificar). Os comandos **Diagnostics.Trace** são removidos do binário compilado, a menos que você utilizes o símbolo de compilação condicional TRACE. Esse é um problema comum que ocorre ao utilizar o msbuild para compilar um projeto.   

## <a name="known-issues-and-mitigations"></a>Problemas e mitigações conhecidos
Aqui está uma lista dos problemas conhecidos com mitigações conhecidas:

**1. Dependência .NET 4.5**

A Extensão de Diagnóstico do Microsoft Azure possui uma dependência de tempo de execução no .NET 4.5 Framework ou posterior. No momento da gravação, todas as máquinas provisionadas para os Serviços de Nuvem do Azure, assim como todas as imagens oficiais baseadas em máquinas virtuais do Azure têm o .NET 4.5 ou posterior instalado. 

Ainda é possível encontrar uma situação, na qual seja necessário executar a Extensão de Diagnóstico do Microsoft Azure em uma máquina que não tenha o .NET 4.5 ou posterior. Isso ocorre ao criar a máquina a partir de uma imagem antiga ou instantâneo, ou quando você traz seu próprio disco personalizado.

Isso geralmente se manifesta como um código de saída **255** ao executar o **DiagnosticsPluginLauncher.exe.** A falha ocorre devido à seguinte exceção sem tratamento: 
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Mitigação:** Instala o .NET 4.5 ou posterior na máquina.

**2. Os dados de contadores de desempenho estão disponíveis no armazenamento, mas não são mostrados no portal**

A experiência do portal nas máquinas virtuais mostra determinados contadores de desempenho por padrão. Se não for possível visualizar os contadores de desempenho mas souber que os dados estão sendo gerados porque estão disponíveis no armazenamento, verifique o seguinte:

- Se os dados armazenados possuem nomes dos contadores no idioma inglês. Se os nomes dos contadores não estiverem em inglês, o gráfico de métrica do portal não será capaz de reconhecê-los.

- Se estiver utilizando caracteres curinga (\*) nos nomes do contador de desempenho, o portal não poderá correlacionar o contador coletado e configurado.

**Mitigação**: altera o idioma do computador para inglês para as contas do sistema. Para fazes isso, selecione **Painel de Controle** > **Região** > **Administrativo** > **Copiar Configurações**. Em seguida, desmarque **Tela de boas-vindas e contas do sistema** de modo que o idioma personalizado não seja aplicado à conta do sistema. Certifique-se também de que não está utilizando curingas, caso você queira que o portal seja sua experiência de consumo principal.
