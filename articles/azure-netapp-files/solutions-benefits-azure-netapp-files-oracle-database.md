---
title: Benefícios do uso de Azure NetApp Files com Oracle Database | Microsoft Docs
description: Descreve a tecnologia e fornece uma comparação de desempenho entre o Oracle Direct NFS (dNFS) e o cliente NFS tradicional. Mostra as vantagens de usar o dNFS com Azure NetApp Files.
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
ms.date: 04/23/2020
ms.author: b-juche
ms.openlocfilehash: 0f431015caf5bb3364fe5628a599f760c6318c47
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91932491"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Benefícios do uso do Azure NetApp Files com o Oracle Database

O Oracle Direct NFS (dNFS) torna possível gerar um desempenho maior do que o próprio driver NFS do sistema operacional. Este artigo explica a tecnologia e fornece uma comparação de desempenho entre o dNFS e o cliente NFS tradicional (kernel NFS). Ele também mostra as vantagens e a facilidade de usar o dNFS com Azure NetApp Files.  

## <a name="how-oracle-direct-nfs-works"></a>Como funciona o Oracle Direct NFS

O resumo a seguir explica como o Oracle Direct NFS funciona em um alto nível:

* O NFS direto do Oracle ignora o cache do buffer do sistema operacional. Os dados são armazenados em cache apenas uma vez no espaço do usuário, eliminando a sobrecarga de cópias de memória.  

* O cliente NFS tradicional usa um único fluxo de rede, conforme mostrado abaixo:    

    ![Cliente NFS tradicional usando um único fluxo de rede](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

    O Oracle Direct NFS melhora ainda mais o desempenho por meio do balanceamento de carga do tráfego de rede entre vários fluxos de rede. Conforme testado e mostrado abaixo, 650 conexões de rede distintas foram estabelecidas dinamicamente pelo Oracle Database:  

    ![Oracle Direct NFS aprimorando o desempenho](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

As [perguntas frequentes da Oracle para o NFS direto](http://www.orafaq.com/wiki/Direct_NFS) mostram que o Oracle dNFS é um cliente NFS otimizado. Ele fornece acesso rápido e escalonável ao armazenamento NFS que está localizado em dispositivos de armazenamento NAS (acessíveis em TCP/IP). o dNFS é integrado ao kernel do banco de dados, assim como o ASM, que é usado principalmente com armazenamento DAS ou SAN. Dessa forma, *a diretriz é usar o dNFS ao implementar o armazenamento nas e usar o ASM ao implementar o armazenamento SAN.*

dNFS é a opção padrão no Oracle 18C.

dNFS está disponível a partir da Oracle Database 11g. O diagrama a seguir compara o dNFS com o NFS nativo. Quando você usa o dNFS, um banco de dados Oracle que é executado em uma máquina virtual do Azure pode gerar mais e/s do que o cliente NFS nativo.

![Comparação entre Oracle e Azure NetApp Files do dNFS com NFS nativo](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

Você pode habilitar ou desabilitar o dNFS executando dois comandos e reiniciando o banco de dados.

Para habilitar:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

Para desabilitar:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Azure NetApp Files combinado com o NFS direto do Oracle

Você pode aprimorar o desempenho do Oracle dNFS com o serviço Azure NetApp Files. O serviço oferece controle total sobre o desempenho do aplicativo. Ele pode atender a aplicativos extremamente exigentes. A combinação do Oracle dNFS com o Azure NetApp Files oferece grande vantagem para suas cargas de trabalho.

## <a name="next-steps"></a>Próximas etapas

- [Arquiteturas da solução usando o Azure NetApp Files](azure-netapp-files-solution-architectures.md)
- [Visão geral de soluções e Aplicativos Oracle no Azure](../virtual-machines/workloads/oracle/oracle-overview.md)