---
title: Implantar o Azure SQL Edge usando o portal do Azure
description: Saiba como implantar o Azure SQL Edge usando o portal do Azure
keywords: implantar o SQL no Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 5d7d232ada814d5d3c30e7b012c6289f847d641f
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395080"
---
# <a name="deploy-azure-sql-edge"></a>Implantar o SQL do Azure no Edge 

O SQL do Azure no Edge é um mecanismo de banco de dados relacional otimizado para implantações de IoT e do Azure IoT Edge. Ele oferece funcionalidades para a criação de uma camada de processamento e armazenamento de dados de alto desempenho para aplicativos e soluções de IoT. Este início rápido mostra como começar a criar um módulo do SQL do Azure no Edge por meio do Azure IoT Edge usando o portal do Azure.

## <a name="before-you-begin"></a>Antes de começar

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).
* Entre no [portal do Azure](https://portal.azure.com/).
* Crie um [Hub IoT do Azure](../iot-hub/iot-hub-create-through-portal.md).
* Criar um [dispositivo Azure IOT Edge](../iot-edge/how-to-install-iot-edge.md).

> [!NOTE]   
> Para implantar uma VM do Linux no Azure como um dispositivo IoT Edge, confira este [guia de início rápido](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>Implantar o módulo do SQL no Edge por meio do Azure Marketplace

O Azure Marketplace é um marketplace online de aplicativos e serviços onde você pode procurar por uma ampla variedade de aplicativos e soluções corporativas que são certificadas e otimizadas para execução no Azure, incluindo [módulos do IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). O SQL do Azure no Edge pode ser implantado em um dispositivo de borda por meio do marketplace.

1. Encontre o módulo do SQL do Azure no Edge no Azure Marketplace.<br><br>

   ![SQL no Edge no Marketplace](media/deploy-portal/find-offer-marketplace.png)

2. Escolha o plano de software mais adequado às suas necessidades e clique em **Criar**. <br><br>

   ![Escolha o plano de software correto](media/deploy-portal/pick-correct-plan.png)

3. Na página Dispositivos de Destino para o Módulo do IoT Edge, especifique os detalhes a seguir e clique em **Criar**

   |**Campo**  |**Descrição**  |
   |---------|---------|
   |Subscription  |  A assinatura do Azure na qual o Hub IoT foi criado |
   |Hub IoT   |  Nome do Hub IoT em que o dispositivo IoT Edge está registrado e selecione a opção "Implantar em um dispositivo"|
   |Nome do Dispositivo IoT Edge  |  Nome do dispositivo IoT Edge no qual o SQL no Edge será implantado |

4. Na página **definir módulos no dispositivo:** , clique no módulo do Azure SQL Edge em **módulos IOT Edge**. O nome do módulo padrão é definido como *AzureSQLEdge*. 

5. Na seção *configurações do módulo* da folha **Atualizar IOT Edge módulo** , especifique os valores desejados para *o nome do módulo IOT Edge* , *reinicie a política* e o *status desejado*. 

   > [!IMPORTANT]    
   > Não altere nem atualize as configurações de **URI de imagem** no módulo.

6. Na seção *variáveis de ambiente* da folha do **módulo atualizar IOT Edge** , especifique os valores desejados para as variáveis de ambiente. Para obter uma lista completa de variáveis de ambiente do Azure SQL Edge, consulte [Configurar usando variáveis de ambiente](configure.md#configure-by-using-environment-variables). As variáveis de ambiente padrão a seguir são definidas para o módulo. 

   |**Parâmetro**  |**Descrição**|
   |---------|---------|
   | MSSQL_SA_PASSWORD  | Altere o valor padrão para especificar uma senha forte para a conta de administrador do SQL Edge. |
   | MSSQL_LCID   | Altere o valor padrão para definir a ID de idioma desejada a ser usada para o SQL Edge. Por exemplo, 1036 é francês. |
   | MSSQL_COLLATION | Altere o valor padrão para definir o agrupamento padrão para o SQL Edge. Essa configuração substitui o mapeamento padrão do LCID (ID de idioma) na ordenação. |

   > [!IMPORTANT]    
   > Não altere nem atualize a variável de ambiente **ACCEPT_EULA** para o módulo.

7. Na seção *Opções de criação do contêiner* da folha **Atualizar IOT Edge módulo** , atualize as seguintes opções de acordo com o requisito. 
   - **Porta do host:** Mapeie a porta de host especificada para a porta 1433 (porta SQL padrão) no contêiner.
   - **Associações** e **montagens:** se você precisar implantar mais de um módulo do SQL Edge, certifique-se de atualizar a opção montagens para criar um novo par de origem & destino para o volume persistente. Para obter mais informações sobre montagens e volume, veja [Usar volumes](https://docs.docker.com/storage/volumes/) na documentação do Docker. 

   ```json
   {
    "HostConfig": {
        "CapAdd": [
            "SYS_PTRACE"
        ],
        "Binds": [
            "sqlvolume:/sqlvolume"
        ],
        "PortBindings": {
            "1433/tcp": [
                {
                    "HostPort": "1433"
                }
            ]
        },
        "Mounts": [
            {
                "Type": "volume",
                "Source": "sqlvolume",
                "Target": "/var/opt/mssql"
            }
        ]
    },
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "PlanId=asde-developer-on-iot-edge"
    ]
   }
   ```
   > [!IMPORTANT]    
   > Não altere a `PlanId` variável de ambiente definida na configuração de criação. Se esse valor for alterado, o contêiner do Azure SQL Edge não será iniciado. 
   
8. No painel **atualizar IOT Edge módulo** , clique em **Atualizar**.
9. Na página **definir módulos no dispositivo** , clique em **avançar: rotas >** se você precisar definir rotas para sua implantação. Caso contrário, clique em **revisar + criar**. Para obter mais informações sobre como configurar rotas, consulte [implantar módulos e estabelecer rotas em IOT Edge](../iot-edge/module-composition.md).
11. Na página **definir módulos no dispositivo** , clique em **criar**.

## <a name="connect-to-azure-sql-edge"></a>Conectar-se ao SQL do Azure no Edge

As etapas a seguir usam a ferramenta de linha de comando do SQL do Azure no Edge, **sqlcmd** , dentro do contêiner para se conectar ao SQL do Azure no Edge.

> [!NOTE]      
> As ferramentas de linha de comando do SQL (sqlcmd) não estão disponíveis na versão ARM64 dos contêineres do Azure SQL Edge.

1. Use o comando `docker exec -it` para iniciar um shell bash interativo dentro do contêiner em execução. No exemplo a seguir `azuresqledge` , é nome especificado pelo `Name` parâmetro do seu módulo de IOT Edge.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. Quando estiver dentro do contêiner, conecte-se localmente com a sqlcmd. A sqlcmd não está no caminho por padrão, portanto, você precisará especificar o caminho completo.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]    
   > É possível omitir a senha na linha de comando para receber uma solicitação para inseri-la.

3. Se isso funcionar, você será levado a um prompt de comando **sqlcmd** : `1>`.

## <a name="create-and-query-data"></a>Criar e consultar dados

As seções a seguir descrevem como usar o **sqlcmd** e o Transact-SQL para criar um banco de dados, adicionar dados e executar uma consulta.

### <a name="create-a-new-database"></a>Criar um novo banco de dados

As etapas a seguir criam um novo banco de dados denominado `TestDB`.

1. No prompt de comando **sqlcmd** , cole o seguinte comando Transact-SQL para criar um banco de dados de teste:

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. Na próxima linha, grave uma consulta para retornar o nome de todos os bancos de dados do servidor:

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>Inserir dados

Em seguida, crie uma nova tabela, `Inventory`, e insira duas novas linhas.

1. No prompt de comando **sqlcmd** , altere o contexto para o novo banco de dados `TestDB`:

   ```sql
   USE TestDB
   ```

2. Criar nova tabela denominada `Inventory`:

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. Inserir dados na nova tabela:

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. Digite `GO` para executar os comandos anteriores:

   ```sql
   GO
   ```

### <a name="select-data"></a>Selecionar dados

Agora, execute uma consulta para retornar da tabela `Inventory`.

1. No prompt de comando **sqlcmd** , digite uma consulta que retorna linhas de tabela `Inventory` em que a quantidade é maior que 152:

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. Execute o comando:

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>Saia do prompt de comando sqlcmd

1. Para encerrar a sessão **sqlcmd** , digite `QUIT`:

   ```sql
   QUIT
   ```

2. Para sair do prompt de comando interativo no contêiner, digite `exit`. O contêiner continuará a ser executado depois que você sair do shell bash interativo.

## <a name="connect-from-outside-the-container"></a> Conectar-se de fora do contêiner

Você pode conectar e executar consultas SQL em sua instância do Azure SQL Edge de qualquer ferramenta externa do Linux, Windows ou macOS que ofereça suporte a conexões SQL. Para obter mais informações sobre como se conectar a um contêiner do SQL no Edge externamente, confira [Conectar e consultar o SQL do Azure no Edge](./connect.md).

Neste início rápido, você implantou um módulo do SQL no Edge em um dispositivo IoT Edge. 

## <a name="next-steps"></a>Próximas etapas

- [Machine Learning e inteligência artificial com ONNX no SQL Edge](onnx-overview.md)
- [Criando uma solução de IoT de ponta a ponta com o SQL Edge usando o IoT Edge](tutorial-deploy-azure-resources.md)
- [Streaming de dados no SQL do Azure no Edge](stream-data.md)
- [Solucionar problemas de erros de implantação](troubleshoot.md)