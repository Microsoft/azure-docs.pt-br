---
title: Perguntas frequentes tamanhos de VM do Azure sem disco temporário local
description: Este artigo fornece respostas para perguntas frequentes sobre Microsoft Azure tamanhos de VM que não têm disco temporário local.
author: brbell
ms.service: virtual-machines
ms.topic: conceptual
ms.subservice: sizes
ms.author: brbell
ms.reviewer: mimckitt
ms.date: 06/15/2020
ms.openlocfilehash: 1937b8392ee3a73ed7c268897c532c643a9151eb
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565454"
---
# <a name="azure-vm-sizes-with-no-local-temporary-disk"></a>Tamanhos de VM do Azure sem disco temporário local 
Este artigo fornece respostas para perguntas frequentes sobre os tamanhos de VM do Azure que não têm um disco temporário local (ou seja, nenhum disco Temp local). Para obter mais informações sobre esses tamanhos de VM, consulte [especificações para dv4 e Dsv4 (cargas de trabalho uso geral)](dv4-dsv4-series.md) ou [especificações para as séries Ev4 e Esv4 (cargas de trabalho com otimização de memória)](ev4-esv4-series.md).

## <a name="what-does-no-local-temp-disk-mean"></a>O que não significa disco temporário local? 
Tradicionalmente, tivemos tamanhos de VM (por exemplo, Standard_D2s_v3, Standard_E48_v3) que incluem um pequeno disco local (ou seja, uma unidade D:). Agora com esses novos tamanhos de VM, esse pequeno disco local não existe mais; no entanto, você ainda pode anexar HDD Standard, SSD Premium ou SSD Ultra.

## <a name="what-if-i-still-want-a-local-temp-disk"></a>E se eu ainda quiser um disco temporário local?
Se sua carga de trabalho exigir um disco temporário local, também temos novos tamanhos [de VM Ddv4 e Ddsv4](ddv4-ddsv4-series.md) ou [Edv4 e Edsv4](edv4-edsv4-series.md) disponíveis. Esses tamanhos oferecem 50% de disco temporário maior em comparação com os tamanhos v3 anteriores.

> [!NOTE]
> O disco temporário local não é persistente; para garantir que seus dados sejam persistentes, use as opções HDD Standard, SSD Premium ou SSD Ultra. 

## <a name="what-are-the-differences-between-these-new-vm-sizes-and-the-general-purpose-dv3dsv3-or-the-memory-optimized-ev3esv3-vm-sizes-that-i-am-used-to"></a>Quais são as diferenças entre esses novos tamanhos de VM e o Uso Geral Dv3/Dsv3 ou os tamanhos de VM Ev3/Esv3 com otimização de memória com os quais estou acostumado? 
| famílias de VMs v3 com disco temporário local   | Novas famílias V4 com disco temporário local | Novas famílias v4 sem disco temporário local |
|---|---|---|
| Dv3   | Ddv4 | Dv4 |
| Dsv3 | Ddsv4  | Dsv4 |
| Ev3   | Edv4  | Ev4 |
| Esv3 | Edsv4 |    Esv4 | 

## <a name="can-i-resize-a-vm-size-that-has-a-local-temp-disk-to-a-vm-size-with-no-local-temp-disk"></a>Posso redimensionar um tamanho de VM que tem um disco temporário local para um tamanho de VM sem disco temporário local?  
Não. As únicas combinações permitidas para redimensionamento são: 

1. VM (com disco temporário local)-> VM (com disco temporário local); e 
2. VM (sem disco temporário local)-> VM (sem disco temporário local). 

Se estiver interessado em uma solução alternativa, consulte a próxima pergunta.

> [!NOTE]
> Se uma imagem depender do disco de recurso ou se existir um arquivo de paginação ou permuta no disco temporário local, as imagens sem disco não funcionarão — em vez disso, use a alternativa ' com disco '. 

## <a name="how-do-i-migrate-from-a-vm-size-with-local-temp-disk-to-a-vm-size-with-no-local-temp-disk"></a>Como fazer migrar de um tamanho de VM com disco temporário local para um tamanho de VM sem disco temporário local?  
Você pode migrar seguindo estas etapas: 

1. Conecte-se à sua máquina virtual que tem um disco temporário local (por exemplo, uma unidade D:) como um administrador local.
2. Siga as diretrizes na seção "mover temporariamente pagefile.sys para a unidade C" em [usar a unidade D: como uma unidade de dados em uma VM do Windows](./windows/change-drive-letter.md) para mover o arquivo de paginação do disco temporário local (D: Drive) para a unidade C:.

   > [!NOTE]
   > Siga as diretrizes na seção "mover temporariamente pagefile.sys para a unidade C" em usar a unidade D: como uma unidade de dados em uma VM do Windows para mover o arquivo de paginação do disco temporário local (D: Drive) para C: Drive. **O desvio das etapas descritas levará à mensagem de erro-"não é possível redimensionar a VM desde que a alteração do disco de recursos para o tamanho da VM do disco não recurso e vice-versa não seja permitida.**

3. Tire um instantâneo da VM seguindo as etapas descritas em [criar um instantâneo usando o portal ou CLI do Azure](./linux/snapshot-copy-managed-disk.md). 
4. Use o instantâneo para criar uma nova VM sem disco (como, DV4, Dsv4, Ev4, Esv4 Series) seguindo as etapas descritas em [criar uma máquina virtual de um instantâneo com a CLI](./scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md). 

## <a name="do-these-vm-sizes-support-both-linux-and-windows-operating-systems-os"></a>Esses tamanhos de VM dão suporte a sistemas operacionais Linux e Windows (SO)?
Sim.

## <a name="will-this-break-my-custom-scripts-custom-images-or-os-images-that-have-scratch-files-or-page-files-on-a-local-temp-disk"></a>Isso interromperá meus scripts personalizados, imagens personalizadas ou imagens do sistema operacional que têm arquivos ou arquivos de paginação em um disco temporário local?
Se a imagem personalizada do sistema operacional apontar para o disco temporário local, a imagem poderá não funcionar corretamente com esse tamanho sem disco.

## <a name="have-questions-or-feedback"></a>Dúvidas ou comentários?
Preencha o [formulário de comentários]( https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRUMzdCQkw0OVVRTldFUUtXSTlLQVBPUkVHSy4u). 

## <a name="next-steps"></a>Próximas etapas 
Neste documento, você aprendeu mais sobre as perguntas mais frequentes relacionadas às VMs do Azure sem disco temporário local. Para obter mais informações sobre esses tamanhos de VM, consulte os seguintes artigos:

- [Especificações para dv4 e Dsv4 (carga de trabalho de Uso Geral)](dv4-dsv4-series.md)
- [Especificações para Ev4 e Esv4 (carga de trabalho com otimização de memória)](ev4-esv4-series.md)
