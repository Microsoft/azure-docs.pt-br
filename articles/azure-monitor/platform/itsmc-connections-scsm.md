---
title: Conectar o SCSM com o Conector de Gerenciamento de Serviços de TI
description: Este artigo fornece informações sobre como SCSM com o Conector de Gerenciamento de Serviços de TI (ITSMC) no Azure Monitor para monitorar e gerenciar de forma centralizada os itens de trabalho de ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 907a78b15cca4718308f79bc6be2e6258bc04d19
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492664"
---
# <a name="connect-system-center-service-manager-with-it-service-management-connector"></a>Conectar System Center Service Manager com Conector de Gerenciamento de Serviços de TI

Este artigo fornece informações sobre como configurar a conexão entre sua instância do System Center Service Manager e o Conector de Gerenciamento de Serviços de TI (ITSMC) no Log Analytics para gerenciar centralmente seus itens de trabalho.

As seções a seguir fornecem detalhes sobre como conectar seu produto System Center Service Manager ao ITSMC no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Verifique se os seguintes pré-requisitos foram atendidos:

- ITSMC instalado. Mais informações: [Adicionar a solução Conector de Gerenciamento de Serviços de TI](./itsmc-definition.md).
- O aplicativo Web do Service Manager (aplicativo da Web) é implantado e configurado. Veja informações sobre o aplicativo Web [aqui](#create-and-deploy-service-manager-web-app-service).
- Conexão híbrida criada e configurada. Mais informações: [Configurar a conexão híbrida](#configure-the-hybrid-connection).
- Versões do Service Manager com suporte:  2012 R2 ou 2016.
- Função de usuário:  [Operador avançado](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10)).
- Hoje, os alertas enviados do Azure Monitor podem ser criados em incidentes System Center Service Manager.

> [!NOTE]
> - O Conector ITSM pode se conectar apenas a instâncias do ServiceNow baseadas em nuvem. No momento, não há suporte para instâncias locais do ServiceNow.
> - Para usar [modelos](./itsmc-definition.md#define-a-template) personalizados como parte das ações, o parâmetro "geometertype" no modelo SCSM deve ser mapeado para "IncidentManagement! System. WorkItem. Incident. Projetype "

## <a name="connection-procedure"></a>Procedimento de conexão

Use o procedimento a seguir para conectar a instância do System Center Service Manager ao ITSMC:

1. No portal do Azure, acesse **Todos os Recursos** e procure **ServiceDesk(YourWorkspaceName)**

2. Em **FONTES DE DADOS DO WORKSPACE**, clique em **Conexões de ITSM**.

    ![Nova conexão](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações, conforme descrito na tabela a seguir, e clique em **OK** para criar a conexão.

> [!NOTE]
> Todos esses parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome da conexão**   | Digite um nome para a instância do System Center Service Manager que você deseja conectar ao ITSMC.  Use esse nome posteriormente quando você configurar os itens de trabalho nesta instância / exibir a análise de logs detalhado. |
| **Tipo de parceiro**   | Selecione **System Center Service Manager**. |
| **Servidor URL**   | Digite a URL do aplicativo Web do Service Manager. Veja mais sobre o aplicativo Web do Service Manager [aqui](#create-and-deploy-service-manager-web-app-service).
| **ID do Cliente**   | Digite a ID de cliente gerado (usando o script automática) para autenticar o aplicativo Web. Veja mais informações sobre o script automatizado [aqui.](./itsmc-service-manager-script.md)|
| **Segredo do Cliente**   | Digite o segredo do cliente gerado para essa ID.   |
| **Sincronizar Dados**   | Selecione os itens de trabalho do Service Manager que você deseja sincronizar por meio do ITSMC.  Esses itens de trabalho são importados para o Log Analytics. **Opções:**  Incidentes, Solicitações de Alteração.|
| **Escopo de Sincronização de Dados** | Digite o número de dias anteriores dos quais você deseja dados. **Limite máximo**: 120 dias. |
| **Criar um novo item de configuração na solução ITSM** | Selecione esta opção se você deseja criar os itens de configuração no produto ITSM. Quando selecionado, o Log Analytics cria os CIs afetados como itens de configuração (no caso de CIs inexistentes) no sistema de ITSM com suporte. **Padrão**: desabilitado. |

![Conexão do Service Manager](media/itsmc-connections/service-manager-connection.png)

**Quando conectado e sincronizado com êxito**:

- Os itens de trabalho selecionados no Service Manager são importados para o Azure **Log Analytics.** Você pode exibir o resumo desses itens de trabalho no bloco do conector de Gerenciamento do Serviço de TI.

- Crie incidentes com base em alertas ou registros de log do Log Analytics ou em alertas do Azure nesta instância do Service Manager.

Saiba mais: [Criar itens de trabalho de ITSM a partir de alertas do Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="create-and-deploy-service-manager-web-app-service"></a>Criar e implantar o serviço de aplicativo Web do Service Manager

Para conectar o Service Manager local ao ITSMC no Azure, a Microsoft criou um aplicativo Web do Service Manager no GitHub.

Para configurar o aplicativo Web ITSM para o Service Manager, faça o seguinte:

- **Implantar o aplicativo Web** – implantar o aplicativo da Web, defina as propriedades e autenticar com o AD do Azure. Você pode implantar o aplicativo web usando o [automatizada script](./itsmc-service-manager-script.md) que a Microsoft forneceu a você.
- **Configurar a conexão híbrida** - [configurar essa conexão](#configure-the-hybrid-connection), manualmente.

### <a name="deploy-the-web-app"></a>Implantar o aplicativo Web
Use o automated [script](./itsmc-service-manager-script.md) para implantar o aplicativo Web, defina as propriedades e autenticar com o AD do Azure.

Execute o script fornecendo os seguintes detalhes necessários:

- Detalhes da assinatura do Azure
- Nome do grupo de recursos
- Location
- Detalhes do servidor do Service Manager (nome do servidor, domínio, nome de usuário e senha)
- Prefixo de nome do site para seu aplicativo Web
- Namespace do ServiceBus.

O script cria um aplicativo Web usando o nome especificado (junto com algumas cadeias de caracteres adicionais para torná-la exclusiva). Ele gera o **URL do aplicativo Web**, a **ID do cliente** e o **segredo do cliente**.

Salve os valores, você os usará quando criar uma conexão com o ITSMC.

**Verificar a instalação do aplicativo Web**

1. Vá para o **portal do Azure** > **Recursos**.
2. Selecione o aplicativo Web, clique em **Configurações** > **Configurações do Aplicativo**.
3. Confirme as informações sobre a instância do Gerenciador de serviço que você forneceu no momento da implantação do aplicativo por meio do script.

## <a name="configure-the-hybrid-connection"></a>Configurar a conexão híbrida

Use o procedimento a seguir para configurar a conexão híbrida que conecta a instância do Service Manager ao ITSMC no Azure.

1. Localize o aplicativo Web do Service Manager em **Recursos do Azure**.
2. Clique em **Configurações** > **Rede**.
3. Em **conexões híbridas**, clique em **configurar seus pontos de extremidade de conexão híbrida**.

    ![Rede de conexão híbrida](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. Na folha **Conexões Híbridas**, clique em **Adicionar conexão híbrida**.

    ![Adição de conexão híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. No **adicionar conexões híbridas** folha, clique em **criar novo híbrida Conexão**.

    ![Nova Conexão híbrida](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Digite os seguintes valores:

   - **Nome do Ponto de Extremidade**: Especifique um nome para a nova conexão híbrida.
   - **Host do Ponto de Extremidade**: FQDN do servidor de gerenciamento do Service Manager.
   - **Porta do Ponto de Extremidade**: Digite 5724
   - **Namespace de Servicebus**: Use um namespace de servicebus existente ou crie um novo.
   - **Local**: selecione o local.
   - **Name**: Especifique um nome para o servicebus se você estiver criando ele.

     ![Valores da conexão híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Clique em **Okey** para fechar o **criar conexão híbrida** folha e começar a criar a conexão híbrida.

    Depois que a conexão híbrida é criada, ela será exibida em folha.

7. Depois que a conexão híbrida é criada, selecione a conexão e clique em **adicionar selecionada conexão híbrida**.

    ![Nova conexão híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

### <a name="configure-the-listener-setup"></a>Configurar a instalação do ouvinte

Use o procedimento a seguir para definir a configuração de ouvinte para a conexão híbrida.

1. No **conexões híbridas** folha, clique em **o Download do Gerenciador de Conexão** e instalá-lo no computador em que a instância do System Center Service Manager está em execução.

    Quando a instalação for concluída, **Gerenciador de Conexão híbrida UI** opção está disponível em **iniciar** menu.

2. Clique em **Gerenciador de Conexão híbrida UI** , você será solicitado para suas credenciais do Azure.

3. Faça logon com suas credenciais do Azure e selecione sua assinatura em que a conexão híbrida foi criada.

4. Clique em **Save** (Salvar).

Sua conexão híbrida foi conectada com êxito.

![conexão híbrida com êxito](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> Após a criação da conexão híbrida, verifique e teste a conexão ao visitar o aplicativo Web do Service Manager. Verifique se a conexão foi bem-sucedida antes de tentar se conectar ao ITSMC no Azure.

O exemplo a seguir mostra os detalhes de uma conexão bem-sucedida:

![Teste de conexão híbrida](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do Conector ITSM](itsmc-overview.md)
* [Criar itens de trabalho de ITSM desde alertas do Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Solução de problemas no Conector ITSM](./itsmc-resync-servicenow.md)