---
title: Visão geral do DevOps Starter para Azure | Microsoft Docs
description: Entenda o valor do DevOps Starter
services: devops-project
documentationcenter: ''
author: mlearned
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/24/2020
ms.author: mlearned
ms.openlocfilehash: 7c36539ef5be503b2cb7e14047e596522ef8e962
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92330641"
---
# <a name="overview-of-devops-starter"></a>Visão geral do DevOps Starter

 Com o DevOps Starter, é mais fácil começar a usar o Azure com o GitHub Actions ou o Azure DevOps. Ele ajuda você a iniciar seu tipo de aplicativo favorito no serviço do Azure de sua escolha em apenas algumas etapas rápidas do portal do Azure. 

 O DevOps Starter configura tudo de que você precisa para desenvolver, implantar e monitorar seu aplicativo. É possível usar o painel do DevOps Starter para monitorar confirmações, builds e implantações de código, tudo em uma só exibição no portal do Azure.

## <a name="advantages-of-using-devops-starter"></a>Vantagens de usar o DevOps Starter

  O DevOps Starter dá suporte aos dois provedores de CI/CD a seguir para automatizar suas implantações
  * [GitHub Actions](https://github.com/features/actions)
  * [Azure DevOps](https://azure.microsoft.com/services/devops)

  O DevOps Starter automatiza a instalação de um pipeline inteiro de CI (integração contínua) e CD (entrega contínua) do seu aplicativo para o Azure.  É possível iniciar com o código existente ou usar um dos aplicativos de exemplo fornecidos. Em seguida, você poderá implantar rapidamente esse aplicativo em vários serviços do Azure, como Máquinas Virtuais, Serviço de Aplicativo, AKS (Serviço de Kubernetes do Azure), Banco de Dados SQL do Azure e Azure Service Fabric.  

  O DevOps Starter faz todo o trabalho de configuração inicial de um pipeline de DevOps, incluindo tudo desde a configuração do repositório inicial do Git, configuração do pipeline de CI/CD, criação de um recurso do Application Insights para monitorar e fornecer uma exibição de toda a solução com a criação de um painel do DevOps Starter no portal do Azure.

É possível usar o DevOps Starter para:

* Implantar rapidamente seu aplicativo no Azure
* Automatizar a configuração de um fluxo de trabalho ou pipeline de CI/CD
* Ver e compreender como configurar corretamente um fluxo de trabalho ou pipeline de CI/CD
* Personalizar ainda mais os pipelines de lançamento com base em seus cenários específicos

## <a name="how-to-use-devops-starter"></a>Como usar o DevOps Starter?

  O DevOps Starter está disponível no portal do Azure. Crie um recurso do DevOps Starter assim como você cria qualquer outro recurso do Azure no portal. O DevOps Projects fornece uma experiência passo a passo do tipo assistente para as várias opções de configuração.  

Você pode escolher várias opções de configuração como parte da instalação inicial. Estas opções incluem:

* Selecionar seu provedor de CI/CD favorito
* Usar o aplicativo de exemplo fornecido ou trazer o próprio código (apenas para Azure DevOps)
* Selecionar uma linguagem de aplicativo
* Escolher uma estrutura de aplicativo com base na linguagem
* Selecionar um serviço do Azure (destino de implantação)
* Selecionar sua organização do GitHub ou Azure DevOps
* Escolher sua assinatura do Azure
* Escolher o local dos serviços do Azure
* Escolher entre vários tipos de preços dos serviços do Azure

Depois de criar seu DevOps Starter, você pode:

* Personalizar seu fluxo de trabalho do GitHub ou o Pipeline do Azure DevOps
* Usar solicitações de pull para gerenciar o fluxo de seu código e manter a qualidade alta
* Teste e compile cada confirmação antes de mesclar seu código a fim de elevar o nível de qualidade

Depois de usar o DevOps Starter, também é possível excluir todos os recursos de um único local no painel do DevOps Starter no portal do Azure.

## <a name="devops-starter-and-github-integration"></a>Integração do DevOps Starter ao GitHub

O DevOps Starter agora dá suporte ao GitHub Actions como um provedor de CI/CD. Ele automatiza todo o trabalho necessário no GitHub para configurar um fluxo de trabalho de CI/CD usando o GitHub Actions. Ele cria um repositório do GitHub em uma organização do GitHub existente e confirma um aplicativo de exemplo para o novo repositório do GitHub.  

A automação também estabelece um gatilho para o fluxo de trabalho; assim, toda nova confirmação inicia um build e implanta o trabalho dentro do fluxo de trabalho. O aplicativo é implantado no serviço do Azure de sua escolha. O fluxo de trabalho do GitHub pode ser personalizado para outros cenários. 

## <a name="devops-starter-and-azure-devops-integration"></a>Integração entre o DevOps Starter e o Azure DevOps

O DevOps Starter usando o Azure DevOps automatiza todo o trabalho necessário no Azure Pipelines para configurar um pipeline de CI/CD. Ele cria um repositório Git em uma organização nova ou existente do Azure DevOps, depois confirma um aplicativo de exemplo ou seu código existente para um novo repositório Git.  

A automação também estabelece um gatilho de CI para o build, de modo que cada nova confirmação de código inicia um build. O DevOps Starter cria um gatilho de CD e implanta cada novo build bem-sucedido no serviço do Azure de sua escolha.  

Os pipelines de build e de lançamento podem ser personalizados para cenários adicionais. Também é possível clonar os pipelines de build e de lançamento para usar em outros projetos.

## <a name="getting-started-with-devops-starter"></a>Introdução ao DevOps Starter

* [Introdução ao DevOps Starter](./azure-devops-project-github.md)

##  <a name="devops-starter-videos"></a>Vídeos sobre o DevOps Starter

* [Criar CI/CD com o Azure DevOps Starter](https://www.youtube.com/watch?v=NuYDAs3kNV8)
