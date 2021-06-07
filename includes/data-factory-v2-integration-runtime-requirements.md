---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: fbde8bc28f8fc34b7a6a6443950b8733c6dcff45
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96023154"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Se o armazenamento de dados estiver localizado dentro de uma rede local, em uma rede virtual do Azure ou na Amazon Virtual Private Cloud, você precisará configurar um [runtime de integração auto-hospedada](../articles/data-factory/create-self-hosted-integration-runtime.md) para se conectar a ele.

Por outro lado, se o armazenamento de dados for um serviço de dados de nuvem gerenciado, você poderá usar o Azure Integration Runtime. Se o acesso for restrito aos IPs que estão aprovados nas regras de firewall, você poderá adicionar [IPs do Azure Integration Runtime](../articles/data-factory/azure-integration-runtime-ip-addresses.md) à lista de permissões. 

Para obter mais informações sobre os mecanismos de segurança de rede e as opções compatíveis com o Data Factory, consulte [Estratégias de acesso a dados](../articles/data-factory/data-access-strategies.md).
