---
title: incluir arquivo
description: incluir arquivo
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 6a37850eb6536c5399d63144e60ea210fbc194d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "77198404"
---
#### <a name="azure-storage-sdk-version-in-functions-1x"></a>Versão do SDK de Armazenamento do Microsoft Azure em Funções 1. x

Em Funções de 1. x, os gatilhos de armazenamento e associações usam a versão 7.2.1 do SDK de Armazenamento do Microsoft Azure ([windowsazure](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) pacote NuGet). Se você referenciar uma versão diferente do SDK do armazenamento e associar a um tipo de SDK de armazenamento na sua assinatura de função, o runtime de funções pode relatar se não é possível associar a esse tipo. A solução é verificar as referências do projeto [windowsazure 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
