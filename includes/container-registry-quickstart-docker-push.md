---
title: incluir arquivo
description: incluir arquivo
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/04/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6a16106495e584c9acbc02d380242df665f35ce5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019983"
---
## <a name="push-image-to-registry"></a>Efetuar push de imagem para registro

Para enviar por push uma imagem para um Registro de Contêiner do Azure, primeiro você deve ter uma imagem. Se você ainda não tiver as imagens de contêiner locais, execute o comando [docker pull][docker-pull] a seguir para efetuar pull de uma imagem existente do Docker Hub. Neste exemplo, extraia a imagem `hello-world`.

```
docker pull hello-world
```

Para efetuar push de uma imagem para o Registro, você precisa marcá-la com o nome totalmente qualificado de seu servidor de logon do Registro. O nome do servidor de logon está no formato *\<registry-name\>.azurecr.io* (tudo em letras minúsculas), por exemplo, *mycontainerregistry.azurecr.io*.

Marque a imagem usando o comando [docker tag][docker-tag]. Substitua o `<login-server>` pelo nome do servidor de logon da sua instância do ACR.

```
docker tag hello-world <login-server>/hello-world:v1
```

Exemplo:

```
docker tag hello-world mycontainerregistry.azurecr.io/hello-world:v1
```


Por fim, use [docker push][docker-push] para efetuar push da imagem para a instância do registro. Substitua o `<login-server>` pelo nome do servidor de logon de sua instância do registro. Este exemplo cria o repositório **Olá, mundo**, que contém a imagem `hello-world:v1`.

```
docker push <login-server>/hello-world:v1
```

Depois de efetuar push da imagem no registro de contêiner, remova a imagem `hello-world:v1` de seu ambiente do Docker local. (Observe que esse comando [docker rmi][docker-rmi] não remove a imagem do repositório **hello-world** no registro de contêiner do Azure.)

```
docker rmi <login-server>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

