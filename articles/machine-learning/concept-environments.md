---
title: Sobre ambientes de Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba mais sobre os ambientes de aprendizado de máquina, que habilitam as definições de dependência de aprendizado de máquina para vários destinos de computação reproduzíveis, auditáveis &.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: BlackMist
ms.date: 11/16/2020
ms.openlocfilehash: 648dbe6b8d275c832f219cb6f3119ac0bc518a54
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508462"
---
# <a name="what-are-azure-machine-learning-environments"></a>O que são ambientes Azure Machine Learning?

Os ambientes de Azure Machine Learning são um encapsulamento do ambiente no qual o treinamento do Machine Learning acontece. Eles especificam os pacotes python, as variáveis de ambiente e as configurações de software em relação aos seus scripts de treinamento e pontuação. Eles também especificam tempos de execução (Python, Spark ou Docker). Os ambientes são entidades gerenciadas e com controle de versão dentro de seu espaço de trabalho de Machine Learning que permitem a reproduzidas, a auditoria e o aprendizado de produção de máquinas portáteis em uma variedade de destinos de computação.

Você pode usar um `Environment` objeto em sua computação local para:
* Desenvolver seu script de treinamento.
* Reutilizar o mesmo ambiente na Computação do Azure Machine Learning para treinamento de modelos em escala.
* Implante seu modelo com o mesmo ambiente.
* Revisite o ambiente no qual um modelo existente foi treinado.

O diagrama a seguir ilustra como você pode usar um único `Environment` objeto em sua configuração de execução (para treinamento) e sua configuração de inferência e implantação (para implantações de serviço Web).

![Diagrama de um ambiente no fluxo de trabalho do Machine Learning](./media/concept-environments/ml-environment.png)

O ambiente, o destino de computação e o script de treinamento em conjunto formam a configuração de execução: a especificação completa de uma execução de treinamento.

## <a name="types-of-environments"></a>Tipos de ambientes

Os ambientes podem ser amplamente divididos em três categorias: *organizadas*, *gerenciadas pelo usuário* e *gerenciadas pelo sistema*.

Os ambientes organizados são fornecidos por Azure Machine Learning e estão disponíveis em seu espaço de trabalho por padrão. Destinado a ser usado como está, eles contêm coleções de pacotes e configurações do Python para ajudá-lo a começar com várias estruturas do Machine Learning. Esses ambientes pré-criados também permitem um tempo de implantação mais rápido. Para obter uma lista completa, consulte o [artigo sobre ambientes organizados](resource-curated-environments.md).

Em ambientes gerenciados pelo usuário, você é responsável por configurar seu ambiente e instalar cada pacote de que seu script de treinamento precisa no destino de computação. O Conda não verifica seu ambiente nem instala nada para você. Se você estiver definindo seu próprio ambiente, deverá listar `azureml-defaults` com a versão `>= 1.0.45` como uma dependência Pip. Esse pacote contém a funcionalidade necessária para hospedar o modelo como um serviço Web.

Você usa ambientes gerenciados pelo sistema quando deseja que o [Conda](https://conda.io/docs/) gerencie o ambiente do Python e as dependências de script para você. Um novo ambiente Conda é criado com base no objeto dependências Conda. O serviço Azure Machine Learning pressupõe esse tipo de ambiente por padrão, devido à sua utilidade em destinos de computação remota que não são configuráveis manualmente.

## <a name="create-and-manage-environments"></a>Criar e gerenciar ambientes

Você pode criar ambientes:

* Definir novos `Environment` objetos, seja usando um ambiente organizado ou definindo suas próprias dependências.
* Usando `Environment` objetos existentes do seu espaço de trabalho. Essa abordagem permite a consistência e a reprodução com suas dependências.
* Importando de uma definição de ambiente Anaconda existente.
* Usando a CLI do Azure Machine Learning
* [Usando a extensão VS Code](how-to-manage-resources-vscode.md#create-environment)

Para obter exemplos de código específicos, consulte a seção "criar um ambiente" de [como usar ambientes](how-to-use-environments.md#create-an-environment). Os ambientes também são facilmente gerenciados por meio de seu espaço de trabalho. Eles incluem a seguinte funcionalidade:

* Os ambientes são automaticamente registrados em seu espaço de trabalho quando você envia um experimento. Eles também podem ser registrados manualmente.
* Você pode buscar ambientes do seu espaço de trabalho para usar para treinamento ou implantação ou para fazer edições na definição do ambiente.
* Com o controle de versão, você pode ver as alterações em seus ambientes ao longo do tempo, o que garante reprodução.
* Você pode criar imagens do Docker automaticamente de seus ambientes.

Para obter exemplos de código, consulte a seção "gerenciar ambientes" de [como usar ambientes](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Criação de ambiente, cache e reutilização

O serviço Azure Machine Learning cria definições de ambiente em imagens do Docker e ambientes Conda. Ele também armazena em cache os ambientes para que eles possam ser reutilizados em execuções de treinamento subsequentes e implantações de ponto de extremidade de serviço. A execução remota de um script de treinamento requer a criação de uma imagem do Docker, enquanto que uma execução local pode usar um ambiente Conda diretamente. 

### <a name="submitting-a-run-using-an-environment"></a>Enviando uma execução usando um ambiente

Quando você envia pela primeira vez uma execução remota usando um ambiente, o serviço de Azure Machine Learning invoca uma [tarefa de compilação ACR](../container-registry/container-registry-tasks-overview.md) no registro de contêiner do Azure (ACR) associado ao espaço de trabalho. Em seguida, a imagem do Docker criada é armazenada em cache no ACR do espaço de trabalho. Os ambientes organizados são apoiados por imagens do Docker armazenadas em cache no ACR global. No início da execução de execução, a imagem é recuperada pelo destino de computação do ACR relevante.

Para execuções locais, um ambiente Docker ou Conda é criado com base na definição do ambiente. Os scripts são então executados na computação de destino – um ambiente de tempo de execução local ou um mecanismo do Docker local.

### <a name="building-environments-as-docker-images"></a>Criando ambientes como imagens do Docker

Se a definição de ambiente ainda não existir no ACR do espaço de trabalho, uma nova imagem será criada. A compilação da imagem consiste em duas etapas:

 1. Baixando uma imagem base e executando qualquer etapa do Docker
 2. Criar um ambiente Conda de acordo com as dependências Conda especificadas na definição de ambiente.

A segunda etapa será omitida se você especificar [dependências gerenciadas pelo usuário](/python/api/azureml-core/azureml.core.environment.pythonsection). Nesse caso, você é responsável por instalar qualquer pacote do Python, incluindo-os em sua imagem base ou especificando etapas personalizadas do Docker na primeira etapa. Você também é responsável por especificar o local correto para o executável do Python. Também é possível usar uma imagem de [base do Docker personalizada](how-to-deploy-custom-docker-image.md).

### <a name="image-caching-and-reuse"></a>Cache e reutilização de imagens

Se você usar a mesma definição de ambiente para outra execução, o serviço de Azure Machine Learning reutiliza a imagem armazenada em cache do ACR do espaço de trabalho. 

Para exibir os detalhes de uma imagem armazenada em cache, use [Environment.get_image_details](/python/api/azureml-core/azureml.core.environment.environment#get-image-details-workspace-) método.

Para determinar se deve reutilizar uma imagem armazenada em cache ou criar uma nova, o serviço computa [um valor de hash](https://en.wikipedia.org/wiki/Hash_table) da definição do ambiente e o compara com os hashes dos ambientes existentes. O hash se baseia em:
 
 * Valor da propriedade da imagem base
 * Valor da propriedade de etapas do Docker personalizado
 * Lista de pacotes do Python na definição de Conda
 * Lista de pacotes na definição do Spark 

O hash não depende do nome do ambiente ou da versão – se você renomear seu ambiente ou criar um novo ambiente com as propriedades e os pacotes exatos de um existente, o valor de hash permanecerá o mesmo. No entanto, as alterações de definição de ambiente, como adicionar ou remover um pacote Python ou alterar a versão do pacote, fazem com que o valor de hash seja alterado. Alterar a ordem das dependências ou dos canais em um ambiente resultará em um novo ambiente e, portanto, exigirá uma nova compilação de imagem. É importante observar que qualquer alteração em um ambiente organizado invalidará o hash e resultará em um novo ambiente "não organizado".

O valor de hash calculado é comparado àqueles no espaço de trabalho e no ACR global (ou no destino de computação para execuções locais). Se houver uma correspondência, a imagem armazenada em cache será puxada, caso contrário, uma compilação de imagem será disparada. A duração para efetuar pull de uma imagem armazenada em cache inclui o tempo de download, enquanto a duração para efetuar pull de uma imagem criada recentemente inclui o tempo de compilação e o tempo de download. 

O diagrama a seguir mostra três definições de ambiente. Dois deles têm diferentes nomes e versões, mas imagens de base e pacotes python idênticos. Mas eles têm o mesmo hash e, portanto, correspondem à mesma imagem armazenada em cache. O terceiro ambiente tem diferentes pacotes e versões do Python e, portanto, corresponde a uma imagem armazenada em cache diferente.

![Diagrama de cache de ambiente como imagens do Docker](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> Se você criar um ambiente com uma dependência de pacote desafixada, por exemplo ```numpy``` , esse ambiente continuará usando a versão do pacote instalada _no momento da criação do ambiente_. Além disso, qualquer ambiente futuro com definição correspondente continuará usando a versão antiga. 

Para atualizar o pacote, especifique um número de versão para forçar a recompilação da imagem, por exemplo ```numpy==1.18.1``` . Serão instaladas novas dependências, incluindo aquelas aninhadas que podem interromper um cenário de trabalho anterior. 

> [!WARNING]
>  O método [Environment. Build](/python/api/azureml-core/azureml.core.environment.environment#build-workspace--image-build-compute-none-) recriará a imagem armazenada em cache, com possível efeito colateral de atualização de pacotes desafixados e interrupção de reprodução para todas as definições de ambiente correspondentes à imagem armazenada em cache.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar e usar ambientes](how-to-use-environments.md) no Azure Machine Learning.
* Consulte a documentação de referência do SDK do Python para a [classe de ambiente](/python/api/azureml-core/azureml.core.environment%28class%29).
* Consulte a documentação de referência do SDK do R para [ambientes](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).