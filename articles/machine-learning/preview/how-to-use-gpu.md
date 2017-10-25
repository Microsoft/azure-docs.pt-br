---
title: Como usar a GPU para o Azure Machine Learning | Microsoft Docs
description: "Este artigo descreve como usar GPUs (Unidades de Processamento Gráfico) para treinar redes neurais profundas no Azure Machine Learning Workbench."
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 7c0dab5ab2d7342ce4de8899040478a6143c184b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-gpu-in-azure-machine-learning"></a>Como usar a GPU no Azure Machine Learning
A GPU (Unidade de Processamento Gráfico) é amplamente usada para processar tarefas de computação intensa que geralmente podem acontecer durante o treinamento de alguns modelos de rede neural profunda. Ao usar as GPUs, você pode reduzir significativamente o tempo de treinamento dos modelos. Neste documento, você aprenderá a configurar o Azure ML Workbench para usar [DSVM (Máquina Virtual de Ciência de Dados)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) equipada com GPUs como destino de execução. 

## <a name="prerequisites"></a>Pré-requisitos
- Para percorrer este guia de instruções, você precisa primeiro [instalar o Azure ML Workbench](quickstart-installation.md).
- Você precisa ter acesso a computadores equipados com GPUs NVidia.
    - Você pode executar os scripts diretamente no computador local (Windows ou macOS) com GPUs.
    - Você também pode executar scripts em um contêiner do Docker em um computador com GPUs.

## <a name="execute-in-local-environment-with-gpus"></a>Executar em Ambiente _local_ com GPUs
Você pode instalar o Azure ML Workbench em um computador equipado com GPUs e executar em um ambiente _local_. Isso pode ser:
- Um computador físico Windows ou macOS.
- Uma VM (Máquina Virtual) do Windows, como uma Windows DSVM provisionada usando o modelo de VMs série NC do Azure.

Nesse caso, nenhuma configuração especial é necessária no Azure ML Workbench. Apenas verifique se que você tem todos os drivers, kits de ferramentas e bibliotecas de aprendizado de máquina habilitadas para GPU necessários instalados localmente. Simplesmente execute com relação ao ambiente _local_ em que o tempo de execução do Python pode acessar diretamente o hardware da GPU local.

1. Abra o AML Workbench. vá para **Arquivo** e **Abrir o Prompt de Comando**. 
2. Na linha de comando, instale a estrutura de machine learning habilitada para GPU, como o Kit de Ferramentas Cognitivas Microsoft, o TensorFlow etc. Por exemplo:

```batch
REM install latest TensorFlow with GPU support
C:\MyProj> pip install tensorflow-gpu

REM install Microsoft Cognitive Toolkit 2.1 (1-bit SGD) with GPU support on Windows
C:\MyProj> pip install https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-win_amd64.whl
```

3. Grave código do Python que aproveite as bibliotecas de aprendizado profundo.
4. Escolha _local_ como o ambiente de computação e execute o código Python.

```batch
REM execute Python script in local environment
C:\MyProj> az ml experiment submit -c local my-deep-learning-script.py
```

## <a name="execute-in-docker-environment-on-linux-vm-with-gpus"></a>Executar no Ambiente do _docker_ na VM do Linux com GPUs
O Workbench de ML do Azure também dá suporte à execução no Docker em uma VM Linux do Azure. Aqui, você tem uma ótima opção para executar trabalhos de computação intensa em um hardware virtual eficiente e pagar somente pelo uso desativando o recurso quando terminar. Embora, em princípio, seja possível usar GPUs em qualquer máquina virtual do Linux, a DSVM com base em Ubuntu vem com os drivers CUDA e bibliotecas necessários pré-instalados, tornando a configuração muito mais fácil. Siga as etapas abaixo:

### <a name="create-a-ubuntu-based-linux-data-science-virtual-machine-in-azure"></a>Criar uma Máquina Virtual de Ciência de Dados do Linux baseada em Ubuntu no Azure
1. Abra seu navegador da Web e vá para o [portal do Azure](https://portal.azure.com)

2. Selecione **+ Novo** à esquerda do portal.

3. Pesquise “Máquina Virtual de Ciência de Dados para Linux (Ubuntu)” no marketplace.

4. Clique em **Criar** para criar uma DSVM Ubuntu.

5. Preencha o formulário **Básico** com as informações necessárias.
Ao selecionar o local para a sua VM, observe que as VMs de GPU estão disponíveis somente em determinadas regiões do Azure, por exemplo, **Centro Sul dos EUA**. Consulte [produtos de computação disponíveis por região](https://azure.microsoft.com/en-us/regions/services/).
Clique em OK para salvar as informações **Básicas**.

6. Escolha o tamanho da máquina virtual. Selecione um dos tamanhos de VMs prefixadas por NC, que são equipadas com chips NVidia GPU.  Clique em **Exibir Tudo** para ver a lista completa, conforme necessário. Saiba mais sobre [VMs do Azure equipadas com GPU](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes-gpu).

7. Conclua as configurações restantes e examine as informações de compra. Clique em Comprar para Criar a VM. Anote o endereço IP alocado à máquina virtual. 

### <a name="create-a-new-project-in-azure-ml-workbench"></a>Criar um novo projeto no Azure ML Workbench 
Você pode usar o exemplo de _Classificar MNIST usando TensorFlow_ ou o exemplo _Classificar conjunto de dados MNIST com CNTK_.

### <a name="create-a-new-compute-target"></a>Criar um novo destino de computação
Inicialize a linha de comando do Azure ML Workbench. Digite o seguinte comando. Substitua o texto de espaço reservado do exemplo a seguir pelos seus próprios valores para nome, endereço IP, nome de usuário e senha. 

```batch
C:\MyProj> az ml computetarget attach --name "my_dsvm" --address "my_dsvm_ip_address" --username "my_name" --password "my_password" --type remotedocker
```

### <a name="configure-azure-ml-workbench-to-access-gpu"></a>Configurar o Azure ML Workbench para acessar a GPU
Volte para o projeto e abra **Exibição de Arquivo** e clique no botão **Atualizar**. Agora você vê dois novos arquivos de configuração `my_dsvm.compute` e `my_dsvm.runconfig`.
 
Abra o `my_dsvm.compute`. Altere o `baseDockerImage` para `microsoft/mmlspark:plus-gpu-0.7.9` e adicione uma nova linha `nvidiaDocker: true`. Portanto, o arquivo deve ter estas duas linhas:
 
```yaml
...
baseDockerImage: microsoft/mmlspark:plus-gpu-0.7.91
nvidiaDocker: true
```
 
Agora, abra `my_dsvm.runconfig`, altere o valor `Framework` de `PySpark` para `Python`. Se você não visualizar essa linha, adicione-a, pois o valor padrão será `PySpark`.

```yaml
"Framework": "Python"
```
### <a name="reference-deep-learning-framework"></a>Consultar a estrutura de aprendizado profundo 
Abra o arquivo `conda_dependencies.yml` e verifique se você está usando a versão da GPU dos pacotes Python da estrutura de aprendizado profundo. Os projetos de exemplo listam as versões de CPU, então você precisa fazer essa alteração.

Exemplo de TensorFlow: 
```
name: project_environment
dependencies:
  - python=3.5.2
  # latest TensorFlow library with GPU support
  - tensorflow-gpu
```

Exemplo para o Kit de Ferramentas Cognitivas Microsoft:
```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip: 
    # use the Linux build of Microsoft Cognitive Toolkit 2.1 with GPU support
    - https://cntk.ai/PythonWheel/GPU/cntk-2.1-cp35-cp35m-win_amd64.whl
```

Você também pode usar a versão de SGD de 1 bit do Kit de Ferramentas Cognitivas Microsoft que oferece melhorias de desempenho em VMs de várias GPUs. Observe [o requisito de licença para SGD de 1 bit](https://docs.microsoft.com/en-us/cognitive-toolkit/cntk-1bit-sgd-license).

```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip:    
    # use the Linux build of the Microsoft Cognitive Toolkit 2.1 with 1-bit SGD and GPU support
    - https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

### <a name="execute"></a>Executar
Agora você está pronto para executar seus scripts Python. Você pode executá-los no Azure ML Workbench usando o contexto `my_dsvm` ou você pode inicializá-lo na linha de comando:
 
```batch
C:\myProj> az ml experiment submit -c my_dsvm my_tensorflow_or_cntk_script.py
```
 
Para verificar se a GPU é usada, examine a saída de execução para ver algo semelhante ao seguinte:

```
name: Tesla K80
major: 3 minor: 7 memoryClockRate (GHz) 0.8235
pciBusID 06c3:00:00.0
Total memory: 11.17GiB
Free memory: 11.11GiB
```

Parabéns! O script acaba de aproveitar a potência da GPU por meio de um contêiner do Docker!

## <a name="next-steps"></a>Próximas etapas
Veja um exemplo de como usar a GPU para acelerar o treinamento de rede neural profunda na Galeria do Azure ML.
