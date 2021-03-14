---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e94b14584f8eece3d772f67b680df4a2ffb8b5cd
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603210"
---
Por enquanto, ultra discos têm limitações adicionais, como a seguir:

As únicas opções de redundância de infraestrutura disponíveis atualmente para ultra discos são zonas de disponibilidade. As VMs que usam qualquer outra opção de redundância não podem anexar um ultra Disk.

A tabela a seguir descreve as regiões em que os ultra discos estão disponíveis, bem como suas opções de disponibilidade correspondentes:

> [!NOTE]
> Se uma região na lista a seguir não tiver nenhuma zona de disponibilidade com capacidade de disco ultra, as VMs nessa região deverão ser implantadas sem nenhuma opção de redundância de infraestrutura para anexar um ultra Disk.

|Regiões  |Opções de redundância  |
|---------|---------|
|Sul do Brasil     |Somente VMs únicas (conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais não têm suporte)|
|Índia Central     |Somente VMs únicas (conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais não têm suporte)|
|Leste da Ásia     |Somente VMs únicas (conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais não têm suporte)|
|Centro-Oeste da Alemanha     |Somente VMs únicas (conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais não têm suporte)|
|Coreia Central     |Somente VMs únicas (conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais não têm suporte)|
|Centro-Sul dos Estados Unidos    |Somente VMs únicas (conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais não têm suporte)|
|Governo dos EUA do Arizona     |Somente VMs únicas (conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais não têm suporte)|
|Gov. dos EUA – Virgínia     |Somente VMs únicas (conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais não têm suporte)|
|Governo dos EUA do Texas     |Somente VMs únicas (conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais não têm suporte)|
|Oeste dos EUA     |Somente VMs únicas (conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais não têm suporte)        |
|Austrália Central    |Somente VMs únicas (conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais não têm suporte)|
|Leste da Austrália     |Três zonas de disponibilidade         |
|Sudeste Asiático    |Três zonas de disponibilidade        |
|Canadá Central     |Três zonas de disponibilidade          |
|Centro dos EUA     |Três zonas de disponibilidade          |
|Leste dos EUA     |Três zonas de disponibilidade          |
|Leste dos EUA 2     |Três zonas de disponibilidade         |
|França Central    |Duas zonas de disponibilidade        |
|Leste do Japão    |Três zonas de disponibilidade        |
|Norte da Europa    |Três zonas de disponibilidade        |
|Sul do Reino Unido    |Três zonas de disponibilidade        |
|Europa Ocidental    | Três zonas de disponibilidade|
|Oeste dos EUA 2    |Três zonas de disponibilidade|

- Há suporte apenas na seguinte série de VMs:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [Dasv4](../articles/virtual-machines/dav4-dasv4-series.md#dasv4-series)
    - [Ddsv4](../articles/virtual-machines/ddv4-ddsv4-series.md#ddsv4-series)
    - [Dsv4](../articles/virtual-machines/dv4-dsv4-series.md#dsv4-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nem todo tamanho de VM está disponível em todas as regiões com suporte com ultra discos.
- Estão disponíveis somente como discos de dados. 
- Suporte ao tamanho de setor físico de 4K por padrão. o tamanho do setor 512E está disponível como uma oferta geralmente disponível (nenhuma inscrição é necessária), mas só está disponível no momento usando a CLI ou o PowerShell. A maioria dos aplicativos é compatível com tamanhos de setor de 4K, mas alguns exigem tamanhos de setor de 512 bytes. Um exemplo seria Oracle Database, que requer a versão 12,2 ou posterior para dar suporte aos discos nativos de 4K. Para versões mais antigas do Oracle DB, é necessário o tamanho do setor de 512 bytes.
- Só pode ser criado como discos vazios.
- Atualmente, o não oferece suporte a instantâneos de disco, imagens de VM, conjuntos de disponibilidade, hosts dedicados do Azure ou Azure Disk Encryption.
- Atualmente, o não oferece suporte à integração com o backup do Azure ou Azure Site Recovery.
- Dá suporte apenas a leituras não armazenadas em cache e gravações não armazenadas em cache.
- O limite máximo atual para IOPS em VMs GA é 80.000.

Os ultra discos do Azure oferecem até 16 TiB por região por assinatura por padrão, mas os ultra discos oferecem suporte à maior capacidade por solicitação. Para solicitar um aumento na capacidade, entre em contato com o suporte do Azure.
