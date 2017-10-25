---
title: Conectar o Configuration Manager ao Log Analytics | Microsoft Docs
description: "Este artigo mostra as etapas para conectar o Configuration Manager ao Log Analytics e começar a analisar dados."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: banders
ms.openlocfilehash: 62d31ed486458245156f7fc832294d662c62991e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-configuration-manager-to-log-analytics"></a>Conectar o Configuration Manager ao Log Analytics
Você pode conectar o System Center Configuration Manager ao Log Analytics no OMS para sincronizar os dados de coleção de dispositivos. Isso disponibiliza dados da hierarquia do Configuration Manager no OMS.

## <a name="prerequisites"></a>Pré-requisitos

O Log Analytics dá suporte à ramificação atual do System Center Configuration Manager, versão 1606 e superior.  

## <a name="configuration-overview"></a>Visão geral de configuração
As etapas a seguir resumem o processo de conectar o Configuration Manager ao Log Analytics.  

1. No Portal de Gerenciamento do Azure, registre o Configuration Manager como um aplicativo Web e/ou aplicativo de API Web e certifique-se de ter a ID do cliente e a chave secreta do cliente do registro do Azure Active Directory. Consulte [Usar o portal para criar um aplicativo e entidade de serviço do Active Directory que pode acessar recursos](../azure-resource-manager/resource-group-create-service-principal-portal.md) para obter informações detalhadas sobre como executar essa etapa.
2. No Portal de Gerenciamento do Azure, [forneça ao Configuration Manager (o aplicativo Web registrado) com permissão para acessar o OMS](#provide-configuration-manager-with-permissions-to-oms).
3. No Configuration Manager, [adicione uma conexão usando o Assistente de Adição de Conexão do OMS](#add-an-oms-connection-to-configuration-manager).
4. No Configuration Manager, [atualize as propriedades de conexão](#update-oms-connection-properties) se a chave secreta do cliente ou a senha expirar ou for perdida.
5. Com as informações do portal do OMS, [baixe e instale o Microsoft Monitoring Agent](#download-and-install-the-agent) no computador executando a função de sistema de site do ponto de conexão de serviço do Configuration Manager. O agente envia dados do Configuration Manager para o OMS.
6. No Log Analytics, [importe coleções do Configuration Manager](#import-collections) como grupos de computadores.
7. No Log Analytics, exiba dados do Configuration Manager como [grupos de computadores](log-analytics-computer-groups.md).

Você pode ler mais sobre a conexão do Configuration Manager ao OMS em [Sync data from Configuration Manager to the Microsoft Operations Management Suite](https://technet.microsoft.com/library/mt757374.aspx) (Sincronizar dados do Configuration Manager com o Microsoft Operations Management Suite).

## <a name="provide-configuration-manager-with-permissions-to-oms"></a>Conceder ao Configuration Manager permissões para o OMS
O procedimento a seguir concede ao Portal de Gerenciamento do Azure permissões para o OMS. Especificamente, você deve conceder a *função Colaborador* aos usuários no grupo de recursos para permitir que o Portal de Gerenciamento do Azure conecte o Configuration Manager ao OMS.

> [!NOTE]
> Você deve especificar permissões no OMS do Configuration Manager. Caso contrário, você receberá uma mensagem de erro ao usar o assistente de configuração do Configuration Manager.
>
>

1. Abra o [Portal do Azure](https://portal.azure.com/) e clique em **Procurar** > **Log Analytics (OMS)** para abrir a folha do Log Analytics (OMS).  
2. Na folha **Log Analytics (OMS)**, clique em **Adicionar** para abrir a folha **Espaço de Trabalho do OMS**.  
   ![Folha do OMS](./media/log-analytics-sccm/sccm-azure01.png)
3. Na folha **Espaço de Trabalho do OMS**, forneça as seguintes informações e clique em **OK**.

   * **Espaço de Trabalho do OMS**
   * **Assinatura**
   * **Grupo de recursos**
   * **Localidade**
   * **Tipo de preço**  
     ![Folha do OMS](./media/log-analytics-sccm/sccm-azure02.png)  

     > [!NOTE]
     > O exemplo acima cria um novo grupo de recursos. O grupo de recursos é usado apenas para conceder ao Configuration Manager as permissões para o espaço de trabalho do OMS nesse exemplo.
     >
     >
4. Clique em **Procurar** > **Grupos de recursos** para abrir a folha **Grupos de recursos**.
5. Na folha **Grupos de recursos**, clique no grupo de recursos criado acima para abrir a folha de configurações &lt;nome de grupo de recursos&gt;.  
   ![folha de configurações do grupo de recursos](./media/log-analytics-sccm/sccm-azure03.png)
6. Na folha de configurações &lt;nome do grupo de recursos&gt;, clique em IAM (Controle de acesso) para abrir a folha Usuários do &lt;nome de grupo de recursos&gt;.  
   ![Folha de Usuários do grupo de recursos](./media/log-analytics-sccm/sccm-azure04.png)  
7. Na folha Usuários do &lt;nome do grupo de recursos&gt;, clique em **Adicionar** para abrir a folha **Adicionar acesso**.
8. Na folha **Adicionar acesso**, clique em **Selecionar uma função** e selecione a função **Colaborador**.  
   ![selecione uma função](./media/log-analytics-sccm/sccm-azure05.png)  
9. Clique em **Adicionar usuários**, selecione o usuário do Configuration Manager, clique em **Selecione** e, em seguida, clique em **OK**.  
   ![adicionar usuários](./media/log-analytics-sccm/sccm-azure06.png)  

## <a name="add-an-oms-connection-to-configuration-manager"></a>Adicionar uma conexão do OMS ao Configuration Manager
Para adicionar uma conexão do OMS, seu ambiente do Configuration Manager deve ter uma [ponto de conexão de serviço](https://technet.microsoft.com/library/mt627781.aspx) configurado para o modo online.

1. No espaço de trabalho **Administração** do Configuration Manager, selecione **Conector OMS**. Isso abre o **Assistente de Adição de Conexão do OMS**. Selecione **Avançar**.
2. Na tela **Geral**, confirme se você realizou as seguintes ações e se tem os detalhes de cada item, em seguida, selecione **Avançar**.

   1. No Portal de Gerenciamento do Azure, você registrou o Configuration Manager como um aplicativo Web e/ou aplicativo de API Web e tem a [ID do cliente do registro](../active-directory/active-directory-integrating-applications.md).
   2. No Portal de Gerenciamento do Azure, você criou uma chave secreta do aplicativo para o aplicativo registrado no Azure Active Directory.  
   3. No Portal de Gerenciamento do Azure, você concedeu ao aplicativo Web registrado a permissão para acessar o OMS.  
      ![Conexão com a página geral do Assistente do OMS](./media/log-analytics-sccm/sccm-console-general01.png)
3. Na tela **Azure Active Directory**, configure suas definições de configuração para o OMS fornecendo seu **Locatário**, **ID do Cliente** e **Chave Secreta do Cliente** e selecione **Avançar**.  
   ![Conexão com a página do Azure Active Directory do Assistente do OMS](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)
4. Se você completar todos os outros procedimentos com êxito, as informações na tela **Configuração de Conexão do OMS** aparecerão automaticamente nessa página. As informações para as configurações de conexão devem aparecer para sua **Assinatura do Azure**, **Grupo de recursos do Azure** e **Espaço de Trabalho do Operations Management Suite**.  
   ![Conexão com a página de Conexão do OMS do Assistente do OMS](./media/log-analytics-sccm/sccm-wizard-configure04.png)
5. O assistente se conecta ao serviço OMS usando as informações inseridas por você. Selecione as coleções de dispositivos que você deseja sincronizar com o OMS e, em seguida, clique em **Adicionar**.  
   ![Selecionar Coleções](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)
6. Verifique as configurações de conexão na tela **Resumo** e, em seguida, selecione **Avançar**. A tela **Progresso** mostra o status da conexão e deve **Concluir**.

> [!NOTE]
> Você deve conectar o OMS ao site de nível superior na hierarquia. Se você conectar o OMS a um site primário autônomo e, em seguida, adicionar um site de administração central ao seu ambiente, precisará excluir e recriar a conexão do OSM na nova hierarquia.
>
>

Depois de vincular o Configuration Manager ao OMS, você pode adicionar ou remover coleções e exibir as propriedades da conexão do OMS.

## <a name="update-oms-connection-properties"></a>Atualizar as propriedades da conexão do OMS
Se uma senha ou chave secreta de cliente expirar ou for perdida, você precisará atualizar as propriedades da conexão do OMS manualmente.

1. No Configuration Manager, navegue até **Serviços de Nuvem**, selecione **Conector OMS** para abrir a página **OMS Connection Properties (Propriedades da Conexão do OMS)**.
2. Nessa página, clique na guia **Azure Active Directory** para exibir seu **Locatário**, **ID do Cliente**, **Client secret key expiration (Expiração da chave secreta do cliente)**. **Verifique se** sua **Chave secreta do cliente** caso ela tenha expirado.

## <a name="download-and-install-the-agent"></a>Baixe e instale o agente
1. No portal do OMS, você deve [Baixar o arquivo de instalação do agente do OMS](log-analytics-windows-agents.md#download-the-agent-setup-file-from-oms).
2. Use um dos seguintes métodos para instalar e configurar o agente no computador que executa a função de sistema de site do ponto de conexão de serviço do Configuration Manager:
   * [Instalar o agente usando a instalação](log-analytics-windows-agents.md#install-the-agent-using-setup)
   * [Instalar o agente usando a linha de comando](log-analytics-windows-agents.md#install-the-agent-using-the-command-line)
   * [Instalar o agente usando o DSC na Automação do Azure](log-analytics-windows-agents.md#install-the-agent-using-dsc-in-azure-automation)

## <a name="import-collections"></a>Importe coleções
Depois de adicionar uma conexão do OMS ao Configuration Manager e instalar o agente no computador executando a função de sistema de site do ponto de conexão de serviço do Configuration Manager, a próxima etapa é importar coleções do Configuration Manager no OMS como grupos de computadores.

Após a importação ser habilitada, as informações de associação da coleção são recuperadas a cada 3 horas para manter as associações da coleção atualizadas. Você pode optar por desabilitar a importação a qualquer momento.

1. No portal do OMS, clique em **Configurações**.
2. Clique na guia **Grupos de Computadores** e clique na guia **SCCM**.
3. Selecione **Importar associações de coleta do Configuration Manager** e clique em **Salvar**.  
   ![Grupos de computadores – guia SCCM](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Exibir dados do Configuration Manager
Depois de adicionar uma conexão do OMS ao Configuration Manager e instalar o agente no computador executando a função de sistema de site do ponto de conexão de serviço do Configuration Manager, os dados do agente são enviados para o OMS. No OMS, suas coleções do Configuration Manager aparecem como [grupos de computadores](log-analytics-computer-groups.md). Você pode exibir os grupos da página **Configuration Manager** em **Grupos de Computadores** em **Configurações**.

Depois que as coleções forem importadas, você poderá ver quantos computadores com associações de coleção foram detectados. Você também pode ver o número de coleções que foram importadas.

![Grupos de computadores – guia SCCM](./media/log-analytics-sccm/sccm-computer-groups02.png)

Quando você clica em qualquer um, Search abre, mostrando todos os grupos importados ou todos os computadores que pertencem a cada grupo. Usando a [Pesquisa de Log](log-analytics-log-searches.md), você pode iniciar uma análise detalhada de dados do Configuration Manager.

## <a name="next-steps"></a>Próximas etapas
* Use a [Pesquisa de Log](log-analytics-log-searches.md) para exibir informações detalhadas sobre os dados do Configuration Manager.
