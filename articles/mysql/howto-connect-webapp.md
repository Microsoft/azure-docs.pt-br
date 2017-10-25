---
title: "Conectar um Serviço de Aplicativo do Azure existente ao Banco de Dados do Azure para MySQL | Microsoft Docs"
description: "Instruções sobre como conectar corretamente um Serviço de Aplicativo do Azure ao Banco de Dados do Azure para MySQL"
services: mysql
author: v-chenyh
ms.author: v-chenyh
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 0028b0c918b573f6884e6f63fa82dbe23079882a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Conectar um Serviço de Aplicativo do Azure existente ao Banco de Dados do Azure para MySQL server
Este tópico explica como conectar um Serviço de Aplicativo do Azure existente ao Banco de Dados do Azure para o servidor MySQL.

## <a name="before-you-begin"></a>Antes de começar
Faça logon no [Portal do Azure](https://portal.azure.com). Criar um servidor de Banco de Dados do Azure para MySQL. Para obter detalhes, confira [Como criar o Banco de Dados do Azure para servidor MySQL por meio do Portal](quickstart-create-mysql-server-database-using-azure-portal.md) ou [Como criar o Banco de Dados do Azure para servidor MySQL usando a CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Atualmente, há duas soluções para habilitar o acesso de um Serviço de Aplicativo do Azure a um Banco de Dados do Azure para MySQL. Ambas as soluções envolvem a configuração de regras de firewall de nível de servidor.

## <a name="solution-1---create-a-firewall-rule-to-allow-all-ips"></a>Solução 1: Criar uma regra de firewall para permitir todos os IPs
O Banco de Dados do Azure para MySQL fornece segurança de acesso usando um firewall para proteger os dados. Ao se conectar de um Serviço de Aplicativo do Azure ao banco de dados para MySQL server, lembre-se de que os IPs de saída do Serviço de Aplicativo são dinâmicos por natureza. 

Para garantir a disponibilidade do Serviço de Aplicativo do Azure, recomendamos o uso dessa solução para permitir TODOS os IPs.

> [!NOTE]
> A Microsoft está trabalhando em uma solução de longo prazo para evitar permitir que todos os IPs para serviços do Azure se conectem ao Banco de Dados do Azure para MySQL.

1. Na folha do servidor MySQL, no título Configurações, clique em **Segurança de conexão** para abrir a folha Segurança de conexão para o Banco de Dados do Azure para MySQL.

   ![Portal do Azure - clique em Segurança de Conexão](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Digite o **NOME DA REGRA**, **IP INICIAL** e **IP FINAL** e, em seguida, clique em **Salvar**.
   - Nome da regra: Permitir-Todos os-IPs
   - IP inicial: 0.0.0.0
   - IP final: 255.255.255.255

   ![Portal do Azure – adicionar todos os IPs](./media/howto-connect-webapp/1_2-add-all-ips.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Solução 2: Criar uma regra de firewall para permitir explicitamente IPs de saída
Você pode adicionar explicitamente todos os IPs de saída do Serviço de Aplicativo do Azure.

1. Na folha Propriedades do Serviço de Aplicativo, exiba o **ENDEREÇO IP DE SAÍDA**.

   ![Portal do Azure – exibir IPs de saída](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. Na folha Segurança de Conexão do MySQL, adicione os IPs de saída individualmente.

   ![Portal do Azure – adicionar IPs explícitos](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Lembre-se de **Salvar** as regras de firewall.

Embora o serviço de aplicativo do Azure tente manter os endereços IP constantes ao longo do tempo, há casos em que os endereços IP podem mudar. Por exemplo, isso pode ocorrer quando o aplicativo é reciclado ou ocorre uma operação em escala, ou quando são adicionados novos computadores aos data centers regionais do Azure para aumentar a capacidade. Quando os endereços IP são alterados, o aplicativo pode apresentar tempo de inatividade, caso ele não possa mais se conectar ao servidor MySQL. Lembre-se disso ao escolher uma das soluções anteriores.

## <a name="ssl-configuration"></a>Configuração de SSL
O Banco de Dados do Azure para MySQL tem SSL habilitado por padrão. Se o aplicativo não estiver usando SSL para se conectar ao banco de dados, você precisará desabilitar o SSL no servidor MySQL. Para obter detalhes sobre como configurar o SSL, confira [Usar SSL com o Banco de Dados do Azure para MySQL](howto-configure-ssl.md).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre cadeias de conexão, confira [Cadeias de conexão](howto-connection-string.md).
