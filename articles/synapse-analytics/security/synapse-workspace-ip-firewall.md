---
title: Configurar regras de firewall de IP
description: Um artigo que ensina a configurar regras de firewall de IP no Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 3cbd9fd697bbae8779f5b2b9fa4ab817efce30ac
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454642"
---
# <a name="azure-synapse-analytics-ip-firewall-rules"></a>Regras de firewall de IP do Azure Synapse Analytics

Este artigo explicará as regras de firewall de IP e ensinará a configurá-las no Azure Synapse Analytics.

## <a name="ip-firewall-rules"></a>Regras de firewall de IP

As regras de firewall de IP permitem ou negam acesso ao seu workspace do Synapse com base no endereço IP de origem de cada solicitação. Configure regras de firewall de IP para o seu workspace. As regras de firewall de IP configuradas no nível do workspace se aplicam a todos os pontos de extremidade públicos do workspace (pools de SQL dedicados, pool de SQL sem servidor e desenvolvimento).

## <a name="create-and-manage-ip-firewall-rules"></a>Criar e gerenciar regras de firewall de IP

Há duas maneiras de adicionar regras de firewall de IP a um workspace do Synapse. Para adicionar um firewall de IP ao workspace, selecione **Segurança + Rede** e marque a opção **Permitir conexões de todos os endereços IP** durante a criação do workspace.

![Captura de tela que realça o botão Segurança + rede.](./media/synpase-workspace-ip-firewall/ip-firewall-1.png)

![Configuração de IP do workspace do Synapse no portal do Azure.](./media/synpase-workspace-ip-firewall/ip-firewall-2.png)

Adicione também regras de firewall de IP a um workspace do Synapse após a criação do workspace. Selecione **Firewalls** em **Segurança** no portal do Azure. Para adicionar uma nova regra de firewall de IP, dê a ela um nome, um IP inicial e um IP final. Selecione **Salvar** quando terminar.

![Configuração de IP do workspace do Azure Synapse no portal do Azure.](./media/synpase-workspace-ip-firewall/ip-firewall-3.png)

## <a name="connect-to-synapse-from-your-own-network"></a>Conectar-se ao Synapse de sua própria rede

Conecte-se ao seu workspace do Synapse usando o Synapse Studio. Use também o SSMS (SQL Server Management Studio) para se conectar aos recursos do SQL (pools de SQL dedicados e pool de SQL sem servidor) no seu workspace.

Verifique se o firewall na rede e no computador local permite a comunicação de saída nas portas TCP 80, 443 e 1443 do Synapse Studio.

Além disso, você precisa permitir a comunicação de saída na porta UDP 53 do Synapse Studio. Para se conectar usando ferramentas como o SSMS e o Power BI, você precisará permitir a comunicação de saída na porta TCP 1433.

Se você estiver usando a configuração de política de conexão de redirecionamento padrão, poderá precisar permitir a comunicação de saída em portas adicionais. Saiba mais sobre as políticas de conexão [aqui](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy).

## <a name="next-steps"></a>Próximas etapas

Criar um [Workspace do Azure Synapse](../quickstart-create-workspace.md)

Criar um workspace do Azure Synapse com uma [Rede Virtual de workspace gerenciada](./synapse-workspace-managed-vnet.md)