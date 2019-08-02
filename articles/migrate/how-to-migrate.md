---
title: Adicionar ferramentas de migração em migrações para Azure
description: Descreve como adicionar ferramentas de migração no hub de migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b3c77f052ed92db235b363e63859b9beb9e4f5a2
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811863"
---
# <a name="add-migration-tools"></a>Adicionar ferramentas de migração

Este artigo descreve como adicionar ferramentas de migração [migrações para Azure](migrate-overview.md).

As migrações para Azure fornece um hub de ferramentas de avaliação e migração para o Azure. Ele inclui ferramentas nativas, ferramentas fornecidas por outros serviços do Azure e as ofertas do ISV (fornecedor) independentes de software de terceiros.

Se você deseja adicionar uma ferramenta de migração e ainda não configurou um projeto de migrações para Azure, siga este [artigo](how-to-add-tool-first-time.md).



## <a name="selecting-an-isv-tool"></a>Selecionando uma ferramenta de ISV

Se você escolher uma [ferramenta de ISV](migrate-services-overview.md#isv-integration) para a migração, você pode iniciar, obtendo uma licença ou inscrição para uma avaliação gratuita, de acordo com a política de ISV. Em cada ferramenta, há uma opção para conectar-se para migrar do Azure. Implantar a ferramenta e siga as instruções de ferramenta e a documentação para o espaço de trabalho da ferramenta de conexão com migrações para Azure. 

## <a name="select-a-migration-scenario"></a>Selecione um cenário de migração

1. No projeto de migrações para Azure, clique em **visão geral**.
2. Selecione o cenário de migração que você deseja usar:

    - Para migrar as máquinas e cargas de trabalho para o Azure, selecione **avaliar e migrar servidores**.
    - Para migrar as máquinas do SQL local, selecione **avaliar e migrar bancos de dados**.
    - Para migrar aplicativos da web no local, selecione **avaliar e migrar aplicativos web**.
    - Para migrar grandes quantidades de dados local para o Azure no modo offline, selecione **solicitar uma Data Box**.

    ![Cenário de avaliação](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Selecionar uma ferramenta de migração do servidor

1. Clique em **avaliar e migrar servidores**.
2. Na **migrações para Azure - servidores**, se você ainda não adicionou ainda, as ferramentas de migração sob **ferramentas de migração**, selecione **clique aqui para adicionar uma ferramenta de migração**. Se você já adicionou ferramentas de migração, na **adicionar mais ferramentas de migração**, selecione **alteração**.

    > [!NOTE]
    > Se você precisa navegar para um projeto diferente, na **migrações para Azure - servidores**, próximo a **consulte detalhes de um projeto de migração diferentes**, clique em **clique aqui**.

3. Na **migrações para Azure**, selecione a ferramenta de migração que você deseja usar.
    - Se você usar a migração do servidor de migrar do Azure, você pode configurar e executar migrações diretamente no projeto de migrações para Azure.
    - Se você usar uma ferramenta de avaliação de produtos de terceiros, navegue até o link fornecido para o ISV e execute a migração de acordo com as instruções que eles fornecem.

## <a name="select-a-database-migration-tool"></a>Selecionar uma ferramenta de migração de banco de dados

1. Clique em **avaliar e migrar bancos de dados**
2. Na **bancos de dados**, clique em **adicionar ferramentas**.
3. Em Adicionar uma ferramenta > **ferramenta de migração selecione**, selecione a ferramenta que você deseja usar para migrar seu banco de dados.

## <a name="select-a-web-app-migration-tool"></a>Selecione uma ferramenta de migração do aplicativo web

1. Clique em **avaliar e migrar aplicativos web**.
2. Siga o link para a ferramenta de migração para o serviço de aplicativo do Azure. Use a ferramenta de migração:

    - **Avaliar aplicativos online**: Você pode avaliar e migrar aplicativos com uma URL pública online, usando o Assistente de migração do serviço de aplicativo do Azure.
    - **.NET/PHP**: Para aplicativos .NET e PHP internos, você pode baixar e executar o Assistente de migração.

## <a name="order-an-azure-data-box"></a>Ordem de um Azure Data Box

Para migrar grandes quantidades de dados para o Azure, você pode solicitar um Azure DAta Box para transferência de dados offline.

1. Clique em **solicitar uma Data Box**.
2. Na **selecionar o Azure Data Box**, especifique a sua assinatura. 
3. A transferência será uma importação para o Azure. Especifique a fonte de dados e o destino de região do Azure para os dados.

## <a name="next-steps"></a>Próximas etapas

Experimentar uma migração usando a migração de servidor migrar do Azure para [Hyper-V](tutorial-migrate-hyper-v.md) ou [VMware](tutorial-migrate-vmware.md) VMs.
