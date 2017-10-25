---
title: "Acessar fontes de dados locais para Aplicativo Lógico do Azure | Microsoft Docs"
description: "Configurar o gateway de dados local para poder acessar fontes de dados locais de aplicativos lógicos"
keywords: "acessar dados, local, transferência de dados, criptografia, fontes de dados"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: f385d832deed2eaf8ea21eb75d62944cbbf3d13d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-data-sources-on-premises-from-logic-apps-with-on-premises-data-gateway"></a>Conectar-se a fontes de dados locais de aplicativos lógicos com o gateway de dados local

Para acessar fontes de dados locais de seus aplicativos lógicos, configure um gateway de dados local que aplicativos lógicos possam usar com conectores com suporte. O gateway atua como uma ponte que fornece rápida transferência de dados e criptografia entre fontes de dados locais e seus aplicativos lógicos. O gateway retransmite dados de fontes locais em canais criptografados por meio do Barramento de Serviço do Azure. Todo o tráfego é originado como tráfego de saída seguro do agente de gateway. Saiba mais sobre [como o gateway de dados funciona](logic-apps-gateway-install.md#gateway-cloud-service). 

O gateway dá suporte a conexões com estas fontes de dados locais:

*   BizTalk Server 2016
*   DB2  
*   Sistema de Arquivos
*   Informix
*   MQ
*   MySQL
*   Banco de dados Oracle
*   PostgreSQL
*   Servidor de aplicativos SAP 
*   Servidor de mensagens SAP
*   SharePoint
*   SQL Server
*   Teradata

Estas etapas mostram como configurar o gateway de dados local para funcionar com seus aplicativos lógicos. Para obter mais informações sobre os conectores com suporte, consulte [Conectores para Aplicativo Lógico do Azure](../connectors/apis-list.md). 

Para obter informações sobre como usar o gateway com outros serviços, consulte estes artigos:

*   [Gateway de dados local do Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Gateway de dados local do Azure Analysis Services](../analysis-services/analysis-services-gateway.md)
*   [Gateway de dados local do Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Gateway de dados local do Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)

## <a name="requirements"></a>Requisitos

* É necessário já ter [instalado o gateway de dados em um computador local](logic-apps-gateway-install.md).

* Quando você entrar no Portal do Azure, deverá usar a mesma conta corporativa ou de estudante que foi usada para [instalar o gateway de dados local](logic-apps-gateway-install.md#requirements). Sua conta de logon também deve ter uma assinatura do Azure para usar ao criar um recurso de gateway no Portal do Azure para sua instalação do gateway.

* Sua instalação do gateway não pode já ter sido reivindicada por um recurso de gateway do Azure. Você pode associar sua instalação do gateway a apenas um recurso de gateway do Azure. A declaração acontece quando você cria o recurso de gateway de modo que a instalação fique indisponível a outros recursos.

* O gateway de dados local é executado como um serviço Windows e é configurado para usar `NT SERVICE\PBIEgwService` para as credenciais de logon de serviço Windows. Para criar e manter o recurso de gateway no portal do Azure, a [conta de serviço Windows](../logic-apps/logic-apps-gateway-install.md) deve ter, pelo menos, permissões de **Colaborador**. 

  > [!NOTE]
  > A conta de serviço Windows é diferente da conta usada para conexão a fontes de dados locais e da conta do Azure corporativa ou de estudante usada para entrar em serviços de nuvem.

## <a name="set-up-the-data-gateway-connection"></a>Configurar a conexão do gateway de dados

### <a name="1-install-the-on-premises-data-gateway"></a>1. Instale o gateway de dados local

Se ainda não tiver feito isso, siga estas [etapas para instalar o gateway de dados local](logic-apps-gateway-install.md). Antes de continuar com as outras etapas, verifique se instalou o gateway de dados em um computador local.

<a name="create-gateway-resource"></a>

### <a name="2-create-an-azure-resource-for-the-on-premises-data-gateway"></a>2. Criar um recurso do Azure para o gateway de dados local

Depois de instalar o gateway em um computador local, crie o gateway de dados como um recurso no Azure. Essa etapa também associa o recurso de gateway à sua assinatura do Azure.

1. Entre no [portal do Azure](https://portal.azure.com "portal do Azure"). Use o mesmo endereço de email do trabalho ou da escolha do Azure que o utilizado para instalar o gateway.

2. No menu principal do Azure, escolha **Novo** > **Enterprise Integration** > **Gateway de dados local** conforme mostrado aqui:

   ![Localize “Gateway de dados local”](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. Na página **Criar gateway de conexão**, forneça estes detalhes para criar o recurso do gateway de dados:

    * **Nome**: insira um nome para o recurso do gateway. 

    * **Assinatura**: selecione a assinatura do Azure a associar ao seu recurso de gateway. 
    Essa assinatura deve ser a mesma assinatura que a do seu aplicativo lógico.
   
      A assinatura padrão baseia-se na conta do Azure utilizada para entrar.

    * **Grupo de recursos**: crie um grupo de recursos ou selecione um grupo de recursos existente para implantar seu recurso de gateway. 
    Grupos de recursos ajudam a gerenciar os ativos do Azure relacionados como uma coleção.

    * **Local**: o Azure restringe esse local à mesma região selecionada para o serviço de nuvem do gateway durante a [instalação do gateway](logic-apps-gateway-install.md). 

      > [!NOTE]
      > Verifique se a localização do recurso gateway corresponde ao local de serviço de nuvem do gateway. Caso contrário, a instalação do gateway poderá não aparecer na lista de gateways instalados para você selecionar na próxima etapa.
      > 
      > Você pode usar diferentes regiões para o recurso de gateway e para seu aplicativo lógico.

    * **Nome da instalação**: se sua instalação do gateway ainda não estiver selecionada, selecione o gateway instalado anteriormente. 

    Para adicionar o recurso de gateway ao seu painel do Azure, escolha **Fixar no painel**. 
    Quando terminar, escolha **Criar**.

    Por exemplo:

    ![Fornecer detalhes para criar o gateway de dados local](./media/logic-apps-gateway-connection/createblade.png)

    Para localizar ou exibir seu gateway de dados a qualquer momento, no menu principal do Azure, acesse **Mais Serviços**>**Enterprise Integration**>**Gateways de Dados Locais**.

    ![Vá para "Mais serviços", "Enterprise Integration", "Gateways de Dados Locais"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

### <a name="3-connect-your-logic-app-to-the-on-premises-data-gateway"></a>3. Conectar seu aplicativo lógico ao gateway de dados local

Agora que você criou seu recurso de gateway de dados e associou sua assinatura do Azure ao recurso, crie uma conexão entre seu aplicativo lógico e o gateway de dados.

> [!NOTE]
> O local da conexão do gateway deve existir na mesma região que seu aplicativo lógico, mas você pode usar um gateway de dados que exista em uma região diferente.

1. No portal do Azure, crie ou abra seu aplicativo lógico no Designer de Aplicativo Lógico.

2. Adicione um conector que dê suporte a conexões locais, como o SQL Server.

3. Seguindo na ordem mostrada, selecione **Conectar por meio do gateway de dados local**, forneça um nome de conexão exclusivo e as informações necessárias e selecione o recurso do gateway de dados que deseja usar. Quando terminar, escolha **Criar**.

   > [!TIP]
   > Um nome de conexão exclusivo ajuda a identificar facilmente essa conexão mais tarde, especialmente quando você cria várias conexões. Se aplicável, também inclua o domínio qualificado para seu nome de usuário. 

   ![Criar a conexão entre o aplicativo lógico e o gateway de dados](./media/logic-apps-gateway-connection/blankconnection.png)

Parabéns, sua conexão de gateway está pronta para seu aplicativo lógico usar.

## <a name="edit-your-gateway-connection-settings"></a>Editar as configurações da conexão do gateway

Depois de criar uma conexão de gateway para seu aplicativo lógico, você talvez queira atualizar as configurações para essa conexão específica.

1. Para localizar a conexão de gateway:

   * No menu do aplicativo lógico, em **Ferramentas de Desenvolvimento**, selecione **Conexões de API**. 
   
     O painel **Conexões da API** mostra todas as conexões de API associadas ao seu aplicativo lógico, incluindo conexões do gateway.

     ![Vá para seu aplicativo lógico e selecione "Conexões de API"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Ou, no menu principal do Azure, acesse **Mais Serviços** > **Web + Móvel** > **Conexões da API** para todas as conexões de API, incluindo conexões do gateway, associadas à sua assinatura do Azure. 

   * Ou, no menu principal do Azure, vá para **Todos os recursos** para todas as conexões de API, incluindo conexões do gateway, associadas à sua assinatura do Azure.

2. Selecione a conexão de gateway que deseja exibir ou editar e escolha **Editar conexão da API**.

   > [!TIP]
   > Se suas atualizações não entrarem em vigor, tente [interromper e reiniciar o serviço Windows do gateway](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>
## <a name="switch-or-delete-your-on-premises-data-gateway-resource"></a>Mudar ou excluir o recurso de gateway de dados local

Para criar um recurso de gateway diferente, associar seu gateway a um recurso diferente ou remover o recurso de gateway, exclua o recurso de gateway sem afetar a instalação do gateway. 

1. No menu principal do Azure, acesse **Todos os recursos**. 
2. Localize e selecione seu recurso de gateway de dados.
3. Escolha **Gateway de Dados Local** e, na barra de ferramentas do recurso, escolha **Excluir**.

<a name="faq"></a>
## <a name="frequently-asked-questions"></a>Perguntas frequentes

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Próximas etapas

* [Proteja seus aplicativos lógicos](./logic-apps-securing-a-logic-app.md)
* [Exemplos comuns e cenários de aplicativos lógicos](./logic-apps-examples-and-scenarios.md)
