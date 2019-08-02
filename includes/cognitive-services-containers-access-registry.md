---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704258"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Use a CLI do Docker para autenticar o registro de contêiner privado

É possível autenticar com o registro de contêiner privado para Contêineres de Serviços Cognitivos, mas o método recomendado por meio da linha de comando é usando a [CLI do Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Use o [comando `docker login`](https://docs.docker.com/engine/reference/commandline/login/), conforme mostrado no exemplo a seguir, para fazer logon no `containerpreview.azurecr.io`, o registro de contêiner privado para Contêineres de Serviços Cognitivos. Substitua *\<nome de usuário\>* pelo nome de usuário e *\<senha\>* com a senha fornecida nas credenciais recebidas da equipe do Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se você tiver protegido suas credenciais em um arquivo de texto, poderá concatenar o conteúdo desse arquivo de texto, usando o comando `cat` para o comando `docker login`, conforme mostrado no exemplo a seguir. Substitua *\<passwordFile\>* pelo caminho e nome do arquivo de texto que contém a senha e o *\<nome de usuário\>* com o nome de usuário fornecido em suas credenciais.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
