---
title: "Visão geral conceitual dos recursos da versão prévia do Azure Machine Learning | Microsoft Docs"
description: "Uma visão geral conceitual dos recursos da versão prévia do Azure Machine Learning, como assinaturas, contas, espaços de trabalho, projetos, etc."
services: machine-learning
author: serinakaye
ms.author: serinak
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: 3d4ba2ca6f7adc8b51030c02d9e9eeb2b9995bb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning---concepts"></a>Conceitos do Azure Machine Learning

Este artigo define e descreve os conceitos que você precisa saber para usar o Azure Machine Learning. 

![Hierarquia de conceitos](media/overview-general-concepts/hierarchy.png)

- **Assinatura:** uma assinatura do Azure que concede acesso aos recursos no Azure. Como o Azure Machine Learning está profundamente integrado com computação, armazenamento e muitos outros recursos e serviços do Azure, o Workbench requer que cada usuário tenha acesso a uma assinatura do Azure válida. Os usuários também devem ter permissões suficientes na assinatura para criar recursos.


- **Conta de experimentação:** a conta de experimentação é um recurso do Azure exigido pelo Azure ML e um veículo de cobrança. Ela contém espaços de trabalho, que, por sua vez, contêm projetos. Você pode adicionar vários usuários, conhecidos como _estações_, a uma conta de experimentação. Você deve ter acesso a uma conta de experimentação para usar o Azure ML Workbench para executar experimentos. 


- **Conta de Gerenciamento de Modelos**: uma conta de Gerenciamento de Modelos também é um recurso do Azure exigido pelo Azure ML para gerenciamento de modelos. Você pode usá-la para registrar manifestos e modelos, criar serviços Web em contêineres e implantá-los localmente ou na nuvem. Também é o veículo de cobrança do Azure ML.


- **Espaço de trabalho:** um espaço de trabalho é o componente primário para o compartilhamento e a colaboração no Azure ML. Os projetos são agrupados dentro de um espaço de trabalho. Um espaço de trabalho pode ser compartilhado com vários usuários que foram adicionados à conta de experimentação.


- **Projeto:** no Azure Machine Learning, um projeto é o contêiner lógico para todo o trabalho sendo feito para resolver um problema. Ele é mapeado para uma pasta de arquivo único no seu disco local e você pode adicionar todos os arquivos ou as subpastas nele. Um projeto pode ser associado a um repositório Git para controle do código-fonte e colaboração.  

- **Experimento:** no Azure ML, um experimento é um ou mais arquivos de código-fonte que podem ser executados de um único ponto de entrada. Ele pode conter tarefas, como ingestão de dados, engenharia de recurso, treinamento de modelo ou avaliação do modelo. Atualmente, o Azure ML dá suporte apenas a experimentos Python ou PySpark.


- **Modelo:** no Azure Machine Learning, os modelos se referem ao produto de um experimento de aprendizado de máquina. Eles são receitas que, quando aplicadas corretamente aos dados, geram valores previstos. Os modelos podem ser implantados em ambientes de teste ou produção e usados para pontuar novos dados. Uma vez na produção, os modelos podem ser monitorados quanto à dessincronização de dados e desempenho e retreinados conforme necessário. 

- **Destino de computação:** um destino de computação é o recurso de computação que você configura para a execução em seus experimentos. Pode ser seu computador local (Windows ou macOS), contêiner do Docker em execução no computador local ou em uma VM do Linux no Azure ou em um cluster HDInsight Spark.


- **Execução:** o serviço de experimentação define uma execução como o tempo de vida de uma execução de experimento em um destino de computação. O Azure ML captura informações de cada execução automaticamente e apresenta todo o histórico de um experimento específico na forma de histórico de execução.

- **Ambiente:** no Azure Machine Learning, um ambiente indica um recurso de computação específico que é usado para implantar e gerenciar seus modelos. Ele pode ser o computador local, uma VM do Linux no Azure ou um cluster Kubernetes em execução no Serviço de Contêiner do Azure, dependendo do contexto e da configuração. O modelo é hospedado em um contêiner Docker em execução nesses ambientes e exposto como um ponto de extremidade da API REST.


- **Modelo gerenciado:** o Gerenciamento de Modelos permite implantar modelos como serviços Web, gerenciar várias versões dos seus modelos e monitorar seu desempenho e métricas. Modelos gerenciados são registrados com uma conta de Gerenciamento de Modelos do Azure Machine Learning.

- **Manifestos:** quando o sistema de Gerenciamento de Modelos implanta um modelo na produção, ele inclui um manifesto que pode abranger modelo, dependências, script de pontuação, dados de exemplo e esquema. O manifesto é a receita usada para criar uma imagem de contêiner do Docker. Usando o Gerenciamento de Modelos, você pode gerar automaticamente os manifestos, criar diferentes versões e gerenciar esses manifestos. 


- **Imagens:** você pode usar manifestos para gerar (e regenerar) imagens do Docker. As imagens do Docker em contêineres criam flexibilidade para execução na nuvem, em computadores locais ou em dispositivo IoT. As imagens são autossuficientes e incluem todas as dependências necessárias para pontuar novos dados com modelos. 

- **Serviços:** o Gerenciamento de Modelos permite que você implante modelos como serviços Web. A lógica de serviço Web e as dependências são encapsuladas em uma imagem. Cada serviço Web é um conjunto de contêineres com base na imagem pronta para solicitações de serviço para uma determinada URL. Um serviço Web é contado como uma única implantação.
