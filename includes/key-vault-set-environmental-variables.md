---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6dfc96df3623e44a4ba513c238f21482c2a2989d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "87013073"
---
O método DefaultAzureCredential em nosso aplicativo depende de três variáveis ambientais: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` e `AZURE_TENANT_ID`. Defina essas variáveis para os valores clientId, clientSecret e tenantId que foram retornados na etapa "Criar uma entidade de serviço" usando o formato `export VARNAME=VALUE`. (Esse método define apenas as variáveis para o shell atual e os processos criados do shell; para adicionar permanentemente essas variáveis ao seu ambiente, edite seu arquivo `/etc/environment `.) 

Você também precisará salvar o nome do cofre de chaves como uma variável de ambiente chamada `KEY_VAULT_NAME`.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````
