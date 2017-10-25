---
title: "Persistir arquivos no Azure Cloud Shell (versão prévia) | Microsoft Docs"
description: Passo a passo de como o Azure Cloud Shell persiste arquivos.
services: azure
documentationcenter: 
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: damaerte
ms.openlocfilehash: 933af3860bfe087a0b4db7eff53d4b978a1475da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-works"></a>Como funciona o Cloud Shell
O Cloud Shell mantém arquivos usando o seguinte método: 
* Montando o compartilhamento de arquivos especificado como `clouddrive` no diretório `$Home` para que haja interação direta com o compartilhamento de arquivos.

## <a name="list-cloud-drive-file-shares"></a>Listar compartilhamentos de arquivos da unidade de nuvem
O comando `Get-CloudDrive` recupera as informações de compartilhamento de arquivos montadas no momento pela unidade de nuvem no Cloud Shell. <br>
![Executando Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-cloud-drive"></a>Desmontar unidade de nuvem
Você pode desmontar um compartilhamento de arquivos que esteja montado no Cloud Shell a qualquer momento. Se o compartilhamento de arquivos tiver sido removido, será solicitado que você crie e monte um novo compartilhamento de arquivos na sessão seguinte.

O comando `Dismount-CloudDrive` desmonta um compartilhamento de arquivos da conta de armazenamento atual. Desmontar a unidade de nuvem encerra a sessão atual. O usuário precisará criar e montar um novo compartilhamento de arquivos durante a próxima sessão.
![Executando Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!include [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Próximas etapas
[Guia de início rápido para o PowerShell](quickstart-powershell.md) <br>
[Saiba mais sobre o armazenamento de Arquivos do Azure](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Saiba mais sobre marcas de armazenamento](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>