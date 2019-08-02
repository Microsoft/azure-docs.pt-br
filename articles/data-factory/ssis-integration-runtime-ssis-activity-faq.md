---
title: Solucionar problemas de execução de pacote no integration runtime do SSIS | Microsoft Docs
description: Este artigo fornece orientação para solução de problemas para execução de pacotes do SSIS no integration runtime do SSIS
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/15/2019
author: wenjiefu
ms.author: wenjiefu
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 05723a90725992e6b955524a2d35c82d3378ee3d
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621847"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Solucionar problemas de execução de pacote no integration runtime do SSIS

Este artigo inclui os erros mais comuns que você pode encontrar quando você estiver executando pacotes do SQL Server Integration Services (SSIS) no tempo de execução de integração do SSIS. Ele descreve as possíveis causas e ações para resolver os erros.

## <a name="where-to-find-logs-for-troubleshooting"></a>Onde encontrar os logs para solução de problemas

Use o portal do Azure Data Factory para verificar a saída da atividade de execução de pacote do SSIS. A saída inclui o resultado da execução, as mensagens de erro e ID da operação. Para obter detalhes, consulte [monitorar o pipeline](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Use o catálogo do SSIS (SSISDB) para verificar os logs de detalhes para a execução. Para obter detalhes, consulte [monitorar pacotes em execução e outras operações](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Erros comuns, causas e soluções

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Mensagem de erro: "Tempo limite de Conexão expirou" ou "o serviço encontrou um erro ao processar sua solicitação. Tente novamente."

Aqui estão as causas em potencial e as ações recomendadas:
* A fonte de dados ou o destino está sobrecarregado. Verifique a carga em sua fonte de dados ou de destino e se ele tem capacidade suficiente. Por exemplo, se você tiver usado o banco de dados SQL, considere escalar verticalmente se o banco de dados é a probabilidade de atingir o tempo limite.
* A rede entre o tempo de execução de integração do SSIS e a fonte de dados ou o destino estiver instável, especialmente quando a conexão é entre regiões ou entre locais e o Azure. Aplica o padrão de repetição no pacote SSIS seguindo estas etapas:
  * Verifique se que os pacotes do SSIS podem executar novamente em caso de falha sem efeitos colaterais (por exemplo, a perda de dados ou a eliminação de duplicação de dados).
  * Configure **Repita** e **intervalo de repetição** de **executar pacote do SSIS** atividade no **geral** guia. ![Definir propriedades na guia Geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Para um ADO.NET e OLE DB componente origem ou destino, defina **ConnectRetryCount** e **ConnectRetryInterval** no Gerenciador de Conexão no pacote do SSIS ou atividade do SSIS.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Mensagem de erro: "Origem do ADO NET não adquiriu a conexão '...'" com "Ocorreu um erro de rede ou específico da instância ao estabelecer uma conexão com o SQL Server. O servidor não foi encontrado ou não estava acessível."

Esse problema geralmente significa que a fonte de dados ou o destino não está acessível de integration runtime do SSIS. Os motivos podem variar. Execute estas ações:
* Verifique se você está passando a fonte de dados ou o destino IP/nome corretamente.
* Verifique se que o firewall está configurado corretamente.
* Verifique se que sua rede virtual está configurada corretamente se sua fonte de dados ou de destino está no local:
  * Você pode verificar se o problema é de configuração de rede virtual ao provisionar uma VM do Azure na mesma rede virtual. Em seguida, verifique se a fonte de dados ou o destino pode ser acessado da VM do Azure.
  * Você pode encontrar mais detalhes sobre como usar uma rede virtual com um integration runtime do SSIS no [unir um tempo de execução de integração do Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Mensagem de erro: "Origem do ADO NET não adquiriu a conexão '...'" com "Não foi possível criar uma conexão gerenciada manager."

A possível causa é que o provedor ADO.NET usado no pacote não está instalado no tempo de execução de integração do SSIS. Você pode instalar o provedor por meio de uma instalação personalizada. Você pode encontrar mais detalhes sobre a instalação personalizada na [personalizar a instalação para o Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Mensagem de erro: "A conexão '...' não foi encontrado"

Um problema conhecido em versões mais antigas do SQL Server Management Studio (SSMS) pode causar esse erro. Se o pacote contiver um componente personalizado (por exemplo, componentes do Feature Pack do SSIS do Azure ou o parceiro) não está instalado no computador em que o SSMS é usado para fazer a implantação, o SSMS remover o componente e fazer com que o erro. Atualizar [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para a versão mais recente que tem o correção do problema.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Mensagem de erro: "código de saída do Executor do SSIS: -1073741819."

* Possível causa & ação recomendada:
  * Esse erro pode ser devido à limitação de origem do Excel e de destino quando várias fontes do Excel ou destinos são executadas em paralelo em vários threads. É possível solucionar essa limitação, alterar os componentes do Excel para executar em sequência ou separá-los em diferentes pacotes e gatilho por meio de "Tarefa executar pacote do" com a propriedade ExecuteOutOfProcess definida como True.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Mensagem de erro: "Não há espaço suficiente no disco"

Esse erro significa que o disco local é usado para cima no nó de tempo de execução de integração do SSIS. Verifique se o seu pacote ou programa de instalação personalizado está consumindo muito espaço em disco:
* Se o disco for consumido por seu pacote, ele será liberado após a conclusão da execução do pacote.
* Se o disco é consumido pelo seu programa de instalação personalizado, você precisa interromper o tempo de execução de integração do SSIS, modificar o script e iniciar o integration runtime novamente. O contêiner de BLOBs do Azure totalmente especificado para a instalação personalizada será copiada para o nó de tempo de execução de integração do SSIS, portanto, verifique se há qualquer conteúdo desnecessário no contêiner.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Mensagem de erro: "Falha ao recuperar o recurso do mestre. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: Código: 300004. Descrição: carregar arquivo de "*" falha. "

* Possível causa & ação recomendada:
  * Se a atividade do SSIS é executar o pacote do sistema de arquivos (arquivo de pacote ou arquivo de projeto), esse erro ocorrerá se o arquivo de projeto, pacote ou a configuração não estiver acessível com a credencial de acesso de pacote fornecida na atividade de SSIS
    * Se você estiver usando arquivos do Azure:
      * O caminho do arquivo deve começar com \\ \\ \<nome da conta de armazenamento\>. file.core.windows.net\\\<caminho de compartilhamento de arquivo\>
      * O domínio deve ser "Azure"
      * O nome de usuário deve ser \<nome da conta de armazenamento\>
      * A senha deve ser \<chave de acesso de armazenamento\>
    * Se você estiver usando um arquivo local, verifique se permissão, a credencial de acesso de pacote e a rede virtual estão configurados corretamente para que o tempo de execução de integração do Azure-SSIS possa acessar o compartilhamento de arquivos local

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Mensagem de erro: "O nome do arquivo '...' especificado na conexão não era válida "

* Possível causa & ação recomendada:
  * Um nome de arquivo inválido é especificado
  * Verifique se que você estiver usando o FQDN (nome de domínio totalmente qualificado) em vez de curto período de tempo em seu Gerenciador de conexão

### <a name="error-message-cannot-open-file-"></a>Mensagem de erro: "Não é possível abrir arquivo '...'"

Esse erro ocorre quando a execução do pacote não é possível localizar um arquivo no disco local no tempo de execução de integração do SSIS. Execute estas ações:
* Não use o caminho absoluto no pacote que está sendo executado no tempo de execução de integração do SSIS. Use o diretório de trabalho de execução atual (.) ou a pasta temp (% TEMP %) em vez disso.
* Se você precisar manter alguns arquivos em nós de integration runtime do SSIS, prepare os arquivos conforme descrito em [personalizar a instalação](how-to-configure-azure-ssis-ir-custom-setup.md). Todos os arquivos no diretório de trabalho serão limpos depois que a execução for concluída.
* Use arquivos do Azure em vez de armazenar o arquivo no nó de tempo de execução de integração do SSIS. Para obter detalhes, consulte [compartilhamentos de arquivos do Azure de uso](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Mensagem de erro: "O banco de dados 'SSISDB' atingiu sua cota de tamanho"

Uma possível causa é que o banco de dados SSISDB criado no banco de dados SQL do Azure ou uma instância gerenciada ao criar um integration runtime do SSIS, atingiu sua cota. Execute estas ações:
* Considere aumentar o DTU do banco de dados. Você pode encontrar detalhes na [limites de recursos do banco de dados SQL para um servidor de banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).
* Verifique se o seu pacote geraria muitos logs. Nesse caso, você pode configurar um trabalho Elástico para limpar esses logs. Para obter detalhes, consulte [limpar os logs SSISDB com trabalhos de banco de dados Elástico](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Mensagem de erro: "O limite de solicitação para o banco de dados é... e foi atingido."

Se muitos pacotes estão em execução em paralelo no integration runtime do SSIS, esse erro pode ocorrer porque o SSISDB atingiu seu limite de solicitação. Considere aumentar o DTC de SSISDB para resolver esse problema. Você pode encontrar detalhes na [limites de recursos do banco de dados SQL para um servidor de banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Mensagem de erro: "Falha na operação do SSIS com status de operação inesperado:..."

O erro é causado principalmente por um problema temporário, portanto, tente executar novamente a execução do pacote. Aplica o padrão de repetição no pacote SSIS seguindo estas etapas:

* Verifique se que os pacotes do SSIS podem executar novamente em caso de falha sem efeitos colaterais (por exemplo, a perda de dados ou a eliminação de duplicação de dados).
* Configure **Repita** e **intervalo de repetição** de **executar pacote do SSIS** atividade no **geral** guia. ![Definir propriedades na guia Geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Para um ADO.NET e OLE DB componente origem ou destino, defina **ConnectRetryCount** e **ConnectRetryInterval** no Gerenciador de Conexão no pacote do SSIS ou atividade do SSIS.

### <a name="error-message-there-is-no-active-worker"></a>Mensagem de erro: "Não há nenhum trabalho ativo."

Esse erro geralmente significa que o tempo de execução de integração do SSIS tem um status não íntegro. Verifique o portal do Azure para o status e erros detalhados. Para obter mais informações, consulte [Azure-SSIS integration runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Mensagem de erro: "O seu tempo de execução de integração não pode ser atualizado e eventualmente deixará de funcionar, já que não é possível acessar o contêiner de BLOBs do Azure fornecida para a instalação personalizada."

Esse erro ocorre quando o tempo de execução de integração do SSIS não é possível acessar o armazenamento configurado para a instalação personalizada. Verifique se a assinatura de acesso compartilhado (SAS) URI que você forneceu é válida e não expirou.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Mensagem de erro: "Provedor Microsoft OLE DB para Analysis Services. 'Hresult: 0x80004005 Description:' Erro COM: Erro COM: mscorlib; Exceção foi lançada pelo destino de uma invocação"

Uma possível causa é que o nome de usuário ou a senha com a autenticação multifator habilitado está configurada para autenticação do Azure Analysis Services. Essa autenticação não tem suporte no tempo de execução de integração do SSIS. Tente usar uma entidade de serviço para autenticação do Azure Analysis Services:
1. Prepare uma entidade de serviço, conforme descrito em [automação com entidades de serviço](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal).
2. No Gerenciador de Conexão, configure **usar um determinado nome de usuário e senha**: defina **AppID** como o nome de usuário e **clientSecret** como a senha.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Mensagem de erro: "Fonte ADONET não adquiriu a conexão {GUID} com a seguinte mensagem de erro: Falha no logon do usuário 'NT AUTHORITY\ANONYMOUS LOGON' "ao usar uma identidade gerenciada

Verifique se você não configurar o método de autenticação do Gerenciador de Conexão como **autenticação de senha do Active Directory** quando o parâmetro *ConnectUsingManagedIdentity* é **True** . Você pode configurá-lo como **autenticação do SQL** em vez disso, que será ignorado se *ConnectUsingManagedIdentity* está definido.

### <a name="package-execution-takes-too-long"></a>Execução do pacote demora muito

Aqui estão as causas em potencial e as ações recomendadas:
* Número excessivo de execuções de pacote foram agendadas no SSIS integration runtime. Todas essas execuções estará esperando em uma fila por sua vez.
  * Determine o máximo usando esta fórmula: 
    
    Contagem de execução paralelo máx por IR = contagem de nós * execução paralelo máx por nó
  * Para saber como definir a contagem de nós e a execução paralela máxima por nó, consulte [criar um tempo de execução de integração do Azure-SSIS no Azure Data Factory](create-azure-ssis-integration-runtime.md).
* O tempo de execução de integração do SSIS está parado ou tem um status não íntegro. Para saber como verificar o status de tempo de execução de integração do SSIS e os erros, consulte [Azure-SSIS integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Também recomendamos que você defina um tempo limite na **geral** guia: ![Definir propriedades na guia geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png).

### <a name="poor-performance-in-package-execution"></a>Um baixo desempenho em execução do pacote

Execute estas ações:

* Verifique se que o tempo de execução de integração do SSIS está na mesma região que a fonte de dados e o destino.

* Defina o nível de log de execução do pacote **desempenho** para coletar informações de duração de cada componente na execução. Para obter detalhes, consulte [registro em log do Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging).

* Verifique o desempenho do nó de IR no portal do Azure:
  * Para obter informações sobre como monitorar o tempo de execução de integração do SSIS, consulte [Azure-SSIS integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Você pode encontrar o histórico de CPU/memória para o integration runtime do SSIS, exibindo as métricas do data factory no portal do Azure.
    ![Monitorar as métricas do tempo de execução de integração do SSIS](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
