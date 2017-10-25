---
title: "Provisionar Máquinas Virtuais de Ciência de Dados do Azure como Servidores de Bloco de Anotações do IPython | Microsoft Docs"
description: "Configure uma Máquina Virtual de Ciência de Dados como um Servidor de Bloco de Anotações do IPython com ferramentas de suporte."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 95e1fa87-794a-4d03-80a4-af4f3f3ac31e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: bradsev
ms.openlocfilehash: 88fe9673176cdade92faad4bbdcb2e1bd11f4a55
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="provision-azure-data-science-virtual-machines-as-ipython-notebook-servers"></a>Provisionar Máquinas Virtuais de Ciência de Dados do Azure como Servidores de Bloco de Anotações do IPython
São fornecidas aqui instruções que descrevem como configurar uma VM do Azure e uma VM do Azure com o serviço do SQL como servidores IPython Notebook. A máquina virtual do Windows está configurada com ferramentas de suporte, como o IPython Notebook, o Gerenciador de Armazenamento do Azure e o AzCopy, bem como outros utilitários que são úteis para projetos de ciência de dados. O Azure Storage Explorer e o AzCopy, por exemplo, fornecem maneiras convenientes para carregar dados no armazenamento do Azure em seu computador local ou baixá-lo em seu computador local por meio do armazenamento. 

Esse menu leva a tópicos que descrevem como configurar os diversos ambientes de ciência de dados usados pelo [TDSP (Processo de Ciência de Dados de Equipe)](overview.md).

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

Vários tipos de máquinas virtuais do Azure podem ser provisionados e configurados para serem usados como parte de um ambiente de ciência de dados baseado em nuvem. A decisão sobre qual tipo de máquina virtual usar depende do tipo e da quantidade de dados que serão modelados com o machine learning e do destino de tais dados na nuvem. 

* Para obter diretrizes sobre as perguntas a serem consideradas ao tomar essa decisão, consulte [Planejar seu ambiente de ciência de dados do Machine Learning do Azure](plan-your-environment.md). 
* Para um catálogo de alguns dos cenários que você pode encontrar ao fazer análises avançadas, consulte [Cenários para o processo de análise avançada e tecnologia no Machine Learning do Azure](plan-sample-scenarios.md)

São fornecidos dois conjuntos de instruções:

* [Configurar uma máquina virtual do Azure como um servidor do IPython Notebook para análise avançada](../data-science-virtual-machine/setup-virtual-machine.md) mostra como provisionar uma máquina virtual do Azure com o IPython Notebook e outras ferramentas usadas para executar o processo de ciência de dados para casos em que uma forma de armazenamento do Azure, com a exceção de SQL, pode ser usada para armazenar os dados.
* [Configurar uma máquina virtual do Azure SQL Server como um servidor do IPython Notebook para análise avançada](../data-science-virtual-machine/setup-sql-server-virtual-machine.md) mostra como provisionar uma máquina virtual do Azure SQL Server com o IPython Notebook e outras ferramentas usadas para executar o processo de ciência de dados para casos em que um banco de dados SQL pode ser usado para armazenar os dados.

Uma vez provisionadas e configuradas, essas máquinas virtuais estão prontas para uso como servidores IPython Notebook para a exploração e o processamento de dados, bem como para outras tarefas necessárias em conjunto com o Machine Learning do Azure e o TDSP (Processo de Ciência de Dados de Equipe). As próximas etapas no processo de ciência de dados são mapeadas no [Roteiro de aprendizagem do TDSP](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) e podem incluir etapas que movem dados para o SQL Server ou HDInsight, que os processam e obtêm amostras deles como parte da preparação para o aprendizado com base nos dados com o Azure Machine Learning.

> [!NOTE]
> A cobrança das máquinas virtuais do Azure ocorre na forma **pague somente pelo que usa**. Para garantir que você não está sendo cobrado quando não estiver usando sua máquina virtual, ela deverá estar no estado **Parado (Desalocado)** do [Portal Clássico do Azure](http://manage.windowsazure.com/). Para obter instruções passo a passo ou como desalocar a sua máquina virtual, veja [Desligar e desalocar máquina virtual quando não estiver em uso](../data-science-virtual-machine/setup-virtual-machine.md#shutdown)
> 
> 

