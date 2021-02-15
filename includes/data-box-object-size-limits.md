---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 9805f53d5901226fc9e32b24a323256cd1da6844
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88655123"
---
Aqui estão os tamanhos dos objetos do Azure que podem ser gravados. Verifique se todos os arquivos que são carregados estão em conformidade com esses limites.

| Tipo de objeto do Azure | Limite padrão                                             |
|-------------------|-----------------------------------------------------------|
| Blob de blocos        | Aproximadamente 4,75 TiB                                                 |
| Blob de páginas         | 8 TiB <br> Todos os arquivos carregados no formato de blob de páginas precisarão ser alinhados com 512 bytes (um integral múltiplo), caso contrário o upload falhará. <br> O VHD e o VHDX são alinhados a 512 bytes. |
| Arquivos do Azure        | 1 TiB                                                      |
| Discos gerenciados     | 4 TiB <br> Para obter mais informações sobre tamanho e limites, consulte: <li>[Metas de escalabilidade do SSDs padrão](../articles/virtual-machines/disks-types.md#standard-ssd)</li><li>[Metas de escalabilidade do SSDs Premium](../articles/virtual-machines/disks-types.md#standard-hdd)</li><li>[Metas de escalabilidade de HDDs padrão](../articles/virtual-machines/disks-types.md#premium-ssd)</li><li>[Preços e cobrança de discos gerenciados](../articles/virtual-machines/disks-types.md#billing)</li>  
