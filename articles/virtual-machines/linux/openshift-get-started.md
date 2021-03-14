---
title: Visão geral do OpenShift no Azure
description: Uma visão geral de OpenShift no Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: aba01fc2317372438bc0d93a6618d518ab03ed0d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672305"
---
# <a name="openshift-in-azure"></a>OpenShift no Azure

O OpenShift é uma plataforma de aplicativo de contêiner aberta e extensível que traz docker e Kubernetes para a empresa.  

O OpenShift inclui Kubernetes para gerenciamento e orquestração do contêiner. Ele adiciona ferramentas concentradas no desenvolvedor e em operações que permitem:

- Desenvolvimento rápido de aplicativos.
- Implantação e dimensionamento fáceis.
- Manutenção de ciclo de vida de longo prazo para equipes e aplicativos.

Há várias versões do OpenShift disponíveis.  Dessas versões, somente duas estão disponíveis atualmente para que os clientes implantem no Azure: plataforma de contêiner OpenShift e OKD (anteriormente OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Red Hat OpenShift no Azure

Microsoft Azure Red Hat OpenShift é uma oferta totalmente gerenciada do OpenShift em execução no Azure. Esse serviço é gerenciado e suportado pela Microsoft e Red Hat em conjunto. Para obter mais detalhes, consulte a documentação do [serviço do Azure Red Hat OpenShift](../../openshift/index.yml) .

## <a name="openshift-container-platform"></a>Plataforma do Contêiner do OpenShift

Plataforma de contêiner é uma [versão comercial](https://www.openshift.com) pronta para empresa do e suportada pelo Red Hat. Com essa versão, o cliente adquire os direitos necessários para a Plataforma de Contêiner OpenShift e é responsável pela instalação e o gerenciamento de toda a infraestrutura.

Como os próprios clientes "possuem" toda a plataforma, eles podem instalá-lo em seu datacenter local ou em uma nuvem pública (como o Azure).

## <a name="okd"></a>OKD

O OKD é um projeto upstream de [software livre](https://www.okd.io/) do OpenShift que tem o suporte da comunidade. O OKD pode ser instalado em CentOS ou RHEL (Red Hat Enterprise Linux).

## <a name="next-steps"></a>Próximas etapas

- [Configurar pré-requisitos comuns para OpenShift no Azure](./openshift-container-platform-3x-prerequisites.md)
- [Implantar o OpenShift Container Platform no Azure](./openshift-container-platform-3x.md)
- [Implantar a plataforma de contêiner OpenShift Self-Managed oferta do Marketplace](./openshift-container-platform-3x-marketplace-self-managed.md)
- [Implantar OpenShift no Azure Stack](./openshift-azure-stack.md)
- [Tarefas de pós-implantação](./openshift-container-platform-3x-post-deployment.md)
- [Solução de problemas de implantação do OpenShift](./openshift-container-platform-3x-troubleshooting.md)
