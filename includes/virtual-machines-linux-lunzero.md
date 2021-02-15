---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09fa612e7e5c681da16bf19e94c626ae14a3b8a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77590763"
---
Ao adicionar discos de dados a uma VM Linux, você poderá encontrar erros se não existir um disco no LUN 0. Se você estiver adicionando um disco manualmente usando o comando `az vm disk attach -new` e especificar um LUN (`--lun`) em vez de deixar que a plataforma Azure determine o LUN apropriado, verifique com atenção se já existe (ou existirá) um disco no LUN 0. 

Considere o exemplo a seguir que mostra um snippet da saída do `lsscsi`:

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

Os dois discos de dados existem no LUN 0 e no LUN 1 (a primeira coluna nos detalhes da saída de `lsscsi``[host:channel:target:lun]`). Ambos os discos devem estar acessíveis na VM. Se você tivesse especificado manualmente o primeiro disco para ser adicionado ao LUN 1 e o segundo disco ao LUN 2, os discos não apareceriam corretamente para a VM.

> [!NOTE]
> O valor de `host` do Azure é 5 nestes exemplos, mas ele poderá variar dependendo do tipo de armazenamento selecionado.
> 
> 

Esse comportamento de disco não é um problema do Azure, mas da maneira em que o kernel do Linux segue as especificações do SCSI. Quando o kernel do Linux verifica os dispositivos conectados no barramento do SCSI, um dispositivo deve estar presente no LUN 0 para que o sistema continue a verificar outros dispositivos. Assim:

* Examine a saída do `lsscsi` depois de adicionar um disco de dados para verificar se há um disco no LUN 0.
* Se o disco não aparecer corretamente para a VM, verifique se existe um disco no LUN 0.

