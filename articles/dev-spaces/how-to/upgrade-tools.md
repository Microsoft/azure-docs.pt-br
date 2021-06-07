---
title: Como atualizar as ferramentas do Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
ms.custom: devx-track-azurecli
description: Saiba como atualizar as ferramentas de linha de comando do Azure Dev Spaces, a extensão do Visual Studio Code e a extensão do Visual Studio
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Contêiner do Azure, contêineres
ms.openlocfilehash: f17643e6130abbc9d5da8b484144c95b0e803f33
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199230"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Como atualizar as ferramentas do Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Se houver uma nova versão e você já estiver usando o Azure Dev Spaces, talvez precise atualizar as ferramentas do seu cliente Azure Dev Spaces.

## <a name="update-the-azure-cli"></a>Atualizar a CLI do Azure

Quando você atualiza a CLI do Azure mais recente, também obtém a versão mais recente da extensão da CLI do Dev Spaces.

Não é necessário desinstalar a versão anterior, apenas localize o download apropriado na [CLI do Azure](/cli/azure/install-azure-cli).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Atualizar a extensão da CLI e as ferramentas de linha de comando do Dev Spaces

Execute o comando a seguir:

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Atualizar a extensão do VS Code

Após a instalação, a extensão é atualizada automaticamente. Talvez seja necessário recarregar a extensão para usar os novos recursos. No VS Code, abra o painel **Extensões**, escolha as extensões **Azure Dev Spaces** e escolha **Recarregar**.

## <a name="update-visual-studio"></a>Atualizar o Visual Studio

O Azure Dev Spaces faz parte da carga de trabalho do Desenvolvimento do Azure e está incluído em todas as atualizações do Visual Studio.

## <a name="next-steps"></a>Próximas etapas

Teste as novas ferramentas criando um novo cluster. Experimente as guias de início rápido e tutoriais no [Azure Dev Spaces](../index.yml).
