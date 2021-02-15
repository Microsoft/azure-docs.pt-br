---
title: Recursos do Azure Cloud Shell | Microsoft Docs
description: Visão geral dos recursos no Azure Cloud Shell
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
ms.date: 04/26/2019
ms.author: damaerte
ms.openlocfilehash: a052364b06ac1b9b30cef76db10a79e8ed85b9a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89470146"
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
Os arquivos fora de `$HOME` e do estado da máquina não são mantidos entre sessões. Use as práticas recomendadas ao armazenar segredos, como chaves SSH. Serviços como o [Azure Key Vault têm tutoriais para a instalação](../key-vault/general/manage-with-cli2.md#prerequisites).

[Saiba mais sobre como manter arquivos no Cloud Shell](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Unidade do Azure (Azure:)

O PowerShell no Cloud Shell fornece a unidade do Azure ( `Azure:` ). Você pode alternar para a unidade do Azure com `cd Azure:` e de volta para o diretório base com `cd  ~` .
A unidade do Azure permite a fácil detecção e navegação de recursos do Azure, como computação, rede, armazenamento e etc. de forma semelhante à navegação de sistema de arquivos.
Você pode continuar usando os [cmdlets do Azure PowerShell](/powershell/azure) comuns para gerenciar esses recursos, independentemente da unidade em que estiver.
Todas as alterações feitas nos recursos do Azure, diretamente no portal do Azure ou por meio de cmdlets do Azure PowerShell, são refletidas na unidade do Azure.  Você pode executar `dir -Force` para atualizar seus recursos.

![Captura de tela de um Azure Cloud Shell que está sendo inicializado e uma lista de recursos de diretório.](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Gerenciar o Exchange Online

O PowerShell no Cloud Shell contém uma compilação particular do módulo do Exchange Online.  Execute `Connect-EXOPSSession` para obter seus cmdlets do Exchange.

![Captura de tela de um Azure Cloud Shell executando os comandos Connect-EXOPSSession e Get-User.](media/features-powershell/exchangeonline.png)

 Executar `Get-Command -Module tmp_*`
> [!NOTE]
> O nome do módulo deve começar com `tmp_` , se você tiver instalado módulos com o mesmo prefixo, seus cmdlets também serão exibidos. 

![Captura de tela de um Azure Cloud Shell executando o comando Get-Command-Module tmp_ *.](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Ampla integração com ferramentas de software livre

O Cloud Shell inclui autenticação pré-configurada para ferramentas de software livre, como Terraform, Ansible e Chef InSpec. Experimente-o a partir do passo a passo de exemplo.

## <a name="tools"></a>Ferramentas

|Categoria   |Nome   |
|---|---|
|Ferramentas do Linux            |bash<br> zsh<br> sh<br> tmux<br> dig<br>               |
|Ferramentas do Azure            |[CLI do Azure](https://github.com/Azure/azure-cli) e [CLI clássica do Azure](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](../storage/common/storage-use-azcopy-v10.md)<br> [CLI do Azure Functions](https://github.com/Azure/azure-functions-core-tools)<br> [CLI do Service Fabric](../service-fabric/service-fabric-cli.md)<br> [Shipyard de lote](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Editores de texto           |código (editor do Cloud Shell)<br> Vim<br> Nano<br> Emacs    |
|Controle do código-fonte         |Git                    |
|Ferramentas de build            |Make<br> Maven<br> npm<br> pip         |
|Contêineres             |[Computador do Docker](https://github.com/docker/machine)<br> [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [CLI do DC/OS](https://github.com/dcos/dcos-cli)         |
|Bancos de dados              |Cliente do MySQL<br> Cliente do PostgreSql<br> [Utilitário sqlcmd](/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Outros                  |Cliente do iPython<br> [CLI do Cloud Foundry](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)<br> [Puppet](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Pack](https://www.packer.io/)<br> [CLI do Office 365](https://pnp.github.io/office365-cli/)|

## <a name="language-support"></a>Suporte ao idioma

|Linguagem   |Versão   |
|---|---|
|.NET Core  |[3.1.302](https://github.com/dotnet/core/blob/master/release-notes/3.1/3.1.6/3.1.302-download.md)       |
|Go         |1,9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[7.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 e 3.5 (padrão)|

## <a name="next-steps"></a>Próximas etapas
[Bash no guia de início rápido Cloud Shell](quickstart.md) <br>
[PowerShell no guia de início rápido Cloud Shell](quickstart-powershell.md) <br>
[Saiba mais sobre a CLI do Azure](/cli/azure/) <br>
[Saiba mais sobre o Azure PowerShell](/powershell/azure/) <br>