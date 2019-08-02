---
title: Introdução à Máquina Virtual de Aprendizado Profundo – Azure | Microsoft Docs
description: Principais cenários de análise e componentes para Máquinas Virtuais de Aprendizado Profundo.
keywords: aprendizado profundo, AI, ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/16/2018
ms.author: vijetaj
ms.openlocfilehash: ddf1169d94c3e71b92e8b1835040ab5738bf94b8
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68591897"
---
# <a name="introduction-to-the-deep-learning-virtual-machine"></a>Introdução à Máquina Virtual de Aprendizado Profundo

## <a name="why-deep-learning-virtual-machine"></a>Por que Máquina Virtual de Aprendizado Profundo? 

Cada vez mais, algoritmos de aprendizado profundo/redes neurais profundas estão se tornando um dos métodos mais comuns utilizados em muitos problemas de aprendizado de máquina. Eles são especialmente bons para tarefas de cognição de computador, como imagem, texto, entendimento de áudio/vídeo, com frequência aproximando-se dos níveis cognitivos humanos em alguns domínios específicos com avançadas arquiteturas de rede neural profunda e acesso a um grande conjunto de dados para treinar modelos. Aprendizado profundo exige uma grande quantidade de potência computacional para treinar modelos com esses grandes conjuntos de dados. Com a nuvem e a disponibilidade de unidades de processamento gráfico (GPUs), está se tornando possível compilar arquiteturas neurais profundas sofisticadas e treiná-los em um conjunto de dados grande em poderosa infraestrutura de computação em nuvem.  A [Máquina Virtual de Ciência de Dados](overview.md) forneceu um rico conjunto de ferramentas e exemplos para preparação de dados, aprendizado de máquina e aprendizado profundo. Porém, um dos desafios enfrentados pelos usuários é descobrir as ferramentas e os exemplos para cenários específicos, como o aprendizado profundo fácil e também como provisionar instâncias de VM baseada em GPU mais facilmente. Esta DLVM Máquina Virtual de Aprendizado Profundo enfrenta esses desafios. 

## <a name="what-is-deep-learning-virtual-machine"></a>O que é Máquina Virtual de Aprendizado Profundo? 
A Máquina Virtual de Aprendizado Profundo é uma variante especialmente configurada da conhecida DSVM ([Máquina Virtual de Ciência de Dados](overview.md)) para tornar mais fácil usar instâncias de VM baseadas em GPU para o treinamento de modelos de aprendizado profundo. Há suporte no Windows 2016 e na Máquina Virtual de Ciência de Dados Ubuntu.  Compartilha as mesmas imagens de VM de núcleo (e, portanto, todo o conjunto de ferramentas avançado) como a DSVM, mas é configurada para facilitar o aprendizado. Nós também fornecemos exemplos de ponta a ponta para entendimento de imagem e texto, que são amplamente aplicáveis a vários cenários de AI do mundo real. A máquina virtual de aprendizado profundo também tenta tornar mais fácil a descoberta de um avançado conjunto de ferramentas e exemplos na DSVM expondo um catálogo das ferramentas e dos exemplos na máquina virtual. Em termos de ferramentas, a Máquina Virtual de Aprendizado Profundo oferece várias estruturas de aprendizado profundo populares, ferramentas para adquirir e pré-processar dados textuais e imagens. Para obter uma lista abrangente de ferramentas, consulte a [página de Visão geral de Máquina Virtual de Ciência de Dados](overview.md#whats-included-in-the-data-science-vm). 

## <a name="next-steps"></a>Próximas etapas

Introdução à Máquina Virtual de Aprendizado Profundo com as seguintes etapas:

* [Provisionar uma Máquina Virtual de Aprendizado Profundo](provision-deep-learning-dsvm.md)
* [Usar a Máquina Virtual de Aprendizado Profundo](use-deep-learning-dsvm.md)
* [Referência de ferramenta](dsvm-deep-learning-ai-frameworks.md)
* [Exemplos](dsvm-samples-and-walkthroughs.md)
