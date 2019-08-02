---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 06/28/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 9832ae6010f262322328ec22876a35c89e1bedca
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850321"
---
Colocar VMs em uma única região reduz a distância física entre as instâncias. Colocá-los em uma única zona de disponibilidade também os colocará fisicamente juntos. No entanto, à medida que a superfície do Azure cresce, uma única zona de disponibilidade pode abranger vários data centers físicos, o que pode resultar em uma latência de rede impactando seu aplicativo. 

Para obter as VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-las em um grupo de posicionamento de proximidade.

Um grupo de posicionamento de proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados próximos um do outro. Os grupos de posicionamento de proximidade são úteis para cargas de trabalho em que a baixa latência é um requisito.


- Baixa latência entre VMs autônomas.
- Baixa latência entre VMs em um único conjunto de disponibilidade ou um conjunto de dimensionamento de máquinas virtuais. 
- Baixa latência entre VMs autônomas, VMs em vários conjuntos de disponibilidade ou vários conjuntos de dimensionamento. Você pode ter vários recursos de computação em um único grupo de posicionamento para reunir um aplicativo em várias camadas. 
- Baixa latência entre várias camadas de aplicativo usando tipos de hardware diferentes. Por exemplo, executar o back-end usando a série M em um conjunto de disponibilidade e o front-end em uma instância da série D, em um conjunto de dimensionamento, em um único grupo de posicionamento de proximidade.

> [!IMPORTANT]
> Os grupos de posicionamento de proximidade estão atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Os grupos de posicionamento de proximidade não estão disponíveis nessas regiões durante a visualização: **Leste do Japão**, **leste da Austrália** e **Índia central**.


## <a name="best-practices"></a>Práticas recomendadas 
- Para a menor latência, use grupos de posicionamento de proximidade junto com a rede acelerada. Para obter mais informações, consulte [criar uma máquina virtual Linux com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [criar uma máquina virtual do Windows com rede acelerada](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Implante todos os tamanhos de VM em um único modelo do Resource Manager. Para evitar a aterrissagem de hardware que não dá suporte a todos os tamanhos e SKUs de VM necessários, inclua todas as camadas de aplicativo em um único modelo para que todas sejam implantadas ao mesmo tempo.
- Se você estiver criando scripts para sua implantação usando o PowerShell, a CLI ou o SDK, poderá obter um `OverconstrainedAllocationRequest`erro de alocação. Nesse caso, você deve parar/desalocar todas as VMs existentes e alterar a sequência no script de implantação para começar com os tamanhos/SKU da VM que falharam. 
- Ao reutilizar um grupo de posicionamento existente do qual as VMs foram excluídas, aguarde até que a exclusão seja concluída completamente antes de adicionar VMs a ela.
- Se a latência for sua primeira prioridade, coloque as VMs em um grupo de posicionamento de proximidade e toda a solução em uma zona de disponibilidade. Mas, se a resiliência for sua prioridade máxima, espalhe suas instâncias entre várias zonas de disponibilidade (um único grupo de posicionamento de proximidade não pode abranger zonas).