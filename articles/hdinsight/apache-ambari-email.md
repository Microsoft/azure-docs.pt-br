---
title: 'Tutorial: Configurar notificações por email do Apache Ambari no Azure HDInsight'
description: Este artigo descreve como usar o SendGrid com o Apache Ambari para receber notificações por email.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: 5b344c0c4b1db9159d0223c861e5d371cb225f5a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867195"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>Tutorial: Configurar notificações por email do Apache Ambari no Azure HDInsight

Neste tutorial, você vai configurar as notificações por email do Apache Ambari usando o SendGrid. O [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) simplifica o gerenciamento e monitoramento de um cluster HDInsight fornecendo uma forma fácil de usar a interface do usuário da Web e a API REST. O Ambari está incluído em clusters HDInsight e é usado para monitorar o cluster e fazer alterações de configuração. [SendGrid](https://sendgrid.com/solutions/) é um serviço de email gratuito baseado em nuvem que fornece uma entrega de email transacional, escalabilidade e análise em tempo real confiáveis com APIs flexíveis que facilitam a integração personalizada. Os clientes do Azure podem desbloquear 25.000 e-mails livres por mês.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Obter o nome de usuário do Sendgrid
> * Configurar notificações por email do Apache Ambari

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de email do SendGrid. Confira [Como enviar emails usando o SendGrid com o Azure](../sendgrid-dotnet-how-to-send-email.md) para obter instruções.

* Um cluster HDInsight. Confira [Criar clusters Apache Hadoop usando o portal do Azure](./hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="obtain-sendgrid-username"></a>Obter o nome de usuário do SendGrid

1. No [portal do Azure](https://portal.azure.com), navegue até o recurso do SendGrid.

1. Na página Visão Geral, selecione **Gerenciar**, acesse a página do SendGrid da sua conta.

    :::image type="content" source="./media/apache-ambari-email/azure-portal-sendgrid-manage.png" alt-text="Visão geral do SendGrid no portal do azure":::

1. No menu esquerdo, navegue até o nome da sua conta e, em seguida, **Detalhes da Conta**.

    :::image type="content" source="./media/apache-ambari-email/sendgrid-dashboard-navigation.png" alt-text="Navegação do painel do SendGrid":::

1. Na página **Detalhes da Conta**, registre o **Nome de Usuário**.

    :::image type="content" source="./media/apache-ambari-email/sendgrid-account-details.png" alt-text="Detalhes da conta do SendGrid":::

## <a name="configure-ambari-e-mail-notification"></a>Configurar a notificação por email do Ambari

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts`, em que `CLUSTERNAME` é o nome do cluster.

1. Na lista suspensa **Ações**, selecione **Gerenciar notificações**.

1. Na janela **Gerenciar notificações de alerta**, selecione o ícone **+** .

    :::image type="content" source="./media/apache-ambari-email/azure-portal-create-notification.png" alt-text="A captura de tela mostra a caixa de diálogo Gerenciar Notificações de Alerta.":::

1. Na caixa de diálogo **Criar Notificação de Alerta**, forneça as seguintes informações:

    |Propriedade |Descrição |
    |---|---|
    |Nome|Fornecer um nome para o bloco de anotações.|
    |Grupos|Configure-a, conforme desejado.|
    |Severity|Configure-a, conforme desejado.|
    |Descrição|Opcional.|
    |Método|Deixe como **EMAIL**.|
    |Email Para|Fornecer email para receber notificações, separados por uma vírgula.|
    |Servidor SMTP|`smtp.sendgrid.net`|
    |Porta SMTP|25 ou 587 (para conexões não criptografadas/TLS).|
    |Email De|Fornecer um endereço de email. O endereço não precisa ser autêntico.|
    |Usar autenticação|Marcar essa caixa de seleção.|
    |Nome de Usuário|Forneça o nome de usuário do SendGrid.|
    |Senha|Forneça a senha usada quando você criou o recurso do SendGrid no Azure.|
    |Confirmação de Senha|Reinsira a senha.|
    |Iniciar o TLS|Marcar essa caixa de seleção|

    :::image type="content" source="./media/apache-ambari-email/ambari-create-alert-notification.png" alt-text="A captura de tela mostra a caixa de diálogo Criar Notificação de Alerta.":::

    Clique em **Salvar**. Você voltará para a janela **Gerenciar Notificações de Alerta**.

1. Na janela **Gerenciar Notificações de Alerta**, selecione **Fechar**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a configurar notificações por email do Apache Ambari usando o SendGrid. Use o seguinte para saber mais sobre o Apache Ambari:

* [Gerenciar clusters HDInsight usando a interface do usuário da Web do Apache Ambari](./hdinsight-hadoop-manage-ambari.md)

* [Criar uma notificação de alerta](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)