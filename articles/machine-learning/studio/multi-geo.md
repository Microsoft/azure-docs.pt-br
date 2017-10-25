---
title: "Documentação de ajuda de várias áreas geográficas | Microsoft Docs"
description: "Saiba como criar um espaço de trabalho e publicar um serviço Web em uma região do Azure diferente do Centro-Sul dos Estados Unidos (SCUS)."
services: machine-learning
documentationcenter: 
author: tedway
manager: jhubbard
editor: rmca14
tags: 
ms.assetid: ed0ca8a8-fa53-4e56-b824-2d7e44641967
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/6/2017
ms.author: tedway; neerajkh
ms.openlocfilehash: e4941ccf8c6d7a0c77527e9c1d722bc3a770114a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="multi-geo-help-documentation"></a>Documentação da Ajuda para diversas áreas geográficas
Este artigo descreve como você pode criar um espaço de trabalho e publicar um serviço Web em diferentes regiões do Azure.  A página [Produtos do Azure por região](https://azure.microsoft.com/en-us/regions/services/) lista regiões em que o Azure Machine Learning está disponível.

## <a name="create-a-workspace"></a>Criar um espaço de trabalho
1. Entre no Portal Clássico do Azure.
2. Clique em **+ NOVO** > **SERVIÇOS DE DADOS** > **ACHINE LEARNING** > **CRIAÇÃO RÁPIDA**.  Em **LOCAL**, selecione outra região, como **Sudeste Asiático**.
   ![Imagem de Ajuda de várias áreas geográficas 1][1]
3. Selecione o espaço de trabalho e, em seguida, clique em **Entrar no Estúdio AM**.
   ![Imagem de Ajuda de várias áreas geográficas 2][2]
4. Agora você tem um espaço de trabalho em outra região que pode usar como qualquer outro espaço de trabalho. Para alternar entre seus espaços de trabalho, veja o canto superior direito da tela. Clique na lista suspensa, selecione a região e, em seguida, selecione o espaço de trabalho. Tudo é local para a região do espaço de trabalho.  Por exemplo, todos os serviços Web criados por meio de um espaço de trabalho estarão na mesma região em que o espaço de trabalho está localizado.
   ![Imagem de Ajuda de várias áreas geográficas 3][3]

## <a name="open-an-experiment-from-gallery"></a>Abrir um experimento da Galeria
Se você abrir um experimento da galeria, também pode selecionar a região para a qual deseja copiar o experimento.

![Imagem de Ajuda de várias áreas geográficas 4][4a]

## <a name="web-service-management"></a>Gerenciamento de serviço Web
Para gerenciar os serviços Web de forma programática, como um novo treinamento, use o endereço específico da região:

* https://asiasoutheast.management.azureml.net
* https://europewest.management.azureml.net

### <a name="things-to-note"></a>Elementos a serem observados
1. Dessa maneira, você só pode copiar experimentos entre espaços de trabalho que pertençam à mesma região. Se precisar copiar experimentos entre espaços de trabalho em regiões diferentes, você poderá usar o cmdlet do [PowerShell](http://aka.ms/amlps), [*Copy-AmlExperiment* ](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment) para fazer isso. Outra solução alternativa é publicar o experimento na Galeria no modo não listado e depois abri-lo no espaço de trabalho da outra região.
2. O seletor de região mostrará apenas os espaços de trabalho para uma região por vez.  
3. Um espaço de trabalho livre ou de acesso de convidado (anônimo) será criado e hospedado no Centro-Sul dos EUA  
4. Os serviços Web implantados por meio de um espaço de trabalho no Sudeste Asiático também serão hospedados no Sudeste Asiático.  

## <a name="more-information"></a>Mais informações
Fazer uma pergunta no [fórum de Azure Machine Learning](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning).

<!--Image references-->
[1]: ./media/multi-geo/multi-geo_1.png
[2]: ./media/multi-geo/multi-geo_2.png
[3]: ./media/multi-geo/multi-geo_3.png
[4a]: ./media/multi-geo/multi-geo_4a.png
