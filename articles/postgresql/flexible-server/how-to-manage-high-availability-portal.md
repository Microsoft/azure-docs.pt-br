---
title: Gerenciar alta disponibilidade redundante de zona-portal do Azure-banco de dados do Azure para PostgreSQL-servidor flexível
description: Este artigo descreve como habilitar ou desabilitar a alta disponibilidade redundante de zona no banco de dados do Azure para PostgreSQL – servidor flexível por meio do portal do Azure.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: fc1bca1265139a438fad86bfce770026866d9a2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90932829"
---
# <a name="manage-zone-redundant-high-availability-in-flexible-server"></a>Gerenciar alta disponibilidade redundante de zona no servidor flexível

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

Este artigo descreve como você pode habilitar ou desabilitar a configuração de alta disponibilidade com redundância de zona no seu servidor flexível.

O recurso de alta disponibilidade provisiona a réplica primária e em espera fisicamente separada em diferentes zonas. Para obter mais detalhes, consulte a [documentação de conceitos de alta disponibilidade](./concepts-high-availability.md). Você pode optar por habilitar a alta disponibilidade no momento da criação do servidor flexível ou após a criação. Esta página fornece diretrizes de como você pode habilitar ou desabilitar a alta disponibilidade. Essa operação não altera as outras configurações, incluindo configuração de VNET, configurações de firewall e retenção de backup. Da mesma forma, habilitar e desabilitar a alta disponibilidade é uma operação online e não afeta a conectividade e as operações do aplicativo.

## <a name="pre-requisites"></a>Pré-requisitos

A alta disponibilidade com redundância de zona está disponível somente em regiões em que há suporte para várias zonas. 

## <a name="enable-high-availability-during-server-creation"></a>Habilitar alta disponibilidade durante a criação do servidor

Esta seção fornece detalhes especificamente para campos relacionados à HA. Você pode seguir estas etapas para implantar a alta disponibilidade ao criar seu servidor flexível.

1.  Na [portal do Azure](https://portal.azure.com/), escolha servidor flexível e clique em criar.  Para obter detalhes sobre como preencher detalhes como **assinatura**, **grupo de recursos**, **nome do servidor**, **região**e outros campos, consulte a documentação de instruções para a criação do servidor.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/subscription-region.png" alt-text="Exibir assinatura e região":::

2.  Escolha sua **zona de disponibilidade**. Isso será útil se você quiser colocar seu aplicativo na mesma zona de disponibilidade que o banco de dados para reduzir a latência. Escolha **sem preferência** se desejar que o servidor flexível seja implantado em qualquer zona de disponibilidade.
    ![Seleção de ]() :::image type="content" source="./media/how-to-manage-high-availability-portal/zone-selection.png" alt-text="Exibir assinatura e região"::: de seleção AZ  

3.  Clique na caixa de seleção de **alta disponibilidade com redundância de zona** na opção disponibilidade.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-checkbox.png" alt-text="Exibir assinatura e região":::

4.  Se você quiser alterar a computação e o armazenamento padrão, clique em  **Configurar servidor**.
 
    :::image type="content" source="./media/how-to-manage-high-availability-portal/configure-server.png" alt-text="Exibir assinatura e região":::  

5.  Se a opção de alta disponibilidade estiver marcada, a camada expansível não estará disponível para escolher. Você pode escolher as camadas de computação de **uso geral** ou **otimizado para memória** . Em seguida, você pode selecionar o **tamanho da computação** para sua escolha na lista suspensa.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/select-compute.png" alt-text="Exibir assinatura e região":::  


6.  Selecione o **tamanho do armazenamento** em GIB usando a barra deslizante e selecione o período de retenção de **backup** entre 7 dias e 35 dias.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/storage-backup.png" alt-text="Exibir assinatura e região"::: 

7. Clique em **Salvar**. 

## <a name="enable-high-availability-post-server-creation"></a>Habilitar a criação de um post Server de alta disponibilidade

Siga estas etapas para habilitar a alta disponibilidade para seu servidor flexível existente.

1.  Na [portal do Azure](https://portal.azure.com/), selecione o servidor flexível PostgreSQL existente.

2.  Na página servidor flexível, clique em **alta disponibilidade** no painel esquerdo para abrir a página de alta disponibilidade.
   
     :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="Exibir assinatura e região"::: 

3.  Clique na caixa de seleção **alta disponibilidade com redundância de zona** para **habilitar** a opção e clique em **salvar**   para salvar a alteração.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/enable-high-availability.png" alt-text="Exibir assinatura e região"::: 

4.  Uma caixa de diálogo de confirmação mostrará que, ao habilitar a alta disponibilidade, seu custo aumentará devido à implantação adicional de armazenamento e servidor.

5.  Clique no botão **habilitar ha** para habilitar a alta disponibilidade.

6.  Uma notificação será exibida informando que a implantação de alta disponibilidade está em andamento.

## <a name="disable-high-availability"></a>Desabilitar alta disponibilidade

Siga estas etapas para desabilitar a alta disponibilidade para seu servidor flexível que já está configurado com redundância de zona.

1.  Na [portal do Azure](https://portal.azure.com/), selecione seu banco de dados do Azure existente para PostgreSQL-servidor flexível.

2.  Na página servidor flexível, clique em **alta disponibilidade** no painel frontal para abrir a página alta disponibilidade.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="Exibir assinatura e região"::: 

3.  Clique na caixa de seleção **alta disponibilidade com redundância de zona** para **desabilitar** a opção. Em seguida, clique em **salvar**   para salvar a alteração.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/disable-high-availability.png" alt-text="Exibir assinatura e região"::: 

4.  Uma caixa de diálogo de confirmação será mostrada onde você pode confirmar a desabilitação da alta disponibilidade.

5.  Clique no botão **desabilitar ha** para desabilitar a alta disponibilidade.

6.  Uma notificação será exibida descomissionando a implantação de alta disponibilidade em andamento.

## <a name="next-steps"></a>Próximas etapas

-   Saiba mais sobre a [continuidade dos negócios](./concepts-business-continuity.md)
-   Saiba mais sobre [alta disponibilidade redundante de zona](./concepts-high-availability.md)
