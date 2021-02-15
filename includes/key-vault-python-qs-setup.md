---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9440a33858cb70389e86621ae76be9c08ec56d61
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "89482107"
---
1. Tenha uma [conta do Azure com uma assinatura ativa](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

1. Instale o [Python 2.7+ ou 3.5.3+](https://www.python.org/downloads).

1. Instale a [CLI do Azure](/cli/azure/install-azure-cli).

1. Siga as instruções em [Configurar a autenticação para desenvolvimento local](/azure/developer/python/configure-local-development-environment?tabs=cmd#configure-authentication), com a qual você cria uma entidade de serviço local e a disponibiliza para o cliente do Azure Key Vault para Python por meio de variáveis de ambiente. 

    Ao executar o código diretamente no Azure, uma entidade de serviço separada não será necessária se o aplicativo usar a identidade gerenciada.

1. Em um terminal ou prompt de comando, crie uma pasta de projeto adequada e depois crie e ative um ambiente virtual do Python conforme descrito em [Usar ambientes virtuais do Python](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)

1. Instale a biblioteca de identidades do Azure Active Directory:

    ```terminal
    pip install azure.identity
    ```
