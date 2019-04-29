---
title: Como configurar parâmetros do servidor no Banco de Dados do Azure para MySQL
description: Este artigo descreve como configurar os parâmetros do MySQL Server no Banco de Dados do Azure para MySQL usando o portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 103e09a0e2b9dd409fa2ddaff1c5311ef9936d22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61422077"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Como configurar parâmetros de servidor no Banco de Dados do Azure para MySQL usando o portal do Azure

O Banco de Dados do Azure para MySQL dá suporte à configuração de alguns parâmetros de servidor. Este artigo descreve como configurar esses parâmetros usando o portal do Azure. Nem todos os parâmetros de servidor podem ser ajustados.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navegue até Parâmetros de servidor no portal do Azure

1. Faça logon no portal do Azure e, em seguida, localize o seu Banco de Dados do Azure para MySQL Server.
2. Na seção **CONFIGURAÇÕES**, clique em **Parâmetros do servidor** para abrir a página de parâmetros do servidor para o servidor do Banco de Dados do Azure para MySQL.
![Página de parâmetros do servidor do Portal do Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Localize as configurações que você precisa ajustar. Examine a coluna **Descrição** para entender a finalidade e os valores permitidos.
![Enumerar a lista suspensa](./media/howto-server-parameters/3-toggle_parameter.png)
4. Clique em **Salvar** para salvar as alterações.
![Clique em salvar ou descartar mudanças](./media/howto-server-parameters/4-save_parameters.png)
5. Se você tiver salvo os novos valores para os parâmetros, você sempre pode reverter tudo o que fazer com os valores padrão selecionando **Redefinir tudo para o padrão**.
![Redefinir tudo para padrão](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista de parâmetros de servidor configuráveis

A lista de parâmetros de servidor com suporte está em constante crescimento. Use a guia de parâmetros de servidor no portal do Azure para obter a definição e configurar parâmetros de servidor com base em seus requisitos de aplicativo.

## <a name="non-configurable-server-parameters"></a>Parâmetros do servidor não configuráveis

Pool de buffers InnoDB e Máximo de Conexões não são configuráveis e são associados ao seu [tipo de preço](concepts-service-tiers.md).

|**Tipo de preço**| **Geração de computação**|**vCore(s)**|**Pool de Buffers InnoDB (MB)**| **Máximo de conexões**|
|---|---|---|---|--|
|Basic| Gen 4| 1| 960| 50|
|Basic| Gen 4| 2| 2560| 100|
|Basic| Gen 5| 1| 960| 50|
|Basic| Gen 5| 2| 2560| 100|
|Uso geral| Gen 4| 2| 3584| 300|
|Uso geral| Gen 4| 4| 7680| 625|
|Uso geral| Gen 4| 8| 15360| 1250|
|Uso geral| Gen 4| 16| 31232| 2500|
|Uso geral| Gen 4| 32| 62976| 5.000|
|Uso geral| Gen 5| 2| 3584| 300|
|Uso geral| Gen 5| 4| 7680| 625|
|Uso geral| Gen 5| 8| 15360| 1250|
|Uso geral| Gen 5| 16| 31232| 2500|
|Uso geral| Gen 5| 32| 62976| 5.000|
|Uso geral| Gen 5| 64| 125952| 10000|
|Otimizado para memória| Gen 5| 2| 7168| 600|
|Otimizado para memória| Gen 5| 4| 15360| 1250|
|Otimizado para memória| Gen 5| 8| 30720| 2500|
|Otimizado para memória| Gen 5| 16| 62464| 5.000|
|Otimizado para memória| Gen 5| 32| 125952| 10000|

Esses parâmetros de servidor adicionais não são configuráveis no sistema:

|**Parâmetro**|**Valor fixo**|
| :------------------------ | :-------- |
|innodb_file_per_table na camada Básica|DESATIVADO|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512MB|

Outros parâmetros de servidor que não estão listados aqui são configurados com seus valores padrão iniciais MySQL nas versões [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) e [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="working-with-the-time-zone-parameter"></a>Trabalhar com o parâmetro de fuso horário

### <a name="populating-the-time-zone-tables"></a>Preencher as tabelas de fuso horário

As tabelas de fuso horário no servidor podem ser preenchidas, chamando o procedimento armazenado `az_load_timezone` de uma ferramenta como a linha de comando do MySQL ou Workbench do MySQL.

> [!NOTE]
> Se estiver executando o comando `az_load_timezone` do Workbench do MySQL, talvez seja necessário desativar primeiro o modo de atualização segura usando `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Para exibir os valores de fuso horário disponíveis, execute o comando a seguir:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Configurar o fuso horário de nível global

O fuso horário de nível global pode ser configurado na página **Parâmetros do servidor** no portal do Azure. O exemplo abaixo configura o fuso horário global para o valor "EUA/Pacífico".

![Definir o parâmetro de fuso horário](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Configurar o fuso horário do nível de sessão

O fuso horário do nível de sessão pode ser configurado, executando o comando `SET time_zone` a partir de uma ferramenta como a linha de comando do MySQL ou Workbench do MySQL. O exemplo abaixo configura o fuso horário para **EUA/Pacífico**.

```sql
SET time_zone = 'US/Pacific';
```

Consulte a documentação do MySQL para [Funções de data e hora](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Próximas etapas

- [Bibliotecas de conexão para o Banco de Dados do Azure para MySQL](concepts-connection-libraries.md).
