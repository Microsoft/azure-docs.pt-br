---
title: Acessar fontes de dados locais
description: Conectar-se a fontes de dados locais de aplicativos lógicos do Azure criando um recurso de gateway de dados no Azure
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: 356e63bb0a749ad0f41d886e75971e9b05c7f9dc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99218954"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Conectar-se a fontes de dados locais dos Aplicativos Lógicos do Azure

Depois de [instalar o *Gateway de dados* local em um computador local](../logic-apps/logic-apps-gateway-install.md) e antes de poder acessar as fontes de dados locais de seus aplicativos lógicos, você precisa criar um recurso de gateway no Azure para a instalação do gateway. Você pode selecionar esse recurso de gateway nos gatilhos e ações que deseja usar para os [conectores locais](../connectors/apis-list.md#on-premises-connectors) disponíveis nos aplicativos lógicos do Azure. O aplicativo lógico do Azure dá suporte a operações de leitura e gravação por meio do gateway de dados. No entanto, essas operações têm [limites no tamanho da carga](/data-integration/gateway/service-gateway-onprem#considerations).

Este artigo mostra como criar seu recurso de gateway do Azure para um [Gateway instalado anteriormente no computador local](../logic-apps/logic-apps-gateway-install.md). Para obter mais informações sobre o gateway, consulte [como funciona o gateway](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Para acessar diretamente os recursos locais em redes virtuais do Azure sem precisar usar o gateway, considere criar um [*ambiente de serviço de integração*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) em vez disso. 

Para obter informações sobre como usar o gateway com outros serviços, consulte estes artigos:

* [Gateway de dados local do Microsoft Power Automate](/power-automate/gateway-reference)
* [Gateway de dados local do Microsoft Power BI](/power-bi/service-gateway-onprem)
* [Gateway de dados local do Microsoft Power Apps](/powerapps/maker/canvas-apps/gateway-reference)
* [Gateway de dados local do Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Fontes de dados com suporte

Nos aplicativos lógicos do Azure, o gateway de dados local dá suporte aos [conectores locais](../connectors/apis-list.md#on-premises-connectors) para essas fontes de dados:

* BizTalk Server 2016
* Sistema de Arquivos
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Banco de dados Oracle
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Você também pode criar [conectores personalizados](../logic-apps/custom-connector-overview.md) que se conectam a fontes de dados por http ou HTTPS usando REST ou SOAP. Embora o próprio gateway não incorra em custos adicionais, o [modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md) se aplica a esses conectores e a outras operações nos aplicativos lógicos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Você já [instalou o gateway de dados local em um computador local](../logic-apps/logic-apps-gateway-install.md). Essa instalação de gateway deve existir antes que você possa criar um recurso de gateway que se vincule a essa instalação.

* Você tem a [mesma conta e assinatura do Azure](../logic-apps/logic-apps-gateway-install.md#requirements) que usou para a instalação do gateway. Essa conta do Azure deve pertencer somente a um único [locatário ou diretório do Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology). Você precisa usar a mesma conta e assinatura do Azure para criar o recurso de gateway no Azure porque apenas o administrador de gateway pode criar o recurso de gateway no Azure. Atualmente, não há suporte para entidades de serviço.

  * Ao criar um recurso de gateway no Azure, você seleciona uma instalação de gateway para vincular com o recurso de gateway e somente esse recurso de gateway. Cada recurso de gateway pode vincular a apenas uma instalação de gateway. Você não pode selecionar uma instalação de gateway que já esteja associada a outro recurso de gateway.

  * Seu aplicativo lógico e o recurso de gateway não precisam existir na mesma assinatura do Azure. Em gatilhos e ações em que você pode usar o recurso de gateway, você pode selecionar uma assinatura do Azure diferente que tenha um recurso de gateway, mas somente se essa assinatura existir no mesmo diretório ou locatário do Azure AD que seu aplicativo lógico. Você também precisa ter permissões de administrador no gateway, que outro administrador pode configurar para você. Para obter mais informações, consulte [Gateway de dados: automação usando o PowerShell-parte 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) e [PowerShell: data gateway-Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser).

    > [!NOTE]
    > No momento, você não pode compartilhar um recurso de gateway ou uma instalação em várias assinaturas. Para enviar comentários sobre o produto, consulte [Microsoft Azure Fórum de comentários](https://feedback.azure.com/forums/34192--general-feedback).

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Criar recurso de gateway do Azure

Depois de instalar o gateway em um computador local, crie o recurso do Azure para seu gateway.

1. Entre no [portal do Azure](https://portal.azure.com) com a mesma conta do Azure que foi usada para instalar o gateway.

1. Na caixa de pesquisa portal do Azure, insira "gateway de dados local" e selecione **gateways de dados locais**.

   ![Localize “Gateway de dados local”](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. Em **gateways de dados locais**, selecione **Adicionar**.

   ![Adicionar novo recurso do Azure para gateway de dados](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. Em **criar gateway de conexão**, forneça essas informações para o recurso de gateway. Quando terminar, selecione **Criar**.

   | Propriedade | Descrição |
   |----------|-------------|
   | **Nome do recurso** | Forneça um nome para o recurso de gateway que contém apenas letras, números, hifens ( `-` ), sublinhados ( `_` ), parênteses ( `(` , `)` ) ou pontos ( `.` ). |
   | **Assinatura** | Selecione a assinatura do Azure para a conta do Azure que foi usada para a instalação do gateway. A assinatura padrão baseia-se na conta do Azure utilizada para entrar. |
   | **Grupo de recursos** | O [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) que você deseja usar |
   | **Localidade** | A mesma região ou local que foi selecionado para o serviço de nuvem do gateway durante a [instalação do gateway](../logic-apps/logic-apps-gateway-install.md). Caso contrário, a instalação do gateway não aparecerá na lista **nome da instalação** . O local do aplicativo lógico pode ser diferente do local do recurso do gateway. |
   | **Nome de Instalação** | Selecione uma instalação de gateway, que aparece na lista somente quando essas condições são atendidas: <p><p>-A instalação do gateway usa a mesma região que o recurso de gateway que você deseja criar. <br>-A instalação do gateway não está vinculada a outro recurso de gateway do Azure. <br>-A instalação do gateway está vinculada à mesma conta do Azure que você está usando para criar o recurso de gateway. <br>-Sua conta do Azure pertence a um único [locatário ou diretório do Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology) e é a mesma conta que você usou para a instalação do gateway. <p><p>Para obter mais informações, consulte a seção [perguntas](#faq) frequentes. |
   |||

   Aqui está um exemplo que mostra uma instalação de gateway que está na mesma região que o recurso de gateway e está vinculado à mesma conta do Azure:

   ![Fornecer detalhes para criar um recurso de gateway de dados](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Conectar-se a dados locais

Após criar o recurso de gateway e associar a assinatura do Azure a esse recurso, será possível criar uma conexão entre o aplicativo lógico e a fonte de dados local usando o gateway.

1. No portal do Azure, crie ou abra o aplicativo lógico no Designer do Aplicativo Lógico.

1. Adicione um conector que dê suporte a conexões locais, por exemplo **SQL Server**.

1. Selecione **Conectar por meio do gateway de dados local**.

1. Em **Gateway**, na lista **assinatura** , selecione sua assinatura do Azure que tem o recurso de gateway desejado.

   Seu aplicativo lógico e o recurso de gateway não precisam existir na mesma assinatura do Azure. Você pode selecionar entre outras assinaturas do Azure que têm um recurso de gateway, mas somente se essas assinaturas existirem no mesmo locatário ou diretório do Azure AD que o aplicativo lógico, e você tiver permissões de administrador no gateway, o que outro administrador poderá configurar para você. Para obter mais informações, consulte [Gateway de dados: automação usando o PowerShell-parte 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) e [PowerShell: data gateway-Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser).
  
1. Na lista **Gateway de conexão** , que mostra os recursos de gateway disponíveis em sua assinatura selecionada, selecione o recurso de gateway desejado. Cada recurso de gateway é vinculado a uma única instalação de gateway.

   > [!NOTE]
   > A lista de gateways inclui recursos de gateway em outras regiões porque o local do seu aplicativo lógico pode ser diferente do local do seu recurso de gateway. 

1. Forneça um nome de conexão exclusivo e outras informações necessárias, que dependem da conexão que você deseja criar.

   Um nome de conexão exclusivo ajuda a localizar facilmente essa conexão mais tarde, especialmente se você criar várias conexões. Se aplicável, também inclua o domínio qualificado para seu nome de usuário.

   Veja um exemplo:

   ![Criar a conexão entre o aplicativo lógico e o gateway de dados](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Quando terminar, selecione **Criar**.

A conexão de gateway agora está pronta para o aplicativo lógico usar.

## <a name="edit-connection"></a>Editar conexão

Para atualizar as configurações de uma conexão de gateway, você pode editar sua conexão.

1. Para localizar todas as conexões de API apenas para seu aplicativo lógico, no menu do aplicativo lógico, em **ferramentas de desenvolvimento**, selecione **conexões de API**.

   ![No menu do aplicativo lógico, selecione "conexões de API"](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Selecione a conexão de gateway desejada e, em seguida, selecione **Editar conexão de API**.

   > [!TIP]
   > Se as atualizações não entrarem em vigor, tente [parar e reiniciar a conta de serviço do Windows de gateway](../logic-apps/logic-apps-gateway-install.md#restart-gateway) para a instalação do gateway.

Para localizar todas as conexões de API associadas à assinatura do Azure:

* No menu portal do Azure, selecione **todos os serviços**  >  conexões de API **da Web**  >  .
* Ou, no menu portal do Azure, selecione **todos os recursos**. Defina o filtro de **tipo** como **conexão de API**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Excluir recurso de gateway

Para criar um recurso de gateway diferente, vincule a instalação do gateway a um recurso de gateway diferente ou remova o recurso de gateway, você pode excluir o recurso de gateway sem afetar a instalação do gateway.

1. No menu portal do Azure, selecione **todos os recursos** ou pesquise e selecione **todos os recursos** em qualquer página. Localize e selecione o recurso de gateway.

1. Se ainda não estiver selecionado, no menu de recursos do gateway, selecione **Gateway de Dados Local**. Na barra de ferramentas de recursos do gateway, selecione **excluir**.

   Por exemplo:

   ![Excluir recurso de gateway no Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P**: por que minha instalação de gateway não aparece quando crio meu recurso de gateway no Azure? <br/>
**R**: Esse problema pode ocorrer pelos seguintes motivos:

* Sua conta do Azure não é a mesma conta que você usou para a instalação do gateway no computador local. Verifique se você entrou no portal do Azure com a mesma identidade que usou para a instalação do gateway. Somente o administrador de gateway pode criar o recurso de gateway no Azure. Atualmente, não há suporte para entidades de serviço.

* Sua conta do Azure não pertence a apenas um único [locatário ou diretório do Azure ad](../active-directory/fundamentals/active-directory-whatis.md#terminology). Verifique se você está usando o mesmo locatário ou diretório do Azure AD que você usou durante a instalação do gateway.

* O recurso de gateway e a instalação do gateway não existem na mesma região. No entanto, o local do seu aplicativo lógico pode ser diferente do local do recurso do gateway.

* A instalação do gateway já está associada a outro recurso de gateway. Cada recurso de gateway pode vincular a apenas uma instalação de gateway, que pode ser vinculada a apenas uma conta do Azure e uma assinatura. Portanto, você não pode selecionar uma instalação de gateway que já esteja associada a outro recurso de gateway. Essas instalações não aparecerão na lista **nome da instalação** .

  Para examinar os registros de gateway no portal do Azure, encontre todos os recursos do Azure que têm o tipo de recurso de **gateways de dados local** em *todas as* suas assinaturas do Azure. Para desvincular a instalação do gateway do outro recurso de gateway, consulte [excluir recurso de gateway](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Próximas etapas

* [Assegurar seus aplicativos lógicos](./logic-apps-securing-a-logic-app.md)
* [Exemplos comuns e cenários de aplicativos lógicos](./logic-apps-examples-and-scenarios.md)
