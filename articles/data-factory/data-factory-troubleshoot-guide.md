---
title: Solucionar problemas Azure Data Factory | Microsoft Docs
description: Saiba como solucionar problemas de atividades de controle externo no Azure Data Factory.
services: data-factory
author: abnarain
manager: craigg
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 6/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: c76242c176ba4f4c9ffc0d6934f6b645743d77f4
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234575"
---
# <a name="troubleshoot-azure-data-factory"></a>Solucionar problemas Azure Data Factory

Este artigo explora métodos comuns de solução de problemas para atividades de controle externo no Azure Data Factory.

## <a name="azure-databricks"></a>Azure Databricks

| Código do erro | Mensagem de erro                                          | DESCRIÇÃO                             | Recomendações                             |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | Erro 403.                                                    | O token de acesso do databricks expirou.                         | Por padrão, o token de acesso do databricks é válido por 90 dias.  Crie um novo token e atualize o serviço vinculado. |
| 3201           | Campo obrigatório ausente: Settings. Task. notebook_task. notebook_path | Criação inadequada: Caminho do bloco de anotações não especificado corretamente. | Especifique o caminho do bloco de anotações na atividade databricks. |
| 3201           | Cluster... Não existe.                                 | Erro de criação: O cluster do databricks não existe ou foi excluído. | Verifique se o cluster do databricks existe. |
| 3201           | URI de arquivo Python inválido.... Visite o guia do usuário do databricks para obter os esquemas de URI com suporte. | Criação inadequada.                                                | Especifique caminhos absolutos para esquemas de endereçamento de espaço `dbfs:/folder/subfolder/foo.py` de trabalho ou para arquivos armazenados no sistema de arquivos do databricks. |
| 3201           | {0}LinkedService deve ter Domain e accessToken como propriedades obrigatórias. | Criação inadequada.                                                | Verifique a [definição do serviço vinculado](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | {0}LinkedService deve especificar uma ID de cluster existente ou novas informações de cluster para criação. | Criação inadequada.                                                | Verifique a [definição do serviço vinculado](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | Não há suporte para o tipo de nó Standard_D16S_v3. Tipos de nó com suporte:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_ NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2. | Criação inadequada.                                                | Consulte a mensagem de erro.                                          |
| 3201           | Notebook_path inválido:... No momento, há suporte apenas para caminhos absolutos. Os caminhos devem começar com '/'. | Criação inadequada.                                                | Consulte a mensagem de erro.                                          |
| 3202           | Já havia 1000 trabalhos criados nos últimos 3600 segundos, excedendo o limite de taxa:   1000 criações de trabalho por 3600 segundos. | Muitos databricks são executados em uma hora.                         | Verifique todos os pipelines que usam este espaço de trabalho do databricks para sua taxa de criação de trabalho.  Se os pipelines inicializados em excesso de databricks forem executados na agregação, migre alguns pipelines para um novo espaço de trabalho. |
| 3202           | Não foi possível analisar o objeto de solicitação: ' Key ' e ' value ' esperados para serem definidos para o campo de mapa JSON base_parameters, obteve ' Key: "..." ' Stead. | Erro de criação: Nenhum valor fornecido para o parâmetro.         | Inspecione o pipeline JSON e verifique se todos os parâmetros no notebook baseparameters especificam um valor não vazio. |
| 3202           | Usuário: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` não está autorizado a acessar o cluster. | O usuário que gerou o token de acesso não tem permissão para acessar o cluster do databricks especificado no serviço vinculado. | Verifique se o usuário tem as permissões necessárias no espaço de trabalho.   |
| 3203           | O cluster está em estado encerrado, não disponível para receber trabalhos. Corrija o cluster ou tente novamente mais tarde. | O cluster foi encerrado.    Para clusters interativos, isso pode ser uma condição de corrida. | A melhor maneira de evitar esse erro é usar clusters de trabalho.             |
| 3204           | Falha na execução do trabalho.  | Mensagens de erro indicam vários problemas, como um estado de cluster inesperado ou uma atividade específica. Geralmente, nenhuma mensagem de erro é exibida.                                                          | N/D                                                          |



## <a name="azure-data-lake-analytics"></a>Análise Azure Data Lake

A tabela a seguir se aplica ao U-SQL.

| Código do erro         | Mensagem de erro                                                | DESCRIÇÃO                                          | Recomendações                            |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | O token de acesso é do locatário incorreto.                    | Locatário incorreto do Azure Active Directory (AD do Azure).                                         | A entidade de serviço usada para acessar o Azure Data Lake Analytics pertence a outro locatário do Azure AD. Crie uma nova entidade de serviço no mesmo locatário que a conta de Data Lake Analytics. |
| 2711,   2705,   2704 | Negado. Falha na verificação da ACL. Ou o recurso não existe ou o usuário não está autorizado a executar a operação solicitada.<br/><br/>O usuário não consegue acessar Data Lake Store.  <br/><br/>O usuário não está autorizado a usar Data Lake Analytics. | A entidade de serviço ou o certificado não tem acesso ao arquivo no armazenamento. | Verifique se a entidade de serviço ou o certificado que o usuário fornece para Data Lake Analytics trabalhos tem acesso à conta de Data Lake Analytics e à instância de Data Lake Storage padrão da pasta raiz. |
| 2711                 | Não é possível localizar o arquivo ou a pasta ' Azure Data Lake Store '.       | O caminho para o arquivo U-SQL está errado ou as credenciais do serviço vinculado não têm acesso. | Verifique o caminho e as credenciais fornecidas no serviço vinculado. |
| 2707                 | Não é possível resolver a conta de AzureDataLakeAnalytics. Verifique ' AccountName ' e ' DataLakeAnalyticsUri '. | A conta de Data Lake Analytics no serviço vinculado está incorreta.                  | Verifique se a conta certa é fornecida.             |
| 2703                 | ID do erro: E_CQO_SYSTEM_INTERNAL_ERROR (ou qualquer erro que comece com "ID do erro:"). | O erro é de Data Lake Analytics.                                    | Um erro como o exemplo significa que o trabalho foi enviado para Data Lake Analytics, e o script falhou. Investigue no Data Lake Analytics. No portal, vá para a conta de Data Lake Analytics e procure o trabalho usando a ID de execução de atividade de Data Factory (não a ID de execução de pipeline). O trabalho fornece mais informações sobre o erro e irá ajudá-lo a solucionar problemas. Se a resolução não estiver clara, entre em contato com a equipe de suporte do Data Lake Analytics e forneça a URL do trabalho, que inclui o nome da conta e a ID do trabalho. |
| 2709                 | Não podemos aceitar seu trabalho neste momento. O número máximo de trabalhos em fila para sua conta é 200. | Esse erro é causado pela limitação no Data Lake Analytics.                                           | Reduza o número de trabalhos enviados para Data Lake Analytics alterando gatilhos de Data Factory e configurações de simultaneidade em atividades. Ou aumente os limites no Data Lake Analytics. |
| 2709                 | Este trabalho foi rejeitado porque requer 24 AUs. A política definida pelo administrador da conta impede que um trabalho use mais de 5 AUs. | Esse erro é causado pela limitação no Data Lake Analytics.                                           | Reduza o número de trabalhos enviados para Data Lake Analytics alterando gatilhos de Data Factory e configurações de simultaneidade em atividades. Ou aumente os limites no Data Lake Analytics. |



## <a name="azure-functions"></a>Azure Functions

| Código do erro | Mensagem de erro                           | DESCRIÇÃO                                                  | Recomendações                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | O conteúdo da resposta não é um JObject válido. | A função do Azure que foi chamada não retornou uma carga JSON na resposta. A atividade de funções do Azure no Data Factory dá suporte apenas ao conteúdo de resposta JSON. | Atualize a função do Azure para retornar uma carga JSON válida. Por exemplo, uma C# função pode retornar `(ActionResult)new<OkObjectResult("{` \"a\"ID\":\"123`}");`. |
| 3600         | HttpMethod inválido: '... '.               | O método HTTP especificado na carga da atividade não é suportado pela atividade da função do Azure. | Use um método HTTP com suporte, como PUT, POST, GET, DELETE, opções, HEAD ou TRACE. |



## <a name="custom"></a>Personalizado

A tabela a seguir se aplica ao lote do Azure.

| Código do erro | Mensagem de erro                                                | DESCRIÇÃO                                                  | Recomendações                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | Clique em exceção inesperada e a execução falhou.             | Não é possível iniciar o comando ou o programa retornou um código de erro. | Verifique se o arquivo executável existe. Se o programa foi iniciado, certifique-se de que *stdout. txt* e *stderr. txt* foram carregados para a conta de armazenamento. É uma boa prática emitir logs de grandes em seu código para depuração. |
| 2501         | Não é possível acessar a conta do lote do usuário; Verifique as configurações da conta do lote. | Chave de acesso do lote ou nome do pool incorreto.            | Verifique o nome do pool e a chave de acesso do lote no serviço vinculado. |
| 2502         | Não é possível acessar a conta de armazenamento do usuário; Verifique as configurações da conta de armazenamento. | Nome da conta de armazenamento ou chave de acesso incorreto.       | Verifique o nome da conta de armazenamento e a chave de acesso no serviço vinculado. |
| 2504         | A operação retornou um código de status inválido ' BadRequest '.     | Muitos arquivos no folderPath da atividade personalizada. O tamanho total de resourceFiles não pode ter mais de 32.768 caracteres. | Remover arquivos desnecessários. Ou compactá-los e adicionar um comando de descompactação para extraí-los. Por exemplo, use`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe` |
| 2505         | Não é possível criar assinatura de acesso compartilhado, a menos que sejam usadas credenciais de chave de conta. | As atividades personalizadas dão suporte apenas a contas de armazenamento que usam uma tecla de acesso. | Consulte a descrição do erro.                                            |
| 2507         | O caminho da pasta não existe ou está vazio:....            | Nenhum arquivo está na conta de armazenamento no caminho especificado.       | O caminho da pasta deve conter os arquivos executáveis que você deseja executar. |
| 2508         | Há arquivos duplicados na pasta de recursos.               | Vários arquivos com o mesmo nome estão em subpastas diferentes de folderPath. | Estrutura de pastas achatada de atividades personalizadas em folderPath.  Se você precisar preservar a estrutura de pastas, compacte os arquivos e extraia-os no lote do Azure usando um comando unzip. Por exemplo, use`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe` |
| 2509         | URL do lote... é inválido; Ele deve estar no formato URI.         | As URLs do lote devem ser semelhantes a`https://mybatchaccount.eastus.batch.azure.com` | Consulte a descrição do erro.                                            |
| 2510         | Ocorreu um erro ao enviar a solicitação.               | A URL do lote é inválida.                                         | Verifique a URL do lote.                                            |

## <a name="hdinsight"></a>HDInsight

A tabela a seguir aplica-se ao Spark, Hive, MapReduce, Pig e streaming do Hadoop.

| Código de erro | Mensagem de erro                                                | DESCRIÇÃO                                                  | Recomendações                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300,   2310 | Falha no envio do trabalho do Hadoop. Erro: O nome remoto não pôde ser resolvido. <br/><br/>O cluster não foi encontrado. | O URI de cluster fornecido é inválido.                              | Verifique se o cluster não foi excluído e se o URI fornecido está correto. Ao abrir o URI em um navegador, você deverá ver a interface do usuário do amAmbari. Se o cluster estiver em uma rede virtual, o URI deverá ser o URI privado. Para abri-lo, use uma VM que faça parte da mesma rede virtual. Para obter mais informações, consulte [conectar-se diretamente a serviços de Apache Hadoop](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services). |
| 2\.300         | Falha no envio do trabalho do Hadoop. Trabalho:..., cluster:.../. Erro: Uma tarefa foi cancelada. | O envio do trabalho atingiu o tempo limite.                         | O problema pode ser a conectividade geral do HDInsight ou a conectividade de rede. Primeiro, confirme se a interface do usuário do HDInsight Ambari está disponível em qualquer navegador. Confirme se suas credenciais ainda são válidas. Se você estiver usando o IR (tempo de execução integrado) auto-hospedado, certifique-se de fazer isso na VM ou computador em que o IR auto-hospedado está instalado. Em seguida, tente enviar o trabalho de Data Factory novamente. Se ainda falhar, contate a equipe de Data Factory para obter suporte. |
| 2\.300         | Não autorizado   O nome de usuário ou a senha do Ambari está incorreto  <br/><br/>Não autorizado   O administrador do usuário está bloqueado no Ambari.   <br/><br/>403-Proibido: Acesso negado. | As credenciais do HDInsight estão incorretas ou expiradas. | Corrija as credenciais e reimplante o serviço vinculado. Primeiro, verifique se as credenciais funcionam no HDInsight abrindo o URI do cluster em qualquer navegador e tentando entrar. Se as credenciais não funcionarem, você poderá redefini-las do portal do Azure. |
| 2300,   2310 | 502 – O servidor Web recebeu uma resposta inválida ao atuar como gateway ou servidor proxy.       <br/>Gateway inadequado. | Este erro é do HDInsight.                               | Esse erro é do cluster HDInsight. Para obter mais informações, consulte [erro do Ambari UI 502](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 erros ao se conectar ao servidor spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502 erros ao se conectar ao servidor Spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)e [solucionar erros de gateway inválidos no gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502). |
| 2\.300         | Falha no envio do trabalho do Hadoop. Trabalho:..., cluster:... Erro: {\"Error\":\"não é possível atender à solicitação de envio do trabalho, pois o serviço Templeton está ocupado com muitas solicitações de trabalho de envio. Aguarde algum tempo antes de repetir a operação. Consulte a configuração Templeton. parallellism. Job. Submit para configurar solicitações simultâneas.  <br/><br/>Falha no envio do trabalho do Hadoop. Trabalho 161da5d4-6fa8-4ef4-a240-6b6428c5ae2f, Cluster: `https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/`.   Erro: {\"erro\":\"Java. IO. IOException: org. Apache. Hadoop. yarn. Exceptions. YarnException: Falha ao enviar application_1561147195099_3730 para YARN: org. Apache. Hadoop. Security. AccessControlexception: A fila raiz. joblauncher já tem 500 aplicativos, não é possível aceitar o envio do aplicativo: application_1561147195099_3730 \ | Muitos trabalhos estão sendo enviados ao HDInsight ao mesmo tempo. | Considere limitar o número de trabalhos simultâneos enviados ao HDInsight. Consulte Data Factory a simultaneidade de atividade se os trabalhos estiverem sendo enviados pela mesma atividade. Altere os gatilhos para que as execuções de pipeline simultâneas sejam distribuídas com o passar do tempo. Consulte a documentação do HDInsight para `templeton.parallellism.job.submit` ajustar conforme o erro sugere. |
| 2303,   2347 | O trabalho do Hadoop falhou com o código de saída ' 5 '. Consulte 'wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' para obter mais detalhes.  <br/><br/>Falha na execução do hive com o código de erro ' UserErrorHiveOdbcCommandExecutionFailure '.   Consulte 'wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' para obter mais detalhes. | O trabalho foi enviado para o HDInsight e falhou no HDInsight. | O trabalho foi enviado para o HDInsight com êxito. Ele falhou no cluster. Abra o trabalho e os logs na interface do usuário do HDInsight Ambari ou abra o arquivo do armazenamento, como sugere a mensagem de erro. O arquivo mostra os detalhes do erro. |
| 2328         | Erro interno do servidor ao processar a solicitação. Repita a solicitação ou contate o suporte. | Esse erro ocorre no HDInsight sob demanda.                              | Esse erro é proveniente do serviço do HDInsight quando o provisionamento do HDInsight falha. Entre em contato com a equipe do HDInsight e forneça o nome do cluster sob demanda. |
| 2310         | java.lang.NullPointerException                               | Esse erro ocorre quando o trabalho é enviado a um cluster Spark.      | Essa exceção é proveniente do HDInsight. Ele oculta o problema real. Entre em contato com a equipe do HDInsight para obter suporte. Forneça o nome do cluster e o intervalo de tempo de execução da atividade. |
|              | Todos os outros erros                                             |                                                              | Consulte [solucionar problemas usando](../hdinsight/hdinsight-troubleshoot-guide.md) as [perguntas frequentes](https://hdinsight.github.io/)do hdinsight e do hdinsight. |



## <a name="web-activity"></a>Atividade da Web

| Código do erro | Mensagem de erro                                                | DESCRIÇÃO                                                  | Recomendações                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | HttpMethod inválido: '... '.                                    | A atividade da Web não dá suporte ao método HTTP especificado na carga da atividade. | Os métodos HTTP com suporte são PUT, POST, GET e DELETE. |
| 2108         | Erro de servidor inválido 500.                                     | Erro interno no ponto de extremidade.                               | Use o Fiddler ou o postmaster para verificar a funcionalidade na URL. |
| 2108         | Não autorizado 401.                                             | Autenticação válida ausente na solicitação.                      | O token pode ter expirado. Forneça um método de autenticação válido. Use o Fiddler ou o postmaster para verificar a funcionalidade na URL. |
| 2108         | Proibido 403.                                                | Permissões necessárias ausentes.                                 | Verifique as permissões de usuário no recurso acessado. Use o Fiddler ou o postmaster para verificar a funcionalidade na URL.  |
| 2108         | Solicitação inadequada 400.                                              | Solicitação HTTP inválida.                                         | Verifique a URL, o verbo e o corpo da solicitação. Use o Fiddler ou o postmaster para validar a solicitação.  |
| 2108         | Não encontrado 404.                                                | O recurso não foi encontrado.                                       | Use o Fiddler ou o postmaster para validar a solicitação.  |
| 2108         | Serviço indisponível.                                          | O serviço está indisponível.                                       | Use o Fiddler ou o postmaster para validar a solicitação.  |
| 2108         | Tipo de mídia sem suporte.                                       | O tipo de conteúdo não corresponde ao corpo da atividade da Web.           | Especifique o tipo de conteúdo que corresponde ao formato da carga. Use o Fiddler ou o postmaster para validar a solicitação. |
| 2108         | O recurso que você está procurando foi removido, teve seu nome alterado ou está temporariamente indisponível. | O recurso não está disponível.                                | Use o Fiddler ou o postmaster para verificar o ponto de extremidade. |
| 2108         | A página que você está procurando não pode ser exibida porque um método inválido (verbo HTTP) está sendo usado. | Um método de atividade da Web incorreto foi especificado na solicitação.   | Use o Fiddler ou o postmaster para verificar o ponto de extremidade. |
| 2108         | invalid_payload                                              | O corpo da atividade da Web está incorreto.                       | Use o Fiddler ou o postmaster para verificar o ponto de extremidade. |

Para usar o Fiddler para criar uma sessão HTTP do aplicativo Web monitorado:

1. Baixe, instale e abra o [Fiddler](https://www.telerik.com/download/fiddler).

1. Se seu aplicativo Web usar HTTPS, vá para **ferramentas** > **Opções** > do Fiddler**https**. Selecione **capturar conexões HTTPS** e **descriptografar tráfego HTTPS**. 
   
   ![Opções do Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Se seu aplicativo usar certificados SSL, adicione o certificado Fiddler ao seu dispositivo. Acesse **ferramentas** > **Fiddler opções** > **https** **ações** **Exportar certificado raiz para área de trabalho.**  >  > 

1. Desative a captura indo para o**tráfego de captura**de **arquivo** > . Ou pressione **F12**.

1. Limpe o cache do navegador para que todos os itens em cache sejam removidos e precisem ser baixados novamente.

1. Criar uma solicitação: 

   a. Selecione a guia **compositor** .

   b. Defina o método e a URL HTTP.

   c. Adicione cabeçalhos e um corpo de solicitação, se necessário.

   d. Selecione **Executar**.

9. Ative a captura de tráfego novamente e conclua a transação problemática em sua página.

10. Vá para **arquivo** > **salvar** > **todas as sessões**.

Para obter mais informações, consulte [introdução ao Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda para solução de problemas, Experimente estes recursos:

*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory solicitações de recursos](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow Fórum para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)



