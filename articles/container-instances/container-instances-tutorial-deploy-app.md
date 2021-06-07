---
title: Tutorial – Implantar aplicativo contêiner na instância de contêiner
description: Tutorial das Instâncias de Contêiner do Azure, parte 3 de 3 – Implantar um aplicativo de contêiner nas Instâncias de Contêiner do Azure
ms.topic: tutorial
ms.date: 03/21/2018
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 2d12b0bc91cf844001b770c8a7e534ad6b1b9b2d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763875"
---
# <a name="tutorial-deploy-a-container-application-to-azure-container-instances"></a>Tutorial: Implantar um aplicativo de contêiner nas Instâncias de Contêiner do Azure

Este é o tutorial final de uma série de três partes. Anteriormente na série, [uma imagem de contêiner foi criada](container-instances-tutorial-prepare-app.md) e [enviada por push ao Registro de Contêiner do Azure](container-instances-tutorial-prepare-acr.md). Este artigo conclui a série implantando o contêiner em Instâncias de Contêiner do Azure.

Neste tutorial, você:

> [!div class="checklist"]
> * Implantará o contêiner do Registro de Contêiner do Azure nas Instâncias de Contêiner do Azure
> * Verá o aplicativo em execução no navegador
> * Exibirá os logs do contêiner

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="deploy-the-container-using-the-azure-cli"></a>Implantar o contêiner usando a CLI do Azure

Nesta seção, você usará a CLI do Azure para implantar a imagem criada no [primeiro tutorial](container-instances-tutorial-prepare-app.md) e enviada por push para o Registro de Contêiner do Azure no [segundo tutorial](container-instances-tutorial-prepare-acr.md). Conclua esses dois tutoriais antes de continuar.

### <a name="get-registry-credentials"></a>Obter credenciais de registro

Ao implantar uma imagem hospedada em um registro de contêiner privado do Azure, como aquele criado no [segundo tutorial](container-instances-tutorial-prepare-acr.md), você precisará fornecer credenciais para acessar o registro. 

Uma melhor prática para muitos cenários é criar e configurar uma entidade de serviço do Azure Active Directory com permissões de *pull* no registro. Consulte [Autenticar com o Registro de Contêiner do Azure de Instâncias de Contêiner do Azure](../container-registry/container-registry-auth-aci.md) para obter os scripts de exemplo para criar uma entidade de serviço com as permissões necessárias. Anote a *ID e a senha da entidade de serviço* e a *senha da entidade de serviço*. Use essas credenciais para acessar o registro quando implantar o contêiner.

Você também precisará do nome completo do servidor de logon do registro de contêiner (substitua `<acrName>` pelo nome do registro):

```azurecli
az acr show --name <acrName> --query loginServer
```

### <a name="deploy-container"></a>Implantar o contêiner

Agora, use o comando [az container create][az-container-create] para implantar o contêiner. Substitua `<acrLoginServer>` pelo valor obtido no comando anterior. Substitua `<service-principal-ID>` e `<service-principal-password>` pela ID e pela senha da entidade de serviço que você criou para acessar o registro. Substitua `<aciDnsLabel>` por um nome DNS desejado.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-login-server <acrLoginServer> --registry-username <service-principal-ID> --registry-password <service-principal-password> --dns-name-label <aciDnsLabel> --ports 80
```

Em alguns segundos, você deverá receber uma resposta inicial do Azure. O valor `--dns-name-label` deve ser exclusivo dentro da região do Azure em que você criar a instância do contêiner. Modifique o valor no comando anterior caso receba uma mensagem de erro de **rótulo do nome DNS** ao executar o comando.

### <a name="verify-deployment-progress"></a>Verificar o progresso da implantação

Para exibir o estado da implantação, use o comando [az container show][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

Repita o comando [az container show][az-container-show] até que o estado mude de *Pendente* para *Executando*, o que deve levar menos de um minuto. Quando o contêiner estiver *Executando*, vá para a próxima etapa.

## <a name="view-the-application-and-container-logs"></a>Exibir os logs do aplicativo e do contêiner

Depois que a implantação for bem-sucedida, exiba o FQDN (nome de domínio totalmente qualificado) do contêiner com o comando [az container show][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

Por exemplo:
```output
"aci-demo.eastus.azurecontainer.io"
```

Para ver o aplicativo em execução, navegue até o nome DNS exibido em seu navegador favorito:

![Aplicativo Olá, Mundo no navegador][aci-app-browser]

Você também pode exibir a saída do log do contêiner:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Saída de exemplo:

```output
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1&quot; 200 1663 &quot;-&quot; &quot;Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Limpar recursos

Se não precisar mais de nenhum dos recursos criados nessa série de tutoriais, você poderá executar o comando [az group delete][az-group-delete] para remover o grupo de recursos e todos os recursos que ele contém. Esse comando exclui o Registro de contêiner que você criou, bem como o contêiner em execução e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você concluiu o processo de implantação do seu contêiner nas Instâncias de Contêiner do Azure. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Implantar o contêiner do Registro de Contêiner do Azure usando a CLI do Azure
> * Exibir o aplicativo no navegador
> * Exibir os logs de contêiner

Agora que você tem as noções básicas, siga em frente para saber mais sobre Instâncias de Contêiner do Azure, por exemplo, como funcionam os grupos de contêiner:

> [!div class="nextstepaction"]
> [Grupos de contêineres em Instâncias de Contêiner do Azure](container-instances-container-groups.md)

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
