---
title: Guia de Início Rápido – Implantar um contêiner do Docker na instância de contêiner – CLI do Docker
description: Neste guia de início rápido, você usará a CLI do Docker para implantar rapidamente um aplicativo Web em contêiner que é executado em uma instância de contêiner isolada do Azure
ms.topic: quickstart
ms.date: 09/14/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9904fb8c2142816196a1939a16445318bdb245d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91262307"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-docker-cli"></a>Início Rápido: Implantar uma instância de contêiner no Azure usando a CLI do Docker

Use as Instâncias de Contêiner do Azure para executar contêineres do Docker sem servidor no Azure de maneira simples e rápida. Implante em uma instância de contêiner sob demanda quando desenvolver aplicativos nativos de nuvem e desejar alternar diretamente do desenvolvimento local para a implantação na nuvem.

Neste guia de início rápido, você usará os comandos nativos da CLI do Docker para implantar um contêiner do Docker e disponibilizar seu aplicativo nas Instâncias de Contêiner do Azure. Essa funcionalidade é habilitada pela [integração entre o Docker e o Azure](https://docs.docker.com/engine/context/aci-integration/). Alguns segundos após a execução de um comando `docker run`, você poderá procurar o aplicativo em execução no contêiner:

:::image type="content" source="media/quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Aplicativos implantados por meio das Instâncias de Contêiner do Azure exibidos no navegador":::

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][azure-account] antes de começar.

Para este guia de início rápido, você precisará do Docker Desktop versão 2.3.0.5 ou posterior, disponível para o [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) ou o [macOS](https://desktop.docker.com/mac/edge/Docker.dmg). Ou instale a [CLI de Integração do Docker ACI para Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux). 

> [!IMPORTANT]
> Nem todos os recursos das Instâncias de Contêiner do Azure são compatíveis. Forneça comentários sobre a integração Docker-Azure criando um problema no repositório GitHub [aci-integration-beta](https://github.com/docker/aci-integration-beta).

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="create-a-container"></a>Criar um contêiner

Depois de criar um contexto do Docker, crie um contêiner no Azure. Neste início rápido, você usa a imagem pública `mcr.microsoft.com/azuredocs/aci-helloworld`. Esta imagem empacota um pequeno aplicativo Web escrito no Node.js que veicula a uma página HTML estática.

Primeiro, altere para o contexto da ACI. Todos os próximos comandos do Docker serão executados nesse contexto.

```
docker context use myacicontext
```

Execute o seguinte comando `docker run` para criar a instância de contêiner do Azure com a porta 80 exposta à Internet:

```
docker run -p 80:80 mcr.microsoft.com/azuredocs/aci-helloworld
```

Saída de exemplo de uma implantação bem-sucedida:

```
[+] Running 2/2
 ⠿ hungry-kirch            Created                                                                               5.1s
 ⠿ single--container--aci  Done                                                                                 11.3s
hungry-kirch
```

Execute `docker ps` para obter detalhes sobre o contêiner em execução, incluindo o endereço IP público:

```
docker ps
```


A saída de exemplo mostra um endereço IP público, neste caso, *52.230.225.232*:

```
CONTAINER ID        IMAGE                                        COMMAND             STATUS              PORTS
hungry-kirch        mcr.microsoft.com/azuredocs/aci-helloworld                       Running             52.230.225.232:80->80/tcp
```

 Agora, acesse o endereço IP no navegador. Se você encontrar uma página da Web semelhante à seguinte, parabéns! Você implantou com êxito um aplicativo em execução em um contêiner do Docker no Azure.

:::image type="content" source="media/quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Aplicativos implantados por meio das Instâncias de Contêiner do Azure exibidos no navegador":::

## <a name="pull-the-container-logs"></a>Acessar os logs de contêiner

Quando você precisar solucionar problemas de um contêiner ou do aplicativo que ele executa (ou apenas ver a saída dele), comece exibindo os logs da instância de contêiner.

Por exemplo, execute o comando `docker logs` para ver os logs do contêiner *hungry-kirch* no contexto da ACI:

```azurecli-interactive
docker logs hungry-kirch
```

A saída exibe os logs para o contêiner e deve mostrar as solicitações HTTP GET geradas quando o aplicativo é exibido no navegador.

```output
listening on port 80
::ffff:10.240.255.55 - - [07/Jul/2020:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```


## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar de usar o contêiner, execute `docker rm` para removê-lo. Esse comando interrompe e exclui a instância de contêiner do Azure.

```
docker rm hungry-kirch
```


## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma instância de contêiner do Azure com base em uma imagem pública usando a integração entre o Docker e o Azure. Saiba mais sobre os cenários de integração na [documentação do Docker](https://docs.docker.com/engine/context/aci-integration/). 

Use também a [extensão do Docker](https://aka.ms/VSCodeDocker) para Visual Studio Code para uma experiência integrada de desenvolvimento, execução e gerenciamento de contêineres, imagens e contextos.

Para usar as ferramentas do Azure para criar e gerenciar Instâncias de Contêiner, confira outros guias de início rápido que usam a [CLI do Azure](container-instances-quickstart.md), o [Azure PowerShell](container-instances-quickstart-powershell.md), o [portal do Azure](container-instances-quickstart-portal.md) e o [modelo do Azure Resource Manager](container-instances-quickstart-template.md).

Se você quer usar o Docker Compose para definir e executar um aplicativo de vários contêineres localmente e depois mudar para as Instâncias de Contêiner do Azure, prossiga para o tutorial.

> [!div class="nextstepaction"]
> [Tutorial do Docker Compose](./tutorial-docker-compose.md)

<!-- LINKS - External -->

[azure-account]: https://azure.microsoft.com/free/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

