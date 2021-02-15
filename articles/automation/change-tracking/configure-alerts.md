---
title: Como criar alertas para Controle de Alterações de automação e inventário do Azure
description: Este artigo informa como configurar alertas do Azure para notificar sobre o status das alterações detectadas pelo Controle de Alterações e pelo inventário.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/15/2020
ms.topic: conceptual
ms.openlocfilehash: 275e57e5dcf173e8d5f30f262641b02698910422
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209404"
---
# <a name="how-to-create-alerts-for-change-tracking-and-inventory"></a>Como criar alertas para Controle de Alterações e inventário

Os alertas no Azure notificam proativamente os resultados de trabalhos de runbook, problemas de integridade do serviço ou outros cenários relacionados à sua conta de automação. A automação do Azure não inclui regras de alerta pré-configuradas, mas você pode criar suas próprias com base nos dados que ele gera. Este artigo fornece orientação sobre como criar regras de alerta com base nas alterações identificadas por Controle de Alterações e inventário.

Se você não estiver familiarizado com alertas de Azure Monitor, consulte [visão geral de alertas no Microsoft Azure](../../azure-monitor/platform/alerts-overview.md) antes de iniciar. Para saber mais sobre alertas que usam consultas de log, consulte [alertas de log em Azure monitor](../../azure-monitor/platform/alerts-unified-log.md).

## <a name="create-alert"></a>Criar alerta

O exemplo a seguir mostra que o arquivo **c:\Windows\System32\drivers\etc\hosts** foi modificado em um computador. Esse arquivo é importante porque o Windows o usa para resolver nomes de host para endereços IP. Essa operação tem precedência sobre o DNS e pode resultar em problemas de conectividade. Ele também pode levar ao redirecionamento de tráfego para sites mal-intencionados ou perigosos.

![Gráfico mostrando a alteração do arquivo de hosts](./media/configure-alerts/changes.png)

Vamos usar este exemplo para discutir as etapas para criar alertas em uma alteração.

1. Na página **controle de alterações** da sua conta de automação, selecione **log Analytics**.

2. Na pesquisa de logs, procure alterações de conteúdo no arquivo **hosts** com a consulta `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Essa consulta procura alterações de conteúdo de arquivos com nomes de caminho totalmente qualificados contendo a palavra `hosts` . Você também pode solicitar um arquivo específico alterando a parte do caminho para sua forma totalmente qualificada, por exemplo, usando `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`.

3. Depois que a consulta retornar seus resultados, selecione **nova regra de alerta** na pesquisa de logs para abrir a página de **criação de alertas** . Você também pode navegar até essa página por meio do **Azure Monitor** no portal do Azure.

4. Verifique a consulta novamente e modifique a lógica de alerta. Nesse caso, você desejará que o alerta seja disparado se for detectada até mesmo uma alteração entre todos os computadores no ambiente.

    ![Alterar para consulta de controle de alterações no arquivo de hosts](./media/configure-alerts/change-query.png)

5. Depois que a lógica de alerta for definida, atribua grupos de ações para executar ações em resposta ao disparo do alerta. Nesse caso, estamos configurando emails a serem enviados e um tíquete de ITSM (Gerenciamento de Serviços de TI) a ser criado.

Siga as etapas abaixo para configurar alertas para que você saiba o status de uma implantação de atualização. Se você for novo nos alertas do Azure, consulte [visão geral de alertas do Azure](../../azure-monitor/platform/alerts-overview.md).

## <a name="configure-action-groups-for-your-alerts"></a>Configurar grupos de ações para seus alertas

Depois de configurar os alertas, você pode configurar um grupo de ações, que é um grupo de ações a serem usadas em vários alertas. As ações podem incluir notificações por email, runbooks, WebHooks e muito mais. Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](../../azure-monitor/platform/action-groups.md).

1. Selecione um alerta e, em seguida, selecione **Criar** em **Grupos de Ações**.

2. Insira um nome completo e um nome curto para o grupo de ações. O Gerenciamento de Atualizações usa o nome curto ao enviar notificações usando o grupo especificado.

3. Em **Ações**, insira um nome que especifique a ação, por exemplo, **Notificação por email**.

4. Para **Tipo de ação**, selecione o tipo apropriado, por exemplo, **Email/SMS/Push/Voz**.

5. Selecione em **Editar detalhes**.

6. Preencha o painel do tipo de ação. Por exemplo, se estiver usando **email/SMS/Push/voz**, insira um nome de ação, marque a caixa de seleção **email** , insira um endereço de email válido e, em seguida, selecione **OK**.

    ![Configurar um grupo de ação de email](./media/configure-alerts/configure-email-action-group.png)

7. No painel Adicionar grupo de ações, selecione **OK**.

8. Para um email de alerta, você pode personalizar o assunto do email. Selecione **Personalizar ações** em **Criar regra** e, em seguida, selecione **Assunto do email**.

9. Quando terminar, selecione **Criar regra de alerta**.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [alertas no Azure monitor](../../azure-monitor/platform/alerts-overview.md).

* Saiba mais sobre [consultas de log](../../azure-monitor/log-query/log-query-overview.md) para recuperar e analisar dados de um espaço de trabalho log Analytics.

* Gerenciar o [uso e os custos com logs de Azure monitor](../../azure-monitor/platform/manage-cost-storage.md) descreve como controlar seus custos alterando seu período de retenção de dados e como analisar e alertar o uso de dados.