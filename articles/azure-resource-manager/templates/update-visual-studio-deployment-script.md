---
title: Atualizar o script de implantação de modelo do Visual Studio para usar AZ PowerShell
description: Atualizar o script de implantação de modelo do Visual Studio de AzureRM para AZ PowerShell
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76963862"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>Atualizar o script de implantação de modelo do Visual Studio para usar AZ PowerShell Module

O Visual Studio 16,4 dá suporte ao uso do módulo AZ PowerShell no script de implantação de modelo. No entanto, o Visual Studio não instala automaticamente esse módulo. Para usar o módulo AZ, você precisa executar quatro etapas:

1. [Desinstalar o módulo AzureRM](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [Instalar o módulo AZ](/powershell/azure/install-az-ps)
1. Atualizar o Visual Studio para 16.4
1. Atualizar o script de implantação em seu projeto.

## <a name="update-visual-studio-to-164"></a>Atualizar o Visual Studio para 16.4

Atualize a instalação do Visual Studio para a versão 16,4 ou posterior. Durante a atualização, verifique se o componente Azure PowerShell não está marcado. Como você instalou o módulo AZ por meio da galeria, não convém reinstalar o módulo AzureRM.

Se você já atualizou para o 16,4 e o componente Azure PowerShell foi marcado, você pode desinstalá-lo executando o Instalador do Visual Studio. Não selecione o componente Azure PowerShell na carga de trabalho do Azure ou na página componentes individuais.

## <a name="update-the-deployment-script-in-your-project"></a>Atualizar o script de implantação em seu projeto

Substitua todas as ocorrências da cadeia de caracteres “AzureRM” por “Az” em seu script de implantação. É possível consultar as revisões neste [gist](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) para ver as alterações. Para obter mais informações sobre como atualizar scripts para o módulo AZ, consulte [migrar Azure PowerShell de AzureRM para AZ](/powershell/azure/migrate-from-azurerm-to-az).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o projeto do Visual Studio, consulte [criando e Implantando projetos do grupo de recursos do Azure por meio do Visual Studio](create-visual-studio-deployment-project.md).
