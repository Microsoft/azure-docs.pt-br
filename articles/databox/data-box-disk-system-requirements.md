---
title: Requisitos de sistema Microsoft Azure Data Box Disk | Microsoft Docs
description: Aprender sobre os requisitos de software e de rede para seu Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/22/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 750ed8f65db04199ea284e69693bced65a1dc8d9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703126"
---
::: zone target="docs"

# <a name="azure-data-box-disk-system-requirements"></a>Requisitos do sistema do Azure Data Box Disk

Este artigo descreve os requisitos de sistema importantes para a solução do Microsoft Azure Data Box Disk e para os clientes se conectando ao Data Box Disk. Recomendamos que você examine as informações com atenção antes de implantar o Data Box Disk e consulte-as, quando necessário, durante a implantação e a subsequente operação.

Os requisitos de sistema incluem as plataformas compatíveis com os clientes que se conectam a discos, contas de armazenamento compatíveis e tipos de armazenamento.

::: zone-end

::: zone target="chromeless"

## <a name="review-prerequisites"></a>Examinar os pré-requisitos

1. Você deve ter solicitado o Data Box Disk usando o [Tutorial: Solicitar Azure Data Box Disk](data-box-disk-deploy-ordered.md). Você recebeu seus discos e um cabo conector por disco.
2. Você tem um computador cliente disponível do qual você pode copiar os dados. Computador cliente deve:

    - Executar um sistema operacional com suporte.
    - Ter outro software necessário instalado.

::: zone-end

## <a name="supported-operating-systems-for-clients"></a>Sistemas operacionais compatíveis para clientes

Aqui está uma lista dos sistemas operacionais compatíveis para desbloqueio de disco e a operação de cópia de dados por meio de clientes conectados ao Data Box Disk.

| **Sistema operacional** | **Versões testadas** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows (64 bits) |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Outros softwares necessários para clientes do Windows

Para o cliente do Windows, o seguinte também deve ser instalado.

| **Software**| **Versão** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.1|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Outros softwares necessários para clientes do Linux

Para o cliente do Linux, o conjunto de ferramentas do Data Box Disk instala o software necessário a seguir:

- dislocker
- OpenSSL

## <a name="supported-connection"></a>Conexão com suporte

O computador cliente que contém os dados deve ter uma porta USB 3.0 ou superior. Os discos se conectam a esse cliente usando o cabo fornecido.

## <a name="supported-storage-accounts"></a>Contas de armazenamento com suporte

Aqui está uma lista dos tipos de armazenamento compatíveis com o Data Box Disk.

| **Conta de armazenamento** | **Observações** |
| --- | --- |
| Clássico | Standard |
| Uso Geral  |Standard; tanto V1 quanto V2 são compatíveis. Tanto a camada de acesso frequente quanto a camada de acesso esporádico são compatíveis. |
| Conta de armazenamento de blobs | |

> [!IMPORTANT]
> O suporte ao protocolo NFS (Network File System) 3.0 no Armazenamento de Blobs do Azure não tem suporte no Data Box Disk.

## <a name="supported-storage-types-for-upload"></a>Tipos de armazenamento com suporte para upload

Veja uma lista dos tipos de armazenamento com suporte a serem carregados no Azure usando o Data Box Disk.

| **Formato de arquivo** | **Observações** |
| --- | --- |
| Blob de blocos do Azure | |
| Blob de páginas do Azure  | |
| Arquivos do Azure  | |
| Managed Disks | |

::: zone target="docs"

## <a name="next-step"></a>Próxima etapa

* [Implante seu Azure Data Box Disk](data-box-disk-deploy-ordered.md)

::: zone-end

