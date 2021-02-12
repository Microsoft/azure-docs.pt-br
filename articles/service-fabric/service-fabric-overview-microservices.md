---
title: Introdução aos microsserviços no Azure
description: Uma visão geral sobre por que criar aplicativos de nuvem com uma abordagem de microsserviços é importante para o desenvolvimento moderno de aplicativos e como o Azure Service Fabric oferece uma plataforma para conseguir isso.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: d20e04820c87a7390d9c20e511259ee9860c27f5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575645"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Por que usar uma abordagem de microserviços para a criação de aplicativos

Para desenvolvedores de software, a fatoração de um aplicativo em partes de componentes não é nada novo. Normalmente, uma abordagem em camadas é usada, com um armazenamento de back-end, uma lógica de negócios de camada intermediária e uma interface do usuário de front-end (UI). O *que mudou* nos últimos anos é que os desenvolvedores estão criando aplicativos distribuídos para a nuvem.

Aqui estão algumas necessidades de negócios em constante mudança:

* Um serviço criado e operado em escala para alcançar clientes em novas regiões geográficas.
* Entrega mais rápida de recursos e recursos para responder às demandas dos clientes de maneira ágil.
* Melhor utilização de recursos para reduzir os custos.

Essas necessidades comerciais estão afetando o *modo* como criamos os aplicativos.

Para obter mais informações sobre a abordagem do Azure para os microserviços, consulte [microservices: uma revolução de aplicativo desativada pela nuvem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservices-design-approach"></a>Abordagem de design monolítico vs. de microserviços

Aplicações evoluem ao longo do tempo. Os aplicativos bem-sucedidos evoluem sendo úteis às pessoas. Aplicativos sem êxito não evoluem e, eventualmente, são preteridos. Esta é a pergunta: quanto você sabe sobre seus requisitos hoje e o que eles estarão no futuro? Por exemplo, digamos que você esteja criando um aplicativo de relatório para um departamento em sua empresa. Você tem certeza de que o aplicativo se aplica somente dentro do escopo da sua empresa e que os relatórios não serão mantidos longos. Sua abordagem será diferente da, digamos, criar um serviço que fornece conteúdo de vídeo para dezenas de milhões de clientes.

Às vezes, fazer algo fora da porta como uma prova de conceito é o fator determinante. Você sabe que o aplicativo pode ser reprojetado mais tarde. Há algum ponto no excesso de engenharia de algo que nunca é usado. Por outro lado, quando as empresas criam para a nuvem, a expectativa é o crescimento e o uso. O crescimento e a escala são imprevisíveis. Queremos desenvolver um protótipo rapidamente e também saber que estamos em um caminho que pode lidar com o sucesso futuro. Essa é a abordagem de inicialização simples: compilar, medir, aprender e iterar.

Durante a era do cliente/servidor, nós tendiammos a se concentrar na criação de aplicativos em camadas usando tecnologias específicas em cada camada. O termo aplicativo *monolítico* surgiu para descrever essas abordagens. As interfaces tendiam a ficar entre as camadas e um design mais rigidamente acoplado era usado entre os componentes em cada camada. Os desenvolvedores criaram e consideraram classes que foram compiladas em bibliotecas e vinculadas em alguns arquivos e DLLs executáveis.

Há benefícios para uma abordagem de design monolítico. Os aplicativos monolíticos costumam ser mais simples de criar, e as chamadas entre os componentes são mais rápidas, pois essas chamadas geralmente são sobre a IPC (comunicação entre processos). Além disso, todos testam um único produto, que tende a ser um uso mais eficiente dos recursos humanos. A desvantagem é que há um acoplamento rígido entre camadas em camadas, e você não pode dimensionar componentes individuais. Se você precisar fazer correções ou atualizações, precisará esperar que outras pessoas concluam seus testes. É mais difícil ser ágil.

Os microserviços atendem a essas desvantagens e alinham-se mais com os requisitos de negócios anteriores. Mas eles também têm benefícios e passivos. Os benefícios dos microserviços são que cada um normalmente encapsula uma funcionalidade comercial mais simples, que você pode escalar horizontalmente, testar, implantar e gerenciar de forma independente. Um benefício importante de uma abordagem de microserviço é que as equipes são mais orientadas por cenários de negócios do que por tecnologia. As equipes menores desenvolvem um microserviço baseado em um cenário de cliente e usam as tecnologias que desejam usar.

Em outras palavras, a organização não precisa padronizar a tecnologia para manter aplicativos de microsserviço. As equipes individuais que detêm serviços podem escolher o que fizer sentido para elas com base em suas competências ou no que é mais apropriado para resolver o problema. Na prática, um conjunto de tecnologias recomendadas, como um armazenamento NoSQL específico ou estrutura de aplicativo Web, é preferível.

A desvantagem dos microserviços é que você precisa gerenciar entidades mais separadas e lidar com implantações e versões mais complexas. O tráfego de rede entre os microserviços aumenta, assim como as latências de rede correspondentes. Muitos serviços granulares, que podem causar um pesadelo no desempenho. Sem ferramentas para ajudá-lo a exibir essas dependências, é difícil ver todo o sistema.

Os padrões fazem com que a abordagem de microserviço funcione especificando como comunicar e tolerar apenas as coisas de que você precisa de um serviço, em vez de contratos rígidos. É importante definir esses contratos antecipadamente no design porque os serviços são atualizados de forma independente. Outra descrição cunhada para desenvolver com uma abordagem de microsserviços é "SOA (arquitetura orientada a serviços) de granulação fina".

***Em sua forma mais simples, a abordagem de design de microserviços é sobre uma federação dissociada de serviços, com alterações independentes em cada um e padrões acordados para comunicação.** _

À medida que mais aplicativos de nuvem são produzidos, as pessoas descobriram que essa decomposição do aplicativo geral em serviços independentes, centrados em cenários, é uma abordagem melhor de longo prazo.

## <a name="comparison-between-application-development-approaches"></a>Comparação entre abordagens de desenvolvimento de aplicativos

![Desenvolvimento de aplicativos da plataforma Service Fabric][Image1]

1) Um aplicativo monolítico contém funcionalidade específica de domínio e normalmente é dividido em camadas funcionais, como Web, negócios e dados.

2) Você dimensiona um aplicativo monolítico clonando-o em vários servidores/máquinas virtuais/contêineres.

3) Um aplicativo de microsserviço separa a funcionalidade em serviços menores separados.

4) Essa abordagem de microsserviços pode ser dimensionada implantando cada serviço de modo independente, criando instâncias desses serviços entre servidores/máquinas virtuais/contêineres.

A criação de uma abordagem de microserviços não é apropriada para todos os projetos, mas alinha-se mais com os objetivos de negócios descritos anteriormente. Começar com uma abordagem monolítica pode fazer sentido se você souber que terá a oportunidade de reformular o código posteriormente em um design de microserviços. Normalmente, você começa com um aplicativo monolítico e o desmembra em etapas, começando com as áreas funcionais que precisam ser mais escalonáveis ou ágeis.

Ao usar uma abordagem de microserviços, você compõe seu aplicativo de muitos serviços pequenos. Esses serviços são executados em contêineres que são implantados em um cluster de computadores. As equipes menores desenvolvem um serviço que se concentra em um cenário e testam, versionam, implantam e dimensionam de forma independente cada serviço para que todo o aplicativo possa evoluir.

## <a name="what-is-a-microservice"></a>O que é um microsserviço?

Há diferentes definições de microsserviços. Mas a maioria dessas características de microserviços é amplamente aceita:

_ Encapsular um cenário de cliente ou de negócios. Qual problema você está resolvendo?
* Desenvolvidos por uma pequena equipe de engenharia.
* Escrito em qualquer linguagem de programação, usando qualquer estrutura.
* Consistem em código e, opcionalmente, com controle de versão, implantados e dimensionados de forma independente.
* Interagem com outros microsserviços em protocolos e interfaces bem definidos.
* Ter nomes exclusivos (URLs) que são usados para resolver seu local.
* Permanecem consistentes e disponíveis na presença de falhas.

Para somar isso:

***Os aplicativos de microatendimento são compostos por serviços pequenos, com controle de versão independente e voltados para o cliente, que se comunicam entre si em protocolos padrão com interfaces bem definidas.** _

### <a name="written-in-any-programming-language-using-any-framework"></a>Escrito em qualquer linguagem de programação, usando qualquer estrutura

Como desenvolvedores, queremos ser livres para escolher uma linguagem ou estrutura, dependendo de nossas habilidades e das necessidades do serviço que estamos criando. Para alguns serviços, você pode aproveitar os benefícios de desempenho do C++ acima de qualquer outra coisa. Para outros, a facilidade de desenvolvimento gerenciado que você obtém do C# ou Java pode ser mais importante. Em alguns casos, talvez seja necessário usar uma biblioteca de parceiros, uma tecnologia de armazenamento de dados ou um método específico para expor o serviço aos clientes.

Depois de escolher uma tecnologia, você precisa considerar o gerenciamento operacional ou de ciclo de vida e o dimensionamento do serviço.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Permite que o código e o estado tenham versão, implantação e dimensionamento independentes

Não importa como você escreve seus microserviços, o código e, opcionalmente, o estado, deve implantar, atualizar e dimensionar de forma independente. Esse problema é difícil de resolver porque se resume às suas opções de tecnologias. Para o dimensionamento, entender como particionar (ou fragmentar) o código e o estado é desafiador. Quando o código e o estado usam tecnologias diferentes, que são comuns hoje, os scripts de implantação do seu microserviço precisam ser capazes de dimensioná-los. Essa separação também é sobre agilidade e flexibilidade, para que você possa atualizar alguns dos microserviços sem precisar atualizar todos eles de uma vez.

Vamos voltar à nossa comparação entre as abordagens monolíticos e de microserviços por um instante. Este diagrama mostra as diferenças nas abordagens para armazenar o estado:

#### <a name="state-storage-for-the-two-approaches"></a>Armazenamento de estado para as duas abordagens

![Armazenamento de estado da plataforma Service Fabric][Image2]

_*_A abordagem monolítica, à esquerda, tem um banco de dados individual e camadas de tecnologias específicas._*_

_*_A abordagem de microserviços, à direita, tem um grafo de microserviços interconectados, em que o estado normalmente é definido como escopo para o microserviço e várias tecnologias são usadas._*_

Uma abordagem monolítica, o aplicativo geralmente usa um único banco de dados. A vantagem de usar um banco de dados é que ele está em um único local, o que facilita a implantação. Cada componente pode ter uma única tabela para armazenar seu estado. As equipes precisam separar estritamente estado, que é um desafio. Inevitavelmente, alguém ficará tentado a adicionar uma coluna a uma tabela de clientes existente, fazer uma junção entre tabelas e criar dependências na camada de armazenamento. Quando isso acontecer, não será possível dimensionar os componentes individuais.

Na abordagem dos microsserviços, cada serviço gerencia e armazena seu próprio estado. Cada serviço é responsável por dimensionar o estado e o código juntos para atender às demandas do serviço. Uma desvantagem é que, quando você precisa criar exibições, ou consultas, dos dados do seu aplicativo, você precisa consultar em vários repositórios de estado. Esse problema normalmente é resolvido por um microserviço separado que cria uma exibição em uma coleção de microservices. Se você precisar executar várias consultas improvisadas nos dados, considere gravar os dados de cada microserviço em um serviço de data warehouse para análise offline.

Os microserviços têm controle de versão. É possível que versões diferentes de um microserviço sejam executadas lado a lado. Uma versão mais recente de um microserviço pode falhar durante uma atualização e precisa ser revertida para uma versão anterior. O controle de versão também é útil para testes A/B, em que diferentes usuários experimentam versões diferentes do serviço. Por exemplo, é comum atualizar um microserviço para um conjunto específico de clientes para testar novas funcionalidades antes de implantá-lo mais amplamente.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interage com outros microservices em protocolos e interfaces bem-definidos

Nos últimos 10 anos, foram publicadas informações abrangentes que descrevem os padrões de comunicação em arquiteturas orientadas a serviços. Em geral, a comunicação do serviço usa uma abordagem do REST com os protocolos TCP e HTTP, e XML ou JSON como formato de serialização. Do ponto de vista da interface, trata-se de fazer uma abordagem de design da Web. Mas nada deve impedir que você use protocolos binários ou seus próprios formatos de dados. Apenas lembre-se de que as pessoas terão um tempo mais difícil de usar seus microserviços se esses protocolos e formatos não estiverem disponíveis em aberto.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Tem um nome exclusivo (URL) usado para determinar sua localização

O microsserviço precisa ser endereçável independentemente do local em que está sendo executado. Se você estiver pensando em máquinas e qual delas está executando um microserviço específico, as coisas podem ficar ruins rapidamente.

Da mesma forma que o DNS resolve um determinado URL para uma máquina específica, seu micros serviço precisa de um nome exclusivo para que sua localização atual seja detectável. Os microserviços precisam de nomes endereçáveis que sejam independentes da infraestrutura em que estão sendo executados. Isso implica que há uma interação entre a maneira como seu serviço é implantado e como ele é detectado, porque deve haver um Registro do serviço. Quando um computador falha, o serviço de registro precisa informar para você para onde o serviço foi movido.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Permanece consistente e disponível na presença de falhas

Lidar com falhas inesperadas é um dos problemas mais difíceis de se resolver, especialmente em um sistema distribuído. Grande parte do código que escrevemos como desenvolvedores é para lidar com exceções. Durante os testes, também gastamos mais tempo na manipulação de exceções. O processo é mais envolvido do que escrever código para lidar com falhas. O que acontece quando o computador no qual o microserviço está em execução falha? Você precisa detectar a falha, que é um problema difícil por si só. Mas você também precisa reiniciar o seu microserviço.

Para disponibilidade, um microserviço precisa ser resiliente a falhas e ser capaz de reiniciar em outro computador. Além desses requisitos de resiliência, os dados não devem ser perdidos e os dados precisam permanecer consistentes.

A resiliência é difícil de alcançar quando ocorrem falhas durante um upgrade de aplicativo. O microsserviço, trabalhando com o sistema de implantação, não precisa fazer a recuperação. Ele precisa determinar se ele pode continuar a avançar para a versão mais recente ou reverter para uma versão anterior para manter um estado consistente. Você precisa considerar algumas perguntas, como se máquinas suficientes estão disponíveis para continuar avançando e como recuperar versões anteriores do microserviço. Para tomar essas decisões, você precisa do Microservice para emitir informações de integridade.

### <a name="reports-health-and-diagnostics"></a>Relatórios de integridade e diagnóstico

Pode parecer óbvio e, muitas vezes, é ignorado, mas um microserviço precisa relatar sua integridade e diagnósticos. Caso contrário, você tem pouca percepção sobre sua integridade de uma perspectiva de operações. Correlacionar eventos de diagnóstico em um conjunto de serviços independentes e lidar com distorções de relógio da máquina para entender a ordem dos eventos é um desafio. Da mesma forma que você interage com um micros serviço em relação a protocolos e formatos de dados acordados, é necessário padronizar como registrar eventos de integridade e diagnóstico que acabarão acabando em um armazenamento de eventos para consulta e visualização. Com uma abordagem de microserviços, diferentes equipes precisam concordar em um único formato de log. Precisa ser uma abordagem consistente para exibir os eventos de diagnóstico no aplicativo inteiro.

Integridade é diferente de diagnóstico. Integridade significa que o microsserviço reporta seu estado atual para tomar as devidas ações. Um bom exemplo é trabalhar com mecanismos de atualização e implantação para manter a disponibilidade. Embora um serviço possa não estar íntegro no momento devido a uma falha de processo ou reinicialização do computador, o serviço ainda pode estar operacional. A última coisa de que você precisa é tornar a situação pior iniciando uma atualização. A melhor abordagem é investigar primeiro ou permitir o tempo de recuperação do microserviço. Os eventos de integridade de um microsserviço permitem tomar decisões bem informadas e ajudam realmente a criar serviços que recuperam a si próprios.

## <a name="guidance-for-designing-microservices-on-azure"></a>Diretrizes para a criação de microserviços no Azure

Visite o centro de arquitetura do Azure para obter orientação sobre como [projetar e criar microserviços no Azure](/azure/architecture/microservices/).

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric como uma plataforma de microsserviço

O Azure Service Fabric surgiu quando a Microsoft fez a transição da entrega de produtos in a box, que normalmente eram monolíticos, para fornecer serviços. A experiência de criação e operação de serviços grandes, como o banco de dados SQL do Azure e Azure Cosmos DB, Service Fabric moldado. A plataforma evoluiu ao longo do tempo conforme mais serviços o adotaram. O Service Fabric tinha que ser executado não apenas no Azure, mas também em implantações autônomas do Windows Server.

_*_O objetivo do Service Fabric é resolver os problemas difíceis de criar e executar um serviço e usar recursos de infraestrutura com eficiência, para que as equipes possam resolver problemas de negócios usando uma abordagem de microserviços._*_

Este breve vídeo apresenta o Service Fabric e os microsserviços:
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric/player]

O Service Fabric ajuda você a criar aplicativos que usam uma abordagem de micros serviços, fornecendo:

_ Uma plataforma que fornece serviços do sistema para implantar, atualizar, detectar e reiniciar serviços com falha, descobrir serviços, rotear mensagens, gerenciar estado e monitorar a integridade.
* A capacidade de implantar aplicativos que sejam executados em contêineres ou como processos. O Service Fabric é um orquestrador de contêiner e processo.
* APIs de programação produtivas para ajudá-lo a criar aplicativos como microservices: [ASP.NET Core, Reliable Actors e Reliable Services](service-fabric-choose-framework.md). Por exemplo, você pode obter informações de integridade e diagnóstico, ou você pode tirar proveito da alta disponibilidade interna.

***Service Fabric é independente de como você cria seu serviço, e você pode usar qualquer tecnologia. Mas ele fornece APIs de programação internas que facilitam a criação de microserviços.** _

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrando aplicativos existentes para o Service Fabric

Service Fabric permite reutilizar o código existente e moderniza-lo com novos microservices. Há cinco estágios para a modernização do aplicativo, e você pode iniciar e parar em qualquer estágio. Os estágios são:

1) Comece com um aplicativo monolítico tradicional.  
2) Migrá. Use contêineres ou executáveis convidados para hospedar o código existente no Service Fabric.  
3) Modernizar. Adicione novos microservices junto com o código em contêineres existente.  
4) Inova. Quebre o aplicativo monolítico em microserviços com base na necessidade.  
5) Transforme aplicativos em microserviços. Transforme aplicativos monolíticos existentes ou crie novos aplicativos Greenfield.

![Migração para microservices][Image3]

Lembre-se de que você pode _start e parar em qualquer um desses estágios *. Você não precisará progredir para o próximo estágio. 

Vejamos exemplos para cada um desses estágios.

**Migrar**  
Por dois motivos, muitas empresas estão migrando aplicativos monolíticos existentes para contêineres:

* Redução de custos, devido à consolidação e remoção de hardware existente ou devido à execução de aplicativos em densidade mais alta.
* Um contrato de implantação consistente entre desenvolvimento e operações.

As reduções de custo são simples. Na Microsoft, muitos aplicativos existentes estão sendo incluídos em contêineres, levando a milhões de dólares em economia. A implantação consistente é mais difícil de avaliar, mas igualmente importante. Isso significa que os desenvolvedores podem escolher as tecnologias que as atendam, mas as operações aceitarão apenas um único método para implantar e gerenciar os aplicativos. Ele alivia as operações de ter que lidar com a complexidade de dar suporte a diferentes tecnologias sem forçar os desenvolvedores a escolher apenas determinadas. Essencialmente, cada aplicativo está em contêineres em imagens de implantação independentes.

Muitas organizações param aqui. Eles já têm os benefícios dos contêineres, e Service Fabric fornece a experiência de gerenciamento completa, incluindo implantação, atualizações, controle de versão, reversões e monitoramento de integridade.

**Modernizar**  
A modernização é a adição de novos serviços junto com o código em contêineres existente. Se você pretende escrever um novo código, é melhor executar etapas pequenas no caminho de microserviços. Isso pode significar adicionar um novo ponto de extremidade de API REST ou uma nova lógica de negócios. Dessa forma, você inicia o processo de criar novos microserviços e praticar o desenvolvimento e a implantação deles.

**Inovar**  
Uma abordagem de microsserviços acomoda as necessidades de negócios em mudança. Neste estágio, você precisa decidir se deseja começar a dividir o aplicativo monolítico em serviços ou inovação. Um exemplo clássico é quando um banco de dados que você está usando como uma fila de fluxo de trabalho se torna um afunilamento de processamento. Conforme o número de solicitações de fluxo de trabalho aumenta o trabalho precisa ser distribuído para dimensionamento. Pegue essa parte específica do aplicativo que não está dimensionando, ou que precisa ser atualizada com mais frequência e divida-a como um microserviço e inovar.

**Transformar aplicativos em microserviços**  
Nesse estágio, seu aplicativo é totalmente composto de microserviços (ou divididos em). Para alcançar esse ponto, você fez a jornada de microserviços. Você pode começar aqui, mas fazer isso sem uma plataforma de microserviços para ajudá-lo a exigir um investimento significativo.

### <a name="are-microservices-right-for-my-application"></a>Os microsserviços são ideais para meu aplicativo?

Talvez. Na Microsoft, à medida que mais equipes começaram a criar para a nuvem por motivos comerciais, muitas delas perceberam os benefícios de fazer uma abordagem semelhante a um microserviço. O Bing, por exemplo, está usando microserviços há anos. Para as outras equipes, a abordagem dos microsserviços foi nova. As equipes descobriram que havia problemas difíceis de resolver fora de suas principais áreas de capacidade. É por isso que Service Fabric ganhou força como a tecnologia para a criação de serviços.

O objetivo do Service Fabric é reduzir as complexidades da criação de aplicativos de microserviço para que você não precise passar pelo máximo de reformulações dispendiosas. Comece pequeno, dimensione quando necessário, descontinue serviços, adicione novos e evolua com o uso do cliente. Também sabemos que há muitos outros problemas que ainda precisam ser resolvidos para tornar os microsserviços mais acessíveis à maioria dos desenvolvedores. Os contêineres e o modelo de programação de ator são exemplos de pequenas etapas nessa direção. Temos certeza de que mais inovações surgirão para facilitar a abordagem de um microserviço.

## <a name="next-steps"></a>Próximas etapas

* [Microsserviços: uma revolução de aplicativo proporcionada pela nuvem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Centro de Arquitetura do Azure: criando microserviços no Azure](/azure/architecture/microservices/)
* [Práticas recomendadas de aplicativo e cluster do Azure Service Fabric](service-fabric-best-practices-overview.md)
* [Visão geral da terminologia do Service Fabric](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
