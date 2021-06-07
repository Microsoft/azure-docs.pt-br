---
title: Armazenando e usando os segredos do aplicativo de malha de Service Fabric do Azure
description: A Malha do Service Fabric dá suporte aos Segredos como recursos do Azure. Veja como armazenar e gerenciar segredos com seus aplicativos de Service Fabric malha.
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 85b4bb6ee4d76a7115c9ebdd1466049afe6683c6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625525"
---
# <a name="service-fabric-mesh-application-secrets"></a>Segredos do aplicativo de Malha do Fabric Mesh

> [!IMPORTANT]
> A versão prévia da Malha do Azure Service Fabric foi desativada. Não serão mais permitidas novas implantações por meio da API da Malha do Service Fabric. O suporte para as implantações existentes continuará até 28 de abril de 2021.
> 
> Para obter detalhes, confira [Desativação da versão prévia da Malha do Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

A Malha do Service Fabric dá suporte aos Segredos como recursos do Azure. Um segredo da Malha do Service Fabric pode ser qualquer informação de texto confidencial como cadeias de conexão de armazenamento, senhas ou outros valores que devem ser armazenados e transmitidos de forma segura.

![Visão geral dos Segredos de Malha][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Recursos dos segredos de Malha
Um Segredo de aplicativo de Malha consiste em:
* Um recurso **Segredos**, que é um contêiner que armazena os segredos de texto. Os segredos contidos no recurso **Segredos** são armazenados e transmitidos de forma segura.
* Um ou mais recursos **Segredos/Valores** que são armazenados no contêiner do recurso **Segredos**. Cada recurso **Segredos/Valores** é diferenciado por um número de versão.

## <a name="next-steps"></a>Próximas etapas 
Para saber mais sobre os Segredos de Malha do Service Fabric, confira:
- [Gerenciar segredos do aplicativo de Malha do Azure Service Fabric](service-fabric-mesh-howto-manage-secrets.md)
- [Introdução ao Modelo de Recurso do Service Fabric](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
