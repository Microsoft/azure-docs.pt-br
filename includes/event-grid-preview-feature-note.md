---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 85a1579e32b4c216f234f77c76316bedeaea77b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565489"
---
Esse recurso está em visualização. Para usá-lo, você deve instalar uma extensão ou módulo de visualização.

### <a name="install-extension-for-azure-cli"></a>Instalar extensão para CLI do Azure

Para Azure CLI, é necessária a [extensão da Grade de Eventos](/cli/azure/azure-cli-extensions-list).

No [CloudShell](/azure/cloud-shell/quickstart):

* Se você instalou a extensão anteriormente, atualize-a `az extension update -n eventgrid`
* Se você não instalou a extensão anteriormente, instale-a `az extension add -n eventgrid`

Para uma instalação local:

1. Desinstale o CLI do Azure localmente.
1. Instale a [versão mais recente](/cli/azure/install-azure-cli) do Azure CLI.
1. Iniciar janela de comando.
1. Desinstalar as versões anteriores da extensão `az extension remove -n eventgrid`
1. Instalar a extensão `az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Instale o módulo para PowerShell

Para PowerShell, é necessário o [módulo AzureRM.EventGrid](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

No [CloudShell](/azure/cloud-shell/quickstart-powershell):

* Instale o módulo `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Para uma instalação local:

1. Abra o console do PowerShell como administrador
1. Instale o módulo `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Se o parâmetro `-AllowPrerelease` não estiver disponível, use as seguintes etapas:

1. Execute o `Install-Module PowerShellGet -Force`
1. Execute o `Update-Module PowerShellGet`
1. Feche o console do PowerShell
1. Reinicie o PowerShell como administrador
1. Instale o módulo `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
