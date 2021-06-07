---
title: Histórico de lançamento de versão
titleSuffix: Microsoft Genomics
description: O histórico de lançamento das atualizações para o cliente Python Microsoft Genomics para correções e novas funcionalidades.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 01/11/2019
ms.openlocfilehash: 20475e2cde1b42790740889cf341b3a0a0afccc0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "76991075"
---
# <a name="version-release-history"></a>Histórico de lançamento de versão
A equipe do Microsoft Genomics atualiza regularmente o cliente Microsoft Genomics Python para correções e novas funcionalidades. 

## <a name="latest-release"></a>Versão mais recente
O cliente Python atual é a versão 0.9.0. Ele foi lançado em fevereiro de 6 2019 e dá suporte a fluxos de trabalho em execução com GATK 3,5 e GATK4. Ele dá suporte à saída de gVCF e pode aceitar um argumento opcional para compactação de saída.


## <a name="release-history"></a>Histórico de versões 
Novas versões do cliente Microsoft Genomics Python são lançadas uma vez por ano. Quando são lançadas novas versões do cliente Microsoft Genomics Python, uma lista de correções e recursos é atualizada aqui. Quando novas versões são lançadas, as versões anteriores devem continuar a ter suporte pelo menos por 90 dias. Quando não há suporte para versões anteriores, será indicado nesta página. 

### <a name="version-090"></a>0.9.0 da versão
A versão 0.9.0 inclui suporte para compactação de saída. Isso é equivalente a executar `-bgzip` seguido de `-tabix` na saída vcf ou gvcf. Confira mais informações nas [Perguntas frequentes](frequently-asked-questions-genomics.md). 

### <a name="version-081"></a>Versão 0.8.1
A versão 0.8.1 inclui correções de bugs secundários.  

### <a name="version-080"></a>Versão 0.8.0
Versão 0.8.0 inclui suporte para GATK4 e saída de gVCFs.  

### <a name="version-074"></a>Versão 0.7.4
Versão 0.7.4 inclui suporte para aceitar tokens SAS em vez de chaves de conta na entrada `config.txt`. Para obter mais informações, consulte [Início rápido de tokens SAS de entrada](quickstart-input-sas.md). 

### <a name="version-073"></a>Versão 0.7.3
A versão 0.7.3 inclui correções de bugs secundários.

### <a name="version-072"></a>Versão 0.7.2
A versão 0.7.2 é a versão inicial. 1 de novembro de 2017 foi lançado.
