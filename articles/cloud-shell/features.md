---
title: Recursos do Azure Cloud Shell | Microsoft Docs
description: Visão geral dos recursos do Bash no Azure Cloud Shell
services: Azure
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
ms.date: 04/10/2019
ms.author: damaerte
ms.openlocfilehash: 46c9350dd2a33d0d25fe193b2ae50c954f2d1f95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614350"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Recursos e ferramentas do Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

O Azure Cloud Shell é executado no `Ubuntu 16.04 LTS`.

## <a name="features"></a>Recursos

### <a name="secure-automatic-authentication"></a>Autenticação automática segura

O Cloud Shell protege e autentica automaticamente o acesso a contas para a CLI do Azure e o Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>Persistência de $HOME entre as sessões

Para persistir arquivos entre as sessões, o Cloud Shell orienta você pela anexação de um compartilhamento de arquivos do Azure na primeira inicialização.
Após a conclusão, o Cloud Shell anexará automaticamente o armazenamento (montado como `$HOME\clouddrive`) para todas as sessões futuras.
Além disso, seu diretório `$HOME` é mantido como .img em seu compartilhamento de arquivos do Azure.
Os arquivos fora de `$HOME` e do estado da máquina não são mantidos entre sessões. Use as práticas recomendadas ao armazenar segredos, como chaves SSH. Serviços como o [Azure Key Vault têm tutoriais para a instalação](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Saiba mais sobre como manter arquivos no Cloud Shell](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Unidade do Azure (Azure:)

O PowerShell no Cloud Shell inicia-se na unidade do Azure (`Azure:`).
A unidade do Azure permite a fácil detecção e navegação de recursos do Azure, como computação, rede, armazenamento e etc. de forma semelhante à navegação de sistema de arquivos.
Você pode continuar usando os [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure) comuns para gerenciar esses recursos, independentemente da unidade em que estiver.
Todas as alterações feitas nos recursos do Azure, diretamente no portal do Azure ou por meio de cmdlets do Azure PowerShell, são refletidas na unidade do Azure.  Você pode executar `dir -Force` para atualizar seus recursos.

![](media/features-powershell/azure-drive.png)

### <a name="deep-integration-with-open-source-tooling"></a>Ampla integração com ferramentas de software livre

O Cloud Shell inclui autenticação pré-configurada para ferramentas de software livre, como Terraform, Ansible e Chef InSpec. Experimente-o a partir do passo a passo de exemplo.

## <a name="tools"></a>Ferramentas

|Category   |NOME   |
|---|---|
|Ferramentas do Linux            |bash<br> zsh<br> sh<br> tmux<br> dig<br>               |
|Ferramentas do Azure            |[CLI do Azure](https://github.com/Azure/azure-cli) e [CLI clássica do Azure](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#writing-your-first-azcopy-command)<br> [CLI do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Shipyard de lote](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Editores de texto           |código (editor do Cloud Shell)<br> vim<br> nano<br> emacs    |
|Controle do código-fonte         |git                    |
|Ferramentas de build            |make<br> maven<br> npm<br> pip         |
|Contêineres             |[Máquina do Docker](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [CLI DO DC/OS](https://github.com/dcos/dcos-cli)         |
|Bancos de dados              |Cliente do MySQL<br> Cliente do PostgreSql<br> [Utilitário SQLCMD](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Outros                  |Cliente do iPython<br> [CLI do Cloud Foundry](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)|

## <a name="language-support"></a>Suporte ao idioma

|Linguagem   |Version   |
|---|---|
|.NET Core  |2.0.0       |
|Go         |1.9        |
|Java       |1.8        |
|Node.js    |8.9.4      |
|PowerShell |[6.2.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 e 3.5 (padrão)|

## <a name="next-steps"></a>Próximas etapas
[Início rápido do Bash no Cloud Shell](quickstart.md) <br>
[Início Rápido do PowerShell no Cloud Shell](quickstart-powershell.md) <br>
[Saiba mais sobre a CLI do Azure](https://docs.microsoft.com/cli/azure/) <br>
[Saiba mais sobre o Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>
