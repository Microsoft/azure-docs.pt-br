---
title: Introdução aos microsserviços no Azure | Microsoft Docs
description: Uma visão geral sobre por que criar aplicativos de nuvem com uma abordagem de microsserviços é importante para o desenvolvimento moderno de aplicativos e como o Azure Service Fabric oferece uma plataforma para conseguir isso.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: fae2be85-0ab4-4cd3-9d1f-e0d95fe1959b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: atsenthi
ms.openlocfilehash: 5bcb52165c7cae18b807eff03c80b51eae8e2717
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204802"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Por que usar uma abordagem de microsserviços para construir aplicativos?

Para desenvolvedores de software, decomposição de um aplicativo em partes componentes é nada de novo. Normalmente, uma abordagem em camadas é usada, com um armazenamento de back-end, lógica de negócios de camada intermediária e uma interface de front-end do usuário (UI). O que *tem* alteradas ao longo dos últimos anos é que os desenvolvedores criando aplicativos distribuídos para a nuvem.

Aqui estão alguns crescentes necessidades do negócio:

* Um serviço que desenvolveu e opera em escala para alcançar clientes em novas regiões geográficas.
* Fornecimento mais rápido de recursos e funcionalidades para responder às demandas dos clientes de forma ágil.
* Melhor utilização de recursos para reduzir os custos.

Essas necessidades comerciais estão afetando o *modo* como criamos os aplicativos.

Para obter mais informações sobre a abordagem do Azure aos microsserviços, consulte [Microsserviços: Uma revolução de aplicativo proporcionada pela nuvem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservices-design-approach"></a>Monolítica versus a abordagem de design de microsserviços

Aplicações evoluem ao longo do tempo. Os aplicativos bem-sucedidos evoluem sendo úteis às pessoas. Aplicativos sem sucesso não evoluem e eventualmente são preteridos. Aqui está a pergunta: quanto você sabe sobre seus requisitos de hoje e o que eles serão no futuro? Por exemplo, digamos que você está criando um aplicativo de relatório para um departamento na sua empresa. Você está se o aplicativo se aplica somente dentro do escopo de sua empresa e que os relatórios não serão mantidos longo. Sua abordagem deverá ser diferente de, digamos, criando um serviço que fornece o conteúdo de vídeo para dezenas de milhões de clientes.

Às vezes, lançar algo como uma prova de conceito é o fator determinante. Você sabe que o aplicativo pode ser reprojetado posteriormente. Há muito sentido em trabalhar muito em algo que nunca é usado. Por outro lado, quando as empresas compilar para a nuvem, a expectativa é de crescimento e uso. Crescimento e o dimensionamento são imprevisíveis. Gostaríamos de protótipo rapidamente, sabendo também que estamos em um caminho que pode lidar com sucesso no futuro. Essa é a abordagem de inicialização simples: compilar, medir, aprender e iterar.

Durante a era do cliente/servidor, tendemos a nos concentrar na criação de aplicativos em camadas usando tecnologias específicas em cada camada. O termo *monolítico* aplicativo surgiu para descrever essas abordagens. As interfaces tendiam a ficar entre as camadas e um design mais rigidamente acoplado era usado entre os componentes em cada camada. Os desenvolvedores projetaram e fatoraram classes que foram compiladas em bibliotecas e vincularam em alguns arquivos executáveis e DLLs.

Há benefícios em uma abordagem de design monolítico. Aplicativos monolíticos costumam ser mais simples de projetar e chamadas entre os componentes são mais rápidas, porque essas chamadas geralmente são sobre a comunicação entre processos (IPC). Além disso, todos testam um único produto, o que tende a ser um uso mais eficiente de recursos humanos. A desvantagem é que há um acoplamento rígido entre camadas em camadas, e não é possível dimensionar componentes individuais. Se você precisar fazer correções ou atualizações, você precisa aguardar para que outras pessoas concluam o teste. É mais difícil de ser ágil.

Microsserviços resolvem essas desvantagens e ficarem mais alinham com os requisitos de negócios anteriores. Mas também têm vantagens e desvantagens. Os benefícios dos micros serviços são que cada um normalmente encapsula uma funcionalidade de negócios mais simples, que você pode ampliar, testar, implantar e gerenciar de forma independente. Uma vantagem importante de uma abordagem de microsserviços é que as equipes são mais orientadas por cenários de negócios que por tecnologia. Equipes menores desenvolvem um microsserviço com base em um cenário de cliente e usam qualquer tecnologia que eles desejam usar.

Em outras palavras, a organização não precisa padronizar a tecnologia para manter aplicativos de microsserviço. As equipes individuais que detêm serviços podem escolher o que fizer sentido para elas com base em suas competências ou no que é mais apropriado para resolver o problema. Na prática, um conjunto de recomendado de tecnologias, como um repositório NoSQL particular ou estrutura de aplicativo da web, é preferível.

A desvantagem dos microsserviços é que você precisa gerenciar entidades mais separadas e lidar com controle de versão e implantações mais complexas. Tráfego de rede entre os microsserviços aumenta, assim como as latências de rede correspondente. Muitos serviços informais e granulares podem causar um pesadelo de desempenho. Sem ferramentas para ajudá-lo a exibir essas dependências, é difícil ver todo o sistema.

Os padrões fazem a abordagem de microsserviços trabalho especificando como se comunicar e tolerar apenas às coisas que você precisa de um serviço, em vez de rígida contratos. É importante definir esses contatos antecipadamente no design, porque os serviços são atualizados independentemente uns dos outros. Outra descrição cunhada para desenvolver com uma abordagem de microsserviços é "SOA (arquitetura orientada a serviços) de granulação fina".

***Em sua forma mais simples, a abordagem de design de microsserviços é sobre uma federação dissociada de serviços, com alterações independentes e padrões acordados para comunicação.***

Conforme mais aplicativos de nuvem são produzidos, as pessoas descobriram que essa Decomposição do aplicativo geral em serviços independentes voltados para o cenário é uma abordagem de longo prazo melhor.

## <a name="comparison-between-application-development-approaches"></a>Comparação entre abordagens de desenvolvimento de aplicativos

![Desenvolvimento de aplicativos da plataforma Service Fabric][Image1]

1) Um aplicativo monolítico contém funcionalidade específica do domínio e normalmente é dividido em camadas funcionais, como web, negócios e dados.

2) Dimensionar um aplicativo monolítico clonando-la em vários servidores/máquinas virtuais/contêineres.

3) Um aplicativo de microsserviço separa a funcionalidade em serviços menores separados.

4) Essa abordagem de microsserviços pode ser dimensionada implantando cada serviço de modo independente, criando instâncias desses serviços entre servidores/máquinas virtuais/contêineres.

Criando com microsserviços de um abordagem não é adequada para todos os projetos, mas ele alinha mais aos objetivos comerciais descritos anteriormente. Começando com uma abordagem monolítica pode fazer sentido se você souber que você terá a oportunidade de refazer o código em um design de microsserviços. Normalmente, você começa com um aplicativo monolítico e o desmembra em etapas, começando com as áreas funcionais que precisam ser mais escalonáveis ou ágeis.

Quando você usa uma abordagem de microsserviços, você pode compor o aplicativo de vários serviços pequenos. Esses serviços são executados em contêineres que são implantados em um cluster de computadores. Equipes menores desenvolvem um serviço que se concentra em um cenário e independentemente de teste, versão, implantar e dimensionar cada serviço para que o aplicativo inteiro possa evoluir.

## <a name="what-is-a-microservice"></a>O que é um microsserviço?

Há diferentes definições de microsserviços. Mas a maioria dessas características dos microsserviços é amplamente aceitas:

* Abrange um cenário de cliente ou comercial. Qual problema você está solucionando?
* Desenvolvidos por uma pequena equipe de engenharia.
* Escritos em qualquer linguagem de programação, usando qualquer estrutura.
* Consistem em código e, opcionalmente, estado, que estão com versões independentes, implantados e dimensionados.
* Interagem com outros microsserviços em protocolos e interfaces bem definidos.
* Ter nomes exclusivos (URLs) que são usados para resolver seu local.
* Permanecem consistentes e disponíveis na presença de falhas.

Para concluir que:

***Os aplicativos de microsserviços são compostos por serviços pequenos e focados no cliente, com controle de versão independente e escalonáveis que se comunicam entre si por meio de protocolos padrão com interfaces bem definidas.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Escritos em qualquer linguagem de programação, usando qualquer estrutura

Como desenvolvedores, queremos ser livres para escolher uma linguagem ou estrutura, dependendo de nossas habilidades e necessidades de serviço que estamos criando. Para alguns serviços, você pode valorizar os benefícios de desempenho C++ acima de qualquer outra coisa. Para outras pessoas, a facilidade de desenvolvimento gerenciado que você obteve de C# ou Java pode ser mais importante. Em alguns casos, talvez você precise usar uma biblioteca de terceiros, a tecnologia de armazenamento de dados ou o método para expor o serviço aos clientes.

Depois de escolher uma tecnologia, você precisa considerar operacional ou o dimensionamento do serviço e gerenciamento de ciclo de vida.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Permite que o código e o estado tenham versão, implantação e dimensionamento independentes

Não importa como você escreve seus microsserviços, o código e, opcionalmente, o estado, deve independentemente implantar, atualizar e dimensionar. Esse problema é difícil de resolver porque se resume à sua escolha de tecnologias. Para dimensionamento, entender como particionar (ou fragmentar) o código e o estado é um desafio. Quando o código e o estado usam tecnologias diferentes, que é comum hoje em dia, os scripts de implantação de seu microsserviço precisam ser capaz de dimensionar os dois. Essa separação é também à agilidade e flexibilidade, portanto, você pode atualizar alguns microsserviços sem a necessidade de atualizar todos eles ao mesmo tempo.

Vamos voltar ao nosso comparação das abordagens monolíticas e microsserviços por um momento. Este diagrama mostra as diferenças nas abordagens para armazenar o estado:

#### <a name="state-storage-for-the-two-approaches"></a>Armazenamento de estado para as duas abordagens

![Armazenamento de estado da plataforma Service Fabric][Image2]

***A abordagem monolítica à esquerda, tem um único banco de dados e camadas de tecnologias específicas.***

***A abordagem de microsserviços, à direita, tem um grafo de microsserviços interconectados em que o estado normalmente tem como escopo o microsserviço e várias tecnologias são usadas.***

Uma abordagem monolítica, o aplicativo geralmente usa um único banco de dados. A vantagem de usar um banco de dados é que ele está em um único local, o que torna mais fácil de implantar. Cada componente pode ter uma única tabela para armazenar seu estado. As equipes precisam separar estritamente estado, que é um desafio. Inevitavelmente, alguém será tentado a adicionar uma coluna a uma tabela de cliente existente, fazer uma junção entre tabelas e criar dependências na camada de armazenamento. Quando isso acontecer, não será possível dimensionar os componentes individuais.

Na abordagem dos microsserviços, cada serviço gerencia e armazena seu próprio estado. Cada serviço é responsável por dimensionar o estado e o código juntos para atender às demandas do serviço. A desvantagem é que, quando você precisa criar exibições ou consultas de dados do seu aplicativo, você precisa consultar em vários armazenamentos de estado. Normalmente, esse problema é resolvido com um microsserviço separado que cria um modo de exibição em uma coleção de microsserviços. Se você precisar executar várias consultas impromptu nos dados, você deve considerar a gravação de dados do cada microsserviço em um serviço de data warehouse para análise offline.

Microsserviços têm controle de versão. É possível para diferentes versões de um microsserviço para ser executado lado a lado. Uma versão mais recente de um microsserviço poderia falhar durante uma atualização e precisará ser revertido para uma versão anterior. Controle de versão também é útil para A / B de teste, em que usuários diferentes experimentam versões diferentes do serviço. Por exemplo, é comum para atualizar um microsserviço para um conjunto específico de clientes para testar a nova funcionalidade antes de distribuí-la mais amplamente.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interage com outros microservices em protocolos e interfaces bem-definidos

Nos últimos 10 anos, informações abrangentes foi publicadas que descreve padrões de comunicação em arquiteturas orientadas a serviços. Em geral, a comunicação do serviço usa uma abordagem do REST com os protocolos TCP e HTTP, e XML ou JSON como formato de serialização. Da perspectiva de interface, trata-se utilizar uma abordagem de design da web. Mas nada deve parar de usar protocolos binários ou seus próprios formatos de dados. Apenas lembre-se de que as pessoas terão dificuldade em usar seus microsserviços se esses protocolos e formatos não estiverem abertamente disponíveis.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Tem um nome exclusivo (URL) usado para determinar sua localização

Seu microsserviço precisa ser endereçável independentemente de onde ele está em execução. Se você estiver pensando em máquinas e qual delas está executando um microsserviço específico, as coisas poderão se complicar rapidamente.

Da mesma forma que o DNS resolve um determinado URL para uma máquina específica, seu micros serviço precisa de um nome exclusivo para que sua localização atual seja detectável. Microsserviços precisam de nomes endereçáveis que são independentes da infraestrutura de que eles estiverem em execução. Isso significa que há uma interação entre como seu serviço é implantado e como ele é descoberto, porque deve haver um registro de serviço. Quando uma máquina falhar, o serviço de registro precisa para informá-lo onde o serviço foi movido.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Permanece consistente e disponível na presença de falhas

Lidar com falhas inesperadas é um dos problemas mais difíceis de se resolver, especialmente em um sistema distribuído. Grande parte do código que escrevemos como desenvolvedores é para tratamento de exceções. Durante o teste, também vamos gastar mais tempo na manipulação de exceção. O processo é mais complicado do que escrever código para lidar com falhas. O que acontece quando o computador que está executando o microsserviço falhar? Você precisa detectar a falha, o que é um problema de disco rígido por conta própria. Mas você também precisa reiniciar seu microsserviço.

Para disponibilidade, um microsserviço precisa ser resiliente a falhas e conseguir reiniciar em outro computador. Além desses requisitos de resiliência, não devem haver perda de dados e dados devem permanecer consistentes.

A resiliência é difícil de alcançar quando ocorrem falhas durante um upgrade de aplicativo. O microsserviço, trabalhando com o sistema de implantação, não precisa fazer a recuperação. Ele precisa para determinar se ele pode continuar a seguir em frente para a versão mais recente ou reverter para uma versão anterior para manter um estado consistente. Você precisa considerar algumas perguntas, como se computadores suficientes estão disponíveis para continuar avançando e como recuperar versões anteriores do microsserviço. Para tomar essas decisões, você precisa ter o microsserviço emita informações de integridade.

### <a name="reports-health-and-diagnostics"></a>Relatórios de integridade e diagnóstico

Pode parecer óbvio e muitas vezes é ignorado, mas um microsserviço precisa informar sua integridade e diagnóstico. Caso contrário, você tem pouco insight para sua integridade de uma perspectiva de operações. Correlacionar eventos de diagnóstico em um conjunto de serviços independentes e lidar com distorções de relógio da máquina para entender a ordem dos eventos é um desafio. Da mesma forma que você interage com um micros serviço em relação a protocolos e formatos de dados acordados, é necessário padronizar como registrar eventos de integridade e diagnóstico que acabarão acabando em um armazenamento de eventos para consulta e visualização. Com uma abordagem de microsserviços, as equipes diferentes precisam concordar sobre um único formato de log. Precisa ser uma abordagem consistente para exibir os eventos de diagnóstico no aplicativo inteiro.

Integridade é diferente de diagnóstico. Integridade significa que o microsserviço reporta seu estado atual para tomar as devidas ações. Um bom exemplo é trabalhar com mecanismos de atualização e implantação para manter a disponibilidade. Embora um serviço pode estar Íntegro no momento devido a uma falha de processo ou reinicialização do computador, o serviço ainda pode estar operacional. A última coisa que você precisa é piorar a situação iniciando uma atualização. A melhor abordagem é investigar primeiro ou aguardar até o microsserviço se recuperar. Os eventos de integridade de um microsserviço permitem tomar decisões bem informadas e ajudam realmente a criar serviços que recuperam a si próprios.

## <a name="guidance-for-designing-microservices-on-azure"></a>Diretrizes para criar microsserviços no Azure 
Visite o Centro de arquitetura do Azure para obter orientação sobre [Projetando e criando microsserviços no Azure](https://docs.microsoft.com/azure/architecture/microservices/).

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric como uma plataforma de microsserviço

O Azure Service Fabric surgiu quando a Microsoft fez a transição do fornecimento de produtos demarcados, o que normalmente eram monolíticos, no fornecimento de serviços. A experiência de criar e operar serviços grandes, como o banco de dados SQL e o Azure Cosmos DB, modelou o Service Fabric. A plataforma evoluiu ao longo do tempo conforme mais serviços a adotaram. O Service Fabric tinha que ser executado não apenas no Azure, mas também em implantações autônomas do Windows Server.

***O objetivo do Service Fabric é resolver os difíceis problemas de compilação e execução de um serviço e usar recursos de infraestrutura com eficiência, portanto, as equipes possam resolver problemas de negócios usando uma abordagem de microsserviços.***

O Service Fabric ajuda você a criar aplicativos que usam uma abordagem de micros serviços, fornecendo:

* Uma plataforma que fornece serviços do sistema para implantar, atualizar, detectar e reiniciar os serviços com falha, descobrir serviços, encaminhar mensagens, gerenciar o estado e monitorar a integridade.
* A capacidade de implantar aplicativos ou em execução em contêineres ou processos. O Service Fabric é um orquestrador de contêiner e processo.
* APIs de programação produtivas para ajudá-lo a criar aplicativos como microsserviços: [ASP.NET Core, Reliable Actors e Reliable Services](service-fabric-choose-framework.md). Por exemplo, você pode obter informações de integridade e diagnóstico, ou você pode tirar proveito da alta disponibilidade interna.

***Service Fabric é indiferente sobre como criar seu serviço, e você pode usar qualquer tecnologia. Mas ele fornece APIs de programação internas que facilitam a criação de microsserviços.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrando aplicativos existentes para o Service Fabric

O Service Fabric permite que você reutilize o código existente e moderniza-o com novos microsserviços. Há cinco estágios para modernização do aplicativo, e você pode iniciar e parar em qualquer estágio. Os estágios são:

1) Comece com um aplicativo monolítico tradicional.  
2) A migração. Usar contêineres ou executáveis de convidado para hospedar o código existente no Service Fabric.  
3) Modernize. Adicione novos microsserviços junto com o código em contêineres existente.  
4) Inove. Divida o aplicativo monolítico em microsserviços com base na necessidade.  
5) Transforme aplicativos em microsserviços. Transformar aplicativos monolíticos existentes ou criar novos aplicativos intactos.

![Migração para microsserviços][Image3]

Lembre-se de que você pode *iniciar e parar em qualquer um desses estágios*. Você não tiver o progresso para o próximo estágio. 

Vamos examinar exemplos para cada um desses estágios.

**Migrar**  
Por dois motivos, muitas empresas estão migrando aplicativos monolíticos existentes em contêineres:

* Redução de custos, devido à consolidação e a remoção de hardware existente ou devido à execução de aplicativos em densidade mais alta.
* Um contrato de implantação consistente entre o desenvolvimento e operações.

Reduções de custos são simples. Na Microsoft, muitos aplicativos existentes estão sendo colocados em contêineres, levando a milhões de dólares em economia. Implantação consistente é mais difícil avaliar, mas igualmente importante. Isso significa que os desenvolvedores podem escolher as tecnologias de acordo com eles, mas operações aceitará um único método para implantar e gerenciar os aplicativos. Reduz as operações de ter que lidar com a complexidade do suporte a tecnologias diferentes, sem forçar os desenvolvedores a escolher apenas algumas. Essencialmente, cada aplicativo está em contêineres em imagens de implantação autocontida.

Muitas organizações param aqui. Eles já têm os benefícios dos contêineres e o Service Fabric fornece a experiência de gerenciamento completo, incluindo implantação, atualizações, controle de versão, reversões e monitoramento de integridade.

**Modernize**  
Modernização é a adição de novos serviços junto com o código em contêineres existente. Se você pretende escrever novo código, é melhor dar pequenos passos o caminho dos microsserviços. Isso pode significar a adição de um novo ponto de extremidade de API REST ou a nova lógica de negócios. Dessa forma, você deve iniciar o processo de criação de novos microsserviços e pratica o desenvolvimento e implantá-los.

**Inove**  
Uma abordagem de microsserviços acomoda as necessidades de negócios em mudança. Nesse estágio, você precisa decidir se deseja começar a dividir o aplicativo monolítico em serviços ou a inovar. Veja um exemplo clássico é quando um banco de dados que você está usando como uma fila de fluxo de trabalho se torna um gargalo de processamento. Conforme o número de solicitações de fluxo de trabalho aumenta o trabalho precisa ser distribuído para dimensionamento. Levar essa parte específica do aplicativo que não é escalonável, ou que precisa ser atualizada com mais frequência e dividi-lo como um microsserviço e inove.

**Transformar aplicativos em microsserviços**  
Nesse estágio, seu aplicativo é totalmente composto de (ou divididas em) microsserviços. Para chegar a este ponto, você fez a jornada dos microsserviços. Você pode começar por aqui, mas para fazer isso sem um microsserviços plataforma para ajudá-lo requer um investimento significativo.

### <a name="are-microservices-right-for-my-application"></a>Os microsserviços são ideais para meu aplicativo?

Talvez. Na Microsoft, conforme mais equipes começaram a construir para a nuvem por motivos comerciais, muitas perceberam os benefícios de adotar uma abordagem semelhante ao microsserviço. Bing, por exemplo, tem sido usando microsserviços por anos. Para as outras equipes, a abordagem dos microsserviços foi nova. As equipes descobriram que havia problemas difíceis de resolver fora de suas principais áreas de capacidade. É por isso o Service Fabric ganhou adeptos como a tecnologia para a criação de serviços.

O objetivo do Service Fabric é reduzir a complexidade da criação de aplicativos de microsserviço para que você não precisa passar por tantas reformulações dispendiosas. Comece pequeno, dimensione quando necessário, descontinue serviços, adicione novos e evolua com o uso do cliente. Também sabemos que há muitos outros problemas que ainda precisam ser resolvidos para tornar os microsserviços mais acessíveis à maioria dos desenvolvedores. Contêineres e o modelo de programação de ator são exemplos de pequenas etapas nessa direção. Temos certeza que outras inovações surgirão para tornar mais fácil uma abordagem de microsserviços.


## <a name="next-steps"></a>Próximas etapas

* [Microsserviços: Uma revolução de aplicativo proporcionada pela nuvem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Azure Architecture Center: Compilar microsserviços no Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* [Práticas recomendadas do cluster e aplicativo do Service Fabric do Azure](service-fabric-best-practices-overview.md)
* [Visão geral da terminologia do Service Fabric](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
