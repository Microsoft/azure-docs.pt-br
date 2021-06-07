---
title: Atualizar o sistema operacional do host para cluster de computação e instância
titleSuffix: Azure Machine Learning
description: Atualize o sistema operacional do host para o cluster de computação e a instância de computação do Ubuntu 16, 4 LTS para 18, 4 LTS.
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 710a860b1ed87f176b6f42b4963dad17acb323b1
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954047"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>Atualizar instância de computação e sistema operacional de host de cluster de computação

Azure Machine Learning __cluster de computação__ e __instância de computação__ são infraestrutura de computação gerenciada. Como um serviço gerenciado, a Microsoft gerencia o sistema operacional host e os pacotes e as versões de software que estão instalados.

O sistema operacional host para cluster de computação e instância de computação tem sido Ubuntu 16, 4 LTS. Em **30 de abril de 2021**, o Ubuntu está terminando o suporte para 16, 4. A partir de __15 de março de 2021__, a Microsoft atualizará automaticamente o sistema operacional do host para o Ubuntu 18, 4 LTS. A atualização para 18, 4 garantirá atualizações de segurança contínuas e suporte da comunidade Ubuntu. Esta atualização será distribuída nas regiões do Azure e estará disponível em todas as regiões até __09 de abril de 2021__. Para obter mais informações sobre o suporte final do Ubuntu para 16, 4, consulte o [blog de lançamento do Ubuntu](https://wiki.ubuntu.com/Releases).

> [!TIP]
> * O sistema operacional host não é a versão do sistema operacional que você pode especificar para um [ambiente](how-to-use-environments.md) ao treinar ou implantar um modelo. Ambientes executados dentro do Docker. O Docker é executado no sistema operacional do host.
> * Se você estiver usando ambientes baseados no Ubuntu 16, 4 para treinamento ou implantação, a Microsoft recomenda que você mude para o uso de imagens baseadas no Ubuntu 18, 4. Para obter mais informações, consulte [como usar ambientes](how-to-use-environments.md) e o [repositório de contêineres de Azure Machine Learning](https://github.com/Azure/AzureML-Containers/tree/master/base).
> * Ao usar uma instância de computação Azure Machine Learning com base no Ubuntu 18, 4, a versão padrão do Python é _python 3,8_.
## <a name="creating-new-resources"></a>Criando novos recursos

Cluster de computação ou instâncias de computação criadas após __09 de abril de 2021,__ use o Ubuntu 18, 4 LTS como o sistema operacional do host por padrão. Não é possível selecionar um sistema operacional host diferente.

## <a name="upgrade-existing-resources"></a>Atualizar recursos existentes

Se você tiver clusters de computadores ou instâncias de computação existentes criados antes de __15 de março de 2021__, você precisará tomar medidas para atualizar o sistema operacional do host para o Ubuntu 18, 4. Dependendo da região de acesso Azure Machine Learning, recomendamos que você execute essas ações após __09 de abril de 2021__ para garantir que nossas alterações foram distribuídas para todas as regiões:

* __Azure Machine Learning cluster de computação__:

    * Se o cluster estiver configurado com __nós mínimos = 0__, ele será atualizado automaticamente quando todos os trabalhos forem concluídos e reduzir para zero nós.
    * Se os __nós mínimos > 0__, altere temporariamente os nós mínimos para zero e permita que o cluster Reduza para zero nós.

    Para obter mais informações sobre como alterar os nós mínimos, consulte o comando [AZ ml computetarget Update amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/update#ext_azure_cli_ml_az_ml_computetarget_update_amlcompute) CLI do Azure ou a referência do SDK [amlcompute. Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-) .

* __Azure Machine Learning instância de computação__: Crie uma nova instância de computação (que usará o Ubuntu 18, 4) e exclua a instância antiga.

    * Qualquer bloco de anotações armazenado no compartilhamento de arquivos do espaço de trabalho, armazenamentos de dados, de DataSets será acessível da nova instância de computação.
    * Se você tiver criado ambientes Conda personalizados, poderá exportar esses ambientes da instância existente e importá-los na nova instância. Para obter informações sobre exportação e importação do Conda, consulte a [documentação do Conda](https://docs.conda.io/) em docs.Conda.IO.

    Para obter mais informações, consulte os artigos [o que são instância de computação](concept-compute-instance.md) e [criar e gerenciar um Azure Machine Learning a instância de computação](how-to-create-manage-compute-instance.md)

## <a name="check-host-os-version"></a>Verificar versão do sistema operacional do host

Para obter informações sobre como verificar a versão do sistema operacional do host, consulte a página do Ubuntu Community wiki sobre como [verificar sua versão do Ubuntu](https://help.ubuntu.com/community/CheckingYourUbuntuVersion).

> [!TIP]
> Para usar o `lsb_release -a` comando do wiki, você pode [usar uma sessão de terminal em uma instância de computação](how-to-access-terminal.md).
## <a name="next-steps"></a>Próximas etapas

Se você tiver dúvidas ou preocupações adicionais, entre em contato conosco em [ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com) .
