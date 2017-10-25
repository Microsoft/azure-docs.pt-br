---
title: "Visão geral do Gerenciador de Dados do Microsoft Azure StorSimple | Microsoft Docs"
description: "Fornece uma visão geral do serviço Gerenciador de Dados do StorSimple (visualização particular)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: aedb44610fe57055851538b9dbdb810e66e58d73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-data-manager-overview-private-preview"></a>Visão geral do Gerenciador de Dados do StorSimple (Visualização particular)

## <a name="overview"></a>Visão geral

O Microsoft Azure StorSimple é uma solução de armazenamento de nuvem híbrida que resolve as complexidades de dados não estruturados comumente associados aos compartilhamentos de arquivos. O StorSimple usa o armazenamento em nuvem como uma extensão da solução local e dispõe os dados em camadas automaticamente no armazenamento local e no armazenamento em nuvem. A proteção de dados integrada, com instantâneos locais e de nuvem, elimina a necessidade de uma infraestrutura de armazenamento ampla. O arquivamento e a recuperação de desastres também são perfeitos com a nuvem agindo como um local externo.

O serviço de transformação de dados que estamos apresentando neste documento, permite o acesso direto aos dados do StorSimple na nuvem. Esse serviço fornece APIs para extração de dados do StorSimple e os apresenta a outros serviços do Azure em formatos que podem ser facilmente consumidos. Os formatos com suporte nesta visualização são Blobs do Azure e ativos dos Serviços de Mídia do Azure. Essa transformação permite que você conecte facilmente os serviços, como os Serviços de Mídia do Azure, o Azure HDInsight, o Azure Machine Learning e o Azure Search, a fim de operar os dados no dispositivo local da série StorSimple 8000.

Veja abaixo um diagrama de bloco de alto nível que ilustra isso.

![Diagrama de alto nível](./media//storsimple-data-manager-overview/high-level-diagram.png)

Este documento explica como você pode se inscrever para uma visualização particular desse serviço. Também explica como você pode usar esse serviço para escrever aplicativos que usam dados do StorSimple e outros serviços do Azure na nuvem.

## <a name="sign-up-for-data-manager-preview"></a>Inscrever-se para a visualização do Gerenciador de Dados
Antes de se inscrever no serviço do Gerenciador de Gados, consulte os seguintes pré-requisitos.

### <a name="prerequisites"></a>Pré-requisitos

Este exercício supõe que você tem
* uma assinatura ativa do Azure.
* acesso a um dispositivo da série StorSimple 8000 registrado
* todas as chaves associadas ao dispositivo da série StorSimple 8000.

### <a name="sign-up"></a>Inscrição

O Gerenciador de Dados do StorSimple está em visualização particular. Execute as etapas a seguir para se inscrever para uma visualização particular desse serviço:

1.  Faça logon no Portal do Azure com a extensão do Gerenciador de Dados do StorSimple no: [https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager). Use suas credenciais do Azure AD para fazer logon.

2.  Clique no ícone **+** para criar um serviço. Clique em **Armazenamento** e depois em **Ver Tudo** na folha exibida.

    ![Ícone Pesquisar no Gerenciador de Dados do StorSimple](./media/storsimple-data-manager-overview/search-data-manager-icon.png)

3. Você verá o ícone do Gerenciador de Dados do StorSimple.

    ![Ícone Selecionar Gerenciador de Dados do StorSimple](./media/storsimple-data-manager-overview/select-data-manager-icon.png)

4. Clique no ícone Gerenciador de Dados do StorSimple e clique em **Criar**. Escolha a assinatura que você deseja habilitar para a visualização particular e, em seguida, clique em **Inscrever-me!**

    ![Inscrever-me](./media/storsimple-data-manager-overview/sign-me-up.png)

5. Isso envia uma solicitação para sua integração. Realizaremos sua integração assim que possível. Após a habilitação de sua assinatura, você poderá criar um serviço de Gerenciador de Dados do StorSimple.

6. Para acessar facilmente o serviço do Gerenciador de Dados do StorSimple, clique no ícone de estrela para fixá-lo aos seus favoritos.

    ![Acessar o Gerenciador de Dados do StorSimple](./media/storsimple-data-manager-overview/access-data-managers.png)


## <a name="next-steps"></a>Próximas etapas

[Use a interface do usuário do Gerenciador de Dados StorSimple para transformar seus dados](storsimple-data-manager-ui.md).