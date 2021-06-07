---
title: Configurar a autenticação para modelos implantados como serviços Web
titleSuffix: Azure Machine Learning
description: Saiba como configurar a autenticação para modelos de aprendizado de máquina implantados em serviços Web no Azure Machine Learning.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/06/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bd938fce347d439c2acb4e3fcace04d5b27d770
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94447523"
---
# <a name="configure-authentication-for-models-deployed-as-web-services"></a>Configurar a autenticação para modelos implantados como serviços Web

Azure Machine Learning permite que você implante seus modelos de aprendizado de máquina treinados como serviços Web. Neste artigo, saiba como configurar a autenticação para essas implantações.

As implantações de modelo criadas pelo Azure Machine Learning podem ser configuradas para usar um dos dois métodos de autenticação:

* **baseado em chave**: uma chave estática é usada para autenticar o serviço Web.
* **baseado em token**: um token temporário deve ser obtido no espaço de trabalho Azure Machine Learning (usando Azure Active Directory) e usado para autenticar o serviço Web. Esse token expira após um período de tempo e deve ser atualizado para continuar trabalhando com o serviço Web.

    > [!NOTE]
    > A autenticação baseada em token só está disponível durante a implantação no serviço kubernetes do Azure.

## <a name="key-based-authentication"></a>Autenticação baseada em chave

Os serviços Web implantados no AKS (serviço de kubernetes do Azure) têm a autenticação baseada em chave *habilitada* por padrão.

Os serviços implantados ACI (instâncias de contêiner do Azure) têm a autenticação baseada em chave *desabilitada* por padrão, mas você pode habilitá-la definindo `auth_enabled=True` ao criar o serviço Web do ACI. O código a seguir é um exemplo de criação de uma configuração de implantação ACI com a autenticação baseada em chave habilitada.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Em seguida, você pode usar a configuração personalizada das ACI na implantação usando a classe `Model`.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Para buscar chaves de autenticação, use `aci_service.get_keys()`. Para regenerar uma chave, use a função `regen_key()` e passe como **Primária** ou **Secundária**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

## <a name="token-based-authentication"></a>Autenticação baseada em token

Quando você habilita a autenticação de token para um serviço Web, os usuários precisam apresentar um Token Web JSON do Azure Machine Learning para o serviço Web para acessá-lo. O token expira após um período de tempo determinado e precisa ser atualizado para continuar fazendo chamadas.

* A autenticação de token é **desabilitada por padrão** quando é feita uma implantação no Serviço de Kubernetes do Azure.
* **Não há suporte** para a autenticação de token durante a implantação nas Instâncias de Contêiner do Azure.
* A autenticação de token **não pode ser usada ao mesmo tempo que a autenticação baseada em chave**.

Para controlar a autenticação de tokens, use o `token_auth_enabled` parâmetro ao criar ou atualizar uma implantação:

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Se a autenticação de token estiver habilitada, você poderá usar o método `get_token` para recuperar um JWT (Token Web JSON) e a hora da expiração do token:

> [!TIP]
> Se você usar uma entidade de serviço para obter o token e quiser que ele tenha o mínimo de acesso necessário para recuperar um token, atribua-o à função **leitor** do espaço de trabalho.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> Será necessário solicitar um novo token depois do período de `refresh_by` do token. Se você precisar atualizar os tokens fora do SDK do Python, uma opção é usar a API REST com a autenticação de entidade de serviço para fazer a chamada `service.get_token()` periodicamente, conforme discutido anteriormente.
>
> É altamente recomendável criar seu workspace do Azure Machine Learning na mesma região que o cluster do Serviço de Kubernetes do Azure.
>
> Para autenticar com um token, o serviço Web fará uma chamada para a região em que o workspace do Azure Machine Learning foi criado. Se a região do espaço de trabalho estiver indisponível, você não poderá buscar um token para o serviço Web, mesmo que o cluster esteja em uma região diferente do seu espaço de trabalho. O resultado é que a autenticação do Azure AD não está disponível até que a região do espaço de trabalho esteja disponível novamente.
>
> Além disso, quanto maior a distância entre a região do cluster e a região do espaço de trabalho, mais tempo será levado para buscar um token.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como autenticar para um modelo implantado, consulte [criar um cliente para um modelo implantado como um serviço Web](how-to-consume-web-service.md).