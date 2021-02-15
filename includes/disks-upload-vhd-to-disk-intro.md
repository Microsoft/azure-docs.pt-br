---
title: arquivo de inclusão
description: arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80420947"
---
Este artigo explica como carregar um VHD do computador local para um disco gerenciado do Azure ou copiar um disco gerenciado para outra região usando AzCopy. Esse processo, upload direto, também permite que você carregue um VHD de até 32 TiB de tamanho diretamente em um disco gerenciado. Atualmente, o carregamento direto tem suporte para discos gerenciados HDD padrão, SSD Standard e SSD Premium. Ainda não há suporte para ultra discos.

Se você estiver fornecendo uma solução de backup para VMs IaaS no Azure, recomendamos o uso do carregamento direto para restaurar os backups do cliente para o Managed disks. Ao carregar um VHD de uma origem externa para o Azure, as velocidades dependeriam da largura de banda local. Ao carregar ou copiar de uma VM do Azure, sua largura de banda será a mesma que a HDDs padrão.