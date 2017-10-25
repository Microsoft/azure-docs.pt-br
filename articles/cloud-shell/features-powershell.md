---
title: "Recursos do PowerShell no Azure Cloud Shell (versão prévia) | Microsoft Docs"
description: "Visão geral dos recursos do PowerShell no Azure Cloud Shell"
services: Azure
documentationcenter: 
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: damaerte
ms.openlocfilehash: 9eacb57b5a00ff11739695ed3311be0c638ba25f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="features--tools-for-powershell-in-azure-cloud-shell"></a>Recursos e ferramentas para o PowerShell no Azure Cloud Shell

[!include [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> Recursos e ferramentas para o [Bash](features.md) também estão disponíveis.

PowerShell no Cloud Shell é executado em `Windows Server 2016`.

## <a name="features"></a>Recursos

### <a name="secure-automatic-authentication"></a>Autenticação automática segura

O PowerShell no Cloud Shell protege e autentica automaticamente o acesso de conta do Azure PowerShell.

### <a name="files-persistence-across-sessions"></a>Persistência de arquivos entre as sessões

Para persistir arquivos entre as sessões, o Cloud Shell orienta você pela anexação de um compartilhamento de arquivos do Azure na primeira inicialização.
Após a conclusão, o Cloud Shell anexará automaticamente o armazenamento (montado como `$home\clouddrive`) para todas as sessões futuras.
Como cada solicitação ao Cloud Shell aloca um computador temporário, os arquivos fora de `$home\clouddrive` e o estado do computador não persistem entre as sessões.

[Saiba mais sobre como anexar os compartilhamentos de arquivos do Azure ao Cloud Shell.](persisting-shell-storage-powershell.md)

### <a name="azure-drive-azure"></a>Unidade do Azure (Azure:)

O PowerShell no Cloud Shell inicia-se na unidade do Azure (`Azure:`).
A unidade do Azure permite a fácil detecção e navegação de recursos do Azure, como computação, rede, armazenamento e etc. de forma semelhante à navegação de sistema de arquivos.
Você pode continuar a usar os [cmdlets do Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure) comuns para gerenciar esses recursos.
Todas as alterações feitas aos recursos do Azure, diretamente no portal do Azure ou por meio de cmdlets do Azure PowerShell, são refletidas imediatamente na unidade do Azure.

![](media/features-powershell/azure-drive.png)

#### <a name="contextual-awareness"></a>Reconhecimento contextual

- **Escopo do grupo de recursos**: quando no contexto de um caminho do grupo de recursos na unidade do Azure (`Azure:`), o nome do grupo de recursos é automaticamente passado para os cmdlets do Azure PowerShell.

    ![](media/features-powershell/resource-group-autocomplete.png)

- **Get-AzureRmCommand**: esse cmdlet retorna a lista de comandos aplicáveis no contexto do local na unidade do Azure (`Azure:`). Por exemplo, ele mostra somente os comandos relacionados ao armazenamento quando o usuário está em `Azure:\<subscription name>\StorageAccounts`

    ![](media/features-powershell/get-azurermcommand.png)

### <a name="rich-powershell-script-editing"></a>Edição de scripts avançados do PowerShell

Ao usar VIM para editar arquivos do PowerShell (`.ps1,.psm1,.psd1`), você automaticamente obtém realce de sintaxe e suporte a IntelliSense.
O suporte a IntelliSense é implementado por meio de um vim-plugin que interage com uma instância local dos [Serviços do editor do PowerShell](https://github.com/PowerShell/PowerShellEditorServices).

> [!TIP]
> Use `TAB` para preencher (IntelliSense) nomes de comando, nomes de parâmetros e valores de parâmetro (se aplicável).

![](media/features-powershell/powershell-editing-vim.png)

### <a name="extensible-model"></a>Modelo extensível

Usando [PowerShellGet](https://docs.microsoft.com/powershell/module/powershellget), você pode facilmente instalar (e atualizar) scripts e módulos personalizados da [Galeria do PowerShell](https://www.powershellgallery.com).
Após a instalação, os módulos são automaticamente mantidos entre as sessões do Cloud Shell.

> [!TIP]
> Os módulos instalados pelos usuários são salvos na pasta `$Home\CloudDrive\.pscloudshell\WindowsPowerShell`. Um link simbólico para essa pasta é criado na pasta de documentos do usuário (`$home\Documents\WindowsPowerShell`).

![](media/features-powershell/powershellget-module.png)

### <a name="management-of-guest-vms"></a>Gerenciamento de VMs convidadas

Usando dois comandos internos - `Enter-AzureRmVM` e `Invoke-AzureRmVMCommand`, você pode gerenciar suas VMs do Azure remotamente.
Esses comandos são criados em cima de comunicação remota do PowerShell e exigem conectividade do PowerShell com as VMs do Azure.

## <a name="tools"></a>Ferramentas

|**Categoria**    |**Nome**                                 |
|----------------|-----------------------------------------|
|Ferramentas do Azure     |[Azure PowerShell (4.3.1)](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1) |
|Editores de texto    |vim<br> nano                             |
|Gerenciador de Pacotes |PowerShellGet<br> PackageManagement<br> npm<br> pip |
|Controle do código-fonte  |git                                      |
|Bancos de dados       |[Módulo do SqlServer](https://www.powershellgallery.com/packages/SqlServer)<br> [Utilitário SQLCMD](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|Ferramentas de teste      |Pester                                   |

## <a name="language-support"></a>Suporte ao idioma

|**Linguagem**|**Versão**|
|------------|-----------|
|.NET        |4.6        |
|Node.js     |6.10       |
|PowerShell  |5.1 e [6.0 (beta)](https://github.com/PowerShell/powershell/releases)       |
|Python      |2.7        |

## <a name="next-steps"></a>Próximas etapas

[Início rápido com o PowerShell no Cloud Shell](quickstart-powershell.md) <br>
[Saiba mais sobre o Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
