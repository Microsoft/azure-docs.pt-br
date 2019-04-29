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
ms.date: 07/02/2017
ms.author: atsenthi
ms.openlocfilehash: 1e02e4fdf0dbe04d8756fc6355c6a9e414b27d2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60719072"
---
# <a name="why-a-microservices-approach-to-building-applications"></a>Por que usar uma abordagem de microsserviço para construir aplicativos?

Para nós, desenvolvedores de software, não há qualquer novidade no modo como pensamos sobre a decomposição de um aplicativo em partes componentes. Em geral, uma abordagem em camadas é adotada com um repositório de back-end, lógica de negócios de camada intermediária e uma IU (interface do usuário) de front-end. O que *mudou* nos últimos anos é que nós, como desenvolvedores, estamos construindo aplicativos distribuídos para a nuvem.

Entre as necessidades variáveis dos negócios estão:

* Um serviço que é compilado e opera em escala para alcançar clientes em novas regiões geográficas.
* Entrega mais ágil de recursos a fim de conseguir responder rapidamente às demandas dos clientes.
* Melhor utilização de recursos para reduzir os custos.

Essas necessidades comerciais estão afetando o *modo* como criamos os aplicativos.

Para obter mais informações sobre a abordagem do Azure para microsserviços, leia [Microsserviços: Uma revolução de aplicativo proporcionada pela nuvem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservice-design-approach"></a>Abordagem de design monolítico versus microsserviços

Aplicações evoluem ao longo do tempo. Os aplicativos bem-sucedidos evoluem sendo úteis às pessoas. Aplicativos sem sucesso não evoluem e eventualmente tornam-se obsoletos. A pergunta é: Quanto você sabe sobre os requisitos de hoje e quais haverá no futuro? Por exemplo, digamos que você esteja criando um aplicativo de relatório para um departamento. Você tem certeza de que o aplicativo só se aplica ao escopo de sua empresa e que os relatórios são de curta duração. Sua escolha de abordagem é diferente de, digamos, criar um serviço de entrega de conteúdo de vídeo a dezenas de milhões de clientes.

Às vezes, lançar algo como prova de conceito é o fator determinante, com a noção de que o aplicativo pode ser reprojetado posteriormente. Não há muito sentido em trabalhar muito em algo que nunca será usado. É a compensação comum da engenharia. Por outro lado, quando as empresas falam sobre criação para a nuvem, a expectativa é de crescimento e uso. O problema é que o crescimento e o dimensionamento são imprevisíveis. Gostaríamos de ser capazes de criar um protótipo rapidamente e saber, ao mesmo tempo, que estamos no caminho certo para lidar com o sucesso no futuro. Essa é a abordagem de inicialização simples: compilar, medir, aprender e iterar.

Durante a era cliente-servidor, nossa tendência era se concentrar na construção de aplicativos em camadas usando tecnologias específicas em cada camada. O termo aplicativo *monolítico* surgiu para essas abordagens. As interfaces tendiam a ficar entre as camadas e um design mais rigidamente acoplado era usado entre os componentes em cada camada. Os desenvolvedores projetaram e fatoraram classes compiladas em bibliotecas e as vincularam em alguns arquivos executáveis e DLLs. 

Há benefícios em tal abordagem de design monolítico. Geralmente, é mais simples de projetar e tem chamadas mais rápidas entre os componentes, pois essas chamadas geralmente são realizadas através de comunicação entre processos (IPC). Além disso, todos testam um único produto, que tende a ser mais eficiente em termos de recursos e pessoas. A desvantagem é que há um forte acoplamento entre as camadas e estratificadas e não é possível dimensionar componentes individuais. Se você precisar executar correções ou atualizações, precisará esperar que as demais pessoas concluam o teste. É mais difícil de ser ágil.

Os microsserviços tratam dessas desvantagens e alinham-se melhor aos requisitos de negócios anteriores, mas também têm vantagens e desvantagens. Os benefícios dos micros serviços são que cada um normalmente encapsula uma funcionalidade de negócios mais simples, que você pode ampliar, testar, implantar e gerenciar de forma independente. Uma vantagem importante de uma abordagem de micros serviço é que as equipes são mais orientadas por cenários de negócios que por tecnologia — o que incentiva a abordagem em camadas. Na prática, equipes menores desenvolvem um microsserviço baseado em um cenário de cliente e usam qualquer tecnologia escolhida.

Em outras palavras, a organização não precisa padronizar a tecnologia para manter aplicativos de microsserviço. As equipes individuais que detêm serviços podem escolher o que fizer sentido para elas com base em suas competências ou no que é mais apropriado para resolver o problema. Na prática, é preferível ter um conjunto de tecnologias recomendadas, como determinado repositório NoSQL ou uma estrutura de aplicativo Web.

A desvantagem dos microsserviços é gerenciar o crescente número de entidades separadas e lidar com implantações mais complexas e controle da versão. O tráfego da rede entre os microsserviços aumenta, bem como as latências de rede correspondentes. Ter muitos serviços informais e granulares é uma receita desastrosa para o desempenho. Sem ferramentas para ajudar a exibir essas dependências, é difícil "ver" todo o sistema.

Os padrões fazem a abordagem do microsserviço funcionar ao criar um acordo sobre como se comunicar e sendo tolerante apenas com aquilo de que você precisa de um serviço, em vez de contratos rígidos. É importante definir esses contatos antecipadamente no design, pois os serviços são atualizados de forma independente. Outra descrição cunhada para desenvolver com uma abordagem de microsserviços é "SOA (arquitetura orientada a serviços) de granulação fina".

***Em sua forma mais simples, a abordagem de design com microsserviços se resume a uma federação dissociada de serviços, com alterações independentes e padrões acordados para a comunicação.***

À medida que mais aplicativos na nuvem são produzidos, as pessoas descobriram que essa decomposição do aplicativo global em serviços independentes, focados no cenário, é uma abordagem melhor a longo prazo.

## <a name="comparison-between-application-development-approaches"></a>Comparação entre abordagens de desenvolvimento de aplicativos

![Desenvolvimento de aplicativos da plataforma Service Fabric][Image1]

1) Um aplicativo monolítico contém funcionalidades específicas do domínio e é normalmente dividido por camadas funcionais, como Web, negócios e dados.

2) Você dimensiona um aplicativo monolítico clonando-o em vários servidores/máquinas virtuais/contêineres.

3) Um aplicativo de microsserviço separa a funcionalidade em serviços menores separados.

4) Essa abordagem de microsserviços pode ser dimensionada implantando cada serviço de modo independente, criando instâncias desses serviços entre servidores/máquinas virtuais/contêineres.

Criar usando uma abordagem de microsserviço não é uma solução para todos os projetos, mas ela se alinha mais aos objetivos comerciais descritos anteriormente. Começar com uma abordagem monolítica pode ser aceitável se você souber que depois terá a oportunidade de refazer o código como um design de microsserviço. Normalmente, você começa com um aplicativo monolítico e o desmembra em etapas, começando com as áreas funcionais que precisam ser mais escalonáveis ou ágeis.

A abordagem de micros serviço significa que você compõe sua aplicação de muitos serviços pequenos. Os serviços são executados em contêineres implantados em um cluster de máquinas. Equipes menores desenvolvem um serviço focado em um cenário e testa, cria a versão, implanta e dimensiona independentemente cada serviço para que todo o aplicativo inteiro possa evoluir.

## <a name="what-is-a-microservice"></a>O que é um microsserviço?

Há diferentes definições de microsserviços. No entanto, a maioria das características dos microsserviços descritas a seguir é aceita:

* Abrange um cenário de cliente ou comercial. Qual é o problema que você está solucionando?
* Desenvolvidos por uma pequena equipe de engenharia.
* Escritos em qualquer linguagem de programação e usam qualquer estrutura.
* Consistem em código e (opcionalmente) estado, com versão, implantação e dimensionamento independentes.
* Interagem com outros microsserviços em protocolos e interfaces bem definidos.
* Têm nomes exclusivos (URLs) usados para determinar seus locais.
* Permanecem consistentes e disponíveis na presença de falhas.

Em resumo:

***Os aplicativos de microsserviços são compostos por serviços pequenos e focados no cliente, com controle de versão independente e escalonáveis que se comunicam entre si por meio de protocolos padrão com interfaces bem definidas.***

### <a name="written-in-any-programming-language-and-use-any-framework"></a>Escritos em qualquer linguagem de programação e usam qualquer estrutura

Como desenvolvedores, queremos ser livres para escolher um idioma ou estrutura que queremos, dependendo de nossas habilidades ou das necessidades do serviço. Em alguns serviços, você pode valorizar os benefícios do desempenho do C++ acima de tudo. Em outros serviços, a facilidade de desenvolvimento gerenciado no C# ou Java pode ser mais importante. Em alguns casos, talvez seja necessário usar uma biblioteca de terceiros, tecnologia de armazenamento de dados ou meios de expor o serviço aos clientes.

Após a escolha de uma tecnologia, chega-se ao gerenciamento operacional ou do ciclo de vida e à colocação em escala do serviço.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Permite que o código e o estado tenham versão, implantação e dimensionamento independentes

No entanto, você escolhe gravar seus micros serviços, o código e, opcionalmente, o estado, deve implantar, atualizar e dimensionar de forma independente. Este é um dos problemas mais difíceis de resolver, porque se resume à sua escolha de tecnologias. Para o dimensionamento, entender como particionar (ou fragmentar) o código e o estado é um desafio. Quando o código e o estado usam tecnologias separadas, o que é comum hoje em dia, os scripts de implantação do seu micros serviço precisam ser capazes de escaloná-los. Isso está relacionado também à agilidade e à flexibilidade; portanto, você pode atualizar alguns microsserviços sem precisar atualizar todos de uma vez.

Voltando à questão da abordagem monolítica versus microsserviço por um momento, o diagrama a seguir mostra as diferenças nas abordagens para armazenar o estado.

#### <a name="state-storage-between-application-styles"></a>Armazenamento de estado entre os estilos de aplicativo

![Armazenamento de estado da plataforma Service Fabric][Image2]

***À esquerda, a abordagem monolítica à esquerda tem um único banco de dados e camadas de tecnologias específicas.***

***A abordagem de microsserviços à direita tem um grafo de microsserviços interconectados em que o estado normalmente tem como escopo o microsserviço e várias tecnologias são usadas.***

Uma abordagem monolítica, o aplicativo geralmente usa um único banco de dados. A vantagem é que se trata de um único local, facilitando a implantação. Cada componente pode ter uma única tabela para armazenar seu estado. As equipes precisam separar estritamente estado, que é um desafio. Há, inevitavelmente, tentações para adicionar uma nova coluna a uma tabela de clientes existente, para fazer uma associação entre as tabelas e para criar dependências na camada de armazenamento. Quando isso acontecer, não será possível dimensionar os componentes individuais.

Na abordagem dos microsserviços, cada serviço gerencia e armazena seu próprio estado. Cada serviço é responsável por dimensionar o estado e o código juntos para atender às demandas do serviço. Uma desvantagem é que, quando há necessidade de criar visualizações ou consultas dos dados de seu aplicativo, você precisa consultar vários armazenamentos de estado. Normalmente, isso é resolvido com um microsserviço separado que cria um modo de exibição em uma coleção de microsserviços. Se você precisar executar várias consultas impromptu nos dados, cada microsserviço deverá considerar grava seus dados em um serviço de data warehouse para análise offline.

Os micros serviços são versionados e é possível que versões diferentes de um micros serviço possam estar sendo executadas lado a lado. Uma versão mais recente de um micros serviço pode falhar durante a atualização e precisa ser revertida para uma versão anterior. O controle de versão também é útil para testes no estilo A/B, em que diferentes usuários experimentam versões diferentes do serviço. Por exemplo, é comum atualizar um microsserviço para um conjunto específico de clientes testar a nova funcionalidade, antes de fazê-lo mais amplamente.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interage com outros microservices em protocolos e interfaces bem-definidos

Extensa literatura sobre arquitetura orientada a serviços foi publicada nos últimos 10 anos descrevendo padrões de comunicação. Geralmente, a comunicação de serviço usa uma abordagem REST com protocolos HTTP e TCP e XML ou JSON como o formato de serialização. Do ponto de vista da interface, isso se resume à adoção da abordagem de design da Web. Mas nada o impede de usar protocolos binários ou seus próprios formatos de dados. Esteja preparado para a possibilidade de as pessoas terem mais dificuldade em usar seus microsserviços se esses protocolos e formatos não estiverem abertamente disponíveis.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Tem um nome exclusivo (URL) usado para determinar sua localização

Seu micros serviço precisa ser endereçável onde quer que esteja em execução. Se você ficar pensando em máquinas, e qual delas está executando um determinado microsserviço, as coisas vão dar errado rapidamente. 

Da mesma forma que o DNS resolve um determinado URL para uma máquina específica, seu micros serviço precisa de um nome exclusivo para que sua localização atual seja detectável. Os micros serviços precisam de nomes endereçáveis que sejam independentes da infraestrutura em que estão sendo executados. Isso implica que haja uma interação entre como seu serviço é implantado e como ele é descoberto, já que deve haver um registro do serviço. Quando uma máquina falha, o serviço de registro deve informar para onde o serviço foi movido.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Permanece consistente e disponível na presença de falhas

Lidar com falhas inesperadas é um dos problemas mais difíceis de se resolver, especialmente em um sistema distribuído. Boa parte do código que escrevemos, como desenvolvedores, serve para lidar exceções, e também é nisso que a maior parte do tempo é gasto durante o teste. O problema é mais complicado do que escrever código para tratar de falhas. O que acontece quando o computador onde o microserviço está em execução falha? Você não precisa apenas detectar a falha (um problema difícil por conta própria), mas também precisa reiniciar seu micros serviço.

Por motivos de disponibilidade, um micros serviço precisa ser resiliente a falhas e ser capaz de reiniciar em outra máquina. Além do código de execução ser resiliente, não deve haver perda de dados e os dados precisam permanecer consistentes.

A resiliência é difícil de alcançar quando ocorrem falhas durante um upgrade de aplicativo. O microsserviço, trabalhando com o sistema de implantação, não precisa fazer a recuperação. Ele precisa decidir se pode continuar avançando para a versão mais recente ou reverter para uma versão anterior para manter um estado consistente. É preciso considerar perguntas como se há computadores suficientes disponíveis para continuar avançando e como recuperar versões anteriores do microsserviço. Isso requer que o micros serviço emita informações de saúde para tomar essas decisões.

### <a name="reports-health-and-diagnostics"></a>Relatórios de integridade e diagnóstico

Pode parecer óbvio, e muitas vezes isso é negligenciado, mas um microsserviço deve relatar sua integridade e diagnóstico. Caso contrário, há pouca percepção sobre sua saúde a partir de uma perspectiva de operações. Correlacionar eventos de diagnóstico em um conjunto de serviços independentes e lidar com distorções de relógio da máquina para entender a ordem dos eventos é um desafio. Da mesma forma que você interage com um micros serviço em relação a protocolos e formatos de dados acordados, é necessário padronizar como registrar eventos de integridade e diagnóstico que acabarão acabando em um armazenamento de eventos para consulta e visualização. Em uma abordagem de micros serviços, ele tem chave que diferentes equipes concordem com um único formato de log. Precisa ser uma abordagem consistente para exibir os eventos de diagnóstico no aplicativo inteiro.

Integridade é diferente de diagnóstico. Integridade significa que o microsserviço reporta seu estado atual para tomar as devidas ações. Um bom exemplo é trabalhar com mecanismos de atualização e implantação para manter a disponibilidade. Embora um serviço possa não estar íntegro no momento devido a uma falha de processo ou reinicialização do computador, o serviço ainda pode estar operacional. A última coisa de que você precisa é piorar a situação executando uma atualização. A melhor abordagem é fazer uma investigação primeiro ou aguardar a recuperação do microsserviço. Os eventos de integridade de um microsserviço permitem tomar decisões bem informadas e ajudam realmente a criar serviços que recuperam a si próprios.

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric como uma plataforma de microsserviço

O Azure Service Fabric surgiu quando a Microsoft passou da entrega de produtos em caixa, que eram tipicamente monolíticos em estilo, para a entrega de serviços. A experiência de criar e operar serviços grandes, como Banco de Dados SQL do Azure e Azure Cosmos DB, modelou o Service Fabric. A plataforma evoluiu ao longo do tempo conforme mais e mais serviços adotaram-na. O Service Fabric tinha que ser executado não apenas no Azure, mas também em implantações autônomas do Windows Server.

***O objetivo do Service Fabric é resolver os problemas difíceis de criação e execução de um serviço, e utilizar os recursos da infraestrutura com eficiência para que as equipes possam resolver os problemas de negócios usando uma abordagem de microsserviços.***

O Service Fabric ajuda você a criar aplicativos que usam uma abordagem de micros serviços, fornecendo:

* Uma plataforma que fornece serviços do sistema para implantar, atualizar, detectar e reiniciar os serviços com falha, descobrir serviços, encaminhar mensagens, gerenciar o estado e monitorar a integridade.
* Capacidade de implantar aplicativos ou em execução em contêineres ou processos. O Service Fabric é um orquestrador de contêiner e processo.
* APIs de programação produtiva, para ajudá-lo a criar aplicativos como microsserviços: [ASP.NET Core, Reliable Actors e Reliable Services](service-fabric-choose-framework.md). Por exemplo, você pode obter informações de integridade e diagnóstico, ou você pode tirar proveito da alta disponibilidade interna.

***O Service Fabric não se importa com o modo de criação de seu serviço, e você pode usar qualquer tecnologia. No entanto, fornece APIs de programação internas que facilitam a criação de microsserviços.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrando aplicativos existentes para o Service Fabric

O Service Fabric permite reutilizar o código existente, que pode então ser modernizado com novos micros serviços. Há cinco estágios para modernização do aplicativo, e você pode iniciar e interromper em qualquer um dos estágios. Estes são:

1) Comece com um aplicativo monolítico tradicional.  
2) Lift and Shift - Usar contêineres ou executáveis de convidado para hospedar o código existente no Service Fabric.  
3) Modernização - Novos microsserviços adicionados junto com o código em contêineres existente.  
4) Inovar - Dividir o monolítico em microsserviços puramente com base na necessidade.  
5) Transformado em microsserviços - a transformação de aplicativos monolíticos existentes ou criar novos aplicativos intactos.

![Migração para Microsserviços][Image3]

É importante enfatizar novamente que é possível **iniciar e parar em qualquer um desses estágios**. Você não é forçado a passar para o próximo estágio. Agora vamos ver exemplos de cada um desses estágios.

**Lift- e -Shift**  
Um grande número de empresas está elevando e transferindo aplicativos monolíticos existentes para contêineres por dois motivos:

- Redução de custos devido à consolidação e remoção de hardware existente ou a execução de aplicativos com densidade mais alta.
- Contrato de implantação consistente entre o desenvolvimento e as operações.

Reduções de custos são compreensíveis e dentro da Microsoft grandes números de aplicativos existentes estão sendo colocados em contêineres para economizar milhões de dólares. Uma implantação consistente é mais difícil avaliar, mas igualmente importante. Nesse tipo de implantação os desenvolvedores ainda possuem a liberdade de escolher a tecnologia adequada, no entanto, as operações só aceitarão uma única maneira de implantar e gerenciar esses aplicativos. Ela elimina a necessidade das operações de ter que lidar com a complexidade das muitas tecnologias diferentes ou de forçar os desenvolvedores a escolher apenas algumas. Essencialmente, cada aplicativo está em contêineres em imagens de implantação independentes.

Muitas organizações param aqui. Elas já têm os benefícios de contêineres e o Service Fabric fornece a experiência completa de gerenciamento de implantação, atualizações, controle de versão, reversões, monitoramento de integridade, etc.

**Modernização**  
A adição de novos serviços junto com os existentes em contêineres de código. Se você pretende escrever um novo código, é recomendável dar pequenos passos e seguir o caminho dos microsserviços. Isso pode adicionar um novo ponto de extremidade de API REST ou uma nova lógica de negócios. Dessa forma, você inicie a jornada de criação de novos microsserviços e pratica o desenvolvimento e a implantação destes.

**Inove**  
Uma abordagem de microsserviços acomoda as necessidades de negócios em mudança. Nesse estágio, a decisão é se você precisa começar a dividir o aplicativo monolítico em serviços ou inovar. Um exemplo aqui é quando um banco de dados sendo usado como uma fila de fluxo de trabalho se torna um gargalo de processamento. Conforme o número de solicitações de fluxo de trabalho aumenta o trabalho precisa ser distribuído para dimensionamento. Para essa parte específica do aplicativo que não está em escala ou que precisa ser atualizada com mais frequência, divida isso em um micros serviço e inove.

**Transformado em micros serviços**  
É aqui que seu aplicativo é totalmente composto (ou decomposto em) micros serviços. Para chegar aqui, você percorreu a jornada dos microsserviços. Você pode iniciar aqui, mas fazer isso sem uma plataforma de microsserviços para ajudá-lo é um investimento significativo. 

### <a name="are-microservices-right-for-my-application"></a>Os microsserviços são ideais para meu aplicativo?

Talvez. O que experimentamos foi que quando mais equipes na Microsoft começaram a construir para a nuvem por motivos comerciais, muitas perceberam os benefícios de adotar uma abordagem semelhante ao microsserviço. O Bing, por exemplo, vem desenvolvendo usando micros serviços há anos. Para as outras equipes, a abordagem dos microsserviços foi nova. As equipes descobriram que havia problemas difíceis de resolver fora de suas principais áreas de capacidade. É por isso que o Service Fabric ganhou adeptos como a tecnologia preferida para a criação de serviços.

O objetivo do Service Fabric é reduzir as complexidades da criação de aplicativos de micros serviços para que você não precise passar por tantos reprojetos caros. Comece pequeno, dimensione quando necessário, descontinue serviços, adicione novos e evolua com o uso do cliente. Também sabemos que há muitos outros problemas que ainda precisam ser resolvidos para tornar os microsserviços mais acessíveis à maioria dos desenvolvedores. Os contêineres e o modelo de programação de ator são exemplos de pequenas etapas nessa direção, e temos certeza de que outras inovações surgirão para tornar isso mais fácil.
 
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da terminologia do Service Fabric](service-fabric-technical-overview.md)
* [Microsserviços: Uma revolução de aplicativo proporcionada pela nuvem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
