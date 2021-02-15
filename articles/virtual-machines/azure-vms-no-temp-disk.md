---
title: Perguntas frequentes tamanhos de VM do Azure sem disco temporário local
description: Este artigo fornece respostas para perguntas frequentes sobre Microsoft Azure tamanhos de VM que não têm disco temporário local.
author: brbell
ms.service: virtual-machines
ms.topic: conceptual
ms.author: brbell
ms.reviewer: mimckitt
ms.date: 06/15/2020
ms.openlocfilehash: 30587fac7d7be37d7595a78502b7999adee9a30f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665303"
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

> [!NOTE]
> Se uma imagem depender do disco de recurso ou se existir um arquivo de paginação ou permuta no disco temporário local, as imagens sem disco não funcionarão — em vez disso, use a alternativa ' com disco '. 

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
