---
title: Solucionar problemas de conexão-banco de dados do Azure para MySQL-servidor flexível
description: Saiba como solucionar problemas de conexão com o servidor flexível do banco de dados do Azure para MySQL.
keywords: conexão do MySQL, Cadeia de conexão, problemas de conectividade, erro persistente, erro de conexão
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: troubleshooting
ms.date: 9/21/2020
ms.openlocfilehash: b328da01cd3b2ecb3eb5f183296eab808de27399
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90932874"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql---flexible-server"></a>Solucionar problemas de conexão com o banco de dados do Azure para MySQL-servidor flexível

> [!IMPORTANT]
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

Os problemas de conexão podem ser causados por uma variedade de coisas, incluindo:

* Configurações de firewall
* Tempo limite da conexão
* Informações de logon incorretas
* Limite máximo atingido em alguns recursos de servidor flexíveis do banco de dados do Azure para MySQL

Neste artigo, discutiremos como você pode solucionar alguns dos erros comuns e as etapas para resolver esses erros.

## <a name="troubleshoot-common-errors"></a>Solucionar erros comuns

Se o aplicativo falhar de maneira persistente ao se conectar ao banco de dados do Azure para MySQL servidor flexível, ele geralmente indica um problema com um dos seguintes:

* Conexão criptografada usando TLS/SSL: o servidor flexível dá suporte apenas a conexões criptografadas usando TLS 1,2 (Transport Layer Security) e todas as **conexões de entrada com tls 1,0 e tls 1,1 serão negadas**. Não é possível desabilitar ou alterar a versão do TLS. Saiba mais sobre a [conectividade criptografada usando 1,2 TLS (segurança de camada de transporte) no banco de dados do Azure para MySQL-servidor flexível](./how-to-connect-tls-ssl.md).
- Servidor flexível no *acesso privado (integração VNet)*: Verifique se você está se conectando de dentro da mesma rede virtual que o servidor flexível. Consulte [rede virtual no banco de dados do Azure para MySQL servidor flexível]<!--(./concepts-networking-virtual-network.md)-->
- Servidor flexível com *acesso público (endereços IP permitidos)*, certifique-se de que o firewall esteja configurado para permitir conexões do seu cliente. Consulte [criar e gerenciar regras de firewall de servidor flexíveis usando o portal do Azure](./how-to-manage-firewall-portal.md).
* Configuração de firewall do cliente: o firewall em seu cliente deve permitir conexões com o servidor de banco de dados. Endereços IP e portas do servidor sem permissão devem ser permitidos, bem como nomes de aplicativo, como MySQL, em alguns firewalls.
* Erro do usuário: você pode ter parâmetros de conexão digitados, como o nome do servidor na cadeia de conexão.

### <a name="resolve-connectivity-issues"></a>Resolver problemas de conectividade

* Consulte [conectividade criptografada usando 1,2 TLS (segurança de camada de transporte) no banco de dados do Azure para MySQL – servidor flexível](./how-to-connect-tls-ssl.md) --> para saber mais sobre conexões criptografadas.
* Se você estiver usando **acesso público (endereços IP permitidos)**, configure [as regras de firewall](./how-to-manage-firewall-portal.md) para permitir o endereço IP do cliente. Para fins de testes temporários, configure uma regra de firewall usando 0.0.0.0 como o endereço IP inicial e usando 255.255.255.255 como o endereço IP final. Isso abrirá o servidor para todos os endereços IP. Se isso resolver seu problema de conectividade, remova essa regra e crie uma regra de firewall para um intervalo de endereçamento ou um endereço IP adequadamente limitado.
* Em todos os firewalls entre o cliente e a Internet, certifique-se de que a porta 3306 esteja aberta para conexões de saída.
* Verifique a cadeia de conexão e outras configurações de conexão. Consulte as cadeias de conexão predefinidas na página **cadeias de conexão** disponíveis para o servidor no portal do Azure para linguagens comuns.

## <a name="next-steps"></a>Próximas etapas
- [Use o MySQL Workbench para conectar e consultar dados no servidor flexível do Azure para MySQL](./connect-workbench.md).
- [Use o PHP para se conectar e consultar dados no servidor do Azure para MySQL flexível](./connect-php.md).
- [Use o Python para se conectar e consultar dados no servidor flexível do Azure para MySQL](./connect-python.md).
