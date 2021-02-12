---
title: Conectar o Cherwell com o Conector de Gerenciamento de Serviços de TI
description: Este artigo fornece informações sobre como Cherwell com o Conector de Gerenciamento de Serviços de TI (ITSMC) no Azure Monitor para monitorar e gerenciar de forma centralizada os itens de trabalho de ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: a24ece3c0b75d0d0d22e13c6e5367c27fbaeab66
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100006778"
---
# <a name="connect-cherwell-with-it-service-management-connector"></a>Conectar o Cherwell com o Conector de Gerenciamento de Serviços de TI

Este artigo fornece informações sobre como configurar a conexão entre sua instância do Cherwell e o Conector de Gerenciamento de Serviços de TI (ITSMC) no Log Analytics para gerenciar centralmente seus itens de trabalho.

> [!NOTE]
> A partir de 1-out-2020 Cherwell, a integração de ITSM com o alerta do Azure não será mais habilitada para novos clientes. Não haverá suporte para novas conexões de ITSM.
> Haverá suporte para conexões de ITSM existentes.

As seções a seguir fornecem detalhes sobre como conectar seu produto Cherwell ao ITSMC no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Verifique se os seguintes pré-requisitos foram atendidos:

- ITSMC instalado. Mais informações: [Adicionar a solução Conector de Gerenciamento de Serviços de TI](./itsmc-definition.md#add-it-service-management-connector).
- ID do Cliente gerada. Mais informações: [Gerar a ID do Cliente do Cherwell](#generate-client-id-for-cherwell).
- Função de usuário:  Administrador.

## <a name="connection-procedure"></a>Procedimento de conexão

Use o procedimento a seguir para criar uma conexão do Cherwell:

1. No portal do Azure, acesse **Todos os Recursos** e procure **ServiceDesk(YourWorkspaceName)**

2. Em **FONTES DE DADOS DO WORKSPACE**, clique em **Conexões de ITSM**.
    ![Nova conexão](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações, conforme descrito na tabela a seguir, e clique em **OK** para criar a conexão.

> [!NOTE]
> Todos esses parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome da conexão**   | Digite um nome para a instância do Cherwell que você deseja conectar ao ITSMC.  Você usará esse nome posteriormente ao configurar itens de trabalho neste ITSM/análise de logs detalhado da exibição. |
| **Tipo de parceiro**   | Selecione **Cherwell.** |
| **Nome de usuário**   | Digite o nome de usuário do Cherwell que pode se conectar ao ITSMC. |
| **Senha**   | Digite a senha associada a esse nome de usuário. **Observação:** O nome de usuário e a senha são usados apenas para gerar tokens de autenticação, não sendo armazenados em nenhum lugar dentro do serviço ITSMC.|
| **Servidor URL**   | Digite a URL da sua instância do Cherwell que você deseja conectar ao ITSMC. |
| **ID do Cliente**   | Digite a ID do cliente para autenticar esta conexão, que é gerado em sua instância do Cherwell.   |
| **Escopo de Sincronização de Dados**   | Selecione os itens de trabalho do Cherwell que você deseja sincronizar por meio do ITSMC.  Esses itens de trabalho são importados para o Log Analytics.   **Opções:**  Incidentes, Solicitações de Alteração. |
| **Sincronizar Dados** | Digite o número de dias anteriores dos quais você deseja dados. **Limite máximo**: 120 dias. |
| **Criar um novo item de configuração na solução ITSM** | Selecione esta opção se você deseja criar os itens de configuração no produto ITSM. Quando selecionado, o ITSMC cria os CIs afetados como itens de configuração (no caso de CIs inexistentes) no sistema de ITSM compatível. **Padrão**: desabilitado. |

![Conexão Cherwell](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**Quando conectado e sincronizado com êxito**:

- Os itens de trabalho selecionados nessa instância do Cherwell são importados para o Azure **Log Analytics.** Você pode exibir o resumo desses itens de trabalho no bloco do conector de Gerenciamento do Serviço de TI.

- Crie incidentes com base em alertas ou registros de log do Log Analytics ou em alertas do Azure nesta instância do Cherwell.

Saiba mais: [Criar itens de trabalho de ITSM a partir de alertas do Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Gerar a ID de Cliente do Cherwell

Para gerar o chave/ID do cliente para Cherwell, use o procedimento a seguir:

1. Fazer logon em sua instância de Cherwell como administrador.
2. Clique em **Segurança** > **Editar configurações de cliente de API REST**.
3. Selecione **Criar novo cliente** > **segredo do cliente**.

    ![Id de usuário do Cherwell](media/itsmc-connections/itsmc-cherwell-client-id.png)

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do Conector ITSM](itsmc-overview.md)
* [Criar itens de trabalho de ITSM desde alertas do Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Solução de problemas no Conector ITSM](./itsmc-resync-servicenow.md)