---
title: Adicionar uma ferramenta de avaliação/migração pela primeira vez em migrações para Azure | Microsoft Docs
description: Descreve como criar um projeto de migrações para Azure e adicionar uma ferramenta de avaliação/migração.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b226f7c5879673b573133cde45db78d8d1f2fffa
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812019"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Adicionar uma ferramenta de avaliação/migração pela primeira vez

Este artigo descreve como adicionar uma ferramenta de avaliação ou migração para um [migrações para Azure](migrate-overview.md) projeto pela primeira vez.  
As migrações para Azure fornece um hub central para acompanhar a descoberta, avaliação e migração de seus aplicativos locais e cargas de trabalho e VMs, para o Azure na nuvem privada/pública. O hub fornece ferramentas de migrações para Azure para avaliação e migração, bem como fornecedor de software de terceiros, independente (ISV) [ofertas](migrate-services-overview.md#isv-integration) . 

## <a name="create-a-project-and-add-a-tool"></a>Criar um projeto e adicionar uma ferramenta

Configurar um novo projeto de migrações para Azure em uma assinatura do Azure e adicionar uma ferramenta.

- Um projeto de migrações para Azure é usado para armazenar a descoberta, avaliação e coletados do ambiente você está avaliando ou migração de metadados de migração. 
- Em um projeto, você pode controlar ativos descobertos e orquestrar a avaliação e migração.

1. No portal do Azure > **todos os serviços**, pesquise **migrações para Azure**.
2. Sob **Services**, selecione **migrações para Azure**.

    ![Configurar migrações para Azure](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. Na **visão geral**, clique em **avaliar e migrar servidores**.
4. Sob **descobrir, avaliar e migrar os servidores**, clique em **avaliar e migrar servidores**.

    ![Descubra e avalie os servidores](./media/how-to-add-tool-first-time/assess-migrate.png)

1. Na **descobrir, avaliar e migrar os servidores**, clique em **adicionar ferramentas**.
2. Na **projeto de migrações**, selecione sua assinatura do Azure e criar um grupo de recursos se você não tiver uma.
3. Na **detalhes do projeto**, especifique o nome do projeto e geografia, na qual você deseja criar o projeto. 

    ![Criar um projeto de migrações para Azure](./media/how-to-add-tool-first-time/migrate-project.png)

    Você pode criar um projeto de migrações para Azure em qualquer uma dessas regiões geográficas.

    **Geografia** | **Região do local de armazenamento**
    --- | ---
    Ásia | Sudeste Asiático ou Ásia Oriental
    Europa | Sul da Europa ou Europa Ocidental
    Reino Unido | Sul do Reino Unido ou oeste do Reino Unido
    Estados Unidos | Centro dos EUA ou oeste dos EUA 2

    A localização geográfica especificada para o projeto só é usada para armazenar os metadados coletados das VMs locais. Você pode selecionar qualquer região de destino para a migração real.

4. Clique em **próxima**e adicionar uma ferramenta de avaliação ou migração.

    > [!NOTE]
    > Quando você cria um projeto, você precisa adicionar pelo menos uma ferramenta de avaliação ou migração.

5. Na **ferramenta de avaliação selecione**, adicionar uma ferramenta de avaliação. Se você não precisa de uma ferramenta de avaliação, selecione **ignorar a adição de uma ferramenta de avaliação por ora** > **próximo**. 
2. Na **ferramenta de migração selecione**, adicionar uma ferramenta de migração conforme necessário. Se não precisar de uma ferramenta de migração no momento, selecione **ignorar a adição de uma ferramenta de migração por ora** > **próximo**.
3. Na **revisar + adicionar ferramentas**, examine as configurações e clique em **adicionar ferramentas**.

Depois de criar o projeto, você pode selecionar ferramentas adicionais de avaliação e migração de servidores e cargas de trabalho, os bancos de dados e aplicativos web.

## <a name="create-additional-projects"></a>Criar projetos adicionais

Em algumas circunstâncias, talvez você precise criar projetos adicionais de migrações para Azure. Por exemplo, se você tem datacenters em regiões diferentes, ou você precisa armazenar metadados em uma geografia diferente. Crie um projeto adicional da seguinte maneira:

1. No atual projeto migrações para Azure, clique em **servidores** ou **bancos de dados**.
2. No canto superior direito, clique em **alteração** próximo ao nome do projeto atual.
3. Na **as configurações**, selecione **clique aqui para criar um novo projeto**.
4. Criar um novo projeto e adicione uma nova ferramenta, conforme descrito no procedimento anterior.

## <a name="next-steps"></a>Próximas etapas

Saiba como adicionar mais [assessment](how-to-assess.md) e [migração](how-to-migrate.md) ferramentas. 
