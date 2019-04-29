---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
origin.date: 05/17/2018
ms.date: 05/30/2018
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: f8733ef907b8f31ace7ea72f705ba1b37d1adece
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306709"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Versão do SDK de Armazenamento do Microsoft Azure em Funções 1. x

Em Funções de 1. x, os gatilhos de armazenamento e associações usam a versão 7.2.1 do SDK de Armazenamento do Microsoft Azure ([windowsazure](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) pacote NuGet). Se você referenciar uma versão diferente do SDK do armazenamento e associar a um tipo de SDK de armazenamento na sua assinatura de função, o tempo de execução de funções pode relatar se não é possível associar a esse tipo. A solução é verificar as referências do projeto [windowsazure 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).

<!-- ms.date: 05/30/2018 -->