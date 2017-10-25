---
title: "Diferenças entre os Serviços de Nuvem e o Service Fabric | Microsoft Docs"
description: "Uma visão geral conceitual para a migração de aplicativos dos Serviços de Nuvem para o Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 0b87b1d3-88ad-4658-a465-9f05a3376dee
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 26c0256f6fa299551d92e9bcd058ca359d8c85b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Saiba mais sobre as diferenças entre os Serviços de Nuvem e o Service Fabric antes de migrar os aplicativos.
O Service Fabric do Microsoft Azure é a plataforma de aplicativos de nuvem de última geração para aplicativos distribuídos altamente escalonáveis e altamente confiáveis. Ele apresenta muitos recursos novos para empacotamento, implantação, atualização e gerenciamento de aplicativos em nuvem distribuídos. 

Este é um guia introdutório para migrar aplicativos dos Serviços de Nuvem para o Service Fabric. Ele se concentra principalmente nas diferenças de arquitetura e de design entre os Serviços de Nuvem e o Service Fabric.

## <a name="applications-and-infrastructure"></a>Aplicativos e infraestrutura
Uma diferença fundamental entre os Serviços de Nuvem e o Service Fabric é a relação entre as VMs, as cargas de trabalho e os aplicativos. Aqui, uma carga de trabalho é definida como o código que você escreve para executar uma tarefa específica ou fornecer um serviço.

* **Os Serviços de Nuvem dizem respeito à implantação de aplicativos como máquinas virtuais.** O código que você escreve está acoplado a uma instância de VM, como uma função de trabalho ou Web. A implantação de uma carga de trabalho nos Serviços de Nuvem é a implantação de uma ou mais instâncias de VM que executam a carga de trabalho. Há uma distinção entre aplicativos e máquinas virtuais; portanto, não há nenhuma definição formal de um aplicativo. Um aplicativo pode ser considerado como um conjunto de instâncias de função de trabalho ou Web em uma implantação de Serviços de Nuvem ou toda uma implantação de Serviços de Nuvem. Neste exemplo, um aplicativo é mostrado como um conjunto de instâncias de função.

![Topologia e aplicativos dos Serviços de Nuvem][1]

* **O Service Fabric diz respeito à implantação de aplicativos em máquinas virtuais existentes ou computadores que executam o Service Fabric no Windows ou no Linux.** Os serviços que você escreve são completamente separados da infraestrutura subjacente, que é abstraída pela plataforma de aplicativo Service Fabric, para que um aplicativo possa ser implantado em vários ambientes. Uma carga de trabalho no Service Fabric é chamada de "serviço", e um ou mais serviços são agrupados em um aplicativo formalmente definido que é executado na plataforma de aplicativo Service Fabric. Vários aplicativos podem ser implantados em um único cluster do Service Fabric.

![Topologia e aplicativos Service Fabric][2]

O Service Fabric em si é uma camada de plataforma de aplicativo que é executada no Windows ou no Linux, enquanto os Serviços de Nuvem são um sistema para a implantação de VMs gerenciadas pelo Azure com cargas de trabalho anexadas.
O modelo de aplicativo Service Fabric tem uma série de vantagens:

* Rapidez na implantação. A criação de instâncias da VM pode ser demorada. No Service Fabric, as VMs são implantadas somente uma vez para formar um cluster que hospeda a plataforma de aplicativo Service Fabric. Daí em diante, os pacotes de aplicativos podem ser implantados no cluster rapidamente.
* Hospedagem de alta densidade. Nos Serviços de Nuvem, uma VM de função de trabalho hospeda uma carga de trabalho. No Service Fabric, os aplicativos são separados das VMs que as executam, o que significa que você pode implantar um grande quantidade de aplicativos em uma pequena quantidade de VMs, podendo reduzir o custo geral de implantações maiores.
* A plataforma Service Fabric pode ser executada em qualquer lugar que tenha computadores com Windows Server ou Linux, seja do Azure ou locais. A plataforma fornece uma camada de abstração sobre a infraestrutura subjacente para que seu aplicativo possa ser executado em diferentes ambientes. 
* Gerenciamento de aplicativo distribuído. O Service Fabric é uma plataforma que não apenas hospeda aplicativos distribuídos, mas também ajuda a gerenciar o ciclo de vida, independentemente do ciclo de vida da VM de hospedagem ou do computador.

## <a name="application-architecture"></a>Arquitetura do aplicativo
A arquitetura de um aplicativo de Serviços de Nuvem geralmente inclui várias dependências de serviço externo, como o Barramento de Serviço, Armazenamento de Tabelas e de Blobs do Azure, SQL, Redis e outros para gerenciar o estado e os dados de um aplicativo e a comunicação entre funções de trabalho e Web em uma implantação de Serviços de Nuvem. Um exemplo de um aplicativo de Serviços de Nuvem completo pode ter esta aparência:  

![Arquitetura dos Serviços de Nuvem][9]

Os aplicativos Service Fabric também podem optar por usar os mesmos serviços externos em um aplicativo completo. Usando essa arquitetura de exemplo de Serviços de Nuvem, o caminho de migração mais simples dos Serviços de Nuvem para o Service Fabric é substituir apenas a implantação de Serviços de Nuvem por um aplicativo Service Fabric, mantendo a arquitetura geral dele. As funções de trabalho e Web podem ser portadas para serviços sem estado do Service Fabric com o mínimo de alterações de código.

![Arquitetura do Service Fabric após migração simples][10]

Neste estágio, o sistema deve continuar a trabalhar como antes. Aproveitando os recursos de serviço com estado do Service Fabric, os armazenamentos de estado externos podem ser internalizados como serviços com estado, quando for o caso. Isso é mais complicado que uma migração simples de funções de trabalho e Web para serviços sem estado do Service Fabric, pois ele requer a criação de serviços personalizados que fornecem funcionalidade equivalente ao seu aplicativo, como os serviços externos faziam antes. Os benefícios de se fazer isso incluem: 

* Removendo dependências externas 
* Unificar a implantação, gerenciamento e modelos de atualização. 

Uma arquitetura de exemplo resultante da internalização desses serviços poderia ter esta aparência:

![Arquitetura do Service Fabric após migração completa][11]

## <a name="communication-and-workflow"></a>Comunicação e fluxo de trabalho
A maioria dos aplicativos do Serviço de Nuvem consistem em mais de uma camada. Da mesma forma, um aplicativo Service Fabric consiste em mais de um serviço (normalmente muitos serviços). Dois modelos comuns de comunicação têm comunicação direta e por meio de armazenamento durável externo.

### <a name="direct-communication"></a>Comunicação direta
Com a comunicação direta, as camadas podem se comunicar diretamente por meio do ponto de extremidade exposto por cada camada. Em ambientes sem estado, como os Serviços de Nuvem, isso significa selecionar uma instância de uma função VM, aleatoriamente ou round-robin, para balancear a carga e conectar-se diretamente ao seu ponto de extremidade.

![Comunicação direta dos Serviços de Nuvem][5]

 A comunicação direta é um modelo de comunicação comum no Service Fabric. A principal diferença entre o Service Fabric e os Serviços de Nuvem é que você se conectar a uma máquina virtual, ao passo que no Service Fabric você se conecta a um serviço. Essa é uma distinção importante por duas razões:

* Os serviços no Service Fabric não são associados às VMs que os hospedam. Os serviços podem mover-se pelo cluster e, na verdade, é o que se espera por vários motivos: balanceamento de recursos, failover, atualizações de aplicativos e da infraestrutura e restrições de posicionamento ou carga. Isso significa que o endereço da instância do serviço pode mudar a qualquer hora. 
* Uma VM no Service Fabric pode hospedar vários serviços, cada um com pontos de extremidade exclusivos.

O Service Fabric fornece um mecanismo de descoberta de serviço chamado Serviço de Nomenclatura, que pode ser usado para resolver endereços de ponto de extremidade de serviços. 

![Comunicação direta do Service Fabric][6]

### <a name="queues"></a>Filas
Um mecanismo de comunicação comum entre camadas em ambientes sem estado, como os Serviços de Nuvem, é o uso de uma fila de armazenamento externo para armazenar permanentemente as tarefas de trabalho de uma camada para outra. Um cenário comum é uma camada da Web que envia os trabalhos para uma fila do Azure ou Barramento de Serviço, onde as instâncias de função de trabalho podem remover os trabalhos da fila e processá-los.

![Comunicação em fila dos Serviços de Nuvem][7]

O mesmo modelo de comunicação pode ser usado no Service Fabric. Isso pode ser útil ao migrar um aplicativo dos Serviços de Nuvem existente para o Service Fabric. 

![Comunicação direta do Service Fabric][8]

## <a name="next-steps"></a>Próximas etapas
O caminho de migração mais simples dos Serviços de Nuvem para o Service Fabric é substituir apenas a implantação dos Serviços de Nuvem por um aplicativo Service Fabric, mantendo a arquitetura geral dele. O artigo a seguir fornece um guia para ajudar a converter uma função de trabalho ou Web em um serviço sem estado do Service Fabric.

* [Migração simples: converter uma função de trabalho ou Web em um serviço sem estado do Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
