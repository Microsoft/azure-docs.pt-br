---
title: "Gerenciar atualizações para várias máquinas virtuais do Azure | Microsoft Docs"
description: "Carregar máquinas virtuais do Azure para gerenciar atualizações."
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: eslesar
ms.openlocfilehash: 89bf87f27fdf276068cba261fc6ae1660307e0b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>Gerenciar atualizações para várias máquinas virtuais do Azure

O gerenciamento de atualizações permite que você gerencie atualizações e patches para suas máquinas virtuais do Azure.
Na conta da [Automação do Azure](automation-offering-get-started.md), você pode carregar máquinas virtuais rapidamente, avaliar o status de atualizações disponíveis, agendar a instalação de atualizações necessárias e examinar os resultados de implantação para verificar se as atualizações foram aplicadas com êxito em todas as máquinas virtuais para as quais o gerenciamento de atualizações está habilitado.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as tarefas deste guia, você precisará de:

* Uma conta de Automação do Azure. Para obter instruções sobre como criar uma conta Executar Como de Automação do Azure, consulte [Conta Executar Como do Azure](automation-sec-configure-azure-runas-account.md).
* Uma máquina virtual do Azure Resource Manager (não clássica). Para obter instruções sobre a criação de uma VM, consulte [Criar sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="enable-update-management-for-azure-virtual-machines"></a>Habilitar o Gerenciamento de Atualizações para máquinas virtuais do Azure

1. No Portal do Azure, abra a Conta de automação.
2. No lado esquerdo da tela, selecione **Gerenciamento de atualizações**.
3. Na parte superior da tela, clique em **Adicionar VM do Azure**.
    ![Carregar VMs](./media/manage-update-multi/update-onboard-vm.png)
4. Selecione uma máquina virtual a ser carregada. A tela **Habilitar Gerenciamento de Atualizações** é exibida.
5. Clique em **Habilitar**.

   ![Habilitar gerenciamento de atualizações](./media/manage-update-multi/update-enable.png)

O gerenciamento de atualizações está habilitado para sua máquina virtual.

## <a name="view-update-assessment"></a>Exibir avaliação de atualização

Depois que o **Gerenciamento de Atualizações** for habilitado, a tela **Gerenciamento de Atualizações** será exibida. Você pode ver uma lista de atualizações ausentes na guia **Atualizações ausentes**.

## <a name="schedule-an-update-deployment"></a>Agendar uma implantação de atualização

Para instalar atualizações, agende uma implantação que siga o agendamento de versão e o período de serviço.
Você pode escolher quais tipos de atualização deseja incluir na implantação. Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualizações.

Agende uma nova implantação de atualização para uma ou mais máquinas virtuais clicando em **Agendar implantação de atualização** na parte superior da tela **Gerenciamento de atualizações**. Na tela **Nova implantação de atualização**, especifique o seguinte:

* **Nome** – forneça um nome exclusivo para identificar a Implantação de atualizações.
* **Tipo de Sistema Operacional** – selecione Windows ou Linux.
* **Computadores a atualizar** – selecione as máquinas virtuais que você deseja atualizar.

  ![Selecionar máquinas virtuais a serem atualizadas](./media/manage-update-multi/update-select-computers.png)

* **Classificação de atualização** – selecione os tipos de software que a implantação de atualização incluirá na implantação. Os tipos de classificação são:
  * Atualizações críticas
  * Atualizações de segurança
  * Pacotes cumulativos de atualização
  * Feature packs
  * Service packs
  * Atualizações de definição
  * Ferramentas
  * Atualizações
* **Configurações de agenda** – você pode aceitar a data e hora padrão, que é de 30 minutos após a hora atual ou especificar um horário diferente.
   Você também pode especificar se a implantação ocorre uma única vez ou configurar um agendamento recorrente. Clique na opção Recorrente em Recorrência para configurar um agendamento recorrente.

   ![Tela de configurações de agenda de atualização](./media/manage-update-multi/update-set-schedule.png)

* **Janela de manutenção (minutos)** – especifique o período de tempo em que deseja que a implantação de atualização ocorra.  Isso ajuda a garantir que as alterações sejam executadas dentro das janelas de serviço definidas.

Depois de concluir a configuração da agenda, clique no botão **Criar** e retorne ao painel de status.
Observe que a tabela **Agendado** mostra a agenda de implantação recém-criada.

> [!WARNING]
> Para atualizações que exigem uma reinicialização, a máquina virtual será reiniciada automaticamente.

## <a name="view-results-of-an-update-deployment"></a>Exibir resultados de uma implantação de atualização

Após o início da implantação agendada, você pode ver o status dessa implantação na guia **Implantações de atualização** na tela **Gerenciamento de Atualizações**.
Se ela estiver em execução, seu status será mostrado como **Em andamento**. Após a conclusão, em caso de êxito, ele será alterado para **Êxito**.
Se houver falha em uma ou mais atualizações na implantação, o status será **Falhou parcialmente**.

![Status da implantação da atualização ](./media/manage-update-multi/update-view-results.png)

Clique na implantação de atualização concluída para ver o painel dessa implantação de atualização.

No bloco **Resultados de atualização** há um resumo do número total de atualizações e os resultados da implantação na máquina virtual.
Na tabela à direita há uma análise detalhada de cada atualização e os resultados da instalação, que podem ser um dos seguintes valores:

* Não foi tentada – a atualização não foi instalada devido a tempo suficiente disponível com base na duração da janela de manutenção definida.
* Êxito – a atualização foi bem-sucedida
* Falha – falha na atualização

Clique em **Todos os logs** para ver todas as entradas de log que a implantação criou.

Clique no bloco **Saída** para ver o fluxo de trabalho do runbook responsável por gerenciar a implantação de atualização na máquina virtual de destino.

Clique em **Erros** para ver informações detalhadas sobre os erros da implantação.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o Gerenciamento de atualizações, consulte [Gerenciamento de Atualizações](../operations-management-suite/oms-solution-update-management.md).