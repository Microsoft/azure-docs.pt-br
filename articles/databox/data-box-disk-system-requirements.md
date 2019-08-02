---
title: Requisitos de sistema Microsoft Azure Data Box Disk | Microsoft Docs
description: Aprender sobre os requisitos de software e de rede para seu Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/22/2019
ms.author: alkohli
ms.openlocfilehash: 10ddf4c42ac5aa2d1cffd7cbca6930896103d2c5
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147772"
---
# <a name="azure-data-box-disk-system-requirements"></a>Requisitos do sistema do Azure Data Box Disk

Este artigo descreve os requisitos de sistema importantes para a solução do Microsoft Azure Data Box Disk e para os clientes se conectando ao Data Box Disk. Recomendamos que você examine as informações com atenção antes de implantar o Data Box Disk e consulte-as, quando necessário, durante a implantação e a subsequente operação.

Os requisitos de sistema incluem as plataformas compatíveis com os clientes que se conectam a discos, contas de armazenamento compatíveis e tipos de armazenamento.


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
| Windows Management Framework |5.0|
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
| Uso geral  |Standard; tanto V1 quanto V2 são compatíveis. Tanto a camada de acesso frequente quanto a camada de acesso esporádico são compatíveis. |
| Conta de armazenamento de blobs | |

>[!NOTE]
> Não há suporte para contas do Azure Data Lake Storage Gen2.


## <a name="supported-storage-types-for-upload"></a>Tipos de armazenamento com suporte para upload

Aqui está uma lista dos tipos de armazenamento com suporte para carregado no Azure usando o disco da caixa de dados.

| **Formato de arquivo** | **Observações** |
| --- | --- |
| Blob de blocos do Azure | |
| Blob de páginas do Azure  | |
| Arquivos do Azure  | |
| Managed Disks | |


## <a name="next-step"></a>Próxima etapa

* [Implante seu Azure Data Box Disk](data-box-disk-deploy-ordered.md)

