---
title: Configuração de TLS-portal do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Saiba como definir a configuração de TLS usando o portal do Azure para o banco de dados do Azure para PostgreSQL servidor único
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 25be6b3c4e3172fc8ee14b97fd890b5948c284ba
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242357"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql-single---server-using-azure-portal"></a>Definindo configurações de TLS no banco de dados do Azure para PostgreSQL servidor único usando portal do Azure

Este artigo descreve como você pode configurar um banco de dados do Azure para PostgreSQL para impor a versão mínima do TLS permitida para conexões e negar todas as conexões com uma versão mais baixa do TLS do que a versão mínima do TLS configurada, melhorando assim a segurança da rede.

Você pode impor a versão do TLS para se conectar ao banco de dados do Azure para PostgreSQL. Agora, os clientes têm a opção de definir a versão mínima do TLS para seu servidor de banco de dados. Por exemplo, definir a versão mínima de configuração de TLS como TLS 1,0 significa que o servidor permitirá conexões de clientes usando TLS 1,0, 1,1 e 1.2 +. Em vez disso, definir a versão mínima do TLS como 1.2 + significa que você só permite conexões de clientes que usam TLS 1,2 e todas as conexões com TLS 1,0 e TLS 1,1 serão rejeitadas.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

* Um [banco de dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>Definir configurações de TLS para o banco de dados do Azure para PostgreSQL-servidor único

Siga estas etapas para definir a versão mínima do TLS do PostgreSQL:

1. Na [portal do Azure](https://portal.azure.com/), selecione seu banco de dados do Azure existente para PostgreSQL.

1.  Na página banco de dados do Azure para PostgreSQL – servidor único, em **configurações** , clique em **segurança de conexão** para abrir a página configuração de segurança da conexão.

1. Em **versão mínima do TLS** , selecione **1,2** para negar conexões com a versão tls inferior ao TLS 1,2 para seu servidor único PostgreSQL.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="Configuração do TLS de servidor único do banco de dados do Azure para PostgreSQL":::

1. Clique em **Salvar** para salvar as alterações.

1. Uma notificação confirmará que a configuração de segurança de conexão foi habilitada com êxito.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="Configuração do TLS de servidor único do banco de dados do Azure para PostgreSQL":::

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como criar alertas sobre métricas](howto-alert-on-metric.md)