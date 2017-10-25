---
title: "Solucionar problemas comuns de conexão no Banco de Dados SQL do Azure"
description: "Etapas para identificar e resolver erros comuns de conexão do Banco de Dados SQL do Azure."
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
ms.assetid: ac463d1c-aec8-443d-b66e-fa5eadcccfa8
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/13/2017
ms.author: daleche
ms.openlocfilehash: 2748768e88c046c39232492396872e1c7ed590a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Solucionar problemas de conexão no Banco de Dados SQL do Azure
Quando a conexão com o banco de dados SQL falha, você recebe [mensagens de erro](sql-database-develop-error-messages.md). Este artigo é um tópico centralizado que ajuda a solucionar problemas de conectividade do Banco de Dados SQL do Azure. Ele apresenta [as causas comuns](#cause) de problemas de conexão, recomenda [uma ferramenta de solução de problemas](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) que ajuda a identificar o problema e fornece etapas de solução de problemas para resolver [erros transitórios](#troubleshoot-transient-errors) e [erros persistentes ou não transitórios](#troubleshoot-persistent-errors). 

Se você encontrar problemas de conexão, experimente as etapas de solução de problemas descritas neste artigo.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Causa
Problemas de conexão podem ser causados por qualquer um dos seguintes fatores:

* Falha ao aplicar práticas recomendadas e diretrizes de design durante o processo de design do aplicativo.  Confira [Visão geral do desenvolvimento de Banco de Dados SQL](sql-database-develop-overview.md) para uma introdução.
* Reconfiguração do Banco de Dados SQL do Azure
* Configurações de firewall
* Tempo limite da conexão
* Informações de logon incorretas
* Limite máximo é atingido para alguns recursos do Banco de Dados SQL do Azure

Normalmente, problemas de conexão com o Banco de Dados SQL do Azure podem ser classificados da seguinte forma:

* [Erros transitórios (de curta duração ou intermitentes)](#troubleshoot-transient-errors)
* [Erros persistentes ou não transitórios (erros regularmente recorrentes)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Tente a solução de problemas de conectividade do Banco de Dados SQL
Se você se deparar com um erro de conexão específico, tente [esta ferramenta](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), que vai ajudá-lo a identificar e resolver rapidamente o problema.

## <a name="troubleshoot-transient-errors"></a>Solucionar problemas de erros transitórios

Quando um aplicativo se conecta a um banco de dados SQL do Azure, a seguinte mensagem de erro é exibida:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Essa mensagem de erro normalmente é transitória (de vida curta).
> 
> 

Esse erro ocorre quando o banco de dados do Azure está sendo movido (ou reconfigurados) e seu aplicativo perde a conexão com o banco de dados SQL. Eventos de reconfiguração do banco de dados SQL ocorrem devido a um evento planejado (por exemplo, um upgrade de software) ou um evento não planejado (por exemplo, uma falha no processo ou balanceamento de carga). A maioria dos eventos de reconfiguração geralmente é de curta duração e deve ser concluída em, no máximo, de 60 segundos. No entanto, esses eventos ocasionalmente podem levar mais tempo para serem concluídos, como quando uma transação grande causa uma recuperação de execução longa.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Etapas para resolver problemas de conectividade temporários

1. Confira o [Painel de Serviços do Microsoft Azure](https://azure.microsoft.com/status) quanto a quaisquer interrupções conhecidas que tenham ocorrido durante o tempo em que o erro foi relatado pelo aplicativo.
2. Aplicativos que se conectam a um serviço de nuvem, como Banco de Dados SQL, devem esperar eventos reconfiguração periódicos e implementar lógica de repetição para lidar com esses erros, em vez de exibir esses erros como erros de aplicativo aos usuários. Confira a seção [Erros transitórios](sql-database-connectivity-issues.md) e práticas recomendadas e diretrizes de design na [Visão geral de Desenvolvimento do Banco de Dados SQL](sql-database-develop-overview.md) para saber mais e ver estratégias de repetição gerais. Em seguida, confira os exemplos de código em [Bibliotecas de Conexões para Banco de Dados SQL e SQL Server](sql-database-libraries.md) para obter as especificações.
3. Conforme um banco de dados se aproxima dos limites de recursos, pode parecer que há um problema de conectividade temporário. Confira [Solução de problemas de desempenho](sql-database-troubleshoot-performance.md).
4. Se problemas de conectividade continuarem, se a duração pela qual o aplicativo encontra o erro exceder 60 segundos ou se você vir várias ocorrências do erro em um determinado dia, envie uma solicitação de suporte do Azure selecionando **Obter Suporte** no site de [Suporte do Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Solucionar erros persistentes
Se o aplicativo falhar persistentemente em se conectar ao Banco de Dados SQL do Azure, ele normalmente indicará um problema com um dos seguintes:

* Configuração do firewall. O firewall de banco de dados ou do lado do cliente do SQL Azure está bloqueando conexões do Banco de Dados SQL.
* Reconfiguração da rede no lado do cliente: por exemplo, um novo endereço IP ou um servidor proxy.
* Erro do usuário: por exemplo, parâmetros de conexão digitados incorretamente, como o nome do servidor na cadeia de conexão.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Etapas para resolver os problemas de conectividade temporários
1. Configure as [regras de firewall](sql-database-configure-firewall-settings.md) para permitir o endereço IP do cliente. Para fins de teste temporárias, configure uma regra de firewall usando 0.0.0.0 como o intervalo de endereços IP inicial e usando 255.255.255.255 como o intervalo de endereço IP final. Isso abrirá o servidor para todos os endereços IP. Se isso resolver seu problema de conectividade, remova essa regra e crie uma regra de firewall para um intervalo de endereçamento ou um endereço IP adequadamente limitado. 
2. Em todos os firewalls entre o cliente e a Internet, certifique-se de que a porta 1433 está aberta para conexões de saída. Revisão [configurar o Firewall do Windows para permitir acesso ao SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) e [híbrida identidade necessárias portas e protocolos](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) ponteiros adicionais relacionadas a portas adicionais que você precisa abrir para autenticação do Azure Active Directory.
3. Verifique a cadeia de conexão e outras configurações de conexão. Confira a seção Cadeia de Conexão do [tópico sobre problemas de conectividade](sql-database-connectivity-issues.md#connections-to-azure-sql-database).
4. Verifique a integridade do serviço no painel. Se você achar que há uma interrupção regional, confira [Recuperar-se de uma paralisação](sql-database-disaster-recovery.md) para obter as etapas de recuperação de uma nova região.

## <a name="next-steps"></a>Próximas etapas
* [Solucionar problemas de desempenho no Banco de Dados SQL do Azure](sql-database-troubleshoot-performance.md)
* [Pesquisar a documentação do Microsoft Azure](http://azure.microsoft.com/search/documentation/)
* [Exibir as atualizações mais recentes ao serviço do Banco de Dados SQL do Azure](http://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Recursos adicionais
* [Visão geral do desenvolvimento de Banco de Dados SQL](sql-database-develop-overview.md)
* [Diretrizes gerais para tratamento de falhas transitórias](../best-practices-retry-general.md)
* [Bibliotecas de conexão para Banco de Dados SQL e SQL Server](sql-database-libraries.md)

