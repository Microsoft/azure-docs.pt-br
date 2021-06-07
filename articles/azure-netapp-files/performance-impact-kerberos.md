---
title: Impacto no desempenho do Kerberos em volumes Azure NetApp Files NFSv 4.1 | Microsoft Docs
description: Descreve as opções de segurança disponíveis, os vetores de desempenho testados e o impacto de desempenho esperado do Kerberos em volumes Azure NetApp Files NFSv 4.1.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: b-juche
ms.openlocfilehash: e9054f11c8f55e9fe00266840a9f6e257f14e659
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744315"
---
# <a name="performance-impact-of-kerberos-on-azure-netapp-files-nfsv41-volumes"></a>Impacto no desempenho do Kerberos em volumes Azure NetApp Files NFSv 4.1

O Azure NetApp Files dá suporte à [criptografia de cliente NFS nos modos Kerberos](configure-kerberos-encryption.md) (krb5, krb5i e krb5p) com a criptografia AES-256. Este artigo descreve o impacto no desempenho do Kerberos em volumes NFSv 4.1. 

## <a name="available-security-options"></a>Opções de segurança disponíveis 

As opções de segurança disponíveis atualmente para os volumes NFSv 4.1 são as seguintes: 

* **SEC = sys** usa UIDs UNIX locais e GIDs usando AUTH_SYS para autenticar operações de NFS.
* **sec = krb5** usa Kerberos v5 em vez de UIDs UNIX locais e GIDs para autenticar usuários.
* **SEC = krb5i** usa Kerberos V5 para autenticação de usuário e executa a verificação de integridade de operações de NFS usando somas de verificação seguras para evitar a violação de dados.
* **SEC = krb5p** usa Kerberos V5 para autenticação de usuário e verificação de integridade. Ele criptografa o tráfego NFS para evitar a detecção de tráfego. Essa opção é a configuração mais segura, mas também envolve a maior sobrecarga de desempenho.

## <a name="performance-vectors-tested"></a>Vetores de desempenho testados

Esta seção descreve o impacto de desempenho único no lado do cliente das várias `sec=*` opções.

* O impacto no desempenho foi testado em dois níveis: baixa simultaneidade (baixa carga) e alta simultaneidade (limites superiores de e/s e taxa de transferência).  
* Três tipos de cargas de trabalho foram testadas:  
    * Leitura/gravação aleatória de operação pequena (usando FIO)
    * Leitura/gravação sequencial de operação grande (usando FIO)
    * Carga de trabalho pesada de metadados, conforme gerado por aplicativos como git

## <a name="expected-performance-impact"></a>Impacto esperado no desempenho 

Há duas áreas de foco: carga leve e limite superior. As listas a seguir descrevem a configuração de segurança de impacto de desempenho por configuração de segurança e cenário por cenário. Todas as comparações são feitas em relação ao `sec=sys` parâmetro de segurança. O teste foi feito em um único volume, usando um único cliente. 

Impacto no desempenho do krb5:

* Baixa simultaneidade (r/w):
    * A latência sequencial aumentou 0,3 ms.
    * Latência de e/s aleatória aumentada em 0,2 ms.
    * A latência de e/s de metadados aumentou 0,2 ms.
* Alta simultaneidade (r/w): 
    * A taxa de transferência máxima sequencial não foi afetada por krb5.
    * A e/s aleatória máxima diminuiu em 30% para cargas de trabalho de leitura puras com o impacto geral descartando para zero, uma vez que a carga de trabalho muda para a gravação pura. 
    * A carga de trabalho de metadados máxima diminuiu em 30%.

Impacto no desempenho do krb5i: 

* Baixa simultaneidade (r/w):
    * A latência sequencial aumentou 0,5 ms.
    * Latência de e/s aleatória aumentada em 0,2 ms.
    * A latência de e/s de metadados aumentou 0,2 ms.
* Alta simultaneidade (r/w): 
    * A taxa de transferência sequencial máxima diminuiu em 70% geral, independentemente da mistura da carga de trabalho.
    * A e/s aleatória máxima diminuiu em 50% para cargas de trabalho de leitura puras com o impacto geral que diminui para 25% à medida que a carga de trabalho muda para a gravação pura. 
    * A carga de trabalho de metadados máxima diminuiu em 30%.

Impacto no desempenho do krb5p:

* Baixa simultaneidade (r/w):
    * A latência sequencial aumentou 0,8 ms.
    * Latência de e/s aleatória aumentada em 0,2 ms.
    * A latência de e/s de metadados aumentou 0,2 ms.
* Alta simultaneidade (r/w): 
    * A taxa de transferência sequencial máxima diminuiu em 85% geral, independentemente da mistura da carga de trabalho. 
    * A e/s aleatória máxima diminuiu em 65% para cargas de trabalho de leitura pura com o impacto geral que diminui para 43%, pois a carga de trabalho muda para a gravação pura. 
    * A carga de trabalho de metadados máxima diminuiu em 30%.

## <a name="next-steps"></a>Próximas etapas  

* [Configurar criptografia do Kerberos do NFSv 4.1 para Azure NetApp Files](configure-kerberos-encryption.md) 
