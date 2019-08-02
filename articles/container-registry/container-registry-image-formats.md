---
title: Formatos de conteúdo do Registro de Contêiner do Azure
description: Saiba mais sobre os formatos de conteúdo com suporte no Registro de Contêiner do Azure.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 04/18/2019
ms.author: danlep
ms.openlocfilehash: c804dd7b73ea4a51c02c8b342a4ac60d992ec7c5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310684"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formatos de conteúdo com suporte no Registro de Contêiner do Azure

Use um repositório privado no Registro de Contêiner do Azure para gerenciar um dos seguintes formatos de conteúdo. 

## <a name="docker-compatible-container-images"></a>Imagens de contêiner compatíveis com Docker

Há suporte para os seguintes formatos de imagem de contêiner do Docker:

* [Manifesto de imagem de Docker V2, Esquema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Manifesto de imagem de Docker V2, Esquema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - inclui Listas de Manifestos que permitem que os registros armazenem imagens multiplataforma sob uma única referência "image:tag"

## <a name="oci-images"></a>Imagens de OCI

O registro de contêiner do Azure também dá suporte a imagens que atendem à [especificação de formato de imagem de OCI (Open container Initiative)](https://github.com/opencontainers/image-spec/blob/master/spec.md). Os formatos de empacotamento incluem o [formato de imagem de singularidade (SIF)](https://www.sylabs.io/2018/03/sif-containing-your-containers/).

## <a name="helm-charts"></a>Gráficos Helm

O registro de contêiner do Azure pode hospedar repositórios para [gráficos Helm](https://helm.sh/), um formato de empacotamento usado para gerenciar e implantar aplicativos para kubernetes rapidamente. Há suporte para [Cliente Helm](https://docs.helm.sh/using_helm/#installing-helm) versão 2.11.0 ou posterior.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [efetuar push e efetuar pull](container-registry-get-started-docker-cli.md) de imagens com Registro de Contêiner do Azure.

* Use [tarefas do ACR](container-registry-tasks-overview.md) para compilar e testar imagens de contêiner. 

* Use o [Moby BuildKit](https://github.com/moby/buildkit) para compilar e empacotar contêineres no formato de OCI.

* Configure um [repositório Helm](container-registry-helm-repos.md) hospedado no Registro de Contêiner do Azure. 


