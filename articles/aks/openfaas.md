---
title: Usar OpenFaaS com o AKS (Serviço de Kubernetes do Azure)
description: Saiba como implantar e usar o OpenFaaS em um cluster do AKS (serviço kubernetes do Azure) para criar funções sem servidor com contêineres.
author: justindavies
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 319107127b79383fc3b49f0eeb856a0e6c5b09f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92747758"
---
# <a name="using-openfaas-on-aks"></a>Usando o OpenFaaS no AKS

[OpenFaaS][open-faas] é uma estrutura para a criação de funções sem servidor por meio do uso de contêineres. Como um projeto de software livre, conquistou adoção em larga escala dentro da comunidade. Este documento fornece detalhes sobre como instalar e usar o OpenFaas em um cluster do AKS (Serviço de Kubernetes do Azure).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste artigo, você precisa dos itens a seguir.

* Noções básicas sobre o Kubernetes.
* Um cluster do AKS (Serviço de Kubernetes do Azure) e credenciais do AKS configuradas no sistema de desenvolvimento.
* CLI do Azure instalada no sistema de desenvolvimento.
* Ferramentas de linha de comando do Git instaladas no sistema.

## <a name="add-the-openfaas-helm-chart-repo"></a>Adicionar o repositório de gráfico OpenFaaS Helm

Acesse [https://shell.azure.com](https://shell.azure.com) para abrir o Azure Cloud Shell em seu navegador.

O OpenFaaS mantém seus próprios gráficos de Helm para manter-se atualizado com todas as alterações mais recentes.

```console
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>Implantar o OpenFaaS

Como uma boa prática, o OpenFaaS e as funções do OpenFaaS devem ser armazenados em seu próprio namespace do Kubernetes.

Crie um namespace para o sistema e as funções do OpenFaaS:

```console
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Gere uma senha para o portal de interface do usuário do OpenFaaS e a API REST:

```console
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

Você pode obter o valor do segredo com `echo $PASSWORD` .

A senha que criamos aqui será usada pelo gráfico do Helm para habilitar a autenticação básica no gateway OpenFaaS, que é exposta à Internet por meio de um balanceador de nuvem.

Um gráfico do Helm para o OpenFaaS está incluído no repositório clonado. Use este gráfico para implantar o OpenFaaS no cluster do AKS.

```console
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
```

Saída:

```output
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

```console
kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

Um endereço IP público é criado para acessar o gateway OpenFaaS. Para recuperar esse endereço IP, use o comando [kubectl get service][kubectl-get]. Pode levar um minuto até que o endereço IP seja atribuído ao serviço.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Saída.

```output
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Para testar o sistema OpenFaaS, navegue para o endereço IP externo na porta 8080, `http://52.186.64.52:8080`, neste exemplo. Você será solicitado a fazer logon. Para buscar sua senha, digite `echo $PASSWORD` .

![Interface do usuário do OpenFaaS](media/container-service-serverless/openfaas.png)

Por fim, instale a CLI do OpenFaaS. Este exemplo usou o Brew. Confira a [documentação da CLI do OpenFaaS][open-faas-cli] para obter mais opções.

```console
brew install faas-cli
```

Definido `$OPENFAAS_URL` como o IP público encontrado acima.

Faça logon com o CLI do Azure:

```console
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Criar a primeira função

Agora que o OpenFaaS está funcionando, crie uma função usando o portal do OpenFaas.

Clique em **Implantar Nova Função** e pesquise **Figlet**. Selecione a função do Figlet e clique em **Implantar**.

![Captura de tela mostra a caixa de diálogo implantar uma nova função com o texto FIGlet na linha de pesquisa.](media/container-service-serverless/figlet.png)

Use o Curl para invocar a função. Substitua o endereço IP no exemplo a seguir pelo endereço IP do gateway do OpenFaas.

```console
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Saída:

```output
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Criar a segunda função

Agora crie uma segunda função. Este exemplo será implantado por meio da CLI do OpenFaaS e da recuperação de dados de um Cosmos DB e inclui uma imagem de contêiner personalizada. Vários itens precisam ser configurados antes da criação da função.

Primeiro, crie um novo grupo de recursos para o Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Implante uma instância do CosmosDB do tipo `MongoDB`. A instância precisa de um nome exclusivo. Portanto, atualize `openfaas-cosmos` para algo exclusivo no ambiente.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Obtenha a cadeia de conexão de banco de dados do Cosmos e armazene-a em uma variável.

Atualize o valor para o argumento `--resource-group` para o nome do grupo de recursos e o argumento `--name` para o nome do Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Agora, popule o Cosmos DB com os dados de teste. Crie um arquivo chamado `plans.json` e copie-o para o JSON a seguir.

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

Use a ferramenta *mongoimport* para carregar a instância do CosmosDB com os dados.

Se necessário, instale as ferramentas do MongoDB. O exemplo a seguir instala essas ferramentas por meio do Brew; consulte a [documentação do MongoDB][install-mongo] para obter outras opções.

```console
brew install mongodb
```

Carregue os dados no banco de dados.

```console
mongoimport --uri=$COSMOS -c plans < plans.json
```

Saída:

```output
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Execute o comando a seguir para criar a função. Atualize o valor do argumento `-g` com o endereço do gateway do OpenFaaS.

```console
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Depois de implantado, você deverá ver o ponto de extremidade do OpenFaaS recém-criado para a função.

```output
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Teste a função usando o Curl. Atualize o endereço IP com o endereço do gateway do OpenFaaS.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Saída:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Você também pode testar a função na interface do usuário do OpenFaaS.

![texto alternativo](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Próximas etapas

Você pode continuar a aprender com o workshop OpenFaaS por meio de um conjunto de laboratórios práticos que abordam tópicos como, por exemplo, como criar seu próprio bot do GitHub, consumindo segredos, exibindo métricas e dimensionamento automático.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
