---
title: Armazenar e usar segredos do aplicativo do Azure Service Fabric Mesh | Microsoft Docs
description: Armazenar e usar segredos de Malha do Service Fabric.
services: service-fabric-mesh
keywords: segredos
author: v-steg
ms.author: v-steg
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 4268356db5f46e92862e19d6391cfe5a94511270
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810635"
---
# <a name="service-fabric-mesh-application-secrets"></a>Segredos do aplicativo de Malha do Fabric Mesh
A Malha do Service Fabric dá suporte a Segredos como recursos do Azure. Um segredo de Malha do Service Fabric pode ser qualquer informação de texto confidenciais como cadeias de caracteres de conexão de armazenamento, senhas ou outros valores que devem ser armazenados e transmitidos de forma segura.

![Visão geral dos Segredos de Malha][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Recursos dos segredos de Malha
Um segredo de aplicativo de Malha consiste em:
* Um recurso **Segredos**, que é um contêiner que armazena os segredos de texto. Os segredos contidos no recurso **Segredos** são armazenados e transmitidos de forma segura.
* Um ou mais recursos **Segredos/Valores** que são armazenados no contêiner do recurso **Segredos**. Cada recurso **Segredos/Valores** é diferenciado por um número de versão.

## <a name="next-steps"></a>Próximas etapas 
Para saber mais sobre os Segredos de Malha do Service Fabric, confira:
- [Gerenciar Segredos do Aplicativo do Service Fabric Mesh](service-fabric-mesh-howto-manage-secrets.md)
- [Introdução ao Modelo de Recurso do Service Fabric](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
