---
title: Comparando as imagens personalizadas e as fórmulas nos DevTest Labs | Microsoft Docs
description: Saiba mais sobre as diferenças entre imagens personalizadas e fórmulas como bases de VM para que você possa decidir qual é mais adequada para seu ambiente.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: df7a8e6209f8033eb5a29c65079e9c2f4cbbe544
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87287540"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Comparando imagens personalizadas e fórmulas em Laboratórios de Desenvolvimento/Teste
É possível usar tanto as [imagens personalizadas](devtest-lab-create-template.md) quanto as [fórmulas](devtest-lab-manage-formulas.md) como bases para as [novas VMs criadas](devtest-lab-add-vm.md). No entanto, a principal diferença entre fórmulas e imagens personalizadas é que uma imagem personalizada é simplesmente uma imagem baseada em um VHD, enquanto uma fórmula é uma imagem baseada em um VHD, *além de* definições pré-configuradas, como tamanho da VM, rede virtual, sub-rede e artefatos. Essas configurações pré-configuradas são definidas com valores padrão que podem ser substituídos no momento da criação da VM. Este artigo explica algumas das vantagens (prós) e desvantagens (contras) de usar imagens personalizadas versus usar fórmulas.

## <a name="custom-image-pros-and-cons"></a>Prós e contras das imagens personalizadas
As imagens personalizadas fornecem uma maneira estática e imutável para criar VMs em um ambiente desejado. 

**Prós**

* O provisionamento da VM de uma imagem personalizada é rápido, uma vez que nada muda depois que a VM é criada por meio da imagem. Em outras palavras, não há nenhuma configuração a ser aplicada, uma vez que a imagem personalizada é apenas uma imagem sem configurações. 
* VMs criadas por meio de uma única imagem personalizada são idênticas.

**Contras**

* Se você precisar atualizar algum aspecto da imagem personalizada, a imagem precisará ser recriada.  

## <a name="formula-pros-and-cons"></a>Prós e contras da fórmula
As fórmulas fornecem uma maneira dinâmica de criar VMs por meio das configurações desejadas.

**Prós**

* As alterações no ambiente podem ser capturadas em tempo real por meio de artefatos. Por exemplo, se você deseja uma VM instalada com os bits mais recentes do seu pipeline de lançamento ou inscrever o código mais recente do seu repositório, pode especificar simplesmente um artefato que implanta os bits mais recentes ou inscreve o código mais recente na fórmula juntamente com uma imagem base de destino. Sempre que essa fórmula é usada para criar VMs, os bits/códigos mais recentes são implantados/inscritos na VM. 
* As fórmulas podem definir as configurações padrão que as imagens personalizadas não podem fornecer, como configurações de rede virtual e tamanhos de VM. 
* As configurações salvas em uma fórmula são exibidas como valores padrão, mas podem ser modificadas quando a VM é criada. 

**Contras**

* Criar uma VM por meio de uma fórmula pode levar mais tempo do que criar uma VM por meio de uma imagem personalizada.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionadas
* [Imagens personalizadas ou fórmulas?](./devtest-lab-faq.md#blog-post)

## <a name="next-steps"></a>Próximas etapas
- [Perguntas frequentes do DevTest Labs](devtest-lab-faq.md)
