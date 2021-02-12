---
title: Modelos de pacote
titleSuffix: Azure Machine Learning
description: Empacotar um modelo. Os modelos podem ser empacotados como uma imagem do Docker, que pode ser baixada, ou você pode criar um Dockerfile e usá-lo para criar a imagem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 299eb316b534ddc9d5eee934cc15eae841276038
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93312630"
---
# <a name="how-to-package-a-registered-model-with-docker"></a>Como empacotar um modelo registrado com o Docker

Este artigo mostra como empacotar um modelo de Azure Machine Learning registrado com o Docker.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tenha treinado e registrado um modelo em seu espaço de trabalho do Machine Learning. Para saber como treinar e registrar um modelo scikit-learn, [siga este tutorial](how-to-train-scikit-learn.md).


## <a name="package-models"></a>Modelos de pacote

Em alguns casos, talvez você queira criar uma imagem do Docker sem implantar o modelo (se, por exemplo, você planeja [implantar no serviço Azure app](how-to-deploy-app-service.md)). Ou talvez você queira baixar a imagem e executá-la em uma instalação local do Docker. Talvez você queira até mesmo baixar os arquivos usados para criar a imagem, inspecioná-los, modificá-los e criar a imagem manualmente.

O empacotamento de modelo permite que você faça essas coisas. Ele empacota todos os ativos necessários para hospedar um modelo como um serviço Web e permite que você baixe uma imagem do Docker totalmente criada ou os arquivos necessários para criar um. Há duas maneiras de usar o empacotamento de modelo:

**Baixar um modelo empacotado:** Baixe uma imagem do Docker que contém o modelo e outros arquivos necessários para hospedá-lo como um serviço Web.

**Gerar um Dockerfile:** Baixe o Dockerfile, o modelo, o script de entrada e outros ativos necessários para criar uma imagem do Docker. Você pode inspecionar os arquivos ou fazer alterações antes de criar a imagem localmente.

Ambos os pacotes podem ser usados para obter uma imagem local do Docker.

> [!TIP]
> A criação de um pacote é semelhante à implantação de um modelo. Você usa um modelo registrado e uma configuração de inferência.

> [!IMPORTANT]
> Para baixar uma imagem totalmente criada ou criar uma imagem localmente, você precisa ter o [Docker](https://www.docker.com) instalado em seu ambiente de desenvolvimento.

### <a name="download-a-packaged-model"></a>Baixar um modelo empacotado

O exemplo a seguir cria uma imagem, que é registrada no registro de contêiner do Azure para seu espaço de trabalho:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Depois de criar um pacote, você pode usar `package.pull()` o para efetuar pull da imagem para o ambiente do Docker local. A saída desse comando exibirá o nome da imagem. Por exemplo: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Depois de baixar o modelo, use o `docker images` comando para listar as imagens locais:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Para iniciar um contêiner local com base nessa imagem, use o comando a seguir para iniciar um contêiner nomeado do Shell ou da linha de comando. Substitua o `<imageid>` valor pela ID da imagem retornada pelo `docker images` comando.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Esse comando inicia a versão mais recente da imagem chamada `myimage` . Ele mapeia a porta local 6789 para a porta no contêiner em que o serviço Web está escutando (5001). Ele também atribui o nome `mycontainer` ao contêiner, o que torna o contêiner mais fácil de parar. Depois que o contêiner é iniciado, você pode enviar solicitações para `http://localhost:6789/score` .

### <a name="generate-a-dockerfile-and-dependencies"></a>Gerar um Dockerfile e dependências

O exemplo a seguir mostra como baixar o Dockerfile, o modelo e outros ativos necessários para criar uma imagem localmente. O `generate_dockerfile=True` parâmetro indica que você deseja os arquivos, não uma imagem totalmente compilada.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Esse código baixa os arquivos necessários para criar a imagem no `imagefiles` diretório. O Dockerfile incluído nos arquivos salvos faz referência a uma imagem base armazenada em um registro de contêiner do Azure. Ao criar a imagem na instalação local do Docker, você precisa usar o endereço, o nome de usuário e a senha para se autenticar no registro. Use as etapas a seguir para criar a imagem usando uma instalação local do Docker:

1. Em uma sessão de linha de comando ou Shell, use o comando a seguir para autenticar o Docker com o registro de contêiner do Azure. Substitua `<address>` , `<username>` e `<password>` pelos valores recuperados por `package.get_container_registry()` .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Para criar a imagem, use o comando a seguir. Substitua `<imagefiles>` pelo caminho do diretório em que `package.save()` os arquivos foram salvos.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Este comando define o nome da imagem como `myimage` .

Para verificar se a imagem foi criada, use o `docker images` comando. Você deve ver a `myimage` imagem na lista:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Para iniciar um novo contêiner com base nessa imagem, use o seguinte comando:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Esse comando inicia a versão mais recente da imagem chamada `myimage` . Ele mapeia a porta local 6789 para a porta no contêiner em que o serviço Web está escutando (5001). Ele também atribui o nome `mycontainer` ao contêiner, o que torna o contêiner mais fácil de parar. Depois que o contêiner é iniciado, você pode enviar solicitações para `http://localhost:6789/score` .

### <a name="example-client-to-test-the-local-container"></a>Exemplo de cliente para testar o contêiner local

O código a seguir é um exemplo de um cliente Python que pode ser usado com o contêiner:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Por exemplo, clientes em outras linguagens de programação, consulte [consumir modelos implantados como serviços Web](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Parar o contêiner do Docker

Para parar o contêiner, use o seguinte comando de um shell ou linha de comando diferente:

```bash
docker kill mycontainer
```

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de uma implantação com falha](how-to-troubleshoot-deployment.md)
* [Implantar no Serviço de Kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md)
* [Criar aplicativos cliente para consumir serviços Web](how-to-consume-web-service.md)
* [Atualizar serviço Web](how-to-deploy-update-web-service.md)
* [Como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Use o TLS para proteger um serviço Web por meio do Azure Machine Learning](how-to-secure-web-service.md)
* [Monitore seus modelos de Azure Machine Learning com Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
* [Criar alertas de eventos e gatilhos para implantações de modelo](how-to-use-event-grid.md)
