---
title: Conectar o Provance com o Conector de Gerenciamento de Serviços de TI
description: Este artigo fornece informações sobre como Provance com o Conector de Gerenciamento de Serviços de TI (ITSMC) no Azure Monitor para monitorar e gerenciar de forma centralizada os itens de trabalho de ITSM.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 542b127823a73058f319e6a39c001bd563f042ae
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045370"
---
# <a name="connect-provance-with-it-service-management-connector"></a>Conectar o Provance com o Conector de Gerenciamento de Serviços de TI

Este artigo fornece informações sobre como configurar a conexão entre sua instância do Provance e o Conector de Gerenciamento de Serviços de TI (ITSMC) no Log Analytics para gerenciar centralmente seus itens de trabalho.

> [!NOTE]
> A partir de 1-out-2020 Provance, a integração de ITSM com o alerta do Azure não será mais habilitada para novos clientes. Não haverá suporte para novas conexões de ITSM.
> Haverá suporte para conexões de ITSM existentes.

As seções a seguir fornecem detalhes sobre como conectar seu produto Provance ao ITSMC no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Verifique se os seguintes pré-requisitos foram atendidos:

- ITSMC instalado. Mais informações: [Adicionar a solução Conector de Gerenciamento de Serviços de TI](./itsmc-definition.md#add-it-service-management-connector).
- Provance aplicativo deve ser registrado com o Azure AD - ID do cliente é disponibilizada. Para obter informações detalhadas, consulte [como configurar a autenticação do active directory](../../app-service/configure-authentication-provider-aad.md).

- Função de usuário:  Administrador.

## <a name="connection-procedure"></a>Procedimento de conexão

Use o procedimento a seguir para criar uma conexão do Provance:

1. No portal do Azure, acesse **Todos os Recursos** e procure **ServiceDesk(YourWorkspaceName)**

2. Em **FONTES DE DADOS DO WORKSPACE**, clique em **Conexões de ITSM**.
    ![Nova conexão](media/itsmc-overview/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações, conforme descrito na tabela a seguir, e clique em **OK** para criar a conexão.

> [!NOTE]
> Todos esses parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome da conexão**   | Digite um nome para a instância do Provance que você deseja conectar com o ITSMC.  Você usará esse nome posteriormente ao configurar itens de trabalho neste ITSM/análise de logs detalhado da exibição. |
| **Tipo de parceiro**   | Selecione **Provance**. |
| **Nome de usuário**   | Digite o nome de usuário que pode se conectar ao ITSMC.    |
| **Senha**   | Digite a senha associada a esse nome de usuário. **Observação:** O nome de usuário e a senha são usados apenas para gerar tokens de autenticação, não sendo armazenados em nenhum lugar dentro do serviço ITSMC.|
| **Servidor URL**   | Digite a URL da sua instância do Provance que você deseja conectar ao ITSMC. |
| **ID do Cliente**   | Digite a ID do cliente para autenticar esta conexão, que é gerado em sua instância de Provance.  Para saber mais sobre a ID do cliente, consulte [como configurar a autenticação do active directory](../../app-service/configure-authentication-provider-aad.md). |
| **Escopo de Sincronização de Dados**   | Selecione os itens de trabalho do Provance que você deseja sincronizar com o Azure Log Analytics por meio do ITSMC.  Esses itens de trabalho são importados para o Log Analytics.   **Opções:**   Incidentes, Solicitações de Alteração.|
| **Sincronizar Dados** | Digite o número de dias anteriores dos quais você deseja dados. **Limite máximo**: 120 dias. |
| **Criar um novo item de configuração na solução ITSM** | Selecione esta opção se você deseja criar os itens de configuração no produto ITSM. Quando selecionado, o ITSMC cria os CIs afetados como itens de configuração (no caso de CIs inexistentes) no sistema de ITSM compatível. **Padrão**: desabilitado.|

![Captura de tela que realça as listas nome da conexão e tipo de parceiro.](media/itsmc-connections-provance/itsm-connections-provance-latest.png)

**Quando conectado e sincronizado com êxito**:

- Os itens de trabalho selecionados nessa instância do Provance são importados para o Azure **Log Analytics.** Você pode exibir o resumo desses itens de trabalho no bloco do conector de Gerenciamento do Serviço de TI.

- Crie incidentes com base em alertas ou registros de log do Log Analytics ou em alertas do Azure nesta instância do Provance.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do Conector ITSM](itsmc-overview.md)
* [Criar itens de trabalho de ITSM desde alertas do Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Solução de problemas no Conector ITSM](./itsmc-resync-servicenow.md)