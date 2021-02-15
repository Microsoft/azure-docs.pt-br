---
title: Recuperação de desastre e distribuição geográfica do Azure Durable Functions
description: Saiba mais sobre recuperação de desastre e distribuição geográfica em Funções Duráveis.
author: MS-Santi
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: azfuncdf
ms.openlocfilehash: 01c400f51cce85ef39e9d39bcad1221253c6942d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89071203"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Recuperação de desastre e distribuição geográfica no Azure Durable Functions

A Microsoft se empenha em garantir que os serviços do Azure estejam sempre disponíveis. No entanto, podem ocorrer interrupções imprevistas do serviço. Se seu aplicativo exigir resiliência, a Microsoft recomenda configurar seu aplicativo para redundância geográfica. Além disso, os clientes devem dispor de um plano de recuperação de desastre para lidar com uma interrupção regional do serviço. Uma parte importante de um plano de recuperação de desastre está se preparando para fazer failover para a réplica secundária de seu aplicativo e armazenamento caso a réplica primária fique indisponível.

Em Durable Functions, todos os Estados são persistidos no armazenamento do Azure por padrão. Um [Hub de tarefas](durable-functions-task-hubs.md) é um contêiner lógico para recursos de armazenamento do Azure que são usados para [orquestrações](durable-functions-types-features-overview.md#orchestrator-functions) e [entidades](durable-functions-types-features-overview.md#entity-functions). As funções de orquestrador, atividade e entidade só podem interagir umas com as outras quando pertencem ao mesmo Hub de tarefas. Este documento fará referência aos hubs de tarefas ao descrever cenários para manter esses recursos de armazenamento do Azure altamente disponíveis.

Orquestrações e entidades podem ser disparadas usando [funções de cliente](durable-functions-types-features-overview.md#client-functions) que são disparadas via http ou um dos outros tipos de gatilho de Azure Functions com suporte. Eles também podem ser disparados usando [APIs http internas](durable-functions-http-features.md#built-in-http-apis). Para simplificar, este artigo se concentrará em cenários que envolvem o armazenamento do Azure e gatilhos de função baseados em HTTP e opções para aumentar a disponibilidade e minimizar o tempo de inatividade durante as atividades de recuperação de desastre Outros tipos de gatilho, como o barramento de serviço ou gatilhos de Cosmos DB, não serão explicitamente cobertos.

Os cenários a seguir se baseiam em configurações de Active-Passive, já que elas são guiadas pelo uso do armazenamento do Azure. Esse padrão consiste em implantar um aplicativo de função (passivo) de backup em uma região diferente. O Gerenciador de tráfego monitorará o aplicativo de funções primário (ativo) para disponibilidade de HTTP. Ele fará failover para o aplicativo de função de backup se o principal falhar. Para obter mais informações, consulte o método de [Traffic-Routing de prioridade](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method) do [Gerenciador de tráfego do Azure](https://azure.microsoft.com/services/traffic-manager/).

> [!NOTE]
> - A configuração ativa-passiva proposta garante que um cliente sempre possa disparar novas orquestrações por meio de HTTP. No entanto, como consequência de ter dois aplicativos de função compartilhando o mesmo Hub de tarefas no armazenamento, algumas transações de armazenamento em segundo plano serão distribuídas entre ambos. Portanto, essa configuração incorre em alguns custos de saída adicionais para o aplicativo de funções secundário.
> - O hub de tarefas e a conta de armazenamento subjacente são criados na região primária e são compartilhadas por ambos os aplicativos de função.
> - Todos os aplicativos de funções que são implantados com redundância devem compartilhar as mesmas chaves de acesso de função no caso de serem ativados via HTTP. O Runtime de Funções expõe uma [API de gerenciamento](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) que permite que os consumidores adicionem, excluam e atualizem as teclas de função programaticamente. O gerenciamento de chaves também é possível usando [APIs de Azure Resource Manager](https://www.markheath.net/post/managing-azure-functions-keys-2).

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Cenário 1 - Computação com balanceamento de carga com armazenamento compartilhado

Se a infraestrutura de computação no Azure falhar, o aplicativo de função poderá se tornar indisponível. Para minimizar a possibilidade desse tempo de inatividade, este cenário usa dois aplicativos de função implantados em diferentes regiões.
O Gerenciador de Tráfego está configurado para detectar problemas no aplicativo de função principal e redirecionar automaticamente o tráfego para o aplicativo de função na região secundária. Este aplicativo de função compartilha a mesma conta do Armazenamento do Azure e o Hub de Tarefas. Portanto, o estado dos aplicativos de função não é perdido e o trabalho pode continuar normalmente. Depois que a integridade for restaurada para a região principal, o Gerenciador de Tráfego do Microsoft Azure começará a rotear solicitações para esse aplicativo de função automaticamente.

![Diagrama mostrando o cenário 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Há vários benefícios ao usar esse cenário de implantação:

- Se a infraestrutura de computação falhar, o trabalho poderá continuar na região de failover sem perda de dados.
- O Gerenciador de tráfego cuida do failover automático para o aplicativo de função íntegro automaticamente.
- O Gerenciador de Tráfego restabelece automaticamente o tráfego para o aplicativo de função primária depois que a interrupção é corrigida.

No entanto, nesse cenário, considere:

- Se o aplicativo de funções for implantado usando um plano do serviço de aplicativo dedicado, a replicação da infraestrutura de computação no datacenter de failover aumentará os custos.
- Esse cenário aborda interrupções na infraestrutura de computação, mas a conta de armazenamento continua sendo o único ponto de falha para o aplicativo de função. Se ocorrer uma interrupção de armazenamento, o aplicativo sofrerá tempo de inatividade.
- Se o aplicativo de função está em failover, haverá um aumento da latência porque ele irá acessar sua conta de armazenamento entre regiões.
- O acesso ao serviço de armazenamento por uma região diferente daquela onde ele está localizado incorre em custos devido ao tráfego de saída da rede.
- Esse cenário depende do Gerenciador de Tráfego. Considerando [como o Gerenciador de Tráfego funciona](../../traffic-manager/traffic-manager-how-it-works.md), pode levar algum tempo até que um aplicativo cliente que consuma uma Função Durável precise consultar novamente o endereço do aplicativo de função do Gerenciador de Tráfego.

> [!NOTE]
> A partir do **v 2.3.0** da extensão de durable functions, dois aplicativos de funções podem ser executados com segurança ao mesmo tempo com a mesma conta de armazenamento e configuração do hub de tarefas. O primeiro aplicativo a ser iniciado obterá uma concessão de blob no nível do aplicativo que impede que outros aplicativos roubem mensagens das filas do hub de tarefas. Se esse primeiro aplicativo parar de ser executado, sua concessão expirará e poderá ser adquirida por um segundo aplicativo, o que continuará a processar mensagens do hub de tarefas.
> 
> Antes do v 2.3.0, os aplicativos de função configurados para usar a mesma conta de armazenamento processarão mensagens e atualizarão os artefatos de armazenamento simultaneamente, resultando em latência geral e custos de egresso muito mais altos. Se os aplicativos primários e de réplica tiverem um código diferente implantado para eles, mesmo temporariamente, as orquestrações também poderiam falhar em executar corretamente devido às inconsistências de função do orquestrador entre os dois aplicativos. Portanto, é recomendável que todos os aplicativos que exigem distribuição geográfica para fins de recuperação de desastre usam v 2.3.0 ou superior da extensão durável.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Cenário 2 - Computação com balanceamento de carga com armazenamento regional

O cenário anterior abrange apenas falhas na infraestrutura de computação. Se o serviço de armazenamento falhar, resultará em uma interrupção do aplicativo de função.
Para garantir a operação contínua das funções duráveis, esse cenário usa uma conta de armazenamento local em cada região à qual os aplicativos de função são implantados.

![Diagrama mostrando o cenário 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Essa abordagem adiciona aprimoramentos ao cenário anterior:

- Se o aplicativo de função falhar, o Gerenciador de Tráfego cuidará de fazer failover para a região secundária. No entanto, como o aplicativo de função conta com a sua própria conta de armazenamento, as funções duráveis continuam a funcionar.
- Durante um failover, não há nenhuma latência adicional na região de failover, pois o aplicativo de funções e a conta de armazenamento estão colocalizados.
- A falha da camada de armazenamento causará falhas nas funções duráveis que, por sua vez, disparará um redirecionamento para a região de failover. Novamente, como o aplicativo de função e o armazenamento são isolados por região, as funções duráveis continuarão a funcionar.

Considerações importantes para esse cenário:

- Se o aplicativo de funções for implantado usando um plano do serviço de aplicativo dedicado, a replicação da infraestrutura de computação no datacenter de failover aumentará os custos.
- O estado atual não faz failover, o que implica que as orquestrações e entidades existentes serão efetivamente pausadas e não estarão disponíveis até que a região primária seja recuperada.

Para resumir, a compensação entre o primeiro e o segundo cenário é que a latência é preservada e os custos de saída são minimizados, mas as orquestrações existentes e as entidades ficarão indisponíveis durante o tempo de inatividade. Se essas compensações são aceitáveis depende dos requisitos do aplicativo.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Cenário 3 - Computação com balanceamento de carga com armazenamento GRS compartilhado

Esse cenário é uma modificação do primeiro cenário, implementando uma conta de armazenamento compartilhado. A principal diferença é que a conta de armazenamento é criada com a replicação geográfica habilitada.
Funcionalmente, este cenário fornece as mesmas vantagens do Cenário 1, mas permite vantagens adicionais de recuperação de dados:

- O GRS (armazenamento com redundância geográfica) e o RA-GRS (armazenamento com redundância geográfica com acesso de leitura) maximizam a disponibilidade para sua conta de armazenamento.
- Se houver uma interrupção regional do serviço de armazenamento, você poderá [iniciar manualmente um failover para a réplica secundária](../../storage/common/storage-initiate-account-failover.md). Em circunstâncias extremas em que uma região for perdida devido a um desastre significativo, a Microsoft poderá iniciar um failover regional. Nesse caso, nenhuma ação sua é necessária.
- Quando ocorre um failover, o estado das funções duráveis será preservado até a última replicação da conta de armazenamento, que geralmente ocorre a cada poucos minutos.

Assim como acontece com os outros cenários, há considerações importantes:

- Um failover para a réplica pode levar algum tempo. Até que o failover seja concluído e os registros de DNS do armazenamento do Azure tenham sido atualizados, o aplicativo de funções sofrerá uma interrupção.
- Há um custo maior para usar contas de armazenamento com replicação geográfica.
- A replicação do GRS copia os dados de forma assíncrona. Algumas das transações mais recentes podem ser perdidas devido à latência do processo de replicação.

![Diagrama mostrando o cenário 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

> [!NOTE]
> Conforme descrito no cenário 1, é altamente recomendável que os aplicativos de funções implantados com essa estratégia usem **v 2.3.0** ou superior da extensão Durable functions.

Para obter mais informações, consulte a documentação de [failover de conta de armazenamento e recuperação de desastre do armazenamento do Azure](../../storage/common/storage-disaster-recovery-guidance.md) .

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre como criar aplicativos altamente disponíveis no armazenamento do Azure](../../storage/common/geo-redundant-design.md)
