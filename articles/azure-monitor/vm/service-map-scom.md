---
title: Integrar o mapa de informações de VM com o Operations Manager | Microsoft Docs
description: O virtual insights descobre automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Este artigo aborda o uso do recurso de mapa para criar automaticamente diagramas de aplicativos distribuídos no Operations Manager.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 3a7d0d49313cb524a5bf39add5c9a55862dcad47
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046883"
---
# <a name="integrate-system-center-operations-manager-with-vm-insights-map-feature"></a>Integrar System Center Operations Manager com o recurso de mapa de informações de VM

No insights de VM, você pode exibir componentes de aplicativos descobertos em VMs (máquinas virtuais) do Windows e Linux que são executadas no Azure ou em seu ambiente. Com essa integração entre o recurso de mapa e System Center Operations Manager, você pode criar automaticamente diagramas de aplicativo distribuídos em Operations Manager que se baseiam nos mapas de dependência dinâmica no insights de VM. Este artigo descreve como configurar seu grupo de gerenciamento de System Center Operations Manager para dar suporte a esse recurso.

>[!NOTE]
>Se você já tiver implantado Mapa do Serviço, poderá exibir seus mapas no insights da VM, o que inclui recursos adicionais para monitorar a integridade e o desempenho da VM. O recurso de mapa do VM insights destina-se a substituir a solução de Mapa do Serviço autônoma. Para saber mais, confira [visão geral do VM insights](../vm/vminsights-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um grupo de gerenciamento de System Center Operations Manager (2012 R2 ou posterior).
* Um espaço de trabalho Log Analytics configurado para dar suporte a informações de VM.
* Uma ou mais máquinas virtuais do Windows e Linux ou computadores físicos que são monitorados pelo Operations Manager e enviando dados para seu espaço de trabalho do Log Analytics. Os servidores Linux que se reportam a um grupo de gerenciamento de Operations Manager precisam ser configurados para se conectar diretamente ao Azure Monitor. Para obter mais informações, consulte a visão geral em [coletar dados de log com o agente de log Analytics](../agents/log-analytics-agent.md).
* Uma entidade de serviço com acesso à assinatura do Azure que está associada ao espaço de trabalho do Log Analytics. Para obter mais informações, acesse [Criar uma entidade de serviço](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Instalar o pacote de gerenciamento do Mapa do Serviço

Você habilita a integração entre Operations Manager e o recurso de mapa importando o pacote de gerenciamento Microsoft.SystemCenter. ServiceMap (Microsoft.SystemCenter. ServiceMap. MPB). Você pode baixar o pacote de gerenciamento no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=55763). O pacote contém os seguintes pacotes de gerenciamento:

* Exibições de Aplicativo do Mapa do Serviço da Microsoft
* Mapa do Serviço Interno do Microsoft System Center
* Substituições do Mapa do Serviço do Microsoft System Center
* Mapa do Serviço do Microsoft System Center

## <a name="configure-integration"></a>Configurar a integração

Depois de instalar o pacote de gerenciamento do Mapa do Serviço, um novo nó, **mapa do serviço**, será exibido em **Operations Management Suite** no painel **Administração** do console de operações do Operations Manager.

>[!NOTE]
>[O Operations Management Suite foi uma coleção de serviços](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) que incluíam log Analytics, agora faz parte do [Azure monitor](../overview.md).

Para configurar a integração de mapa do Revisions do VM, faça o seguinte:

1. Para abrir o assistente de configuração, no painel **Visão Geral do Mapa do Serviço**, clique em **Adicionar workspace**.  

    ![Painel Visão Geral do Mapa do Serviço](media/service-map-scom/scom-configuration.png)

2. Na janela **Configuração da Conexão**, insira o nome ou a ID do locatário, a ID do aplicativo (também conhecida como o nome de usuário ou a clientID) e a senha da entidade de serviço e, em seguida, clique em **Avançar**. Para obter mais informações, acesse Criar uma entidade de serviço.

    ![A janela Configuração da Conexão](media/service-map-scom/scom-config-spn.png)

3. Na janela **Seleção de Assinatura**, selecione a assinatura do Azure, o grupo de recursos do Azure (aquele que contém o espaço de trabalho do Log Analytics) e o espaço de trabalho do Log Analytics e, em seguida, clique em **Avançar**.

    ![O workspace de configuração do Operations Manager](media/service-map-scom/scom-config-workspace.png)

4. No **seleção de grupo do computador** janela, que você escolha quais grupos de máquina do mapa de serviço que deseja sincronizar com o Operations Manager. Clique em **adicionar ou remover grupos de computadores**, escolha grupos na lista de **grupos de computadores disponíveis** e clique em **adicionar**.  Quando você terminar de selecionar os grupos, clique em **OK** para concluir.

    ![Os grupos de computadores de configuração do Operations Manager](media/service-map-scom/scom-config-machine-groups.png)

5. Na janela **seleção de servidor** , você configura o grupo de servidores mapa do serviço com os servidores que você deseja sincronizar entre Operations Manager e o recurso de mapa. Clique em **Adicionar/Remover Servidores**.

    Para que a integração crie um diagrama de aplicativo distribuído para um servidor, o servidor deve ser/estar:

   * Monitorado por Operations Manager
   * Configurado para relatar para o espaço de trabalho Log Analytics configurado com o VM insights
   * Listado no Grupo de Servidores do Mapa do Serviço

     ![O grupo de configuração do Operations Manager](media/service-map-scom/scom-config-group.png)

6. Opcional: selecione o pool de recursos de todos os servidores de gerenciamento para se comunicar com Log Analytics e, em seguida, clique em **adicionar espaço de trabalho**.

    ![Captura da tela do pool de servidores em adicionar Microsoft Operations Management Suite espaço de trabalho com o pool de recursos de todos os servidores de gerenciamento selecionado.](media/service-map-scom/scom-config-pool.png)

    Pode levar alguns minutos para configurar e registrar o espaço de trabalho do Log Analytics. Depois de configurado, Operations Manager inicia a primeira sincronização do mapa.

    ![Captura da tela de conclusão em adicionar Microsoft Operations Management Suite espaço de trabalho confirmando que o espaço de trabalho foi adicionado.](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Monitorar a integração

Depois que o espaço de trabalho do Log Analytics estiver conectado, uma nova pasta, Mapa do Serviço, será exibida no painel **monitoramento** do console de operações do Operations Manager.

![O painel Monitoramento do Operations Manager](media/service-map-scom/scom-monitoring.png)

A pasta do Mapa do Serviço tem quatro nós:

* **Alertas ativos**: lista todos os alertas ativos sobre a comunicação entre Operations Manager e Azure monitor.  

  >[!NOTE]
  >Esses alertas não são Log Analytics alertas sincronizados com o Operations Manager, eles são gerados no grupo de gerenciamento com base em fluxos de trabalho definidos no pacote de gerenciamento do Mapa do Serviço.

* **Servidores**: lista os servidores monitorados que estão configurados para sincronização a partir do recurso de mapa de informações de VM.

    ![O painel Monitorando Servidores do Operations Manager](media/service-map-scom/scom-monitoring-servers.png)

* **Exibições de dependência do grupo de computadores**: lista todos os grupos de computadores que são sincronizados a partir do recurso de mapa. É possível clicar em qualquer grupo para exibir seu diagrama de aplicativo distribuído.

    ![Captura de tela de Mapa do Serviço mostrando um diagrama com imagens para cada grupo de computadores e linhas que indicam as dependências entre eles.](media/service-map-scom/scom-group-dad.png)

* **Exibições de dependência do servidor**: lista todos os servidores que são sincronizados a partir do recurso de mapa. É possível clicar em qualquer servidor para exibir seu diagrama de aplicativo distribuído.

    ![Captura de tela de Mapa do Serviço mostrando um diagrama com imagens para cada servidor e as linhas que indicam as dependências entre elas.](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Editar ou excluir o workspace

É possível editar ou excluir o workspace configurado por meio do painel **Visão Geral do Mapa do Serviço** (painel **Administração** &gt; **Operations Management Suite** > **Mapa do Serviço**).

> [!NOTE]
> [Operations Management Suite foi uma coleção de serviços](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) que incluiu o Log Analytics, que agora faz parte do [Azure Monitor](../overview.md).

Você pode configurar apenas um espaço de trabalho Log Analytics nesta versão atual.

![O painel Editar Workspace do Operations Manager](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Configurar regras e substituições

Uma regra, *Microsoft.SystemCenter. ServiceMapImport. Rule*, busca periodicamente informações do recurso de mapa da VM insights. Para modificar o intervalo de sincronização, você pode substituir a regra e modificar o valor do parâmetro **IntervalMinutes**.

![A janela de propriedades Substituições do Operations Manager](media/service-map-scom/scom-overrides.png)

* **Enabled**: habilite ou desabilite atualizações automáticas.
* **IntervalMinutes**: especifica o tempo entre as atualizações. O intervalo padrão é uma hora. Se você quiser sincronizar mapas com mais frequência, poderá alterar o valor.
* **TimeoutSeconds**: especifica o período de tempo antes que a solicitação expire.
* **TimeWindowMinutes**: especifica a janela de tempo para consultar dados. O padrão é 60 minutos, que é o intervalo máximo permitido.

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

O design atual apresenta os seguintes problemas e limitações:

* Você só pode se conectar a um único espaço de trabalho do Log Analytics.
* Embora você possa adicionar servidores ao Grupo de Servidores do Mapa do Serviço manualmente por meio do painel **Criação**, os mapas desses servidores não são sincronizados imediatamente. Eles serão sincronizados do recurso de mapa do virtual insights durante o próximo ciclo de sincronização.
* Se você fizer alterações nos diagramas de aplicativo distribuído criados pelo pacote de gerenciamento, essas alterações provavelmente serão substituídas na próxima sincronização com o VM insights.

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Para obter a documentação oficial do Azure sobre como criar uma entidade de serviço, consulte:

* [Criar uma entidade de serviço usando o PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Criar uma entidade de serviço usando a CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli)
* [Criar uma entidade de serviço usando o portal do Azure](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="suggestions"></a>Sugestões

Você tem algum comentário sobre a integração com o recurso de mapa de informações de VM ou esta documentação? Visite nossa [página de voz do usuário](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), onde você pode sugerir recursos ou votar em sugestões existentes.

