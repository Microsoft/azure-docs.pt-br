---
title: "Visão geral do Jenkins e do Azure | Microsoft Docs"
description: "Hospede o servidor de automação de build e implantação do Jenkins no Azure e use os recursos de computação e armazenamento do Azure para estender os pipelines de CI/CD (integração e implantação contínuas)."
services: jenkins
author: rloutlaw
manager: justhe
ms.service: jenkins
ms.devlang: NA
ms.topic: article
ms.workload: na
ms.date: 08/22/2017
ms.author: routlaw
ms.custom: mvc
ms.openlocfilehash: daa202ddf0dc934c491ead3951ddc4fdc3dd819c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-and-jenkins"></a>Azure e Jenkins

O [Jenkins](https://jenkins.io/) é um servidor de automação de software livre popular usado para configurar a integração e entrega contínuas (CI/CD) em projetos de software. Você pode hospedar a implantação do Jenkins no Azure ou estender a configuração existente do Jenkins usando os recursos do Azure. Os plug-ins do Jenkins também estão disponíveis para simplificar a CI/CD dos aplicativos para o Azure.

Este artigo é uma introdução ao uso do Azure com o Jenkins, fornecendo detalhes dos principais recursos do Azure disponíveis para os usuários do Jenkins. Para começar a usar seu próprio servidor do Jenkins no Azure, consulte nosso [guia de início rápido](install-jenkins-solution-template.md).

## <a name="host-your-jenkins-servers-in-azure"></a>Hospedar os servidores do Jenkins no Azure

Hospede o Jenkins no Azure para centralizar a automação de build e dimensionar a implantação, conforme as necessidades de seus projetos de software aumentam. Implante o Jenkins no Azure usando:
 
- [O modelo de solução do Jenkins](install-jenkins-solution-template.md) no Azure Marketplace.
- [Máquinas virtuais do Azure](/azure/virtual-machines/linux/overview). Consulte nosso [tutorial](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd) para criar uma instância do Jenkins em uma VM.
- Em um cluster Kubernetes em execução no [Serviço de Contêiner do Azure](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough), consulte nossas [instruções](/azure/container-service/kubernetes/container-service-kubernetes-jenkin).

Monitore e gerencie sua implantação do Jenkins no Azure usando o [Log Analytics](/azure/log-analytics/log-analytics-overview), o [Operations Management Suite](/azure/operations-management-suite/operations-management-suite-overview) e a [CLI do Azure] (/cli/azure/overview).

## <a name="scale-your-build-automation-on-demand"></a>Dimensionar a automação de build sob demanda

Adicione agentes de build à implantação existente do Jenkins para dimensionar a capacidade de build do Jenkins conforme o número de builds e a complexidade dos trabalhos e pipelines aumentam. Execute esses agentes de build em máquinas virtuais do Azure usando o [plug-in de Agentes de VM do Azure](jenkins-azure-vm-agents.md). Consulte nosso [tutorial](/azure/jenkins/jenkins-azure-vm-agents) para obter mais detalhes.

Depois de configurados com uma [entidade de serviço do Azure](/azure/azure-resource-manager/resource-group-overview), os trabalhos e pipelines do Jenkins podem usar essa credencial para:

- Armazenar e arquivar com segurança artefatos de build no [Armazenamento do Azure](/azure/storage/common/storage-introduction) usando o [plug-in do Armazenamento do Azure](https://plugins.jenkins.io/windows-azure-storage). Examine as [instruções de armazenamento do Jenkins](/azure/storage/common/storage-java-jenkins-continuous-integration-solution) para saber mais.
- Gerenciar e configurar os recursos do Azure com a [CLI do Azure](/azure/jenkins/execute-cli-jenkins-pipeline).

## <a name="deploy-your-code-into-azure-services"></a>Implantar o código nos serviços do Azure

Use os plug-ins do Jenkins para implantar seus aplicativos no Azure como parte dos pipelines de CI/CD do Jenkins. A implantação no [Serviço de Aplicativo do Azure](/azure/app-service/) e no [Serviço de Contêiner do Azure](/azure/container-service/kubernetes/) permite preparar, testar e liberar atualizações para os aplicativos sem gerenciar a infraestrutura subjacente.

 Plug-ins estão disponíveis para serem implantados nos seguintes serviços e ambientes:

- [Aplicativo Web do Azure no Linux](/azure/app-service/containers/app-service-linux-intro). Consulte o [tutorial](java-deploy-webapp-tutorial.md) para começar.
- [Aplicativo Web do Azure](/azure/app-service/app-service-web-overview). Consulte as [instruções](deploy-Jenkins-app-service-plugin.md) para começar.

