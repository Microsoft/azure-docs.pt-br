---
title: Atualizar com aplicativos lógicos para modelos de Azure Analysis Services | Microsoft Docs
description: Este artigo descreve como codificar a atualização assíncrona para Azure Analysis Services usando os aplicativos lógicos do Azure.
author: chrislound
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.custom: references_regions
ms.openlocfilehash: 8a8d434fca7cab4432f38fc64093cf1fe060bd5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92019079"
---
# <a name="refresh-with-logic-apps"></a>Atualizar com Aplicativos Lógicos

Usando aplicativos lógicos e chamadas REST, você pode executar operações automatizadas de atualização de dados em seus modelos de tabela de análise do Azure, incluindo sincronização de réplicas somente leitura para expansão de consulta.

Para saber mais sobre como usar as APIs REST com Azure Analysis Services, consulte [atualização assíncrona com a API REST](analysis-services-async-refresh.md).

## <a name="authentication"></a>Autenticação

Todas as chamadas devem ser autenticadas com um token válido do Azure Active Directory (OAuth 2).  Os exemplos neste artigo usarão uma entidade de serviço (SPN) para autenticar para Azure Analysis Services. Para saber mais, confira [criar uma entidade de serviço usando portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Criar o aplicativo lógico

> [!IMPORTANT]
> Os exemplos a seguir pressupõem que o Firewall do Azure Analysis Services está desabilitado. Se o firewall estiver habilitado, o endereço IP público do iniciador da solicitação deverá ser adicionado à lista aprovada no firewall do Azure Analysis Services. Para saber mais sobre intervalos de IP de aplicativos lógicos do Azure por região, consulte [limites e informações de configuração para aplicativos lógicos do Azure](../logic-apps/logic-apps-limits-and-config.md#configuration).

### <a name="prerequisites"></a>Pré-requisitos

#### <a name="create-a-service-principal-spn"></a>Criar uma entidade de serviço (SPN)

Para saber como criar uma entidade de serviço, consulte [Criar uma entidade de serviço usando o portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Configurar permissões no Azure Analysis Services
 
A entidade de serviço que você criar deve ter permissões de administrador do servidor no servidor. Para saber mais, consulte [Adicionar uma entidade de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Configurar o aplicativo lógico

Neste exemplo, o aplicativo lógico foi projetado para disparar quando uma solicitação HTTP é recebida. Isso permitirá o uso de uma ferramenta de orquestração, como Azure Data Factory, para disparar a atualização do modelo de Azure Analysis Services.

Depois de criar um aplicativo lógico:

1. No designer do aplicativo lógico, escolha a primeira ação como **quando uma solicitação HTTP é recebida**.

   ![Adicionar atividade recebida de HTTP](./media/analysis-services-async-refresh-logic-app/1.png)

Esta etapa será preenchida com a URL HTTP POST quando o aplicativo lógico for salvo.

2. Adicione uma nova etapa e pesquise por **http**.  

   ![Captura de tela da seção "escolher uma ação" com o bloco "HTTP" selecionado.](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Captura de tela da janela "HTTP" com o bloco "HTTP-HTTP" selecionado.](./media/analysis-services-async-refresh-logic-app/10.png)

3. Selecione **http** para adicionar esta ação.

   ![Adicionar atividade HTTP](./media/analysis-services-async-refresh-logic-app/2.png)

Configure a atividade HTTP da seguinte maneira:

|Propriedade  |Valor  |
|---------|---------|
|**Método**     |POST         |
|**URI**     | https://*sua região do servidor*/Servers/*AAS nome do servidor*/Models/*nome do seu banco de dados*/refreshes <br /> <br /> Por exemplo: https: \/ /westus.asazure.Windows.net/servers/MyServer/Models/AdventureWorks/refreshes|
|**Cabeçalhos**     |   Tipo de conteúdo, aplicativo/JSON <br /> <br />  ![Cabeçalhos](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Corpo**     |   Para saber mais sobre como formar o corpo da solicitação, consulte [atualização assíncrona com a API REST – post/refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Autenticação**     |Active Directory OAuth         |
|**Locatário**     |Preencha seu Azure Active Directory Tenantid         |
|**Público-alvo**     |https://*. asazure. Windows. net         |
|**ID do Cliente**     |Insira o nome da entidade de serviço ClientID         |
|**Tipo de Credencial**     |Segredo         |
|**Segredo**     |Insira o segredo do nome da entidade de serviço         |

Exemplo:

![Atividade HTTP concluída](./media/analysis-services-async-refresh-logic-app/7.png)

Agora, teste o aplicativo lógico.  No designer do aplicativo lógico, clique em **executar**.

![Testar o aplicativo lógico](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Consumir o aplicativo lógico com Azure Data Factory

Depois que o aplicativo lógico for salvo, examine a atividade **quando uma solicitação HTTP é recebida** e, em seguida, copie a **URL http post** que agora é gerada.  Essa é a URL que pode ser usada pelo Azure Data Factory para fazer a chamada assíncrona disparar o aplicativo lógico.

Aqui está um exemplo Azure Data Factory atividade da Web que faz essa ação.

![Data Factory atividade da Web](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Usar um aplicativo lógico independente

Se você não planeja usar uma ferramenta de orquestração como Data Factory para disparar a atualização do modelo, você pode definir o aplicativo lógico para disparar a atualização com base em um agendamento.

Usando o exemplo acima, exclua a primeira atividade e substitua-a por uma atividade de **agendamento** .

![Captura de tela que mostra a página "aplicativos lógicos" com o bloco "Agenda" selecionado.](./media/analysis-services-async-refresh-logic-app/12.png)

![Captura de tela que mostra a página "gatilhos".](./media/analysis-services-async-refresh-logic-app/13.png)

Este exemplo usará a **recorrência**.

Depois que a atividade tiver sido adicionada, configure o intervalo e a frequência e, em seguida, adicione um novo parâmetro e escolha **essas horas**.

![Captura de tela que mostra a seção "recorrência" com o parâmetro "em estas horas" selecionado.](./media/analysis-services-async-refresh-logic-app/16.png)

Selecione as horas desejadas.

![Atividade de agendamento](./media/analysis-services-async-refresh-logic-app/15.png)

Salve o aplicativo lógico.

## <a name="next-steps"></a>Próximas etapas

[Amostras](analysis-services-samples.md)  
[REST API](/rest/api/analysisservices/servers)