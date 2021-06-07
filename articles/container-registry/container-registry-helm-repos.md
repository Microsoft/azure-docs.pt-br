---
title: Armazenar gráficos do Helm
description: Saiba como armazenar gráficos Helm para seus aplicativos kubernetes usando repositórios no registro de contêiner do Azure
ms.topic: article
ms.date: 06/12/2020
ms.openlocfilehash: 69b16f35589586787e1c31a0e9755b9030af755d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86537860"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Enviar por push e efetuar pull de gráficos do Helm para um registro de contêiner do Azure

Para gerenciar e implanta rapidamente aplicativos para Kubernetes, é possível usar o [gerenciador de pacotes do Helm de software livre][helm]. Com o Helm, os pacotes de aplicativos são definidos como [gráficos](https://helm.sh/docs/topics/charts/), que são coletados e armazenados em um [repositório de gráfico do Helm](https://helm.sh/docs/topics/chart_repository/).

Este artigo mostra como hospedar repositórios de gráficos do Helm em um registro de contêiner do Azure, usando comandos do Helm 3. Em muitos cenários, você criaria e carregaria seus próprios gráficos para os aplicativos que você desenvolve. Para obter mais informações sobre como criar seus próprios gráficos do Helm, consulte o [Guia do desenvolvedor do modelo de gráfico][develop-helm-charts]. Você também pode armazenar um gráfico Helm existente de outro repositório do Helm.

> [!IMPORTANT]
> O suporte para gráficos Helm no registro de contêiner do Azure está atualmente em visualização. As visualizações são disponibilizadas para você sobre a condição de que você concorda com os [termos de uso][terms-of-use]complementares. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="helm-3-or-helm-2"></a>Helm 3 ou Helm 2?

Para armazenar, gerenciar e instalar gráficos do Helm, você usa um cliente do Helm e a CLI do Helm. As versões principais do cliente Helm incluem Helm 3 e Helm 2. Para obter detalhes sobre as diferenças de versão, consulte as [perguntas frequentes sobre a versão](https://helm.sh/docs/faq/). 

Helm 3 deve ser usado para hospedar gráficos Helm no registro de contêiner do Azure. Com o Helm 3, você:

* Pode criar um ou mais repositórios do Helm em um registro de contêiner do Azure
* Armazene gráficos Helm 3 em um registro como [artefatos de OCI](container-registry-image-formats.md#oci-artifacts). Atualmente, o suporte do Helm 3 para o OCI é *experimental*.
* Autentique com o registro usando o `helm registry login` comando.
* Usar `helm chart` comandos na CLI do Helm para enviar, extrair e gerenciar gráficos do Helm em um registro
* Use `helm install` para instalar gráficos em um cluster kubernetes de um cache de repositório local.
> [!NOTE]
> A partir de Helm 3, [AZ ACR Helm][az-acr-helm] comandos para uso com o cliente Helm 2 estão sendo preteridos. Consulte o [roteiro do produto](https://github.com/Azure/acr/blob/master/docs/acr-roadmap.md#acr-helm-ga). Se você tiver implantado anteriormente Helm 2 gráficos, consulte [migrando Helm v2 para v3](https://helm.sh/docs/topics/v2_v3_migration/).

## <a name="prerequisites"></a>Pré-requisitos

Os recursos a seguir são necessários para o cenário neste artigo:

- **Um registro de contêiner do Azure** em sua assinatura do Azure. Se necessário, crie um registro usando o [portal do Azure](container-registry-get-started-portal.md) ou o [CLI do Azure](container-registry-get-started-azure-cli.md).
- **Helm Client versão 3.1.0 ou posterior** -execute `helm version` para localizar sua versão atual. Para saber mais sobre como instalar e usar o Helm, consulte [Instalar o Helm][helm-install].
- **Um cluster kubernetes** em que você instalará um gráfico do Helm. Se necessário, crie um [cluster do serviço kubernetes do Azure][aks-quickstart]. 
- **CLI do Azure versão 2.0.71 ou posterior** -execute `az --version` para localizar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="enable-oci-support"></a>Habilitar o suporte a OCI

Use o `helm version` comando para verificar se você instalou o Helm 3:

```console
helm version
```

Defina a seguinte variável de ambiente para habilitar o suporte a OCI no cliente Helm 3. Atualmente, esse suporte é experimental. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

## <a name="create-a-sample-chart"></a>Criar um gráfico de exemplo

Crie um gráfico de teste usando os seguintes comandos:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Como um exemplo básico, altere o diretório para a `templates` pasta e primeiro exclua o conteúdo ali:

```console
cd hello-world/templates
rm -rf *
```

Na `templates` pasta, crie um arquivo chamado `configmap.yaml` , executando o seguinte comando:

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

Para obter mais informações sobre como criar e executar este exemplo, consulte [introdução](https://helm.sh/docs/chart_template_guide/getting_started/) no docs Helm.

## <a name="save-chart-to-local-registry-cache"></a>Salvar gráfico no cache do Registro local

Altere o diretório para o `hello-world` subdiretório. Em seguida, execute `helm chart save` para salvar uma cópia do gráfico localmente e também criar um alias com o nome totalmente qualificado do registro (todas as letras minúsculas) e o repositório e a marca de destino. 

No exemplo a seguir, o nome do registro é *mycontainerregistry*, o repositório de destino é *Hello-World* e a marca do gráfico de destino é *v1*, mas valores substitutos para o seu ambiente:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Execute `helm chart list` para confirmar que você salvou os gráficos no cache do Registro local. A saída é semelhante a:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

## <a name="authenticate-with-the-registry"></a>Autenticar com o registro

Execute o `helm registry login` comando na CLI do Helm 3 para [autenticar com o registro](container-registry-authentication.md) usando as credenciais apropriadas para seu cenário.

Por exemplo, crie uma [entidade de serviço Azure Active Directory com as permissões pull e Push](container-registry-auth-service-principal.md#create-a-service-principal) (função AcrPush) para o registro. Em seguida, forneça as credenciais da entidade de serviço para `helm registry login` . O exemplo a seguir fornece a senha usando uma variável de ambiente:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

## <a name="push-chart-to-registry"></a>Enviar gráfico por push ao registro

Execute o `helm chart push` comando na CLI do Helm 3 para enviar o gráfico por push para o repositório de destino totalmente qualificado:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Após um push bem-sucedido, a saída será semelhante a:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

## <a name="list-charts-in-the-repository"></a>Listar gráficos no repositório

Assim como acontece com as imagens armazenadas em um registro de contêiner do Azure, você pode usar os comandos [AZ ACR Repository][az-acr-repository] para mostrar os repositórios que hospedam seus gráficos e as marcas e os manifestos do gráfico. 

Por exemplo, execute [AZ ACR Repository show][az-acr-repository-show] para ver as propriedades do repositório que você criou na etapa anterior:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

A saída é semelhante a:

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Execute o comando [AZ ACR Repository show-manifestas][az-acr-repository-show-manifests] para ver detalhes do gráfico armazenado no repositório. Por exemplo:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

A saída, abreviada neste exemplo, mostra um `configMediaType` de `application/vnd.cncf.helm.config.v1+json` :

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

## <a name="pull-chart-to-local-cache"></a>Gráfico de pull para cache local

Para instalar um gráfico do Helm no kubernetes, o gráfico deve estar no cache local. Neste exemplo, primeiro execute `helm chart remove` para remover o gráfico local existente chamado `mycontainerregistry.azurecr.io/helm/hello-world:v1` :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Execute `helm chart pull` para baixar o gráfico do registro de contêiner do Azure para o cache local:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

## <a name="export-helm-chart"></a>Exportar gráfico Helm

Para trabalhar ainda mais com o gráfico, exporte-o para um diretório local usando `helm chart export` . Por exemplo, exporte o gráfico que você recebeu para o `install` diretório:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Para exibir informações do gráfico exportado no repositório, execute o `helm show chart` comando no diretório em que você exportou o gráfico.

```console
cd install
helm show chart hello-world
```

Helm retorna informações detalhadas sobre a versão mais recente do gráfico, conforme mostrado na seguinte saída de exemplo:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

## <a name="install-helm-chart"></a>Instalar o gráfico do Helm

Execute `helm install` para instalar o gráfico Helm que você recebeu no cache local e exportado. Especifique um nome de versão, como *myhelmtest*, ou passe o `--generate-name` parâmetro. Por exemplo:

```console
helm install myhelmtest ./hello-world
```

A saída após a instalação bem-sucedida do gráfico é semelhante a:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Para verificar a instalação, execute o `helm get manifest` comando. 

```console
helm get manifest myhelmtest
```

O comando retorna os dados de YAML em seu `configmap.yaml` arquivo de modelo.

Execute `helm uninstall` para desinstalar a versão do gráfico em seu cluster:

```console
helm uninstall myhelmtest
```

## <a name="delete-chart-from-the-registry"></a>Excluir gráfico do registro

Para excluir um gráfico do registro de contêiner, use o comando [AZ ACR Repository Delete][az-acr-repository-delete] . Execute o comando a seguir e confirme a operação quando solicitado:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como criar e implantar gráficos do Helm, confira [Developing Helm charts][develop-helm-charts] (Desenvolvendo gráficos do Helm).
* Saiba mais sobre como instalar aplicativos com o Helm no [AKs (serviço kubernetes do Azure)](../aks/kubernetes-helm.md).
* Os gráficos do Helm podem ser usados como parte do processo de build do contêiner. Para obter mais informações, consulte [usar tarefas do registro de contêiner do Azure][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[acr-tasks]: container-registry-tasks-overview.md
