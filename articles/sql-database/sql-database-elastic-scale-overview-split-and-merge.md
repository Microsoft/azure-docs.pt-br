---
title: Mover dados entre bancos de dados na nuvem escalados horizontalmente | Microsoft Docs
description: Explica como manipular fragmentos e mover os dados por meio de um serviço auto-hospedado usando APIs de banco de dados elástico.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 506847b436eeb3e1f612a17bf1182359a0e00947
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348433"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Mover dados entre bancos de dados na nuvem escalados horizontalmente

Se você for um desenvolvedor de Software como Serviço e seu aplicativo sofrer uma imensa demanda repentinamente, será necessário acomodar o crescimento. Por isso você adiciona mais bancos de dados (fragmentos). Como você redistribui os dados para novos bancos de dados sem afetar a integridade dos dados? Use a **ferramenta de divisão/mesclagem** para mover dados de bancos de dados restritos para novos bancos de dados.  

A ferramenta de divisão/mesclagem é executada como um serviço Web do Azure. Um administrador ou desenvolvedor usa a ferramenta para mover shardlets (dados de um fragmento) entre diferentes bancos de dados (fragmentos). A ferramenta usa o gerenciamento de mapa de fragmentos para manter o banco de dados de metadados de serviço e garantir mapeamentos consistentes.

![Visão geral][1]

## <a name="download"></a>Baixar

[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Documentação

1. [Tutorial de ferramenta da Divisão de Mesclagem do banco de dados elástico](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Configuração de segurança da divisão e mesclagem](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Considerações de segurança de divisão/mesclagem](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [Gerenciamento de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md)
5. [Migrar bancos de dados existentes para escala horizontal](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [Ferramentas de Banco de Dados Elástico](sql-database-elastic-scale-introduction.md)
7. [Glossário de ferramentas do banco de dados elástico](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Por que usar a ferramenta de divisão/mesclagem?

- **Flexibilidade**

  Aplicativos precisam ampliar-se com flexibilidade para além dos limites de um único banco de dados do Banco de Dados SQL do Azure. Use a ferramenta para mover os dados conforme necessário para novos bancos de dados, mantendo a integridade.

- **Dividir para crescer**

  Para aumentar a capacidade total para lidar com o crescimento explosivo, crie capacidade adicional fragmentando os dados e distribuindo-os de forma incremental e distribuindo-os em mais bancos de dados até que as necessidades de capacidade sejam atendidas. Isso é um excelente exemplo do recurso**Dividir**.

- **Mesclar para reduzir**

  A capacidade precisa ser reduzida devido à natureza sazonal de um negócio. A ferramenta permite reduzir verticalmente para menos unidades quando o negócio desacelera. O recurso ‘mesclar’ no Serviço de divisão/mesclagem de escala elástica aborda esse requisito.

- **Gerenciar pontos de acesso movendo shardlets**

  Com vários locatários por banco de dados, a alocação de shardlets para fragmentos pode levar a gargalos de capacidade em alguns fragmentos. Isso exige a realocação de shardlets ou a movimentação de shardlets ocupados para fragmentos novos ou menos utilizados.

## <a name="concepts--key-features"></a>Principais recursos e conceitos

- **Serviços hospedados no cliente**

  A divisão/mesclagem é fornecida como um serviço hospedado no cliente. Você deve implantar e hospedar o serviço na sua assinatura do Microsoft Azure. O pacote que você baixou do NuGet contém um modelo de configuração para concluir as informações da sua implantação específica. Consulte o [tutorial divisão mesclagem](sql-database-elastic-scale-configure-deploy-split-and-merge.md) para obter detalhes. Uma vez que o serviço é executado na sua assinatura do Azure, você pode controlar e configurar a maioria dos aspectos de segurança do serviço. O modelo padrão inclui as opções para configurar o SSL, autenticação de cliente baseada no certificado, criptografia para credenciais armazenadas, proteção DoS e restrições de IP. Você pode encontrar mais informações sobre os aspectos de segurança no documento de [configuração de segurança de divisão/mesclagem](sql-database-elastic-scale-split-merge-security-configuration.md)a seguir.

  O serviço padrão implantado é executado com um operador e uma função web. Cada um usa o tamanho da VM A1 nos Serviços de Nuvem do Azure. Embora não seja possível modificar essas configurações durante a implantação do pacote, eles podem ser alterados após uma implantação bem-sucedida no serviço de nuvem em execução (por meio do portal do Azure). Observe que a função de trabalho não deve ser configurada para mais de uma única instância por motivos técnicos.

- **Integração de mapa de fragmentos**

  O serviço de divisão/mesclagem interage com o mapa de fragmentos do aplicativo. Ao usar o serviço de divisão/mesclagem para dividir ou mesclar intervalos ou para mover shardlets entre fragmentos, o serviço mantém automaticamente o mapa de fragmentos atualizado. Para fazer isso, o serviço se conecta ao banco de dados do gerenciador de mapa do fragmento do aplicativo e mantém intervalos e mapeamentos como progresso de solicitações de divisão/mesclagem/movimentação. Isso garante que o mapa de fragmentos sempre apresente uma exibição atualizada quando operações de divisão mesclagem estiverem sendo realizadas. Operações de divisão, mesclagem e de movimentação de shardlet são implementadas movendo um lote de shardlets do fragmento de origem para o fragmento de destino. Durante a operação de movimentação do shardlet, os shardlets sujeitos ao lote atual são marcados como offline no mapa de fragmentos e ficam indisponíveis para conexões de roteamento dependentes de dados usando a API **OpenConnectionForKey** .

- **Conexões consistentes de shardlet**

  Quando a movimentação de dados é iniciada em um novo lote de shardlets, todas as conexões de roteamento dependentes dos dados fornecidas pelo mapa de fragmentos ao fragmento que armazena o shardlet são interrompidas e as próximas conexões das APIs do mapa de fragmentos com os shardlets são bloqueadas enquanto a movimentação de dados está em andamento para evitar inconsistências. Conexões com outros shardlets no mesmo fragmento também serão interrompidas, mas funcionarão de novo, imediatamente em uma nova tentativa. Depois que o lote é movido, os shardlets são marcados novamente como online para o fragmento de destino e os dados de origem são removidos do fragmento de origem. O serviço passa por essas etapas, a cada lote, até que todos os shardlets sejam movidos. Isso levará a várias operações de interrupção de conexão ao longo da operação de divisão/mesclagem/movimentação.  

- **Gerenciando a disponibilidade de shardlets**

  A limitação a interrupção de conexão do lote atual de shardlets, conforme discutido acima, restringe o escopo de indisponibilidade a um lote de shardlets por vez. Isso é preferível a uma abordagem em que o fragmento completo permaneceria offline para todos os seus shardlets ao longo da operação de divisão/mesclagem. O tamanho de um lote, definido como o número de shardlets distintos para mover por vez, é um parâmetro de configuração. Ele pode ser definido para cada operação de divisão/mesclagem, dependendo das necessidades de desempenho e de disponibilidade do aplicativo. Observe que o intervalo que está sendo bloqueado no mapa do fragmento pode ser maior que o tamanho de lote especificado. Isso ocorre porque o serviço escolhe o tamanho do intervalo, de modo que o número real de valores de chave de fragmentação nos dados corresponda aproximadamente ao tamanho do lote. É importante lembrar isso, especialmente para chaves de fragmentação escassamente preenchidas.

- **Armazenamento de metadados**

  O serviço de divisão/mesclagem usa um banco de dados para manter seu status e logs durante o processamento da solicitação. O usuário cria este banco de dados na sua assinatura e fornece a cadeia de conexão para ele no arquivo de configuração para a implantação do serviço. Os administradores da organização do usuário também podem se conectar a este banco de dados para examinar o andamento da solicitação e para investigar informações detalhadas sobre falhas potenciais.

- **Reconhecimento de fragmentação**

  O serviço de divisão/mesclagem faz distinção entre (1) tabelas fragmentadas, (2) tabelas de referência e (3) tabelas normais. A semântica de uma operação de divisão/mesclagem/movimentação depende do tipo da tabela usada e é definida da seguinte maneira:

  - **Tabelas fragmentadas**

    Operações de divisão, mesclagem e movimentação movem os shardlets do fragmento de origem para o fragmento de destino. Após a conclusão bem-sucedida da solicitação geral, esses shardlets não estarão mais presentes na origem. Observe que as tabelas de destino precisam existir no fragmento de destino e não podem conter dados no intervalo de destino antes do processamento da operação.

  - **Tabelas de referência**

    Para tabelas de referência, as operações de divisão, mesclagem e movimentação copiam os dados do fragmento de origem no fragmento de destino. No entanto, observe que nenhuma alteração ocorre no fragmento de destino, para uma determinada tabela, se já houver alguma linha nesta tabela, no destino. A tabela deve estar vazia para que qualquer operação de cópia de tabela de referência seja processada.

  - **Outras tabelas**

    Outras tabelas podem estar presentes na origem ou no destino de uma operação de divisão/mesclagem. A divisão/mesclagem ignora essas tabelas em qualquer operação de cópia ou movimentação de dados. No entanto, observe que elas podem interferir com essas operações no caso de restrições.

    As informações na referência versus tabelas fragmentadas são fornecidas pela `SchemaInfo` APIs no mapa de fragmentos. O exemplo a seguir ilustra o uso dessas APIs em um objeto de gerenciador de mapa de fragmento:

    ```csharp
    // Create the schema annotations
    SchemaInfo schemaInfo = new SchemaInfo();

    // Reference tables
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region"));
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation"));

    // Sharded tables
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY"));
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY"));
    // Publish
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo);
    ```

    As tabelas “region” e “nation” são definidas como tabelas de referência e serão copiadas com operações de divisão/mesclagem/mover. Já “customer” e “order” por sua vez são definidos como tabelas fragmentadas. `C_CUSTKEY` e `O_CUSTKEY` servir como a chave de fragmentação.

- **Integridade referencial**

  O serviço de divisão mesclagem analisa dependências entre as tabelas e usa as relações de chave estrangeira - chave primária para montar as operações para mover as tabelas de referência e shardlets. Em geral, as tabelas de referência são copiadas primeiro em ordem de dependência, os shardlets são então copiados na ordem de suas dependências em cada lote. Isso é necessário para que as restrições FK-PK no fragmento de destino sejam respeitadas a medida que os novos dados chegam.

- **Consistência de mapa de fragmentos e eventual conclusão**

  Na presença de falhas, o serviço de divisão mesclagem retoma operações após qualquer interrupção e pretende concluir qualquer em solicitações de andamento. No entanto, podem haver situações irrecuperáveis (ex.: quando o fragmento de destino é perdido ou comprometido e não pode ser reparado). Sob tais circunstâncias, alguns shardlets que deveriam ser movidos podem continuar a residir no fragmento de origem. O serviço garante que os mapeamentos de shardlet são atualizados somente depois que os dados necessários tiverem sido copiados, com êxito, no destino. Shardlets só são excluídos na origem depois que todos os seus dados tiverem sido copiados para o destino e os mapeamentos correspondentes atualizados com êxito. A operação de exclusão ocorre em segundo plano quando o intervalo já está online no fragmento de destino. O serviço de divisão/mesclagem sempre garante a exatidão dos mapeamentos armazenados no mapa de fragmentos.

## <a name="the-split-merge-user-interface"></a>A interface do usuário de divisão/mesclagem

O pacote de serviço de divisão/mesclagem inclui uma função de trabalho e uma função Web. A função Web é usada para enviar solicitações de divisão/mesclagem de forma interativa. Os componentes principais da interface do usuário são os seguintes:

- **Tipo de operação**

  O tipo de operação é um botão de opção que controla o tipo de operação executada pelo serviço para esta solicitação. Você pode escolher entre os cenários de divisão, mesclagem e movimentação. Você também pode cancelar uma operação enviada anteriormente. Você pode usar divisão, mesclagem e mover solicitações para mapas de fragmento de intervalo. Os mapas de fragmento da lista apenas oferecem suporte para as operações de movimentação.

- **Mapa de fragmentos**

  A próxima seção de parâmetros de solicitação abrange as informações sobre o mapa de fragmentos e o banco de dados que hospeda o mapa de fragmentos. Em particular, você precisa fornecer o nome do servidor do Banco de Dados SQL do Azure e do banco de dados que hospeda o mapa do fragmento, credenciais para se conectar ao banco de dados de mapa do fragmento e, finalmente, o nome do mapa do fragmento. Atualmente, a operação aceita apenas um único conjunto de credenciais. Essas credenciais precisam ter permissões suficientes para realizar alterações no mapa de fragmento, bem como para os dados do usuário nos fragmentos.

- **Intervalo de origem (dividir e mesclar)**

  Uma operação de divisão e mesclagem que processa um intervalo usando sua chave de baixa e alta. Para especificar uma operação com um valor alto de chave não associado, marque a caixa de seleção "Chave alta é max" e deixe o campo de chave com valor alto vazio. Os valores de chave do intervalo que você especificar não precisam corresponder exatamente a um mapeamento e seu limite no seu mapa de fragmento. Se você não especificar nenhum limite de intervalo em todos os serviços irá inferir o intervalo mais próximo para você automaticamente. Você pode usar o script do PowerShell GetMappings.ps1 para recuperar os mapeamentos atuais em um determinado mapa de fragmento.

- **Comportamento da fonte de divisão (divisão)**

  Para operações de divisão, defina o ponto em que você deseja dividir o intervalo de origem. Para tanto, forneça a chave de fragmentação onde deseja que a divisão ocorra. Em seguida, use o botão de opção para definir se deseja mover a parte inferior do intervalo (exceto a chave de divisão) ou a superior (incluindo a chave de divisão).

- **Shardlet de origem (mover)**

  Operações de movimentação são diferentes de divisão ou mesclagem, pois não precisam de um intervalo para descrever a origem. Uma origem para mover é identificada simplesmente pelo valor de chave de fragmentação que pretende mover.

- **Fragmento de destino (divisão)**

  Uma vez fornecidas as informações da origem da operação de divisão, será necessário definir onde você deseja que os dados sejam copiados fornecendo os nomes do banco de dados e do servidor do Banco de Dados SQL do Azure e o nome do banco de dados do destino.

- **Intervalo de destino (mesclagem)**

  Operações de mesclagem movem shardlets para um fragmento existente. Você identifica o fragmento existente fornecendo os limites de intervalo do intervalo existente que ao qual deseja mesclar.

- **Tamanho do lote**

  O tamanho do lote controla o número de shardlets que ficarão offline por vez durante a movimentação de dados. Isso é um valor inteiro onde for possível usar valores menores quando for sensível a períodos longos de inatividade para shardlets. Valores maiores aumentarão o tempo que um determinado shardlet fica offline, mas podem melhorar o desempenho.

- **ID da operação (Cancelar)**

  Se houver uma operação em andamento que não é mais necessária, você pode cancelá-la fornecendo sua ID da operação neste campo. Você pode recuperar a ID da operação da tabela de status de solicitação (consulte a seção 8.1) ou da saída do navegador da Web em que enviou a solicitação.

## <a name="requirements-and-limitations"></a>Requisitos e limitações

A implementação atual do serviço de divisão/mesclagem está sujeita aos requisitos e limitações a seguir:

- Os fragmentos precisam existir e estarem registrados no mapa de fragmentos para que uma operação de divisão/mesclagem possa ser executada nesses fragmentos.
- O serviço não cria tabelas ou quaisquer outros objetos de banco de dados automaticamente como parte de suas operações. Isso significa que o esquema de todas as tabelas fragmentadas e das tabelas de referência precisa existir no fragmento de destino antes de qualquer operação de divisão/mesclagem/movimentação. Tabelas fragmentadas, em particular, devem estar vazias no intervalo onde novos shardlets devem ser adicionados por uma operação de divisão/mesclagem/movimentação. Caso contrário, a operação falhará na verificação de consistência inicial no fragmento de destino. Observe também que os dados de referência só são copiados se a tabela de referência estiver vazia e se não houver nenhuma garantia de consistência em relação a outras operações de gravação simultâneas nas tabelas de referência. Recomendamos fazer o seguinte: ao executar operações de divisão/mesclagem, nenhuma outra operação de gravação deve fazer alterações nas tabelas de referência.
- O serviço depende da identidade de linha estabelecida por um índice exclusivo ou chave que inclui a chave de fragmentação para melhorar o desempenho e a confiabilidade de shardlets grandes. Isso permite que o serviço mova dados em uma granularidade ainda maior do que apenas fragmentando o valor chave. Isso ajuda a reduzir a quantidade máxima de espaço de log e de bloqueios necessários durante a operação. Considere a criação de um índice exclusivo ou uma chave primária, incluindo a chave de fragmentação em uma determinada tabela, se quiser usar essa tabela com solicitações de divisão/mesclagem/movimentação. Por motivos de desempenho, a chave de fragmentação deve ser a coluna à esquerda na chave ou índice.
- Durante o processamento de solicitações, alguns dados de shardlet podem estar presentes tanto no fragmento de origem quanto no de destino. Isso é necessário para proteger contra falhas durante a movimentação de shardlet. A integração de divisão/mesclagem com o mapa de fragmentos garante que as conexões por meio das APIs de roteamento dependente de dados que usam o método **OpenConnectionForKey** no mapa de fragmentos não encontrem nenhum estado intermediário inconsistente. No entanto, na conexão aos fragmentos de origem ou destino sem usar o método **OpenConnectionForKey** , estados intermediários inconsistentes podem ficar visíveis quando solicitações de divisão/mesclagem/movimentação estiverem ocorrendo. Essas conexões podem mostrar resultados parciais ou duplicados, dependendo do tempo ou do fragmento que subjaz à conexão. Atualmente, essa limitação inclui as conexões feitas por Consultas-Multi-Fragmento de Escala Elástica.
- O banco de dados de metadados para o serviço de divisão/mesclagem não deve ser compartilhado entre diferentes funções. Por exemplo, uma função do serviço de divisão/mesclagem executado de preparo precisa apontar para um banco de dados de metadados diferentes da função de produção.

## <a name="billing"></a>Cobrança

O serviço de divisão/mesclagem é executado como um serviço de nuvem em sua assinatura do Microsoft Azure. Portanto, os encargos para serviços de nuvem se aplicam à sua instância do serviço. A menos que você execute operações de divisão/mesclagem/movimentação com frequência, recomendamos que você exclua seu serviço de nuvem de divisão/mesclagem. Isso reduz os custos de execução ou de instâncias de serviço de nuvem implantadas. Você pode implantar novamente e iniciar sua configuração prontamente executável sempre que desejar executar operações de divisão/mesclagem.

## <a name="monitoring"></a>Monitoramento

### <a name="status-tables"></a>Tabelas de status

O Serviço de divisão/mesclagem fornece a tabela **RequestStatus** no banco de dados de armazenamento dos metadados para o monitoramento das solicitações em andamento e concluídas. A tabela lista uma linha para cada solicitação de divisão/mesclagem enviada para essa instância do serviço de divisão/mesclagem. Ele oferece as seguintes informações para cada solicitação:

- **Timestamp**

  Hora e a data em que a solicitação foi iniciada.

- **OperationId**

  GUID que identifica exclusivamente a solicitação. Essa solicitação também pode ser usada para cancelar a operação enquanto ela ainda está em andamento.

- **Status**

  Estado atual da solicitação. Solicitações em andamento, também relaciona a fase em que a solicitação está no momento.

- **CancelRequest**

  Um sinalizador que indica se a solicitação foi cancelada.

- **Progresso**

  Uma estimativa percentual da conclusão da operação. Um valor de 50 indica que a operação está aproximadamente 50% concluída.

- **Detalhes**

  Um valor XML que fornece um relatório de andamento mais detalhado. O relatório de andamento é atualizado periodicamente a medida em que conjuntos de linhas são copiados da origem ao destino. No caso de falhas ou exceções, essa coluna também inclui informações mais detalhadas sobre a falha.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

O serviço de divisão/mesclagem usa o diagnóstico do Azure com base no SDK do Azure 2.5 para monitoramento e diagnóstico. Você controla a configuração de diagnóstico conforme explicado aqui: [Habilitar o Diagnóstico nos Serviços de Nuvem do Azure e Máquinas virtuais](../cloud-services/cloud-services-dotnet-diagnostics.md). O pacote de download inclui duas configurações de diagnóstico: uma para a função web e outra para a função de trabalho. Inclui as definições para registrar os Contadores de desempenho, logs do IIS, Logs de Eventos do Windows e logs de eventos do aplicativo de divisão/mesclagem.

## <a name="deploy-diagnostics"></a>Implantar Diagnósticos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

Para habilitar o monitoramento e diagnóstico usando a configuração de diagnóstico para as funções Web e de trabalho fornecidas pelo pacote NuGet, execute os seguintes comandos usando o Azure PowerShell:

```powershell
    $storage_name = "<YourAzureStorageAccount>"
    $key = "<YourAzureStorageAccountKey"
    $storageContext = New-AzStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  
    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml"
    $service_name = "<YourCloudServiceName>"
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb"
    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml"
    $service_name = "<YourCloudServiceName>"
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker"
```

Você pode encontrar mais informações sobre como configurar e implantar as configurações de diagnóstico aqui: [Habilitar o Diagnóstico nos Serviços de Nuvem do Azure e Máquinas virtuais](../cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="retrieve-diagnostics"></a>Recuperar diagnósticos

Você pode acessar facilmente o diagnóstico do Gerenciador de Servidores do Visual Studio na parte do Azure da árvore do Gerenciador de Servidores. Abra uma instância do Visual Studio e, na barra de menus, clique em Modo de exibição e em Gerenciador de Servidores. Clique no ícone do Azure para conectar-se à sua assinatura do Azure. Em seguida, navegue para Azure -> Armazenamento -> `<your storage account>` -> Tabelas -> WADLogsTable. Para obter mais informações, consulte [Gerenciador de Servidores](https://msdn.microsoft.com/library/x603htbk.aspx).

![WADLogsTable][2]

O WADLogsTable destacado na figura acima contém os eventos detalhados do log de aplicativo do serviço de divisão/mesclagem. Observe que a configuração padrão do pacote baixado destina-se a uma implantação de produção. Por isso, o intervalo em que contadores e logs são extraídos das instâncias de serviço é grande (5 minutos). Para teste e desenvolvimento, você pode diminuir o intervalo, ajustando as configurações de diagnóstico da função Web ou de trabalho, de acordo com as suas necessidades. Você pode fazer isso clicando com o botão direito na função no Gerenciador de Servidores do Visual Studio (veja acima) e, em seguida, ajustando o Período de Transferência na caixa de diálogo de configuração do Diagnóstico:

![Configuração][3]

## <a name="performance"></a>Desempenho

Em geral, é esperado um melhor desempenho das camadas de serviço maiores e mais funcionais no Banco de dados SQL do Azure. Alocações de E/S, CPU e memória maiores para camadas de serviço mais altas beneficiarão a cópia em massa e excluirão operações que o serviço de divisão/mesclagem usa internamente. Por esse motivo, aumente a camada de serviço apenas para os bancos de dados por um período definido e limitado.

O serviço também executa consultas de validação como parte de suas operações normais. Essas consultas de validação verificam a presença inesperada de dados no intervalo de destino e garantem que qualquer operação de divisão/mesclagem/movimentação comecem de um estado consistente. Todas essas consultas funcionam em intervalos de chave de fragmentação definidos pelo escopo da operação e o tamanho do lote fornecido como parte da definição de solicitação. Essas consultas têm melhor desempenho quando um índice é apresentado com a chave de fragmentação como coluna à esquerda.

Além disso, uma propriedade de exclusividade com a chave de fragmentação como a coluna principal permitirá que o serviço use uma abordagem otimizada, que limita o consumo de recursos em termos de memória e de espaço de log. Essa propriedade de exclusividade é necessária para mover os tamanhos de dados grandes (normalmente acima de 1GB).

## <a name="how-to-upgrade"></a>Como atualizar

1. Siga as etapas em [Implantar um serviço de divisão/mesclagem](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Altere o arquivo de configuração do serviço de nuvem para sua implantação de divisão/mesclagem refletir os novos parâmetros de configuração. Um novo parâmetro obrigatório são as informações sobre o certificado usado para criptografia. Uma maneira fácil de fazer isso é comparar o novo arquivo de modelo de configuração do download em relação a sua configuração existente. Certifique-se de que adicionar as configurações para "DataEncryptionPrimaryCertificateThumbprint" e "DataEncryptionPrimary" para a função de trabalho e da web.
3. Antes de implantar a atualização para o Azure, certifique-se de que todas as operações de divisão/mesclagem em execução no momento terminaram. Você pode fazer isso facilmente consultando as tabelas RequestStatus e PendingWorkflows no banco de dados de metadados de divisão/mesclagem para as solicitações em andamento.
4. Atualize sua implantação de serviço de nuvem existente para divisão/mesclagem na sua assinatura do Azure com o novo pacote e o seu arquivo de configuração de serviço atualizado.

Você não precisa provisionar um novo banco de dados de metadados para atualizar a divisão/mesclagem. A nova versão irá atualizar automaticamente seu banco de dados de metadados existentes para a nova versão.

## <a name="best-practices--troubleshooting"></a>Práticas recomendadas e solução de problemas

- Defina um locatário de teste e exercite suas operações mais importantes de divisão/mesclagem/movimentação com o locatário de teste ao longo de vários fragmentos. Isso ajudará a garantir que todos os metadados sejam definidos corretamente no seu mapa de fragmentos e que as operações não violem restrições ou chaves estrangeiras.
- Mantenha o tamanho dos dados de locatário de teste acima do tamanho máximo de dados do seu maior locatário para garantir que você não encontrará problemas relacionados ao tamanho dos dados. Isso também ajudará a avaliar um limite superior no tempo que leva para mover um único locatário.
- Certifique-se de que seu esquema permita exclusões. O serviço de divisão/mesclagem requer a capacidade de remover dados de fragmento de origem depois que os dados tiverem sido copiados com êxito para o destino. Por exemplo, **excluir gatilhos** pode impedir o serviço de excluir os dados na origem e causar falha nas operações.
- A chave de fragmentação deve estar na primeira coluna à esquerda da sua chave primária ou definição de índice exclusivo. Isso vai garantir um melhor desempenho para as consultas de validação de divisão/mesclagem e para as operações de movimentação e exclusão de dados reais que sempre operam em intervalos de chave de fragmentação.
- Coloque seu serviço de divisão/mesclagem no data center e região onde residem os bancos de dados.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
