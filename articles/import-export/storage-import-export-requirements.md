---
title: Requisitos do serviço de Importação/Exportação do Azure | Microsoft Docs
description: Compreenda os requisitos de hardware e software para o serviço de Importação/Exportação do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 99521a746a16a2d0de310fc6cfb2d2272e7b2593
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98706184"
---
# <a name="azure-importexport-system-requirements"></a>Requisitos do sistema para Importação/Exportação do Azure

Este artigo descreve os requisitos importantes para o serviço de Importação/Exportação do Azure. Recomendamos que você leia as informações com atenção antes de usar o serviço de Importação/Exportação, e consulte-as quando precisar durante a operação.

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

Para preparar os discos rígidos usando a ferramenta WAImportExport, os seguintes **sistemas operacionais de 64 bits dão suporte à Criptografia de Unidade de Disco BitLocker**.


|Plataforma |Versão |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Outro software necessário para clientes do Windows

|Plataforma |Versão |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Contas de armazenamento com suporte

O serviço de importação/exportação do Azure é compatível com os seguintes tipos de contas de armazenamento:

- Contas de armazenamento Standard Uso Geral v2 (recomendado para a maioria dos cenários)
- Contas de Armazenamento de Blobs
- Contas de armazenamento v1 de Uso Geral (implantações clássicas ou do Azure Resource Manager)

Para saber mais sobre as contas de armazenamento, confira [Azure storage accounts overview](../storage/common/storage-account-overview.md)(Visão geral de contas de armazenamento do Azure).

Cada trabalho pode ser usado para transferir dados para apenas uma conta de armazenamento, ou por meio dela. Em outras palavras, um único trabalho de importação/exportação não pode estender-se por várias contas de armazenamento. Para obter informações sobre como criar uma nova conta de armazenamento, consulte [Como criar uma conta de armazenamento](../storage/common/storage-account-create.md).

> [!IMPORTANT]
> Para contas de armazenamento em que o recurso de [pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) foi habilitado, use a configuração **permitir serviços confiáveis da Microsoft...** para habilitar o serviço de [importação/exportação](../storage/common/storage-network-security.md) para executar importação/exportação de dados de/para o Azure.

## <a name="supported-storage-types"></a>Tipos de armazenamento com suporte

Os tipos de armazenamento na lista a seguir têm suporte com o serviço de Importação/Exportação do Azure.


|Trabalho  |Serviço de Armazenamento |Com suporte  |Sem suporte  |
|---------|---------|---------|---------|
|Importar     |  Armazenamento de Blobs do Azure <br><br> Armazenamento de arquivos do Azure       | Blobs de página e Blobs de bloco com suporte <br><br> Arquivos com suporte          |
|Exportação     |   Armazenamento de Blobs do Azure       | Blobs de blocos, blobs de páginas e blobs de acréscimo com suporte         | Arquivos do Azure sem suporte


## <a name="supported-hardware"></a>Hardware com suporte

Para o serviço de Importação/Exportação do Azure, você precisa de discos com suporte para copiar dados.

### <a name="supported-disks"></a>Discos com suporte

Os discos na lista a seguir têm suporte para uso com o serviço de Importação/Exportação.


|Tipo de disco  |Tamanho  |Com suporte |
|---------|---------|---------|
|SSD    |   2,5"      |SATA III          |
|HDD     |  2,5"<br>3,5"       |SATA II, SATA III         |

Não há suporte para os seguintes tipos de disco:

- USBs.
- HDD externo com adaptador USB interno.
- Discos que estão dentro dos compartimentos de uma HDD externa.

Um trabalho de importação/exportação único pode ter:

- No máximo 10 HDD/SSDs.
- Uma combinação de HDD/SSD de qualquer tamanho.

Um grande número de unidades pode ser distribuído em vários trabalhos e não há nenhum limite no número de trabalhos que podem ser criados. Para trabalhos de importação, somente o primeiro volume de dados na unidade é processado. O volume de dados deve ser formatado com NTFS.

Ao preparar discos rígidos e copiar os dados usando a ferramenta WAImportExport, você pode usar os adaptadores USB externos a seguir. A maioria dos adaptadores disponíveis no mercado USB 3.0 ou posteriores deve funcionar.

## <a name="next-steps"></a>Próximas etapas

* [Transferir dados com o utilitário de linha de comando AzCopy](../storage/common/storage-use-azcopy-v10.md)