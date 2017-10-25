---
title: "Utilização da CLI do Azure no Windows | Microsoft Docs"
description: "Utilização da CLI do Azure no Windows"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/14/2017
ms.author: nepeters
ms.openlocfilehash: 0ac4aa10db43fb84119ab97cf78b2d5592cfd277
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-azure-cli-on-windows"></a>Utilização da CLI do Azure no Windows

A Interface de linha de comando (CLI) do Azure fornece um ambiente de script e linha de comando para criar e gerenciar recursos do Azure. A CLI do Azure está disponível para os sistemas operacionais macOS, Linux e Windows. Entre esses sistemas operacionais, os comandos da CLI são idênticos, mas a sintaxe de script específica do sistema operacional pode ser diferente.

Este documento explica em detalhes como a CLI do Azure pode ser instalada e executada no Windows e fornece considerações minuciosas acerca da sintaxe de cada um. Para ler a documentação detalhada da CLI do Azure, confira a [Documentação da CLI do Azure]( https://docs.microsoft.com/en-us/cli/azure/overview).

## <a name="windows-subsystem-for-linux"></a>Subsistema do Windows para Linux

O WSL (subsistema do Windows para Linux) fornece um ambiente Ubuntu Linux na edição de aniversário do Windows 10 e edições posteriores. Quando habilitado, o WSL fornece uma experiência de Bash nativa, que pode ser usada para criar e executar scripts da CLI do Azure. Como o WSL fornece uma experiência de Bash nativa, os scripts da CLI do Azure podem ser compartilhados entre o macOS, Linux e Windows sem modificação.

Siga as seguintes etapas para usar a CLI do Azure em WSL.

|Tarefa | Instruções |
|---|---|
| Habilitar WSL | [Como instalar a documentação de WSL](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide) |
| Instalar a CLI do Azure |[Como instalar a CLI no WSL/Ubuntu 14.04](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2#ubuntu)|

## <a name="powershell"></a>PowerShell

A CLI do Azure pode ser executada nativamente no Windows. Nessa configuração, o pacote da CLI do Azure é instalado no sistema operacional Windows e os comandos podem ser executados a partir do PowerShell. Nessa configuração, os scripts e os comandos da CLI do Azure podem ser executados em qualquer versão com suporte do Windows, porém você precisará da sintaxe de script específica da plataforma. Por isso, scripts não podem ser necessariamente compartilhados entre macOS, Linux e Windows sem modificação.

Siga essas instruções para usar a CLI do Azure no Windows: [Como instalar a CLI no Windows](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2#windows).

## <a name="docker-image"></a>Imagem de Docker

Ao usar o Docker para Windows, uma imagem de Docker, que inclui a CLI do Azure, pode ser iniciada. Essa imagem se baseia no Linux e inclui uma experiência de bash nativa.  Ao usar o Docker para Windows e a imagem da CLI do Azure, os scripts são compartilhados entre o macOS, Linux e Windows. 

Para usar a CLI do Azure no Docker para Windows, confira se o Docker para Windows está em execução e execute o comando a seguir.

```bash
docker run -it azuresdk/azure-cli-python:latest bash
```

Depois de concluído, uma sessão Bash pré-carregada com as ferramentas da CLI do Azure será iniciada.

## <a name="next-steps"></a>Próximas etapas

[Amostra de CLI para máquinas virtuais do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Amostras de CLI para aplicativos Web do Azure](../../app-service/app-service-cli-samples.md)

[Amostras de CLI para Azure SQL](../../sql-database/sql-database-cli-samples.md)
