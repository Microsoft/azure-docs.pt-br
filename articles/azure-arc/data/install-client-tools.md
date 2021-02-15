---
title: Instalar ferramentas de cliente
description: Instale o azdata, o kubectl, o CLI do Azure, o psql, o Azure Data Studio (insiders) e a extensão ARC para Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ecf0f54913f980d879b562eb4aa8063acf6c4772
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320246"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>Instalar as ferramentas de cliente para implantar e gerenciar serviços de dados habilitados para Azure Arc

> [!IMPORTANT]
> Se você estiver atualizando para uma nova versão mensal, certifique-se também de atualizar para a versão mais recente do Azure Data Studio, da [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] ferramenta e das [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] extensões Arc do Azure para Azure Data Studio.

Este documento orienta você pelas etapas para instalar o [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] , Azure Data Studio, CLI do Azure (AZ) e a ferramenta KUBERNETES CLI (kubectl) no computador cliente.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>Ferramentas para criar e gerenciar serviços de dados habilitados para Arc do Azure 

A tabela a seguir lista as ferramentas comuns necessárias para criar e gerenciar os serviços de dados habilitados para Arc do Azure e como instalar essas ferramentas:

| Ferramenta | Obrigatório | Descrição | Instalação |
|---|---|---|---|
| [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] | Sim | Ferramenta de linha de comando para instalar e gerenciar um cluster de Big Data. [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] também inclui um utilitário de linha de comando para se conectar e consultar instâncias do Azure SQL e SQL Server e servidores postgres usando os comandos `azdata sql query` (executar uma única consulta na linha de comando), `azdata sql shell` (um shell interativo) `azdata postgres query` e `azdata postgres shell` . | [Instalar](/sql/azdata/install/deploy-install-azdata?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json) |
| Azure Data Studio | Sim | Ferramenta de experiência avançada para conectar e consultar uma variedade de bancos de dados, incluindo Azure SQL, SQL Server, PostrgreSQL e MySQL. As extensões para Azure Data Studio fornecem uma experiência de administração para os serviços de dados habilitados para o Azure Arc. | [Instalar](/sql/azure-data-studio/download-azure-data-studio) |
| [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] extensão para Azure Data Studio | Sim | Extensão para Azure Data Studio que será instalado [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] se você ainda não o tiver.| Instale a partir da Galeria de extensões no Azure Data Studio.|
| Extensão de arco do Azure para Azure Data Studio | Sim | Extensão para Azure Data Studio que fornece uma experiência de gerenciamento para serviços de dados habilitados para o Azure Arc. Há uma dependência na [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] extensão para Azure Data Studio. | Instale a partir da Galeria de extensões no Azure Data Studio.|
| Extensão PostgreSQL no Azure Data Studio | Não | A extensão PostgreSQL para Azure Data Studio que fornece recursos de gerenciamento para PostgreSQL. | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> Instale a partir da Galeria de extensões no Azure Data Studio.|
| CLI do Azure (AZ)<sup>1</sup> | Sim | Interface de linha de comando moderna para gerenciar serviços do Azure. Usado com implantações AKS e para carregar dados de inventário e cobrança dos serviços de dados habilitados para o Azure Arc no Azure. ([Mais informações](/cli/azure/?view=azure-cli-latest&preserve-view=true)). | [Instalar](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) |
| Kubernetes CLI (kubectl)<sup>2</sup> | Sim | Ferramenta de linha de comando para gerenciar o cluster kubernetes ([mais informações](https://kubernetes.io/docs/tasks/tools/install-kubectl/)). | [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| ondulação <sup>3</sup> | Necessário para alguns scripts de exemplo. | Ferramenta de linha de comando para transferência de dados com URLs. | [Windows](https://curl.haxx.se/windows/) \| Linux: instalar pacote do curl |
| OC | Necessário para as implantações do Red Hat OpenShift e do Red Hat OpenShift no Azure. |`oc` é a CLI (interface de linha de comando) do OpenShift. | [Como instalar a CLI](https://docs.openshift.com/container-platform/4.4/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli)



<sup>1</sup> você deve estar usando CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para localizar a versão, se necessário.

<sup>2</sup> você deve usar a `kubectl` versão 1,13 ou posterior. Além disso, a versão do `kubectl` deve estar acima ou abaixo de uma versão secundária do cluster do Kubernetes. Caso deseje instalar uma versão específica no cliente do `kubectl`, confira [Instalar o binário do `kubectl` por meio da ondulação](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl) (no Windows 10, use cmd.exe e não o Windows PowerShell para executar a ondulação).

<sup>3</sup> se você estiver usando o PowerShell, a ondulação será um alias para o cmdlet Invoke-WebRequest.

## <a name="next-steps"></a>Próximas etapas

[Criar o controlador de dados de arco do Azure](create-data-controller.md)