---
title: Matriz de suporte de backup do SAP HANA
description: Neste artigo, saiba mais sobre os cenários e limitações com suporte ao usar o backup do Azure para fazer backup de bancos de dados SAP HANA em VMs do Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.custom: references_regions
ms.openlocfilehash: cbf910a0291e90965c9698a8b2a43c587cbfe0b8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707227"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matriz de suporte para backup de bancos de dados do SAP HANA em VMs do Azure

O Backup do Azure oferece suporte ao backup de bancos de dados SAP HANA no Azure. Este artigo resume os cenários com suporte e as limitações presentes quando você usa o Backup do Azure para fazer backup de bancos de dados SAP HANA em VMs do Azure.

> [!NOTE]
> Agora a frequência do backup de log pode ser definida para 15 minutos, no mínimo. Os backups de log só começam a fluir depois que um backup completo bem-sucedido do banco de dados é concluído.

## <a name="scenario-support"></a>Suporte ao cenário

| **Cenário**               | **Configurações compatíveis**                                | **Configurações não compatíveis**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologia**               | SAP HANA em execução apenas em VMs Linux do Azure                    | Instâncias grandes do HANA (HLI)                                   |
| **Regiões**                   | **GA:**<br> **Américas**  - EUA Central, Leste dos EUA 2, Leste dos EUA, Centro-Norte dos EUA, Centro-Sul dos EUA, Oeste dos EUA 2, Centro-Oeste dos EUA, Oeste dos EUA, Canadá Central, Leste do Canadá, Sul do Brasil <br> **Ásia-Pacífico**  - Austrália Central, Austrália Central 2, Leste da Austrália, Sudeste da Austrália, Leste do Japão, Oeste do Japão, Coreia Central, Sul da Coreia, Leste da Ásia, Sudeste da Ásia, Índia Central, Sul da Índia, Oeste da Índia, Leste da China, Norte da China, Oeste da China 2, Norte da China 2 <br> **Europa** – Europa Ocidental, Europa setentrional, França Central, Sul do Reino Unido, Oeste do Reino Unido, Norte da Alemanha, Centro-oeste da Alemanha, Norte da Suíça, oeste da Suíça, central norte da Suíça, leste da Noruega, oeste da Noruega <br> **África/Oriente Médio** - Norte da África do Sul, Oeste da África do Sul, Norte dos EAU, EAU Central  <BR>  **Regiões do Azure Governamental** | Sul da França, Alemanha Central, Nordeste da Alemanha, IOWA do US Gov |
| **Versões do sistema operacional**            | SLES 12 com SP2, SP3, SP4 e SP5; SLES 15 com SP0, SP1, SP2 <br><br>  RHEL 7,4, 7,6, 7,7, 8,1 & 8,2                |                                             |
| **Versões do HANA**          | SDC no HANA 1. x, MDC no HANA 2. x SPS04, SPS05 Rev <= 53 (validado para cenários habilitados para criptografia também)      |                                                            |
| **Implantações do HANA**       | SAP HANA em uma única VM do Azure - Apenas escalar verticalmente. <br><br> Para implantações de alta disponibilidade, ambos os nós em dois computadores diferentes são tratados como nós individuais com cadeias de dados separadas.               | Escalabilidade <br><br> O backup não faz failover para o nó secundário automaticamente em implantações de alta disponibilidade. A configuração do backup deve ser feita separadamente para cada nó.                                           |
| **Instâncias do HANA**         | Uma única instância do SAP HANA em uma única VM do Azure - Apenas escalar verticalmente | Várias instâncias de SAP HANA em uma única VM. Você pode proteger apenas uma dessas várias instâncias de cada vez.                  |
| **Tipos de banco de dados do HANA**    | Contêiner do Banco de Dados Individual (SDC) em 1.x, contêiner de vários bancos de dados (MDC) em 2.x | MDC no HANA 1.x                                              |
| **Tamanho do banco de dados do HANA**     | Bancos de dados do HANA com tamanho <= 8 TB (esse não é o tamanho da memória do sistema HANA)               |                                                              |
| **Tipos de backup**           | Backups completos, diferenciais, incrementais e de log                          |  Instantâneos                                       |
| **Restaurar tipos**          | Consulte a observação [1642148](https://launchpad.support.sap.com/#/notes/1642148) do SAP HANA para saber mais sobre os tipos de restauração compatíveis |                                                              |
| **Limites do backup**          | Até 8 TB de tamanho de backup completo por instância de SAP HANA (limite flexível)         |                                                              |
| **Configurações especiais** |                                                              | SAP HANA + Camadas dinâmicas <br>  Clonagem por meio do LaMa        |

------

>[!NOTE]
>O Backup do Azure não é ajustado automaticamente para alterações do horário de verão ao fazer backup de um banco de dados SAP HANA em execução em uma VM do Azure.
>
>Modifique a política manualmente, se necessário.

> [!NOTE]
> Agora você pode [monitorar os trabalhos de backup e restauração](./sap-hana-db-manage.md#monitor-manual-backup-jobs-in-the-portal) (no mesmo computador) disparados de clientes nativos do HANA (SAP Hana Studio/cockpit/DBA cockpit) no portal do Azure.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [fazer backup de bancos de dados SAP Hana em execução em VMs do Azure](./backup-azure-sap-hana-database.md)
* Saiba como [restaurar bancos de dados do SAP HANA em execução em VMs do Azure](./sap-hana-db-restore.md)
* Saiba como [gerenciar bancos de dados do SAP HANA cujo backup é feito usando o Backup do Azure](sap-hana-db-manage.md)
* Saiba como [solucionar problemas comuns ao fazer backup de bancos de dados do SAP HANA](./backup-azure-sap-hana-database-troubleshoot.md)
