---
title: Como atualizar as ferramentas do Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/03/2018
ms.topic: conceptual
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Contêiner do Azure, contêineres
ms.openlocfilehash: 4e0a3c5aa849799872371ef1c5ac0867babffebb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686400"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Como atualizar as ferramentas do Azure Dev Spaces

Se houver uma nova versão e você já estiver usando o Azure Dev Spaces, talvez precise atualizar as ferramentas do seu cliente Azure Dev Spaces.

## <a name="update-the-azure-cli"></a>Atualizar a CLI do Azure

Quando você atualiza a CLI do Azure mais recente, também obtém a versão mais recente da extensão da CLI do Dev Spaces.

Não é necessário desinstalar a versão anterior, apenas localize o download apropriado na [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Atualizar a extensão da CLI e as ferramentas de linha de comando do Dev Spaces

Execute o comando a seguir:

```cmd
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Atualizar a extensão do VS Code

Após a instalação, a extensão é atualizada automaticamente. Talvez seja necessário recarregar a extensão para usar os novos recursos. No VS Code, abra o painel **Extensões**, escolha as extensões **Azure Dev Spaces** e escolha **Recarregar**.

## <a name="update-the-visual-studio-extension"></a>Atualizar a extensão do Visual Studio

Assim como ocorre com outras extensões e atualizações, o Visual Studio notificará você quando houver uma atualização disponível para o Ferramentas do Visual Studio para Kubernetes, o que inclui o Azure Dev Spaces. Procure um ícone de sinalizador na parte superior direita da tela.

Para atualizar as ferramentas no Visual Studio, escolha o item de menu **Ferramentas > Extensões e Atualizações** e, no lado esquerdo, escolha **Atualizações**. Encontre as **Ferramentas do Visual Studio para Kubernetes** e escolha o botão **Atualizar**.

## <a name="next-steps"></a>Próximas etapas

Teste as novas ferramentas criando um novo cluster. Experimente as guias de início rápido e tutoriais no [Azure Dev Spaces](/azure/dev-spaces).

> [!WARNING]
> O Azure Dev Spaces em clusters existentes não será corrigido imediatamente. Portanto, use a versão mais recente em todas as implantações do Azure, crie um novo cluster após a atualização das ferramentas.
