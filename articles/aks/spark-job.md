---
title: Executar um trabalho do Apache Spark com o AKS (Serviço de Kubernetes do Azure)
description: Utilizar o AKS (Serviço de Kubernetes do Azure) para executar um trabalho do Apache Spark
services: container-service
author: lenadroid
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/15/2018
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: ddaff590fd493b430a72c30dd35cb1b891b80d84
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205344"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Executar trabalhos do Apache Spark no AKS

O [Apache Spark][apache-spark] é um mecanismo rápido para processamento de dados em grande escala. A partir do [Spark versão 2.3.0][spark-latest-release], o Apache Spark fornece suporte para integração nativa com clusters de Kubernetes. O AKS (Serviço de Kubernetes do Azure) é um ambiente Kubernetes gerenciado em execução no Azure. Este documento detalha a preparação e execução de trabalhos do Apache Spark em um cluster do AKS (Serviço de Kubernetes do Azure).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste artigo, você precisa dos itens a seguir.

* Reconhecimento básico de Kubernetes e [Apache Spark][spark-quickstart].
* Conta de [Hub do Docker][docker-hub] ou um [Registro de Contêiner do Azure][acr-create].
* CLI do Azure [instalada][azure-cli] no sistema de desenvolvimento.
* [JDK 8][java-install] instalado no sistema.
* SBT ([Ferramenta de Compilação Scala][sbt-install]) instalada no sistema.
* Ferramentas de linha de comando do Git instaladas no sistema.

## <a name="create-an-aks-cluster"></a>Criar um cluster AKS

O Spark é utilizado para processamento de dados em grande escala e exige que os nós de Kubernetes sejam dimensionados para atender aos requisitos dos recursos do Spark. É recomendável um tamanho mínimo de `Standard_D3_v2` para os nós do AKS (Serviço de Kubernetes do Azure).

Se for necessário um cluster do AKS que atenda a essa recomendação mínima, execute os comandos a seguir.

Crie um grupo de recursos para o cluster.

```azurecli
az group create --name mySparkCluster --location eastus
```

Crie o cluster do AKS com nós de tamanho `Standard_D3_v2`.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2
```

Conecte o cluster do AKS.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Se você estiver utilizando o ACR (Registro de Contêiner do Azure) para armazenar imagens de contêiner, configure a autenticação entre o AKS e o ACR. Consulte a [Documentação de autenticação do ACR][acr-aks] para essas etapas.

## <a name="build-the-spark-source"></a>Compilar a fonte do Spark

Antes de executar tarefas do Spark em um cluster do AKS, será necessário compilar o código-fonte do Spark e empacotá-lo em uma imagem do contêiner. A fonte do Spark inclui scripts que podem ser utilizados para concluir esse processo.

Clone o repositório do projeto do Spark no sistema de desenvolvimento.

```bash
git clone -b branch-2.3 https://github.com/apache/spark
```

Altere para o diretório do repositório clonado e salve o caminho da fonte do Spark para uma variável.

```bash
cd spark
sparkdir=$(pwd)
```

Se houver várias versões do JDK instaladas, defina `JAVA_HOME` para usar a versão 8 para a sessão atual.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Execute o comando a seguir para criar o código-fonte do Spark com o suporte de Kubernetes.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Os comandos a seguir criam a imagem do contêiner do Spark e a enviam para um registro de imagens de contêiner. Substitua `registry.example.com` pelo nome do registro do contêiner e `v1` pela marca que você prefere usar. Se estiver usando o Hub do Docker, esse valor será o nome do registro. Se estiver usando o ACR (Registro de Contêiner do Azure), esse valor será o nome do servidor de logon do ACR.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Efetue push da imagem do contêiner para o registro de imagem do contêiner.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Preparar um trabalho do Spark

Em seguida, prepare um trabalho do Spark. Um arquivo Jar é usado para manter o trabalho do Spark e é necessário ao executar o comando `spark-submit`. O Jar pode ser disponibilizado por meio de uma URL pública ou pré-empacotado em uma imagem de contêiner. Neste exemplo, um Jar de amostra é criado para calcular o valor de Pi. Esse Jar é então carregado para o armazenamento do Azure. Se houver um Jar existente, será possível substituir

Crie um diretório onde você deseja criar o projeto para um trabalho do Spark.

```bash
mkdir myprojects
cd myprojects
```

Crie um novo projeto do Scala a partir de um modelo.

```bash
sbt new sbt/scala-seed.g8
```

Quando solicitado, insira `SparkPi` para o nome do projeto.

```bash
name [Scala Seed Project]: SparkPi
```

Navegue até o diretório do projeto criado recentemente.

```bash
cd sparkpi
```

Execute os comandos a seguir para adicionar um plug-in de SBT, que permite empacotar o projeto como um arquivo Jar.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.6")' >> project/assembly.sbt
```

Execute esses comandos para copiar o código de exemplo no projeto criado recentemente e inclua todas as dependências necessárias.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11",/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

Para empacotar o projeto em um Jar, execute o comando a seguir.

```bash
sbt assembly
```

Após o empacotamento concluído com êxito, você deverá ver uma saída semelhante à seguinte.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Copiar trabalho para armazenamento

Crie um contêiner e uma conta de armazenamento do Azure para manter o arquivo Jar.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Carregue o arquivo Jar na conta de armazenamento do Azure com os comandos a seguir.

```bash
CONTAINER_NAME=jars
BLOB_NAME=SparkPi-assembly-0.1.0-SNAPSHOT.jar
FILE_TO_UPLOAD=target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar

echo "Creating the container..."
az storage container create --name $CONTAINER_NAME
az storage container set-permission --name $CONTAINER_NAME --public-access blob

echo "Uploading the file..."
az storage blob upload --container-name $CONTAINER_NAME --file $FILE_TO_UPLOAD --name $BLOB_NAME

jarUrl=$(az storage blob url --container-name $CONTAINER_NAME --name $BLOB_NAME | tr -d '"')
```

A variável `jarUrl` agora contém o caminho publicamente acessível para o arquivo Jar.

## <a name="submit-a-spark-job"></a>Enviar um trabalho do Spark

Inicie kube-proxy em uma linha de comando separada com o código a seguir.

```bash
kubectl proxy
```

Navegue de volta para a raiz do repositório do Spark.

```bash
cd $sparkdir
```

Envie o trabalho utilizando `spark-submit`.

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Essa operação inicia o trabalho do Spark, que envia o status do trabalho para a sessão do Shell. Enquanto o trabalho estiver em execução, será possível ver o pod do driver do Spark e os pods executores usando o comando kubectl get pods. Abra uma segunda sessão de terminal para executar esses comandos.

```console
$ kubectl get pods

NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Enquanto o trabalho estiver em execução, também será possível acessar a interface do usuário do Spark. Na segunda sessão de terminal, utilize o comando `kubectl port-forward` para fornecer acesso à interface do usuário do Spark.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Para acessar a interface do usuário do Spark, abra o endereço `127.0.0.1:4040` em um navegador.

![Interface do usuário do Spark](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Obter logs e resultados do trabalho

Após a conclusão do trabalho, o pod de driver estará no estado "Concluído". Obtenha o nome do pod com o comando a seguir.

```bash
kubectl get pods --show-all
```

Saída:

```bash
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Utilize o comando `kubectl logs` para obter logs do pod de driver do Spark. Substitua o nome do pod pelo nome do pod de driver.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

Dentro desses logs, é possível visualizar o resultado do trabalho do Spark, que é o valor de Pi.

```bash
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Empacotar Jar com imagem de contêiner

No exemplo acima, o arquivo Jar do Spark foi carregado no armazenamento do Azure. Outra opção é empacotar o arquivo Jar em imagens do Docker personalizadas internas.

Para fazer isso, localize o diretório `dockerfile` da imagem do Spark localizada em `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/`. Adicione uma instrução `ADD` para o trabalho do Spark `jar` em algum lugar entre as declarações `WORKDIR` e `ENTRYPOINT`.

Atualize o caminho do Jar para o local do arquivo `SparkPi-assembly-0.1.0-SNAPSHOT.jar` no sistema de desenvolvimento. Também é possível utilizar seu próprio arquivo Jar personalizado.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Compilar e efetuar push da imagem com os scripts do Spark incluídos.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

Ao executar o trabalho, em vez de indicar uma URL do Jar remota, o esquema `local://` pode ser usado com o caminho para o arquivo Jar na imagem do Docker.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> Do Spark [documentação][spark-docs]: "O Agendador Kubernetes está atualmente experimental. Em versões futuras, pode haver alterações de comportamento em torno de configuração, imagens de contêiner e pontos de entrada".

## <a name="next-steps"></a>Próximas etapas

Faça check-out da documentação do Spark para obter mais detalhes.

> [!div class="nextstepaction"]
> [Documentação do Spark][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: https://aka.ms/azure-jdks
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-latest-release]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks
[acr-create]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli
[aks-quickstart]: https://docs.microsoft.com/azure/aks/
[azure-cli]: https://docs.microsoft.com/cli/azure/?view=azure-cli-latest
[storage-account]: https://docs.microsoft.com/azure/storage/common/storage-azure-cli
