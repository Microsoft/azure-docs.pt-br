---
title: Configuração de TLS-portal do Azure-banco de dados do Azure para MySQL
description: Saiba como definir a configuração de TLS usando o portal do Azure para o banco de dados do Azure para MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 5ecf2992fa9ea56f73748a9f1f98c75f9076c68f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656882"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>Definindo configurações de TLS no banco de dados do Azure para MySQL usando portal do Azure

Este artigo descreve como você pode configurar um servidor de banco de dados do Azure para MySQL para impor a versão mínima do TLS permitida para conexões a serem passadas e negar todas as conexões com uma versão mais baixa do TLS do que a versão mínima do TLS, melhorando assim a segurança da rede.

Você pode impor a versão do TLS para se conectar ao banco de dados do Azure para MySQL. Agora, os clientes têm a opção de definir a versão mínima do TLS para seu servidor de banco de dados. Por exemplo, definir essa versão mínima do TLS como 1,0 significa que você deve permitir que os clientes se conectem usando TLS 1.0, 1.1 e 1,2. Como alternativa, definir isso como 1,2 significa que você só permitirá que os clientes que se conectam usando TLS 1.2 + e todas as conexões de entrada com TLS 1,0 e TLS 1,1 serão rejeitados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

* Um [banco de dados do Azure para MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>Definir configurações de TLS para o banco de dados do Azure para MySQL

Siga estas etapas para definir a versão mínima do TLS do servidor MySQL:

1. Na [portal do Azure](https://portal.azure.com/), selecione seu banco de dados do Azure para servidor MySQL existente.

1. Na página MySQL Server, em **configurações**, clique em **segurança de conexão** para abrir a página configuração de segurança de conexão.

1. Em **versão mínima do TLS**, selecione **1,2** para negar conexões com a versão tls inferior ao TLS 1,2 para o servidor MySQL.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="Configuração TLS do banco de dados do Azure para MySQL":::

1. Clique em **Salvar** para salvar as alterações. 

1. Uma notificação confirmará se a configuração de segurança de conexão foi habilitada com êxito e em vigor imediatamente. Não há **reinicialização** do servidor necessária ou executada. Depois que as alterações forem salvas, todas as novas conexões com o servidor serão aceitas somente se a versão do TLS for maior ou igual à versão mínima do TLS definida no Portal.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="Êxito na configuração de TLS do banco de dados do Azure para MySQL":::

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [como criar alertas sobre métricas](howto-alert-on-metric.md)
