---
title: Integração do Azure Monitor para VMs com o System Center Operations Manager | Microsoft Docs
description: Azure Monitor para VMs descobre automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Este artigo aborda o uso do recurso de mapa para criar automaticamente diagramas de aplicativos distribuídos no Operations Manager.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: b16505eb2c12819532b8675472cf0e6f4177f7bf
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489715"
---
# <a name="system-center-operations-manager-integration-with-azure-monitor-for-vms-map-feature"></a>Integração do System Center Operations Manager com o recurso de mapa do Azure Monitor para VMs

No Azure Monitor para VMs, você pode exibir componentes de aplicativos descobertos em VMs (máquinas virtuais) do Windows e Linux que são executadas no Azure ou no seu ambiente. Com essa integração entre o recurso de mapa e System Center Operations Manager, você pode criar automaticamente diagramas de aplicativo distribuídos em Operations Manager que se baseiam nos mapas de dependência dinâmica no Azure Monitor para VMs. 

>[!NOTE]
>Se você já tiver implantado Mapa do Serviço, poderá exibir seus mapas no Azure Monitor para VMs, que inclui recursos adicionais para monitorar a integridade e o desempenho da VM. O recurso de mapa do Azure Monitor para VMs destina-se a substituir a solução de Mapa do Serviço autônoma. Para saber mais, consulte [visão geral do Azure Monitor para VMs](vminsights-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um grupo de gerenciamento de System Center Operations Manager (2012 R2 ou posterior).
* Um espaço de trabalho Log Analytics configurado para dar suporte a Azure Monitor para VMs.
* Uma ou mais máquinas virtuais do Windows e Linux ou computadores físicos que são monitorados pelo Operations Manager e enviando dados para seu espaço de trabalho do Log Analytics. Os servidores Linux que se reportam a um grupo de gerenciamento de Operations Manager precisam ser configurados para se conectar diretamente ao Azure Monitor. Para obter mais informações, consulte a visão geral em [coletar dados de log com o agente de log Analytics](../platform/log-analytics-agent.md).
* Uma entidade de serviço com acesso à assinatura do Azure que está associada ao espaço de trabalho do Log Analytics. Para obter mais informações, acesse [Criar uma entidade de serviço](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Instalar o pacote de gerenciamento do Mapa do Serviço

Você habilita a integração entre Operations Manager e o recurso de mapa importando o pacote de gerenciamento Microsoft. SystemCenter. ServiceMap (Microsoft. SystemCenter. ServiceMap. MPB). Você pode baixar o pacote de gerenciamento no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=55763). O pacote contém os seguintes pacotes de gerenciamento:

* Exibições de Aplicativo do Mapa do Serviço da Microsoft
* Mapa do Serviço Interno do Microsoft System Center
* Substituições do Mapa do Serviço do Microsoft System Center
* Mapa do Serviço do Microsoft System Center

## <a name="configure-integration"></a>Configurar a integração

Depois de instalar o pacote de gerenciamento do Mapa do Serviço, um novo nó, **mapa do serviço**, será exibido em **Operations Management Suite** no painel **Administração** do console de operações do Operations Manager.

>[!NOTE]
>[O Operations Management Suite foi uma coleção de serviços](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) que incluíam log Analytics, agora faz parte do [Azure monitor](../overview.md).

Para configurar a integração do mapa do Azure Monitor para VMs, faça o seguinte:

1. Para abrir o assistente de configuração, no painel **Visão Geral do Mapa do Serviço**, clique em **Adicionar workspace**.  

    ![Painel Visão Geral do Mapa do Serviço](media/service-map-scom/scom-configuration.png)

2. Na janela **Configuração da Conexão**, insira o nome ou a ID do locatário, a ID do aplicativo (também conhecida como o nome de usuário ou a clientID) e a senha da entidade de serviço e, em seguida, clique em **Avançar**. Para obter mais informações, acesse Criar uma entidade de serviço.

    ![A janela Configuração da Conexão](media/service-map-scom/scom-config-spn.png)

3. Na janela **Seleção de Assinatura**, selecione a assinatura do Azure, o grupo de recursos do Azure (aquele que contém o espaço de trabalho do Log Analytics) e o espaço de trabalho do Log Analytics e, em seguida, clique em **Avançar**.

    ![O workspace de configuração do Operations Manager](media/service-map-scom/scom-config-workspace.png)

4. No **seleção de grupo do computador** janela, que você escolha quais grupos de máquina do mapa de serviço que deseja sincronizar com o Operations Manager. Clique em **adicionar ou remover grupos de computadores**, escolha grupos na lista de **grupos de computadores disponíveis**e clique em **adicionar**.  Quando você terminar de selecionar os grupos, clique em **OK** para concluir.

    ![Os grupos de computadores de configuração do Operations Manager](media/service-map-scom/scom-config-machine-groups.png)

5. Na janela **seleção de servidor** , você configura o grupo de servidores mapa do serviço com os servidores que você deseja sincronizar entre Operations Manager e o recurso de mapa. Clique em **Adicionar/Remover Servidores**.

    Para que a integração crie um diagrama de aplicativo distribuído para um servidor, o servidor deve ser/estar:

   * Monitorado por Operations Manager
   * Configurado para relatar para o espaço de trabalho Log Analytics configurado com Azure Monitor para VMs
   * Listado no Grupo de Servidores do Mapa do Serviço

     ![O grupo de configuração do Operations Manager](media/service-map-scom/scom-config-group.png)

6. Opcional: Selecione o pool de recursos de todos os servidores de gerenciamento para se comunicar com Log Analytics e, em seguida, clique em **adicionar espaço de trabalho**.

    ![O pool de recursos de configuração do Operations Manager](media/service-map-scom/scom-config-pool.png)

    Pode levar alguns minutos para configurar e registrar o espaço de trabalho do Log Analytics. Depois de configurado, Operations Manager inicia a primeira sincronização do mapa.

    ![O pool de recursos de configuração do Operations Manager](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Monitorar a integração

Depois que o espaço de trabalho do Log Analytics estiver conectado, uma nova pasta, Mapa do Serviço, será exibida no painel **monitoramento** do console de operações do Operations Manager.

![O painel Monitoramento do Operations Manager](media/service-map-scom/scom-monitoring.png)

A pasta do Mapa do Serviço tem quatro nós:

* **Alertas Ativos**: Lista todos os alertas ativos sobre a comunicação entre Operations Manager e Azure Monitor.  

  >[!NOTE]
  >Esses alertas não são Log Analytics alertas sincronizados com o Operations Manager, eles são gerados no grupo de gerenciamento com base em fluxos de trabalho definidos no pacote de gerenciamento do Mapa do Serviço.

* **Servidores**: Lista os servidores monitorados que estão configurados para sincronização de Azure Monitor para VMs recurso de mapa.

    ![O painel Monitorando Servidores do Operations Manager](media/service-map-scom/scom-monitoring-servers.png)

* **Exibições de Dependência do Grupo de Computadores**: Lista todos os grupos de computadores que são sincronizados a partir do recurso de mapa. É possível clicar em qualquer grupo para exibir seu diagrama de aplicativo distribuído.

    ![O diagrama de aplicativo distribuído do Operations Manager](media/service-map-scom/scom-group-dad.png)

* **Exibições de Dependências do Servidor**: Lista todos os servidores que são sincronizados a partir do recurso de mapa. É possível clicar em qualquer servidor para exibir seu diagrama de aplicativo distribuído.

    ![O diagrama de aplicativo distribuído do Operations Manager](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Editar ou excluir o workspace

É possível editar ou excluir o workspace configurado por meio do painel **Visão Geral do Mapa do Serviço** (painel **Administração** &gt; **Operations Management Suite** > **Mapa do Serviço**).

>[!NOTE]
>[Operations Management Suite foi uma coleção de serviços](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) que incluiu o Log Analytics, que agora faz parte do [Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

Você pode configurar apenas um espaço de trabalho Log Analytics nesta versão atual.

![O painel Editar Workspace do Operations Manager](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Configurar regras e substituições

Uma regra, *Microsoft. SystemCenter. ServiceMapImport. Rule*, busca periodicamente informações de Azure monitor para VMs recurso de mapa. Para modificar o intervalo de sincronização, você pode substituir a regra e modificar o valor do parâmetro **IntervalMinutes**.

![A janela de propriedades Substituições do Operations Manager](media/service-map-scom/scom-overrides.png)

* **Enabled**: Habilite ou desabilite as atualizações automáticas.
* **IntervalMinutes**: Especifica o tempo entre as atualizações. O intervalo padrão é uma hora. Se você quiser sincronizar mapas com mais frequência, poderá alterar o valor.
* **TimeoutSeconds**: Especifica o período de tempo antes que a solicitação expire.
* **TimeWindowMinutes**: Especifica a janela de tempo para consultar dados. O padrão é 60 minutos, que é o intervalo máximo permitido.

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos

O design atual apresenta os seguintes problemas e limitações:

* Você só pode se conectar a um único espaço de trabalho do Log Analytics.
* Embora você possa adicionar servidores ao Grupo de Servidores do Mapa do Serviço manualmente por meio do painel **Criação**, os mapas desses servidores não são sincronizados imediatamente. Eles serão sincronizados de Azure Monitor para VMs recurso de mapa durante o próximo ciclo de sincronização.
* Se você fizer alterações nos diagramas de aplicativo distribuído criados pelo pacote de gerenciamento, essas alterações provavelmente serão substituídas na próxima sincronização com Azure Monitor para VMs.

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Para obter a documentação oficial do Azure sobre como criar uma entidade de serviço, consulte:

* [Criar uma entidade de serviço usando o PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Criar uma entidade de serviço usando a CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Criar uma entidade de serviço usando o portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Comentários
Você tem algum comentário para nós sobre a integração com Azure Monitor para VMs recurso de mapa ou esta documentação? Visite nossa [página do User Voice](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), na qual você pode sugerir recursos ou votar em sugestões existentes.
