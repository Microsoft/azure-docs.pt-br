---
title: Dimensionamento de aplicativos de HPC - máquinas virtuais do Azure | Microsoft Docs
description: Saiba como dimensionar aplicativos de HPC em VMs do Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 00d5b86c8cae01d342d55b7ad20ec59c3f7530bd
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707830"
---
# <a name="scaling-hpc-applications"></a>Dimensionamento de aplicativos de HPC

Desempenho ideal de ampliação e expansão de aplicativos de HPC no Azure requer o ajuste de desempenho e otimização experimentos para a carga de trabalho específica. Esta seção e as páginas específicas à série VM oferecem diretrizes gerais para dimensionar seus aplicativos.

## <a name="compiling-applications"></a>Compilação de aplicativos

Embora não seja necessário, compilação de aplicativos com os sinalizadores de otimização apropriada fornece o melhor desempenho de escala vertical em HB e VMs da série HC.

### <a name="amd-optimizing-cc-compiler"></a>AMD otimizando C /C++ compilador

O AMD otimizando C /C++ sistema de compilador do compilador (AOCC) oferece um alto nível de vários segmentos, com otimizações avançadas e suporte de processador que inclui a otimização global, vetorização, análises entre procedimentos, transformações de loop, e geração de código. Binários do compilador AOCC são adequados para sistemas de Linux com a Biblioteca GNU C (glibc) versão 2.17 e acima. O conjunto de compilador consiste em uma C /C++ compilador (clang), um compilador Fortran (FLANG) e um front-end Fortran para Clang (Dragão ovo).

### <a name="clang"></a>Clang

Clang é um C, C++e o compilador de Objective-C de tratamento de pré-processamento, análise, otimização, geração de código, assembly e vinculação. Clang dá suporte a `-march=znver1` sinalizador para permitir melhor geração de código e ajuste para o Zen da AMD com base em x86 arquitetura.

### <a name="flang"></a>FLANG

O compilador FLANG é uma adição recente ao suite AOCC (adicionado de abril de 2018) e está atualmente em pré-lançamento para desenvolvedores baixar e testar. Com base em 2008 Fortran, AMD estende a versão do GitHub do FLANG (https://github.com/flangcompiler/flang). O compilador FLANG dá suporte a todas as opções do compilador Clang e um número adicional de opções do compilador FLANG específicos.

### <a name="dragonegg"></a>DragonEgg

DragonEgg é um plug-in gcc que substitui o do GCC otimizadores e geradores de código com aqueles do projeto LLVM. DragonEgg que vem com o works AOCC com 4.8.x gcc, foi testado para destinos x86 de 32/64 x86 e foi usado com êxito em várias plataformas Linux.

GFortran é o front-end real para programas de Fortran responsáveis de pré-processamento, análise e análise semântica geração da representação intermediária de GCC GIMPLE (IR). DragonEgg é um plug-in do GNU, conectando em um fluxo de compilação GFortran. Ele implementa o plug-in GNU API. Com a arquitetura de plug-in, DragonEgg torna-se o driver do compilador, orientando as diferentes fases de compilação.  Depois de seguir as instruções de download e instalação, Dragon ovo podem ser invocado usando: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>Compilador PGI
Comunidade PGI ver Edition. 17 é confirmada para trabalhar com EPYC AMD. Uma versão de compilação PGI do fluxo de entregar a largura de banda de memória total da plataforma. 18.10 de edição de comunidade mais recentes (novembro de 2018) da mesma forma deve funcionar bem. Abaixo está o exemplo de CLI para o compilador de maneira ideal com o compilador Intel:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Compilador Intel
Versão do compilador Intel 18 é confirmada para trabalhar com EPYC AMD. Abaixo está o exemplo de CLI para compilador de maneira ideal com o compilador Intel.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>Compilador GCC 
Para HPC, AMD recomenda compilador GCC 7.3 ou mais recente. Versões mais antigas, como 4.8.5 incluído com o RHEL/CentOS 7.4, não são recomendadas. GCC 7.3 e mais recente, proporcionará um desempenho significativamente mais alto em testes HPL, HPCG e DGEMM.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Dimensionamento de aplicativos 

As sugestões a seguir se aplicam a eficiência, desempenho e a consistência de dimensionamento ideal do aplicativo:

* PIN processa ao uso de 0 a 59 núcleos uma fixação sequencial abordagem (em oposição a uma abordagem de balanceamento automático). 
* Associação pelo Numa/Core/HwThread é melhor do que a associação padrão.
* Para aplicativos paralelos híbrido (OpenMP + MPI), use 4 threads e 1 classificação MPI por CCX.
* Para aplicativos de MPI puros, experimente com 1 a 4 que MPI classifica por CCX para otimizar o desempenho.
* Alguns aplicativos com extremo sensibilidade à largura de banda de memória podem se beneficiar do uso de um número reduzido de núcleos por CCX. Para esses aplicativos, o usando 3 ou 2 núcleos por CCX pode reduzir a contenção de largura de banda de memória e produzir um melhor desempenho do mundo real e escalabilidade mais consistente. Em particular, Allreduce MPI pode se beneficiar deste.
* Para execuções de dimensionamento significativamente maiores, é recomendável usar os transportes de RC + UD UD ou híbrida. Muitas bibliotecas de tempo de execução/bibliotecas MPI fazer isso internamente (como UCX ou MVAPICH2). Verifique suas configurações de transporte para as execuções em larga escala.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
