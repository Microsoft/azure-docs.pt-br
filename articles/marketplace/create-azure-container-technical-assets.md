---
title: Preparar seus ativos técnicos de contêiner do Azure
description: Recursos técnicos e diretrizes para ajudá-lo a configurar uma oferta de contêiner no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 014bcd6fc519c267cdf17e9e98b850425c25ead6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459326"
---
# <a name="prepare-your-azure-container-technical-assets"></a>Preparar seus ativos técnicos de contêiner do Azure

Este artigo fornece recursos técnicos e recomendações para ajudá-lo a criar uma oferta de contêiner no Azure Marketplace.

## <a name="before-you-begin"></a>Antes de começar

Confira a [documentação das Instâncias de Contêiner do Azure](../container-instances/index.yml) para ver início rápido, tutoriais e amostras.

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, criar e testar esses ativos leva tempo e exige conhecimento técnico da plataforma do Azure e das tecnologias usadas para criar a oferta.

Além do domínio da solução, a equipe de engenharia deve ter conhecimento das seguintes tecnologias Microsoft:

- Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/)
- Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/)
- Conhecimento prático de [Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/?filter=storage) e [Rede do Azure](https://azure.microsoft.com/services/?filter=networking)
- Conhecimento de prático do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Conhecimento prático de [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos dos seguintes ambientes de script para ajudar a gerenciar sua imagem do contêiner:

- [PowerShell do Azure](/powershell/azure/)
- [CLI do Azure](/cli/azure/).

Recomendamos adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

- [Gerenciador de Armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Extensão: [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Analise as ferramentas disponíveis na página [Ferramentas para Desenvolvedores do Azure](https://azure.microsoft.com/). Se você estiver usando o Visual Studio, examine as ferramentas disponíveis no [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Crie a imagem do contêiner

Não é possível implantar uma imagem nas instâncias de contêiner do Azure de um registro local.

- Se você já tiver um contêiner de trabalho em seu registro local, crie um registro do Azure e carregue sua imagem de contêiner para o registro de contêiner do Azure. Para saber mais, consulte [tutorial: criar e implantar imagens de contêiner na nuvem com tarefas de registro de contêiner do Azure](../container-registry/container-registry-tutorial-quick-task.md).

- Se ainda não tiver uma imagem de contêiner e precisar colocar seu aplicativo existente em contêiner ou criar um novo aplicativo baseado em contêiner, clone o código-fonte do aplicativo do GitHub, crie uma imagem de contêiner a partir da origem do aplicativo e teste a imagem em um ambiente do Docker local. Para saber mais, consulte [tutorial: criar uma imagem de contêiner para implantação em instâncias de contêiner do Azure](../container-instances/container-instances-tutorial-prepare-app.md).

## <a name="next-steps"></a>Próximas etapas

- [Criar sua oferta de contêiner](create-azure-container-offer.md)