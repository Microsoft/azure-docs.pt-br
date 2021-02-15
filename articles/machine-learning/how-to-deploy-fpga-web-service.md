---
title: Implantar modelos de ML em FPGAs
titleSuffix: Azure Machine Learning
description: Saiba mais sobre matrizes de portais programáveis por campo. Você pode implantar um serviço Web em um FPGA com Azure Machine Learning para inferência de latência extremamente baixa.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 09/24/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, devx-track-python, deploy
ms.openlocfilehash: 9e43291325510f92f2e5fd6c07cdb9257fdede9d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033061"
---
# <a name="deploy-ml-models-to-field-programmable-gate-arrays-fpgas-with-azure-machine-learning"></a>Implantar modelos de ML em FPGAs (matrizes de porta programável por campo) com Azure Machine Learning 

Neste artigo, você aprenderá sobre o FPGAs e como implantar seus modelos de ML em um FPGA do Azure usando o [pacote de Python dos modelos acelerados por hardware](/python/api/azureml-accel-models/azureml.accel?preserve-view=true&view=azure-ml-py) da [Azure Machine Learning](overview-what-is-azure-ml.md).

## <a name="what-are-fpgas"></a>O que são FPGAs?
As FPGAs contêm uma matriz de blocos lógicos programáveis e uma hierarquia de interconexões reconfiguráveis. As interconexões permitem que esses blocos sejam configurados de várias maneiras após a fabricação. Comparado a outros chips, as FPGAs fornecem uma combinação de programação e desempenho. 

O FPGAs possibilita a obtenção de baixa latência para solicitações de inferência em tempo real (ou pontuação de modelo). As solicitações assíncronas (envio em lote) não são necessárias. O envio em lote pode causar latência, porque mais dados precisam ser processados. As implementações de unidades de processamento neural não exigem envio em lote; Portanto, a latência pode ser muitas vezes menor, em comparação com processadores de CPU e GPU.

Reconfigure as FPGAs para diferentes tipos de modelos de machine learning. Essa flexibilidade torna mais fácil acelerar os aplicativos baseados na precisão numérica mais ideal e o modelo de memória que está sendo usado. Como as FPGAs são reconfiguráveis, você pode ficar atualizado com os requisitos dos algoritmos de IA em rápida mudança.

![Diagrama de Azure Machine Learning comparação de FPGA](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Processador| Abreviação |Descrição|
|---|:-------:|------|
|Circuitos integrados específicos do aplicativo|ASICs|Os circuitos personalizados, como o TPU (tensor Processor Units) do Google, fornecem a mais alta eficiência. Elas não podem ser reconfiguradas conforme suas necessidades mudam.|
|Matrizes de portas programáveis em campo|FPGAs|As FPGAs, como aquelas disponíveis no Azure, fornecem desempenho aproximado ao dos ASICs. Eles também são flexíveis e reconfiguráveis ao longo do tempo, a fim de implementar a nova lógica.|
|Unidades de processamento gráfico|GPUs|Uma opção popular para cálculos de IA. As GPUs oferecem funcionalidades de processamento paralelo, tornando-as mais rápidas na renderização de imagens comparado às CPUs.|
|Unidades de processamento central|CPUs|Processadores de uso geral cujo desempenho não é ideal para processamento de elementos gráficos e vídeo.|

## <a name="fpga-support-in-azure"></a>Suporte do FPGA no Azure

O Microsoft Azure é o maior investimento de nuvem do mundo em FPGAs. A Microsoft usa FPGAs para avaliação de DNN (redes neurais profundas), classificação de pesquisa do Bing e aceleração de SDN (rede definida pelo software) para reduzir a latência, liberando CPUs para outras tarefas.

O FPGAs no Azure baseia-se nos dispositivos FPGA da Intel, que os cientistas de dados e os desenvolvedores usam para acelerar os cálculos de AI em tempo real. Essa arquitetura habilitada para FPGA oferece desempenho, flexibilidade e escala e está disponível no Azure.

Os FPGAs do Azure são integrados com o Azure Machine Learning. O Azure pode paralelizar o DNN pré-treinado em FPGAs para escalar horizontalmente seu serviço. As DNNs podem ser pré-treinadas, como um transformador de recursos profundo para transferência de aprendizado, ou ajustadas com pesos atualizados.

|Cenários & configurações no Azure|Modelos DNN com suporte|Suporte regional|
|--------------------------|--------------------|----------------|
|+ Cenários de classificação e reconhecimento de imagens<br/>+ Implantação de TensorFlow (requer Tensorflow 1. x)<br/>+ Hardware Intel FPGA|-ResNet 50<br/>-ResNet 152<br/>-DenseNet-121<br/>-VGG-16<br/>-SSD-VGG|-Leste dos EUA<br/>-Sudeste da Ásia<br/> – Europa Ocidental<br/>-Oeste dos EUA 2|

Para otimizar a latência e a taxa de transferência, o cliente que está enviando dados para o modelo FPGA deve estar em uma das regiões acima (aquela em que você implantou o modelo).

A **família PBS de VMs do Azure** contém o Intel Arria 10 FPGAs. Ele será mostrado como "Standard PBS Family vCPUs" quando você verificar sua alocação de cota do Azure. A VM PB6 tem seis vCPUs e uma FPGA. A VM PB6 é provisionada automaticamente por Azure Machine Learning durante a implantação do modelo em um FPGA. Ele só é usado com o Azure ML e não pode executar fragmentado arbitrários. Por exemplo, você não poderá atualizar o FPGA com fragmentado para fazer criptografia, codificação, etc.

## <a name="deploy-models-on-fpgas"></a>Implantar modelos em FPGAs

Você pode implantar um modelo como um serviço Web no FPGAs com [Azure Machine Learning modelos de aceleração de hardware](/python/api/azureml-accel-models/azureml.accel?preserve-view=true&view=azure-ml-py). O uso de FPGAs fornece inferência de latência ultra baixa, mesmo com um único tamanho de lote. 

Neste exemplo, você cria um grafo TensorFlow para pré-processar a imagem de entrada, torna-o um featurizer usando ResNet 50 em um FPGA e, em seguida, executa os recursos por meio de um classificador treinado no conjunto de dados ImageNet. Em seguida, o modelo é implantado em um cluster AKS.

### <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma, crie uma conta [paga conforme o uso](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go) (contas gratuitas do Azure não são elegíveis para a cota FPGA).

- Um espaço de trabalho Azure Machine Learning e o SDK do Azure Machine Learning para Python instalados, conforme descrito em [criar um espaço de trabalho](how-to-manage-workspace.md).
 
- O pacote de modelos de hardware acelerado:  `pip install --upgrade azureml-accel-models[cpu]`    
    
- A [CLI do Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)

- Cota de FPGA. Envie uma [solicitação de cota](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2nac9-PZhBDnNSV2ITz0LNUN0U5S0hXRkNITk85QURTWk9ZUUFUWkkyTC4u)ou execute este comando da CLI para verificar a cota: 

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

   Verifique se você tem pelo menos 6 vCPUs sob o __CurrentValue__ retornado.  

### <a name="define-the-tensorflow-model"></a>Definir o modelo TensorFlow

Comece usando o [SDK do Azure Machine Learning para Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) para criar uma definição de serviço. Uma definição de serviço é um arquivo que descreve um pipeline de grafos (entrada, recursos e classificador) com base no TensorFlow. O comando de implantação compacta a definição e os grafos em um arquivo ZIP e carrega o ZIP para o armazenamento de BLOBs do Azure. O DNN já está implantado para ser executado no FPGA.

1. Carregar Azure Machine Learning espaço de trabalho

   ```python
   import os
   import tensorflow as tf
   
   from azureml.core import Workspace
  
   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
   ```

1. Pré-processar imagem. A entrada para o serviço Web é uma imagem JPEG.  A primeira etapa é decodificar a imagem JPEG e pré-processar-la.  As imagens JPEG são tratadas como cadeias de caracteres e o resultado são os tempos que serão a entrada para o modelo ResNet 50.

   ```python
   # Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
   import azureml.accel.models.utils as utils
   tf.reset_default_graph()
   
   in_images = tf.placeholder(tf.string)
   image_tensors = utils.preprocess_array(in_images)
   print(image_tensors.shape)
   ```

1. Carregar featurizer. Inicialize o modelo e baixe um ponto de verificação TensorFlow da versão quantizada do ResNet50 para ser usado como um featurizer.  Substitua "QuantizedResnet50" no trecho de código para importar outras redes neurais profundas:

   - QuantizedResnet152
   - QuantizedVgg16
   - Densenet121

   ```python
   from azureml.accel.models import QuantizedResnet50
   save_path = os.path.expanduser('~/models')
   model_graph = QuantizedResnet50(save_path, is_frozen=True)
   feature_tensor = model_graph.import_graph_def(image_tensors)
   print(model_graph.version)
   print(feature_tensor.name)
   print(feature_tensor.shape)
   ```

1. Adicione um classificador. Este classificador foi treinado no conjunto de dados ImageNet.

   ```python
   classifier_output = model_graph.get_default_classifier(feature_tensor)
   print(classifier_output)
   ```

1. Salve o modelo. Agora que o pré-processador, ResNet 50 featurizer e o classificador foram carregados, salve o grafo e as variáveis associadas como um modelo.

   ```python
   model_name = "resnet50"
   model_save_path = os.path.join(save_path, model_name)
   print("Saving model in {}".format(model_save_path))
   
   with tf.Session() as sess:
       model_graph.restore_weights(sess)
       tf.saved_model.simple_save(sess, model_save_path,
                                  inputs={'images': in_images},
                                  outputs={'output_alias': classifier_output})
   ```

1. Economize tempos de entrada e de saída **, pois você os usará para conversão de modelo e solicitações de inferência**. 

   ```python
   input_tensors = in_images.name
   output_tensors = classifier_output.name

   print(input_tensors)
   print(output_tensors)
   ```

   Os modelos a seguir estão disponíveis listados com seus tempos de saída de classificador para inferência se você usou o classificador padrão.

   + Resnet50, QuantizedResnet50
     ```python
     output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
     ```
   + Resnet152, QuantizedResnet152
     ```python
     output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
     ```
   + Densenet121, QuantizedDensenet121
     ```python
     output_tensors = "classifier/densenet121/predictions/Softmax:0"
     ```
   + Vgg16, QuantizedVgg16
     ```python
     output_tensors = "classifier/vgg_16/fc8/squeezed:0"
     ```
   + SsdVgg, QuantizedSsdVgg
     ```python
     output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
     ```

### <a name="convert-the-model-to-the-open-neural-network-exchange-format-onnx"></a>Converter o modelo para o formato de troca de rede neural aberto (ONNX)

Antes de poder implantar no FPGAs, converta o modelo no formato [ONNX](https://onnx.ai/) .

1. [Registre](concept-model-management-and-deployment.md) o modelo usando o SDK com o arquivo zip no armazenamento de BLOBs do Azure. Adicionar marcas e outros metadados sobre o modelo ajuda você a controlar seus modelos treinados.

   ```python
   from azureml.core.model import Model

   registered_model = Model.register(workspace=ws,
                                     model_path=model_save_path,
                                     model_name=model_name)

   print("Successfully registered: ", registered_model.name,
         registered_model.description, registered_model.version, sep='\t')
   ```

   Se você já registrou um modelo e deseja carregá-lo, você pode recuperá-lo.

   ```python
   from azureml.core.model import Model
   model_name = "resnet50"
   # By default, the latest version is retrieved. You can specify the version, i.e. version=1
   registered_model = Model(ws, name="resnet50")
   print(registered_model.name, registered_model.description,
         registered_model.version, sep='\t')
   ```

1. Converta o grafo TensorFlow no formato ONNX.  Você deve fornecer os nomes dos tempos de entrada e de saída, para que o cliente possa usá-los quando você consumir o serviço Web.

   ```python
   from azureml.accel import AccelOnnxConverter

   convert_request = AccelOnnxConverter.convert_tf_model(
       ws, registered_model, input_tensors, output_tensors)

   # If it fails, you can run wait_for_completion again with show_output=True.
   convert_request.wait_for_completion(show_output=False)

   # If the above call succeeded, get the converted model
   converted_model = convert_request.result
   print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
         converted_model.id, converted_model.created_time, '\n')
   ```

### <a name="containerize-and-deploy-the-model"></a>Colocar em contêiner e implantar o modelo

Em seguida, crie uma imagem do Docker do modelo convertido e todas as dependências.  Essa imagem do Docker pode ser implantada e instanciada.  Os destinos de implantação com suporte incluem o AKS (serviço kubernetes do Azure) na nuvem ou em um dispositivo de borda, como [Azure data Box Edge](../databox-online/azure-stack-edge-overview.md).  Você também pode adicionar marcas e descrições para sua imagem do Docker registrada.

   ```python
   from azureml.core.image import Image
   from azureml.accel import AccelContainerImage

   image_config = AccelContainerImage.image_configuration()
   # Image name must be lowercase
   image_name = "{}-image".format(model_name)

   image = Image.create(name=image_name,
                        models=[converted_model],
                        image_config=image_config,
                        workspace=ws)
   image.wait_for_creation(show_output=False)
   ```

   Liste as imagens por marca e obtenha os logs detalhados para qualquer depuração.

   ```python
   for i in Image.list(workspace=ws):
       print('{}(v.{} [{}]) stored at {} with build log {}'.format(
           i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
   ```

#### <a name="deploy-to-an-azure-kubernetes-service-cluster"></a>Implantar em um cluster do serviço kubernetes do Azure

1. Para implantar seu modelo como um serviço Web de produção de grande escala, use AKS. Você pode criar um novo usando o SDK do Azure Machine Learning, a CLI ou o [Azure Machine Learning Studio](https://ml.azure.com).

    ```python
    from azureml.core.compute import AksCompute, ComputeTarget
    
    # Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
    prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                        agent_count = 1,
                                                        location = "eastus")
    
    aks_name = 'my-aks-cluster'
    # Create the cluster
    aks_target = ComputeTarget.create(workspace=ws,
                                      name=aks_name,
                                      provisioning_configuration=prov_config)
    ```

    A implantação do AKS pode levar cerca de 15 minutos.  Verifique se a implantação foi bem-sucedida.

    ```python
    aks_target.wait_for_completion(show_output=True)
    print(aks_target.provisioning_state)
    print(aks_target.provisioning_errors)
    ```

1. Implante o contêiner no cluster AKS.

    ```python
    from azureml.core.webservice import Webservice, AksWebservice
    
    # For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
    aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=1,
                                                    auth_enabled=False)
    
    aks_service_name = 'my-aks-service'
    
    aks_service = Webservice.deploy_from_image(workspace=ws,
                                               name=aks_service_name,
                                               image=image,
                                               deployment_config=aks_config,
                                               deployment_target=aks_target)
    aks_service.wait_for_deployment(show_output=True)
    ```

#### <a name="deploy-to-a-local-edge-server"></a>Implantar em um servidor de borda local

Todos os [dispositivos Azure data Box Edge](../databox-online/azure-stack-edge-overview.md
) contêm um FPGA para executar o modelo.  Somente um modelo pode ser executado em FPGA ao mesmo tempo.  Para executar um modelo diferente, basta implantar um novo contêiner. As instruções e o código de exemplo podem ser encontrados neste [exemplo do Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

### <a name="consume-the-deployed-model"></a>Consumir o modelo implantado

Por fim, use o cliente de exemplo para chamar a imagem do Docker para obter previsões do modelo.  O código de cliente de exemplo está disponível:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

A imagem do Docker dá suporte a gRPC e TensorFlow que atende à API "Predict".

Você também pode baixar um cliente de exemplo para o serviço de TensorFlow.

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Como esse classificador foi treinado no conjunto de dados [ImageNet](http://www.image-net.org/) , mapeie as classes para rótulos legíveis.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-resources"></a>Limpar os recursos

Para evitar custos desnecessários, limpe os recursos **nesta ordem**: serviço Web, imagem e, em seguida, o modelo.

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="next-steps"></a>Próximas etapas

+ Saiba como [proteger seu documento de serviços Web](how-to-secure-web-service.md) .

+ Saiba mais sobre [preços e custos](https://azure.microsoft.com/pricing/details/machine-learning/)do FPGA e do Azure Machine Learning.

+ [Hardware de hiperescala: ML em escala na parte superior do Azure + FPGA: Build 2018 (vídeo)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Nuvem configurável baseada no Microsoft FPGA (vídeo)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Projeto BrainWave para AI em tempo real](https://www.microsoft.com/research/project/project-brainwave/)

+ [Sistema de inspeção óptica automatizado](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)
