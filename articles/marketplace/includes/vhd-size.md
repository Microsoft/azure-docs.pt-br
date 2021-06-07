---
title: incluir arquivo
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: f4e34e850391696506beed9f6f386f85528dff24
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92283558"
---
Se você selecionou uma das VMs pré-configuradas com um sistema operacional (e serviços adicionais opcionais), você já escolheu um tamanho de VM padrão do Azure. Iniciar sua solução com um SO pré-configurado é a abordagem recomendada. No entanto, se você estiver instalando um so manualmente, deverá dimensionar seu VHD primário em sua imagem de VM. Verifique se o tamanho do disco do sistema operacional está dentro dos limites do Linux ou do Windows.

| Sistema operacional | Tamanho do VHD |
| --- | --- |
| Linux | 30 a 1023 GB |
| Windows | 30 a 250 GB |
|

As imagens base do Windows ou SQL Server fornecidas como uma base aprovada já atendem a esses requisitos, portanto, não altere o formato ou o tamanho do VHD.

Discos de dados podem ser de até 1 TB. Ao decidir sobre o tamanho, lembre-se de que os clientes não podem redimensionar VHDs dentro de uma imagem no momento da implantação. Crie VHDs de disco de dados como VHDs de formato fixo. Eles também devem ser expansíveis (esparsos/dinâmicos). Os discos de dados podem inicialmente estar vazios ou conter dados.