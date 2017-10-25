---
title: "Instalação e configuração do Gerenciamento de Modelos do Azure Machine Learning | Microsoft Docs"
description: "Este documento descreve as etapas e os conceitos envolvidos na instalação e na configuração do Gerenciamento de Modelos no Azure Machine Learning."
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 08/29/2017
ms.openlocfilehash: c89596a6d721c4cba899b8a6e2859ee36cba7b80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="model-management-setup"></a>Configuração do gerenciamento de modelos

## <a name="overview"></a>Visão geral
Este documento mostra como usar o Gerenciamento de Modelos do Azure ML para implantar e gerenciar seus modelos de Machine Learning como serviços Web. 

Usando o gerenciamento de modelos de ML do Azure, você pode implantar e gerenciar com eficiência modelos de Machine Learning criados usando várias estruturas, incluindo SparkML, Keras, TensorFlow, Kit de Ferramentas Cognitivas da Microsoft ou Python. 

Ao final deste documento, seu ambiente de gerenciamento de modelos deverá estar configurado e pronto para implantar seus modelos do Machine Learning.

## <a name="what-you-need-to-get-started"></a>Para começar, você precisa do seguinte:
Para aproveitar este guia ao máximo, você deve ter acesso de proprietário a uma assinatura do Azure na qual você possa implantar seus modelos.
A CLI vem pré-instalada no Azure Machine Learning Workbench e nas [DSVMs do Azure](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).

## <a name="using-the-cli"></a>Usando a CLI
Para usar as CLIs (interfaces de linha de comando) do Workbench, clique em **Arquivo** -] **Abrir interface de linha de comando**. 

Em uma Máquina Virtual de Ciência de Dados, conecte-se e abra o prompt de comando. Digite `az ml -h` para ver as opções. Para obter mais detalhes sobre os comandos, use o sinalizador --help.

Em todos os outros sistemas, você precisa instalar as CLIs.

### <a name="installing-or-updating-on-windows"></a>Instalando (ou atualizando) no Windows

Instale o Python de https://www.python.org/. Verifique se você selecionou a instalação do pip.

Abra um prompt de comando usando Executar como administrador e execute os seguintes comandos:

```cmd
pip install azure-cli
pip install azure-cli-ml
```
 
>[!NOTE]
>Se você tiver uma versão anterior, desinstale-a primeiro usando o seguinte comando:
>

```cmd
pip uninstall azure-cli-ml
```

### <a name="installing-or-updating-on-linux"></a>Instalando (ou atualizando) no Linux
Execute o seguinte comando na linha de comando e siga os prompts:

```bash
sudo -i
pip install azure-cli
pip install azure-cli-ml
```

Após a conclusão da instalação, execute o seguinte comando:

```bash
sudo /opt/microsoft/azureml/initial_setup.sh
```

>[!NOTE]
>Faça logoff e logon novamente com sua sessão SSH para que as alterações entrem em vigor.
>Você pode usar os comandos anteriores para atualizar uma versão anterior das CLIs na DSVM.
>

## <a name="deploying-your-model"></a>Implantando seu modelo
Use as CLIs para implantar modelos como serviços Web. Os serviços Web podem ser implantados localmente ou em um cluster.

Comece com uma implantação local, valide que o modelo e o código funcionam e, em seguida, implante em um cluster para uso em escala de produção.

Para começar, você precisa configurar seu ambiente de implantação. A configuração do ambiente é uma tarefa de ocorrência única. Quando a instalação estiver concluída, você poderá reutilizar o ambiente para as próximas implantações. Consulte a seção a seguir para obter mais detalhes.

Ao concluir a configuração do ambiente:
- Será solicitado que você entre no Azure. Para entrar, use um navegador da Web para abrir a página https://aka.ms/devicelogin e insira o código fornecido para autenticar.
- Durante o processo de autenticação, será solicitado que você se autentique com uma conta. Importante: selecione uma conta que tenha uma assinatura válida do Azure e permissões suficientes para criar recursos na conta.- Quando o logon estiver concluído, as informações da sua assinatura serão apresentadas e você será consultado sobre se deseja continuar com a conta selecionada.

### <a name="environment-setup"></a>Configuração do ambiente
Para iniciar o processo de instalação, você precisa registrar o provedor de ambiente inserindo o seguinte comando:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
```

#### <a name="local-deployment"></a>Implantação local
Para implantar e testar o serviço Web no computador local, configure um ambiente local usando o comando a seguir:

```azurecli
az ml env setup -l [location of Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>A implantação do serviço Web local requer que você instale o Docker no computador local. 
>

O comando de instalação do ambiente local cria os seguintes recursos na sua assinatura:
- Um grupo de recursos (se não tiver sido fornecido)
- Uma conta de armazenamento
- Um ACR (Registro de Contêiner do Azure)
- Application insights

Após a configuração ser concluída com êxito, defina o ambiente a ser usado com o seguinte comando:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Implantação de cluster
Use a implantação de Cluster para cenários de produção de grande escala. Ele configura um cluster do ACS com o Kubernetes como o orquestrador. O cluster do ACS pode ser expandido para lidar com a maior taxa de transferência para suas chamadas de serviço Web.

Para implantar o serviço Web em um ambiente de produção, primeiro configure o ambiente usando o seguinte comando:

```azurecli
az ml env setup -c --name [your environment name] --location [Azure region e.g. eastus2] [-g [resource group]]
```

O comando de instalação do ambiente de cluster cria os seguintes recursos na sua assinatura:
- Um grupo de recursos (se não tiver sido fornecido)
- Uma conta de armazenamento
- Um ACR (Registro de Contêiner do Azure)
- Uma implantação do Kubernetes em um cluster do ACS (Serviço de Contêiner do Azure)
- Application insights

O grupo de recursos, a conta de armazenamento e o ACR são criados rapidamente. A implantação do ACS pode levar até 20 minutos. 

Após a conclusão da instalação, você precisa definir o ambiente a ser usado para essa implantação. Use o seguinte comando:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> Depois que o ambiente for criado, para implantações subsequentes, você somente precisa usar o comando definido acima para reutilizá-lo.
>

>[!NOTE] 
>Para criar um ponto de extremidade HTTPS, especifique um certificado SSL ao criar um cluster usando as opções --cert-name e --cert-pem na configuração az ml env. Isso configura o cluster para atender a solicitações em https, protegidas por meio do certificado fornecido. Após a conclusão da instalação, crie um registro de DNS CNAME que aponta para o FQDN do cluster.

### <a name="create-an-account"></a>Criar uma conta
É necessário ter uma conta para a implantação de modelos. É necessário fazer isso uma vez para cada conta e você pode reutilizar a mesma conta em várias implantações.

Para criar uma nova conta, use o comando a seguir:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

Para usar uma conta existente, use o seguinte comando:
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

### <a name="deploy-your-model"></a>Implantar o seu modelo
Agora você está pronto para implantar seu modelo salvo como um serviço Web. 

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```

### <a name="next-steps"></a>Próximas etapas
Experimente uma das diversas amostras na Galeria.
