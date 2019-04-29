---
title: Visão geral do Serviço de Análise de Falha | Microsoft Docs
description: Este artigo descreve o Serviço de Análise de Falha no Service Fabric para induzir falhas e executar cenários de teste em seus serviços.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: chackdan
editor: vturecek
ms.assetid: 1f064276-293a-4989-a513-e0d0b9fdf703
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: 3581550779b2387515b4f300d211b4e0a894edc7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544800"
---
# <a name="introduction-to-the-fault-analysis-service"></a>Introdução ao Serviço de Análise de Falha
O Serviço de Análise de Falha foi desenvolvido para testar serviços criados com base no Microsoft Azure Service Fabric. Com o Serviço de Análise de Falha, é possível induzir falhas significativas e executar cenários de teste completos para seus aplicativos. Esses cenários e falhas praticam e validam os vários estados e transições pelos quais um serviço passa durante seu tempo de vida, tudo de maneira consistente, segura e controlada.

Ações são as falhas individuais visando um serviço com o objetivo de testá-lo. Um desenvolvedor de serviço pode usá-las como blocos de construção para escrever cenários complicados. Por exemplo: 

* Reinicie um nó para simular qualquer quantidade de situações nas quais um computador ou VM é reinicializada.
* Mova uma réplica de seu serviço com estado para simular o balanceamento de carga, failover ou atualização do aplicativo.
* Invoque a perda de quorum em um serviço com estado para criar uma situação na qual não é possível realizar operações de gravação, pois não há réplicas "secundárias" ou de "backup" suficientes para aceitar novos dados.
* Invoque a perda de dados em um serviço com estado para criar uma situação na qual todo o estado na memória é completamente apagado.

Os cenários são operações complexas compostas de uma ou mais ações. O Serviço de Análise de Falha fornece dois cenários internos completos:

* Cenário de caos
* Cenário de failover

## <a name="testing-as-a-service"></a>Teste como serviço
O Serviço de Análise de Falha é um sistema do Service Fabric iniciado automaticamente com um cluster do Service Fabric. Este serviço age como o host para injeção de falha, execução de cenário de teste e análise de integridade. 

![Serviço de Análise de Falha][0]

Quando uma ação de falha ou cenário de teste é iniciado, um comando é enviado para o Serviço de Análise de Falha para executar o cenário de teste ou a ação de falha. O Serviço de Análise de Falha é um serviço com estado, por isso ele pode executar suas falhas e cenários de forma confiável e validar os resultados. Por exemplo, um cenário de teste de longa execução pode ser executado de forma confiável pelo Serviço de Análise de Falha. E como os testes estão sendo executados dentro do cluster, o serviço pode examinar o estado do cluster e os serviços para fornecer informações mais detalhadas sobre falhas.

## <a name="testing-distributed-systems"></a>Testando sistemas distribuídos
A Malha do Serviço facilita muito o trabalho de escrever e gerenciar aplicativos escalonáveis distribuídos. O Serviço de Análise de Falha facilita da mesma forma o teste de um aplicativo distribuído. Há três problemas principais que precisam ser resolvidos durante o teste:

1. Simulação/geração de falhas que podem ocorrer em cenários do mundo real: Um dos aspectos importantes do Service Fabric é que ele permite que aplicativos distribuídos se recuperem de várias falhas. No entanto, a fim de testar se o aplicativo é capaz de recuperar-se dessas falhas, precisamos de um mecanismo para simular/gerar essas falhas reais em um ambiente de teste controlado.
1. A capacidade de gerar falhas correlacionadas: Falhas básicas no sistema, como falhas de rede e de máquina, são fáceis de produzir individualmente. Gerar uma quantidade considerável de cenários que podem ocorrer no mundo real como resultado de interações dessas falhas individuais não é algo simples.
1. Experiência unificada em vários níveis de desenvolvimento e implantação: Há muitos sistemas de injeção de falhas que podem executar vários tipos de falhas. No entanto, a experiência em todos eles é ruim ao mudar de um cenário de desenvolvedor one box para a execução dos mesmos testes em ambientes de teste de grande porte a fim de usá-los para teste em produção.

Embora haja muitos mecanismos para resolver esses problemas, falta um sistema que faça o mesmo com as garantias exigidas completamente, desde o ambiente de desenvolvedor one box até o teste em clusters de produção. O Serviço de Análise de Falha ajuda o desenvolvedor de aplicativos a se concentrar no teste da sua lógica de negócios. O Serviço de Análise de Falha fornece todos os recursos necessários para testar a interação do serviço com o sistema distribuído subjacente.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Simulação/geração de cenários reais de falha
A fim de testar a robustez de um sistema distribuído contra falhas, precisamos de um mecanismo para gerar falhas. Embora, teoricamente, seja fácil gerar uma falha, a desativação de um nó, por exemplo, isso começará a atingir o mesmo conjunto de problemas de consistência que o Service Fabric está tentando resolver. Por exemplo, se quisermos desativar um nó, o fluxo de trabalho necessário será o seguinte:

1. No cliente, emita uma solicitação de desligamento do nó.
1. Envie a solicitação ao nó correto.
   
     a. Se o nó não for encontrado, a solicitação falhará.
   
    b. Se o nó for encontrado, haverá retorno apenas se ele for desligado.

Para verificar a falha de uma perspectiva de teste, é necessário saber que quando essa falha é induzida, ela realmente acontece. A garantia que o Service Fabric fornece é que ou o nó ficará inativo ou já estava inativo quando foi atingido pelo comando. Em ambos os casos, o teste deve conseguir justificar corretamente o estado e obter êxito ou falhar corretamente em sua validação. Um sistema implementado fora do Service Fabric que realize o mesmo conjunto de falhas pode gerar muitos problemas de rede, hardware e software, o que impediria o fornecimento das garantias precedentes. Na presença dos problemas mencionados anteriormente, o Service Fabric reconfigurará o estado do cluster para solucionar os problemas e, portanto, o Serviço de Análise de Falha ainda será capaz de fornecer o conjunto certo de garantias.

### <a name="generating-required-events-and-scenarios"></a>Gerando os eventos e cenários necessários
Apesar da dificuldade de simular uma falha real de forma consistente, a capacidade de gerar falhas correlacionadas é ainda mais difícil. Por exemplo, uma perda de dados ocorre em um serviço persistente com estado quando isto acontece:

1. Somente um quorum de gravação das réplicas é capturado na replicação. Todas as réplicas secundárias ficam atrasadas com relação à primária.
1. O quorum de gravação falha devido à falha das réplicas (devido a um pacote de códigos ou desativação do nó).
1. O quorum de gravação não pode voltar, pois ocorre a perda dos dados para as réplicas (devido à corrupção do disco nova imagem do computador).

Essas falhas correlacionadas ocorrem no mundo real, mas não com tanta frequência quanto as falhas individuais. A Possibilidade de Teste desses cenários antes que eles ocorram na produção é essencial. Ainda mais importante é a capacidade de simular esses cenários com cargas de trabalho de produção em circunstâncias controladas (no meio do dia com todos os engenheiros a postos). Isso é muito melhor do que ocorrer pela primeira vez em produção às 2h00 da manhã.

### <a name="unified-experience-across-different-environments"></a>Experiência unificada em ambientes diferentes
Tradicionalmente, a prática tem sido criar três conjuntos diferentes de experiências, um para o ambiente de desenvolvimento, um para testes e outro para produção. O modelo era:

1. No ambiente de desenvolvimento, produzir as transições de estado que permitem testes de unidade de métodos individuais.
1. No ambiente de teste, produzir falhas a fim de permitir testes completos que usem vários cenários de falha.
1. Manter o ambiente de produção original para impedir qualquer falha não natural e garantindo a existência de uma resposta humana extremamente rápida às falhas.

No Service Fabric, por meio do Serviço de Análise de Falha, estamos propondo a solução desse problema a fim de usar a mesma metodologia desde o ambiente de desenvolvimento até a produção. Há duas maneiras de fazer isso:

1. Para induzir as falhas controladas, use as APIs do Serviço de Análise de Falha de um ambiente de caixa única até os clusters de produção.
1. Para agitar o cluster, gerando a indução automática de falhas, use o Serviço de Análise de Falha para gerar falhas automáticas. O controle da taxa de falhas por meio da configuração possibilita que o mesmo serviço seja testado de maneira diferente em ambientes diferentes.

Com o Service Fabric, embora haja diferença na escala das falhas em ambientes diferentes, os mecanismos reais seriam idênticos. Isso permite um pipeline de código até implantação muito mais rápido e a capacidade de testar os serviços com cargas reais.

## <a name="using-the-fault-analysis-service"></a>Usando o Serviço de Análise de Falha
**C#**

Recursos do Serviço de Análise de Falha estão no namespace System.Fabric no pacote NuGet Microsoft.ServiceFabric. Para usar os recursos do Serviço de Análise de Falha, inclua o pacote nuget como uma referência em seu projeto.

**PowerShell**

Para usar o PowerShell, você deve instalar o SDK do Service Fabric. Depois da instalação do SDK, o módulo ServiceFabric PowerShell é automaticamente carregado para você usar.

## <a name="next-steps"></a>Próximas etapas
Para realmente criar serviços em escala de nuvem, é essencial garantir, antes e após a implantação, que os serviços possam dar suporte a falhas reais. No mundo dos serviços atual, a capacidade de inovar e mover rapidamente o código para produção é muito importante. O Serviço de Análise de Falha ajuda os desenvolvedores de serviço a fazer exatamente isso.

Comece testando seus aplicativos e serviços usando os [cenários de teste](service-fabric-testability-scenarios.md) internos ou crie seus próprios cenários de teste usando as [ações de falha](service-fabric-testability-actions.md) fornecidas pelo Serviço de Análise de Falha.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
