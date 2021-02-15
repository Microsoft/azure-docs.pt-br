---
title: Limites do Azure Data Box | Microsoft Docs
description: Descreve os limites do sistema e os tamanhos recomendados para as conexões e os componentes do Microsoft Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/05/2021
ms.author: alkohli
ms.openlocfilehash: 97d8da86565db73aa9a3866f39f793aaf0905470
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900154"
---
# <a name="azure-data-box-limits"></a>Limita o Azure Data Box

Considere estes limites ao implantar e operar o Microsoft Azure Data Box. A tabela a seguir descreve esses limites para o Data Box.

## <a name="data-box-service-limits"></a>Limites de serviço do Data Box

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-limits"></a>Limites do Data Box

- Data Box pode armazenar no máximo 500 milhões arquivos para importação e exportação.
- Data Box dá suporte a um máximo de 512 contêineres ou compartilhamentos na nuvem. Os diretórios de nível superior no compartilhamento do usuário se tornam contêineres ou compartilhamentos de arquivos do Azure na nuvem. 
- A capacidade de uso de Data Box pode ser menor que 80 TB devido ao consumo de espaço de metadados ReFS.
- O Data Box dá suporte a um máximo de 10 conexões de cliente por vez em um compartilhamento NFS.

## <a name="azure-storage-limits"></a>Limites de armazenamento do Azure

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>Limitações de upload de dados


### <a name="for-import-order"></a>Para a ordem de importação

Data Box advertências para uma ordem de importação incluem:

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

### <a name="for-export-order"></a>Para a ordem de exportação

Data Box advertências para uma ordem de exportação incluem:

- Data Box é um dispositivo baseado no Windows e não dá suporte a nomes de arquivos que diferenciam maiúsculas de minúsculas. Por exemplo, você pode ter dois arquivos diferentes no Azure com nomes que diferem em maiúsculas e minúsculas. Não use Data Box para exportar esses arquivos, pois os arquivos serão substituídos no dispositivo.
- Se você tiver marcas duplicadas em arquivos de entrada ou marcas fazendo referência aos mesmos dados, a Data Box exportação poderá ignorar ou substituir os arquivos. O número de arquivos e o tamanho dos dados que o portal do Azure exibe podem ser diferentes do tamanho real dos dados no dispositivo. 
- Data Box exporta dados para o sistema baseado no Windows por meio de SMB e é limitado por limitações SMB para arquivos e pastas. Arquivos e pastas com nomes sem suporte não são exportados.
- Há um mapeamento 1:1 do prefixo para o contêiner.
- O tamanho máximo do arquivo é de 1024 caracteres. Os nomes de FileName que excedem esse tamanho não são exportados.
- Os prefixos duplicados no arquivo *XML* (carregados durante a criação do pedido) são exportados. Os prefixos duplicados não são ignorados.
- Os blobs de páginas e os nomes de contêiner diferenciam maiúsculas de minúsculas. Se o uso de maiúsculas e minúsculas for incompatível, o blob e/ou o contêiner não serão encontrados.
 

## <a name="azure-storage-account-size-limits"></a>Limites de tamanho da conta de armazenamento do Azure

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Limites de tamanho do objeto do Azure

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Convenções de nomenclatura de arquivo, blob de páginas e blob de blocos do Azure

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
