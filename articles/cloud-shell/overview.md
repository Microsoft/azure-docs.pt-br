---
title: Visão Geral Azure Cloud Shell | Microsoft Docs
description: Visão geral do Azure Cloud Shell.
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/20/2020
ms.author: damaerte
ms.openlocfilehash: f824bddf833a1e2c01a3b779abc2c5252d8e0547
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89468650"
---
# <a name="overview-of-azure-cloud-shell"></a>Visão geral do Azure Cloud Shell

O Azure Cloud Shell é um shell interativo, autenticado e acessível pelo navegador para o gerenciamento de recursos do Azure. Ele dá a você a flexibilidade de escolher a experiência de shell que melhor se adequa ao modo como você trabalha, seja com o Bash ou o PowerShell.

Você pode acessar o Cloud Shell de três maneiras:

- **Link direto**: Abra um navegador para [https://shell.azure.com](https://shell.azure.com) .

- **Portal do Azure**: selecione o ícone de Cloud Shell na [portal do Azure](https://portal.azure.com):

    ![Ícone para iniciar o Cloud Shell do portal do Azure](media/overview/portal-launch-icon.png)

- **Trechos de código**: em [docs.Microsoft.com]() e [Microsoft Learn](/learn/), selecione o botão **experimentar** que aparece com CLI do Azure e Azure PowerShell trechos de código:

    ```azurecli-interactive
    az account show
    ```

    ```azurepowershell-interactive
    Get-AzSubscription
    ```

    O botão **experimentar** abre o Cloud shell diretamente junto com a documentação usando bash (para trechos de CLI do Azure) ou o PowerShell (para trechos de Azure PowerShell).

    Para executar o comando, use **Copy** no trecho de código, use **Ctrl** + **Shift** + **v** (Windows/Linux) ou **cmd** + **Shift** + **v** (MacOS) para colar o comando e pressione **Enter**.

## <a name="features"></a>Recursos

### <a name="browser-based-shell-experience"></a>Experiência de shell baseada no navegador

O Cloud Shell permite o acesso a uma experiência de linha de comando baseada em navegador, que foi criada tendo em mente as tarefas de gerenciamento do Azure. Utilize o Cloud Shell para trabalhar de forma independente de um computador local, de uma maneira que somente a nuvem pode proporcionar.

### <a name="choice-of-preferred-shell-experience"></a>Opção de experiência de shell preferida

Os usuários podem escolher entre o bash ou o PowerShell.

1. Selecione **Cloud Shell**.

    ![Ícone de Cloud Shell](media/overview/overview-cloudshell-icon.png)

2. Selecione **bash** ou **PowerShell**.

    ![Escolha o bash ou o PowerShell](media/overview/overview-choices.png)

    Após a primeira inicialização, você pode usar o controle suspenso tipo de Shell para alternar entre o bash e o PowerShell:

    ![Controle suspenso para selecionar bash ou PowerShell](media/overview/select-shell-drop-down.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Estação de trabalho configurada e autenticada do Azure

O Cloud Shell é gerenciado pela Microsoft, portanto vem com ferramentas de linha de comando populares e suporte para idiomas. O Cloud Shell também autentica com segurança automaticamente para acesso instantâneo aos recursos por meio dos cmdlets da CLI do Azure ou Azure PowerShell.

Exiba toda a [lista das ferramentas instaladas no Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Editor de Cloud Shell integrado

O Cloud Shell oferece um editor de texto gráfico integrado com base no Monaco Editor de software livre. Basta criar e editar arquivos de configuração executando `code .` para implantação perfeita por meio da CLI do Azure ou do Azure PowerShell.

[Saiba mais sobre o editor do Cloud Shell](using-cloud-shell-editor.md).

### <a name="multiple-access-points"></a>Pontos de acesso múltiplos

O Cloud Shell é uma ferramenta flexível que pode ser usada a partir de:

* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Documentação da CLI do Azure](/cli/azure)
* [Documentação do Azure PowerShell](/powershell/azure/)
* [Aplicativo móvel do Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Extensão da Conta do Azure para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Conectar seu armazenamento de arquivos do Microsoft Azure

Cloud Shell computadores são temporários, mas os arquivos são persistidos de duas maneiras: por meio de uma imagem de disco e por meio de um compartilhamento de arquivos montado chamado `clouddrive` . Na primeira inicialização, o Cloud Shell solicita a criação de um grupo de recursos, uma conta de armazenamento e um compartilhamento de Arquivos do Azure em seu nome. Essa é uma etapa única e será anexada automaticamente para todas as sessões. Um compartilhamento de arquivo único pode ser mapeado e será usado pelo Bash e pelo PowerShell no Cloud Shell.

Leia mais para saber como montar uma [conta de armazenamento nova ou existente](persisting-shell-storage.md) ou para saber mais sobre os [mecanismos de persistência usados no Cloud Shell](persisting-shell-storage.md#how-cloud-shell-storage-works).

> [!NOTE]
> O Firewall do armazenamento do Azure não tem suporte para contas de armazenamento do Cloud Shell.

## <a name="concepts"></a>Conceitos

* O Cloud Shell é executado em um host temporário fornecido por sessão e por usuário
* O Cloud Shell atinge o tempo limite após 20 minutos sem atividade interativa
* O Cloud Shell exige que um compartilhamento de arquivos do Azure seja montado
* O Cloud Shell usa o mesmo compartilhamento de arquivos para o Bash e o PowerShell
* É atribuído ao Cloud Shell um computador por conta de usuário
* O Cloud Shell persiste o $HOME usando uma imagem de 5 GB mantida no compartilhamento de arquivos
* As permissões são definidas da mesma forma que para um usuário normal do Linux em Bash

Saiba mais sobre os recursos em [Bash no Cloud Shell](features.md) e [PowerShell no Cloud Shell](./features.md).

## <a name="pricing"></a>Preços

O computador que hospeda o Cloud Shell é gratuito, com um pré-requisito de um compartilhamento de Arquivos do Azure montado. Custos de armazenamento regulares se aplicam.

## <a name="next-steps"></a>Próximas etapas

[Início rápido do Bash no Cloud Shell](quickstart.md) <br>
[Início rápido do PowerShell no Azure Cloud Shell](quickstart-powershell.md)