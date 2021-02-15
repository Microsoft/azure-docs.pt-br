---
title: incluir arquivo
description: incluir arquivo
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 644669ea27938e385e11f3b1911a23ab30829a95
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026089"
---
Esse recurso está em visualização. Para usá-lo, você deve instalar uma extensão ou módulo de visualização.

### <a name="install-extension-for-azure-cli"></a>Instalar extensão para CLI do Azure

Para Azure CLI, é necessária a [extensão da Grade de Eventos](/cli/azure/azure-cli-extensions-list).

No [CloudShell](../articles/cloud-shell/quickstart.md):

* Se você instalou a extensão anteriormente, atualize-a `az extension update -n eventgrid`
* Se você não instalou a extensão anteriormente, instale-a `az extension add -n eventgrid`

Para uma instalação local:

1. [Instale a CLI do Azure](/cli/azure/install-azure-cli). Certifique-se de você tem a versão mais recente, verificando com o `az --version`.
1. Desinstalar as versões anteriores da extensão `az extension remove -n eventgrid`
1. Instale a extensão `eventgrid` com o `az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Instale o módulo para PowerShell

Para PowerShell, é necessário o [módulo AzureRM.EventGrid](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

No [CloudShell](../articles/cloud-shell/quickstart-powershell.md):

* Instale o módulo `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Para uma instalação local:

1. Abra o console do PowerShell como administrador
1. Instale o módulo `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Se o parâmetro `-AllowPrerelease` não estiver disponível, use as seguintes etapas:

1. Execute `Install-Module PowerShellGet -Force`
1. Execute `Update-Module PowerShellGet`
1. Feche o console do PowerShell
1. Reinicie o PowerShell como administrador
1. Instale o módulo `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`