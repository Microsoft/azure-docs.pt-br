---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
manager: cfowler
ms.service: azure-functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: c2fff707dcaafac69efcad3dbf33446a7b797396
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608077"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Versão do SDK de Armazenamento do Microsoft Azure em Funções 1. x

Em Funções de 1. x, os gatilhos de armazenamento e associações usam a versão 7.2.1 do SDK de Armazenamento do Microsoft Azure ([windowsazure](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) pacote NuGet). Se você referenciar uma versão diferente do SDK do armazenamento e associar a um tipo de SDK de armazenamento na sua assinatura de função, o tempo de execução de funções pode relatar se não é possível associar a esse tipo. A solução é verificar as referências do projeto [windowsazure 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
