---
title: Visão geral do Azure Functions | Microsoft Docs
description: Compreenda como usar o Azure Functions para otimizar cargas de trabalho assíncronas em minutos.
services: functions
documentationcenter: na
author: mattchenderson
manager: jeconnoc
keywords: azure functions, functions, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 10/03/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 4fbe3a5d308559a4ac9e1a781f3a4f385c767f90
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348202"
---
# <a name="an-introduction-to-azure-functions"></a>Uma introdução ao Azure Functions  
O Azure Functions é uma solução para executar facilmente pequenos trechos de código, ou "funções", na nuvem. Você pode simplesmente escrever o código de que necessita para o problema em questão, sem se preocupar com todo o aplicativo ou a infraestrutura para executá-lo. As funções podem tornar o desenvolvimento ainda mais produtivo e você pode usar a linguagem de desenvolvimento de sua escolha, como C#, Java, JavaScript, Python ou PHP. Pague somente pelo tempo de execução do seu código e confie no Azure para dimensioná-lo conforme a necessidade. O Azure Functions permite desenvolver aplicativos [sem servidor](https://azure.microsoft.com/solutions/serverless/) no Microsoft Azure.

Este tópico fornece uma visão geral de alto nível do Azure Functions. Se você quiser começar a usar imediatamente o Functions, comece com [Criar seu primeiro Azure Function](functions-create-first-azure-function.md). Se você estiver procurando informações mais técnicas sobre o Functions, confira a [referência do desenvolvedor](functions-reference.md).

## <a name="features"></a>Recursos
Aqui estão alguns dos principais recursos do Functions:

* **Opção de linguagem** – escreva funções usando a linguagem que quiser: C#, Java, JavaScript, Python ou outra. Consulte as [Linguagens compatíveis](supported-languages.md) para obter a lista completa.
* **Modelo de preços de pagamento por uso** – pague somente pelo tempo gasto na execução de seu código. Veja a opção de plano de hospedagem de consumo na [seção de preços](#pricing).  
* **Traga suas próprias dependências** – o Functions dá suporte a NuGet e NPM e, portanto, você pode usar suas bibliotecas favoritas.  
* **Segurança integrada** – proteja funções disparadas por HTTP com provedores de OAuth como Azure Active Directory, Facebook, Google, Twitter e Conta da Microsoft.  
* **Integração simplificada** – aproveite facilmente as ofertas dos serviços do Azure e de SaaS (software como um serviço). Confira a [seção de integrações](#integrations) para obter alguns exemplos.  
* **Desenvolvimento flexível** – escreva suas funções diretamente no portal ou configure a integração contínua e implante seu código por meio do [GitHub](../app-service/scripts/cli-continuous-deployment-github.md), do [Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md) e de outras [ferramentas de desenvolvimento com suporte](../app-service/deploy-local-git.md).  
* **Software livre** – O Functions é um software livre e [está disponível no GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>O que posso fazer com o Functions?
O Functions é uma ótima solução para processamento de dados, integração de sistemas, trabalho com a IoT (Internet das coisas) e criação de APIs e microsserviços simples. Considere o Functions para tarefas como processamento de imagens ou pedidos, manutenção de arquivos ou tarefas que você deseja executar de maneira agendada. 

O Functions fornece modelos para você poder começar em cenários chave, incluindo os seguintes:

* **HTTPTrigger** – dispara a execução do seu código usando uma solicitação HTTP. Para obter um exemplo, consulte [Criar sua primeira função](functions-create-first-azure-function.md).
* **TimerTrigger** – execute limpeza ou outras tarefas em lotes em uma programação predefinida. Para um exemplo, consulte [Criar uma função disparada por um temporizador](functions-create-scheduled-function.md).
* **CosmosDBTrigger** – processe documentos do Azure Cosmos DB quando eles forem adicionados ou atualizados em coleções em um banco de dados NoSQL. Para saber mais, veja [Associações do Azure Cosmos DB](functions-bindings-cosmosdb-v2.md).
* **BlobTrigger** – processa blobs do Armazenamento do Azure quando são adicionados a contêineres. Você pode usar essa função para o redimensionamento de imagens. Para obter mais informações, consulte [Associações de armazenamento de blobs](functions-bindings-storage-blob.md).
* **QueueTrigger** – responde às mensagens que chegam em uma fila do Armazenamento do Azure. Para saber mais, veja [Associações de armazenamento do Azure Queue](functions-bindings-storage-queue.md).
* **EventGridTrigger**- responder a eventos entregues a uma assinatura na Grade de Eventos do Azure. Dá suporte a um modelo baseado em assinatura para recebimento de eventos, o que inclui a filtragem. Uma boa solução para a criação de arquiteturas baseadas em evento. Para obter um exemplo, veja [Automatizar o redimensionamento de imagens carregadas usando a Grade de Eventos](../event-grid/resize-images-on-storage-blob-upload-event.md).
* **EventHubTrigger** - responder a eventos entregues a um Hub de Eventos do Azure. É especialmente útil em cenários de instrumentação de aplicativos, de processamento de fluxo de trabalho ou experiência do usuário e de Internet das Coisas (IoT). Para obter mais informações, consulte [Associações de Hubs de Eventos](functions-bindings-event-hubs.md).
* **ServiceBusQueueTrigger** – conecte seu código a outros serviços do Azure ou serviços locais por meio da escuta de filas de mensagens. Para obter mais informações, consulte [Associações de Barramento de Serviço](functions-bindings-service-bus.md).
* **ServiceBusTopicTrigger** – conecte seu código a outros serviços do Azure ou serviços locais por meio da assinatura de tópicos. Para obter mais informações, consulte [Associações de Barramento de Serviço](functions-bindings-service-bus.md).

O Azure Functions dá suporte a *gatilhos*, que são maneiras de iniciar a execução do seu código, e *associações*, que são maneiras de simplificar a codificação de dados de entrada e de saída. Para obter uma descrição detalhada dos gatilhos e associações que o Azure Functions fornece, confira a [Referência do desenvolvedor de disparadores e associações do Azure Functions](functions-triggers-bindings.md).

## <a name="integrations"></a>Integrações
O Azure Functions integra-se com uma variedade de serviços do Azure e de terceiros. Esses serviços podem disparar a sua função e iniciar a execução ou podem servir como entrada e saída para seu código. As integrações de serviço a seguir têm suporte do Azure Functions:

* Azure Cosmos DB
* Hubs de eventos do Azure
* Grade de Eventos do Azure
* Hubs de Notificação do Azure
* Barramento de Serviço do Azure (filas e tópicos)
* Armazenamento do Azure (blob, filas e tabelas)
* No local (usando o Barramento de Serviço)
* Twilio (mensagens SMS)

## <a name="pricing"></a>Quanto custa o Functions?
As funções do Azure têm dois tipos de planos de preços. Escolha a opção que melhor atende às suas necessidades: 

* **Plano de consumo** – quando sua função é executada, o Azure fornece todos os recursos computacionais necessários. Você não precisa se preocupar com o gerenciamento de recursos e paga apenas pelo tempo de execução do seu código. 
* **Plano do Serviço de Aplicativo** – executa suas funções da mesma forma que os aplicativos Web. Quando você já estiver usando o Serviço de Aplicativo para os outros aplicativos, poderá executar suas funções no mesmo plano sem custo adicional. 

Para obter mais informações sobre planos de hospedagem, consulte [Comparação de planos de hospedagem do Azure Functions](functions-scale.md). Os detalhes de preços completos estão disponíveis na [página Preço do Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Próximas etapas
* [Criar sua primeira função](functions-create-first-azure-function.md)  
  Crie diretamente sua primeira função usando o início rápido do Azure Functions. 
* [Referência do desenvolvedor do Azure Functions](functions-reference.md)  
  Fornece informações mais técnicas sobre o tempo de execução do Azure Functions e uma referência para funções de codificação e definição de associações e gatilhos.
* [Testando o Azure Functions](functions-test-a-function.md)  
  Descreve várias ferramentas e técnicas para testar suas funções.
* [Como escalar o Azure Functions](functions-scale.md)  
  Discute os planos de serviço disponíveis com o Azure Functions, incluindo o plano de hospedagem de consumo e como escolher o plano certo. 
* [Saiba mais sobre o Serviço de Aplicativo do Azure](../app-service/overview.md)  
  O Azure Functions aproveita o Serviço de Aplicativo do Azure para a funcionalidade básica como implantações, variáveis de ambiente e diagnóstico. 

