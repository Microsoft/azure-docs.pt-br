---
title: "Gerenciamento de conta de automação do Azure | Microsoft Docs"
description: "Este artigo descreve como gerenciar a configuração da sua conta de automação, como renovação de certificados, exclusão e uma configuração incorreta."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/13/2017
ms.author: magoedte
ms.openlocfilehash: fa3109f15cf14a95af35e814fb61a505d3130462
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-automation-account"></a>Como gerenciar uma conta de Automação do Azure
Em algum momento antes que a conta de Automação expire, será necessário renovar o certificado. Se achar que a conta Executar como foi comprometida, você poderá excluí-la e recriá-la. Esta seção descreve como realizar essas operações.

## <a name="self-signed-certificate-renewal"></a>Renovação do certificado autoassinado
O certificado autoassinado que você criou para a conta Executar como expira um ano a contar da data de criação. Você pode renová-lo a qualquer momento antes que ele expire. Quando você o renova, o certificado atual é mantido para garantir que todos os runbooks que estão enfileiradas ou ativamente em execução e que se autenticam com a conta Executar como não sejam afetados negativamente. O certificado permanece válido até a data de expiração.

> [!NOTE]
> Se tiver configurado a conta Executar como da Automação para usar um certificado emitido por a autoridade de certificação corporativa e usar essa opção, o certificado da empresa será substituído por um certificado autoassinado.

Para renovar o certificado, faça o seguinte:

1. No Portal do Azure, abra a Conta de automação.

2. Na **Conta de Automação**, 
3. no painel **Propriedades da conta**, em **Configurações da Conta**, selecione **Contas Executar como**.

    ![Painel de propriedades da conta de Automação](media/automation-manage-account/automation-account-properties-pane.png)
3. Na página de propriedades **Contas Executar como**, selecione a conta Executar como ou a conta Executar como Clássica para a qual você deseja renovar o certificado.

4. No painel **Propriedades** da conta selecionada, clique em **Renovar certificado**.

    ![Renovar o certificado da conta Executar como](media/automation-manage-account/automation-account-renew-runas-certificate.png)

5. Enquanto o certificado está sendo renovado, você poderá acompanhar o andamento em **Notificações** no menu.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Excluir uma conta Executar como ou Executar como Clássica
Esta seção descreve como excluir e recriar uma conta Executar como ou Executar como Clássica. Quando você executa essa ação, a conta de Automação é mantida. Após excluir uma conta Executar como ou Executar como Clássica, você pode recriá-la no portal do Azure.

1. No Portal do Azure, abra a Conta de automação.

2. Na página **Conta de automação**, selecione **Contas Executar como**.

3. Na página de propriedades de **Contas Executar como**, selecione a conta Executar como ou a conta Executar como Clássica que você deseja excluir. Em seguida, no painel **Propriedades** da conta selecionada, clique em **Excluir**.

 ![Excluir Conta Executar como](media/automation-manage-account/automation-account-delete-runas.png)

4. Enquanto a conta está sendo excluída, você poderá acompanhar o andamento em **Notificações** no menu.

5. Depois que a conta for excluída, você poderá recriá-la na página de propriedades **Contas Executar como** selecionando a opção de criação **Executar como Conta do Azure**.

 ![Recriar a conta de Automação Executar como](media/automation-manage-account/automation-account-create-runas.png)

## <a name="misconfiguration"></a>Configuração incorreta
Alguns itens de configuração necessários para que a conta Executar como ou Executar como Clássica funcione corretamente podem foram excluídos ou criados incorretamente durante a instalação inicial. Os itens incluem:

* Ativo de certificado
* Ativo de conexão
* A Conta Executar como foi removida da função de Colaborador
* Entidade de serviço ou aplicativo no Azure AD

Nos casos anteriores e em outras instâncias de uma configuração incorreta, a conta de Automação detecta as alterações e exibe o status *Incompleto* na página de propriedades **Contas Executar como** para a conta.

![Status incompleto de configuração de conta Executar como](media/automation-manage-account/automation-account-runas-incomplete-config.png)

Quando você selecionar a conta Executar como, o painel **Propriedades** da conta exibirá a seguinte mensagem de erro:

![Mensagem de aviso incompleto da configuração da conta Executar como](media/automation-manage-account/automation-account-runas-incomplete-config-msg.png).

Você pode resolver rapidamente esses problemas de conta Executar como excluindo e recriando a conta.

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre Entidades de Serviço, veja [Objetos de aplicativo e objetos de entidade de serviço](../active-directory/active-directory-application-objects.md).
* Para saber mais sobre o Controle de Acesso baseado em Função na Automação do Azure, consulte [Controle de acesso baseado em função na Automação do Azure](automation-role-based-access-control.md).
* Para saber mais sobre certificados e serviços do Azure, confira [Visão geral dos certificados de serviços de nuvem do Azure](../cloud-services/cloud-services-certs-create.md).