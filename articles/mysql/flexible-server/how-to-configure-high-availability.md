---
title: Gerenciar alta disponibilidade redundante de zona-portal do Azure-banco de dados do Azure para servidor flexível MySQL
description: Este artigo descreve como habilitar ou desabilitar a alta disponibilidade redundante de zona no banco de dados do Azure para o servidor flexível do MySQL por meio do portal do Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: d65b074385311e74444929ef74901e402e29ec03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93241728"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-preview"></a>Gerenciar alta disponibilidade redundante de zona no banco de dados do Azure para MySQL servidor flexível (versão prévia)

Este artigo descreve como você pode habilitar ou desabilitar a configuração de alta disponibilidade com redundância de zona no seu servidor flexível.

O recurso de alta disponibilidade provisiona a réplica primária e em espera fisicamente separada em diferentes zonas. Para obter mais detalhes, consulte a [documentação de conceitos de alta disponibilidade](./concepts/../concepts-high-availability.md). 

> [!IMPORTANT]
> Você só pode habilitar a alta disponibilidade redundante de zona durante a criação de servidor flexível.

Esta página fornece diretrizes de como você pode habilitar ou desabilitar a alta disponibilidade. Essa operação não altera as outras configurações, incluindo configuração de VNET, configurações de firewall e retenção de backup. Da mesma forma, a desabilitação da alta disponibilidade é uma operação online e não afeta a conectividade e as operações do aplicativo.

> [!IMPORTANT]
> A alta disponibilidade com redundância de zona está disponível em um conjunto limitado de regiões: Sudeste Asiático, Westus 2, Europa Ocidental e leste dos EUA.  

## <a name="enable-high-availability-during-server-creation"></a>Habilitar alta disponibilidade durante a criação do servidor

Esta seção fornece detalhes especificamente para campos relacionados à HA. Você pode seguir estas etapas para implantar a alta disponibilidade ao criar seu servidor flexível.

1.  Na [portal do Azure](https://portal.azure.com/), escolha servidor flexível e clique em **criar**.  Para obter detalhes sobre como preencher detalhes como **assinatura**, **grupo de recursos**, **nome do servidor**, **região** e outros campos, consulte a documentação de instruções para a criação do servidor.

2.  Clique na caixa de seleção de **alta disponibilidade com redundância de zona** na opção disponibilidade.

3.  Se você quiser alterar a computação e o armazenamento padrão, clique em  **Configurar servidor**.

4.  Se a opção de alta disponibilidade estiver marcada, a camada expansível não estará disponível para escolher. Você pode escolher as camadas de computação de **uso geral** ou **otimizado para memória** .

    > [!IMPORTANT]
    > Há suporte apenas para alta disponibilidade redundante de zona para o tipo de preço ***uso geral** de *_memória otimizada_**.

5.  Selecione o **tamanho de computação** para sua escolha no menu suspenso.

6.  Selecione o **tamanho do armazenamento** em GIB usando a barra deslizante e selecione o período de retenção de **backup** entre 7 dias e 35 dias.   

## <a name="disable-high-availability"></a>Desabilitar alta disponibilidade

Siga estas etapas para desabilitar a alta disponibilidade para seu servidor flexível que já está configurado com redundância de zona.

1.  Na [portal do Azure](https://portal.azure.com/), selecione o servidor existente banco de dados do Azure para MySQL flexível.

2.  Na página servidor flexível, clique em **alta disponibilidade** no painel frontal para abrir a página alta disponibilidade.

3.  Clique na caixa de seleção **alta disponibilidade com redundância de zona** para desabilitar a opção e clique em **salvar** para salvar a alteração.

4.  Uma caixa de diálogo de confirmação será mostrada onde você pode confirmar a desabilitação da HA.

5.  Clique no botão **desabilitar ha** para desabilitar a alta disponibilidade.

6.  Uma notificação será exibida descomissionando a implantação de alta disponibilidade em andamento.

## <a name="next-steps"></a>Próximas etapas

-   Saiba mais sobre a [continuidade dos negócios](./concepts-business-continuity.md)
-   Saiba mais sobre [alta disponibilidade redundante de zona](./concepts-high-availability.md)
