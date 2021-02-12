---
title: Configurar parâmetros do servidor-portal do Azure-banco de dados do Azure para MySQL servidor flexível
description: Este artigo descreve como configurar os parâmetros do MySQL Server no banco de dados do Azure para o servidor flexível do MySQL usando o portal do Azure.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 7733a6211363b4f1c9e9006f757b4d152c7af7f5
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489549"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Configurar parâmetros de servidor no banco de dados do Azure para MySQL-servidor flexível usando o portal do Azure

> [!IMPORTANT] 
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

Você pode gerenciar a configuração de servidor flexível do banco de dados do Azure para MySQL usando parâmetros de servidor. Os parâmetros de servidor são configurados com o valor padrão e recomendado quando você cria o servidor.  

Este artigo descreve como exibir e configurar parâmetros de servidor usando o portal do Azure. A folha parâmetro de servidor no portal do Azure mostra o parâmetro de servidor modificável e não modificável. Os parâmetros de servidor não modificáveis estão esmaecidos.

>[!Note]
> Os parâmetros do servidor podem ser atualizados globalmente no nível do servidor, use o [CLI do Azure](./how-to-configure-server-parameters-cli.md) ou [portal do Azure](./how-to-configure-server-parameters-portal.md).

## <a name="configure-server-parameters"></a>Configurar parâmetros do servidor

1. Entre na [portal do Azure](https://portal.azure.com)e, em seguida, localize o servidor flexível do banco de dados do Azure para MySQL.
2. Na seção **configurações** , clique em **parâmetros do servidor** para abrir a página parâmetros do servidor para o servidor flexível do banco de dados do Azure para MySQL.
[:::image type="content" source="./media/how-to-server-parameters/azure-portal-server-parameters.png" alt-text="Página de parâmetros do servidor do portal do Azure":::](./media/how-to-server-parameters/azure-portal-server-parameters.png#lightbox)
3. Localize qualquer parâmetro de servidor que você precise ajustar. Examine a coluna **Descrição** para entender a finalidade e os valores permitidos.
[:::image type="content" source="./media/how-to-server-parameters/3-toggle-parameter.png" alt-text="Enumerar lista suspensa":::](./media/how-to-server-parameters/3-toggle-parameter.png#lightbox)
4. Clique em  **salvar** para salvar as alterações.
[:::image type="content" source="./media/how-to-server-parameters/4-save-parameters.png" alt-text="Salvar ou descartar alterações":::](./media/how-to-server-parameters/4-save-parameters.png#lightbox)
5. Os parâmetros estáticos são aqueles que exigem que a reinicialização do servidor entre em vigor. Se você estiver modificando o parâmetro estático, será solicitado a **reiniciar agora** ou **reiniciar mais tarde**.
[:::image type="content" source="./media/how-to-server-parameters/5-save-parameter.png" alt-text="Reiniciar em salvamento de parâmetro estático":::](./media/how-to-server-parameters/5-save-parameter.png#lightbox)
6. Se você tiver salvo os novos valores para os parâmetros, você sempre pode reverter tudo o que fazer com os valores padrão selecionando **Redefinir tudo para o padrão**.
[:::image type="content" source="./media/how-to-server-parameters/6-reset-parameters.png" alt-text="Redefinir tudo para o padrão":::](./media/how-to-server-parameters/6-reset-parameters.png#lightbox)

## <a name="setting-non-modifiable-server-parameters"></a>Definindo parâmetros de servidor não modificáveis

Se o parâmetro de servidor que você deseja atualizar não for modificável, você poderá, opcionalmente, definir o parâmetro no nível de conexão usando `init_connect` . Isso define os parâmetros de servidor para cada cliente que se conecta ao servidor. 

1. Na seção **CONFIGURAÇÕES** , clique em **Parâmetros do servidor** para abrir a página de parâmetros do servidor para o servidor do Banco de Dados do Azure para MySQL.
2. Pesquisar por `init_connect`
3. Adicione os parâmetros de servidor no formato: `SET parameter_name=YOUR_DESIRED_VALUE` em valor, a coluna valor.

    Por exemplo, você pode alterar o conjunto de caracteres do seu servidor definindo `init_connect` como `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. Clique em **Salvar** para salvar as alterações.

>[!Note]
> `init_connect` pode ser usado para mudar parâmetros que não exigem privilégio(s) SUPER no nível da sessão. Para verificar se você pode definir o parâmetro usando `init_connect`, execute o comando `set session parameter_name=YOUR_DESIRED_VALUE;` e, se ele apresentar o erro **Acesso negado, você precisa de privilégios SUPER** , não será possível definir o parâmetro usando "init_connect".

## <a name="working-with-the-time-zone-parameter"></a>Trabalhar com o parâmetro de fuso horário

### <a name="populating-the-time-zone-tables"></a>Preencher as tabelas de fuso horário

As tabelas de fuso horário no servidor podem ser preenchidas, chamando o procedimento armazenado `mysql.az_load_timezone` de uma ferramenta como a linha de comando do MySQL ou Workbench do MySQL.

> [!NOTE]
> Se estiver executando o comando `mysql.az_load_timezone` do Workbench do MySQL, talvez seja necessário desativar primeiro o modo de atualização segura usando `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Você deve reiniciar o servidor para garantir que as tabelas de fuso horário sejam populadas corretamente.<!-- FIX ME To restart the server, use the [Azure portal](how-to-restart-server-portal.md) or [CLI](how-to-restart-server-cli.md).-->

Para exibir os valores de fuso horário disponíveis, execute o comando a seguir:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Configurar o fuso horário de nível global

O fuso horário de nível global pode ser configurado na página **Parâmetros do servidor** no portal do Azure. O exemplo abaixo configura o fuso horário global para o valor "EUA/Pacífico".

[:::image type="content" source="./media/how-to-server-parameters/timezone.png" alt-text="Definir o parâmetro de fuso horário":::](./media/how-to-server-parameters/timezone.png#lightbox)

### <a name="setting-the-session-level-time-zone"></a>Configurar o fuso horário do nível de sessão

O fuso horário do nível de sessão pode ser configurado, executando o comando `SET time_zone` a partir de uma ferramenta como a linha de comando do MySQL ou Workbench do MySQL. O exemplo abaixo configura o fuso horário para **EUA/Pacífico**.

```sql
SET time_zone = 'US/Pacific';
```

Consulte a documentação do MySQL para [Funções de data e hora](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Próximas etapas

- Como configurar [parâmetros de servidor no CLI do Azure](./how-to-configure-server-parameters-cli.md)
