---
title: Regras de firewall do Banco de Dados do Azure para servidor PostgreSQL
description: Este artigo descreve as regras de firewall do Banco de Dados do Azure para servidor PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 793a68ee829d87a8433d5bdd77fe7b43e2f3af19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560169"
---
# <a name="azure-database-for-postgresql-server-firewall-rules"></a>Regras de firewall do Banco de Dados do Azure para servidor PostgreSQL
O firewall do servidor do Banco de Dados do Azure para PostgreSQL bloqueia todo o acesso ao seu servidor de banco de dados até você especificar quais computadores têm permissão. O firewall concede acesso ao servidor com base no endereço IP de origem de cada solicitação.
Para configurar seu firewall, você deve criar regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Você pode criar regras de firewall no nível de servidor.

**Regras de firewall:** Essas regras permitem que os clientes acessem todo o servidor do Banco de Dados do Azure para PostgreSQL, ou seja, todos os bancos dentro do mesmo servidor lógico. As regras de firewall no nível de servidor podem ser configuradas por meio do Portal do Azure ou usando comandos da CLI do Azure. Para criar regras de firewall no nível de servidor, você deve ser o proprietário da assinatura ou um colaborador da assinatura.

## <a name="firewall-overview"></a>Visão geral do firewall
Todo acesso de banco de dados ao seu Banco de Dados do Azure para servidor PostgreSQL é bloqueado por padrão pelo firewall. Para começar a usar o servidor de outro computador, especifique uma ou mais regras de firewall no nível do servidor para permitir o acesso ao seu servidor. Use as regras de firewall para especificar quais intervalos de endereços IP da Internet permitir. O acesso em si ao site do Portal do Azure não é afetado pelas regras de firewall.
As tentativas de conexão da Internet e do Azure devem passar primeiramente pelo firewall antes de poderem acessar seu Banco de Dados PostgreSQL, conforme exibido no diagrama a seguir:

![Fluxo de exemplo de como funciona o firewall](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Conectando pela Internet
As regras de firewall no nível do servidor se aplicam a todos os bancos de dados no mesmo servidor do Banco de Dados do Azure para PostgreSQL. Se o endereço IP da solicitação estiver dentro de um dos intervalos especificados nas regras de firewall no nível do servidor, a conexão será concedida.
Se o endereço IP da solicitação não estiver dentro dos intervalos especificados em uma das regras de firewall no nível do servidor, a solicitação de conexão falhará.
Por exemplo, se o seu aplicativo se conectar ao driver JDBC para PostgreSQL, você poderá encontrar esse erro ao tentar se conectar quando o firewall estiver bloqueando a conexão.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="connecting-from-azure"></a>Conexão pelo Azure
Para permitir que aplicativos do Azure se conectem ao seu servidor do Banco de Dados do Azure para PostgreSQL, as conexões do Azure deverão estar habilitadas. Por exemplo, para hospedar um aplicativo dos Aplicativos Web do Azure, um aplicativo executando em uma VM do Azure ou se conectar a partir de um gateway de gerenciamento de dados do Azure Data Factory. Os recursos não precisam estar na mesma Rede Virtual (VNet) ou Grupo de Recursos para a regra de firewall habilitar essas conexões. Quando um aplicativo do Azure tenta se conectar ao seu servidor de banco de dados, o firewall verifica se há permissão para conexões do Azure. Há alguns métodos para habilitar esses tipos de conexões. Uma configuração de firewall com endereço inicial e final igual a 0.0.0.0 indica que essas conexões são permitidas. Como alternativa, é possível configurar a opção **Permitir o Acesso aos Serviços do Azure** como **ON** no portal no painel **Segurança da Conexão** e clicar em **Salvar**. Se a tentativa de conexão não for permitida, a solicitação não alcançará o servidor do Banco de Dados do Azure para PostgreSQL.

> [!IMPORTANT]
> Esta opção configura o firewall para permitir todas as conexões do Azure, incluindo as conexões das assinaturas de outros clientes. Ao selecionar essa opção, verifique se as permissões de logon e de usuário limitam o acesso somente a usuários autorizados.
> 

![Configurar Permitir o acesso aos serviços do Azure](media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Gerenciando programaticamente as regras de firewall
Além do Portal do Azure, as regras de firewall podem ser gerenciadas por meio de programação usando a CLI do Azure.
Confira também [Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-server-firewall"></a>Solução de problemas do firewall do servidor de banco de dados
Considere os seguintes pontos quando o acesso ao Banco de Dados do Microsoft Azure para o serviço de servidor PostgreSQL não se comportar conforme o esperado:

* **As alterações à lista de permissões ainda não entraram em vigor ainda:** Pode ocorrer um atraso de cinco minutos para que as alterações na configuração do firewall do Banco de Dados do Azure para PostgreSQL entrem em vigor.

* **O logon não está autorizado ou uma senha incorreta foi usada:** Se um logon não tiver permissões no servidor do Banco de Dados do Azure para PostgreSQL, ou se a senha usada estiver incorreta, a conexão com o Banco de Dados do Azure para servidor PostgreSQL será negada. Criar uma configuração de firewall apenas oferece aos clientes uma oportunidade de tentar se conectar ao servidor; cada cliente ainda deverá fornecer as credenciais de segurança necessárias.

Por exemplo, usando um cliente JDBC, o seguinte erro pode aparecer.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: falha de autenticação de senha do usuário "seunomedeusuário"

* **Endereço IP dinâmico:** Se você tiver uma conexão com a Internet com endereçamento IP dinâmico e estiver com dificuldades para atravessar o firewall, tente uma das seguintes soluções:

* Peça ao seu Provedor de serviços de Internet (ISP) o intervalo de endereços IP atribuído aos computadores clientes que acessarão o servidor de Banco de Dados do Azure para servidor PostgreSQL e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.

* Obtenha o endereçamento IP estático para os computadores cliente e adicione os endereços IP estáticos como uma regra de firewall.

## <a name="next-steps"></a>Próximas etapas
Para ver artigos sobre como criar regras de firewall no nível de servidor e de banco de dados, consulte:
* [Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando o Portal do Azure](howto-manage-firewall-using-portal.md)
* [Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure](howto-manage-firewall-using-cli.md)
