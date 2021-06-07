---
title: Excluir uma conta Executar como da automação do Azure
description: Este artigo informa como excluir uma conta Executar como com o PowerShell ou da portal do Azure.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: 6924a9a9394d237b08db878ef910de6767ca9b39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055615"
---
# <a name="delete-an-azure-automation-run-as-account"></a>Excluir uma conta Executar como da automação do Azure

As contas Executar como na automação do Azure fornecem autenticação para gerenciar recursos no Azure Resource Manager ou no modelo de implantação clássico do Azure usando runbooks de automação e outros recursos de automação. Este artigo descreve como excluir uma conta Executar como ou executar como clássica. Quando você executa essa ação, a conta de Automação é mantida. Depois de excluir a conta Executar como, você poderá recriá-la no portal do Azure ou com o script do PowerShell fornecido.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Excluir uma conta Executar como ou Executar como Clássica

1. No Portal do Azure, abra a Conta de automação.

2. No painel do lado esquerdo, selecione **Contas Executar como** na seção Configurações de conta.

3. Na página de propriedades de Contas Executar como, selecione a conta Executar como ou a conta Executar como Clássica que você deseja excluir.

4. No painel Propriedades da conta selecionada, clique em **Excluir**.

   ![Excluir Conta Executar como](media/delete-run-as-account/automation-account-delete-run-as.png)

5. Enquanto a conta está sendo excluída, você poderá acompanhar o andamento em **Notificações** no menu.

## <a name="next-steps"></a>Próximas etapas

Para recriar suas contas Executar como ou executar como clássica, consulte [criar contas Executar como](create-run-as-account.md).