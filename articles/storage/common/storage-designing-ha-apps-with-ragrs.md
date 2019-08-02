---
title: Criando aplicativos altamente disponíveis usando o armazenamento com redundância geográfica de acesso de leitura (RA-GRS) | Microsoft Docs
description: Como usar o armazenamento RA-GRS do Azure para projetar um aplicativo altamente disponível que seja flexível o suficiente para lidar com interrupções.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 01/17/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 16f38f6aae11f7bf806b7bad76db8f739fb2823d
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357076"
---
# <a name="designing-highly-available-applications-using-ra-grs"></a>Projetar aplicativos altamente disponíveis usando RA-GRS

Um recurso comum das infraestruturas baseadas em nuvem como o Armazenamento do Azure é que elas fornecem uma plataforma altamente disponível para hospedar aplicativos. Os desenvolvedores de aplicativos baseados em nuvem devem considerar cuidadosamente como aproveitar essa plataforma para fornecer aplicativos altamente disponíveis aos usuários. Este artigo ressalta como os desenvolvedores podem usar o RA-GRS (armazenamento com redundância geográfica com acesso de leitura) para garantir que seus aplicativos do Armazenamento do Azure são altamente disponíveis.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Este artigo tem como foco o GRS e o RA-GRS. Com GRS, três cópias dos dados são mantidas na região primária que você selecionou ao configurar a conta de armazenamento. Três cópias adicionais são mantidas de forma assíncrona em uma região secundária especificada pelo Azure. RA-GRS oferece armazenamento com redundância geográfica com acesso de leitura à cópia secundária.

Para obter informações sobre quais regiões primárias são emparelhadas com quais regiões secundárias, consulte [BCDR (continuidade dos negócios e recuperação de desastres): Regiões Combinadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Há snippets de código incluídos neste artigo e um link para um exemplo completo no fim, que você pode baixar e executar.

> [!NOTE]
> O Armazenamento do Azure agora oferece suporte a armazenamento com redundância de zona (ZRS) para criar aplicativos altamente disponíveis. O ZRS oferece uma solução simples para as necessidades de redundância de muitos aplicativos. O ZRS oferece proteção contra falhas de hardware ou desastres catastróficas que afetam um único datacenter. Para obter mais informações, confira [ZRS (Armazenamento com redundância de zona): aplicativos de Armazenamento do Azure altamente disponíveis](storage-redundancy-zrs.md).

## <a name="key-features-of-ra-grs"></a>Principais recursos do RA-GRS

Tenha em mente estes pontos-chave ao projetar seu aplicativo para RA-GRS:

* O Armazenamento do Azure mantém uma cópia somente leitura dos dados armazenados na região primária em uma região secundária. Conforme observado acima, o serviço de armazenamento determina o local da região secundária.

* A cópia somente leitura é [eventualmente consistente](https://en.wikipedia.org/wiki/Eventual_consistency) com os dados na região primária.

* Para blobs, tabelas e filas, você pode consultar a região secundária para obter um valor de *Hora da Última Sincronização* que informa quando ocorreu a última replicação da região primária para a secundária. (Não há suporte para isso nos Arquivos do Azure, que, no momento, não têm redundância RA-GRS.)

* Você pode usar a Biblioteca de Cliente de Armazenamento para interagir com os dados na região primária ou secundária. Você também poderá redirecionar as solicitações de leitura automaticamente para a região secundária se uma solicitação de leitura para a região primária atingir o tempo limite.

* Se a região primária ficar indisponível, você pode iniciar um failover de conta. Quando você executa o failover para a região secundária, as entradas DNS que apontam para a região primária são alteradas para apontar para a região secundária. Após o failover estar concluído, o acesso de gravação é restaurado para as contas GRS e RA-GRS. Para obter mais informações, confira [Recuperação de desastre e failover de conta de armazenamento (versão prévia) no Armazenamento do Azure](storage-disaster-recovery-guidance.md).

## <a name="application-design-considerations-when-using-ra-grs"></a>Considerações de design de aplicativo ao usar RA-GRS

A finalidade deste artigo é mostrar como criar um aplicativo que continuará funcionando (embora com capacidade limitada) mesmo que ocorra um grande desastre no data center primário. Você pode projetar seu aplicativo para resolver problemas transitórios ou de execução longa com a leitura da região secundária quando há um problema que interfere na leitura da região primária. Quando a região primária estiver disponível novamente, o aplicativo poderá retornar com a leitura da região primária.

### <a name="using-eventually-consistent-data"></a>Usando dados eventualmente consistentes

A solução proposta pressupõe que é aceitável retornar dados potencialmente obsoletos ao aplicativo de chamada. Como os dados na região secundária são finalmente consistentes, é possível que a região primária se torne inacessível antes que uma atualização para a região secundária tenha concluído a replicação.

Por exemplo, suponha que o cliente envie uma atualização com êxito, mas a região primária falhe antes que a atualização seja propagada para a região secundária. Quando o cliente solicita a leitura dos dados novamente, eles recebem os dados obsoletos da região secundária, em vez dos dados atualizados. Ao projetar seu aplicativo, você deve decidir se isso é aceitável e, nesse caso, como você enviará uma mensagem ao cliente. 

Neste artigo, mostramos como verificar a Hora da Última Sincronização para os dados secundários para verificar se o secundário está atualizado.

### <a name="handling-services-separately-or-all-together"></a>Tratamento de serviços separadamente ou em conjunto

Embora não seja provável, é possível que um serviço não fique disponível enquanto os outros serviços ainda estão totalmente funcionais. Você pode lidar com as repetições e o modo somente leitura para cada serviço separadamente (blobs, filas, tabelas) ou pode lidar com as repetições de forma genérica para todos os serviços de armazenamento em conjunto.

Por exemplo, se usar filas e blobs no aplicativo, você poderá optar por incluir código separado para lidar com erros com nova tentativa para cada um desses itens. Em seguida, se você receber uma repetição do serviço blob, mas o serviço fila ainda estiver funcionando, somente a parte do aplicativo que lida com os blobs será afetada. Se você decidir lidar com todas as tentativas de serviço de armazenamento de forma genérica e uma chamada ao serviço blob retornar um erros com nova tentativa, as solicitações para o serviço blob e o serviço fila serão afetadas.

Em última análise, isso depende da complexidade do aplicativo. Você pode optar por não lidar com as falhas de serviço, mas, em vez disso, redirecionar as solicitações de leitura a todos os serviços de armazenamento para a região secundária e executar o aplicativo no modo somente leitura ao detectar um problema em qualquer serviço de armazenamento na região primária.

### <a name="other-considerations"></a>Outras considerações

Essas são as outras considerações que discutiremos no restante deste artigo.

*   Lidar com repetições de solicitações de leitura usando o padrão de Disjuntor

*   Dados eventualmente consistentes e a Hora da Última Sincronização

*   Testes

## <a name="running-your-application-in-read-only-mode"></a>Executando o aplicativo no modo somente leitura

Para usar o armazenamento de RA-GRS, você deve ser capaz de lidar com as solicitações de leitura com falha e as solicitações de atualização com falha (nesse caso, atualizações significam inserções, atualizações e exclusões). Se o data center primário falhar, as solicitações de leitura poderão ser redirecionadas para o data center secundário. No entanto, as solicitações de atualização não podem ser redirecionadas para o secundário porque o secundário é somente leitura. Por esse motivo, você precisa projetar o aplicativo para ser executado no modo somente leitura.

Por exemplo, você pode definir um sinalizador que é verificado antes que todas as solicitações de atualização sejam enviadas ao Armazenamento do Azure. Quando uma das solicitações de atualização chega, você pode ignorá-la e retornar uma resposta apropriada para o cliente. Talvez você até ainda queira desabilitar totalmente determinados recursos até que o problema seja resolvido e notificar os usuários de que esses recursos estão temporariamente indisponíveis.

Se optar por lidar com os erros em cada serviço separadamente, você também precisará lidar com a capacidade de executar o aplicativo no modo somente leitura pelo serviço. Por exemplo, você pode ter sinalizadores somente leitura para cada serviço que pode ser habilitado e desabilitado. Em seguida, você pode manipular o sinalizador nos lugares apropriados do código.

Ser capaz de executar o aplicativo no modo somente leitura tem outro benefício: a capacidade de garantir funcionalidade limitada durante uma atualização importante do aplicativo. Você pode disparar o aplicativo para ser executado no modo somente leitura e apontar para o data center secundário, garantindo que ninguém acessa os dados na região primária enquanto você faz atualizações.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Tratamento de atualizações ao executar no modo somente leitura

Há várias maneiras de lidar com solicitações de atualização durante a execução no modo somente leitura. Não abordaremos isso de forma abrangente, mas, em geral, há alguns padrões a serem considerados.

1.  Você pode responder ao usuário e informar que não está aceitando atualizações. Por exemplo, um sistema de gerenciamento de contatos pode habilitar os clientes a acessar informações de contato, mas não fazer atualizações.

2.  Você pode enfileirar as atualizações em outra região. Nesse caso, você gravaria as solicitações de atualização pendentes em uma fila em uma região diferente e teria uma maneira de processar essas solicitações depois que o data center primário ficasse online novamente. Nesse cenário, você deve informar ao cliente que a atualização solicitada está na fila para processamento posterior.

3.  Você pode gravar as atualizações em uma conta de armazenamento em outra região. Em seguida, quando o data center primário ficar online novamente, você poderá ter uma maneira de mesclar essas atualizações nos dados principais, dependendo da estrutura dos dados. Por exemplo, se estiver criando arquivos separados com um carimbo de data/hora no nome, você poderá copiar esses arquivos de volta para a região primária. Isso funciona para algumas cargas de trabalho, como dados de log e iOT.

## <a name="handling-retries"></a>Lidar com repetições

Como saber quais são os erros com nova tentativa? Isso é determinado pela biblioteca de cliente de armazenamento. Por exemplo, um erro 404 (recurso não encontrado) não é um erro com nova tentativa, pois isso provavelmente não resultará em sucesso. Por outro lado, um erro 500 é um erro com nova tentativa, pois ele é um erro de servidor e pode ser simplesmente um problema temporário. Para obter mais detalhes, confira [abrir o código-fonte para a classe ExponentialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) na biblioteca de cliente de armazenamento do .NET. (Procure o método ShouldRetry.)

### <a name="read-requests"></a>Solicitações de leitura

Solicitações de leitura poderão ser redirecionadas para o armazenamento secundário, se houver um problema no armazenamento primário. Conforme observado acima em [Usando dados eventualmente consistentes](#using-eventually-consistent-data), deve ser aceitável que o aplicativo potencialmente leia dados obsoletos. Se estiver usando a biblioteca de cliente de armazenamento para acessar dados de RA-GRS, você poderá especificar o comportamento de nova tentativa de uma solicitação de leitura definindo um valor para a propriedade **LocationMode** como um dos seguintes:

*   **PrimaryOnly** (o padrão)

*   **PrimaryThenSecondary**

*   **SecondaryOnly**

*   **SecondaryThenPrimary**

Quando você define **LocationMode** como **PrimaryThenSecondary**, se a solicitação de leitura inicial para o ponto de extremidade primário falhar com um erro com nova tentativa, o cliente fará automaticamente outra solicitação de leitura para o ponto de extremidade secundário. Se o erro for um tempo limite do servidor, o cliente terá que aguardar até que o tempo limite expire antes de receber um erro com nova tentativa do serviço.

Existem basicamente dois cenários a serem considerados ao decidir como reagir a um erro com nova tentativa:

*   Esse é um problema isolado, e solicitações subsequentes para o ponto de extremidade primário não retornarão um erro com nova tentativa. Um exemplo é quando há um erro de rede temporário.

    Nesse cenário, não há uma redução de desempenho significativa quando **LocationMode** é definido como **PrimaryThenSecondary**, pois isso ocorre apenas raramente.

*   Esse é um problema pelo menos para um dos serviços de armazenamento na região primária, e todas as solicitações subsequentes desse serviço na região primária provavelmente retornarão erros com nova tentativa por um período de tempo. Um exemplo disso é se a região primária está totalmente inacessível.

    Nesse cenário, há uma redução de desempenho porque todas as solicitações de leitura tentarão primeiro o ponto de extremidade primário, aguardarão até que o tempo limite expire e alternarão para o ponto de extremidade secundário.

Nesses cenários, você deve identificar que há um problema contínuo no ponto de extremidade primário e enviar todas as solicitações de leitura diretamente para o ponto de extremidade secundário, definindo a propriedade **LocationMode** como **SecondaryOnly**. Nesse momento, você também deve alterar o aplicativo para que seja executado no modo somente leitura. Essa abordagem é conhecida como [Padrão de Disjuntor](/azure/architecture/patterns/circuit-breaker).

### <a name="update-requests"></a>Solicitações de atualização

O padrão de Disjuntor também pode ser aplicado a solicitações de atualização. No entanto, as solicitações de atualização não poderão ser redirecionadas para o armazenamento secundário, que é somente leitura. Para essas solicitações, você deve deixar a propriedade **LocationMode** definida como **PrimaryOnly** (o padrão). Para lidar com esses erros, você pode aplicar uma métrica para essas solicitações (por exemplo, 10 falhas contínuas) e, quando o limite for atingido, alternar o aplicativo para o modo somente leitura. Você pode usar os mesmos métodos para retornar para o modo de atualização que são descritos abaixo na seção seguinte sobre o padrão de Disjuntor.

## <a name="circuit-breaker-pattern"></a>Padrão de Disjuntor

Usar o padrão de Disjuntor no aplicativo pode impedi-lo de repetir uma operação que provavelmente falhará repetidas vezes. Ele permite que o aplicativo continue a ser executado em vez de gastar tempo enquanto a operação é repetida exponencialmente. Ele também detecta quando a falha é corrigida e, nesse momento, o aplicativo pode tentar a operação novamente.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Como implementar o padrão de disjuntor

Para identificar um problema contínuo em um ponto de extremidade primário, você pode monitorar com que frequência o cliente encontra erros com nova tentativa. Como cada caso é diferente, você precisa decidir sobre o limite que deseja usar para a decisão de alternar para o ponto de extremidade secundário e executar o aplicativo no modo somente leitura. Por exemplo, você poderá decidir executar a mudança se houver 10 falhas contínuas sem sucesso. Outro exemplo é alternar se 90% das solicitações em um período de dois minutos falharem.

Para o primeiro cenário, você pode simplesmente manter uma contagem de falhas e, se houver êxito antes de atingir o máximo, definir a contagem como zero. Para o segundo cenário, uma maneira de implementá-lo é usar o objeto MemoryCache (no .NET). Para cada solicitação, adicione um CacheItem no cache, defina o valor para sucesso (1) ou falha (0) e defina o tempo de expiração de dois minutos a partir de agora (ou a restrição de tempo que desejar). Quando o tempo de expiração de uma entrada for atingido, a entrada será removida automaticamente. Isso lhe dará uma janela de dois minutos sem interrupção. Sempre que faz uma solicitação ao serviço de armazenamento, primeiro você usa uma consulta Linq no objeto MemoryCache para calcular a porcentagem de êxitos somando os valores e dividindo pela contagem. Quando a porcentagem de êxitos ficar abaixo de determinado limite (como 10%), defina a propriedade **LocationMode** para solicitações de leitura como **SecondaryOnly** e alterne o aplicativo para o modo somente leitura antes de continuar.

O limite de erros usado para determinar quando alternar pode variar dependendo do serviço no aplicativo. Portanto, você deve considerar torná-los parâmetros configuráveis. Esse também é o momento para decidir se erros com nova tentativa de cada serviço devem ser tratados separadamente ou em conjunto, conforme discutido anteriormente.

Outra consideração é como lidar com várias instâncias de um aplicativo e o que fazer ao detectar erros com nova tentativa em cada instância. Por exemplo, você pode ter 20 VMs em execução com o mesmo aplicativo carregado. Você lida com cada instância separadamente? Se uma instância começar a apresentar problemas, você deseja limitar a resposta para apenas se uma instância ou deseja tentar fazer com que todas as instâncias respondam da mesma forma quando uma instância tiver um problema? Tratar as instâncias separadamente é muito mais simples do que tentar coordenar a resposta entre elas, mas a maneira de fazer isso depende da arquitetura do aplicativo.

### <a name="options-for-monitoring-the-error-frequency"></a>Opções de monitoramento da frequência de erros

Você tem três opções principais para monitorar a frequência de novas tentativas na região primária para determinar quando passar para a região secundária e alterar o aplicativo para que seja executado no modo somente leitura.

*   Adicionar um manipulador para o evento [**Retrying**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.operationcontext.retrying) do objeto [**OperationContext**](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.context.operationcontext) que você passa para solicitações de armazenamento. Esse é o método apresentado neste artigo e usado no exemplo que o acompanha. Esses eventos são acionados sempre que o cliente tenta novamente uma solicitação, permitindo que você controle com que frequência o cliente encontra erros com nova tentativa em um ponto de extremidade primário.

    ```csharp 
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

*   No método [**Evaluate**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.iextendedretrypolicy.evaluate) em uma política de repetição personalizada, você pode executar código personalizado sempre que uma repetição ocorre. Além de gravação quando uma repetição ocorre, isso também lhe dá a oportunidade de modificar o comportamento de repetição.

    ```csharp 
    public RetryInfo Evaluate(RetryContext retryContext,
    OperationContext operationContext)
    {
        var statusCode = retryContext.LastRequestResult.HttpStatusCode;
        if (retryContext.CurrentRetryCount >= this.maximumAttempts
            || ((statusCode >= 300 && statusCode < 500 && statusCode != 408)
            || statusCode == 501 // Not Implemented
            || statusCode == 505 // Version Not Supported
            ))
        {
            // Do not retry
            return null;
        }

        // Monitor retries in the primary location
        ...

        // Determine RetryInterval and TargetLocation
        RetryInfo info =
            CreateRetryInfo(retryContext.CurrentRetryCount);

        return info;
    }
    ```

*   A terceira abordagem é implementar um componente de monitoramento personalizado no aplicativo que continuamente executa ping no ponto de extremidade de armazenamento primário com solicitações de leitura fictícias (como ler um blob pequeno) para determinar sua integridade. Isso deve exigir alguns recursos, mas não uma quantidade significativa. Quando é descoberto um problema que atinge o limite você executa a mudança para **SecondaryOnly** e o modo somente leitura.

Em algum momento, convém alternar de volta para o ponto de extremidade primário e permitir atualizações. Se usar um dos dois primeiros métodos listados acima, você poderá simplesmente alternar de novo para o ponto de extremidade primário e habilitar o modo de atualização após um período arbitrariamente selecionado ou a execução de determinado número de operações. Você pode permitir que ele percorra a lógica de repetição novamente. Se o problema tiver sido corrigido, ele continuará a usar o ponto de extremidade primário e a permitir atualizações. Se ainda houver um problema, ele alternará mais uma vez para o ponto de extremidade secundário e o modo somente leitura depois de ser reprovado nos critérios que você definiu.

Para o terceiro cenário, quando a execução de ping no ponto de extremidade de armazenamento primário tiver êxito novamente, você poderá alternar de volta para **PrimaryOnly** e continuar permitindo atualizações.

## <a name="handling-eventually-consistent-data"></a>Manipulação de dados eventualmente consistentes

RA-GRS funciona por meio da replicação de transações da região primária para a secundária. Esse processo de replicação garante que os dados na região secundária sejam *eventualmente consistentes*. Isso significa que todas as transações na região primária eventualmente aparecerão na região secundária, mas pode haver um atraso antes de aparecerem e não há garantia de que as transações chegarão na região secundária na mesma ordem que em que foram aplicadas originalmente na região primária. Se as transações chegarem na região secundária fora de ordem, você *poderá* considerar que os dados na região secundária estão em um estado inconsistente até que o serviço restabeleça a ordem.

A tabela a seguir mostra um exemplo do que pode acontecer quando você atualiza os detalhes de um funcionário para torná-los um membro do *administradores* função. Para este exemplo, isso requer que você atualize a entidade **funcionário** e atualize uma entidade **função de administrador** com uma contagem do número total de administradores. Observe como as atualizações são aplicadas fora de ordem na região secundária.

| **Hora** | **Transação**                                            | **Replicação**                       | **Hora da Última Sincronização** | **Resultado** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transação A: <br> Inserir funcionário <br> entidade no principal |                                   |                    | Transação A inserida no primário,<br> ainda não replicada. |
| T1       |                                                            | Transação A <br> replicada para<br> secundário | T1 | A transação A foi replicada para o secundário. <br>Hora da Última Sincronização atualizada.    |
| T2       | Transação B:<br>Atualizar<br> entidade de funcionário<br> no principal  |                                | T1                 | Transação B gravada no principal,<br> ainda não replicada.  |
| T3       | Transação C:<br> Atualizar <br>administrator<br>entidade de função em<br>primary |                    | T1                 | Transação C gravada no principal,<br> ainda não replicada.  |
| *T4*     |                                                       | Transação C <br>replicada para<br> secundário | T1         | Transação C replicada para o secundário.<br>LastSyncTime não atualizado porque <br>a transação B ainda não foi replicada.|
| *T5*     | Ler entidades <br>de secundário                           |                                  | T1                 | Você obtém o valor obsoleto para a entidade de funcionário <br> porque a transação B não foi <br> replicada ainda. Você obtém o novo valor para<br> a entidade de função de administrador porque C foi<br> replicada. A Hora da Última Sincronização ainda não<br> foi atualizada porque a transação B<br> não foi replicada. Você pode ver que a<br>entidade da função de administrador está inconsistente <br>porque a data/hora da entidade é posterior <br>à Hora da Última Sincronização. |
| *T6*     |                                                      | Transação B<br> replicada para<br> secundário | T6                 | *T6* – todas as transações até C <br>foram replicadas; a Hora da Última Sincronização<br> foi atualizada. |

Neste exemplo, suponha que o cliente alterne para leitura da região secundária em T5. Ele pode ler com êxito a entidade de **função de administrador** nesse momento, mas a entidade contém um valor para a contagem de administradores que não é consistente com o número de entidades **funcionário** que são marcadas como administradores na região secundária nesse momento. O cliente simplesmente pode exibir esse valor, com o risco de que se trata de informações inconsistentes. Como alternativa, o cliente pode tentar determinar que a **função de administrador** está em um estado potencialmente inconsistente porque as atualizações ocorreram fora de ordem e informar esse fato ao usuário.

Para reconhecer que ele tem dados potencialmente inconsistentes, o cliente pode usar o valor da *Hora da Última Sincronização*, que você pode obter a qualquer momento consultando um serviço de armazenamento. Isso indica a hora em que os dados na região secundária estiveram consistentes pela última vez e quando o serviço aplicou todas as transações antes desse ponto no tempo. No exemplo mostrado acima, após o serviço inserir a entidade **funcionário** na região secundária, a Hora da Última Sincronização é definida como *T1*. Ela permanece em *T1* até que as atualizações de serviço da entidade **funcionário** na região secundária, quando ela é definida como *T6*. Se o cliente recupera a hora da última sincronização ao ler a entidade em *T5*, pode compará-la ao carimbo de data/hora na entidade. Se o carimbo de data/hora na entidade é posterior à hora da última sincronização, a entidade está em um estado potencialmente inconsistente, e você pode tomar a ação apropriada para o aplicativo. Usar esse campo requer que você saiba quando a última atualização do primário foi concluída.

## <a name="getting-the-last-sync-time"></a>Obtendo a hora da última sincronização

Você pode usar o PowerShell ou CLI do Azure para recuperar a hora da última sincronização para determinar quando os dados foi gravados pela última vez no secundário.

### <a name="powershell"></a>PowerShell

Para obter a hora da última sincronização da conta de armazenamento usando o PowerShell, verifique a conta de armazenamento **GeoReplicationStats.LastSyncTime** propriedade. Lembre-se de substituir os valores de espaço reservado com seus próprios valores:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

### <a name="azure-cli"></a>CLI do Azure

Para obter a hora da última sincronização da conta de armazenamento usando a CLI do Azure, verifique a conta de armazenamento **geoReplicationStats.lastSyncTime** propriedade. Use o `--expand` parâmetro para retornar valores para as propriedades aninhadas abaixo **geoReplicationStats**. Lembre-se de substituir os valores de espaço reservado com seus próprios valores:

```azurecli
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

## <a name="testing"></a>Testes

É importante testar se o aplicativo se comporta conforme o esperado ao encontra erros com nova tentativa. Por exemplo, você precisa testar se o aplicativo alterna para o secundário e o modo somente leitura ao detectar um problema e alterna de volta quando a região primária fica disponível novamente. Para fazer isso, você precisa de uma maneira de simular erros com nova tentativa e controlar com que frequência eles ocorrem.

Você pode usar o [Fiddler](https://www.telerik.com/fiddler) para interceptar e modificar respostas HTTP em um script. Esse script pode identificar as respostas que vêm do ponto de extremidade primário e alterar o código de status HTTP de forma que a Biblioteca de Cliente de Armazenamento o reconheça como um erros com nova tentativa. Este snippet de código mostra um exemplo simples de um script do Fiddler que intercepta as respostas para ler as solicitações em relação à tabela **employeedata** para retornar um status 502:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Você pode estender esse exemplo para interceptar uma maior gama de solicitações e alterar apenas o **responseCode** em alguns deles para simular melhor um cenário do mundo real. Para obter mais informações sobre como personalizar os scripts do Fiddler, confira [Modificando uma solicitação ou resposta](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) na documentação do Fiddler.

Se você tiver tornado configuráveis os limites para alternar o aplicativo para o modo somente leitura, será mais fácil testar o comportamento com volumes de transações de não produção.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a Redundância Geográfica com Acesso de Leitura, incluindo outro exemplo de como LastSyncTime é definido, consulte [Opções de redundância de armazenamento do Microsoft Azure e o Armazenamento com Redundância Geográfica com Acesso de Leitura](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

* Para obter um exemplo completo que mostra como fazer a alternância entre os pontos de extremidade primário e secundário, confira [Exemplos do Azure – usando o padrão de Disjuntor com o armazenamento de RA-GRS](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
