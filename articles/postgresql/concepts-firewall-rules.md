---
title: Regras de firewall-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como usar regras de firewall para se conectar ao banco de dados do Azure para PostgreSQL-servidor único.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: ba353cf41cf3876a681f8f18d4121401260ff4ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98877163"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Regras de firewall no banco de dados do Azure para PostgreSQL-servidor único
O banco de dados do Azure para servidor PostgreSQL é seguro por padrão, impedindo todo acesso ao servidor de banco de dados até que você especifique quais hosts de IP têm permissão para acessá-lo. O firewall concede acesso ao servidor com base no endereço IP de origem de cada solicitação.
Para configurar seu firewall, você deve criar regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Você pode criar regras de firewall no nível de servidor.

**Regras de firewall:** essas regras permitem que os clientes acessem todo o Banco de Dados do Azure para servidor PostgreSQL, ou seja, todos os bancos dentro do mesmo servidor lógico. As regras de firewall no nível de servidor podem ser configuradas por meio do Portal do Azure ou usando comandos da CLI do Azure. Para criar regras de firewall no nível de servidor, você deve ser o proprietário da assinatura ou um colaborador da assinatura.

## <a name="firewall-overview"></a>Visão geral do firewall
Todo o acesso ao banco de dados do Azure para servidor PostgreSQL é bloqueado por padrão pelo firewall. Para acessar o servidor de outro computador/cliente ou aplicativo, você precisa especificar uma ou mais regras de firewall no nível de servidor para habilitar o acesso ao servidor. Use as regras de firewall para especificar intervalos de endereços IP públicos permitidos. O acesso em si ao site do Portal do Azure não é afetado pelas regras de firewall.
As tentativas de conexão da Internet e do Azure devem passar pela primeira vez pelo firewall antes que possam acessar seu banco de dados PostgreSQL, conforme mostrado no diagrama a seguir:

:::image type="content" source="media/concepts-firewall-rules/1-firewall-concept.png" alt-text="Fluxo de exemplo de como funciona o firewall":::

## <a name="connecting-from-the-internet"></a>Conectando pela Internet
As regras de firewall no nível do servidor se aplicam a todos os bancos de dados no mesmo servidor do Banco de Dados do Azure para PostgreSQL. Se o endereço IP de origem da solicitação estiver dentro de um dos intervalos especificados nas regras de firewall no nível de servidor, a conexão será concedida, caso contrário, será rejeitada. Por exemplo, se o seu aplicativo se conectar ao driver JDBC para PostgreSQL, você poderá encontrar esse erro ao tentar se conectar quando o firewall estiver bloqueando a conexão.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="connecting-from-azure"></a>Conexão pelo Azure
É recomendável que você localize o endereço IP de saída de qualquer aplicativo ou serviço e permita explicitamente o acesso a esses endereços IP ou intervalos individuais. Por exemplo, você pode encontrar o endereço IP de saída de um serviço de Azure App ou usar um IP público vinculado a uma máquina virtual ou outro recurso (consulte abaixo para obter informações sobre como se conectar com o IP privado de uma máquina virtual nos pontos de extremidade de serviço). 

Se um endereço IP de saída fixo não estiver disponível para seu serviço do Azure, você poderá considerar a possibilidade de habilitar conexões de todos os endereços IP do datacenter do Azure. Essa configuração pode ser habilitada no portal do Azure definindo a opção **permitir acesso aos serviços do Azure** como **ativado** no painel **segurança de conexão** e ao pressionar **salvar**. No CLI do Azure, uma configuração de regra de firewall com endereço inicial e final igual a 0.0.0.0 faz o equivalente. Se a tentativa de conexão for rejeitada por regras de firewall, ela não alcançará o servidor de banco de dados do Azure para PostgreSQL.

> [!IMPORTANT]
> A opção **permitir acesso aos serviços do Azure** configura o firewall para permitir todas as conexões do Azure, incluindo conexões das assinaturas de outros clientes. Ao selecionar essa opção, verifique se as permissões de logon e de usuário limitam o acesso somente a usuários autorizados.
> 

:::image type="content" source="media/concepts-firewall-rules/allow-azure-services.png" alt-text="Configurar Permitir o acesso aos serviços do Azure":::

## <a name="connecting-from-a-vnet"></a>Conectando de uma VNet
Para se conectar com segurança ao banco de dados do Azure para servidor PostgreSQL de uma VNet, considere o uso de [pontos de extremidade de serviço de VNet](./concepts-data-access-and-security-vnet.md). 

## <a name="programmatically-managing-firewall-rules"></a>Gerenciando programaticamente as regras de firewall
Além do Portal do Azure, as regras de firewall podem ser gerenciadas por meio de programação usando a CLI do Azure.
Confira também [Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Solucionando problemas de firewall
Considere os seguintes pontos quando o acesso ao Banco de Dados do Microsoft Azure para o serviço de servidor PostgreSQL não se comportar conforme o esperado:

* **As alterações à lista de permissões ainda não entraram em vigor:** pode ocorrer um atraso de cinco minutos para que as alterações na configuração do firewall do Banco de Dados do Azure para servidor PostgreSQL entrem em vigor.

* **O logon não está autorizado ou uma senha incorreta foi usada:** se um logon não tiver permissões no servidor do Banco de Dados do Azure para servidor PostgreSQL, ou se a senha usada estiver incorreta, a conexão com o Banco de Dados do Azure para servidor PostgreSQL será negada. Criar uma configuração de firewall apenas oferece aos clientes uma oportunidade de tentar se conectar ao servidor; cada cliente ainda deverá fornecer as credenciais de segurança necessárias.

   Por exemplo, usando um cliente JDBC, o seguinte erro pode aparecer.
   > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: falha na autenticação da senha para o usuário "seunomedeusuário"

* **Endereço IP dinâmico:** se você tiver uma conexão com a Internet com endereçamento IP dinâmico e estiver com dificuldades para atravessar o firewall, tente uma das seguintes soluções:

   * Peça ao seu Provedor de serviços de Internet (ISP) o intervalo de endereços IP atribuído aos computadores clientes que acessarão o servidor de Banco de Dados do Azure para servidor PostgreSQL e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.

   * Obtenha o endereçamento IP estático para os computadores cliente e adicione os endereços IP estáticos como uma regra de firewall.

* O **IP do servidor parece ser público:** As conexões com o banco de dados do Azure para servidor PostgreSQL são roteadas por meio de um gateway do Azure acessível publicamente. No entanto, o IP do servidor real é protegido pelo firewall. Para obter mais informações, consulte o [artigo sobre arquitetura de conectividade](concepts-connectivity-architecture.md).

* **Não é possível se conectar do recurso do Azure com o IP permitido:** Verifique se o ponto de extremidade de serviço **Microsoft. SQL** está habilitado para a sub-rede da qual você está se conectando. Se **o Microsoft. SQL** estiver habilitado, ele indicará que você só deseja usar [regras de ponto de extremidade de serviço de VNet](concepts-data-access-and-security-vnet.md) nessa sub-rede.

   Por exemplo, você poderá ver o erro a seguir se estiver se conectando de uma VM do Azure em uma sub-rede que tem **o Microsoft. SQL** habilitado, mas não tem nenhuma regra de VNet correspondente:  `FATAL: Client from Azure Virtual Networks is not allowed to access the server`

* **A regra de firewall não está disponível para o formato IPv6:** As regras de firewall devem estar no formato IPv4. Se você especificar regras de firewall no formato IPv6, ele mostrará o erro de validação.


## <a name="next-steps"></a>Próximas etapas
* [Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando o Portal do Azure](howto-manage-firewall-using-portal.md)
* [Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure](howto-manage-firewall-using-cli.md)
* [Pontos de extremidade de serviço de VNet no banco de dados do Azure para PostgreSQL](./concepts-data-access-and-security-vnet.md)
