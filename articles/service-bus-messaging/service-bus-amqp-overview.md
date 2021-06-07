---
title: Visão geral do AMQP 1,0 no barramento de serviço do Azure
description: Saiba como o barramento de serviço do Azure dá suporte a Advanced Message Queuing Protocol (AMQP), um protocolo padrão aberto.
ms.topic: article
ms.date: 02/17/2021
ms.openlocfilehash: b2ca126312f5fc3da2a7ff6e20a9ade252f489f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653876"
---
# <a name="amqp-10-support-in-service-bus"></a>Suporte ao AMQP 1.0 no Barramento de Serviço
O serviço de nuvem do barramento de serviço do Azure usa o [protocolo de enfileiramento de mensagens avançado (AMQP) 1,0](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html) como seus principais meios de comunicação. A Microsoft tem participado de parceiros em todo o setor, clientes e fornecedores de agentes de mensagens concorrentes, desenvolver e evoluir AMQP na última década, com novas extensões sendo desenvolvidas no [comitê técnico de AMQP da Oasis](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp). AMQP 1,0 é um padrão ISO e IEC ([iso 19464:20149](https://www.iso.org/standard/64955.html)). 

O AMQP permite que você crie aplicativos híbridos de plataforma cruzada usando um protocolo padrão aberto e independente de fornecedor e de implementação. Você pode criar aplicativos que usam componentes que são criados usando estruturas e linguagens diferentes e que são executados em sistemas operacionais diferentes. Todos esses componentes podem se conectar ao Service Bus e perfeitamente trocam mensagens de negócios estruturados com eficiência e fidelidade total.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Introdução: O que é AMQP 1.0 e por que é importante?
Tradicionalmente, os produtos de middleware orientados à mensagens têm usado protocolos proprietários para comunicação entre os aplicativos cliente e os agentes. Isso significa que, depois que você selecionou agente de mensagens de um determinado fornecedor, é necessário usar bibliotecas do fornecedor para conectar os aplicativos de cliente para esse agente. Isso resulta em um grau de dependência desse fornecedor, uma vez que a portabilidade de um aplicativo para um produto diferente exige a recodificação de todos os aplicativos conectados. Na comunidade Java, os padrões de API específicos à linguagem, como Java Message Service (JMS) e as abstrações do Spring Framework, diminuíram isso de certa forma, mas têm um escopo de recursos muito estreito e excluem os desenvolvedores que usam outras linguagens.

Além disso, é complicado conectar agentes de mensagens de diferentes fornecedores. Isso geralmente requer uma ponte no nível de aplicativo para mover as mensagens de um sistema para outro e para converter entre os formatos de mensagem proprietários. Esse é um requisito comum. Por exemplo, quando você deve fornecer uma nova interface unificada diferentes sistemas mais antigos para ou integrar sistemas de TI após uma fusão. O AMQP permite a interconexão de agentes de conexão diretamente, por exemplo, usando roteadores como o [roteador de expedição do Apache QPID](https://qpid.apache.org/components/dispatch-router/index.html) ou o agente nativo "shovels" como o de [RabbitMQ](service-bus-integrate-with-rabbitmq.md).

A indústria de software é uma empresa de avanço rápido. Novas linguagens de programação e estruturas de aplicativo são inventadas em um ritmo por vezes desconcertante. Da mesma forma, os requisitos dos sistemas de TI evoluem ao longo do tempo e os desenvolvedores querem tirar proveito dos recursos da plataforma mais recentes. No entanto, às vezes o fornecedor de mensagens selecionado não suporta essas plataformas. Se os protocolos de mensagens forem proprietários, não será possível que outras pessoas forneçam bibliotecas para essas novas plataformas. Portanto, você deve usar abordagens como criar gateways ou pontes para que você possa continuar a usar o produto do sistema de mensagens.

O desenvolvimento do AMQP (Advanced Message Queueing Protocol) 1.0 foi motivado por tais problemas. Ela se originou no JP Morgan Chase, que, como as empresas de serviços financeiras mais, são usuários pesados de middleware orientado a mensagens. O objetivo era simples: criar um protocolo de mensagens de padrão livre que torna possível a criação de aplicativos com base em mensagem de uso dos componentes incorporados usando diferentes linguagens, estruturas e sistemas operacionais, tudo isso usando componentes de ponta em uma variedade de fornecedores.

## <a name="amqp-10-technical-features"></a>Recursos técnicos do AMQP 1.0
O AMQP (Advanced Message Queuing Protocol) 1.0 é um protocolo de mensagens eficiente, confiável e conectado que pode ser usado para criar aplicativos de mensagens avançados entre plataformas. O protocolo tem um simples objetivo: definir a mecânica da transferência segura, confiável e eficiente de mensagens entre duas partes. As mensagens em si são codificadas usando uma representação de dados portáteis que permite heterogêneos remetentes e receptores trocar mensagens comerciais estruturados com fidelidade total. A seguir está um resumo dos recursos mais importantes:

* **Eficiente**: o AMQP 1.0 é um protocolo orientado para conexão que usa uma codificação binária para as instruções de protocolo e as mensagens de negócios transferidas por ele. Ele incorpora esquemas sofisticadas de controle de fluxo para maximizar a utilização da rede e os componentes conectados. Dito isso, o protocolo foi projetado para obter um equilíbrio entre a eficiência, a flexibilidade e a interoperabilidade.
* **Confiável**: o protocolo AMQP 1.0 permite que mensagens sejam trocadas com uma variedade de garantias de confiabilidade, de disparar e esquecer a confiável, entrega confirmada apenas uma vez.
* **Flexível**: o AMQP 1.0 é um protocolo flexível que pode ser usado para oferecer suporte a topologias diferentes. O mesmo protocolo pode ser usado para comunicações de cliente a cliente, agente de cliente e agente de agente.
* **Independente do modelo de agente**: a especificação do AMQP 1.0 não faz quaisquer requisitos no modelo de mensagens usado por um agente. Isso significa que é possível adicionar facilmente suporte AMQP 1.0 para corretores de mensagens existentes.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 é um Padrão (com P maiúsculo)
O AMQP 1.0 é um padrão internacional aprovado pela ISO e IEC como ISO/IEC 19464:2014.

AMQP 1.0 foi desenvolvida desde 2008 por um grupo central de mais de 20 empresas, fornecedores de tecnologia e empresas de usuário final. Durante esse tempo, empresas de usuário contribuíram suas necessidades de negócios reais e os fornecedores de tecnologia se desenvolveram o protocolo para atender a esses requisitos. Durante todo o processo, fornecedores participaram workshops colaboraram para validar a interoperabilidade entre suas implementações.

Em outubro de 2011, o trabalho de desenvolvimento para um comitê técnico dentro da organização para o avanço dos Structured Information Standards (OASIS) e o Padrão OASIS AMQP 1.0 foi lançado em outubro de 2012. As seguintes empresas participaram do Comitê técnico durante o desenvolvimento do padrão:

* **Fornecedores de tecnologia**: Axway Software, Huawei Technologies, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, progresso Software, Red Hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.
* **Empresas de usuário**: Bank of America, Credit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

As cadeiras atuais do [comitê técnico da Oasis AMQP](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) representam a Red Hat e a Microsoft.

Alguns dos benefícios de padrões abertos citados com frequência incluem:

* Menos chance de bloqueio de fornecedor
* Interoperabilidade
* Ampla disponibilidade de bibliotecas e ferramentas
* Proteção contra obsolescência
* Disponibilidade de equipe capacitada
* Risco menor e gerenciável

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 e Service Bus
O suporte do AMQP 1,0 no barramento de serviço do Azure significa que você pode aproveitar o enfileiramento do barramento de serviço e publicar/assinar recursos de mensagens orientadas de uma variedade de plataformas usando um protocolo binário eficiente. Além disso, você pode criar aplicativos formados por componentes criados com o uso de uma mistura de linguagens, estruturas e sistemas operacionais.

O diagrama a seguir ilustra um exemplo de implantação no qual clientes Java em execução no Linux, escrito usando a API JMS (Java Message Service) padrão e clientes .NET em execução no Windows, trocam mensagens por meio do Barramento de Serviço usando o AMQP 1.0.

![Diagrama que mostra uma troca de mensagens de um barramento de serviço com dois ambientes Linux e dois ambientes do Windows.][0]

**Figura 1: Exemplo de cenário de implantação mostrando mensagens entre plataformas usando o Barramento de Serviço e o AMQP 1.0**

Todas as bibliotecas de cliente do barramento de serviço com suporte disponíveis por meio do SDK do Azure usam o AMQP 1,0.

- [Barramento de Serviço do Azure para .NET](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Bibliotecas do Barramento de Serviço do Azure para Java](/java/api/overview/azure/servicebus?preserve-view=true)
- [Provedor do Barramento de Serviço do Azure para Java JMS 2.0](how-to-use-java-message-service-20.md)
- [Módulos do Barramento de Serviço do Azure para JavaScript e TypeScript](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Bibliotecas do Barramento de Serviço do Azure para Python](/python/api/overview/azure/servicebus?preserve-view=true)

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

Além disso, você pode usar o barramento de serviço de qualquer pilha de protocolo compatível com AMQP 1,0:

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="summary"></a>Resumo
* O AMQP (Advanced Message Queuing Protocol) 1.0 é um protocolo de mensagens aberto e confiável que pode ser usado para criar aplicativos híbridos de mensagens avançados entre plataformas. O AMQP 1.0 é um padrão OASIS.

## <a name="next-steps"></a>Próximas etapas
Está pronto(a) para saber mais? Visite os links a seguir:

* [Usando o Barramento de Serviço do .NET com AMQP]
* [Usando o Barramento de Serviço do Java com AMQP]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Usando o Barramento de Serviço do .NET com AMQP]: service-bus-amqp-dotnet.md
[Usando o Barramento de Serviço do Java com AMQP]: ./service-bus-java-how-to-use-jms-api-amqp.md

