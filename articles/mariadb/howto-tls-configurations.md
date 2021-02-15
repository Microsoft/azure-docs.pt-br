---
title: Configuração de TLS-portal do Azure-banco de dados do Azure para MariaDB
description: Saiba como definir a configuração de TLS usando portal do Azure para o banco de dados do Azure para MariaDB
author: mksuni
ms.author: sumuth
ms.service: jroth
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: b4e167714ffccd20b4628fd21fb6b284d5400112
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664759"
---
# <a name="configuring-tls-settings-in-azure-database-for-mariadb-using-azure-portal"></a>Definindo configurações de TLS no banco de dados do Azure para MariaDB usando portal do Azure

Este artigo descreve como você pode configurar um banco de dados do Azure para o servidor MariaDB para impor a versão mínima do TLS para conexões a serem passadas e negar todas as conexões com uma versão mais baixa do TLS do que a versão mínima do TLS, melhorando assim a segurança da rede.

Você pode impor a versão do TLS para se conectar ao banco de dados do Azure para MariaDB definindo a versão mínima do TLS para seu servidor de banco de dados. Por exemplo, definir a versão mínima de configuração de TLS como TLS 1,0 significa que o servidor permitirá conexões de clientes usando TLS 1,0, 1,1 e 1.2 +. Como alternativa, definir isso como 1,2 significa que você só permite conexões de clientes que usam o TLS 1.2 + e todas as conexões com TLS 1,0 e TLS 1,1 serão rejeitadas.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

* Um [banco de dados do Azure para MariaDB](quickstart-create-mariaDB-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mariadb"></a>Definir configurações de TLS para o banco de dados do Azure para MariaDB

Siga estas etapas para definir a versão mínima do TLS do servidor MariaDB:

1. Na [portal do Azure](https://portal.azure.com/), selecione seu banco de dados do Azure existente para o servidor MariaDB.

1. Na página do servidor do MariaDB, em **configurações**, clique em **segurança de conexão** para abrir a página configuração de segurança da conexão.

1. Em **versão mínima do TLS**, selecione **1,2** para negar conexões com a versão tls inferior ao TLS 1,2 para seu servidor MariaDB.

    ![Banco de dados do Azure para configuração do MariaDB TLS](./media/howto-tls-configurations/tls-configurations.png)

1. Clique em **Salvar** para salvar as alterações.

1. Uma notificação confirmará que a configuração de segurança de conexão foi habilitada com êxito.

    ![Êxito na configuração do banco de dados do Azure para MariaDB TLS](./media/howto-tls-configurations/tls-configurations-success.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como criar alertas sobre métricas](howto-alert-metric.md)