---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: 80e34c117c02c2126b46297c4219effa85e9caa6
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792276"
---
- Não copie arquivos diretamente para nenhum dos compartilhamentos precriados. Você precisa criar uma pasta sob o compartilhamento e, em seguida, copiar os arquivos nessa pasta.
- Uma pasta no *StorageAccount_BlockBlob* e no *StorageAccount_PageBlob* é um contêiner. Por exemplo, os contêineres são criados como *StorageAccount_BlockBlob/container* e *StorageAccount_PageBlob/container*.
- Cada pasta criada diretamente em *StorageAccount_AzureFiles* é convertida em um compartilhamento de arquivos do Azure.
- Se um objeto que está sendo copiado tiver o mesmo nome que um objeto do Azure, como um BLOB ou um arquivo, que já está na nuvem, Data Box substituirá o arquivo na nuvem.
- Todos os arquivos gravados nos compartilhamentos *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* são carregados como um blob de blocos e um blob de páginas, respectivamente.
- O armazenamento de BLOBs do Azure não dá suporte a diretórios. Se você criar uma pasta na pasta *StorageAccount_BlockBlob*, as pastas virtuais serão criadas no nome do blob. Para os Arquivos do Azure, a estrutura de diretório real é mantida.
- Qualquer hierarquia de diretório vazia (sem nenhum arquivo) criada em pastas *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* não é carregada.
- Se há erros ao carregar dados no Azure, um log de erros é criado na conta de armazenamento de destino. O caminho para esse log de erros está disponível quando o upload é concluído e você pode examinar o log para executar uma ação corretiva. Não exclua dados da fonte sem verificar os dados carregados.
- Os metadados de arquivo e as permissões NTFS podem ser preservados quando os dados são carregados nos arquivos do Azure usando as diretrizes em [preservar ACLs de arquivo, atributos e carimbos de data/hora com Azure data Box](../articles/databox/data-box-file-acls-preservation.md).
