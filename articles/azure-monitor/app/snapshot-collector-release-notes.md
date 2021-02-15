---
title: Notas de versão para Microsoft. ApplicationInsights. SnapshotCollector NuGet Package-Application Insights
description: Notas de versão do pacote NuGet Microsoft. ApplicationInsights. SnapshotCollector usado pelo Depurador de Instantâneos de Application Insights.
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 11/10/2020
ms.openlocfilehash: 4a787c6e2a9b59874f965a2bbcebea9ce02d8082
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093253"
---
# <a name="release-notes-for-microsoftapplicationinsightssnapshotcollector"></a>Notas de versão para Microsoft. ApplicationInsights. SnapshotCollector

Este artigo contém as notas de versões para o pacote NuGet Microsoft. ApplicationInsights. SnapshotCollector para aplicativos .NET, que é usado pelo Depurador de Instantâneos de Application Insights.

[Saiba](./snapshot-debugger.md) mais sobre o Depurador de Instantâneos de Application insights para aplicativos .net.

Para obter relatórios de bugs e comentários, abra um problema no GitHub em https://github.com/microsoft/ApplicationInsights-SnapshotCollector

## <a name="release-notes"></a>Notas de versão

## <a name="1375"></a>[1.3.7.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.5)
Uma versão do ponto para backport uma correção do 1.4.0-pre.
### <a name="bug-fixes"></a>Correções de bug
- Corrija [ObjectDisposedException no desligamento](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2097).

## <a name="1374"></a>[1.3.7.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.4)
Uma versão do ponto para resolver um problema descoberto em teste Azure App cenário de anexação de código do serviço.
### <a name="changes"></a>Alterações
- O destino do netcoreapp 3.0 agora depende de Microsoft. ApplicationInsights. AspNetCore >= 2.1.1 (anteriormente >= 2.1.2).

## <a name="1373"></a>[1.3.7.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.3)
Uma versão pontual para resolver alguns problemas de alto impacto.
### <a name="bug-fixes"></a>Correções de bug
- Correção da descoberta de PDB na pasta wwwroot/bin, que foi quebrada quando alteramos o algoritmo de pesquisa de símbolo em 1.3.6.
- Corrigido o ExtractWasCalledMultipleTimesException com ruído na telemetria.

## <a name="137"></a>[1.3.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7)
### <a name="changes"></a>Alterações
- O destino netcoreapp 2.0 de SnapshotCollector depende de Microsoft. ApplicationInsights. AspNetCore >= 2.1.1 (novamente). Isso reverte o comportamento de como ele era antes de 1.3.5. Tentamos atualizá-lo no 1.3.6, mas ele quebrou alguns cenários de serviço Azure App.
### <a name="new-features"></a>Novos recursos
- Snapshot Collector lê e analisa o ConnectionString a partir da variável de ambiente APPLICATIONINSIGHTS_CONNECTION_STRING ou do TelemetryConfiguration. Basicamente, isso é usado para definir o ponto de extremidade para se conectar ao serviço de instantâneo. Para obter mais informações, consulte a [documentação cadeias de conexão](./sdk-connection-string.md).
### <a name="bug-fixes"></a>Correções de bug
- Mudou para o uso de HttpClient para todos os destinos, exceto Net45 porque WebRequest falhou em alguns ambientes devido a um SecurityProtocol incompatível (requer TLS 1,2).

## <a name="136"></a>[1.3.6](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.6)
### <a name="changes"></a>Alterações
- SnapshotCollector agora depende de Microsoft. ApplicationInsights >= 2.5.1 para todas as estruturas de destino. Isso pode ser uma alteração significativa se seu aplicativo depender de uma versão mais antiga do SDK do Microsoft. ApplicationInsights.
- Remova o suporte para TLS 1,0 e 1,1 no carregador de instantâneos.
- O período de verificações de PDB agora é padrão 24 horas em vez de 15 minutos. Configurável via PdbRescanInterval em SnapshotCollectorConfiguration.
- A verificação de PDB pesquisa somente pastas de nível superior, em vez de recursiva. Isso pode ser uma alteração significativa se os símbolos estiverem em subpastas da pasta binária.
### <a name="new-features"></a>Novos recursos
- Rotação de log em SnapshotUploader para evitar preencher a pasta de logs com arquivos antigos.
- Suporte à desotimização (via ReJIT em anexo) para aplicativos .NET Core 3,0.
- Adicione símbolos ao pacote NuGet.
- Defina metadados adicionais ao carregar minidespejos.
- Adicionada uma propriedade Initialized a SnapshotCollectorTelemetryProcessor. É um CancellationToken, que será cancelado quando o Snapshot Collector for completamente inicializado e conectado ao ponto de extremidade de serviço.
- Os instantâneos agora podem ser capturados para exceções em métodos gerados dinamicamente. Por exemplo, as árvores de expressões compiladas geradas por Entity Framework consultas.
### <a name="bug-fixes"></a>Correções de bug
- AmbiguousMatchException carregando Snapshot Collector devido a Status Monitor.
- O método de extensão GetSnapshotCollector agora pesquisa todos os TelemetrySinks.
- Não inicie o carregador de instantâneos em plataformas sem suporte.
- Tratar InvalidOperationException ao desotimizar métodos dinâmicos (por exemplo, Entity Framework)

## <a name="135"></a>[1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5)
- Adicionar suporte para nuvens soberanas (as versões mais antigas não funcionarão em nuvens soberanas)
- Adicionar o coletor de instantâneos ficou mais fácil usando AddSnapshotCollector (). Encontre mais informações [aqui](./snapshot-debugger-appservice.md).
- Use a configuração de MD5 do FISMA para verificar blocos de BLOB. Isso evita o algoritmo de criptografia MD5 padrão do .NET, que não está disponível quando o sistema operacional é definido para o modo compatível com FIPS.
- Ignore .NET Framework quadros ao desotimizar as chamadas de função. Esse comportamento pode ser controlado pela definição de configuração DeoptimizeIgnoredModules.
- Adicione `DeoptimizeMethodCount` uma definição de configuração que permita a desotimização de mais de uma chamada de função. Mais informações podem ser obtidas aqui

## <a name="134"></a>[1.3.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.4)
- Permitir chaves de instrumentação estruturada.
- Aumentar a robustez do SnapshotUploader-continuar a inicialização mesmo se os logs antigos do carregador não puderem ser movidos.
- Reativada relatando telemetria adicional quando SnapshotUploader.exe é encerrada imediatamente (foi desabilitada no 1.3.3).
- Simplifique a telemetria interna.
- _Recurso experimental_: planos de coleta do Snappoint: Adicione "snapshotOnFirstOccurence". Mais informações estão disponíveis [aqui](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe).

## <a name="133"></a>[1.3.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.3)
- Corrigido o bug que estava fazendo SnapshotUploader.exe parar de responder e não carregar instantâneos para aplicativos .NET Core.

## <a name="132"></a>[1.3.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.2)
- _Recurso experimental_: planos de coleta do Snappoint. Mais informações estão disponíveis [aqui](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe).
- SnapshotUploader.exe sairá quando o tempo de execução descarrega o AppDomain do qual SnapshotCollector é carregado, em vez de aguardar a saída do processo. Isso melhora a confiabilidade do coletor quando hospedado no IIS.
- Adicione configuração para permitir que várias instâncias de SnapshotCollector que estejam usando a mesma chave de instrumentação compartilhem o mesmo processo SnapshotUploader: ShareUploaderProcess (o padrão é `true` ).
- Relatar telemetria adicional quando SnapshotUploader.exe for encerrada imediatamente.
- Redução do número de arquivos de suporte SnapshotUploader.exe precisa gravar no disco.

## <a name="131"></a>[1.3.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.1)
- Remova o suporte para coletar instantâneos com a API RtlCloneUserProcess e dar suporte apenas à API PssCaptureSnapshots.
- Aumente o limite padrão de quantos instantâneos podem ser capturados em 10 minutos, de 1 a 3.
- Permitir que o SnapshotUploader.exe negocie o TLS 1,1 e 1,2
- Relatar telemetria adicional quando o SnapshotUploader registra um aviso ou um erro
- Parar de tirar instantâneos quando o serviço de back-end relatar que a cota diária foi atingida (50 instantâneos por dia)
- Adicione check-in adicional SnapshotUploader.exe para não permitir que duas instâncias sejam executadas ao mesmo tempo.

## <a name="130"></a>[1.3.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.0)
### <a name="changes"></a>Alterações
- Para os aplicativos que visam .NET Framework, Snapshot Collector agora depende do Microsoft. ApplicationInsights versão 2.3.0 ou superior.
Ele costumava ser 2.2.0 ou superior.
Acreditamos que isso não será um problema para a maioria dos aplicativos, mas nos avise se essa alteração impedir que você use as Snapshot Collector mais recentes.
- Use atrasos de retirada exponencial no carregador de instantâneos ao tentar fazer uploads com falha.
- Use ServerTelemetryChannel (se disponível) para um relatório mais confiável de telemetria.
- Use ' SdkInternalOperationsMonitor ' na conexão inicial com o serviço de Depurador de Instantâneos para que ele seja ignorado pelo controle de dependência.
- Melhore a telemetria em volta da conexão inicial ao serviço de Depurador de Instantâneos.
- Relatar telemetria adicional para:
  - Azure App versão do serviço.
  - Instâncias de computação do Azure.
  - Contêineres.
  - Aplicativo de funções do Azure.
### <a name="bug-fixes"></a>Correções de bug
- Quando o intervalo de redefinição do contador de problemas é definido como 24 dias, interprete-o como 24 horas.
- Corrigido um bug em que o carregador de instantâneos interromperia o processamento de novos instantâneos se houvesse uma exceção ao descartar um instantâneo.

## <a name="123"></a>[1.2.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.3)
- Corrija a assinatura de nome forte com binários do carregador de instantâneo.

## <a name="122"></a>[1.2.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.2)
### <a name="changes"></a>Alterações
- Os arquivos necessários para SnapshotUploader (64). exe agora são inseridos como recursos na DLL principal. Isso significa que a pasta SnapshotCollectorFiles não é mais criada, simplificando a compilação e a implantação e reduzindo a desordem em Gerenciador de Soluções. Tome cuidado ao atualizar para examinar as alterações em seu `.csproj` arquivo. O `Microsoft.ApplicationInsights.SnapshotCollector.targets` arquivo não é mais necessário.
- A telemetria é registrada em seu recurso de Application Insights, mesmo se ProvideAnonymousTelemetry estiver definido como false. Isso é para que possamos implementar um recurso de verificação de integridade no portal do Azure. ProvideAnonymousTelemetry afeta apenas a telemetria enviada à Microsoft para o suporte e o aperfeiçoamento do produto.
- Quando TempFolder ou ShadowCopyFolder são redirecionados para variáveis de ambiente, mantenha o coletor ocioso até que essas variáveis de ambiente sejam definidas.
- Para aplicativos que se conectam à Internet por meio de um servidor proxy, Snapshot Collector agora irá detectar automaticamente as configurações de proxy e passá-las para SnapshotUploader.exe.
- Diminua a prioridade do processo SnapshotUplaoder (sempre que possível). Essa prioridade pode ser substituída por meio da opção IsLowPrioirtySnapshotUploader.
- Adicionado um método de extensão GetSnapshotCollector no TelemetryConfiguration para cenários em que você deseja configurar o Snapshot Collector de forma programática.
- Defina a versão do SDK do Application Insights (em vez da versão do aplicativo) na telemetria voltada para o cliente.
- Envie o primeiro evento de pulsação após dois minutos.
### <a name="bug-fixes"></a>Correções de bug
- Corrigir NullReferenceException quando as exceções tiverem dicionários de dados nulos ou imutáveis.
- No carregador, tente novamente a PDB correspondendo algumas vezes se obtivermos uma violação de compartilhamento.
- Corrija a telemetria duplicada quando mais de um thread chamar o pipeline de telemetria na inicialização.

## <a name="121"></a>[1.2.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.1)
### <a name="changes"></a>Alterações
- Os arquivos de comentário de documento XML agora estão incluídos no pacote NuGet.
- Adicionado um método de extensão ExcludeFromSnapshotting no `System.Exception` para cenários em que você sabe que tem uma exceção ruidosa e deseja evitar a criação de instantâneos para ele.
- Adicionada uma propriedade de configuração IsEnabledWhenProfiling, o padrão é true. Essa é uma alteração de versões anteriores em que a criação de instantâneo foi temporariamente desabilitada se o Application Insights Profiler estava executando uma coleção detalhada. O comportamento antigo pode ser recuperado definindo essa propriedade como false.
### <a name="bug-fixes"></a>Correções de bug
- Assine SnapshotUploader64.exe corretamente.
- Proteja-se contra a inicialização dupla do processador de telemetria.
- Impedir o log duplo de telemetria em aplicativos com vários pipelines.
- Corrija um bug com o tempo de expiração de um plano de coleta, o que pode impedir instantâneos após 24 horas.

## <a name="120"></a>[1.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.0)
A maior alteração nessa versão (por isso, a mudança para um novo número de versão secundária) é uma reescrita do pipeline de criação e manipulação de instantâneos. Em versões anteriores, essa funcionalidade foi implementada em código nativo (ProductionBreakpoints *. dll e SnapshotHolder*. exe). A nova implementação é todo o código gerenciado com P/Invokes. Para essa primeira versão usando o novo pipeline, não nos estivéssemos de longe do comportamento original. A nova implementação permite um relatório de erros melhor e nos configura para aprimoramentos futuros.

### <a name="other-changes-in-this-version"></a>Outras alterações nesta versão
- MinidumpUploader.exe foi renomeado para SnapshotUploader.exe (ou SnapshotUploader64.exe).
- Telemetria de tempo adicionada para desotimizar/reotimizar solicitações.
- A compactação Gzip foi adicionada para carregamentos de minidespejo.
- Corrigido um problema em que PDBs foram bloqueados impedindo a atualização do site.
- Registre o nome da pasta original (SnapshotCollectorFiles) ao copiar sombra.
- Ajuste os limites de memória para processos de 64 bits para evitar reinicializações de site devido a OOM.
- Corrija um problema em que os instantâneos ainda foram coletados mesmo após a desabilitação.
- Registrar eventos de pulsação no recurso de ia do cliente.
- Melhore a velocidade do instantâneo removendo "origem" da ID do problema.

## <a name="112"></a>[1.1.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.2)
### <a name="changes"></a>Alterações
Telemetria de uso aumentada
- Detectar e relatar a versão e o sistema operacional .NET
- Detectar e relatar ambientes adicionais do Azure (serviço de nuvem, Service Fabric)
- Registre e relate métricas de exceção (número de exceções de 1ª chance e número de chamadas Trackexception) na telemetria de pulsação.
### <a name="bug-fixes"></a>Correções de bug
- Tratamento correto de SqlException em que a exceção interna (Win32exception) não é lançada.
- Corte os espaços à direita nas pastas de símbolo, o que causou uma análise incorreta dos argumentos de linha de comando para o MinidumpUploader.
- Impeça a repetição infinita de conexões com falha para o ponto de extremidade do agente de Depurador de Instantâneos.

## <a name="110"></a>[1.1.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.0)
### <a name="changes"></a>Alterações
- Proteção de memória do host adicionada. Esse recurso reduz o impacto na memória da máquina host.
- Melhore a experiência de exibição de instantâneo portal do Azure.