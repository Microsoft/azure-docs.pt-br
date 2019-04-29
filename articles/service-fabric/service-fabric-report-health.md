---
title: Adicionar relatórios de integridade personalizados do Service Fabric | Microsoft Docs
description: Descreve como enviar relatórios de integridade personalizados para entidades de integridade da Malha de Serviço do Azure. Fornece recomendações para projetar e implementar relatórios de integridade de qualidade.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: 0a00a7d2-510e-47d0-8aa8-24c851ea847f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 49ebf4ab95816a3da2f74a464b12b46de6228456
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723436"
---
# <a name="add-custom-service-fabric-health-reports"></a>Adicionar relatórios de integridade personalizados do Service Fabric
O Azure Service Fabric apresenta um [modelo de integridade](service-fabric-health-introduction.md) desenvolvido para sinalizar condições de cluster e aplicativo não íntegras em entidades específicas. O modelo de integridade usa **relatores de integridade** (componentes do sistema e watchdogs). O objetivo é facilitar e agilizar o diagnóstico e o reparo. Os criadores de serviço precisam pensar à frente sobre a integridade. Qualquer condição que possa afetar a integridade deve ser apontada, especialmente se ela puder ajudar a sinalizar problemas próximos da raiz. As informações de integridade podem economizar tempo e esforço na investigação e depuração. A utilidade é especialmente clara quando o serviço está em funcionamento em grande escala na nuvem (Azure ou privada).

Os relatores da Malha de Serviço monitoram condições identificadas de interesse. Eles informam essas condições com base na respectiva exibição local. O [repositório de integridade](service-fabric-health-introduction.md#health-store) agrega dados de integridade enviados por todos os relatores para determinar se as entidades estão globalmente íntegras. O modelo foi desenvolvido com a intenção de ser completo, flexível e fácil de usar. A qualidade dos relatórios de integridade determina a precisão da exibição de integridade do cluster. Falsos positivos que mostram incorretamente problemas de integridade podem afetar negativamente atualizações ou outros serviços que usam dados de integridade. Exemplos de tais serviços são os serviços de reparo e os mecanismos de alerta. Portanto, é necessário um pouco de criatividade para fornecer relatórios que capturem condições de interesse da melhor forma possível.

Para projetar e implementar relatórios de integridade, os watchdogs e os componentes do sistema devem:

* Definir a condição de interesse, como ela será monitorada e o impacto sobre a funcionalidade do cluster ou do aplicativo. Com base nessas informações, escolha a propriedade de relatório de integridade e o estado de integridade.
* Determine a [entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy) à qual o relatório se aplica.
* Determine onde o relatório é realizado, do watchdog dentro do serviço ou de um watchdog interno ou externo.
* Definir uma fonte usada para identificar o relator.
* Escolher uma estratégia de relatório: periodicamente ou nas transições. A maneira recomendada é periodicamente, pois exige código mais simples e está menos sujeita a erros.
* Determinar por quanto tempo o relatório de condições não íntegras deve permanecer no repositório de integridade e como ele deve ser eliminado. Usando essas informações, decida a vida útil do relatório e o comportamento de remoção à expiração.

Conforme mencionado, o relatório pode ser gerado:

* Na réplica do serviço da Malha de Serviço monitorada.
* De watchdogs internos implantados como um serviço do Service Fabric (por exemplo, um serviço sem estado do Service Fabric que monitora as condições e emite relatórios). Os watchdogs podem ser implantados em todos os nós ou podem ser relacionados por afinidade com o serviço monitorado.
* Watchdogs internos que são executados nos nós do Service Fabric, mas que *não* são implementados como serviços do Service Fabric.
* De watchdogs externos que testam o recurso de *fora* do cluster do Service Fabric (por exemplo, um serviço de monitoramento, como o Gomez).

> [!NOTE]
> Pronto para uso, o cluster é populado com relatórios de integridade enviados pelos componentes do sistema. Leia mais em [Usando relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Os relatórios do usuário devem ser enviados em [entidades de integridade](service-fabric-health-introduction.md#health-entities-and-hierarchy) que já foram criadas pelo sistema.
> 
> 

Uma vez o design de relatório de integridade estiver claro, os relatórios de integridade poderão ser enviados facilmente. Você poderá usar o [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) para relatar a integridade se o cluster não for [seguro](service-fabric-cluster-security.md) ou se o cliente de malha tiver privilégios administrativos. O relatório pode ser produzido pela API usando o [FabricClient.HealthManager.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), por meio do PowerShell ou REST. Botões de configuração que dividem os relatórios em lotes para melhor desempenho.

> [!NOTE]
> O relatório de integridade é síncrono e representa apenas o trabalho de validação no lado do cliente. O fato de que o relatório é aceito pelo cliente de integridade, ou pelos objetos `Partition` ou `CodePackageActivationContext`, não significa que ele será aplicado no repositório. Ele será enviado de forma assíncrona e, possivelmente, em lote com outros relatórios. O processamento no servidor ainda pode falhar: o número de sequência é obsoleto, a entidade na qual o relatório deve ser aplicado foi excluída, etc.
> 
> 

## <a name="health-client"></a>Cliente de integridade
Os relatórios de integridade são enviados para o Gerenciador de integridade por meio de um cliente de integridade, que reside dentro do cliente de malha. O Gerenciador de integridade salva relatórios no repositório de integridade. O cliente de integridade pode ser configurado da seguinte maneira:

* **HealthReportSendInterval**: O atraso entre a hora em que o relatório será adicionado ao cliente e o tempo que ele é enviado para o Gerenciador de integridade. Usado para relatórios de lote em uma única mensagem, em vez de enviar uma mensagem para cada relatório. O envio em lote melhora o desempenho. Padrão: 30 segundos.
* **HealthReportRetrySendInterval**: Relata o intervalo no qual o cliente de integridade reenvia integridade acumulados ao Gerenciador de integridade. Padrão: 30 segundos no mínimo: 1 segundo.
* **HealthOperationTimeout**: O período de tempo limite para uma mensagem de relatório enviada ao Gerenciador de integridade. Se uma mensagem de tempo limite, o cliente de integridade fará uma nova tentativa até que o Gerenciador de integridade confirme que o relatório foi processado. Padrão: dois minutos.

> [!NOTE]
> Quando os relatórios são agrupados em lotes, o cliente de malha deve ser mantido em atividade até que, pelo menos, HealthReportSendInterval garanta que eles sejam enviados. Se a mensagem for perdida ou o Gerenciador de integridade não pode aplicá-las devido a erros transitórios, o cliente de malha deve ser mantido ativo mais dar-lhe a oportunidade de tentar novamente.
> 
> 

O armazenamento em buffer no cliente leva a exclusividade dos relatórios em consideração. Por exemplo, se um relator específico com problemas estiver relatando 100 relatórios por segundo na mesma propriedade da mesma entidade, os relatórios serão substituídos pela versão mais recente. Existe, no máximo, um relatório como este na fila do cliente. Se o envio em lote estiver configurado, o número de relatórios enviados para o Gerenciador de integridade é apenas um por intervalo de envio. Este relatório é o último adicionado, que reflete o estado mais atual da entidade.
Os parâmetros de configuração podem ser especificados quando `FabricClient` é criado, passando o [FabricClientSettings](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclientsettings) com os valores desejados para as entradas relacionadas à integridade.

O exemplo a seguir cria um cliente de malha e especifica que os relatórios devem ser enviados quando forem adicionados. Em tempos limite e erros que podem ser recuperados, as repetições ocorrem a cada 40 segundos.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Recomendamos manter as configurações padrão de cliente de malha, que definem `HealthReportSendInterval` para 30 segundos. Essa configuração garante um desempenho ideal devido ao envio em lote. Para relatórios importantes que devem ser enviados o quanto antes, use `HealthReportSendOptions` com `true` imediato na API do [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth). Relatórios imediatos ignoram o intervalo de envio em lote. Use este sinalizador com cuidado. Queremos aproveitar o envio em lote do cliente de integridade sempre que possível. O envio imediato também é útil quando o cliente de malha está em encerramento (por exemplo, o processo determinou estado inválido e precisa ser desligado para evitar efeitos colaterais). Isso garante envios de melhor esforço dos relatórios acumulados. Quando um relatório é adicionado com o sinalizador de Imediato, o cliente de integridade envia em lote todos os relatórios acumulados desde o último envio.

Os mesmos parâmetros podem ser especificados ao criar uma conexão com um cluster por meio do PowerShell. Veja abaixo o início de uma conexão com um cluster local:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

Os relatórios podem ser enviados de maneira semelhante à API, usando o comutador `-Immediate` a ser enviado imediatamente, independentemente do valor `HealthReportSendInterval`.

Para REST, os relatórios são enviados para o gateway do Service Fabric, que tem um cliente de malha interno. Por padrão, esse cliente está configurado para enviar relatórios em lote a cada 30 segundos. Você pode alterar o intervalo de lote com a configuração de cluster `HttpGatewayHealthReportSendInterval` em `HttpGateway`. Conforme mencionado, a melhor opção é enviar os relatórios com `Immediate` verdadeiro. 

> [!NOTE]
> Para garantir que os serviços não autorizados não possam relatar a integridade em relação às entidades no cluster, o servidor pode ser configurado para aceitar somente solicitações de clientes protegidos. O `FabricClient` usado para o relatório deve ter a segurança habilitada para ser capaz de se comunicar com o cluster (por exemplo, com a autenticação Kerberos ou de certificado). Leia mais sobre [segurança de cluster](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Relatório nos serviços de baixo privilégio
Se os serviços do Service Fabric não têm acesso administrativo ao cluster, você pode enviar relatórios de integridade sobre entidades do contexto atual por meio de `Partition` ou `CodePackageActivationContext`.

* Em serviços sem estado, use [IStatelessServicePartition.ReportInstanceHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) para relatar a instância atual do serviço.
* Em serviços com estado, use [IStatefulServicePartition.ReportReplicaHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) para relatar a réplica atual.
* Use [IServicePartition.ReportPartitionHealth](https://docs.microsoft.com/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) para relatar a entidade de partição atual.
* Use [CodePackageActivationContext.ReportApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) para relatar o aplicativo atual.
* Use [CodePackageActivationContext.ReportDeployedApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) para relatar o aplicativo atual implantado no nó atual.
* Use [CodePackageActivationContext.ReportDeployedServicePackageHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) para enviar relatório sobre um pacote de serviços ao aplicativo atual implantado no nó atual.

> [!NOTE]
> Internamente, `Partition` e `CodePackageActivationContext` mantêm um cliente de integridade que é configurado com parâmetros padrão. Conforme explicado para o [cliente de integridade](service-fabric-report-health.md#health-client), os relatórios são reunidos e enviados em lote de acordo com um temporizador. Os objetos devem ser mantidos ativos para tentar enviar o relatório.
> 
> 

Você pode especificar `HealthReportSendOptions` durante o envio de relatórios por meio de APIs de integridade de`Partition` e `CodePackageActivationContext`. Se você tiver relatórios importantes que devem ser enviados assim que possível, use `HealthReportSendOptions` com `true` imediato. Os relatórios imediatos ignoram o intervalo de envio em lote do cliente de integridade interno. Como mencionado anteriormente, use este sinalizador com cuidado. Queremos aproveitar o envio em lote do cliente de integridade sempre que possível.

## <a name="design-health-reporting"></a>Projetar relatórios de integridade
A primeira etapa da geração de relatórios de alta qualidade é identificar as condições que podem afetar a integridade do serviço. Qualquer condição que possa ajudar a sinalizar problemas no serviço ou cluster no início ou, melhor ainda, antes de um problema ocorrer, tem o potencial para economizar bilhões de dólares. Os benefícios incluem menos tempo de inatividade, menos horas à noite gastas para investigar e corrigir problemas e maior satisfação do cliente.

Assim que as condições são identificadas, os desenvolvedores de watchdog precisam descobrir a melhor maneira de monitorá-las para atingir o equilíbrio entre sobrecarga e utilidade. Por exemplo, pense em um serviço que faça cálculos complexos usando alguns arquivos temporários em um compartilhamento. Um watchdog pode monitorar o compartilhamento para garantir que haja espaço suficiente disponível. Ele pode escutar notificações de alterações em arquivo ou diretório. Ele pode relatar um aviso se um limite inicial for atingido e um erro se o compartilhamento estiver cheio. Ao aviso, um sistema de reparo pode começar a remover arquivos mais antigos do compartilhamento. Em um erro, um sistema de reparo pode mover a réplica de serviço para outro nó. Observe como os estados de condição são descritos em termos de integridade: o estado da condição que pode ser considerada íntegra (ok) ou não íntegra (aviso ou erro).

Quando os detalhes do monitoramento são definidos, o criador do watchdog precisará descobrir como implementar o watchdog. Se as condições puderem ser determinadas dentro do serviço, o watchdog poderá fazer parte do próprio serviço monitorado. Por exemplo, o código de serviço pode verificar o uso de compartilhamento e enviar relatórios sempre que tentar gravar um arquivo. A vantagem dessa abordagem é que o relatório é simples. É preciso ter cuidado para evitar que bugs no watchdog afetem a funcionalidade do serviço.

Os relatórios de dentro do serviço monitorado nem sempre são uma opção. Um watchdog dentro do serviço não poderá detectar as condições. Ele pode não ter a lógica ou os dados para fazer a determinação. A sobrecarga do monitoramento das condições pode ser alta. As condições também podem não ser específicas para um serviço, mas, em vez disso, afetar as interações entre os serviços. Outra opção é ter watchdogs no cluster como processos separados. Os watchdogs monitoram as condições e o relatório sem afetar os serviços principais. Por exemplo, esses watchdogs podem ser implementados como serviços sem estado no mesmo aplicativo, implantados em todos os nós ou nos mesmos nós do serviço.

Às vezes, um watchdog em execução no cluster também não é uma opção. Se a condição monitorada for a disponibilidade ou a funcionalidade do serviço como os usuários a veem, é melhor ter os watchdogs no mesmo local que os clientes do usuário. Lá, eles podem testar as operações da mesma forma que os usuários as chamam. Por exemplo, você pode ter um watchdog que reside fora do cluster e emite solicitações para o serviço e, então, verifica a latência e a exatidão do resultado. (Para um serviço de calculadora, por exemplo, 2+2 retorna 4 em um período de tempo razoável?)

Depois que os detalhes do watchdog tiverem sido finalizados, escolha uma ID de origem que o identifique de modo exclusivo. Se vários watchdogs do mesmo tipo são residentes no cluster, eles devem relatar entidades diferentes, ou, se eles relatam na mesma entidade, use ID de origem ou propriedade diferente. Dessa forma, os relatórios poderão coexistir. A propriedade do relatório de integridade deve capturar a condição monitorada. (Para o exemplo anterior, a propriedade poderia ser **TamanhoDoCompartilhamento**). Se vários relatórios aplicarem-se à mesma condição, a propriedade deverá conter algumas informações dinâmicas que permitem a coexistência dos relatórios. Por exemplo, se houver vários compartilhamentos que precisem ser monitorados, o nome da propriedade poderá ser **TamanhoDoCompartilhamento-nomedocompartilhamento**.

> [!NOTE]
> *Não* use o repositório de integridade para manter informações de status. Apenas informações relacionadas à integridade devem ser relatadas como integridade, uma vez que essas informações afetam a avaliação de integridade de uma entidade. O repositório de integridade não foi desenvolvido como um repositório para fins gerais. Ele usa a lógica de avaliação de integridade para agregar todos os dados no estado de integridade. O envio de informações não relacionadas à integridade (como relatórios de status com um estado de integridade OK) não afetará o estado de integridade agregado, mas poderá afetar negativamente o desempenho do repositório de integridade.
> 
> 

O próximo ponto de decisão é qual entidade relatar. Na maioria das vezes, a condição identifica claramente a entidade. Você deve escolher a entidade com a melhor granularidade possível. Se uma condição afeta todas as réplicas em uma partição, reporte a partição, não o serviço. Porém, há situações extremas em que é preciso ter mais cuidado. Se a condição afetar uma entidade, como uma réplica, mas o que se quer é sinalizar a condição por mais tempo do que a duração da vida útil da réplica, ela deverá ser relatada na partição. Caso contrário, quando a réplica for excluída, o repositório de integridade apagará todos os seus relatórios. Os gravadores de watchdog devem pensar sobre o tempo de vida da entidade e do relatório. Deve ficar claro quando um relatório deverá ser eliminado de um repositório (por exemplo, quando um erro relatado em uma entidade não for mais aplicável).

Vamos analisar um exemplo que reúne os pontos que descrevi. Considere um aplicativo do Service Fabric composto por um serviço persistente com estado mestre e serviços sem estado secundários implantados em todos os nós (um tipo de serviço secundário para cada tipo de tarefa). O mestre tem uma fila de processamento que contém os comandos a serem executados pelos secundários. Os secundários executam as solicitações de entrada e enviam de volta sinais de confirmação. Uma condição que pode ser monitorada é o comprimento da fila de processamento mestre. Se o comprimento da fila mestre atingir um limite, um aviso é relatado. O aviso indica que os secundários não conseguem processar a carga. Se a fila atinge o tamanho máximo e comandos são descartados, um erro será relatado, uma vez que não será possível recuperar o serviço. Os relatórios podem estar na propriedade **QueueStatus**. O watchdog reside no serviço e é enviado periodicamente na réplica primária mestre. A vida útil é de dois minutos e é enviada periodicamente a cada 30 segundos. Se a primária falhar, o relatório será automaticamente removido do repositório. Se a réplica de serviço estiver ativa, mas apresentar deadlock ou outros problemas, o relatório expirará no Repositório de Integridade. Neste caso, a entidade é avaliada no erro.

Outra condição que pode ser monitorada é o tempo de execução da tarefa. O mestre distribui as tarefas aos secundários com base no tipo de tarefa. Dependendo do design, o mestre pode sondar os secundários quanto ao status da tarefa. Ele também pode aguardar que os secundários enviem sinais de confirmação de volta quando tiverem concluído. No segundo caso, é preciso ter cuidado para detectar situações em que os subordinados são desativados ou as mensagens são perdidas. Uma opção é o mestre enviar uma solicitação de ping para o mesmo secundário, que envia o status de volta. Se nenhum status for recebido, o mestre considerará uma falha e reagendará a tarefa. Este comportamento pressupõe que as tarefas sejam idempotentes.

A condição monitorada poderá ser traduzida como um aviso se a tarefa não for executada em um determinado momento (**t1**, por exemplo, 10 minutos). Se a tarefa não for concluída no prazo (**t2**, por exemplo, 20 minutos), a condição monitorada poderá ser traduzida como Erro. Esse relatório pode ser gerado de várias maneiras:

* A réplica primária mestre relata a si mesma periodicamente. Você pode ter uma propriedade para todas as tarefas pendentes na fila. Se pelo menos uma tarefa demorar mais, o status do relatório na propriedade **PendingTasks** será um aviso ou erro, conforme apropriado. Se não houver tarefas pendentes ou se a execução de todas tiver sido iniciada, o status de relatório será OK. As tarefas são persistentes. Se a primária falhar, a nova primária promovida poderá continuar relatando adequadamente.
* Outro processo do watchdog (na nuvem ou externo) verifica as tarefas (do lado de fora, com base no resultado da tarefa desejado) para ver se estão concluídas. Se elas não respeitarem os limites, um relatório será enviado no serviço mestre. Um relatório também será enviado em cada tarefa que inclui o identificador de tarefa, como **PendingTask+taskId**. Os relatórios devem ser enviados somente em estados não íntegros. Defina a vida útil para alguns minutos e marque os relatórios a serem removidos quando expirados para garantir a limpeza.
* O secundário que está executando uma tarefa relatará quando demorar mais do que o esperado para executá-la. Ele relata a instância do serviço na propriedade **PendingTasks**. O relatório identifica a instância do serviço que apresenta problemas, mas não captura a situação em que a instância é desativada. Os relatórios são removidos neste momento. Ele pode relatar o serviço secundário. Se o subordinado concluir a tarefa, a instância secundária removerá o relatório do repositório. Este relatório não captura a situação em que a mensagem de confirmação é perdida e a tarefa não é finalizada do ponto de vista do mestre.

No entanto, o relatório será gerado nos casos descritos acima; eles serão capturados na integridade do aplicativo quando a integridade for avaliada.

## <a name="report-periodically-vs-on-transition"></a>Relatar periodicamente x na transição
Usando o modelo de relatório de integridade, os watchdogs podem enviar relatórios periodicamente ou nas transições. A forma recomendada para relatórios watchdog é periodicamente, pois o código é muito mais simples e menos sujeito a erros. Os watchdogs devem ser sempre buscar ser o mais simples possível para evitar bugs que disparem relatórios incorretos. Relatórios *não íntegro* incorretos afetam as avaliações de integridade e os cenários com base na integridade, incluindo atualizações. Os relatórios incorretos de *integridade* ocultam problemas no cluster, o que não é desejado.

Para relatórios periódicos, o watchdog pode ser implementado com um temporizador. Em um retorno de chamada do temporizador, o watchdog pode verificar o estado e enviar um relatório com base no estado atual. Não é necessário ver qual relatório foi enviado anteriormente nem fazer qualquer otimização em termos de mensagens. O cliente de integridade tem lógica de envio em lote para ajudar com o desempenho. Enquanto o cliente de integridade for mantido em atividade, ele fará novas tentativas até que o relatório seja confirmado pelo Repositório de Integridade ou até que o watchdog gere um relatório mais recente com a mesma entidade, propriedade e origem.

Relatórios sobre transições exigem tratamento cuidadoso do estado. O watchdog monitora algumas condições e relata apenas quando as condições mudam. A vantagem dessa abordagem é que menos relatórios são necessários. O lado negativo é que a lógica do watchdog é complexa. O watchdog deve manter as condições ou os relatórios para que possam ser inspecionados para determinar as mudanças de estado. Durante o failover, deve-se ter cuidado com relatórios adicionados que ainda não foram enviados ao repositório de integridade. O número de sequência deve ser crescente. Caso contrário, os relatórios serão rejeitados como obsoletos. Em raros casos em que ocorra perda de dados, pode ser necessária sincronização entre o estado do relator e o estado do repositório de integridade.

Relatar transições faz sentido para serviços que relatam a si próprios, por meio de `Partition` ou `CodePackageActivationContext`. Quando o objeto local (réplica ou pacote de serviços implantado/aplicativo implantado) é removido, todos os seus relatórios também serão removidos. Esta limpeza automática alivia a necessidade de sincronização entre o relator e o Repositório de Integridade. Se o relatório for para a partição pai ou o aplicativo pai, será preciso tomar cuidado com o failover para evitar relatórios obsoletos no repositório de integridade. A lógica deve ser adicionada para manter o estado correto e limpar o relatório do repositório quando ele não for mais necessário.

## <a name="implement-health-reporting"></a>Implementar relatório de integridade
Quando os detalhes da entidade e do relatório estiverem claros, os relatórios de integridade podem ser enviados usando API, PowerShell ou REST.

### <a name="api"></a>API
Para relatar por meio da API, você precisa criar um relatório de integridade específico para o tipo de entidade que deseja relatar. Envie o relatório a um cliente de integridade. Como alternativa, crie informações de integridade e transmita-as aos métodos corretos de relatório em `Partition` ou `CodePackageActivationContext` para relatar em entidades atuais.

O exemplo a seguir mostra um relatório periódico de um watchdog dentro do cluster. O watchdog verifica se um recurso externo pode ser acessado em um nó. O recurso é necessário para um manifesto do serviço no aplicativo. Mesmo que o recurso não esteja disponível, os outros serviços no aplicativo ainda poderão funcionar corretamente. Desse modo, o relatório é enviado na entidade do pacote de serviço implantado a cada 30 segundos.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### <a name="powershell"></a>PowerShell
Enviar relatórios de integridade com ***Send-ServiceFabric*EntityType**HealthReport.

O exemplo a seguir mostra um relatório periódico sobre valores de CPU em um nó. Os relatórios devem ser enviados a cada 30 segundos e têm uma vida útil de dois minutos. Se eles expirarem, o relator está com problemas, de modo que o nó é avaliado com erro. Quando a CPU estiver acima de um limite, o relatório terá um estado de integridade de aviso. Quando a CPU permanece acima de um limite por mais do que o tempo configurado, ela é relatada como um erro. Caso contrário, o relator enviará um estado de integridade OK.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

O exemplo a seguir relata um aviso transitório em uma réplica. Primeiro, ele obtém a ID da partição e então a ID de réplica para o serviço em que está interessado. Ele envia um relatório do **PowershellWatcher** na propriedade **ResourceDependency**. O relatório é de interesse somente por dois minutos, e ele é removido do repositório automaticamente.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

### <a name="rest"></a>REST
Envie relatórios de integridade usando o REST com solicitações POST que vão para a entidade desejada e têm, no corpo, a descrição do relatório de integridade. Por exemplo, veja como enviar [relatórios de integridade do cluster](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-cluster) ou [relatórios de integridade do serviço](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service) de REST. Todas as entidades têm suporte.

## <a name="next-steps"></a>Próximas etapas
Com base nos dados de integridade, os criadores de serviço e administradores de cluster/aplicativo podem pensar em maneiras de consumir as informações. Por exemplo, eles podem configurar alertas com base no status de integridade para capturar problemas graves antes que eles provoquem interrupções. Os administradores também podem definir os sistemas de reparo para corrigir problemas automaticamente.

[Introdução ao monitoramento da integridade do Service Fabric](service-fabric-health-introduction.md)

[Como exibir relatórios de integridade do Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Como relatar e verificar a integridade do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Usar relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização de aplicativos do Service Fabric](service-fabric-application-upgrade.md)

