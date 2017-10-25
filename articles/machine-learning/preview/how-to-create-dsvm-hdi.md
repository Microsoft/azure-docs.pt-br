---
title: "Como criar DSVM e HDI como destinos de computação para o Azure ML"
description: "Crie cluster DSVM e Spark HDI como destinos de computação para experimentação do Azure ML."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 7325d15cc4bec5f0df50be222e1f0988775ded2c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-dsvm-and-hdi-spark-cluster-as-compute-targets"></a>Criar cluster DSVM e Spark HDI como destinos de computação

Você pode facilmente aumentar ou expandir seu experimento de aprendizado de máquina adicionando destinos de computação adicionais como DSVM (máquina virtual de ciência de dados) com base no Ubuntu e o Apache Spark para cluster Azure HDInsight. Este artigo apresenta as etapas de criação desses destinos de computação no Azure. Para obter mais informações sobre destinos de computação do Azure ML, consulte [visão geral do serviço de execução do experimento do Azure Machine Learning](experiment-execution-configuration.md).

>[!NOTE]
>Você precisa garantir que tenha as permissões adequadas para criar recursos, como clusters de VM e HDI no Azure antes de continuar. Também ambos esses recursos podem consumir vários núcleos de computação dependendo da configuração. Verifique se sua assinatura tem capacidade suficiente para os núcleos de CPU virtual. Você sempre pode entrar em contato com o suporte do Azure para aumentar o número máximo de núcleos permitido em sua assinatura.

## <a name="create-an-ubuntu-dsvm-in-azure-portal"></a>Criar uma DSVM do Ubuntu no portal do Azure

Você pode criar uma DSVM pelo portal do Azure. 

1. Faça logon no portal do Azure em https://portal.azure.com
2. Clique no link **+NEW** e pesquise “máquina virtual de ciência de dados para Linux”.
    ![Ubuntu](media/how-to-create-dsvm-hdi/ubuntu_dsvm.png)
4. Escolha **Máquina virtual de ciência de dados para Linux (Ubuntu)** na lista e siga as instruções na tela para criar a DSVM.

>[!IMPORTANT]
>Certifique-se de escolher **Senha** como _Tipo de autenticação_.

![use pwd](media/how-to-create-dsvm-hdi/use_pwd.png)

## <a name="create-an-ubuntu-dsvm-using-azure-cli"></a>Criar uma DSVM do Ubuntu usando azure-cli

Você também pode usar um modelo de gerenciamento de recursos do Azure para implantar uma DSVM.

>[!NOTE]
>Todos os comandos a seguir devem ser presumidos emitidos na pasta raiz de um projeto do Azure ML.

Primeiro, crie um arquivo `mydsvm.json` usando seu editor de texto favorito na pasta `docs`. (Se você não tiver uma pasta `docs` na pasta raiz do projeto, crie uma.) Usamos esse arquivo para configurar alguns parâmetros básicos para o modelo de gerenciamento de recursos do Azure. 

Copie e cole o trecho JSON a seguir para o arquivo `mydsvm.json` e preencha os valores apropriados:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "adminUsername": { "value" : "<admin username>"},
     "adminPassword": { "value" : "<admin password>"},
     "vmName": { "value" : "<vm name>"},
     "vmSize": { "value" : "<vm size>"}
  }
}
```

Para o campo _vmSize_, você pode usar qualquer tamanho de VM com suporte listado no [modelo de gerenciamento de recursos do Azure para DSVM do Ubuntu](https://github.com/Azure/DataScienceVM/blob/master/Scripts/CreateDSVM/Ubuntu/multiazuredeploywithext.json). Recomendamos que você use um dos tamanhos a seguir como destinos de computação para o Azure ML. 

- Standard_DS2_v2 
- Standard_DS3_v2 
- Standard_DS4_v2 
- Standard_DS12_v2 
- Standard_DS13_v2 
- Standard_DS14_v2 
- Standard_NC6 
- Standard_NC12 
- Standard_NC24 
 
>[!TIP]
> Os tamanhos de VM iniciados com "NC" são aqueles equipados com GPU.

Saiba mais sobre esses [tamanhos para máquinas virtuais Linux no Azure](../../virtual-machines/linux/sizes.md) e as respectivas [informações de preço](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Inicie a janela da CLI pelo aplicativo do Azure ML Workbench clicando em **Arquivo** --> **Abrir o prompt de comando** ou no item do menu **Abrir PowerShell**. 

>[!NOTE]
>Você também pode fazer isso em qualquer ambiente de linha de comando em que tenha az-cli instalado.

Na janela de linha de comando, digite os comandos abaixo:

```azurecli
# first make sure you have a valid Azure authentication token
$ az account get-access-token

# if you don't have a valid token, please log in to Azure first. 
# if you already do, you can skip this step.
$ az login

# list all subscriptions you have access to
$ az account list -o table

# make sure you set the subscription you want to use to create DSVM as the current subscription
$ az account set -s <subscription name or Id>

# it is always a good idea to create a resource group for the VM and associated resources to live in.
# you can use any Azure region, but it is best to create them in the region where your Azure ML Experimentation account is, e.g. eastus2, westcentralus or australiaeast.
# also, only certain Azure regions has GPU-equipped VMs available.
$ az group create -n <resource group name> -l <azure region>

# now let's create the DSVM based on the JSON configuration file you created earlier.
# note we assume the mydsvm.json config file is placed in the "docs" sub-folder.
$ az group deployment create -g <resource group name> --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/azuredeploy.json --parameters @docs/mydsvm.json

# find the FQDN (fully qualified domain name) of the VM just created. 
# you can also use IP address from the next command if FQDN is not set.
$ az vm show -g <resource group name> -n <vm name> --query "fqdns"

# find the IP address of the VM just created
$ az vm show -g <resource group name> -n <vm name> --query "publicIps"
```
## <a name="attach-a-dsvm-compute-target"></a>Anexar um destino de computação da DSVM
Uma vez criada a DSVM, você poderá anexá-la ao seu projeto do Azure ML.

```azurecli
# attach the DSVM compute target
# it is a good idea to use FQDN in case the IP address changes after you deallocate the VM and restart it
$ az ml computetarget attach --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password> --type remotedocker

# prepare the Docker image on the DSVM 
$ az ml experiment prepare -c <compute target name>
```
Agora você deve estar pronto para executar experimentos nessa DSVM.

## <a name="deallocate-a-dsvm-and-restart-it-later"></a>Desalocar uma DSVM e reiniciá-la mais tarde
Quando você concluir as tarefas de computação do Azure ML, poderá desalocar a DSVM. Esse procedimento desliga a VM, libera os recursos de computação, mas preserva os discos virtuais. Você não receberá uma cobrança pelo custo da computação quando a VM for desalocada.

Para desalocar uma VM:

```azurecli
$ az vm deallocate -g <resource group name> -n <vm name>
```

Para reativar a VM, use o comando `az ml start`:

```azurecli
$ az vm start -g <resource group name> -n <vm name>
```

## <a name="expand-the-dsvm-os-disk"></a>Expandir o disco do SO da DSVM
A VM Linux no Azure geralmente vem com um disco de sistema operacional de 30 GB. Quando usado como destino de computação para o Azure ML, ele pode ser consumido rapidamente pelo mecanismo do Docker puxando imagens do Docker e criando camadas de conda sobre ele. É uma boa ideia aumentar o tamanho do disco do sistema operacional (como 200 GB) para evitar o erro de "disco cheio" quando estiver no meio de uma execução. Consulte [Como expandir os discos rígidos virtuais em uma VM Linux com a CLI do Azure](../../virtual-machines/linux/expand-disks.md) para saber como fazer isso facilmente na azure-cli. 

## <a name="create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal"></a>Criar um Apache Spark para cluster Azure HDInsight no portal do Azure

Para executar trabalhos de expansão do Spark, você precisa criar um Apache Spark para cluster Azure HDInsight no portal do Azure.

1. Faça logon no portal do Azure em https://portal.azure.com
2. Clique no link **+NEW** e procure "HDInsight".

    ![localizar hdi](media/how-to-create-dsvm-hdi/hdi.png)
    
3. Escolha **HDInsight** na lista e, em seguida, clique no botão **Criar**.
4. Na tela de configuração **Noções básicas**, definições de **Tipo de cluster**, escolha **Spark** como _Tipo de cluster_, **Linux** como _Sistema operacional_ e **Spark 2.1.0 (HDI 3.6)** como _Version.

    ![configurar hdi](media/how-to-create-dsvm-hdi/configure_hdi.png)

    >[!IMPORTANT]
    >Observe na tela acima que o cluster tem um campo de _Nome de usuário de logon do cluster_ e um campo de _Nome de usuário do Secure Shell (SSH)_. Essas são duas identidades de usuário diferentes, apesar de você poder especificar a mesma senha para ambos os logons por questões de conveniência. O _Nome de usuário de logon do cluster_ é usado para fazer logon na interface do usuário da Web do cluster HDI. O _Nome de usuário de logon do SSH_ é usado para fazer logon no nó de cabeçalho do cluster e isso é o que é necessário para o Azure ML enviar trabalhos do Spark.

5. Escolha o tamanho do cluster e o tamanho do nó necessários e conclua o assistente de criação. Pode levar até 30 minutos para o cluster concluir o provisionamento. 

## <a name="attach-an-hdi-spark-cluster-compute-target"></a>Anexar um destino de computação de cluster Spark HDI

Uma vez criado o cluster Spark HDI, você poderá anexá-lo ao seu projeto do Azure ML.

```azurecli
# attach the HDI compute target
$ az ml computetarget attach --name <compute target name> --address <cluster name, such as myhdicluster123.azurehdinsight.net> --username <ssh username> --password <ssh password> --type cluster

# prepare the conda environment on HDI
$ az ml experiment prepare -c <compute target name>
```
Agora você deve estar pronto para executar experimentos nesse cluster Spark.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:
- [Visão geral do serviço de execução de teste do Microsoft Azure Machine Learning](experiment-execution-configuration.md)
- [Arquivos de configuração de execução do Azure Machine Learning Workbench](experiment-execution-configuration-reference.md)
- [Apache Spark para cluster Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/)
- [Máquina Virtual de Ciência de Dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
