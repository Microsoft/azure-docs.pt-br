---
title: Regras de firewall do Banco de Dados do Azure para servidor MySQL
description: Descreve as regras de firewall para seu Banco de Dados do Azure para servidor MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 0802185b7fb0d1a6d7d41cd1fa5a30f5ce10424b
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443919"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Regras de firewall do Banco de Dados do Azure para servidor MySQL
Os firewalls impedem todo acesso ao seu servidor de banco de dados até que você especifique quais computadores têm permissão. O firewall concede acesso ao servidor com base no endereço IP de origem de cada solicitação.

Para configurar seu firewall, crie regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Você pode criar regras de firewall no nível de servidor.

**Regras de firewall:** Essas regras permitem que os clientes acessem todo o servidor do Banco de Dados do Azure para MySQL, ou seja, todos os bancos de dados no mesmo servidor lógico. As regras de firewall no nível de servidor podem ser configuradas por meio do Portal do Azure ou dos comandos da CLI do Azure. Para criar regras de firewall no nível de servidor, você deve ser o proprietário da assinatura ou um colaborador da assinatura.

## <a name="firewall-overview"></a>Visão geral do firewall
Todo acesso de banco de dados ao seu servidor de Banco de Dados do Azure para MySQL é bloqueado por padrão pelo firewall. Para começar a usar o servidor de outro computador, especifique uma ou mais regras de firewall no nível do servidor para permitir o acesso ao seu servidor. Use as regras de firewall para especificar quais intervalos de endereços IP da Internet permitir. O acesso em si ao site do Portal do Azure não é afetado pelas regras de firewall.

As tentativas de conexão da Internet e do Azure devem passar primeiramente pelo firewall antes de poderem acessar seu Banco de Dados do Azure para banco de dados MySQL, conforme exibido no diagrama a seguir:

![Fluxo de exemplo de como funciona o firewall](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Conectando pela Internet
Regras de firewall de nível de servidor se aplicam a todos os bancos de dados no Banco de Dados do Azure para servidor MySQL.

Se o endereço IP da solicitação estiver dentro de um dos intervalos especificados nas regras de firewall no nível do servidor, a conexão será concedida.

Se o endereço IP da solicitação não estiver dentro dos intervalos especificados em uma das regras de firewall no nível de banco de dados ou de servidor, a solicitação de conexão falhará.

## <a name="connecting-from-azure"></a>Conexão pelo Azure
Para permitir que aplicativos do Azure se conectem ao seu Banco de Dados do Azure para servidor MySQL, as conexões do Azure deverão estar habilitadas. Por exemplo, para hospedar um aplicativo dos Aplicativos Web do Azure, um aplicativo executando em uma VM do Azure ou se conectar a partir de um gateway de gerenciamento de dados do Azure Data Factory. Os recursos não precisam estar na mesma Rede Virtual (VNet) ou Grupo de Recursos para a regra de firewall habilitar essas conexões. Quando um aplicativo do Azure tenta se conectar ao seu servidor de banco de dados, o firewall verifica se há permissão para conexões do Azure. Há alguns métodos para habilitar esses tipos de conexões. Uma configuração de firewall com endereço inicial e final igual a 0.0.0.0 indica que essas conexões são permitidas. Como alternativa, é possível configurar a opção **Permitir o Acesso aos Serviços do Azure** para **ON** no portal no painel **Segurança da Conexão** e clicar em **Salvar**. Se a tentativa de conexão não for permitida, a solicitação não alcançará o Banco de Dados do Azure para servidor MySQL.

> [!IMPORTANT]
> Esta opção configura o firewall para permitir todas as conexões do Azure, incluindo as conexões das assinaturas de outros clientes. Ao selecionar essa opção, verifique se as permissões de logon e de usuário limitam o acesso somente a usuários autorizados.
> 

![Configurar Permitir o acesso aos serviços do Azure](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Gerenciando programaticamente as regras de firewall
Além do Portal do Azure, as regras de firewall podem ser gerenciadas por meio de programação usando a CLI do Azure. Confira também [Criar e gerenciar regras de firewall do Banco de Dados do Azure para MySQL usando a CLI do Azure](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-firewall"></a>Solucionando problemas do firewall de banco de dados
Considere os seguintes pontos quando o acesso ao serviço do servidor de Banco de Dados do Microsoft Azure para MySQL não se comportar conforme o esperado:

* **As alterações à lista de permissões ainda não entraram em vigor ainda:** Pode haver um atraso de até cinco minutos para que as alterações na configuração do firewall do Banco de Dados do Azure para MySQL Server entrem em vigor.

* **O logon não está autorizado ou uma senha incorreta foi usada:** Se um logon não tiver permissões no servidor do Banco de Dados do Azure para MySQL ou se a senha usada estiver incorreta, a conexão com o Banco de Dados do Azure para o servidor MySQL será negada. Criar uma configuração de firewall apenas oferece aos clientes uma oportunidade de tentar se conectar ao servidor; cada cliente deve fornecer as credenciais de segurança necessárias.

* **Endereço IP dinâmico:** Se você tiver uma conexão com a Internet com endereçamento IP dinâmico e estiver tendo problemas para acessar o firewall, tente uma das seguintes soluções:

* Peça ao seu Provedor de serviços de Internet (ISP) o intervalo de endereços IP atribuído aos computadores clientes que acessarão o servidor de Banco de Dados do Azure para servidor MySQL e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.

* Obtenha o endereçamento IP estático para os computadores cliente e, em seguida, adicione os endereços IP como regras de firewall.

## <a name="next-steps"></a>Próximas etapas

* [Criar e gerenciar regras de firewall do Banco de Dados do Azure para MySQL usando o Portal do Azure](./howto-manage-firewall-using-portal.md)
* [Criar e gerenciar regras de firewall do banco de dados do Azure para MySQL usando o CLI do Azure](./howto-manage-firewall-using-cli.md)
