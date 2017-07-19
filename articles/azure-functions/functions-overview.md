---
title: "Visão geral do Azure Functions | Microsoft Docs"
description: "Compreenda como usar o Azure Functions para otimizar cargas de trabalho assíncronas em minutos."
services: functions
documentationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, functions, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: cfowler;mahender;glenga
ms.custom: H1Hack27Feb2017, mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7fcf35b99cc410655f2a3b9b6961f5e3bef3aeb5
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="an-introduction-to-azure-functions"></a>Uma introdução ao Azure Functions  
O Azure Functions é uma solução para executar facilmente pequenos trechos de código, ou "funções", na nuvem. Você pode simplesmente escrever o código de que necessita para o problema em questão, sem se preocupar com todo o aplicativo ou a infraestrutura para executá-lo. Funções podem tornar o desenvolvimento ainda mais produtivo e você pode usar a linguagem de desenvolvimento de sua escolha, por exemplo, C#, F#, Node.js, Python ou PHP. Pague somente pelo tempo de execução do seu código e confie no Azure para dimensioná-lo conforme a necessidade. O Azure Functions permite desenvolver aplicativos serverless no Microsoft Azure.

Este tópico fornece uma visão geral de alto nível do Azure Functions. Se você quiser começar a usar imediatamente o Azure Functions, comece com [Criar sua primeira função](functions-create-first-azure-function.md). Se você estiver procurando informações mais técnicas sobre o Functions, confira a [referência do desenvolvedor](functions-reference.md).

## <a name="features"></a>Recursos
Aqui estão alguns dos principais recursos do Azure Functions:

* **Opção de linguagem** – escreva funções usando C#, F#, Node.js, Python, PHP, batch, bash ou qualquer executável.
* **Modelo de preços de pagamento por uso** – pague somente pelo tempo gasto na execução de seu código. Veja a opção de plano de hospedagem de consumo na [seção de preços](#pricing).  
* **Traga suas próprias dependências** – o Functions dá suporte a NuGet e NPM e, portanto, você pode usar suas bibliotecas favoritas.  
* **Segurança integrada** – proteja funções disparadas por HTTP com provedores de OAuth como Azure Active Directory, Facebook, Google, Twitter e Conta da Microsoft.  
* **Integração simplificada** – aproveite facilmente as ofertas dos serviços do Azure e de SaaS (software como um serviço). Confira a [seção de integrações](#integrations) para obter alguns exemplos.  
* **Desenvolvimento flexível** – escreva suas funções diretamente no portal ou configure a integração contínua e implante seu código por meio do GitHub, do Visual Studio Team Services e de outras [ferramentas de desenvolvimento com suporte](../app-service-web/web-sites-deploy.md#deploy-using-an-ide).  
* **Software livre** – O Functions é um software livre e [está disponível no GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>O que posso fazer com o Functions?
O Azure Functions é uma ótima solução para processamento de dados, integração de sistemas, trabalho com a IoT (Internet das coisas) e criação de APIs e microsserviços simples. Considere o Functions para tarefas como processamento de imagens ou pedidos, manutenção de arquivos ou tarefas que você deseja executar de maneira agendada. 

O Functions fornece modelos para você poder começar em cenários chave, incluindo os seguintes:

* **BlobTrigger** – processa blobs do Armazenamento do Azure quando são adicionados a contêineres. Você pode usar essa função para o redimensionamento de imagens.
* **EventHubTrigger** - responder a eventos entregues a um Hub de Eventos do Azure. É especialmente útil em cenários de instrumentação de aplicativos, de processamento de fluxo de trabalho ou experiência do usuário e de Internet das Coisas (IoT).
* **Webhook genérico** – processa solicitações de webhook HTTP de qualquer serviço que dá suporte a webhooks.
* **Webhook GitHub** – responde a eventos que ocorrem em seus repositórios GitHub. Para obter um exemplo, confira [Criar uma função de API ou webhook](functions-create-a-web-hook-or-api-function.md).
* **HTTPTrigger** – dispara a execução do seu código usando uma solicitação HTTP.
* **QueueTrigger** – responde às mensagens que chegam em uma fila do Armazenamento do Azure. Para ver um exemplo, confira [Criar uma Azure Function vinculada a um serviço do Azure](functions-create-an-azure-connected-function.md).
* **ServiceBusQueueTrigger** – conecte seu código a outros serviços do Azure ou serviços locais por meio da escuta de filas de mensagens. 
* **ServiceBusTopicTrigger** – conecte seu código a outros serviços do Azure ou serviços locais por meio da assinatura de tópicos. 
* **TimerTrigger** – execute limpeza ou outras tarefas em lotes em uma programação predefinida. Para ver um exemplo, confira [Criar um função de processamento de eventos](functions-create-an-event-processing-function.md).

O Azure Functions dá suporte a *gatilhos*, que são maneiras de iniciar a execução do seu código, e *associações*, que são maneiras de simplificar a codificação de dados de entrada e de saída. Para obter uma descrição detalhada dos gatilhos e associações que o Azure Functions fornece, confira a [Referência do desenvolvedor de disparadores e associações do Azure Functions](functions-triggers-bindings.md).

## <a name="integrations"></a>Integrações
O Azure Functions integra-se com uma variedade de serviços do Azure e de terceiros. Esses serviços podem disparar a sua função e iniciar a execução ou podem servir como entrada e saída para seu código. As integrações de serviço a seguir têm suporte do Azure Functions. 

* BD Cosmos do Azure
* Hubs de eventos do Azure 
* Aplicativos Móveis do Azure (tabelas)
* Hubs de Notificação do Azure
* Barramento de Serviço do Azure (filas e tópicos)
* Armazenamento do Azure (blob, filas e tabelas) 
* GitHub (webhooks)
* No local (usando o Barramento de Serviço)
* Twilio (mensagens SMS)

## <a name="pricing"></a>Quanto custa o Functions?
O Azure Functions tem dois tipos de planos de preços. Escolha a opção que melhor atende às suas necessidades: 

* **Plano de consumo** – quando sua função é executada, o Azure fornece todos os recursos computacionais necessários. Você não precisa se preocupar com o gerenciamento de recursos e paga apenas pelo tempo de execução do seu código. 
* **Plano do Serviço de Aplicativo** – executa suas funções como aplicativos Web, móveis e de API. Quando você já estiver usando o Serviço de Aplicativo para os outros aplicativos, poderá executar suas funções no mesmo plano sem custo adicional. 

Os detalhes de preços completos estão disponíveis na [página Preço do Functions](https://azure.microsoft.com/pricing/details/functions/). Para saber mais sobre o dimensionamento de suas funções, confira [Como dimensionar o Azure Functions](functions-scale.md).

## <a name="next-steps"></a>Próximas etapas
* [Criar sua primeira função](functions-create-first-azure-function.md)  
  Crie diretamente sua primeira função usando o início rápido do Azure Functions. 
* [Referência do desenvolvedor do Azure Functions](functions-reference.md)  
  Fornece informações mais técnicas sobre o tempo de execução do Azure Functions e uma referência para funções de codificação e definição de associações e gatilhos.
* [Testando o Azure Functions](functions-test-a-function.md)  
  Descreve várias ferramentas e técnicas para testar suas funções.
* [Como escalar o Azure Functions](functions-scale.md)  
  Discute os planos de serviço disponíveis com o Azure Functions, incluindo o plano de hospedagem de consumo e como escolher o plano certo. 
* [Saiba mais sobre o Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md)  
  O Azure Functions utiliza a plataforma Serviço de Aplicativo do Azure para a funcionalidade básica, como implantações, variáveis de ambiente e diagnóstico. 


