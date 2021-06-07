---
title: Visão geral do Kubernetes habilitado para o Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Este artigo fornece uma visão geral do Kubernetes habilitado para o Azure Arc.
keywords: Kubernetes, Arc, Azure, contêineres
ms.custom: references_regions
ms.openlocfilehash: 69e9886f214d0076c8e66231fd6ad15bb060828f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449640"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>O que é o Kubernetes habilitado para Azure Arc?

Com o Kubernetes habilitado para Azure Arc, você pode anexar e configurar clusters do Kubernetes localizados dentro ou fora do Azure. Quando você conectar um cluster do Kubernetes ao Azure Arc, ele vai:
* Aparecer no portal do Azure com uma ID e uma identidade gerenciada do Azure Resource Manager. 
* Ser colocado em uma assinatura do Azure e em um grupo de recursos.
* Receber marcas assim como qualquer outro recurso do Azure. 

Para conectar um cluster do Kubernetes ao Azure, o administrador do cluster precisa implantar agentes. Esses agentes:
* São executados no namespace do Kubernetes `azure-arc` como implantações padrão do Kubernetes.
* Manipulam a conectividade com o Azure.
* Coletam métricas e logs do Azure Arc.
* Observam as solicitações de configuração. 

O Kubernetes habilitado para o Azure Arc dá suporte ao SSL padrão do setor para proteger dados em trânsito. Além disso, os dados são armazenados criptografados quando em repouso em um banco de dados Azure Cosmos DB para garantir a confidencialidade dos dados.

## <a name="supported-kubernetes-distributions"></a>Distribuições do Kubernetes compatíveis

O Kubernetes habilitado para Azure Arc funciona com qualquer cluster Kubernetes certificado pela CNCF (Cloud Native Computing Foundation). A equipe de Azure Arc trabalhou com os [principais parceiros do setor para validar a conformidade](./validation-program.md) das distribuições do Kubernetes deles com o Kubernetes habilitado para Azure Arc.

## <a name="supported-scenarios"></a>Cenários com suporte 

O Kubernetes habilitado para o Azure Arc dá suporte aos seguintes cenários: 

* Conectar o Kubernetes em execução fora do Azure para inventário, agrupamento e marcação.

* Implantar aplicativos e aplicar a configuração usando o gerenciamento de configuração baseado em GitOps. 

* Exibir e monitorar seus clusters usando o Azure Monitor para contêineres.

* Impor a proteção contra ameaças usando o Azure Defender para Kubernetes.

* Aplicar políticas usando o Azure Policy para Kubernetes.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Regiões com suporte 

Atualmente, há suporte para o Kubernetes habilitado para o Azure Arc nestas regiões: 

* Leste dos EUA
* Europa Ocidental
* Centro-Oeste dos EUA
* Centro-Sul dos Estados Unidos
* Sudeste Asiático
* Sul do Reino Unido
* Oeste dos EUA 2
* Leste da Austrália
* Leste dos EUA 2
* Norte da Europa

## <a name="next-steps"></a>Próximas etapas

Saiba como conectar um cluster ao Azure Arc.
> [!div class="nextstepaction"]
> [Conectar um cluster ao Azure Arc](./quickstart-connect-cluster.md)
