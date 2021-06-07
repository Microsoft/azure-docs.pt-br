---
title: Gerenciar conteúdo público no registro de contêiner privado
description: Práticas e fluxos de trabalho no registro de contêiner do Azure para gerenciar dependências em imagens públicas do Hub do Docker e de outros conteúdos públicos
author: dlepow
ms.topic: article
ms.author: danlep
ms.date: 11/20/2020
ms.openlocfilehash: 0c92899528d417f9c91f8f8930ca4932dc74e850
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95024731"
---
# <a name="manage-public-content-with-azure-container-registry"></a>Gerenciar conteúdo público com o registro de contêiner do Azure

Este artigo é uma visão geral de práticas e fluxos de trabalho para usar um registro local, como um [registro de contêiner do Azure](container-registry-intro.md) para manter cópias de conteúdo público, como imagens de contêiner no Hub do Docker. 


## <a name="risks-with-public-content"></a>Riscos com conteúdo público

Seu ambiente pode ter dependências de conteúdo público, como imagens de contêiner público, [gráficos de Helm](https://helm.sh/), políticas de Opa ( [Open Policy Agent](https://www.openpolicyagent.org/) ) ou outros artefatos. Por exemplo, você pode executar [Nginx](https://hub.docker.com/_/nginx) para roteamento de serviço ou `docker build FROM alpine` puxando imagens diretamente do Hub do Docker ou outro registro público. 

Sem os controles adequados, ter dependências do conteúdo do registro público pode introduzir riscos aos fluxos de trabalho de desenvolvimento e implantação de imagens. Para atenuar os riscos, mantenha cópias locais do conteúdo público quando possível. Para obter detalhes, consulte o [blog Open container Initiative](https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/). 

## <a name="authenticate-with-docker-hub"></a>Autenticar com o Hub do Docker

Como uma primeira etapa, se você efetuar pull de imagens públicas do Hub do Docker como parte de um fluxo de trabalho de compilação ou implantação, recomendamos que você [autentique usando uma conta de Hub do Docker](https://docs.docker.com/docker-hub/download-rate-limit/#how-do-i-authenticate-pull-requests) em vez de fazer uma solicitação de pull anônima.

Ao fazer solicitações de pull anônimas frequentes, você pode ver erros de Docker semelhantes `ERROR: toomanyrequests: Too Many Requests.` ou `You have reached your pull rate limit.` autenticados no Hub do Docker para evitar esses erros.

> [!NOTE]
> A partir de 2 de novembro de 2020, [os limites de taxa de download](https://docs.docker.com/docker-hub/download-rate-limit) se aplicam a solicitações anônimas e autenticadas ao Hub do Docker de contas de plano livre do Docker e são impostos pelo endereço IP e pela ID do Docker, respectivamente. 
>
> Ao estimar o número de solicitações pull, leve em conta que ao usar os serviços do provedor de nuvem ou trabalhando atrás de um NAT corporativo, vários usuários serão apresentados ao Hub do Docker em agregação como um subconjunto de endereços IP. Adicionar a autenticação de conta paga do Docker às solicitações feitas ao Hub do Docker evitará possíveis interrupções de serviço devido à limitação de limite de taxa.
>
> Para obter detalhes, consulte [preços e assinaturas do Docker](https://www.docker.com/pricing) e os [termos de serviço do Docker](https://www.docker.com/legal/docker-terms-service).

### <a name="docker-hub-access-token"></a>Token de acesso do Hub do Docker

O Hub do Docker dá suporte a [tokens de acesso pessoal](https://docs.docker.com/docker-hub/access-tokens/) como alternativas para uma senha do Docker ao autenticar no Hub do Docker. Tokens são recomendados para serviços automatizados que recebem imagens do Hub do Docker. Você pode gerar vários tokens para diferentes usuários ou serviços e revogar tokens quando não for mais necessário.

Para autenticar com o `docker login` usando um token, omita a senha na linha de comando. Quando uma senha for solicitada, insira o token em vez disso. Se você tiver habilitado a autenticação de dois fatores para sua conta do Hub do Docker, deverá usar um token de acesso pessoal ao fazer logon na CLI do Docker.

### <a name="authenticate-from-azure-services"></a>Autenticar de serviços do Azure

Vários serviços do Azure, incluindo o serviço de aplicativo e as instâncias de contêiner do Azure, dão suporte ao pull de imagens de registros públicos, como o Hub do Docker para implantações de contêiner Se você precisar implantar uma imagem do Hub do Docker, recomendamos que você defina as configurações para autenticar usando uma conta do Hub do Docker. Exemplos:

**Serviço de Aplicativo**

* **Origem da imagem**: Hub do Docker
* **Acesso ao repositório**: privado
* **Logon**: \<Docker Hub username>
* **Senha**: \<Docker Hub token>

Para obter detalhes, consulte [pull autenticado do Hub do Docker no serviço de aplicativo](https://azure.github.io/AppService/2020/10/15/Docker-Hub-authenticated-pulls-on-App-Service.html).

**Instâncias de Contêiner do Azure**

* **Origem da imagem**: Hub do Docker ou outro registro
* **Tipo de imagem**: privada
* **Servidor de logon do registro de imagem**: Docker.Io
* **Nome de usuário do registro de imagem**: \<Docker Hub username>
* **Senha do registro de imagem**: \<Docker Hub token>
* **Imagem**: Docker.Io/ \<repo name\> :\<tag>

## <a name="import-images-to-an-azure-container-registry"></a>Importar imagens para um registro de contêiner do Azure
 
Para começar a gerenciar cópias de imagens públicas, você poderá criar um registro de contêiner do Azure se ainda não tiver um. Crie um registro usando o [CLI do Azure](container-registry-get-started-azure-cli.md), [portal do Azure](container-registry-get-started-portal.md), [Azure PowerShell](container-registry-get-started-powershell.md)ou outras ferramentas. 

Como uma etapa única recomendada, [importe](container-registry-import-images.md) imagens base e outros conteúdos públicos para o registro de contêiner do Azure. O comando [AZ ACR Import](/cli/azure/acr#az_acr_import) no CLI do Azure dá suporte à importação de imagem de registros públicos, como o Hub do Docker e o registro de contêiner da Microsoft e de outros registros de contêineres privados. 

`az acr import` Não requer uma instalação local do Docker. Você pode executá-lo com uma instalação local do CLI do Azure ou diretamente no Azure Cloud Shell. Ele dá suporte a imagens de qualquer tipo de sistema operacional, imagens de várias arquiteturas ou artefatos de OCI, como gráficos Helm.

Exemplo:

```azurecli-interactive
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest \
  --username <Docker Hub username> \
  --password <Docker Hub token>
```

Dependendo das necessidades da sua organização, você pode importar para um registro dedicado ou um repositório em um registro compartilhado.

## <a name="automate-application-image-updates"></a>Automatizar atualizações de imagem de aplicativo

Os desenvolvedores de imagens de aplicativos devem garantir que seu código faça referência ao conteúdo local sob seu controle. Por exemplo, uma `Docker FROM` instrução em um Dockerfile deve fazer referência a uma imagem em um registro de imagem de base particular em vez de um registro público. 

Expandindo a importação de imagem, configure uma [tarefa de registro de contêiner do Azure](container-registry-tasks-overview.md) para automatizar compilações de imagem de aplicativo quando as imagens base são atualizadas. Uma tarefa de compilação automatizada pode controlar as atualizações de [imagem base](container-registry-tasks-base-images.md) e [as atualizações de código-fonte](container-registry-tasks-overview.md#trigger-task-on-source-code-update).

Para obter um exemplo detalhado, consulte [como consumir e manter conteúdo público com tarefas de registro de contêiner do Azure](tasks-consume-public-content.md). 

> [!NOTE]
> Uma única tarefa pré-configurada pode recriar automaticamente todas as imagens do aplicativo que fazem referência a uma imagem base dependente. 
 
## <a name="next-steps"></a>Próximas etapas
 
* Saiba mais sobre [as tarefas do ACR](container-registry-tasks-overview.md) para compilar, executar, enviar por push e corrigir imagens de contêiner no Azure.
* Consulte [como consumir e manter conteúdo público com tarefas de registro de contêiner do Azure](tasks-consume-public-content.md) para um fluxo de trabalho de retenção automatizada para atualizar as imagens base para o seu ambiente. 
* Consulte os [tutoriais de tarefas do ACR](container-registry-tutorial-quick-task.md) para obter mais exemplos para automatizar compilações e atualizações de imagem.
