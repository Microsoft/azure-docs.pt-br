---
title: Permissões para repositórios no Registro de Contêiner do Azure
description: Crie um token com permissões com escopo para repositórios específicos em um registro Premium para efetuar pull ou enviar imagens por Push ou executar outras ações
ms.topic: article
ms.date: 02/04/2021
ms.openlocfilehash: ceec69d746f77ea7a23bc70d029c8b3736e7f292
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988261"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Criar um token com permissões no escopo do repositório

Este artigo descreve como criar tokens e mapas de escopo para gerenciar o acesso a repositórios específicos no registro de contêiner. Com a criação de tokens, um proprietário do registro pode fornecer aos usuários ou serviços acesso aos repositórios, com escopo, limitado por tempo, para efetuar pull ou push de imagens ou executar outras ações. Um token fornece permissões mais refinadas do que outras [opções de autenticação](container-registry-authentication.md) de registro, que dão escopo de permissões para um registro inteiro. 

Os cenários para criar um token incluem:

* Permitir que dispositivos IoT com tokens individuais efetuem pull de uma imagem de um repositório
* Fornecer permissões a uma organização externa para um repositório específico 
* Limitar o acesso ao repositório a diferentes grupos de usuários em sua organização. Por exemplo, fornecer acesso de gravação e leitura aos desenvolvedores que criam imagens direcionadas a repositórios específicos e acesso de leitura às equipes que fazem implantação desses repositórios.

Esse recurso está disponível na camada de serviço **Premium** do registro de contêiner. Para obter informações sobre os limites e as camadas de serviço do Registro, confira [Camadas de serviço do Registro de Contêiner do Azure](container-registry-skus.md).

> [!IMPORTANT]
> Esse recurso está na versão prévia no momento; algumas [limitações se aplicam](#preview-limitations). As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="preview-limitations"></a>Limitações de visualização

* Atualmente, não é possível atribuir permissões no escopo do repositório a uma identidade do Azure Active Directory, como uma entidade de serviço ou uma identidade gerenciada.
* Não é possível criar um mapa de escopo em um registro habilitado para [acesso de pull anônimo](container-registry-faq.md#how-do-i-enable-anonymous-pull-access).

## <a name="concepts"></a>Conceitos

Para configurar permissões no escopo do repositório, você cria um *token* com um *mapa de escopo* associado. 

* Um **token** junto com uma senha gerada permite que o usuário se autentique com o registro. Você pode definir uma data de validade para uma senha de token ou desabilitar um token a qualquer momento.  

  Depois de autenticar com um token, o usuário ou serviço pode executar uma ou mais *ações* com escopo para um ou mais repositórios.

  |Ação  |Descrição  | Exemplo |
  |---------|---------|--------|
  |`content/delete`    | Remover dados do repositório  | Excluir um repositório ou um manifesto |
  |`content/read`     |  Ler dados do repositório |  Efetuar pull de um artefato |
  |`content/write`     |  Gravar dados no repositório     | Usar com `content/read` para efetuar push de um artefato |
  |`metadata/read`    | Ler metadados do repositório   | Listar tags ou manifestos |
  |`metadata/write`     |  Gravar metadados no repositório  | Habilitar ou desabilitar operações de leitura, gravação ou exclusão |

* Um **mapa de escopo** agrupa as permissões de repositório que você aplica a um token e pode reaplicar a outros tokens. Cada token é associado a um único mapa de escopo. 

   Com um mapa de escopo:

    * Configure vários tokens com permissões idênticas para um conjunto de repositórios
    * Atualize permissões de token ao adicionar ou remover ações de repositório no mapa de escopo ou aplicar um mapa de escopo diferente 

  O registro de contêiner do Azure também fornece vários mapas de escopo definidos pelo sistema que você pode aplicar ao criar tokens. As permissões de mapas de escopo definidos pelo sistema se aplicam a todos os repositórios no registro.

A imagem a seguir mostra a relação entre tokens e mapas de escopo. 

![Tokens de registro e mapas de escopo](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Pré-requisitos

* Os exemplos de comando de comandos **CLI do Azure** CLI do Azure neste artigo exigem CLI do Azure versão 2.17.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).
* **Docker** - Para autenticar com o registro para efetuar pull ou push de imagens, você precisa de uma instalação local do Docker. O Docker fornece instruções de instalação para sistemas [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Registro de contêiner** - Crie um registro de contêiner Premium, caso não tenha um, na sua assinatura do Azure ou atualize um registro existente. Por exemplo, use o [Portal do Azure](container-registry-get-started-portal.md) ou a [CLI do Azure](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Criar token - CLI

### <a name="create-token-and-specify-repositories"></a>Criar token e especificar repositórios

Para criar um token, use o comando [az acr token create][az-acr-token-create]. Ao criar um token, você pode especificar um ou mais repositórios e ações associadas em cada repositório. Os repositórios não precisam ainda estar no registro. Para criar um token especificando um mapa de escopo existente, consulte a [próxima seção](#create-token-and-specify-scope-map).

O exemplo a seguir cria um token no registro *myregistry* com as seguintes permissões no repositório `samples/hello-world`: `content/write` e `content/read`. Por padrão, o comando define o status do token padrão como `enabled`, mas você pode atualizar o status para `disabled` a qualquer momento.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

A saída mostra detalhes sobre o token. Por padrão, são geradas duas senhas que não expiram, mas você pode opcionalmente definir uma data de expiração. É recomendável salvar as senhas em um local seguro para usar posteriormente para autenticação. As senhas não podem ser recuperadas, mas novas podem ser geradas.

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

> [!NOTE]
> Para regenerar senhas de token e períodos de expiração, consulte [regenerar senhas de token](#regenerate-token-passwords) mais adiante neste artigo.

A saída inclui detalhes sobre o mapa de escopo que o comando criou. Você pode usar o mapa de escopo, aqui chamado de `MyToken-scope-map`, para aplicar as mesmas ações de repositório a outros tokens. Ou atualize o mapa de escopo depois para alterar as permissões dos tokens associados.

### <a name="create-token-and-specify-scope-map"></a>Criar token e especificar mapa de escopo

Uma maneira alternativa de criar um token é especificar um mapa de escopo existente. Se você ainda não tiver um mapa de escopo, primeiro crie um especificando repositórios e ações associadas. Em seguida, especifique o mapa de escopo ao criar um token. 

Para criar um mapa de escopo, use o comando[az acr scope-map create][az-acr-scope-map-create]. O comando a seguir cria um mapa de escopo com as mesmas permissões no repositório `samples/hello-world` usado anteriormente. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Execute [az acr token create][az-acr-token-create] para criar um token, especificando o mapa de escopo *MyScopeMap*. Como no exemplo anterior, o comando define o status do token padrão como `enabled`.

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

A saída mostra detalhes sobre o token. Por padrão, duas senhas são geradas. É recomendável salvar as senhas em um local seguro para usar posteriormente para autenticação. As senhas não podem ser recuperadas, mas novas podem ser geradas.

> [!NOTE]
> Para regenerar senhas de token e períodos de expiração, consulte [regenerar senhas de token](#regenerate-token-passwords) mais adiante neste artigo.

## <a name="create-token---portal"></a>Criar token - portal

Você pode usar o portal do Azure para criar tokens e mapas de escopo. Assim como ocorre com o comando CLI `az acr token create`, você pode aplicar um mapa de escopo existente ou criar um mapa de escopo ao criar um token especificando um ou mais repositórios e ações associadas. Os repositórios não precisam ainda estar no registro. 

O exemplo a seguir cria um token e cria um mapa de escopo com as seguintes permissões no repositório `samples/hello-world`: `content/write` e `content/read`.

1. No portal, navegue até o registro de contêiner.
1. Em **permissões do repositório**, selecione **tokens (versão prévia) > + adicionar**.

      :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-add.png" alt-text="Criar token no portal":::
1. Insira um nome do token.
1. Em **Mapa de escopo**, selecione **Criar novo**.
1. Configurar o mapa de escopo:
    1. Insira um nome e descrição para o mapa de escopo. 
    1. Em **Repositórios**, insira `samples/hello-world` e, em **Permissões**, selecione `content/read` e `content/write`. Em seguida, selecione **+Adicionar**.  

        :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-scope-map-add.png" alt-text="Criar mapa de escopo no portal":::

    1. Depois de adicionar repositórios e permissões, selecione **Adicionar** para adicionar o mapa de escopo.
1. Aceite o **Status** de token padrão de **Habilitado** e, em seguida, selecione **Criar**.

Depois que o token for validado e criado, os detalhes do token serão exibidos na tela **Tokens**.

### <a name="add-token-password"></a>Adicionar senha do token

Para usar um token criado no portal, você deve gerar uma senha. Você pode gerar uma ou duas senhas e definir uma data de validade para cada uma. 

1. No portal, navegue até o registro de contêiner.
1. Em **permissões do repositório**, selecione **tokens (versão prévia)** e selecione um token.
1. Nos detalhes do token, selecione **password1** ou **password2** e selecione o ícone Gerar.
1. Na tela da senha, defina uma data de validade para a senha, se quiser, e selecione **Gerar**. É recomendável definir uma data de expiração.
1. Depois de gerar uma senha, copie-a e salve-a em um local seguro. Não é possível recuperar uma senha gerada após fechar a tela, mas você pode gerar uma nova.

    :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-password.png" alt-text="Criar senha de token no portal":::

## <a name="authenticate-with-token"></a>Autenticar com token

Quando um usuário ou serviço usa um token para autenticar com o registro de destino, ele fornece o nome do token como um nome de usuário e uma de suas senhas geradas. 

O método de autenticação depende da ação configurada ou das ações associadas ao token.

|Ação  |Como autenticar  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete` na CLI do Azure<br/><br/>Exemplo: `az acr repository delete --name myregistry --repository myrepo --username MyToken --password xxxxxxxxxx`|
  |`content/read`     |  `docker login`<br/><br/>`az acr login` na CLI do Azure<br/><br/>Exemplo: `az acr login --name myregistry --username MyToken --password xxxxxxxxxx`  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login` na CLI do Azure     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests` na CLI do Azure   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update` na CLI do Azure |

## <a name="examples-use-token"></a>Exemplos: Usar token

Os exemplos a seguir usam o token criado anteriormente neste artigo para executar operações comuns em um repositório: efetuar push e pull de imagens, excluir imagens e listar tags de repositório. O token foi configurado inicialmente com permissões de push (ações `content/write` e `content/read`) no repositório `samples/hello-world`.

### <a name="pull-and-tag-test-images"></a>Efetuar pull e marcar imagens de teste

Para os exemplos a seguir, receba Public `hello-world` e `nginx` imagens do registro de contêiner da Microsoft e marque-as para o registro e o repositório.

```bash
docker pull mcr.microsoft.com/hello-world
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
docker tag mcr.microsoft.com/hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx:v1
```

### <a name="authenticate-using-token"></a>Autenticar usando token

Execute `docker login` ou `az acr login` para se autenticar com o registro para enviar ou extrair imagens. Forneça o nome do token como o nome de usuário e forneça uma de suas senhas. O token deve ter o status de `Enabled`.

O exemplo a seguir é formatado para o shell do bash e fornece os valores usando variáveis de ambiente.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

A saída deve mostrar a autenticação bem-sucedida:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Efetuar push de imagens para registro

Após o logon bem-sucedido, tente efetuar push das imagens marcadas para o registro. Como o token tem permissões para efetuar push das imagens para o repositório de `samples/hello-world`, o seguinte push é executado com sucesso:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

O token não tem permissões para o repositório de `samples/nginx`, portanto, a seguinte tentativa de push falha com um erro semelhante a `requested access to the resource is denied`:

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

### <a name="update-token-permissions"></a>Atualizar permissões de token

Para atualizar as permissões de um token, atualize as permissões no mapa de escopo associado. O mapa de escopo atualizado é aplicado imediatamente a todos os tokens associados. 

Por exemplo, atualize `MyToken-scope-map` com ações `content/write` e `content/read` no repositório `samples/ngnx` e remova a ação `content/write` no repositório `samples/hello-world`.  

Para usar a CLI do Azure, execute [az acr scope-map update][az-acr-scope-map-update] para atualizar o mapa de escopo:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/write content/read \
  --remove-repository samples/hello-world content/write 
```

No Portal do Azure:

1. Navegue até seu registro de contêiner.
1. Em **permissões de repositório**, selecione **mapas de escopo (versão prévia)** e selecione o mapa de escopo a ser atualizado.
1. Em **Repositórios**, insira `samples/nginx` e, em **Permissões**, selecione `content/read` e `content/write`. Em seguida, selecione **+Adicionar**.
1. Em **Repositórios**, selecione `samples/hello-world` e, em **Permissões**, desmarque `content/write`. Em seguida, selecione **Salvar**.

Depois de atualizar o mapa de escopo, o push a seguir será efetuado:

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

Como o mapa de escopo tem apenas a permissão `content/read` no repositório `samples/hello-world`, uma tentativa de efetuar push para o repositório `samples/hello-world` agora falhará:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

O pull de imagens de ambos os repositórios é efetuado com sucesso, porque o mapa de escopo fornece permissões `content/read` em ambos os repositórios:

```bash
docker pull myregistry.azurecr.io/samples/nginx:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Excluir imagens

Para atualizar o mapa de escopo, adicione a ação `content/delete` ao repositório `nginx`. Essa ação permite excluir imagens no repositório ou todo o repositório.

Para resumir, mostraremos apenas o comando [az acr scope-map update][az-acr-scope-map-update] para atualizar o mapa do escopo:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/delete
``` 

Para atualizar o mapa de escopo usando o portal, consulte a [seção anterior](#update-token-permissions).

Use o comando a seguir [az acr repository delete][az-acr-repository-delete] para excluir o repositório `samples/nginx`. Para excluir imagens ou repositórios, passe o nome e a senha do token para o comando. O exemplo a seguir usa as variáveis de ambiente criadas anteriormente neste artigo:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/nginx \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Mostrar tags do repositório 

Para atualizar o mapa de escopo, adicione a ação `metadata/read` ao repositório `hello-world`. Essa ação permite a leitura de dados de manifesto e tags no repositório.

Para resumir, mostraremos apenas o comando [az acr scope-map update][az-acr-scope-map-update] para atualizar o mapa do escopo:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/hello-world metadata/read 
```  

Para atualizar o mapa de escopo usando o portal, consulte a [seção anterior](#update-token-permissions).

Para ler metadados no repositório `samples/hello-world`, execute o comando [az acr repository show-manifests][az-acr-repository-show-manifests] ou [az acr repository show-tags][az-acr-repository-show-tags]. 

Para ler metadados, passe o nome e a senha do token para o comando. O exemplo a seguir usa as variáveis de ambiente criadas anteriormente neste artigo:

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

Saída de exemplo:

```console
[
  "v1"
]
```

## <a name="manage-tokens-and-scope-maps"></a>Gerenciar tokens e mapas de escopo

### <a name="list-scope-maps"></a>Listar mapas de escopo

Use o comando [az acr scope-map list][az-acr-scope-map-list] ou a tela **Mapas de escopo (Versão prévia)** no portal, para listar todos os mapas de escopo configurados em um registro. Por exemplo:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

A saída consiste em três mapas de escopo definidos pelo sistema e outros mapas de escopo gerados por você. Os tokens podem ser configurados com qualquer um desses mapas de escopo.

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Mostrar detalhes do token

Para exibir os detalhes de um token, como seu status e datas de expiração de senha, execute o comando [az acr token show][az-acr-token-show] ou selecione o token na tela **Tokens (Versão prévia)** no portal. Por exemplo:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Use o comando [az acr token list][az-acr-token-list] ou a tela **Tokens (Versão prévia)** no portal, para listar todos os tokens configurados em um registro. Por exemplo:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="regenerate-token-passwords"></a>Regenerar senhas de token

Se você não gerou uma senha de token ou deseja gerar novas senhas, execute o comando [AZ ACR token Credential Generate][az-acr-token-credential-generate] . 

O exemplo a seguir gera um novo valor para password1 para o token *MyToken*, com um período de expiração de 30 dias. Ele armazena a senha na variável de ambiente `TOKEN_PWD`. Este exemplo é formatado para o shell do bash.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --expiration-in-days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Para usar o portal do Azure para gerar uma senha de token, consulte as etapas em [Criar o token - portal](#create-token---portal) anteriormente explicadas neste artigo.

### <a name="update-token-with-new-scope-map"></a>Atualizar o token com o novo mapa de escopo

Se você quiser atualizar um token com um mapa de escopo diferente, execute [az acr token update][az-acr-token-update] e especifique o novo mapa de escopo. Por exemplo:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

No portal, na tela **Tokens (versão prévia)** , selecione o token e, em **Mapa de escopo**, selecione um mapa de escopo diferente.

> [!TIP]
> Depois de atualizar um token com um novo mapa de escopo, talvez você queira gerar novas senhas de token. Use o comando [az acr token credential generate][az-acr-token-credential-generate] ou gere novamente uma senha de token no portal do Azure.

## <a name="disable-or-delete-token"></a>Desabilitar ou excluir token

Talvez seja necessário desabilitar temporariamente o uso das credenciais de token para um usuário ou serviço. 

Usando a CLI do Azure, execute o comando[az acr token update][az-acr-token-update] para definir o `status` como `disabled`:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

No portal, selecione o token na tela **Tokens (Versão prévia)** e selecione **Desabilitado** em **Status**.

Para excluir um token para invalidar permanentemente o acesso por qualquer pessoa que use suas credenciais, execute o comando [az acr token delete][az-acr-token-delete]. 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

No portal, selecione o token na tela **Tokens (Versão prévia)** e selecione **Descartar**.

## <a name="next-steps"></a>Próximas etapas

* Para gerenciar tokens e mapas de escopo, use comandos adicionais em grupos de comando [az acr scope-map][az-acr-scope-map] e [az acr token][az-acr-token].
* Consulte a [visão geral da autenticação](container-registry-authentication.md) para obter outras opções de autenticação com um registro de contêiner do Azure, incluindo o uso de uma identidade de Azure Active Directory, uma entidade de serviço ou uma conta de administrador.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az-acr-repository-show-manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az-acr-repository-delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-list]: /cli/azure/acr/token/#az-acr-token-list
[az-acr-token-delete]: /cli/azure/acr/token/#az-acr-token-delete
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
