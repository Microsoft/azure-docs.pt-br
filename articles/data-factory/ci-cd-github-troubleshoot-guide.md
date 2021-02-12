---
title: Solução de problemas de CI-CD, Azure DevOps e GitHub no ADF
description: Use métodos diferentes para solucionar problemas de CI-CD no ADF.
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 12/03/2020
ms.openlocfilehash: e5e1a4ff676a6677357638dc4b67dc94926adbd2
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556300"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>Solução de problemas de CI-CD, Azure DevOps e GitHub no ADF 

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

Isso ocorre devido a um Integration Runtime com o mesmo nome na fábrica de destino, mas com um tipo diferente. Integration Runtime precisa ser do mesmo tipo durante a implantação.

#### <a name="recommendation"></a>Recomendação

- Consulte estas práticas recomendadas para CI/CD abaixo:

    https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd 
- Os tempos de execução de integração não são alterados com frequência e são semelhantes em todos os estágios em seu CI/CD, portanto Data Factory espera que você tenha o mesmo nome e tipo de tempo de execução de integração em todos os estágios de CI/CD. Se o nome e os tipos & propriedades forem diferentes, certifique-se de corresponder à configuração de IR de origem e de destino e, em seguida, implantar o pipeline de liberação.
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

#### <a name="symptom"></a>Sintoma

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

Você precisa excluir o SSIS-IR e o IRs compartilhado para permitir a operação de movimentação. Se você não quiser excluir o IRs, a melhor maneira é seguir o documento copiar e clonar para fazer a cópia e, depois disso, excluir o data Factory antigo.

###  <a name="unable-to-export-and-import-arm-template"></a>Não é possível exportar e importar o modelo ARM

#### <a name="issue"></a>Problema

Não é possível exportar e importar o modelo ARM. No entanto, nenhum erro estava no Portal. no entanto, você pode ver o seguinte erro:

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>Causa

Você criou uma função de cliente como o usuário e ela não tinha a permissão necessária. Quando a fábrica é carregada na interface do usuário, uma série de valores de controle de exposição para a fábrica é verificada. Nesse caso, a função de acesso do usuário não tem permissão para acessar a API do *queryFeaturesValue* . Para acessar essa API, o recurso de parâmetros globais é desativado. O caminho do código de exportação do ARM está parcialmente contando com o recurso de parâmetros globais.

#### <a name="resolution"></a>Resolução

Para resolver o problema, você precisa adicionar a seguinte permissão à sua função: *Microsoft. datafactory/factories/queryFeaturesValue/Action*. Essa permissão deve ser incluída por padrão na função "colaborador de Data Factory".

## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, experimente os seguintes recursos:

*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum do Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)
