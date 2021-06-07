---
title: Tutorial – implantar grupo com vários contêineres – YAML
description: Neste tutorial, saiba como implantar um grupo de contêiner com vários contêineres nas Instâncias de Contêiner do Azure usando um arquivo YAML com a CLI do Azure.
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: 74269440357ee2d7ae36661618a31293346fa712
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771255"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Tutorial: Implantar um grupo com vários contêineres usando um arquivo YAML

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

As Instâncias de Contêiner do Azure são compatíveis com a implantação de vários contêineres em um único host utilizando um [grupo de contêineres](container-instances-container-groups.md). Um grupo de contêineres é útil ao criar um aplicativo secundário para registro em log, monitoramento ou qualquer outra configuração em que um serviço precise de um segundo processo anexado.

Neste tutorial, você seguirá as etapas para executar uma configuração simples secundária de dois contêineres implantando um [arquivo YAML](container-instances-reference-yaml.md) usando a CLI do Azure. Um arquivo YAML fornece um formato conciso para especificar as configurações de instância. Você aprenderá como:

> [!div class="checklist"]
> * Configurar um arquivo YAML
> * Implantar o grupo de contêineres
> * Exibir os logs de contêineres

> [!NOTE]
> Grupos com vários contêineres são atualmente restritos a contêineres do Linux.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="configure-a-yaml-file"></a>Configurar um arquivo YAML

Para implantar um grupo com vários contêineres com o comando [az container create][az-container-create] na CLI do Azure, você precisa especificar a configuração do grupo de contêineres em um arquivo YAML. Em seguida, passe o arquivo YAML como um parâmetro para o comando.

Comece copiando o YAML a seguir em um novo arquivo chamado **deploy-aci.yaml**. No Azure Cloud Shell, você pode usar o Visual Studio Code para criar o arquivo em seu diretório de trabalho:

```
code deploy-aci.yaml
```

Esse arquivo YAML define um grupo de contêineres nomeado "myContainerGroup" com dois contêineres, um endereço IP público e duas portas expostas. Os contêineres são implantados de imagens públicas da Microsoft. O primeiro contêiner no grupo executa um aplicativo Web voltado para a Internet. O segundo contêiner, o secundário, faz periodicamente solicitações HTTP para o aplicativo Web em execução no primeiro contêiner por meio da rede local do grupo de contêiner.

```YAML
apiVersion: 2019-12-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 80
    - protocol: tcp
      port: 8080
tags: {exampleTag: tutorial}
type: Microsoft.ContainerInstance/containerGroups
```

Para usar um Registro de imagem de contêiner privado, adicione a propriedade `imageRegistryCredentials` ao grupo de contêineres com valores modificados para seu ambiente:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>Implantar o grupo de contêineres

Crie um grupo de recursos com o comando [az group create][az-group-create]:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implante o grupo de contêineres com o comando [az container create][az-container-create], passando o arquivo YAML como argumento:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

Em alguns segundos, você deverá receber uma resposta inicial do Azure.

## <a name="view-deployment-state"></a>Exibir estado da implantação

Para exibir o estado da implantação, use o seguinte comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Se quiser exibir o aplicativo em execução, navegue até o endereço IP dele em seu navegador. Por exemplo, o IP é `52.168.26.124` nesta saída de exemplo:

```console
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Exibir logs do contêiner

Exiba a saída de log de um contêiner usando o comando [az container logs][az-container-logs]. O argumento `--container-name` especifica o contêiner do qual efetuar pull dos logs. Neste exemplo, o contêiner `aci-tutorial-app` é especificado.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Saída:

```console
listening on port 80
::1 - - [02/Jul/2020:23:17:48 +0000] "HEAD / HTTP/1.1&quot; 200 1663 &quot;-&quot; &quot;curl/7.54.0"
::1 - - [02/Jul/2020:23:17:51 +0000] "HEAD / HTTP/1.1&quot; 200 1663 &quot;-&quot; &quot;curl/7.54.0"
::1 - - [02/Jul/2020:23:17:54 +0000] "HEAD / HTTP/1.1&quot; 200 1663 &quot;-&quot; &quot;curl/7.54.0"
```

Para ver os logs do contêiner secundário, execute um comando similar, especificando o contêiner `aci-tutorial-sidecar`.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Saída:

```console
Every 3s: curl -I http://localhost                          2020-07-02 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 02 Jul 2020 20:36:41 GMT
Connection: keep-alive
```

Como você pode ver, o secundário está periodicamente fazendo uma solicitação HTTP ao aplicativo Web principal por meio da rede local do grupo a fim de garantir que ele esteja em execução. Este exemplo secundário pode ser expandido para disparar um alerta, caso receba um código de resposta HTTP diferente de `200 OK`.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou um arquivo YAML para implantar um grupo de vários contêineres em Instâncias de Contêiner do Azure. Você aprendeu a:

> [!div class="checklist"]
> * Configurar um arquivo YAML para um grupo com vários contêineres
> * Implantar o grupo de contêineres
> * Exibir os logs de contêineres

Você também pode especificar um grupo com vários contêineres usando um [modelo do Resource Manager](container-instances-multi-container-group.md). Um modelo do Resource Manager pode ser adaptado prontamente para cenários quando você precisar implantar recursos de serviço do Azure adicionais com o grupo de contêineres.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az_container_create
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups
