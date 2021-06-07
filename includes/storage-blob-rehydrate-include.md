---
title: incluir arquivo
description: incluir arquivo
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: a369eb7000fb8622a69f4205ffcc232ae9c9d242
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95545917"
---
Para ler dados no armazenamento de arquivo morto, primeiro você deve alterar a camada do blob para quente ou fria. Esse processo é conhecido como reidratação e pode levar horas para ser concluído. Recomendamos tamanhos de blob grandes para o desempenho ideal de reidratação. Reidratar vários blobs pequenos simultaneamente pode inserir um tempo adicional. Atualmente, há duas prioridades de reidratar, alta e padrão, que podem ser definidas por meio da propriedade *x-MS-reidratar-Priority* opcional em uma operação de [camada de blob](/rest/api/storageservices/set-blob-tier) ou de [cópia](/rest/api/storageservices/copy-blob) de conjunto.

* **Prioridade padrão**: a solicitação reidratação será processada na ordem em que foi recebida e pode levar até 15 horas.
* **Prioridade alta**: a solicitação reidratação será priorizada em relação às solicitações padrão e poderá ser concluída em menos de uma hora para os objetos em tamanho de dez GB. 

> [!NOTE]
> A prioridade padrão é a opção reidratação padrão para arquivamento. Alta prioridade é uma opção mais rápida que custará mais do que a reidratação de prioridade padrão e geralmente é reservada para uso em situações de restauração de dados de emergência.
>
> A alta prioridade pode levar mais de 1 hora, dependendo do tamanho do blob e da demanda atual. As solicitações de alta prioridade têm garantia de serem priorizadas em relação às solicitações de prioridade padrão.

Depois que uma solicitação reidratação é iniciada, ela não pode ser cancelada. Durante o processo de reidratação, a propriedade de blob *x-MS-Access-Tier* continuará a ser mostrada como arquivo morto até que reidratação seja concluído para uma camada online. Para confirmar o status e o progresso do reidratação, você pode chamar [obter propriedades de blob](/rest/api/storageservices/get-blob-properties) para verificar as propriedades de blob *x-MS-Archive-status* e *x-MS-reidratar-Priority* . O status do arquivo pode ler "reidratar-Pending-to-Hot" ou "reidratar-Pending-to-cool", dependendo da camada de destino reidratar. A prioridade reidratar indicará a velocidade de "alta" ou "padrão". Após a conclusão, as propriedades status do arquivo morto e prioridade reidratar são removidas e a propriedade BLOB da camada de acesso será atualizada para refletir a camada quente ou fria selecionada.