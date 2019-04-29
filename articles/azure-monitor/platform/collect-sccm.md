---
title: Conectar o Configuration Manager para o Azure Monitor | Microsoft Docs
description: Este artigo mostra as etapas para conectar o Configuration Manager ao espaço de trabalho no Azure Monitor e começar a analisar dados.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: magoedte
ms.openlocfilehash: e5cf89b7fe01946de9944a7026ec448cd55dd6dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741426"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Conectar o Configuration Manager para o Azure Monitor
Você pode conectar seu ambiente do System Center Configuration Manager para o Azure Monitor para sincronizar dados de coleção de dispositivo e fazer referência a essas coleções no Azure Monitor e a automação do Azure.  

## <a name="prerequisites"></a>Pré-requisitos

O Azure Monitor oferece suporte a ramificação atual do System Center Configuration Manager, versão 1606 e superior.  

## <a name="configuration-overview"></a>Visão geral de configuração
As etapas a seguir resumem as etapas para configurar a integração do Configuration Manager com o Azure Monitor.  

1. No portal do Azure, registre o Configuration Manager como um aplicativo Web e/ou aplicativo de API Web e certifique-se de ter a ID do cliente e a chave secreta do cliente do registro do Azure Active Directory. Consulte [Usar o portal para criar um aplicativo e entidade de serviço do Active Directory que pode acessar recursos](../../active-directory/develop/howto-create-service-principal-portal.md) para obter informações detalhadas sobre como executar essa etapa.
2. No portal do Azure [conceder o Configuration Manager (o aplicativo web registrado) com permissão para acessar o Azure Monitor](#grant-configuration-manager-with-permissions-to-log-analytics).
3. No Configuration Manager, adicione uma conexão usando o Assistente de Adição de Conexão do OMS.
4. No Configuration Manager, atualize as propriedades de conexão se a chave secreta do cliente ou a senha expirar ou for perdida.
5. [Baixe e instale o Microsoft Monitoring Agent](#download-and-install-the-agent) no computador que execute a função de sistema do site de ponto de conexão de serviço do Configuration Manager. O agente envia dados do Configuration Manager ao espaço de trabalho do Log Analytics no Azure Monitor.
6. No Azure Monitor [importar coleções do Configuration Manager](#import-collections) como grupos de computadores.
7. No Azure Monitor, exibir dados do Configuration Manager como [grupos de computadores](computer-groups.md).

Você pode ler mais sobre como conectar o Configuration Manager para o Azure Monitor no [sincronizar dados do Configuration Manager para o espaço de trabalho do Log Analytics do Microsoft](https://technet.microsoft.com/library/mt757374.aspx).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Conceder o Configuration Manager com permissões para o Log Analytics
No procedimento a seguir, você concede a função *Colaborador* no espaço de trabalho do Log Analytics ao aplicativo do AD e à entidade de serviço criada anteriormente para o Configuration Manager.  Se você ainda não tiver um espaço de trabalho, consulte [criar um espaço de trabalho no Azure Monitor](../../azure-monitor/learn/quick-create-workspace.md) antes de continuar.  Isso permite que o Configuration Manager autentique e conecte-se ao espaço de trabalho do Log Analytics.  

> [!NOTE]
> Você deve especificar permissões no espaço de trabalho do Log Analytics para o Configuration Manager. Caso contrário, você receberá uma mensagem de erro ao usar o assistente de configuração do Configuration Manager.
>

1. No Portal do Azure, clique em **Todos os serviços**, localizado no canto superior esquerdo. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.
2. Na lista de workspaces do Log Analytics, selecione o workspace para modificar.
3. No painel esquerdo, selecione **Controle de acesso (IAM)**.
4. Na página Controle de acesso (IAM), clique em **Adicionar atribuição de função** e o painel **Adicionar atribuição de função** será exibido.
5. No painel **Adicionar atribuição de função** na lista suspensa **Função**, selecione a função **Colaborador**.  
6. Na lista suspensa **Atribuir acesso a** selecione o aplicativo Configuration Manager criado no AD anteriormente e clique em **OK**.  

## <a name="download-and-install-the-agent"></a>Baixe e instale o agente
Examine o artigo [computadores Windows conectar-se para o Azure Monitor no Azure](agent-windows.md) para entender os métodos disponíveis para instalar o Microsoft Monitoring Agent no computador que hospeda o ponto de conexão de serviço do Configuration Manager função do sistema de site.  

## <a name="add-a-log-analytics-connection-to-configuration-manager"></a>Adicionar uma conexão do Log Analytics ao Configuration Manager
Para adicionar uma conexão do Log Analytics, seu ambiente do Configuration Manager deve ter uma [ponto de conexão de serviço](https://technet.microsoft.com/library/mt627781.aspx) configurado para o modo online.

1. No workspace **Administração** do Configuration Manager, selecione **Conector OMS**. Isso abre o **Assistente de Adicionar Conexão do Log Analytics**. Selecione **Avançar**.

   >[!NOTE]
   >OMS agora é chamado de análise de Log que é um recurso do Azure Monitor.
   
2. Na tela **Geral**, confirme se você realizou as seguintes ações e se tem os detalhes de cada item, em seguida, selecione **Avançar**.

   1. No portal do Azure, você registrou o Configuration Manager como um aplicativo Web e/ou aplicativo de API Web e tem a [ID do cliente do registro](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md).
   2. No portal do Azure, você criou uma chave secreta do aplicativo para o aplicativo registrado no Azure Active Directory.  
   3. No portal do Azure, você concedeu ao aplicativo web registrado com permissão para acessar o espaço de trabalho do Log Analytics no Azure Monitor.  
      ![Conexão para a página geral do Assistente do Log Analytics](./media/collect-sccm/sccm-console-general01.png)
3. Sobre o **Active Directory do Azure** tela, defina as configurações de conexão para o espaço de trabalho do Log Analytics, fornecendo seu **locatário**, **ID do cliente**e **Chave secreta do cliente**, em seguida, selecione **próxima**.  
   ![Conexão com a página do Azure Active Directory do Assistente do Log Analytics](./media/collect-sccm/sccm-wizard-tenant-filled03.png)
4. Se você completar todos os outros procedimentos com êxito, as informações na tela **Configuração de Conexão do OMS** aparecerão automaticamente nessa página. As informações para as configurações de conexão devem aparecer para sua **Assinatura do Azure**, **Grupo de recursos do Azure** e **Workspace do Operations Management Suite**.  
   ![Conexão à página Conexão do Log Analytics do Assistente do Log Analytics](./media/collect-sccm/sccm-wizard-configure04.png)
5. O assistente se conecta ao espaço de trabalho do Log Analytics usando as informações que você inseriu. Selecione as coleções de dispositivos que você deseja sincronizar com o serviço e clique em **Adicionar**.  
   ![Selecionar Coleções](./media/collect-sccm/sccm-wizard-add-collections05.png)
6. Verifique as configurações de conexão na tela **Resumo** e, em seguida, selecione **Avançar**. A tela **Progresso** mostra o status da conexão e deve **Concluir**.

> [!NOTE]
> Você deve conectar o site de nível superior na hierarquia para o Azure Monitor. Se você se conectar a um site primário autônomo para o Azure Monitor e, em seguida, adicionar um site de administração central ao seu ambiente, você precisa excluir e recriar a conexão dentro da nova hierarquia.
>
>

Depois que você tiver vinculado o Configuration Manager para o Azure Monitor, você pode adicionar ou remover coleções e exibir as propriedades da conexão.

## <a name="update-log-analytics-workspace-connection-properties"></a>Atualizar propriedades de conexão de área de trabalho do Log Analytics
Se uma senha ou chave secreta do cliente expirar ou for perdida, será necessário atualizar manualmente as propriedades de conexão do Log Analytics.

1. No Configuration Manager, navegue até **Serviços de Nuvem**, selecione **Conector OMS** para abrir a página **OMS Connection Properties (Propriedades da Conexão do OMS)**.
2. Nessa página, clique na guia **Azure Active Directory** para exibir seu **Locatário**, **ID do Cliente**, **Client secret key expiration (Expiração da chave secreta do cliente)**. **Verifique se** sua **Chave secreta do cliente** caso ela tenha expirado.

## <a name="import-collections"></a>Importe coleções
Depois de adicionar uma conexão do Log Analytics ao Configuration Manager e o agente foi instalado no computador que executa a conexão de serviço do Configuration Manager ponto de função do sistema de site, a próxima etapa é importar coleções do Configuration Manager no Azure Monitorar como grupos de computadores.

Após concluir a configuração inicial para importar coleções de dispositivos de sua hierarquia, as informações de associação de coleta serão recuperadas a cada 3 horas para manter a associação atual. É possível escolher desabilitar isso a qualquer momento.

1. No Portal do Azure, clique em **Todos os serviços**, localizado no canto superior esquerdo. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Escolha **workspaces do Log Analytics**.
2. Na sua lista de workspaces do Log Analytics, selecione o workspace no qual o Configuration Manager está registrado.  
3. Selecione **Configurações avançadas**.
4. Selecione **Grupos de Computadores** e, em seguida, selecione **SCCM**.  
5. Selecione **Importar associações de coleta do Configuration Manager** e clique em **Salvar**.  
   ![Grupos de computadores – guia SCCM](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Exibir dados do Configuration Manager
Depois de adicionar uma conexão do Log Analytics ao Configuration Manager e instalar o agente no computador que executa a função de sistema de site do Configuration Manager service conexão ponto, os dados do agente são enviados ao espaço de trabalho do Log Analytics no Azure Monitor. No Azure Monitor, suas coleções do Configuration Manager aparecem como [grupos de computadores](../../azure-monitor/platform/computer-groups.md). Você pode exibir os grupos da página **Configuration Manager** em **Configurações\Grupos de Computadores**.

Depois que as coleções forem importadas, você poderá ver quantos computadores com associações de coleção foram detectados. Você também pode ver o número de coleções que foram importadas.

![Grupos de computadores – guia SCCM](./media/collect-sccm/sccm-computer-groups02.png)

Quando você clica em qualquer um, Search abre, mostrando todos os grupos importados ou todos os computadores que pertencem a cada grupo. Usando a [Pesquisa de Log](../../azure-monitor/log-query/log-query-overview.md), você pode iniciar uma análise detalhada de dados do Configuration Manager.

## <a name="next-steps"></a>Próximas etapas
* Use a [Pesquisa de Log](../../azure-monitor/log-query/log-query-overview.md) para exibir informações detalhadas sobre os dados do Configuration Manager.
