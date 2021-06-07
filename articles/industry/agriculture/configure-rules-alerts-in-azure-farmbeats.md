---
title: Configurar regras e gerenciar alertas
description: Descreve como configurar regras e gerenciar alertas no FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: a04f973cbfa3a68016065f50e9e2ff4f7566da94
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182920"
---
# <a name="configure-rules-and-manage-alerts"></a>Configurar regras e gerenciar alertas

O Azure FarmBeats permite que você crie regras com base na lógica de negócios, além dos dados do sensor que fluem dos sensores e dispositivos implantados em seu farm. As regras disparam alertas no sistema sempre que os valores do sensor ultrapassarem um valor limite. Ao exibir e analisar os alertas criados após os valores de limite, você pode agir rapidamente sobre problemas e obter as soluções necessárias.

## <a name="create-rule"></a>Criar regra

1. Na home page, vá para **regras**.
2. Selecione **nova regra**. A janela nova regra é exibida.

    ![Captura de tela que realça o botão nova regra e a seção nova regra.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. Insira o **nome da regra** e a **Descrição da regra** e, em seguida, selecione um farm no menu suspenso **selecionar Farm** .
4. Digite o nome do farm para selecionar a seção farm e **condições** aparece na mesma janela.  

    ![Captura de tela que realça a seção de condições.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. Em **condições**, insira os valores para **medida**, **operador** e **valor**.
6. Digite o nome da medida no menu suspenso **medida** .
7. Selecione **+ Adicionar condição** para adicionar mais condições à regra.
8. Selecione o **nível de severidade**.
9. Em **ação**, alterne o botão de alternância **email habilitado** para habilitar alertas de email.

    ![Captura de tela que mostra a opção de email habilitado.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. Insira os **endereços de email** para os quais você deseja enviar o alerta por email, juntamente com o **assunto do email** e as **observações adicionais**.  
11. No **status da regra**, alterne para o botão de alternância **habilitado** para habilitar ou desabilitar a regra.
    Você pode exibir o número de dispositivos que serão afetados pela regra.
12. Selecione **aplicar** para criar a regra.

## <a name="view-rule"></a>Exibir regra

A página do **farm** exibe a lista de regras disponíveis. Selecione um **nome de regra**. Uma janela exibe os seguintes detalhes que são aplicáveis para a regra selecionada:
 - Nome da regra
 - Link para o farm ao qual a regra está associada
 - Data da criação
 - Data da última atualização
 - Nível de severidade
 - Status da regra
 - Lista de condições  
 - Número de dispositivos afetados pela regra

    ![Captura de tela que mostra os detalhes da regra.](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>Editar regra

Para editar uma regra, siga estas etapas:

1. Na home page, selecione **regras** no menu de navegação à esquerda.
   A janela regras é exibida.
2. Selecione a regra para a qual você deseja editar.

    ![Captura de tela que mostra a regra selecionada.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. Selecione **Editar** na barra de ações, a janela **Editar regra** é exibida.

    ![Captura de tela que mostra as telas de editar regra.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. Altere o **nome da regra** e a **Descrição da regra** e, em seguida, selecione um farm no menu suspenso **selecionar Farm** .
5. Digite o nome do farm para selecionar o farm e as **condições** aparecerão na mesma janela.  
6. Em **condições**, edite **Measure**, **Operator** e **Value**.
7. Digite o nome da medida no menu suspenso **medida** .
8. Selecione **+ Adicionar condição** para adicionar/editar condições para as regras.

    ![Captura de tela que realça o botão Adicionar condição.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  Selecione o **nível de severidade**.  
10. Em **ação**, alterne o botão de alternância **email habilitado** para habilitar alertas de email.
11. Edite os **endereços de email** para os quais você deseja enviar o alerta por email, juntamente com o assunto do **email** e as **observações adicionais**.  
12. No **status da regra**, alterne para o botão de alternância **habilitado** para habilitar ou desabilitar a regra.
Você pode exibir o número de dispositivos que serão afetados por essa regra.
13. Selecione **aplicar** para editar a regra.

## <a name="change-rule-status"></a>Alterar status da regra

Para alterar o status de uma regra, siga estas etapas:

1. Na home page, selecione **regras** no menu de navegação à esquerda. A janela regras é exibida.
2. Selecione a regra para a qual você deseja alterar o status.

    ![Captura de tela que mostra o botão Alterar status.](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. Selecione **Alterar status** na barra de ação. A janela **status da alteração** é exibida.

    ![Captura de tela que mostra as telas de status de alteração.](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. Altere o status da regra usando o botão de alternância **Alterar status** .
   Você pode exibir o número de dispositivos que serão afetados pela regra.
4. Selecione **aplicar** para alterar o status da regra.

## <a name="delete-rule"></a>Excluir regra

Para excluir uma regra, siga estas etapas:

1. Na home page, selecione **regras** no menu de navegação à esquerda. A janela regras é exibida.
2. Selecione a regra para a qual você deseja excluir.

    ![Captura de tela que realça o botão de exclusão.](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. Selecione **excluir** na barra de ação.

    ![Projeto do FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. A caixa de diálogo **excluir regra** é exibida. Selecione **Excluir**.
