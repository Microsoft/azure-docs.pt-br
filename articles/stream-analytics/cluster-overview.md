---
title: Visão geral dos clusters do Azure Stream Analytics
description: Saiba mais sobre a oferta dedicada de locatário único do Cluster do Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: e5157a02f97370b20db85bf5e3e8aae98a2d8668
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709284"
---
# <a name="overview-of-azure-stream-analytics-cluster"></a>Visão geral do cluster do Azure Stream Analytics

O Cluster do Azure Stream Analytics oferece uma implantação de locatário único para cenários de streaming complexos e mais rígidos. Em grande escala, os clusters do Stream Analytics podem processar mais de 200 MB/segundo em tempo real. Os trabalhos do Stream Analytics executados em clusters dedicados podem aproveitar todos os recursos da oferta Standard e incluem suporte para conectividade de link privado com entradas e saídas.

Os clusters do Stream Analytics são cobrados por SUs (unidades de streaming) que representam a quantidade de recursos de CPU e memória alocados ao cluster. Uma unidade de streaming é a mesma nas ofertas Standard e Dedicado. Você pode comprar 36, 72, 108, 144, 180 ou 216 SUs para cada cluster. Um cluster do Stream Analytics pode funcionar como a plataforma de streaming para a sua organização e pode ser compartilhado por diferentes equipes que trabalham em vários casos de uso.

## <a name="what-are-stream-analytics-clusters"></a>O que são clusters do Stream Analytics

Os clusters do Stream Analytics são ativados pelo mesmo mecanismo que ativa os trabalhos do Stream Analytics em execução em um ambiente multilocatário. O cluster dedicado de locatário único traz os seguintes recursos:

* Hospedagem de único locatário, sem ruído de outros locatários. Seus recursos ficam verdadeiramente "isolados" e têm um melhor desempenho quando há intermitência no tráfego.

* Escale o cluster entre 36 e 216 SUs, conforme seu uso de streaming aumentar com o passar do tempo.

* O suporte à VNET que permite que os seus trabalhos do Stream Analytics se conectem a outros recursos com segurança por meio de pontos de extremidade privados.

* Capacidade de criar funções C# definidas pelo usuário e desserializadores personalizados em qualquer região.

* Custo de manutenção zero, permitindo que você concentre seus esforços na criação de soluções de análise em tempo real.

## <a name="how-to-get-started"></a>Como começar

[Crie um cluster do Stream Analytics](create-cluster.md) por meio do [portal do Azure](https://aka.ms/asaclustercreateportal). Caos você tenha alguma dúvida ou precise de ajuda com a integração, entre em contato com a [equipe do Stream Analytics](mailto:askasa@microsoft.com).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-do-i-choose-between-a-stream-analytics-cluster-and-a-stream-analytics-job"></a>Como fazer para escolher entre um cluster e um trabalho do Stream Analytics?

A maneira mais fácil de começar é criar e desenvolver um trabalho do Stream Analytics para se familiarizar com o serviço e ver como ele pode atender aos seus requisitos de análise.

Os trabalhos do Stream Analytics isoladamente não dão suporte a VNETs. Se as entradas ou as saídas estiverem protegidas atrás de um firewall ou de uma Rede Virtual do Azure, você terá as duas seguintes opções:

* Se o computador local tiver acesso aos recursos de entrada e de saída protegidos por uma VNET (por exemplo, Hubs de Eventos do Azure ou Banco de Dados SQL do Azure), você poderá [instalar as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-tools-for-visual-studio-install.md) no computador local. Desenvolva e [teste os trabalhos do Stream Analytics no local](stream-analytics-live-data-local-testing.md) no dispositivo sem gerar nenhum custo. Quando estiver pronto para usar o Stream Analytics na sua arquitetura, crie um cluster do Stream Analytics, configure pontos de extremidade privados e execute seus trabalhos em escala.

* Crie um cluster do Stream Analytics, configure o cluster com os pontos de extremidade privados necessários para o pipeline e execute seus trabalhos do Stream Analytics no cluster.

### <a name="what-performance-can-i-expect"></a>Que tipo de desempenho posso esperar?

Uma SU é a mesma nas ofertas Standard e Dedicado. Um trabalho individual que utiliza um cluster completo de 36 SUs pode atingir uma taxa de transferência de aproximadamente 36 MB/segundo com latência de milissegundos. O número exato depende do formato dos eventos e do tipo de análise. Como ele é dedicado, o cluster do Stream Analytics oferece garantias de desempenho mais confiáveis. Todos os trabalhos em execução no cluster só pertencem a você.

### <a name="can-i-scale-my-cluster"></a>Posso escalar meu cluster?

Sim. Você pode configurar com facilidade a capacidade do seu cluster, permitindo [escalar ou reduzi-lo verticalmente](scale-cluster.md), conforme necessário, para atender à demanda em constante mudança.

### <a name="can-i-run-my-existing-jobs-on-these-new-clusters-ive-created"></a>Posso executar meus trabalhos existentes nesses novos clusters criados por mim?

Sim. Você pode vincular seus trabalhos ao cluster recém-criado do Stream Analytics e executá-los como de costume. Você não precisa recriar do zero os trabalhos do Stream Analytics que você já tem.

### <a name="how-much-will-these-clusters-cost-me"></a>Quanto esses clusters custarão?

Os clusters do Stream Analytics são cobrados de acordo com a capacidade de SU escolhida. Eles são cobrados por hora e não há custos adicionais por trabalho em execução nesses clusters. Confira a [página de preços do Serviço de Link Privado](https://azure.microsoft.com/pricing/details/private-link/) para obter atualizações de cobrança do ponto de extremidade privado.

### <a name="which-inputs-and-outputs-can-i-privately-connect-to-from-my-stream-analytics-cluster"></a>Às quais entradas e saídas posso me conectar em modo privada do meu cluster do Stream Analytics?

O Stream Analytics dá suporte a vários tipos de entrada e saída. [Crie pontos de extremidade privados](private-endpoints.md) no cluster que permitem que os trabalhos acessem os recursos de entrada e saída. No momento, o Banco de Dados SQL do Azure, o Armazenamento do Azure, o Azure Data Lake Storage Gen2, o Hub de Eventos do Azure e o Barramento de Serviço do Azure são serviços com suporte e outros tipos serão adicionados em breve. 

## <a name="next-steps"></a>Próximas etapas

Agora você tem uma visão geral do cluster do Azure Stream Analytics. Em seguida, você poderá criar seu cluster e executar seu trabalho do Stream Analytics: 

* [Criar um cluster do Stream Analytics](create-cluster.md)
* [Gerenciar pontos de extremidade privados em um cluster do Azure Stream Analytics](private-endpoints.md)
* [Como gerenciar trabalhos do Stream Analytics em um cluster do Stream Analytics](manage-jobs-cluster.md)
* [Criar um trabalho de Stream Analytics](stream-analytics-quick-create-portal.md)
