---
title: Solucionar problemas de execução de pacote no Integration Runtime do SSIS
description: Este artigo fornece diretrizes de solução de problemas para a execução do pacote SSIS no Integration Runtime do SSIS
ms.service: data-factory
ms.topic: conceptual
ms.author: wenjiefu
author: wenjiefu
ms.reviewer: sawinark
ms.custom: seo-lt-2019
ms.date: 04/15/2019
ms.openlocfilehash: 2bc56d39de392c9e4c20c25b554e3bdeea048bfb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361869"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Solucionar problemas de execução de pacote no Integration Runtime do SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo inclui os erros mais comuns que você pode encontrar quando estiver executando pacotes do SQL Server Integration Services (SSIS) no Integration Runtime do SSIS. Ele descreve as possíveis causas e ações para resolver os erros.

## <a name="where-to-find-logs-for-troubleshooting"></a>Onde encontrar logs para solução de problemas

Use o portal de Azure Data Factory para verificar a saída da atividade de execução do pacote SSIS. A saída inclui o resultado da execução, as mensagens de erro e a ID da operação. Para obter detalhes, consulte [monitorar o pipeline](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Use o catálogo do SSIS (SSISDB) para verificar os logs de detalhes da execução. Para obter detalhes, consulte [monitorar pacotes em execução e outras operações](/sql/integration-services/performance/monitor-running-packages-and-other-operations).

## <a name="common-errors-causes-and-solutions"></a>Erros, causas e soluções comuns

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Mensagem de erro: "tempo limite de conexão expirado" ou "o serviço encontrou um erro ao processar sua solicitação. Tente novamente.”

Aqui estão as possíveis causas e as ações recomendadas:
* A fonte de dados ou o destino está sobrecarregado. Verifique a carga na fonte de dados ou no destino e veja se ela tem capacidade suficiente. Por exemplo, se você usou o banco de dados SQL do Azure, considere escalar verticalmente se o banco de dados provavelmente atingir o tempo limite.
* A rede entre o tempo de execução de integração do SSIS e a fonte de dados ou destino é instável, especialmente quando a conexão é entre regiões ou entre o local e o Azure. Aplique o padrão de repetição no pacote do SSIS seguindo estas etapas:
  * Verifique se seus pacotes SSIS podem ser executados novamente em caso de falha sem efeitos colaterais (por exemplo, perda de dados ou duplicação de dados).
  * Configure o **intervalo** de **repetição** e repetição da atividade **Executar Pacote SSIS** na guia **geral** . ![ Definir propriedades na guia geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Para um componente de origem ou destino ADO.NET e OLE DB, defina **ConnectRetryCount** e **ConnectRetryInterval** no Gerenciador de conexões no pacote SSIS ou na atividade SSIS.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Mensagem de erro: "a origem do ADO NET falhou ao adquirir a conexão '... '" com "ocorreu um erro relacionado à rede ou específico à instância ao estabelecer uma conexão com SQL Server. O servidor não foi encontrado ou não estava acessível. "

Normalmente, esse problema significa que a fonte de dados ou o destino está inacessível pelo runtime de integração do SSIS. Os motivos podem variar. Tente estas ações:
* Verifique se você está passando a fonte de dados ou o nome/IP de destino corretamente.
* Verifique se o firewall está definido corretamente.
* Verifique se sua rede virtual está configurada corretamente se sua fonte de dados ou destino é local:
  * Você pode verificar se o problema é da configuração de rede virtual Provisionando uma VM do Azure na mesma rede virtual. Em seguida, verifique se a fonte de dados ou o destino pode ser acessado da VM do Azure.
  * Você pode encontrar mais detalhes sobre como usar uma rede virtual com um tempo de execução de integração do SSIS em [unir um tempo de execução de integração do Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Mensagem de erro: "a origem do ADO NET falhou ao adquirir a conexão '... '" com "não foi possível criar um Gerenciador de conexões gerenciado".

A causa potencial é que o provedor ADO.NET usado no pacote não está instalado no Integration Runtime do SSIS. Você pode instalar o provedor usando uma instalação personalizada. Você pode encontrar mais detalhes sobre a instalação personalizada em [Personalizar a instalação para o tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Mensagem de erro: "a conexão '... ' não encontrado "

Um problema conhecido em versões mais antigas do SQL Server Management Studio (SSMS) pode causar esse erro. Se o pacote contiver um componente personalizado (por exemplo, SSIS Azure Feature Pack ou componentes de parceiro) que não esteja instalado no computador em que o SSMS é usado para fazer a implantação, o SSMS removerá o componente e causará o erro. Atualize o [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) para a versão mais recente que tem o problema corrigido.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Mensagem de erro: "código de saída do executor do SSIS:-1073741819."

* Possível causa e ação recomendada:
  * Esse erro pode ser devido à limitação da origem e do destino do Excel quando várias fontes ou destinos do Excel estão sendo executados em paralelo em vários threads. Você pode solucionar essa limitação alterando os componentes do Excel para serem executados em sequência ou separá-los em pacotes diferentes e disparar por meio da "tarefa executar pacote" com a Propriedade ExecuteOutOfProcess definida como true.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Mensagem de erro: "não há espaço suficiente no disco"

Esse erro significa que o disco local é usado no nó do Integration Runtime do SSIS. Verifique se o pacote ou a instalação personalizada está consumindo muito espaço em disco:
* Se o disco for consumido pelo pacote, ele será liberado depois que a execução do pacote for concluída.
* Se o disco for consumido pela instalação personalizada, você precisará parar o tempo de execução de integração do SSIS, modificar o script e iniciar o tempo de execução de integração novamente. Todo o contêiner de blob do Azure especificado para a instalação personalizada será copiado para o nó do tempo de execução de integração do SSIS; portanto, verifique se há algum conteúdo desnecessário sob esse contêiner.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Mensagem de erro: "falha ao recuperar o recurso do mestre. Microsoft. SqlServer. Integrationservices. Dimension. ScaleoutContract. Common. MasterResponseFailedException: código: 300004. Descrição: falha no carregamento do arquivo "* *". "

* Possível causa e ação recomendada:
  * Se a atividade do SSIS estiver executando o pacote do sistema de arquivos (arquivo de pacote ou arquivo de projeto), esse erro ocorrerá se o projeto, pacote ou arquivo de configuração não estiver acessível com a credencial de acesso de pacote fornecida na atividade do SSIS
    * Se você estiver usando o arquivo do Azure:
      * O caminho do arquivo deve começar com \\ \\ \<storage account name\> . File.Core.Windows.net\\\<file share path\>
      * O domínio deve ser "Azure"
      * O nome de usuário deve ser \<storage account name\>
      * A senha deve ser \<storage access key\>
    * Se você estiver usando um arquivo local, verifique se a VNet, a credencial de acesso ao pacote e a permissão estão configuradas corretamente para que o tempo de execução de integração do Azure-SSIS possa acessar seu compartilhamento de arquivos local

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Mensagem de erro: "o nome do arquivo '... ' especificado na conexão não era válido "

* Possível causa e ação recomendada:
  * Um nome de arquivo inválido foi especificado
  * Verifique se você está usando o FQDN (nome de domínio totalmente qualificado) em vez de curto tempo no Gerenciador de conexões

### <a name="error-message-cannot-open-file-"></a>Mensagem de erro: "não é possível abrir o arquivo '... '"

Esse erro ocorre quando a execução do pacote não consegue localizar um arquivo no disco local no tempo de execução de integração do SSIS. Tente estas ações:
* Não use o caminho absoluto no pacote que está sendo executado no Integration Runtime do SSIS. Use o diretório de trabalho de execução atual (.) ou a pasta temporária (% TEMP%) Stead.
* Se você precisar manter alguns arquivos em nós do tempo de execução de integração do SSIS, prepare os arquivos conforme descrito em [Personalizar configuração](how-to-configure-azure-ssis-ir-custom-setup.md). Todos os arquivos no diretório de trabalho serão limpos após a conclusão da execução.
* Use os arquivos do Azure em vez de armazenar o arquivo no nó do Integration Runtime do SSIS. Para obter detalhes, consulte [usar compartilhamentos de arquivos do Azure](/sql/integration-services/lift-shift/ssis-azure-files-file-shares#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Mensagem de erro: "o banco de dados ' SSISDB ' atingiu sua cota de tamanho"

Uma causa potencial é que o banco de dados SSISDB criado no banco de dados SQL do Azure ou no SQL Instância Gerenciada atingiu sua cota. Tente estas ações:
* Considere aumentar a DTU do seu banco de dados. Você pode encontrar detalhes nos [limites do banco de dados SQL para um servidor lógico](../azure-sql/database/resource-limits-logical-server.md).
* Verifique se o pacote geraria muitos logs. Nesse caso, você pode configurar um trabalho elástico para limpar esses logs. Para obter detalhes, consulte [Limpar os logs do SSISDB com trabalhos de Banco de Dados Elástico do Azure](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Mensagem de erro: "o limite de solicitação para o banco de dados é... e foi atingido ".

Se muitos pacotes estiverem sendo executados em paralelo no tempo de execução de integração do SSIS, esse erro poderá ocorrer porque o SSISDB atingiu seu limite de solicitação. Considere aumentar o DTC do SSISDB para resolver esse problema. Você pode encontrar detalhes nos [limites do banco de dados SQL para um servidor lógico](../azure-sql/database/resource-limits-logical-server.md).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Mensagem de erro: "falha na operação do SSIS com status de operação inesperado:..."

O erro é causado principalmente por um problema transitório, portanto, tente executar novamente a execução do pacote. Aplique o padrão de repetição no pacote do SSIS seguindo estas etapas:

* Verifique se seus pacotes SSIS podem ser executados novamente em caso de falha sem efeitos colaterais (por exemplo, perda de dados ou duplicação de dados).
* Configure o **intervalo** de **repetição** e repetição da atividade **Executar Pacote SSIS** na guia **geral** . ![ Definir propriedades na guia geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Para um componente de origem ou destino ADO.NET e OLE DB, defina **ConnectRetryCount** e **ConnectRetryInterval** no Gerenciador de conexões no pacote SSIS ou na atividade SSIS.

### <a name="error-message-there-is-no-active-worker"></a>Mensagem de erro: "não há nenhum trabalhador ativo".

Esse erro geralmente significa que o Integration Runtime do SSIS tem um status não íntegro. Verifique a portal do Azure do status e os erros detalhados. Para obter mais informações, consulte [tempo de execução de integração do Azure-SSIS](./monitor-integration-runtime.md#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Mensagem de erro: "o Integration Runtime não pode ser atualizado e eventualmente deixará de funcionar, pois não podemos acessar o contêiner de blob do Azure fornecido para a instalação personalizada".

Esse erro ocorre quando o Integration Runtime do SSIS não pode acessar o armazenamento configurado para a instalação personalizada. Verifique se o URI de assinatura de acesso compartilhado (SAS) que você forneceu é válido e não expirou.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Mensagem de erro: "provedor de OLE DB da Microsoft para Analysis Services. ' HRESULT: a descrição de 0x80004005: ' COM erro: erro COM: mscorlib; A exceção foi gerada pelo destino de uma invocação "

Uma causa potencial é que o nome de usuário ou a senha com a autenticação multifator do Azure AD habilitada está configurado para Azure Analysis Services autenticação. Essa autenticação não tem suporte no Integration Runtime do SSIS. Tente usar uma entidade de serviço para Azure Analysis Services autenticação:

1. Prepare uma entidade de serviço conforme descrito em [automação com entidades de serviço](../analysis-services/analysis-services-service-principal.md).
2. No Gerenciador de conexões, configure **usar um nome de usuário e senha específicos**: defina **AppID** como o username e **clientSecret** como a senha.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Mensagem de erro: "a origem do ADONET falhou ao adquirir a conexão {GUID} com a seguinte mensagem de erro: falha de logon para o usuário" NT AUTHORITY\ANONYMOUS LOGON "" ao usar uma identidade gerenciada

Certifique-se de não configurar o método de autenticação do Gerenciador de conexões como **Active Directory autenticação de senha** quando o parâmetro *ConnectUsingManagedIdentity* for **true**. Você pode configurá-lo como **autenticação SQL** , o que será ignorado se *ConnectUsingManagedIdentity* estiver definido.

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>Mensagem de erro: "0xC020801F em..., fonte OData [...]: não é possível adquirir uma conexão gerenciada do Gerenciador de conexões de tempo de execução"

Uma causa potencial é que a TLS (segurança da camada de transporte) não é habilitada no tempo de execução de integração do SSIS, que é exigido pela sua fonte OData. Você pode habilitar o TLS no tempo de execução de integração do SSIS usando personalizar configuração. Mais detalhes podem ser encontrados em [não é possível conectar o Project online OData do SSIS](/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) e [Personalizar a instalação para o tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>Mensagem de erro: "solicitar tarefa de preparo com GUID de operação... falha desde o erro: falha ao distribuir a operação de preparo com a mensagem de erro: Microsoft. SqlServer. Integrationservices. AisAgentCore. AisAgentException: falha ao carregar o proxy de dados. "

Verifique se o tempo de execução de integração do Azure-SSIS está configurado com Self-Hosted Integration Runtime. Mais detalhes podem ser encontrados em [configurar Self-Hosted ir como um proxy para Azure-SSIS ir no ADF](self-hosted-integration-runtime-proxy-ssis.md).

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>Mensagem de erro: "status da tarefa de preparo: falha. Erro de tarefa de preparo: ErrorCode: 2010, ErrorMessage: o autohospedado Integration Runtime... está offline "

Verifique se o tempo de execução de integração do Self-Hosted está instalado e iniciado. Mais detalhes podem ser encontrados em [criar e configurar um tempo de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>Mensagem de erro: "erro de tarefa de preparo: ErrorCode: 2906, ErrorMessage: falha na execução do pacote., saída: {" OperationErrorMessages ":" erro: o provedor de OLE DB solicitado... Não está registrado. Se o driver de 64 bits não estiver instalado, execute o pacote no modo de 32 bits... "

Verifique se o provedor correspondente usado por seus conectores de OLE DB no pacote está instalado no computador Self-Hosted Integration Runtime corretamente. Mais detalhes podem ser encontrados em [configurar Self-Hosted ir como um proxy para Azure-SSIS ir no ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>Mensagem de erro: "erro de tarefa de preparo: ErrorCode: 2906, ErrorMessage: falha na execução do pacote., saída: {" OperationErrorMessages ":" erro: System. IO. FileLoadException: não foi possível carregar o arquivo ou o assembly ' Microsoft. WindowsAzure. Storage, Version =..., Culture = neutral, PublicKeyToken = 31bf3856ad364e35 ' ou uma de suas dependências. A definição do manifesto do assembly localizado não corresponde à referência do assembly. ' ..."

Uma possível causa é que o tempo de execução de integração do Self-Hosted não está instalado ou atualizado corretamente. Sugira o download e reinstale o tempo de execução de integração auto-hospedado mais recente. Mais detalhes podem ser encontrados em [criar e configurar um tempo de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md#installation-best-practices)

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>Mensagem de erro: "uma conexão é necessária ao solicitar metadados. Se você estiver trabalhando offline, desmarque trabalhar offline no menu do SSIS para habilitar a conexão "

* Possível causa e ação recomendada:
  * Se também houver uma mensagem de aviso "o componente não dá suporte ao uso do Gerenciador de conexões com a configuração de valor de ConnectByProxy true" no log de execução, isso significa que um Gerenciador de conexões é usado em um componente que ainda não tem suporte "ConnectByProxy". Os componentes com suporte podem ser encontrados em [configurar Self-Hosted ir como um proxy para Azure-SSIS ir no ADF](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy)
  * O log de execução pode ser encontrado no [relatório do SSMS](/sql/integration-services/performance/monitor-running-packages-and-other-operations#reports) ou na pasta de log especificada na atividade de execução do pacote SSIS.
  * a vNet também pode ser usada para acessar dados locais como uma alternativa. Mais detalhes podem ser encontrados em [unir um tempo de execução de integração do Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>Mensagem de erro: "status da tarefa de preparo: falha. Erro de tarefa de preparo: ErrorCode: 2906, ErrorMessage: falha na execução do pacote., saída: {"OperationErrorMessages": "código de saída do executor do SSIS:-1. \ n", "LogLocation": "... \\ SSISTelemetry \\ ExecutionLog \\ ... "," effectiveIntegrationRuntime ":"... "," executionDuration ":...," durationInQueue ": {" integrationRuntimeQueue ":...}}"

Verifique se Visual C++ tempo de execução está instalado na máquina Self-Hosted Integration Runtime. Mais detalhes podem ser encontrados em [configurar Self-Hosted ir como um proxy para Azure-SSIS ir no ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Várias execuções de pacote são disparadas inesperadamente

* Possível causa e ação recomendada:
  * Atividade de procedimento armazenado do ADF ou atividade de pesquisa são usadas para disparar a execução do pacote SSIS. O comando t-SQL pode atingir um problema transitório e disparar a nova execução, o que causaria várias execuções de pacote.
  * Use a atividade ExecuteSSISPackage em vez disso, que garante que a execução do pacote não seja executada novamente, a menos que o usuário defina a contagem Os detalhes podem ser encontrados em [https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](./how-to-invoke-ssis-package-ssis-activity.md)
  * Refine seu comando t-SQL para poder executar novamente verificando se uma execução já foi disparada

### <a name="package-execution-takes-too-long"></a>A execução do pacote leva muito tempo

Aqui estão as possíveis causas e as ações recomendadas:

* Muitas execuções de pacote foram agendadas no Integration Runtime do SSIS. Todas essas execuções estarão aguardando em uma fila por sua vez.
  * Determine o máximo usando esta fórmula:

    Contagem de execução paralela máxima por IR = contagem de nós * execução paralela máxima por nó
  * Para saber como definir a contagem de nós e a execução paralela máxima por nó, confira [criar um tempo de execução de integração do Azure-SSIS no Azure data Factory](create-azure-ssis-integration-runtime.md).
* O tempo de execução de integração do SSIS foi interrompido ou tem um status não íntegro. Para saber como verificar o status e os erros do tempo de execução de integração do SSIS, consulte [tempo de execução de integração do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Também recomendamos que você defina um tempo limite na guia **geral** : ![ definir propriedades na guia geral ](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png) .

### <a name="poor-performance-in-package-execution"></a>Desempenho insatisfatório na execução do pacote

Tente estas ações:

* Verifique se o Integration Runtime do SSIS está na mesma região que a fonte de dados e o destino.

* Defina o nível de log da execução do pacote como **desempenho** para coletar informações de duração de cada componente na execução. Para obter detalhes, consulte [log do Integration Services (SSIS)](/sql/integration-services/performance/integration-services-ssis-logging).

* Verifique o desempenho do nó IR no portal do Azure:
  * Para obter informações sobre como monitorar o tempo de execução de integração do SSIS, consulte [tempo de execução de integração do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Você pode encontrar o histórico de CPU/memória para o tempo de execução de integração do SSIS exibindo as métricas do data factory no portal do Azure.
    ![Monitorar métricas do Integration Runtime do SSIS](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)