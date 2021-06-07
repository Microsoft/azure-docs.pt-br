---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/05/2020
ms.author: alkohli
ms.openlocfilehash: 5aaf0ce747b14b2fa9f2fcd9a65b774aa7d2db3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87102443"
---
Com base na conta de armazenamento selecionada, o Data Box cria até:

* Três compartilhamentos para cada conta de armazenamento associada para GPv1 e GPv2.
* Um compartilhamento para armazenamento premium.
* Um compartilhamento para conta do Armazenamento de Blobs.

Em compartilhamentos de blob de blocos e blob de páginas, as entidades de primeiro nível são contêineres e as entidades de segundo nível são blobs. Em compartilhamentos de Arquivos do Azure, as entidades de primeiro nível são compartilhamentos e entidades de segundo nível são arquivos.

A tabela a seguir mostra o caminho UNC para os compartilhamentos na URL de caminho do Data Box e do Armazenamento do Azure em que os dados são carregados. A URL final de caminho do Armazenamento do Azure pode ser derivada do caminho de compartilhamento UNC.
 
| Blobs e arquivos | Caminhos e URLs |
| --------------- | -------------- |
| Blobs de blocos do Azure | <li>Caminho UNC para compartilhamentos: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>URL do Armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Blobs de páginas do Azure  | <li>Caminho UNC para compartilhamentos: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>URL do Armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Arquivos do Azure       |<li>Caminho UNC para compartilhamentos: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>URL do Armazenamento do Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

