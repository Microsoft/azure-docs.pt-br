---
title: Criar ativos técnicos do módulo do aplicativo do Azure | Microsoft Docs
description: Crie os recursos técnicos para uma oferta do aplicativo do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/13/2018
ms.author: pbutlerm
ms.openlocfilehash: a498fb2bc3efcc3a081a0ab854df107135a4e008
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744951"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Preparar seus ativos técnicos do aplicativo do Azure

Este artigo descreve os recursos para preparar os recursos técnicos para sua oferta de aplicativo do Azure.

## <a name="before-you-begin"></a>Antes de começar

Examine o vídeo a seguir, [Construindo Modelos de Solução e Aplicativos Gerenciados para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), uma visão geral sobre como criar um modelo do Azure Resource Manager para definir uma solução de aplicativo do Azure e como publicar a oferta de aplicativo posteriormente no mercado do Azure.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Examine a seguinte documentação do aplicativo do Azure, que fornece guias de início rápido, tutoriais e exemplos.

- [Compreender os modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Inícios Rápidos:

  - [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/)
  - [Modelos de Início Rápido do Azure GitHub](https://github.com/azure/azure-quickstart-templates)
  - [Publicar definição do aplicativo](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Implantar aplicativo do catálogo de serviços](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Tutoriais:

  - [Criar arquivos de definição](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Publicar o aplicativo do Marketplace](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Exemplos:

    - [CLI do Azure](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [PowerShell do Azure](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Soluções de aplicativo gerenciado](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, criar e testar esses recursos leva tempo e exige conhecimento técnico da plataforma do Azure e das tecnologias usadas para criar a oferta.

A equipe de engenharia deverá ter conhecimento das seguintes tecnologias Microsoft:

- Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/)
- Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/)
- Conhecimento prático de [Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/?filter=storage) e [Rede do Azure](https://azure.microsoft.com/services/?filter=networking)
- Conhecimento de prático do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Conhecimento prático de [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos dos seguintes ambientes de script para ajudar a gerenciar seu aplicativo Azure:

- [PowerShell do Azure](https://docs.microsoft.com/powershell/azure/overview)
- [CLI do Azure](https://docs.microsoft.com/cli/azure)

Além disso, é recomendável adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

- [Gerenciador de Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/) (VS Code) com as extensões a seguir:

  - Extensão: [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Também é recomendável que você revise as ferramentas disponíveis na página [Ferramentas para Desenvolvedores do Azure](https://azure.microsoft.com/tools/) e, se estiver usando o Visual Studio, o [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="next-steps"></a>Próximas etapas

[Criar oferta de aplicativo do Azure](./cpp-create-offer.md)

