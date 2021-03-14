---
title: Conectar Configuration Manager ao Azure Monitor | Microsoft Docs
description: Este artigo mostra as etapas para se conectar Configuration Manager ao espaço de trabalho no Azure Monitor e iniciar a análise de dados.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/30/2020
ms.openlocfilehash: 7302f61fce6763fdbb41c2b4899dea749ac3225f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031065"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Conectar Configuration Manager ao Azure Monitor
Você pode conectar seu ambiente de Configuration Manager de ponto de extremidade da Microsoft para Azure Monitor para sincronizar dados de coleta de dispositivos e fazer referência a essas coleções no Azure Monitor e na automação do Azure.  

> [!IMPORTANT]
> A partir do Configuration Manager versão 2010, esse recurso foi preterido.<!-- 8269855 --> Para obter mais informações, confira [Recursos removidos e preteridos do Configuration Manager](/mem/configmgr/core/plan-design/changes/deprecated/removed-and-deprecated-cmfeatures).

## <a name="prerequisites"></a>Pré-requisitos

O Azure Monitor dá suporte Configuration Manager Branch atual, versão 1606 e superior.

>[!NOTE]
>O recurso para se conectar Configuration Manager com um espaço de trabalho de Log Analytics é opcional e não é habilitado por padrão. É necessário habilitar esse recurso antes de usá-lo. Para obter mais informações, consulte [Enable optional features from updates (Habilitar recursos opcionais de atualizações)](/configmgr/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Visão geral de configuração

As etapas a seguir resumem as etapas para configurar a integração de Configuration Manager com o Azure Monitor.  

1. No Azure Active Directory, registre Configuration Manager como um aplicativo Web e/ou aplicativo de API Web e verifique se você tem a ID do cliente e a chave secreta do cliente do registro de Azure Active Directory. Consulte [Usar o portal para criar um aplicativo e entidade de serviço do Active Directory que pode acessar recursos](../../active-directory/develop/howto-create-service-principal-portal.md) para obter informações detalhadas sobre como executar essa etapa.

2. No Azure Active Directory, [conceda Configuration Manager (o aplicativo Web registrado) com permissão para acessar Azure monitor](#grant-configuration-manager-with-permissions-to-log-analytics).

3. Em Configuration Manager, adicione uma conexão usando o assistente de **Serviços do Azure** .

4. [Baixe e instale o agente do log Analytics para Windows](#download-and-install-the-agent) no computador que executa a função do sistema de site do ponto de conexão de serviço Configuration Manager. O agente envia Configuration Manager dados para o espaço de trabalho Log Analytics no Azure Monitor.

5. No Azure Monitor, [importe coleções de Configuration Manager](#import-collections) como grupos de computadores.

6. Em Azure Monitor, exiba dados de Configuration Manager como [grupos de computadores](computer-groups.md).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Conceder o Configuration Manager com permissões para o Log Analytics

No procedimento a seguir, você concede a função *Colaborador* no espaço de trabalho do Log Analytics ao aplicativo do AD e à entidade de serviço criada anteriormente para o Configuration Manager. Se você ainda não tiver um espaço de trabalho, consulte [criar um espaço de trabalho no Azure monitor antes de](./quick-create-workspace.md) continuar. Isso permite que o Configuration Manager autentique e conecte-se ao espaço de trabalho do Log Analytics.  

> [!NOTE]
> Você deve especificar permissões no espaço de trabalho Log Analytics para Configuration Manager. Caso contrário, você receberá uma mensagem de erro ao usar o assistente de configuração do Configuration Manager.
>

1. No portal do Azure, clique em **Todos os serviços**, encontrado no canto superior esquerdo. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione o **Log Analytics**.

2. Na lista de workspaces do Log Analytics, selecione o workspace para modificar.

3. No painel esquerdo, selecione **Controle de acesso (IAM)**.

4. Na página Controle de acesso (IAM), clique em **Adicionar atribuição de função** e o painel **Adicionar atribuição de função** será exibido.

5. No painel **Adicionar atribuição de função** na lista suspensa **Função**, selecione a função **Colaborador**.  

6. Na lista suspensa **Atribuir acesso a** selecione o aplicativo Configuration Manager criado no AD anteriormente e clique em **OK**.  

## <a name="download-and-install-the-agent"></a>Baixe e instale o agente

Examine o artigo [conectar computadores Windows ao Azure monitor no Azure](../agents/agent-windows.md) para entender os métodos disponíveis para instalar o agente de log Analytics para Windows no computador que hospeda a função do sistema de sites do ponto de conexão de serviço do Configuration Manager.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Conectar Configuration Manager ao espaço de trabalho Log Analytics

>[!NOTE]
> Para adicionar uma conexão de Log Analytics, seu ambiente de Configuration Manager deve ter um [ponto de conexão de serviço](/configmgr/core/servers/deploy/configure/about-the-service-connection-point) configurado para o modo online.

> [!NOTE]
> Você deve conectar o site de camada superior em sua hierarquia para Azure Monitor. Se você conectar um site primário autônomo a Azure Monitor e, em seguida, adicionar um site de administração central ao seu ambiente, será necessário excluir e recriar a conexão na nova hierarquia.

1. No espaço de trabalho **Administração** de Configuration Manager, selecione **nuvens serviços** e, em seguida, selecione **Serviços do Azure**. 

2. Clique com o botão direito do mouse em **Serviços do Azure** e selecione **Configurar serviços do Azure**. A página **Configurar serviços do Azure** é exibida. 
   
3. Na tela **Geral**, confirme se você realizou as seguintes ações e se tem os detalhes de cada item, em seguida, selecione **Avançar**.

4. Na página Serviços do Azure do Assistente de Serviços do Azure:

    1. Especifique um **Nome** para o objeto no Configuration Manager.
    2. Especifique uma **Descrição** opcional para ajudá-lo a identificar o serviço.
    3. Selecione o conector do **OMS** de serviço do Azure.

    >[!NOTE]
    >O OMS agora é conhecido como Log Analytics, que é um recurso do Azure Monitor.

5. Selecione **Avançar** para continuar para a página Propriedades do aplicativo do Azure do Assistente de Serviços do Azure.

6. Na página **aplicativo** do assistente de serviços do Azure, primeiro selecione o ambiente do Azure na lista e clique em **importar**.

7. Na página **importar aplicativos** , especifique as seguintes informações:

    1. Especifique o **nome do locatário do Azure ad** para o aplicativo.

    2. Especifique para a **ID de locatário do Azure ad** o locatário do Azure AD. Você pode encontrar essas informações na página de **propriedades** Azure Active Directory. 

    3. Especifique para **nome do aplicativo** o nome do aplicativo.

    4. Especifique para **ID do cliente**, a ID do aplicativo criado pelo aplicativo do Azure ad criado anteriormente.

    5. Especifique para a **chave secreta**, a chave secreta do cliente do aplicativo do Azure ad criado.

    6. Especifique a **expiração da chave secreta**, a data de validade da sua chave.

    7. Especifique para o **URI da ID do aplicativo**, o URI da ID do aplicativo criado no Azure ad criado anteriormente.

    8. Selecione **verificar** e, à direita, os resultados devem mostrar **verificados com êxito!**.

8. Na página **configuração** , examine as informações para verificar se os campos **assinaturas do Azure**, **grupo de recursos do Azure** e **espaço de trabalho do Operations Management Suite** estão preenchidos previamente, indicando que o aplicativo Azure ad tem permissões suficientes no grupo de recursos. Se os campos estiverem vazios, indica que seu aplicativo não tem os direitos necessários. Selecione as coleções de dispositivos a serem coletadas e encaminhadas para o espaço de trabalho e, em seguida, selecione **Adicionar**.

9. Examine as opções na página **confirmar as configurações** e selecione **Avançar** para começar a criar e configurar a conexão.

10. Quando a configuração for concluída, a página **conclusão** será exibida. Selecione **Fechar**. 

Depois de vincular Configuration Manager ao Azure Monitor, você pode adicionar ou remover coleções e exibir as propriedades da conexão.

## <a name="update-log-analytics-workspace-connection-properties"></a>Atualizar propriedades de conexão do espaço de trabalho Log Analytics

Se uma senha ou chave secreta do cliente expirar ou for perdida, você precisará atualizar manualmente as propriedades de conexão do Log Analytics.

1. No espaço de trabalho **Administração** do Configuration Manager, selecione **serviços de nuvem** e, em seguida, selecione conector do **OMS** para abrir a página Propriedades de **conexão do OMS** .
2. Nessa página, clique na guia **Azure Active Directory** para exibir seu **Locatário**, **ID do Cliente**, **Client secret key expiration (Expiração da chave secreta do cliente)**. **Verifique se** sua **Chave secreta do cliente** caso ela tenha expirado.

## <a name="import-collections"></a>Importe coleções

Depois de adicionar uma conexão de Log Analytics para Configuration Manager e instalar o agente no computador que executa a função de sistema de site do ponto de conexão de serviço Configuration Manager, a próxima etapa é importar coleções de Configuration Manager no Azure Monitor como grupos de computadores.

Depois de concluir a configuração inicial para importar coleções de dispositivos de sua hierarquia, as informações de coleção são recuperadas a cada três horas para manter a associação atual. É possível escolher desabilitar isso a qualquer momento.

1. No portal do Azure, clique em **Todos os serviços**, encontrado no canto superior esquerdo. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Escolha **workspaces do Log Analytics**.
2. Na sua lista de workspaces do Log Analytics, selecione o workspace no qual o Configuration Manager está registrado.  
3. Selecione **Configurações avançadas**.
4. Selecione **Grupos de Computadores** e, em seguida, selecione **SCCM**.  
5. Selecione **Importar associações de coleta do Configuration Manager** e clique em **Salvar**.  
   
    ![Captura de tela das configurações avançadas de grupos de computadores para S c C M contendo a opção para importar Configuration Manager associações de coleção.](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Exibir dados do Configuration Manager

Depois de adicionar uma conexão de Log Analytics para Configuration Manager e instalar o agente no computador que executa a função de sistema de site do ponto de conexão de serviço Configuration Manager, os dados do agente serão enviados para o espaço de trabalho Log Analytics no Azure Monitor. Em Azure Monitor, suas coleções de Configuration Manager aparecem como [grupos de computadores](./computer-groups.md). Você pode exibir os grupos da página **Configuration Manager** em **Configurações\Grupos de Computadores**.

Depois que as coleções forem importadas, você poderá ver quantos computadores com associações de coleção foram detectados. Você também pode ver o número de coleções que foram importadas.

![Captura de tela das configurações avançadas de grupos de computadores para a S C C M mostrando a opção para importar Configuration Manager associações de coleção selecionadas.](./media/collect-sccm/sccm-computer-groups02.png)

Quando você clica em qualquer um, o editor de consultas de log é aberto exibindo todos os grupos importados ou todos os computadores que pertencem a cada grupo. Usando a [pesquisa de log](./log-query-overview.md), você pode executar uma análise mais detalhada dos dados de associação da coleção.

## <a name="next-steps"></a>Próximas etapas

Use a [Pesquisa de Log](./log-query-overview.md) para exibir informações detalhadas sobre os dados do Configuration Manager.