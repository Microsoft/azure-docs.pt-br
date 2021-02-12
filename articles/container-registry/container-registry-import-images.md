---
title: Importar imagens de contêiner
description: Importe imagens de contêiner para um registro de contêiner do Azure usando APIs do Azure sem a necessidade de executar comandos do Docker.
ms.topic: article
ms.date: 01/15/2021
ms.openlocfilehash: e6976f854b449f68faedd51878c2f3a7fe75cb0f
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988244"
---
# <a name="import-container-images-to-a-container-registry"></a>Importar imagens de contêiner para um registro de contêiner

Você pode importar (copiar) com facilidade imagens de contêiner para um registro de contêiner do Azure sem usar de comandos do Docker. Por exemplo, importe imagens de um registro de desenvolvimento para um registro de produção ou copie imagens de base de um registro público.

O Registro de Contêiner do Azure lida com inúmeros cenários comuns para copiar imagens de um registro existente:

* Importar de um registro público

* Importar de outro registro de contêiner do Azure, no mesmo ou em uma assinatura ou locatário diferente do Azure

* Importar de um registro de contêiner particular que não é do Azure

A importação de imagem para um registro de contêiner do Azure tem os seguintes benefícios em relação ao uso de comandos da CLI do Docker:

* Como seu ambiente de cliente não precisa de uma instalação local do Docker, importe qualquer imagem de contêiner, independentemente do tipo de sistema operacional com suporte.

* Quando você importa imagens de várias arquiteturas (como imagens oficiais do Docker), são copiadas as imagens de todas as arquiteturas e plataformas especificadas na lista de manifesto.

* O acesso ao registro de destino não precisa usar o ponto de extremidade público do registro.

Para importar imagens de contêiner, este artigo requer que você execute a CLI do Azure no Azure Cloud Shell ou localmente (versão 2.0.55 ou posterior recomendada). Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli].

> [!NOTE]
> Se for necessário distribuir imagens de contêiner idênticas em várias regiões do Azure, o Registro de Contêiner do Azure também suporta a [replicação geográfica](container-registry-geo-replication.md). Ao replicar geograficamente um registro (camada de serviço Premium necessária), você pode atender a várias regiões com nomes de imagem e marca idênticos de um único registro.
>

> [!IMPORTANT]
> As alterações na importação de imagem entre dois registros de contêiner do Azure foram introduzidas a partir de janeiro de 2021:
> * Importar de ou para um registro de contêiner do Azure restrito à rede requer que o registro restrito [**permita o acesso por serviços confiáveis**](allow-access-trusted-services.md) para ignorar a rede. Por padrão, a configuração é habilitada, permitindo a importação. Se a configuração não estiver habilitada em um registro recém-criado com um ponto de extremidade privado ou com regras de firewall do registro, a importação falhará. 
> * Em um registro de contêiner do Azure com restrição de rede existente que é usado como uma origem ou destino de importação, a habilitação desse recurso de segurança de rede é opcional, mas recomendada.

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não tiver um registro de contêiner do Azure, crie um. Para obter as etapas, consulte [início rápido: criar um registro de contêiner privado usando o CLI do Azure](container-registry-get-started-azure-cli.md).

Para importar uma imagem para um registro de contêiner do Azure, sua identidade deve ter permissões de gravação para o registro de destino (pelo menos a função de colaborador ou uma função personalizada que permita a ação importImage). Confira [Funções e permissões do Registro de Contêiner do Azure](container-registry-roles.md#custom-roles). 

## <a name="import-from-a-public-registry"></a>Importar de um registro público

### <a name="import-from-docker-hub"></a>Importar do Hub do Docker

Por exemplo, use o comando [az acr import][az-acr-import] para importar as imagens de várias arquiteturas `hello-world:latest` do Hub do Docker para um registro chamado *myregistry*. Como `hello-world` é uma imagem oficial do Hub do Docker, esta imagem está no repositório `library` padrão. Inclua o nome do repositório e, opcionalmente, uma marca no valor do parâmetro de imagem `--source`. (Opcionalmente, você pode identificar uma imagem pelo resumo do manifesto, em vez de usar marca, o que garante uma versão específica de uma imagem.)
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

Você pode verificar que vários manifestos são associados esta imagem executando o comando `az acr repository show-manifests`:

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

Se você tiver uma [conta de Hub do Docker](https://www.docker.com/pricing), recomendamos que use as credenciais ao importar uma imagem do Hub do Docker. Passe o nome de usuário do Hub do Docker e a senha ou um [token de acesso pessoal](https://docs.docker.com/docker-hub/access-tokens/) como parâmetros para `az acr import` . O exemplo a seguir importa uma imagem pública do `tensorflow` repositório no Hub do Docker, usando as credenciais do Hub do Docker:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
  --username <Docker Hub user name>
  --password <Docker Hub token>
```

### <a name="import-from-microsoft-container-registry"></a>Importar do Registro de Contêiner da Microsoft

Por exemplo, importe a `ltsc2019` imagem do Windows Server Core do `windows` repositório no registro de contêiner da Microsoft.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>Importar de um registro de contêiner do Azure no mesmo locatário do AD

Você pode importar uma imagem de um registro de contêiner do Azure no mesmo locatário do AD usando permissões de Azure Active Directory integradas.

* Sua identidade deve ter Azure Active Directory permissões para ler o registro de origem (função de leitor) e importar para o registro de destino (função de colaborador ou uma [função personalizada](container-registry-roles.md#custom-roles) que permita a ação importImage).

* O registro pode estar na mesmo assinatura ou em uma assinatura diferente do Azure no mesmo locatário do Active Directory.

* O [acesso público](container-registry-access-selected-networks.md#disable-public-network-access) ao registro de origem pode estar desabilitado. Se o acesso público estiver desabilitado, especifique o registro de origem por ID de recurso, em vez de pelo nome do servidor de logon do registro.

* Se o registro de origem e/ou o registro de destino tiver um ponto de extremidade privado ou regras de firewall do registro forem aplicadas, verifique se o registro restrito permite que os [serviços confiáveis](allow-access-trusted-services.md) acessem a rede.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importar de um registro na mesma assinatura

Por exemplo, importe a imagem `aci-helloworld:latest` de um registro de origem *mysourceregistry* para *myregistry* na mesma assinatura do Azure.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

O exemplo a seguir importa a `aci-helloworld:latest` imagem para *myregistry* de um *mysourceregistry* do registro de origem no qual o acesso ao ponto de extremidade público do registro está desabilitado. Forneça a ID de recurso do registro de fonte com o parâmetro `--registry`. Observe que o `--source` parâmetro especifica apenas o repositório de origem e a marca, não o nome do servidor de logon do registro.

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

O exemplo a seguir importa uma imagem pelo resumo do manifesto (hash de SHA-256, representado como `sha256:...`) em vez de por marca:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importar um registro de uma assinatura diferente

No exemplo a seguir, *mysourceregistry* está em uma assinatura diferente de *myregistry* no mesmo locatário do Active Directory. Forneça a ID de recurso do registro de fonte com o parâmetro `--registry`. Observe que o `--source` parâmetro especifica apenas o repositório de origem e a marca, não o nome do servidor de logon do registro.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importar de um registro usando as credenciais da entidade de serviço

Para importar de um registro que você não pode acessar usando permissões de Active Directory integradas, você pode usar as credenciais da entidade de serviço (se disponível) para o registro de origem. Forneça a appID e a senha da [entidade de serviço](container-registry-auth-service-principal.md) do Active Directory que tem acesso de ACRPull no registro de origem. O uso de uma entidade de serviço é útil para sistemas de compilação e outros sistemas autônomos que precisam importar imagens confiáveis para seu registro.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>Importar de um registro de contêiner do Azure em um locatário do AD diferente

Para importar de um registro de contêiner do Azure em um locatário Azure Active Directory diferente, especifique o registro de origem por nome do servidor de logon e forneça as credenciais de nome de usuário e senha que habilitam o acesso de pull ao registro. Por exemplo, use um [token no escopo do repositório](container-registry-repository-scoped-permissions.md) e uma senha, ou a AppID e a senha de uma [entidade de serviço](container-registry-auth-service-principal.md) Active Directory que tenha acesso ACRPull ao registro de origem. 

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importar de um registro de contêiner particular que não é do Azure

Importe uma imagem de um registro privado não Azure especificando as credenciais que habilitam o acesso de pull ao registro. Por exemplo, efetue o pull de uma imagem de um registro particular do Docker: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre como importar imagens de contêiner para um registro de contêiner do Azure a partir de um registro público ou de outro registro particular. Para saber opções adicionais de importação de imagem, consulte a referência do comando [az acr import][az-acr-import]. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
