---
title: Recuperação de desastre e distribuição geográfica em Funções Duráveis - Azure
description: Saiba mais sobre recuperação de desastre e distribuição geográfica em Funções Duráveis.
services: functions
author: MS-Santi
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 1363dd3c620789b9f3c8ce1dbe0892ee61d66051
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741321"
---
# <a name="disaster-recovery-and-geo-distribution"></a>Recuperação de desastres e distribuição geográfica

## <a name="overview"></a>Visão geral

Nas Durable Functions, todos os estados são persistentes no Armazenamento do Azure. Um [hub de tarefas](durable-functions-task-hubs.md) é um contêiner lógico dos recursos do Armazenamento do Azure usado para orquestrações. Funções de orquestrador e atividade só podem interagir entre si quando pertencem ao mesmo hub de tarefas.
Os cenários descritos propõem opções de implantação para aumentar a disponibilidade e minimizar o tempo de inatividade durante as atividades de recuperação de desastre.

É importante observar que esses cenários são baseados nas configurações ativa-passiva, pois são guiados pelo uso do Armazenamento do Azure. Esse padrão consiste em implantar um aplicativo de função (passivo) de backup em uma região diferente. O Gerenciador de Tráfego monitorará o aplicativo de função (ativo) principal quanto à disponibilidade. Ele fará failover para o aplicativo de função de backup se o principal falhar. Para obter mais informações, consulte [Método de roteamento de tráfego por prioridade](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method) do [Gerenciador de Tráfego](https://azure.microsoft.com/services/traffic-manager/).

>[!NOTE]
>
> - A configuração ativa-passiva proposta garante que um cliente sempre possa disparar novas orquestrações por meio de HTTP. No entanto, como consequência de haver dois aplicativos de função compartilhando o mesmo armazenamento, o processamento em segundo plano será distribuído entre ambos, competindo por mensagens nas mesmas filas. Essa configuração incorre em custos de saída adicionais para o aplicativo de função secundário.
> - O hub de tarefas e a conta de armazenamento subjacente são criados na região primária e são compartilhadas por ambos os aplicativos de função.
> - Todos os aplicativos de função que são implantados de forma redundante devem compartilhar as mesmas chaves de acesso de função caso sejam ativados por meio de HTTP. O Tempo de Execução de Funções expõe uma [API de gerenciamento](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) que permite que os consumidores adicionem, excluam e atualizem as teclas de função programaticamente.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Cenário 1 - Computação com balanceamento de carga com armazenamento compartilhado

Se a infraestrutura de computação no Azure falhar, o aplicativo de função poderá se tornar indisponível. Para minimizar a possibilidade desse tempo de inatividade, este cenário usa dois aplicativos de função implantados em diferentes regiões.
O Gerenciador de Tráfego está configurado para detectar problemas no aplicativo de função principal e redirecionar automaticamente o tráfego para o aplicativo de função na região secundária. Este aplicativo de função compartilha a mesma conta do Armazenamento do Azure e o Hub de Tarefas. Portanto, o estado dos aplicativos de função não é perdido e o trabalho pode continuar normalmente. Depois que a integridade for restaurada para a região principal, o Gerenciador de Tráfego do Microsoft Azure começará a rotear solicitações para esse aplicativo de função automaticamente.

![Diagrama mostrando o cenário 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Há vários benefícios ao usar esse cenário de implantação:

- Se a infraestrutura de computação falhar, o trabalho poderá retomar na região com failover sem perda de estado.
- O Gerenciador de Tráfego cuida do failover automático para o aplicativo de função íntegro automaticamente.
- O Gerenciador de Tráfego restabelece automaticamente o tráfego para o aplicativo de função primária depois que a interrupção é corrigida.

No entanto, nesse cenário, considere:

- Se o aplicativo de função for implantado usando um plano do Serviço de Aplicativo dedicado, a replicação da infraestrutura de computação no datacenter de failover aumenta os custos.
- Esse cenário aborda interrupções na infraestrutura de computação, mas a conta de armazenamento continua sendo o único ponto de falha para o aplicativo de função. Se houver uma interrupção do Armazenamento, o aplicativo sofre com tempo de inatividade.
- Se o aplicativo de função está em failover, haverá um aumento da latência porque ele irá acessar sua conta de armazenamento entre regiões.
- O acesso ao serviço de armazenamento por uma região diferente daquela onde ele está localizado incorre em custos devido ao tráfego de saída da rede.
- Esse cenário depende do Gerenciador de Tráfego. Considerando [como o Gerenciador de Tráfego funciona](../../traffic-manager/traffic-manager-how-it-works.md), pode levar algum tempo até que um aplicativo cliente que consuma uma Função Durável precise consultar novamente o endereço do aplicativo de função do Gerenciador de Tráfego.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Cenário 2 - Computação com balanceamento de carga com armazenamento regional

O cenário anterior abrange apenas falhas na infraestrutura de computação. Se o serviço de armazenamento falhar, resultará em uma interrupção do aplicativo de função.
Para garantir a operação contínua das funções duráveis, esse cenário usa uma conta de armazenamento local em cada região à qual os aplicativos de função são implantados.

![Diagrama mostrando o cenário 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Essa abordagem adiciona aprimoramentos ao cenário anterior:

- Se o aplicativo de função falhar, o Gerenciador de Tráfego cuidará de fazer failover para a região secundária. No entanto, como o aplicativo de função conta com a sua própria conta de armazenamento, as funções duráveis continuam a funcionar.
- Durante um failover, não há nenhuma latência adicional na região com failover, pois o aplicativo de função e a conta de armazenamento estão em uma mesma localização.
- A falha da camada de armazenamento causará falhas nas funções duráveis, o que, por sua vez, disparará um redirecionamento para a região com failover. Novamente, como o aplicativo de função e o armazenamento são isolados por região, as funções duráveis continuarão a funcionar.

Considerações importantes para esse cenário:

- Se o aplicativo de função for implantado usando um plano do Serviço de Aplicativo dedicado, a replicação da infraestrutura de computação no datacenter de failover aumenta os custos.
- O estado atual não apresenta failover, o que significa que funções em execução e com ponto de verificação falharão. O aplicativo cliente é responsável por repetir/reiniciar o trabalho.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Cenário 3 - Computação com balanceamento de carga com armazenamento GRS compartilhado

Esse cenário é uma modificação do primeiro cenário, implementando uma conta de armazenamento compartilhado. A principal diferença é que a conta de armazenamento é criada com replicação geográfica habilitada.
Funcionalmente, este cenário fornece as mesmas vantagens do Cenário 1, mas permite vantagens adicionais de recuperação de dados:

- O GRS (armazenamento com redundância geográfica) e o RA-GRS (armazenamento com redundância geográfica com acesso de leitura) maximizam a disponibilidade para sua conta de armazenamento.
- Se houver uma interrupção na região do serviço de armazenamento, uma das possibilidades é que as operações do datacenter determinarão que o armazenamento deve realizar failover para a região secundária. Nesse caso, o acesso à conta de armazenamento será redirecionado de forma transparente para a cópia replicada geograficamente da conta de armazenamento, sem intervenção do usuário.
- Nesse caso, o estado das funções duráveis será preservado até a última replicação da conta de armazenamento, o que ocorre a cada poucos minutos.

Assim como acontece com os outros cenários, há considerações importantes:

- A execução de failover para a réplica é feita por operadores do datacenter e pode levar algum tempo. Até lá, o aplicativo de função sofrerá uma interrupção.
- Há um custo maior para usar contas de armazenamento com replicação geográfica.
- O GRS ocorre de forma assíncrona. Algumas das transações mais recentes podem ser perdidas devido à latência do processo de replicação.

![Diagrama mostrando o cenário 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>Próximas etapas

Você pode ler mais sobre [Projetar aplicativos altamente disponíveis usando RA-GRS](../../storage/common/storage-designing-ha-apps-with-ragrs.md)
