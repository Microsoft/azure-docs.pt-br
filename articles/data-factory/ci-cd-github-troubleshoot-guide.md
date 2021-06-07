---
title: Solucionar problemas de CI-CD, DevOps do Azure e problemas do GitHub no ADF
description: Use métodos diferentes para solucionar problemas de CI-CD no ADF.
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 03/12/2021
ms.openlocfilehash: 2b6f97f0966cb2c92dbd88c4a70188282ed3ed27
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802026"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>Solucionar problemas de CI-CD, DevOps do Azure e problemas do GitHub no ADF 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de solução de problemas para implantação contínua de Integration-Continuous (CI-CD), problemas do Azure DevOps e do GitHub no Azure Data Factory.

Se você tiver dúvidas ou problemas ao usar o controle do código-fonte ou as técnicas de DevOps, aqui estão alguns artigos que podem ser úteis:

- Consulte [controle do código-fonte no ADF](source-control.md) para saber como o controle do código-fonte é praticado no ADF. 
- Consulte  [CI-CD no ADF](continuous-integration-deployment.md) para saber mais sobre como o DevOps CI-CD é praticado no ADF.
 
## <a name="common-errors-and-messages"></a>Erros e mensagens comuns

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>Falha ao conectar-se ao repositório git devido a um locatário diferente

#### <a name="issue"></a>Problema
    
Às vezes, você encontra problemas de autenticação como o status HTTP 401. Especialmente quando você tem vários locatários com a conta de convidado, as coisas podem se tornar mais complicadas.

#### <a name="cause"></a>Causa

O que observamos é que o token foi obtido do locatário original, mas o ADF está no locatário convidado e tentando usar o token para visitar DevOps no locatário convidado. Esse não é o comportamento esperado.

#### <a name="recommendation"></a>Recomendação

Em vez disso, você deve usar o token emitido pelo locatário do convidado. Por exemplo, você precisa atribuir o mesmo Azure Active Directory para ser seu locatário convidado, bem como seu DevOps, para que possa definir corretamente o comportamento do token e usar o locatário correto.

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>Os parâmetros de modelo no arquivo de parâmetros não são válidos

#### <a name="issue"></a>Problema

Se excluirmos um gatilho na ramificação de desenvolvimento, que já está disponível no teste ou Branch de produção com a **mesma** configuração (como frequência e intervalo), a implantação do pipeline de liberação será realizada com sucesso e o gatilho correspondente serão excluídos nos respectivos ambientes. Mas se você tiver uma configuração **diferente** (como frequência e intervalo) para o gatilho em ambientes de teste/produção e se excluir o mesmo gatilho em desenvolvimento, a implantação falhará com um erro.

#### <a name="cause"></a>Causa

O pipeline de CI/CD falha com o seguinte erro:

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>Recomendação

O erro ocorre porque muitas vezes excluímos um gatilho, que é parametrizado, portanto, os parâmetros não estarão disponíveis no modelo ARM (porque o gatilho não existe mais). Como o parâmetro não está mais no modelo ARM, precisamos atualizar os parâmetros substituídos no pipeline DevOps. Caso contrário, cada vez que os parâmetros no modelo ARM forem alterados, eles deverão atualizar os parâmetros substituídos no pipeline DevOps (na tarefa de implantação).

### <a name="updating-property-type-is-not-supported"></a>Não há suporte para a atualização do tipo de propriedade

#### <a name="issue"></a>Problema

Falha no pipeline de liberação de CI/CD com o seguinte erro:

`
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
`

#### <a name="cause"></a>Causa

Isso ocorre devido a um tempo de execução de integração com o mesmo nome na fábrica de destino, mas com um tipo diferente. Integration Runtime precisa ser do mesmo tipo durante a implantação.

#### <a name="recommendation"></a>Recomendação

- Consulte estas práticas recomendadas para CI/CD abaixo:

    https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd 
- Os tempos de execução de integração não são alterados com frequência e são semelhantes em todos os estágios em seu CI/CD, portanto Data Factory espera que você tenha o mesmo nome e tipo de tempo de execução de integração em todos os estágios de CI/CD. Se o nome e os tipos & propriedades forem diferentes, certifique-se de corresponder a configuração de tempo de execução de integração de origem e destino e, em seguida, implante o pipeline de liberação.
- Se desejar compartilhar runtimes de integração em todas as fases, considere usar um alocador ternário apenas para conter os runtimes de integração compartilhados. Você pode usar esse alocador compartilhado em todos os seus ambientes como um tipo de runtime de integração vinculado.

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>Falha na criação ou atualização do documento devido à referência inválida

#### <a name="issue"></a>Problema

Ao tentar publicar alterações em um Data Factory, você receberá a seguinte mensagem de erro:

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`
### <a name="cause"></a>Causa

Você desanexou a configuração do git e a configurou novamente com o sinalizador "importar recursos" selecionado, que define o Data Factory como "em sincronia". Isso significa que não há alterações para publicar.

#### <a name="resolution"></a>Resolução

Desanexe a configuração do git e configure-a novamente e certifique-se de não marcar a caixa de seleção "importar recursos existentes".

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>Falha na movimentação de Data Factory de um grupo de recursos para outro

#### <a name="issue"></a>Problema

Não é possível mover Data Factory de um grupo de recursos para outro, falhando com o seguinte erro:

`
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>Resolução

Você precisa excluir o SSIS-IR e o IRs compartilhado para permitir a operação de movimentação. Se você não quiser excluir os tempos de execução de integração, a melhor maneira é seguir o documento copiar e clonar para fazer a cópia e, depois disso, excluir o Data Factory antigo.

###  <a name="unable-to-export-and-import-arm-template"></a>Não é possível exportar e importar o modelo ARM

#### <a name="issue"></a>Problema

Não é possível exportar e importar o modelo ARM. No entanto, nenhum erro estava no Portal. no entanto, você pode ver o seguinte erro:

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>Causa

Você criou uma função de cliente como o usuário e ela não tinha a permissão necessária. Quando a fábrica é carregada na interface do usuário, uma série de valores de controle de exposição para a fábrica é verificada. Nesse caso, a função de acesso do usuário não tem permissão para acessar a API do *queryFeaturesValue* . Para acessar essa API, o recurso de parâmetros globais é desativado. O caminho do código de exportação do ARM está parcialmente contando com o recurso de parâmetros globais.

#### <a name="resolution"></a>Resolução

Para resolver o problema, você precisa adicionar a seguinte permissão à sua função: *Microsoft. datafactory/factories/queryFeaturesValue/Action*. Essa permissão deve ser incluída por padrão na função "colaborador de Data Factory".

###  <a name="cannot-automate-publishing-for-cicd"></a>Não é possível automatizar a publicação para CI/CD 

#### <a name="cause"></a>Causa

Até recentemente, apenas a maneira de publicar o pipeline do ADF para implantações estava usando o botão do portal do ADF. Agora, você pode tornar o processo automático. 

#### <a name="resolution"></a>Resolução

O processo de CI/CD foi aprimorado. O recurso de **publicação automatizada** pega, valida e exporta todos os recursos de modelo do Azure Resource Manager (ARM) da UX do ADF. Ele torna a lógica consumível por meio de um pacote NPM publicamente disponível [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . Isso permite que você acione programaticamente essas ações em vez de ter que ir para a interface do usuário do ADF e fazer um clique de botão. Isso dá aos pipelines de CI/CD uma experiência de integração contínua **verdadeira** . Siga as [melhorias de publicação de CI/CD do ADF](./continuous-integration-deployment-improvements.md) para obter detalhes. 

###  <a name="cannot-publish-because-of-4mb-arm-template-limit"></a>Não é possível publicar devido a um limite de modelo de 4 MB  

#### <a name="issue"></a>Problema

Não é possível implantar porque você atingiu Azure Resource Manager limite de tamanho total do modelo de 4 MB. Você precisa de uma solução para implantar depois de cruzar o limite. 

#### <a name="cause"></a>Causa

Azure Resource Manager restringe o tamanho do modelo a ser 4 MB. Limite o tamanho do modelo para 4 MB e cada arquivo de parâmetro para 64 KB. O limite de 4 MB se aplica ao estado final do modelo depois que ele é expandido com definições de recursos iterativos e valores para variáveis e parâmetros. Mas, você ultrapassou o limite. 

#### <a name="resolution"></a>Resolução

Para pequenas e médias soluções, um único modelo é mais fácil de entender e manter. Você pode ver todos os recursos e valores em um único arquivo. Para cenários avançados, os modelos vinculados permitem que você detalhe a solução em componentes de destino. Siga a prática recomendada em [usando modelos vinculados e aninhados](../azure-resource-manager/templates/linked-templates.md?tabs=azure-powershell).

### <a name="cannot-connect-to-git-enterprise"></a>Não é possível se conectar ao GIT Enterprise  

##### <a name="issue"></a>Problema

Não é possível se conectar ao GIT Enterprise devido a problemas de permissão. Você pode ver um erro como **422-entidade não processável.**

#### <a name="cause"></a>Causa

* Você não configurou o OAuth para ADF. 
* A URL está configurada incorretamente.

##### <a name="resolution"></a>Resolução

Você concede acesso OAuth ao ADF primeiro. Em seguida, você precisa usar a URL correta para se conectar ao GIT Enterprise. A configuração deve ser definida para as organizações do cliente. Por exemplo, o ADF tentará *https://hostname/api/v3/search/repositories?q=user%3 <customer credential> ..* . primeiro e falhará. Em seguida, ele tentará *https://hostname/api/v3/orgs/ <org> / <repo> ...* e terá sucesso. 
 
### <a name="cannot-recover-from-a-deleted-data-factory"></a>Não é possível recuperar de um data factory excluído

#### <a name="issue"></a>Problema
O cliente excluiu o data Factory ou o grupo de recursos que contém o Data Factory. Ele gostaria de saber como restaurar um data factory excluído.

#### <a name="cause"></a>Causa

É possível recuperar o Data Factory somente se o cliente tiver o controle do código-fonte configurado (DevOps ou git). Isso trará todos os recursos publicados mais recentes e **não** restaurará o pipeline não publicado, o conjunto de informações e o serviço vinculado.

Se não houver nenhum controle do código-fonte, a recuperação de um Data Factory excluído do back-end não será possível porque, uma vez que o serviço recebe o comando excluído, a instância é excluída e nenhum backup foi armazenado.

#### <a name="resolution"></a>Resolução

Para recuperar o Data Factory excluído que tem o controle do código-fonte, consulte as etapas abaixo:

 * Crie um novo Azure Data Factory.

 * Reconfigure o Git com as mesmas configurações, mas certifique-se de importar recursos existentes do Data Factory para o repositório selecionado e escolha novo Branch.

 * Crie uma solicitação de pull para mesclar as alterações para a ramificação de colaboração e publicar.

 * Se o cliente tivesse um Integration Runtime autohospedado no ADF excluído, ele precisará criar uma nova instância no ADF novo, além de desinstalar e reinstalar a instância em sua máquina/VM local com a nova chave obtida. Depois que a configuração do IR for concluída, o cliente precisará alterar o serviço vinculado para apontar para o novo IR e testar a conexão, ou ele falhará com a referência de erro **inválida.**



## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, experimente os seguintes recursos:

*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum do Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)
