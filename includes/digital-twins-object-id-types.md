---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/20/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 40ab53c941a7ac619ebb09d381a4ae0450f26e8b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172555"
---
O `objectIdType` (ou **tipo de identificador de objeto**) refere-se ao tipo de identidade que é dada a uma função. Além dos tipos `DeviceId` e `UserDefinedFunctionId`, os tipos de identificador de objeto correspondem às propriedades dos objetos do Azure Active Directory.

A tabela a seguir contém os tipos de identificador de objeto com suporte no Gêmeos Digitais do Azure:

| Tipo | DESCRIÇÃO |
| --- | --- |
| UserId | Atribui uma função a um usuário. |
| deviceId | Atribui uma função a um dispositivo. |
| DomainName | Atribui uma função a um nome de domínio. Cada usuário com o nome de domínio especificado possui os direitos de acesso da função correspondente. |
| TenantId | Atribui uma função a um locatário. Cada usuário que pertence ao ID de locatário do Microsoft Azure Active Directory especificado tem os direitos de acesso da função correspondente. |
| ServicePrincipalId | Atribui uma função a uma ID de objeto de entidade de serviço. |
| UserDefinedFunctionId | Atribui uma função a uma UDF (função definida pelo usuário). |