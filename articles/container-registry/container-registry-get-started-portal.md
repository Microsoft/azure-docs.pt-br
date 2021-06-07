---
title: Início rápido – Criar registro no portal
description: Aprenda rapidamente a criar um registro de contêiner do Azure privado usando o portal do Azure.
ms.date: 08/04/2020
ms.topic: quickstart
ms.custom:
- mvc
- mode-portal
ms.openlocfilehash: 4618a83a43d9c27f44432d5d346fdb053cee7054
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773577"
---
# <a name="quickstart-create-an-azure-container-registry-using-the-azure-portal"></a>Início Rápido: Criar um Registro de Contêiner do Azure usando o portal do Azure

Um registro de contêiner do Azure é um registro particular do Docker no Azure no qual você pode armazenar e gerenciar imagens de contêiner particulares do Docker e artefatos relacionados. Neste guia de início rápido, você criará um registro de contêiner com o Portal do Azure. Em seguida, use os comandos do Docker para efetuar push de uma imagem de contêiner no Registro e, por fim, efetuar pull e executar a imagem do seu Registro.

Para fazer logon no Registro para trabalhar com imagens de contêiner, este início rápido exige que você esteja executando a CLI do Azure (versão 2.0.55 ou posterior recomendada). Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli].

Você também deve ter o Docker instalado localmente. O Docker fornece pacotes que configuram facilmente o Docker em qualquer sistema [Mac][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner**.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="Navegar até o registro de contêiner no portal":::

Na guia **Noções básicas**, insira valores para **Grupo de recursos** e **Nome do registro**. O nome do registro deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. Para este início rápido crie um novo grupo de recursos no local `West US` denominado `myResourceGroup` e para **SKU**, selecione “Basic”.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="Criar o registro de contêiner no portal":::

Aceite os valores padrão para as configurações restantes. Em seguida, selecione **Examinar + criar**. Depois de revisar as configurações, selecione **Criar**.

Neste início rápido você criará um Registro *Básico*, que é uma opção com otimização de custo para desenvolvedores que estão aprendendo sobre o Registro de Contêiner do Azure. Para obter detalhes sobre as camadas de serviço (SKUs) disponíveis, confira [Camadas de serviço do registro de contêiner][container-registry-skus].

Quando a mensagem **Implantação bem-sucedida** é exibida, selecione o registro de contêiner no portal. 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="Visão geral do registro de contêiner no portal":::

Anote o nome do registro e o valor do **servidor de logon**. Você usará esses valores nas etapas a seguir ao enviar por push e efetuar pull de imagens com o Docker.

## <a name="log-in-to-registry"></a>Fazer logon no registro

Antes de efetuar push e pull das imagens de contêiner, você precisa fazer logon na instância do registro. [Entre na CLI do Azure][get-started-with-azure-cli] no computador local e, em seguida, execute o comando [az acr login][az-acr-login]. Especifique somente o nome do Registro ao fazer logon com a CLI do Azure. Não use o nome do servidor de logon, que inclui um sufixo de domínio como `azurecr.io`.

```azurecli
az acr login --name <registry-name>
```

Exemplo:

```azurecli
az acr login --name mycontainerregistry
```

O comando retorna `Login Succeeded` na conclusão. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Listar imagens de contêiner

Para listar as imagens no registro, navegue até ele no portal, selecione **Repositórios** e selecione o repositório **hello-world** que você criou com `docker push`.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="Listar imagens de contêiner no portal":::

Ao selecionar o repositório **hello-world**, você verá a imagem marcada com `v1` em **Marcas**.

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar seus recursos, navegue até o grupo de recursos **myResourceGroup** no portal. Depois que o grupo de recursos for carregado, clique em **Excluir grupo de recursos** para remover o grupo de recursos, o registro de contêiner e as imagens armazenadas lá.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="Excluir grupo de recursos no portal":::


## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Registro de Contêiner do Azure com o portal do Azure, efetuou push de uma imagem de contêiner e extraiu e executou a imagem do Registro. Prossiga para os tutoriais de Registro de Contêiner do Azure para uma análise mais profunda do ACR.

> [!div class="nextstepaction"]
> [Tutoriais de Registro de Contêiner do Azure][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Tutoriais das Tarefas do Registro de Contêiner do Azure][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[get-started-with-azure-cli]: /cli/azure/get-started-with-azure-cli
[az-acr-login]: /cli/azure/acr#az_acr_login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
