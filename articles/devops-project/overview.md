---
title: Visão geral do Azure DevOps Projects | Microsoft Docs
description: Entender o valor do Azure DevOps Projects
services: devops-project
documentationcenter: ''
author: mlearned
manager: douge
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: fed4f0976dd118186c122b0152f255d0c452c0ce
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404491"
---
# <a name="overview-of-azure-devops-projects"></a>Visão geral do Azure DevOps Projects

 O Azure DevOps Projects facilita o uso inicial do Azure. Ele ajuda você a iniciar seu tipo de aplicativo favorito no serviço do Azure de sua escolha em apenas algumas etapas rápidas do portal do Azure. 

 O DevOps Projects configura tudo o que você precisa para desenvolver, implantar e monitorar seu aplicativo. É possível usar o painel do DevOps Projects para monitorar confirmações, compilações e implantações de código, tudo a partir de um único modo de exibição no portal do Azure.

## <a name="why-should-i-use-devops-projects"></a>Por que devo usar o DevOps Projects?

  O DevOps Projects automatiza a instalação de um pipeline inteiro de CI (integração contínua) e CD (entrega contínua) para o Azure.  É possível iniciar com o código existente ou usar um dos aplicativos de exemplo fornecidos. Em seguida, você poderá implantar rapidamente esse aplicativo em vários serviços do Azure, como Máquinas Virtuais, Serviço de Aplicativo, AKS (Serviço de Kubernetes do Azure), Banco de Dados SQL do Azure e Azure Service Fabric.  

  O DevOps Projects faz todo o trabalho de configuração inicial de um pipeline de DevOps, incluindo tudo desde a configuração do repositório inicial do Git, configuração do pipeline de CI/CD, criação de um recurso do Application Insights para monitorar e fornecer uma única exibição de toda a solução com a criação de um painel do DevOps Projects no portal do Azure.

É possível usar o DevOps Projects para:

* Implantar rapidamente seu aplicativo no Azure
* Automatizar a configuração de um pipeline de CI/CD
* Exibir e compreender como configurar corretamente um pipeline de CI/CD
* Personalizar ainda mais os pipelines de lançamento com base em seus cenários específicos

## <a name="how-do-i-use-devops-projects"></a>Como devo usar o DevOps Projects?

  O DevOps Projects está disponível no portal do Azure. Crie um recurso do DevOps Projects assim como você cria qualquer outro recurso do Azure no portal. O DevOps Projects fornece uma experiência passo a passo do tipo assistente para as várias opções de configuração.  

Você pode escolher várias opções de configuração como parte da instalação inicial. Estas opções incluem:

* Usar o aplicativo de exemplo fornecido ou trazer seu próprio código
* Selecionar uma linguagem de aplicativo
* Escolher uma estrutura de aplicativo com base na linguagem
* Selecionar um serviço do Azure (destino de implantação)
* Criar uma nova organização do Azure DevOps ou usar uma organização existente 
* Escolher sua assinatura do Azure
* Escolher o local dos serviços do Azure
* Escolher entre vários tipos de preços dos serviços do Azure

Depois de usar o DevOps Projects, também é possível excluir todos os recursos de um único local a partir do painel do DevOps Projects no portal do Azure.

## <a name="devops-projects-and-azure-devops-integration"></a>Integração do DevOps Projects e Azure DevOps

O DevOps Projects faz parte da plataforma do Azure DevOps. O DevOps Projects automatiza todo o trabalho necessário em Pipelines do Azure para configurar um pipeline de CI/CD. Ele cria um repositório Git em uma organização nova ou existente do Azure DevOps, depois confirma um aplicativo de exemplo ou seu código existente para um novo repositório Git.  

A automação também estabelece um gatilho de CI para o build, de modo que cada nova confirmação de código inicia um build. O DevOps Projects cria um gatilho de CD e implanta cada novo build bem-sucedido no serviço do Azure de sua escolha.  

Os pipelines de build e de lançamento podem ser personalizados para cenários adicionais. Também é possível clonar os pipelines de build e de lançamento para usar em outros projetos.

Depois de criar seu projeto DevOps, você pode:

* Personalizar seu pipeline de compilação e versão
* Usar solicitações de pull para gerenciar o fluxo de seu código e manter a qualidade alta
* Teste e compile cada confirmação antes de mesclar seu código a fim de elevar o nível de qualidade
* Acompanhar a lista de pendências e os problemas do aplicativo

## <a name="how-do-i-start-using-devops-projects"></a>Como faço para começar a usar o DevOps Projects?

* [Começar a usar o DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-projects-videos"></a>Vídeos do DevOps Projects

* [Criar CI/CD com o Azure DevOps Projects](https://www.youtube.com/watch?v=NuYDAs3kNV8)
