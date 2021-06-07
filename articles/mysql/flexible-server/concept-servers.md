---
title: Conceitos de servidor-banco de dados do Azure para MySQL servidor flexível
description: Este tópico fornece considerações e diretrizes para trabalhar com o servidor flexível do banco de dados do Azure para MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: a7978410dbe28a5da5dae81cb380d118fe13a159
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869371"
---
# <a name="server-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Conceitos de servidor no banco de dados do Azure para MySQL servidor flexível (versão prévia)

> [!IMPORTANT] 
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

Este artigo fornece considerações e diretrizes para trabalhar com servidores flexíveis do banco de dados do Azure para MySQL.

## <a name="what-is-an-azure-database-for-mysql-flexible-server"></a>O que é um servidor flexível do banco de dados do Azure para MySQL?

O banco de dados do Azure para MySQL servidor flexível é um serviço de banco de dados totalmente gerenciado executando a versão da Comunidade do MySQL. Em geral, o serviço foi projetado para fornecer flexibilidade e personalizações de configuração com base nos requisitos do usuário. É a mesma construção de servidor MySQL com a qual talvez você já esteja familiarizado no mundo local. Especificamente, o servidor flexível é gerenciado, fornece desempenho pronto para uso, melhor capacidade de gerenciamento e controle do servidor e expõe o acesso e os recursos no nível do servidor.

Um servidor flexível do banco de dados do Azure para MySQL:

- É criado dentro de uma assinatura do Azure.
- É o recurso pai para bancos de dados.
- Permite a configuração do MySQL exposta por meio de parâmetros do servidor (link para conceitos de parâmetro do servidor).
- Executa backups automatizados e dá suporte a restaurações pontuais.
- Fornece um namespace para bancos de dados.
- É um contêiner com semântica de tempo de vida fortes – exclua um servidor e ele excluirá os bancos de dados contidos.
- Coloca recursos em uma região.
- Suporte para agendamento de manutenção do servidor fornecido pelo cliente
- Capacidade de implantar servidores flexíveis em uma configuração com redundância de zona para melhorar a alta disponibilidade
- Fornece uma integração de rede virtual para o acesso ao servidor de banco de dados
- Fornece uma maneira de economizar custos pausando o servidor flexível quando não estiver em uso
- Fornece o escopo para políticas de gerenciamento que se aplicam a seus bancos de dados: logons, firewall, usuários, funções, configurações etc.
- Dá suporte à versão principal MySQL 5,7 e MySQL 8,0. Para obter mais informações, consulte [versões do mecanismo do banco de dados do Azure para MySQL com suporte](./../concepts-supported-versions.md).

Em um servidor flexível do banco de dados do Azure para MySQL, você pode criar um ou vários bancos. É possível optar por criar um único banco de dados por servidor para utilizar todos os recursos ou criar vários bancos de dados para compartilhar os recursos. O preço é estruturado por servidor, com base na configuração da camada de computação, vCores e armazenamento (GB). Para obter mais informações, consulte [computação e armazenamento](./concepts-compute-storage.md).

## <a name="stopstart-an-azure-database-for-mysql-flexible-server"></a>Parar/iniciar um servidor flexível do banco de dados do Azure para MySQL

O banco de dados do Azure para MySQL servidor flexível oferece a capacidade de **parar** o servidor quando ele não estiver em uso e **Iniciar** o servidor quando você retomar a atividade. Isso é essencialmente feito para economizar custos nos servidores de banco de dados e pagar apenas pelo recurso quando estiver em uso. Isso se torna ainda mais importante para cargas de trabalho de desenvolvimento e teste e quando você está usando o servidor apenas para parte do dia. Quando você parar o servidor, todas as conexões ativas serão descartadas. Posteriormente, quando você quiser colocar o servidor online novamente, poderá usar o [portal do Azure](how-to-stop-start-server-portal.md) ou a CLI.

Quando o servidor estiver no estado **parado** , a computação do servidor não será cobrada. No entanto, o armazenamento continua a ser cobrado, pois o armazenamento do servidor permanece para garantir que os arquivos de dados estejam disponíveis quando o servidor for iniciado novamente.

> [!IMPORTANT]
> Quando você **interrompe** o servidor, ele permanece nesse estado para os próximos 7 dias em um Stretch. Se você não **iniciá** -lo manualmente durante esse tempo, o servidor será iniciado automaticamente no final de 7 dias. Você pode optar por **interrompê** -lo novamente se não estiver usando o servidor.

Durante a interrupção do servidor de horário, nenhuma operação de gerenciamento pode ser executada no servidor. Para alterar as definições de configuração no servidor, você precisará [iniciar o servidor](how-to-stop-start-server-portal.md). Consulte as [limitações de parar/iniciar](./concepts-limitations.md#stopstart-operation).

## <a name="how-do-i-manage-a-server"></a>Como posso gerenciar um servidor?

Você pode gerenciar a criação, a exclusão, a configuração de parâmetro do servidor (My. cnf), o dimensionamento, a rede, a segurança, a alta disponibilidade, o backup & restauração, o monitoramento de seu banco de dados do Azure para o servidor flexível do MySQL usando o [portal do Azure](./quickstart-create-server-portal.md) ou o [CLI do Azure](./quickstart-create-server-cli.md). Além disso, os procedimentos armazenados a seguir estão disponíveis no banco de dados do Azure para MySQL para executar determinadas tarefas de administração de banco de dados necessárias, pois não há suporte para o privilégio de superusuário no servidor.

|**Nome do procedimento armazenado**|**Parâmetros de Entrada**|**Parâmetros de saída**|**Observação de uso**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|N/D|Equivalente a [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Command. Encerrará a conexão associada ao processlist_id fornecido depois de encerrar qualquer instrução que a conexão estiver executando.|
|*mysql.az_kill_query*|processlist_id|N/D|Equivalente a [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Command. Encerrará a instrução que a conexão está executando no momento. Deixa a própria conexão ativa.|
|*mysql.az_load_timezone*|N/D|N/D|Carrega [tabelas de fuso horário](../howto-server-parameters.md#working-with-the-time-zone-parameter) para permitir que o `time_zone` parâmetro seja definido para valores nomeados (ex. "EUA/Pacífico").|


## <a name="next-steps"></a>Próximas etapas

-   Saiba mais sobre [criar servidor](./quickstart-create-server-portal.md)
-   Saiba mais sobre [monitoramento e alertas](./how-to-alert-on-metric.md)

