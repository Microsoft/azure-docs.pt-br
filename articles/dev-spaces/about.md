---
title: Introdução ao Azure Dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 05/07/2019
ms.topic: overview
description: Introdução ao Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Serviço do Kubernetes do Azure, contêineres, kubectl, k8s
manager: gwallace
ms.openlocfilehash: c96115f2eba6f8c8a028251d146ca3f766eba044
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305464"
---
# <a name="azure-dev-spaces"></a>Espaços de Desenvolvimento do Azure

O Azure Dev Spaces é uma experiência de desenvolvimento rápida e iterativa do Kubernetes para equipes em clusters do AKS (Serviço de Kubernetes do Azure). Você pode colaborar com sua equipe em um cluster do AKS compartilhado. O Azure Dev Spaces também permite testar todos os componentes do aplicativo no AKS sem replicar ou simular dependências. Você pode executar iterativamente e depurar contêineres diretamente no AKS com a configuração mínima do computador de desenvolvimento.

![](media/azure-dev-spaces/collaborate-graphic.gif)


## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Como Azure Dev Spaces simplifica o desenvolvimento de Kubernetes

O Azure Dev Spaces ajuda as equipes a se concentrarem no desenvolvimento e na rápida iteração de seu aplicativo de microsserviço permitindo que trabalhem diretamente com toda a arquitetura ou aplicativo de microsserviço em execução no AKS. O Azure Dev Spaces também fornece uma maneira independente de atualizar partes da arquitetura de seus microsserviços de forma isolada, sem afetar o restante do cluster do AKS ou outros desenvolvedores. O Azure Dev Spaces destina-se ao desenvolvimento e testes em ambientes de desenvolvimento e teste de nível inferior e não se destina a ser executado em clusters do AKS de produção.

Como as equipes podem trabalhar com o aplicativo inteiro e colaborar diretamente no AKS, o Azure Dev Spaces:

* Minimiza a configuração do computador local
* Diminui o tempo de preparação para novos desenvolvedores da equipe
* Aumenta a velocidade da equipe com uma iteração mais rápida
* Reduz o número de ambientes de desenvolvimento e integração redundantes, pois os membros da equipe podem compartilhar um cluster
* Remove a necessidade de replicar ou simular dependências
* Melhora a colaboração entre as equipes de desenvolvimento, bem como as equipes com as quais trabalham, como equipes de DevOps

O Azure Dev Spaces fornece ferramentas para gerar recursos do Docker e do Kubernetes para seus projetos. Esse conjunto de ferramentas permite que você adicione facilmente aplicativos novos e existentes a um espaço de desenvolvimento e a outros clusters do AKS.

Para obter mais informações sobre como funciona o Azure Dev Spaces, confira [Como funciona o Azure Dev Spaces e como ele é configurado][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Configurações e regiões com suporte

O Azure Dev Spaces só é compatível com os clusters do AKS nas regiões **Leste dos EUA**, **Leste dos EUA 2**, **EUA Central**, **Centro-Sul dos EUA**, **Oeste dos EUA 2**, **Europa Setentrional**, **Oeste da Europa**, **Sul do Reino Unido**, **Sudeste Asiático**, **Leste da Austrália**, **Canadá Central** e **Leste do Canadá**. O Azure Dev Spaces dá suporte ao uso da [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) ou do [Visual Studio Code](https://code.visualstudio.com/download) com a [extensão do Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) instalada no Linux, no MacOS ou no Windows 8 ou posterior para criar e executar aplicativos no AKS. Ele também dá suporte à utilização do [Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) instalado no Windows 8 ou posterior. Para o Visual Studio 2019, você precisará da carga de trabalho de Desenvolvimento do Azure. No caso do Visual Studio 2017, você precisará da carga de trabalho de Desenvolvimento da Web e das [Ferramentas do Visual Studio para Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Próximas etapas

Confira o início rápido de Desenvolvimento de Equipe para saber mais sobre o desenvolvimento rápido e iterativo de equipes com o Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Início rápido de Desenvolvimento de Equipe](quickstart-team-development.md)


[how-dev-spaces-works]: how-dev-spaces-works.md
[team-development-quickstart]: quickstart-team-development.md
