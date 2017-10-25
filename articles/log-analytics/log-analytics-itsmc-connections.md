---
title: "Conexões com suporte do Conector de Gerenciamento de Serviço de TI no Azure Log Analytics | Microsoft Docs"
description: "Conecte seus produtos/serviços de ITSM com o Conector de Gerenciamento de Serviço de TI no Azure Log Analytics para monitorar e gerenciar os itens de trabalho ITSM de forma centralizada."
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 8231b7ce-d67f-4237-afbf-465e2e397105
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: v-jysur
ms.openlocfilehash: bbec5773987b29eb62d10d17b88efcda29889612
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector-preview"></a>Conectar produtos/serviços ITSM ao Conector de Gerenciamento do Serviço de TI (Versão Prévia)
Este artigo fornece informações sobre como conectar seu produto ou serviço ITSM conector de gerenciamento de serviço de TI na OMS e gerenciar centralmente os seus itens de trabalho. Para saber mais sobre o Conector de Gerenciamento do Serviço de TI, consulte [Visão geral](log-analytics-itsmc-overview.md).

Há suporte para os seguintes produtos/serviços:

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-oms)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-oms)
- [Provance](#connect-provance-to-it-service-management-connector-in-oms)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-oms"></a>Conectar o System Center Service Manager ao Conector de Gerenciamento do Serviço de TI no OMS

As seções a seguir fornecem detalhes sobre como conectar seu produto System Center Service Manager ao Conector de Gerenciamento do Serviço de TI no OMS.

### <a name="prerequisites"></a>Pré-requisitos

Verifique se você cumpriu os seguintes pré-requisitos:

- Conector de Gerenciamento do Serviço de TI instalado.
Mais informações: [Adicionando a solução Conector de Gerenciamento de Serviço de TI](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- O aplicativo Web do Service Manager (aplicativo da Web) é implantado e configurado. Veja informações sobre o aplicativo Web [aqui](#create-and-deploy-service-manager-web-app-service).
- Conexão híbrida criada e configurada. Mais informações: [Configurar a Conexão híbrida](#configure-the-hybrid-connection).
- Versões do Service Manager com suporte: 2012 R2 ou 2016.
- Função de usuário: [Operador avançado](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Procedimento de conexão

Use o procedimento a seguir para conectar a instância do System Center Service Manager ao Conector de Gerenciamento do Serviço de TI:

1. Vá para **OMS** >**Configurações** > **Origens Conectadas**.
2. Selecione **Conector ITSM,**  clique em **Adicionar Nova Conexão**.

    ![Service manager ](./media/log-analytics-itsmc/itsmc-service-manager-connection.png)
3. Forneça as informações conforme descrito na tabela a seguir e clique em **Salvar** para criar a conexão:

> [!NOTE]
> Todos esses parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome**   | Digite um nome para a instância do System Center Service Manager que você deseja se conectar com o Conector de Gerenciamento do Serviço de TI.  Use esse nome posteriormente quando você configurar os itens de trabalho nesta instância / exibir a análise de log detalhado. |
| **Selecionar Tipo de conexão**   | Selecione **System Center Service Manager**. |
| **URL do Servidor**   | Digite a URL do aplicativo Web do Service Manager. Veja mais sobre o aplicativo Web do Service Manager [aqui](#create-and-deploy-service-manager-web-app-service).
| **ID do Cliente**   | Digite a ID de cliente gerado (usando o script automática) para autenticar o aplicativo Web. Veja mais informações sobre o script automatizado [aqui.](log-analytics-itsmc-service-manager-script.md)|
| **Segredo do Cliente**   | Digite o segredo do cliente gerado para essa ID.   |
| **Escopo de Sincronização de Dados**   | Selecione os itens de trabalho do Service Manager que você deseja sincronizar por meio do Conector de Gerenciamento do Serviço de TI.  Esses itens de trabalho são importados para o Log Analytics. **Opções:** Incidentes, Solicitações de Alteração.|
| **Sincronizar Dados** | Digite o número de dias anteriores dos quais você deseja dados. **Limite máximo** 120 dias. |
| **Criar um novo item de configuração na solução ITSM** | Selecione esta opção se você deseja criar os itens de configuração no produto ITSM. Quando selecionado, o OMS cria os CIs afetados como itens de configuração (no caso de CIs inexistentes) no sistema de ITSM com suporte. **Padrão**: desabilitado. |

Quando conectado e sincronizado com êxito:

- Itens de trabalho selecionados do Service Manager são importados para OMS **Log Analytics.** Você pode exibir o resumo desses itens de trabalho no bloco do conector de Gerenciamento do Serviço de TI.

- Do OMS, você pode criar incidentes de alertas do OMS ou de pesquisa de log nesta instância do Service Manager.

Mais informações: [Criar itens de trabalho ITSM para alertas do OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts) e [Criar itens de trabalho ITSM de logs do OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Criar e implantar o serviço de aplicativo Web do Service Manager

Para conectar o Service Manager local com o Conector de Gerenciamento do Serviço de TI no OMS, a Microsoft criou um aplicativo Web do Service Manager no GitHub.

Para configurar o aplicativo Web ITSM para o Service Manager, faça o seguinte:

- **Implantar o aplicativo Web** – implantar o aplicativo da Web, defina as propriedades e autenticar com o AD do Azure. Você pode implantar o aplicativo web usando o [automatizada script](log-analytics-itsmc-service-manager-script.md) que a Microsoft forneceu a você.
- **Configurar a conexão híbrida** - [configurar essa conexão](#configure-the-hybrid-connection), manualmente.

#### <a name="deploy-the-web-app"></a>Implantar o aplicativo Web
Use o automated [script](log-analytics-itsmc-service-manager-script.md) para implantar o aplicativo Web, defina as propriedades e autenticar com o AD do Azure.

Execute o script fornecendo os seguintes detalhes necessários:

- Detalhes da assinatura do Azure
- Nome do grupo de recursos
- Local
- Detalhes do servidor do Service Manager (nome do servidor, domínio, nome de usuário e senha)
- Prefixo de nome do site para seu aplicativo Web
- Namespace do ServiceBus.

O script cria um aplicativo Web usando o nome especificado (junto com algumas cadeias de caracteres adicionais para torná-la exclusiva). Ele gera o **URL do aplicativo Web**, a **ID do cliente** e o **segredo do cliente**.

Salve os valores, você os usa ao criar uma conexão com o Conector de Gerenciamento do Serviço de TI.

**Verificar a instalação do aplicativo Web**

1. Vá para o **portal do Azure** > **Recursos**.
2. Selecione o aplicativo Web, clique em **Configurações** > **Configurações do Aplicativo**.
3. Confirme as informações sobre a instância do Gerenciador de serviço que você forneceu no momento da implantação do aplicativo por meio do script.

### <a name="configure-the-hybrid-connection"></a>Configurar a conexão híbrida

Use o procedimento a seguir para configurar a conexão híbrida que conecta a instância do Service Manager com o Conector de Gerenciamento do Serviço de TI no OMS.

1. Localize o aplicativo Web do Service Manager em **Recursos do Azure**.
2. Clique em **Configurações** > **Rede**.
3. Em **conexões híbridas**, clique em **configurar seus pontos de extremidade de conexão híbrida**.

    ![Rede de conexão híbrida](./media/log-analytics-itsmc/itsmc-hybrid-connection-networking-and-end-points.png)
4. Na folha **Conexões Híbridas**, clique em **Adicionar conexão híbrida**.

    ![Adição de conexão híbrida](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-add.png)

5. No **adicionar conexões híbridas** folha, clique em **criar novo híbrida Conexão**.

    ![Nova Conexão híbrida](./media/log-analytics-itsmc/itsmc-create-new-hybrid-connection.png)

6. Digite os seguintes valores:

    - **Nome do Ponto de Extremidade**: especifique um nome para a nova conexão híbrida.
    -  **Host do Ponto de Extremidade**: FQDN do servidor de gerenciamento do Service Manager.
    - **Porta do Ponto de Extremidade**: digite 5724
    - **Namespace ServiceBus**: usar um namespace de barramento de serviço existente ou crie um novo.
    - **Local**: selecione o local.
    -  **Nome**: especifique um nome para o barramento de serviço se você estiver criando a ele.

    ![Valores da conexão híbrida](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-values.png)
6. Clique em **Okey** para fechar o **criar conexão híbrida** folha e começar a criar a conexão híbrida.

    Depois que a conexão híbrida é criada, ela será exibida em folha.

7. Depois que a conexão híbrida é criada, selecione a conexão e clique em **adicionar selecionada conexão híbrida**.

    ![Nova conexão híbrida](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Configurar a instalação do ouvinte

Use o procedimento a seguir para definir a configuração de ouvinte para a conexão híbrida.

1. No **conexões híbridas** folha, clique em **o Download do Gerenciador de Conexão** e instalá-lo no computador em que a instância do System Center Service Manager está em execução.

    Quando a instalação for concluída, **Gerenciador de Conexão híbrida UI** opção está disponível em **iniciar** menu.

2. Clique em **Gerenciador de Conexão híbrida UI** , você será solicitado para suas credenciais do Azure.

3. Faça logon com suas credenciais do Azure e selecione sua assinatura em que a conexão híbrida foi criada.

4. Clique em **Salvar**.

Sua conexão híbrida foi conectada com êxito.

![conexão híbrida com êxito](./media/log-analytics-itsmc/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]

> Após a criação da conexão híbrida, verifique e teste a conexão ao visitar o aplicativo Web do Service Manager. Certifique-se de que a conexão foi bem-sucedida antes de tentar se conectar ao Conector de Gerenciamento do Serviço de TI no OMS.

A imagem a seguir mostra os detalhes de uma conexão bem-sucedida:

![Teste de conexão híbrida](./media/log-analytics-itsmc/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-oms"></a>Conectar o ServiceNow ao Conector de Gerenciamento do Serviço de TI no OMS

As seções a seguir fornecem detalhes sobre como conectar seu produto ServiceNow ao Conector de Gerenciamento do Serviço de TI no OMS.

### <a name="prerequisites"></a>Pré-requisitos

Verifique se você cumpriu os seguintes pré-requisitos:

- Conector de Gerenciamento do Serviço de TI instalado. Mais informações: [Adicionando o Conector de Gerenciamento de Serviço de TI](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Versões com suporte do ServiceNow – Fuji, Geneva, Helsinki.

Os administradores do ServiceNow devem fazer o seguinte em sua instância do ServiceNow:
- Gere a ID do cliente e o segredo do cliente para o produto ServiceNow. Para obter informações sobre como gerar o ID do cliente e o segredo, consulte [configuração OAuth](http://wiki.servicenow.com/index.php?title=OAuth_Setup).
- Instale o aplicativo de usuário para integração com o Microsoft OMS (aplicativo ServiceNow). [Saiba mais](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.0 ).
- Crie função de usuário de integração para o aplicativo de usuário instalado. Veja informações sobre como criar a função de usuário de integração [aqui](#create-integration-user-role-in-servicenow-app).


### <a name="connection-procedure"></a>**Procedimento de conexão**

Use o procedimento a seguir para criar uma conexão do ServiceNow:

1. Vá para **OMS** > **Configurações** > **Origens Conectadas**.
2. Selecione **Conector ITSM,**  clique em **Adicionar Nova Conexão**.

    ![Conexão do ServiceNow](./media/log-analytics-itsmc/itsmc-servicenow-connection.png)

3. Forneça as informações conforme descrito na tabela a seguir e clique em **Salvar** para criar a conexão:

> [!NOTE]
> Todos esses parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome**   | Digite um nome para a instância do ServiceNow que você deseja conectar com o Conector de Gerenciamento do Serviço de TI.  Use esse nome posteriormente no OMS quando você configurar os itens de trabalho nesse ITSM/exibir o Log Analytics detalhado. |
| **Selecionar Tipo de conexão**   | Selecione **ServiceNow**. |
| **Nome de Usuário**   | Digite o nome de usuário de integração que você criou no aplicativo ServiceNow para dar suporte à conexão com o Conector de Gerenciamento do Serviço de TI. Mais informações: [Criar função de usuário de aplicativo do ServiceNow](#create-integration-user-role-in-servicenow-app).|
| **Senha**   | Digite a senha associada a esse nome de usuário. **Observação**: nome de usuário e senha são usados para gerar tokens de autenticação apenas e não são armazenados em qualquer lugar no serviço OMS.  |
| **URL do Servidor**   | Digite a URL da instância do ServiceNow que você deseja conectar ao Conector de Gerenciamento do Serviço de TI. |
| **ID do Cliente**   | Digite a ID do cliente que você deseja usar para autenticação de OAuth2, que você gerou anteriormente.  Para saber mais sobre como gerar o ID do cliente e o segredo: [configuração OAuth](http://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Segredo do Cliente**   | Digite o segredo do cliente gerado para essa ID.   |
| **Escopo de Sincronização de Dados**   | Selecione os itens de trabalho do ServiceNow que você deseja sincronizar com o OMS, por meio do Conector de Gerenciamento do Serviço de TI.  Os valores selecionados são importados para a análise de log.   **Opções:** Incidentes e Solicitações de Alteração.|
| **Sincronizar Dados** | Digite o número de dias anteriores dos quais você deseja dados. **Limite máximo** 120 dias. |
| **Criar um novo item de configuração na solução ITSM** | Selecione esta opção se você deseja criar os itens de configuração no produto ITSM. Quando selecionado, o OMS cria os CIs afetados como itens de configuração (no caso de CIs inexistentes) no sistema de ITSM com suporte. **Padrão**: desabilitado. |


Quando conectado e sincronizado com êxito:

- Selecionar trabalho itens de conexão do ServiceNow são importados para o Log Analytics do OMS.  Você pode exibir o resumo desses itens de trabalho no bloco do conector de Gerenciamento do Serviço de TI.
- Você pode criar incidentes, alertas e eventos de pesquisa de log ou alertas de OMS nesta instância do ServiceNow.  


Mais informações: [Criar itens de trabalho ITSM para alertas do OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts) e [Criar itens de trabalho ITSM de logs do OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs).

### <a name="create-integration-user-role-in-servicenow-app"></a>Criar função de usuário de integração no aplicativo do ServiceNow

Use este procedimento:

1.  Visite o [ServiceNow repositório](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.0) e instale o **aplicativo de usuário para ServiceNow e integração de OMS do Microsoft** em sua instância do ServiceNow.
2.  Após a instalação, visite a barra de navegação à esquerda da instância do ServiceNow, pesquisa e selecione Microsoft OMS integrador.  
3.  Clique em **Lista de Verificação de Instalação**.

    O status é exibido como **concluir não** se a função de usuário ainda não foi criado.

4.  Nas caixas de texto, próximo a **Criar usuário de integração**, insira o nome de usuário para o usuário que pode se conectar ao Conector de Gerenciamento do Serviço de TI no OMS.
5.  Insira a senha para esse usuário e clique em **OK**.  

>[!NOTE]

> Você pode usar essas credenciais para fazer a conexão do ServiceNow em OMS.

O usuário recém-criado é exibido com as funções padrão atribuídas.

Funções padrão:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   itil
-   template_editor
-   view_changer

Depois que o usuário é criado com êxito, o status de **lista de verificação de instalação verificar** Move como concluída, listar os detalhes da função de usuário criado para o aplicativo.

> [!NOTE]

> Para permitir que um usuário crie **alertas** e **eventos** no ServiceNow do OMS:

> - Certifique-se de que você possui o módulo de gerenciamento de eventos instalado na instância do ServiceNow.

> - Adicione as seguintes funções para o usuário de integração:
>      - evt_mgmt_integration
>      - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-oms"></a>Conectar o Provance ao Conector de Gerenciamento do Serviço de TI no OMS

As seções a seguir fornecem detalhes sobre como conectar seu produto Provance ao Conector de Gerenciamento do Serviço de TI no OMS.

### <a name="prerequisites"></a>Pré-requisitos

Verifique se você cumpriu os seguintes pré-requisitos:


- Conector de Gerenciamento do Serviço de TI instalado. Mais informações: [Adicionando o Conector de Gerenciamento de Serviço de TI](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Provance aplicativo deve ser registrado com o Azure AD - ID do cliente é disponibilizada. Para obter informações detalhadas, consulte [como configurar a autenticação do active directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

- Função de usuário: Administrador.

### <a name="connection-procedure"></a>Procedimento de Conexão

Use o procedimento a seguir para criar uma conexão do Provance:

1. Vá para **OMS** > **Configurações** > **Origens Conectadas**.
2. Selecione **Conector ITSM,**  clique em **Adicionar Nova Conexão**.  

    ![Conexão do Provance](./media/log-analytics-itsmc/itsmc-provance-connection.png)
3. Forneça as informações conforme descrito na tabela a seguir e clique em **salvar** para criar a conexão.

> [!NOTE]
> Todos esses parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome**   | Digite um nome para a instância do Provance que você deseja conectar com o Conector de Gerenciamento do Serviço de TI.  Use esse nome posteriormente no OMS quando você configurar os itens de trabalho nesse ITSM/exibir o Log Analytics detalhado. |
| **Selecionar Tipo de conexão**   | Selecione **Provance**. |
| **Nome de Usuário**   | Digite o nome de usuário que pode se conectar ao Conector de Gerenciamento do Serviço de TI.    |
| **Senha**   | Digite a senha associada a esse nome de usuário. **Observação:** nome de usuário e senha são usados para gerar tokens de autenticação apenas e não são armazenados em qualquer lugar no serviço OMS. _|
| **URL do Servidor**   | Digite a URL da sua instância do Provance que você deseja conectar ao Conector de Gerenciamento do Serviço de TI. |
| **ID do Cliente**   | Digite a ID do cliente para autenticar esta conexão, que é gerado em sua instância de Provance.  Para saber mais sobre a ID do cliente, consulte [como configurar a autenticação do active directory](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md). |
| **Escopo de Sincronização de Dados**   | Selecione os itens de trabalho do Provance que você deseja sincronizar com o OMS, por meio do Conector de Gerenciamento do Serviço de TI.  Esses itens de trabalho são importados para o Log Analytics.   **Opções:** Incidentes, Solicitações de Alteração.|
| **Sincronizar Dados** | Digite o número de dias anteriores dos quais você deseja dados. **Limite máximo** 120 dias. |
| **Criar um novo item de configuração na solução ITSM** | Selecione esta opção se você deseja criar os itens de configuração no produto ITSM. Quando selecionado, o OMS cria os CIs afetados como itens de configuração (no caso de CIs inexistentes) no sistema de ITSM com suporte. **Padrão**: desabilitado.|

Quando conectado e sincronizado com êxito:

- Itens de trabalho selecionados da conexão Provance são importados para OMS **Log Analytics.**  Você pode exibir o resumo desses itens de trabalho no bloco do conector de Gerenciamento do Serviço de TI.
- Você pode criar incidentes e eventos de alertas do OMS ou pesquisa de Log nesta instância Provance.

Mais informações: [Criar itens de trabalho ITSM para alertas do OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts) e [Criar itens de trabalho ITSM de logs do OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs).

## <a name="connect-cherwell-to-it-service-management-connector-in-oms"></a>Conectar o Cherwell ao Conector de Gerenciamento do Serviço de TI no OMS

As seções a seguir fornecem detalhes sobre como conectar seu produto Cherwell ao Conector de Gerenciamento do Serviço de TI no OMS.

### <a name="prerequisites"></a>Pré-requisitos

Verifique se você cumpriu os seguintes pré-requisitos:

- Conector de Gerenciamento do Serviço de TI instalado. Mais informações: [Adicionando o Conector de Gerenciamento de Serviço de TI](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- ID do Cliente gerada. Mais informações: [Gerar a ID de cliente do Cherwell](#generate-client-id-for-cherwell).
- Função de usuário: Administrador.

### <a name="connection-procedure"></a>Procedimento de Conexão

Use o procedimento a seguir para criar uma conexão do Cherwell:

1. Vá para **OMS** >  **Configurações** > **Origens Conectadas**.
2. Selecione **Conector ITSM,**  clique em **Adicionar Nova Conexão**.  

    ![Id de usuário do Cherwell](./media/log-analytics-itsmc/itsmc-cherwell-connection.png)

3. Forneça as informações conforme descrito na tabela a seguir e clique em **salvar** para criar a conexão.

> [!NOTE]
> Todos esses parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome**   | Digite um nome para a instância do Cherwell que você deseja conectar ao Conector de Gerenciamento do Serviço de TI.  Use esse nome posteriormente no OMS quando você configurar os itens de trabalho nesse ITSM/exibir o Log Analytics detalhado. |
| **Selecionar Tipo de conexão**   | Selecione **Cherwell.** |
| **Nome de Usuário**   | Digite o nome de usuário do Cherwell que pode se conectar ao Conector de Gerenciamento do Serviço de TI. |
| **Senha**   | Digite a senha associada a esse nome de usuário. **Observação:** nome de usuário e senha são usados para gerar tokens de autenticação apenas e não são armazenados em qualquer lugar no serviço OMS.|
| **URL do Servidor**   | Digite a URL da sua instância do Cherwell que você deseja conectar ao Conector de Gerenciamento do Serviço de TI. |
| **ID do Cliente**   | Digite a ID do cliente para autenticar esta conexão, que é gerado em sua instância do Cherwell.   |
| **Escopo de Sincronização de Dados**   | Selecione os itens de trabalho do Cherwell que deseja sincronizar por meio do Conector de Gerenciamento do Serviço de TI.  Esses itens de trabalho são importados para o Log Analytics.   **Opções:** Incidentes, Solicitações de Alteração. |
| **Sincronizar Dados** | Digite o número de dias anteriores dos quais você deseja dados. **Limite máximo** 120 dias. |
| **Criar um novo item de configuração na solução ITSM** | Selecione esta opção se você deseja criar os itens de configuração no produto ITSM. Quando selecionado, o OMS cria os CIs afetados como itens de configuração (no caso de CIs inexistentes) no sistema de ITSM com suporte. **Padrão**: desabilitado. |

Quando conectado e sincronizado com êxito:

- Selecionar trabalho itens dessa conexão Cherwell são importados para o Log Analytics do OMS. Você pode exibir o resumo desses itens de trabalho no bloco do Conector de Gerenciamento do Serviço de TI.
- Você pode criar incidentes e eventos nesta instância do Cherwell do OMS. Mais informações: criar itens de trabalho ITSM para alertas do OMS e ITSM criar itens de trabalho de logs do OMS.

Mais informações: [Criar itens de trabalho ITSM para alertas do OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts) e [Criar itens de trabalho ITSM de logs do OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs).

### <a name="generate-client-id-for-cherwell"></a>Gerar a ID de Cliente do Cherwell

Para gerar o chave/ID do cliente para Cherwell, use o procedimento a seguir:

1. Fazer logon em sua instância de Cherwell como administrador.
2. Clique em **Segurança** > **Editar configurações de cliente de API REST**.
3. Selecione **Criar novo cliente** > **segredo do cliente**.

    ![Id de usuário do Cherwell](./media/log-analytics-itsmc/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Próximas etapas
 - [Criar itens de trabalho de ITSM para alertas do OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts)

 - [Criar itens de trabalho de ITSM com base em logs do OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs)

- [Exibir a análise de log para sua conexão](log-analytics-itsmc-overview.md#using-the-solution)
