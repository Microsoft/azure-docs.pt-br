---
title: "Guia de início rápido – criar um registro particular do Docker no Azure com o Portal do Azure"
description: "Aprenda rapidamente a criar um registro de contêiner particular do Docker com o Portal do Azure."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 033ef0cdd607ced5de6c975e071e0ce37e677201
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-container-registry-using-the-azure-portal"></a>Criar um registro de contêiner usando o portal do Azure

Um Registro de Contêiner do Azure é um registro particular do Docker no Azure em que você pode armazenar e gerenciar suas imagens de contêiner particulares do Docker. Neste guia de início rápido, você criará um Registro de Contêiner com o Portal do Azure.

Para concluir este guia de início rápido, você deve ter o Docker instalado localmente. O Docker fornece pacotes que configuram facilmente o Docker em qualquer sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em https://portal.azure.com.

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Selecione **Novo** > **Contêineres** > **Registro de Contêiner do Azure**.

![Criar um registro de contêiner no Portal do Azure][qs-portal-01]

Insira os valores para **Nome do registro** e **Grupo de recursos**. O nome do registro deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. Crie um novo grupo de recursos denominado `myResourceGroup` e para **SKU**, selecione "Clássico". Selecione **Criar** para implantar a instância do ACR.

![Criar um registro de contêiner no Portal do Azure][qs-portal-03]

O Registro de Contêiner do Azure está disponível em vários SKUs: `Classic`, `Basic`, `Standard` e `Premium`. Embora `Basic`, `Standard` e `Premium` forneçam recursos avançados como armazenamento gerenciado e Webhooks, eles estão atualmente em versão prévia e não estão disponíveis em algumas regiões do Azure. Selecionamos o SKU `Classic` neste guia de início rápido devido a sua disponibilidade em todas as regiões.

Quando a mensagem **Implantação com êxito** for exibida, selecione o registro de contêiner no portal e, em seguida, selecione as **Chaves de acesso**.

![Criar um registro de contêiner no Portal do Azure][qs-portal-05]

Em **Usuário administrador**, selecione **Habilitar**. Anote os seguintes valores:

* Servidor de logon
* Nome de Usuário
* Senha

Você usará esses valores nas etapas seguintes ao trabalhar com o registro na CLI do Docker.

![Criar um registro de contêiner no Portal do Azure][qs-portal-06]

## <a name="log-in-to-acr"></a>Fazer logon no ACR

Antes de enviar por push e pull imagens de contêiner, você deverá fazer logon na instância ACR. Para fazer isso, use o comando [docker login](https://docs.docker.com/engine/reference/commandline/login/). Substitua os valores *nome de usuário*, *senha* e *servidor de logon* por aqueles que você anotou na etapa anterior.

```
docker login --username <username> --password <password> <login server>
```

O comando retornará "Êxito no logon" quando concluído.

## <a name="push-image-to-acr"></a>Enviar imagem por push para o ACR

Para enviar por push uma imagem para o Registro de Contêiner do Azure, primeiro você deve ter uma imagem. Se necessário, execute o seguinte comando para efetuar pull de uma imagem existente do Hub do Docker.

```bash
docker pull microsoft/aci-helloworld
```

Antes de enviar a imagem por push para o registro, você deve marcar a imagem com o nome do servidor de logon do ACR. Marque a imagem usando o comando [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). Substitua o *servidor de logon* pelo nome do servidor de logon que você anotou anteriormente.

```
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

Por fim, use [docker push](https://docs.docker.com/engine/reference/commandline/push/) para enviar a imagem por push para a instância do ACR. Substitua o *servidor de logon* pelo nome do servidor de logon da sua instância do ACR.

```
docker push <login server>/aci-helloworld:v1
```

A saída de um comando `docker push` bem sucedido é semelhante a:

```
The push refers to a repository [uniqueregistryname.azurecr.io/aci-helloworld]
7c701b1aeecd: Pushed
c4332f071aa2: Pushed
0607e25cc175: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:f2867748615cc327d31c68b1172cc03c0544432717c4d2ba2c1c2d34b18c62ba size: 1577
```

## <a name="list-container-images"></a>Listar imagens de contêiner

Para listar as imagens na sua instância do ACR, navegue até o seu registro no portal e selecione **Repositórios** e, em seguida, selecione o repositório que você criou com `docker push`.

Neste exemplo, selecionamos o repositório **aci-helloworld** e nós podemos ver a imagem marcada com `v1` em **TAGS**.

![Criar um registro de contêiner no Portal do Azure][qs-portal-09]

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos **myResourceGroup**. Isso excluirá o grupo de recursos, a instância do ACR e todas as imagens de contêiner.

![Criar um registro de contêiner no Portal do Azure][qs-portal-08]

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você ciou um Registro de Contêiner do Azure com a CLI do Azure. Se você quiser usar o Registro de Contêiner do Azure com Instâncias de Contêiner do Azure, prossiga para o tutorial de Instâncias de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Tutoriais sobre Instâncias de Contêiner do Azure](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-04]: ./media/container-registry-get-started-portal/qs-portal-04.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-06]: ./media/container-registry-get-started-portal/qs-portal-06.png
[qs-portal-07]: ./media/container-registry-get-started-portal/qs-portal-07.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png