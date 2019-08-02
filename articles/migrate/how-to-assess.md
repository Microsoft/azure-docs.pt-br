---
title: Adicionar ferramentas de avaliação em migrações para Azure | Microsoft Docs
description: Descreve como adicionar ferramentas de avaliação no hub de migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: d176e6276d69cd3465aa4943efa86ea1e6b0736d
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811642"
---
# <a name="add-assessment-tools"></a>Adicionar ferramentas de avaliação

Este artigo descreve como adicionar ferramentas de avaliação na [migrações para Azure](migrate-overview.md).

As migrações para Azure fornece um hub de ferramentas de avaliação e migração para o Azure. Ele inclui ferramentas nativas, ferramentas fornecidas por outros serviços do Azure e as ofertas do ISV (fornecedor) independentes de software de terceiros.

Se você deseja adicionar uma ferramenta de avaliação e você ainda não tiver um projeto de migrações para Azure, siga este [artigo](how-to-add-tool-first-time.md).

## <a name="selecting-an-isv-tool"></a>Selecionando uma ferramenta de ISV

Se você escolher uma [ferramenta de ISV](migrate-services-overview.md#isv-integration) para avaliação, você pode começar por adquirir uma licença ou inscrição para uma avaliação gratuita, de acordo com a política de ISV. Em cada ferramenta, há uma opção para conectar-se para migrar do Azure. Siga as instruções de ferramenta e a documentação para o espaço de trabalho da ferramenta de conexão com migrações para Azure. 


## <a name="select-an-assessment-scenario"></a>Selecione um cenário de avaliação

1. No projeto de migrações para Azure, clique em **visão geral**.
2. Selecione o cenário de avaliação que você deseja usar:

    - Para descobrir e avaliar computadores e cargas de trabalho de migração para o Azure, selecione **avaliar e migrar servidores**.
    - Para avaliar as máquinas do SQL local, selecione **avaliar e migrar bancos de dados**.
    - Para avaliar os aplicativos da web no local, selecione **avaliar e migrar aplicativos web**.

    ![Cenário de avaliação](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Selecione uma ferramenta de avaliação do servidor 

1. Clique em **avaliar e migrar servidores**.
2. Na **migrações para Azure - servidores**, se você ainda não tiver adicionado uma ferramenta de avaliação, sob **ferramentas de avaliação**, selecione **clique aqui para adicionar uma ferramenta de avaliação**. Se você já adicionou ferramentas de avaliação na **adicionar mais ferramentas de avaliação**, selecione **alteração**.

    > [!NOTE]
    > Se você precisa navegar para um projeto diferente, na **migrações para Azure - servidores**, próximo a **consulte detalhes de um projeto de migração diferentes**, clique em **clique aqui**.

3. Na **migrações para Azure**, selecione a ferramenta de avaliação que você deseja usar.

    
    ![Ferramentas de avaliação](./media/how-to-assess/assess-tool.png)

    - Se você usar a avaliação de servidor de migrar do Azure, você pode configurar, executar e exibir avaliações diretamente no projeto de migrações para Azure.
    - Se você usar uma ferramenta de avaliação de produtos de terceiros, navegue até o link fornecido para seu site e execute a avaliação de acordo com as instruções que eles fornecem.


## <a name="select-a-database-assessment-tool"></a>Selecione uma ferramenta de avaliação do banco de dados

1. Clique em **avaliar e migrar bancos de dados**
2. Na **bancos de dados**, clique em **adicionar ferramentas**.
3. Em Adicionar uma ferramenta > **ferramenta de avaliação selecione**, selecione a ferramenta que você deseja usar para avaliar seu banco de dados.

## <a name="select-a-web-app-assessment-tool"></a>Selecione uma ferramenta de avaliação do aplicativo web

1. Clique em **avaliar e migrar aplicativos web**.
2. Siga o link para a ferramenta de migração para o serviço de aplicativo do Azure. Use a ferramenta de migração:

    - **Avaliar aplicativos online**: Você pode avaliar os aplicativos com uma URL pública online, usando o Assistente de migração do serviço de aplicativo do Azure.
    - **.NET/PHP**: Para aplicativos .NET e PHP internos, você pode baixar e executar o Assistente de migração.



## <a name="next-steps"></a>Próximas etapas

Experimente uma avaliação usando a avaliação de servidor migrar do Azure para [Hyper-V](tutorial-prepare-hyper-v.md) ou [VMware](tutorial-prepare-vmware.md) VMs.
