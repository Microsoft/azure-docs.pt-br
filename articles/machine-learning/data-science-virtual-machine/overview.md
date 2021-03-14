---
title: O que é a Máquina Virtual de Ciência de Dados do Azure
titleSuffix: Azure Data Science Virtual Machine
description: 'Visão geral da Máquina Virtual de Ciência de Dados do Azure: uma máquina virtual fácil de usar na plataforma de nuvem Azure, com ferramentas e bibliotecas pré-instaladas e configuradas para executar a ciência de dados.'
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 04/02/2020
ms.openlocfilehash: bd2333d89e4d1789b3464606b49f624609ef67d5
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518752"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>O que é a Máquina Virtual de Ciência de Dados do Azure para Linux e Windows?

A DSVM (Máquina Virtual de Ciência de Dados) é uma imagem de VM personalizada plataforma de nuvem do Azure especificamente criada para ciência de dados. Ela tem muitas ferramentas populares de ciência de dados pré-instaladas e pré-configuradas a fim de iniciar rapidamente a criação de aplicativos inteligentes para análise avançada.

O DSVM está disponível em:

+ Windows Server 2019
+ Ubuntu 18.04 LTS

## <a name="comparison-with-azure-machine-learning"></a>Comparação com o Azure Machine Learning

A DSVM é uma imagem de VM personalizada para ciência de dados, enquanto o [Azure ML](../overview-what-is-azure-ml.md) (Azure Machine Learning) é uma plataforma completa que abrange:

+ Computação totalmente gerenciada
  + Instâncias de computação
  + Clusters de computação para tarefas de ML distribuídas
  + Clusters de inferência para pontuação em tempo real
+ Armazenamentos de dados (por exemplo, Blob, ADLS Gen2, BD SQL)
+ Acompanhamento de experimentos
+ Gerenciamento de modelos
+ Notebooks
+ Ambientes (gerenciam dependências do Conda e do R)
+ Rotulagem
+ Pipelines (automatizam fluxos de trabalho de ciência de dados de ponta a ponta)

### <a name="comparison-with-azureml-compute-instances"></a>Comparação com as instâncias de computação do AzureML

As [instâncias de Computação do Azure Machine Learning](../concept-compute-instance.md) são uma imagem de VM __totalmente configurada e gerenciada__, enquanto a DSVM é uma VM __não gerenciada__.

As principais diferenças entre essas duas ofertas de produtos são detalhadas abaixo:


|Recurso |Ciência de dados<br>VM |AzureML<br>Instância de computação  | 
|---------|---------|---------|
| Totalmente gerenciado | Não        | Sim        |
|Suporte ao idioma     |  Python, R, Julia, SQL, C#,<br> Java, Node.js, F#       | Python e R        |
|Sistema operacional     | Ubuntu<br>Windows         |    Ubuntu     |
|Opção de GPU pré-configurada     |  Sim       |    Sim     |
|Opção de escala vertical | Sim | Sim |
|Acesso ao SSH    | Sim        |    Sim     |
|Acesso ao RDP    | Sim        |     Não    |
|Interno<br>Notebooks hospedados     |   Não<br>(exige configuração adicional)      |      Sim   |
|SSO interno     | Não <br>(exige configuração adicional)         |    Sim     |
|Colaboração interna     | Não         | Sim        |
|Ferramentas pré-instaladas     |  Jupyter (laboratório), RStudio Server, VS Code,<br> Visual Studio, PyCharm, Juno,<br>Power BI Desktop, SSMS, <br>Microsoft Office 365, Apache Drill       |     Jupyter (laboratório)<br> RStudio Server   |

## <a name="sample-use-cases"></a>Casos de uso de exemplo

Abaixo, ilustramos alguns casos de uso comuns para clientes da DSVM.

### <a name="short-term-experimentation-and-evaluation"></a>Avaliação e experimento de curto prazo

Use a DSVM para avaliar ou aprender a usar novas [ferramentas](./tools-included.md) de ciência de dados, especialmente vendo algumas de nossas [amostras e nossos passos a passos](./dsvm-samples-and-walkthroughs.md) publicados.

### <a name="deep-learning-with-gpus"></a>Aprendizado profundo com GPUs

Na DSVM, seus modelos de treinamento podem usar algoritmos de aprendizado profundo em hardware baseado em GPUs (unidades de processamento gráfico). Aproveitando os recursos de dimensionamento de VM da plataforma Azure, a DSVM ajuda a usar o hardware baseado em GPU na nuvem de acordo com suas necessidades. É possível mudar para uma VM baseada em GPU durante o treinamento de modelos grandes ou quando houver necessidade de cálculos em alta velocidade mantendo o mesmo disco do SO. Escolha um dos SKUs de máquina virtual habilitada para GPUs da série N com a DSVM. Observe que não há suporte para SKUs de máquina virtual habilitada para GPU em contas gratuitas do Azure.

As edições do Windows da DSVM vêm pré-instaladas com drivers de GPU, estruturas e versões de GPU de estruturas de aprendizado profundo. Nas edições do Linux, o aprendizado profundo em GPUs está habilitado nas DSVMs do Ubuntu. 

Você também pode implantar as edições para Ubuntu ou Windows da DSVM em uma máquina virtual do Azure não baseada em GPUs. Nesse caso, todas as estruturas de aprendizado profundo farão o fallback para o modo de CPU.

[Saiba mais sobre o aprendizado profundo e as estruturas de IA disponíveis](dsvm-tools-deep-learning-frameworks.md).

### <a name="data-science-training-and-education"></a>Educação e treinamento de ciência de dados

Os treinadores corporativos e educadores que dão aulas de ciência de dados geralmente fornecem uma imagem de máquina virtual. A imagem garante que os alunos tenham uma configuração consistente e que as amostras funcionem de maneira previsível.

A DSVM cria um ambiente sob demanda com uma configuração consistente que facilita o suporte e os desafios de incompatibilidade. Nos casos em que esses ambientes precisam ser criados com frequência, especialmente para aulas rápidas de treinamento, os alunos são consideravelmente beneficiados.


## <a name="whats-included-on-the-dsvm"></a>O que está incluso na DSVM?

Confira uma lista completa de ferramentas em DSVMs do Windows e do Linux [aqui](tools-included.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais com estes artigos:

+ Windows:
  + [Configurar uma DSVM do Windows](provision-vm.md)
  + [Ciência de dados em uma DSVM do Windows](vm-do-ten-things.md)

+ Linux:
  + [Configurar uma DSVM do Linux (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Ciência de dados em uma DSVM do Linux](linux-dsvm-walkthrough.md)