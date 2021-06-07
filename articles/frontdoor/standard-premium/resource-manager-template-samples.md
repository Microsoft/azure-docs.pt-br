---
title: Exemplos de modelos do Resource Manager – Azure Front Door
description: Informações sobre modelos do Azure Resource Manager de exemplo fornecidos para o Azure Front Door.
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 03/24/2021
ms.openlocfilehash: 929adb0be948339af033d85b0dabd7e1cedf353e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561739"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Modelos do Azure Resource Manager para o Azure Front Door

> [!Note]
> Esta documentação é específica para o Azure Front Door Standard/Premium (Versão Prévia). Está procurando informações sobre o Azure Front Door? Veja [aqui](../front-door-overview.md).

A tabela a seguir incluirá links para acessar modelos do Azure Resource Manager para o Azure Front Door com arquiteturas de referência, incluindo outros serviços do Azure.

| Amostra | Descrição |
|-|-|
| [Conjunto de regras](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set/) | Cria um perfil e um conjunto de regras do Front Door.  |
|**Origens do Serviço de Aplicativo**| **Descrição** |
| [Serviço de Aplicativo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | Cria um aplicativo do Serviço de Aplicativo com um ponto de extremidade público e um perfil do Front Door.  |
| [Serviço de Aplicativo com um Link Privado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | Cria um aplicativo do Serviço de Aplicativo com um ponto de extremidade privado e um perfil do Front Door.  |
| [Ambiente do Serviço de Aplicativo com um Link Privado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-environment-internal-private-link) | Cria um Ambiente do Serviço de Aplicativo, um aplicativo com um ponto de extremidade privado e um perfil do Front Door.  |
|**Origens do Azure Functions**| **Descrição** |
| [Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-function-public/) | Cria um aplicativo do Azure Functions com um ponto de extremidade público e um perfil do Front Door.  |
| [Azure Functions com um Link Privado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-function-private-link) | Cria um aplicativo do Azure Functions com um ponto de extremidade privado e um perfil do Front Door.  |
|**Origens do Gerenciamento de API**| **Descrição** |
| [Gerenciamento de API (externo)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-api-management-external) | Cria uma instância do Gerenciamento de API com uma integração VNet externa e um perfil do Front Door.  |
|**Origens do Armazenamento**| **Descrição** |
| [Site estático de armazenamento](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-storage-static-website) | Cria uma conta de armazenamento do Azure e um site estático com um ponto de extremidade público e um perfil do Front Door.  |
| [Blobs de armazenamento com um Link Privado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-storage-blobs-private-link) | Cria uma Conta de Armazenamento do Azure e um contêiner de blob com um ponto de extremidade privado e um perfil do Front Door.  |
|**Origens do Gateway de Aplicativo**| **Descrição** |
| [Gateway de Aplicativo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-application-gateway-public) | Cria um Gateway de Aplicativo e um perfil do Front Door. |
|**Origens da máquina virtual**| **Descrição** |
| [Máquina virtual com o serviço de Link Privado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-vm-private-link) | Cria uma máquina virtual e um serviço de Link Privado, além de um perfil do Front Door. |
| | |
