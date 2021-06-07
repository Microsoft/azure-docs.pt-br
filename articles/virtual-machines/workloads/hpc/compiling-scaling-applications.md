---
title: Dimensionamento de aplicativos HPC-máquinas virtuais do Azure | Microsoft Docs
description: Saiba como dimensionar aplicativos HPC em VMs do Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 4ab2c599bea4b2e3e682755a80a2ee348e4de7ef
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606769"
---
# <a name="scaling-hpc-applications"></a>Dimensionamento de aplicativos HPC

O desempenho ideal de expansão e escalabilidade horizontal de aplicativos HPC no Azure requer testes de desempenho e de otimização para a carga de trabalho específica. Esta seção e as páginas específicas da série de VMs oferecem diretrizes gerais para dimensionar seus aplicativos.

## <a name="application-setup"></a>Instalação do aplicativo
O [repositório azurehpc](https://github.com/Azure/azurehpc) contém muitos exemplos de:
- Configurando e executando [aplicativos](https://github.com/Azure/azurehpc/tree/master/apps) de maneira ideal.
- Configuração de [sistemas de arquivos e clusters](https://github.com/Azure/azurehpc/tree/master/examples).
- [Tutoriais](https://github.com/Azure/azurehpc/tree/master/tutorials) sobre como começar facilmente com alguns fluxos de trabalho de aplicativo comuns.

## <a name="optimally-scaling-mpi"></a>Dimensionamento de MPI de maneira ideal 

As sugestões a seguir se aplicam à eficiência, ao desempenho e à consistência de dimensionamento de aplicativos:

- Para trabalhos de escala menores (ou seja, < conexões de 256K), use a opção:
   ```bash
   UCX_TLS=rc,sm
   ```

- Para trabalhos de escala maiores (ou seja, > conexões de 256K), use a opção:
   ```bash
   UCX_TLS=dc,sm
   ```

- No acima, para calcular o número de conexões para seu trabalho MPI, use:
   ```bash
   Max Connections = (processes per node) x (number of nodes per job) x (number of nodes per job) 
   ```

## <a name="adaptive-routing"></a>Roteamento adaptável
O AR (roteamento adaptável) permite que as VMs (máquinas virtuais) do Azure executando EDR e HDR InfiniBand detectem e evitem automaticamente o congestionamento da rede, selecionando dinamicamente caminhos de rede mais ideais. Como resultado, o AR oferece maior latência e largura de banda na rede InfiniBand, que, por sua vez, impulsiona o desempenho e a eficiência de dimensionamento. Para obter mais detalhes, consulte o [artigo TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/adaptive-routing-on-azure-hpc/ba-p/1205217).

## <a name="process-pinning"></a>Fixação de processo

- Fixe processos em núcleos usando uma abordagem de fixação sequencial (em oposição a uma abordagem de autoequilíbrio). 
- A associação por numa/Core/HwThread é melhor que a associação padrão.
- Para aplicativos paralelos híbridos (OpenMP + MPI), use 4 threads e uma classificação MPI por CCX nos tamanhos de VM HB e HBv2.
- Para aplicativos MPI puros, experimente com 1-4 classificações MPI por CCX para obter um desempenho ideal nos tamanhos de VM HB e HBv2.
- Alguns aplicativos com extrema sensibilidade à largura de banda da memória podem se beneficiar do uso de um número reduzido de núcleos por CCX. Para esses aplicativos, usar 3 ou 2 núcleos por CCX pode reduzir a contenção de largura de banda de memória e gerar um desempenho do mundo real maior ou uma escalabilidade mais consistente. Em particular, a MPI irreduza pode se beneficiar dessa abordagem.
- Para execuções de escala significativamente maiores, é recomendável usar transportes UD ou RC do UD híbrido. Muitas bibliotecas MPI/bibliotecas de tempo de execução fazem isso internamente (como UCX ou MVAPICH2). Verifique suas configurações de transporte para execuções em larga escala.

## <a name="compiling-applications"></a>Compilando aplicativos
<br>
<details>
<summary>Clique para expandir</summary>

Embora não seja necessário, a compilação de aplicativos com sinalizadores de otimização apropriados fornece o melhor desempenho de expansão nas VMs da série HB e HC.

### <a name="amd-optimizing-cc-compiler"></a>AMD otimizando o compilador C/C++

O sistema do compilador AMD de otimização C/C++ (AOCC) oferece um alto nível de otimizações avançadas, multithreading e suporte a processador que inclui otimização global, Vetorização, análises entre procedimentos, transformações de loop e geração de código. Os binários do compilador AOCC são adequados para sistemas Linux com a versão 2,17 do GNU C (glibc) e superior. O conjunto de compiladores consiste em um compilador C/C++ (Clang), um compilador Fortran (FLANG) e um front-end Fortran para Clang (ovo de dragão).

### <a name="clang"></a>Clang

Clang é um compilador C, C++ e Objective-C que manipula o pré-processamento, a análise, a otimização, a geração de código, o assembly e a vinculação. O Clang dá suporte ao  `-march=znver1` sinalizador para permitir a melhor geração de código e ajuste para a arquitetura x86 baseada em Zen da AMD.

### <a name="flang"></a>FLANG

O compilador FLANG é uma adição recente ao AOCC Suite (adicionado em abril de 2018) e atualmente está em pré-lançamento para que os desenvolvedores baixem e testem. Com base no Fortran 2008, o AMD estende a versão do GitHub do FLANG ( https://github.com/flang-compiler/flang) . O compilador FLANG dá suporte a todas as opções do compilador Clang e a um número adicional de opções de compilador específicas do FLANG.

### <a name="dragonegg"></a>DragonEgg

DragonEgg é um plug-in gcc que substitui os otimizadores de GCC e os geradores de código pelos do projeto LLVM. O DragonEgg que vem com o AOCC funciona com gcc-4.8. x, foi testado para destinos x86-32/x86-64 e foi usado com êxito em várias plataformas Linux.

GFortran é o front-end real para programas Fortran responsáveis pelo pré-processamento, análise e análise semântica que geram a IR () de uma GIMPLE (representação intermediária) de GCC. DragonEgg é um plug-in GNU, conectando-se ao fluxo de compilação do GFortran. Ele implementa a API de plug-in GNU. Com a arquitetura do plug-in, o DragonEgg se torna o driver do compilador, orientando as diferentes fases de compilação.  Depois de seguir as instruções de download e instalação, o ovo de Dragão pode ser chamado usando: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>Compilador PGI
PGI Community Edition ver. 17 é confirmado para funcionar com o AMD EPYC. Uma versão compilada PGI do STREAM fornece largura de banda de memória completa da plataforma. O mais recente Community Edition 18,10 (Nov 2018) deve funcionar bem. Abaixo está a CLI de exemplo para o compilador de forma ideal com o compilador da Intel:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Compilador Intel
Intel compilador ver. 18 é confirmado para funcionar com o AMD EPYC. Abaixo está a CLI de exemplo para o compilador de forma ideal com o compilador da Intel.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>Compilador GCC 
Para HPC, a AMD recomenda o compilador GCC 7,3 ou mais recente. As versões mais antigas, como 4.8.5 inclusas com RHEL/CentOS 7,4, não são recomendadas. O GCC 7,3 e mais recente fornecerá um desempenho significativamente maior nos testes HPL, HPCG e DGEMM.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```
</details>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [HPC](/azure/architecture/topics/high-performance-computing/) no Azure.
