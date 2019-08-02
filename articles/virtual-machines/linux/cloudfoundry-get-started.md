---
title: Introdução ao Cloud Foundry no Microsoft Azure | Microsoft Docs
description: Executar OSS ou Pivotal Cloud Foundry no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: gwallace
editor: ''
tags: ''
keywords: ''
ms.assetid: 2a15ffbf-9f86-41e4-b75b-eb44c1a2a7ab
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: e042c9cbce985882b468472425d6803862e82941
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668323"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry no Azure

O Cloud Foundry é um PaaS (plataforma como serviço) de software livre para criação, implantação e operação de aplicativos de 12 fatores desenvolvidos em várias linguagens e estruturas. Este documento descreve as opções disponíveis para a execução do Cloud Foundry no Azure e como começar.

## <a name="cloud-foundry-offerings"></a>Ofertas do Cloud Foundry

Há duas opções do Cloud Foundry disponíveis para execução no Azure: CF OSS (Cloud Foundry de software livre) e PCF (Pivotal Cloud Foundry). O CF OSS é uma versão do Cloud Foundry totalmente de [software livre](https://github.com/cloudfoundry) gerenciada pela Cloud Foundry Foundation. Pivotal Cloud Foundry é uma distribuição corporativa do Cloud Foundry da Pivotal Software Inc. Vamos examinar algumas das diferenças entre as duas ofertas.

### <a name="open-source-cloud-foundry"></a>Cloud Foundry de software livre

Você pode implantar o Cloud Foundry OSS no Azure implantando primeiro um diretor BOSH e, em seguida, implantando o Cloud Foundry usando as [instruções fornecidas no GitHub](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Para saber mais sobre como usar o CF OSS, confira a [documentação](https://docs.cloudfoundry.org/) fornecida pela Cloud Foundry Foundation.

A Microsoft dá suporte aprimorado para o CF OSS usando os seguintes canais de comunidade:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slackhttpsslackcloudfoundryorg"></a>canal bosh-azure-cpi no [Cloud Foundry Slack](https://slack.cloudfoundry.org/)
- [lista de endereçamento cf-bosh](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- Problemas do GitHub para [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) e [service broker](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> O nível de suporte para os recursos do Azure, como as máquinas virtuais em que você executa o Cloud Foundry, baseia-se no seu contrato de suporte do Azure. O suporte aprimorado da comunidade só se aplica aos componentes específicos do Cloud Foundry.

### <a name="pivotal-cloud-foundry"></a>Pivotal Cloud Foundry

O Pivotal Cloud Foundry inclui a mesma plataforma principal que a distribuição de OSS, juntamente com um conjunto de ferramentas de gerenciamento exclusivas e suporte corporativo. Para executar o PCF no Azure, você deve adquirir uma licença da Pivotal. A oferta de PCF do Azure Marketplace inclui uma licença de avaliação de 90 dias.

As ferramentas incluem o [Pivotal Operations Manager](https://docs.pivotal.io/pivotalcf/customizing/), um aplicativo Web que simplifica a implantação e o gerenciamento de uma fundação Cloud Foundry, e o [Pivotal Apps Manager](https://docs.pivotal.io/pivotalcf/console/), um aplicativo Web para gerenciar usuários e aplicativos.

Além dos canais de suporte listados acima para o CF OSS, uma licença do PCF permite que você entre em contato com a Pivotal para obter suporte. A Microsoft e a Pivotal também têm fluxos de trabalho de suporte habilitados que permitem que você entre em contato com uma das partes para obter assistência e ter sua consulta direcionada corretamente dependendo de onde está o problema.

## <a name="azure-service-broker"></a>Azure Service Broker

O Cloud Foundry incentiva a metodologia do ["aplicativo de 12 fatores"](https://12factor.net/), que promove uma separação clara entre processos de aplicativos sem estado e serviços de apoio com estado. Os [service brokers](https://docs.cloudfoundry.org/services/api.html) possibilitam provisionar e associar os serviços de suporte a aplicativos de uma maneira consistente. O [Azure Service Broker](https://github.com/Azure/meta-azure-service-broker) fornece alguns dos principais serviços do Azure por este canal, incluindo o armazenamento do Azure e SQL do Azure.

Se você estiver usando Pivotal Cloud Foundry, o service broker também está [disponível como um mosaico](https://docs.pivotal.io/azure-sb/installing.html) da Pivotal Network.

## <a name="related-resources"></a>Recursos relacionados

### <a name="azure-devops-services-plugin"></a>Plug-in do Azure DevOps Services

O Cloud Foundry serve para o desenvolvimento rápido de software, incluindo o uso de CI (integração contínua) e de CD (entrega contínua). Se você usar o Azure DevOps Services para gerenciar seus projetos e quiser configurar um pipeline de CI/CD direcionado ao Cloud Foundry, use a [extensão de build do Cloud Foundry do Azure DevOps Services](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). O plug-in simplifica a configuração e a automatização de implantações no Cloud Foundry, quer esteja executando no Azure ou em outro ambiente.

## <a name="next-steps"></a>Próximas etapas

- [Implantar o Pivotal Cloud Foundry do Microsoft Azure Marketplace](https://azure.microsoft.com/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Implantar um aplicativo no Cloud Foundry no Azure](./cloudfoundry-deploy-your-first-app.md)
