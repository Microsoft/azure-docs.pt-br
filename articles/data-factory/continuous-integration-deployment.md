---
title: Integração e entrega contínuas no Azure Data Factory
description: Aprenda a usar integração e entrega contínuas para mover os pipelines do Data Factory de um ambiente (desenvolvimento, teste, produção) para outro.
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 4f03236176acea14bed2dfaac53b1a1e6cf7a1e2
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103197863"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Integração e entrega contínuas no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Visão geral

Integração contínua é a prática de testar cada alteração feita em sua base de código automaticamente e o mais cedo possível.  A entrega contínua segue o teste que acontece durante a integração contínua e envia as alterações para um sistema de preparo ou produção.

No Azure Data Factory, CI/CD (integração e entrega contínuas) significa mover pipelines do Data Factory de um ambiente (desenvolvimento, teste, produção) para outro. O Azure Data Factory utiliza [modelos do Azure Resource Manager](../azure-resource-manager/templates/overview.md) para armazenar a configuração de suas várias entidades do ADF (pipelines, conjuntos de dados, fluxos e assim por diante). Há dois métodos sugeridos para promover um data factory para outro ambiente:

-    Implantação automatizada usando a integração do Data Factory com o [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines)
-    Carregue manualmente um modelo do Resource Manager usando a integração da UX do Data Factory ao Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>Ciclo de vida de CI/CD

Abaixo há uma visão geral de exemplo do ciclo de vida de CI/CD em um Azure Data Factory configurado com o Git do Azure Repos. Para obter mais informações sobre como configurar um repositório Git, confira [Controle do código-fonte no Azure Data Factory](source-control.md).

1.  Um data factory de desenvolvimento é criado e configurado com o Git do Azure Repos. Todos os desenvolvedores devem ter permissão para criar recursos do Data Factory como pipelines e conjuntos de dados.

1.  Um desenvolvedor [cria um branch de recurso](source-control.md#creating-feature-branches) para fazer uma alteração. Ele depura as execuções do pipeline com as alterações mais recentes. Para obter mais informações sobre como depurar uma execução de pipeline, confira [Desenvolvimento iterativo e depuração com o Azure Data Factory](iterative-development-debugging.md).

1.  Depois que um desenvolvedor estiver satisfeito com suas alterações, ele criará uma solicitação de pull de sua ramificação de recursos para a ramificação principal ou de colaboração para que suas alterações sejam revisadas por colegas.

1.  Depois que uma solicitação de pull é aprovada e as alterações são mescladas no Branch principal, as alterações são publicadas na fábrica de desenvolvimento.

1.  Quando a equipe estiver pronta para implantar as alterações em uma fábrica de teste ou UAT (teste de aceitação do usuário), a equipe vai para o Azure Pipelines versão e implanta a versão desejada da fábrica de desenvolvimento em UAT. Essa implantação ocorre como parte de uma tarefa do Azure Pipelines e usa parâmetros do modelo do Resource Manager para aplicar a configuração apropriada.

1.  Depois que as alterações tiverem sido verificadas no alocador de testes, implante no alocador de produção usando a próxima tarefa da versão de pipelines.

> [!NOTE]
> Somente o alocador de desenvolvimento está associado a um repositório git. Os alocadores de teste e produção não devem ter um repositório git associado a eles e só devem ser atualizados por meio de um pipeline do Azure DevOps ou por meio de um modelo do Resource Manager.

A imagem abaixo realça as diferentes etapas desse ciclo de vida.

![Diagrama de integração contínua com os pipelines do Azure](media/continuous-integration-deployment/continuous-integration-image12.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Automatizar a integração contínua usando versões do Azure Pipelines

A seguir há um guia para configurar uma versão do Azure Pipelines que automatiza a implantação de um data factory em vários ambientes.

### <a name="requirements"></a>Requisitos

-   Uma assinatura do Azure vinculada ao Visual Studio Team Foundation Server ou Azure Repos que usa o [ponto de extremidade de serviço do Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   Um data factory configurado com a integração do Git do Azure Repos.

-   Um [cofre de chaves do Azure](https://azure.microsoft.com/services/key-vault/) que contém os segredos para cada ambiente.

### <a name="set-up-an-azure-pipelines-release"></a>Configurar um lançamento do Azure Pipelines

1.  Em [Azure DevOps](https://dev.azure.com/), abra o projeto configurado com seu data factory.

1.  No lado esquerdo da página, selecione **Pipelines** e, em seguida, selecione **Versões**.

    ![Selecionar Pipelines, Versões](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Selecione **Novo pipeline** ou, se tiver pipelines existentes, selecione **Novo** e, em seguida, **Novo pipeline de lançamento**.

1.  Selecione o modelo **Trabalho vazio**.

    ![Selecionar Trabalho vazio](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Na caixa **Nome da fase**, insira o nome do seu ambiente.

1.  Selecione **Adicionar artefato** e, em seguida, selecione o repositório do git configurado com seu data factory de desenvolvimento. Selecione o [branch de publicação](source-control.md#configure-publishing-settings) do repositório para o **Branch padrão**. Por padrão, esse branch de publicação é `adf_publish`. Para a **Versão padrão**, selecione **Mais recente do branch padrão**.

    ![Adicionar um artefato](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Adicione uma tarefa de Implantação do Azure Resource Manager:

    a.  No modo de exibição de fase, selecione **Exibir tarefas da fase**.

    ![Exibição de fase](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Crie uma tarefa. Pesquise **implantação de modelo ARM** e, em seguida, selecione **Adicionar**.

    c.  Na Tarefa de implantação, selecione a assinatura, o grupo de recursos e a localização do data factory de destino. Forneça as credenciais, se necessário.

    d.  Na lista **Ação**, selecione **Criar ou atualizar grupo de recursos**.

    e.  Selecione o botão de reticências ( **…** ) ao lado da caixa **Modelo**. Procure o modelo do Azure Resource Manager gerado no branch de publicação do repositório do git configurado. Procure o arquivo `ARMTemplateForFactory.json` na pasta <FactoryName> do branch adf_publish.

    f.  Selecione **...** ao lado da caixa **Parâmetros do modelo** para escolher o arquivo de parâmetros. Procure o arquivo `ARMTemplateParametersForFactory.json` na pasta <FactoryName> do branch adf_publish.

    g.  Selecione **...** ao lado da caixa **Substituir parâmetros de modelo** e insira os valores de parâmetro desejados para o data factory de destino. Para as credenciais provenientes do Azure Key Vault, insira o nome do segredo entre aspas duplas. Por exemplo, se o nome do segredo for cred1, insira **"$(cred1)"** para esse valor.

    h. Selecione **Incremental** para o **Modo de implantação**.

    > [!WARNING]
    > No modo de implantação completa, os recursos existentes no grupo de recursos, mas não são especificados no novo modelo do Resource Manager, serão **excluídos**. Para obter mais informações, consulte [Azure Resource Manager modos de implantação](../azure-resource-manager/templates/deployment-modes.md)

    ![Implantação de Produção do Data Factory](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Salve o pipeline de lançamento.

1. Para disparar uma versão, selecione **Criar versão**. Para automatizar a criação de versões, confira [Gatilhos de versão do Azure DevOps](/azure/devops/pipelines/release/triggers)

   ![Selecionar Criar versão](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> Em cenários de CI/CD, o tipo de IR (runtime de integração) em ambientes diferentes deve ser o mesmo. Por exemplo, se você tiver um IR auto-hospedado no ambiente de desenvolvimento, o mesmo IR também deverá ser do tipo auto-hospedado em outros ambientes, como teste e produção. Da mesma forma, se você estiver compartilhando runtimes de integração em várias fases, será necessário configurar os runtimes de integração como auto-hospedados vinculados em todos os ambientes, como desenvolvimento, teste e produção.

### <a name="get-secrets-from-azure-key-vault"></a>Obter segredos do Azure Key Vault

Se você tiver segredos para passar em um modelo do Azure Resource Manager, recomendamos o uso do Azure Key Vault com a versão do Azure Pipelines.

Há duas maneiras de lidar cos segredos:

1.  Adicione os segredos ao arquivo de parâmetros. Para obter mais informações, consulte [Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](../azure-resource-manager/templates/key-vault-parameter.md).

    Crie uma cópia do arquivo de parâmetros carregado para o branch de publicação. Defina os valores dos parâmetros que você deseja obter do Key Vault usando este formato:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    Quando você usa esse método, o segredo é extraído do cofre de chaves automaticamente.

    O arquivo de parâmetros também deve estar no branch de publicação.

1. Adicione uma [tarefa do Azure Key Vault](/azure/devops/pipelines/tasks/deploy/azure-key-vault) antes da tarefa de Implantação do Azure Resource Manager descrita na seção anterior:

    1.  Na guia **Tarefas**, crie uma tarefa. Procure **Azure Key Vault** e adicione-o.

    1.  Na tarefa do Key Vault, selecione a assinatura na qual você criou o cofre de chaves. Forneça as credenciais, se necessário, e selecione o cofre de chaves.

    ![Adicionar uma tarefa do Key Vault](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Conceder permissões ao agente do Azure Pipelines

A tarefa do Azure Key Vault poderá falhar com um erro de Acesso Negado se as permissões corretas não estiverem definidas. Baixe os logs da versão e localize o arquivo .ps1 que contém o comando para conceder permissões ao agente do Azure Pipelines. Você pode executar o comando diretamente. Como alternativa, você pode copiar a ID de entidade de segurança do arquivo e adicionar a política de acesso manualmente no portal do Azure. `Get` e `List` são as permissões mínimas necessárias.

### <a name="updating-active-triggers"></a>Atualizar gatilhos ativos

A implantação poderá falhar se você tentar atualizar gatilhos ativos. Para atualizar os gatilhos ativos, você precisa interrompê-los manualmente e iniciá-los após a implantação. Faça isso usando uma tarefa do Azure PowerShell:

1.  Na guia **Tarefas** da versão, adicione uma tarefa do **Azure PowerShell**. Escolha a versão da tarefa 4.*. 

1.  Selecione a assinatura em que seu factory está.

1.  Selecione **Caminho do Arquivo de Script** como o tipo de script. Isso exige que você salve o script do PowerShell em seu repositório. O seguinte script do PowerShell pode ser usado para parar gatilhos:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

Você pode concluir etapas semelhantes (com a função `Start-AzDataFactoryV2Trigger`) para reiniciar os gatilhos depois da implantação.

A equipe do data factory forneceu um [script pré e pós-implantação de exemplo](#script) localizado na parte inferior deste artigo. 

## <a name="manually-promote-a-resource-manager-template-for-each-environment"></a>Promover manualmente um modelo do Resource Manager para cada ambiente

1. Na lista do **Modelo do ARM**, selecione **Exportar Modelo do ARM** para exportar o modelo do Resource Manager para seu data factory no ambiente de desenvolvimento.

   ![Exportar um modelo do Resource Manager](media/continuous-integration-deployment/continuous-integration-image1.png)

1. Em suas data factories de teste e produção, selecione **Importar Modelo do ARM**. Essa ação leva você até o Portal do Azure, onde você pode importar o modelo exportado. Selecione **Criar seu modelo no editor** para abrir o editor de modelo do Resource Manager.

   ![Criar seu modelo](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Select **Carregar arquivo** e, em seguida, selecione o modelo do Resource Manager gerado. Este é o arquivo **arm_template.json** localizado no arquivo .zip exportado na etapa 1.

   ![Editar modelo](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. Na seção de configurações, insira os valores de configuração, como credenciais de serviço vinculado. Quando tiver terminado, selecione **Comprar** para implantar o modelo do Resource Manager.

   ![Seção de configurações](media/continuous-integration-deployment/continuous-integration-image5.png)

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Usar parâmetros personalizados com o modelo do Resource Manager

Se o seu alocador de desenvolvimento tiver um repositório do git associado, você poderá substituir os parâmetros de modelo do Resource Manager padrão do modelo do Resource Manager gerado pela publicação ou exportação do modelo. Talvez você queira substituir a configuração padrão do parâmetro do Resource Manager nesses cenários:

* Você usa CI/CD automatizadas e deseja alterar algumas propriedades durante a implantação do Resource Manager, mas as propriedades não são parametrizadas por padrão.
* Seu alocador é tão grande que o modelo padrão do Resource Manager é inválido porque ele tem mais do que os parâmetros máximos permitidos (256).

    Para lidar com o limite 256 do parâmetro personalizado, há três opções:    
  
    * Use o arquivo de parâmetro personalizado e remova as propriedades que não precisam de parametrização, ou seja, propriedades que podem manter um valor padrão e, portanto, diminuir a contagem de parâmetros.
    * Refatorar lógica no fluxo de os para reduzir parâmetros, por exemplo, todos os parâmetros de pipeline têm o mesmo valor, você pode usar apenas parâmetros globais em vez disso.
    * Divida um data factory em vários fluxos de dados.

Para substituir a configuração padrão do parâmetro do Resource Manager, vá para o modelo **gerenciar** o Hub e selecione **ARM** na seção "controle do código-fonte". Na seção **configuração do parâmetro ARM** , clique no ícone **Editar** em "Editar configuração de parâmetros" para abrir o editor de código de configuração de parâmetros do Resource Manager.

![Gerenciar parâmetros personalizados](media/author-management-hub/management-hub-custom-parameters.png)

> [!NOTE]
> A **configuração do parâmetro ARM** só está habilitada no "modo git". Atualmente, ele está desabilitado no modo "modo dinâmico" ou "Data Factory".

A criação de uma configuração de parâmetro do Gerenciador de recursos Personalizada cria um arquivo chamado **arm-template-parameters-definition.js** no na pasta raiz do seu Branch git. Você deve usar esse nome de arquivo exato.

![Arquivo de parâmetros personalizado](media/continuous-integration-deployment/custom-parameters.png)

Ao publicar do branch de colaboração, o Data Factory lerá esse arquivo e usará a configuração dele para gerar quais propriedades são parametrizadas. Se nenhum arquivo for encontrado, o modelo padrão será usado.

Ao exportar um modelo do Resource Manager, o Data Factory lê esse arquivo de qualquer Branch na qual você está trabalhando no momento, não a ramificação de colaboração. Você pode criar ou editar o arquivo de um branch privado, no qual você pode testar suas alterações selecionando **Exportar modelo do ARM** na interface do usuário. Em seguida, você pode mesclar o arquivo no branch de colaboração.

> [!NOTE]
> Uma configuração de parâmetro do Gerenciador de recursos personalizada não altera o limite do parâmetro de modelo ARM de 256. Ele permite que você escolha e diminua o número de propriedades parametrizadas.

### <a name="custom-parameter-syntax"></a>Sintaxe do parâmetro personalizado

Veja a seguir algumas diretrizes a serem seguidas ao criar o arquivo de parâmetros personalizados, **arm-template-parameters-definition.json**. O arquivo é composto por uma seção para cada tipo de entidade: gatilho, pipeline, serviço vinculado, conjunto de dados, runtime de integração e fluxo de dados.

* Insira o caminho da propriedade sob o tipo de entidade relevante.
* Definir um nome de propriedade para `*` indicar que você deseja parametrizar todas as propriedades abaixo dele (somente até o primeiro nível, não recursivamente). Você também pode fornecer exceções a essa configuração.
* Definir o valor de uma propriedade como uma cadeia de caracteres indica que você deseja parametrizar a propriedade. Use o formato `<action>:<name>:<stype>`.
   *  `<action>` pode ser um destes caracteres:
      * `=` significa manter o valor atual como o valor padrão para o parâmetro.
      * `-` significa que não mantenha o valor padrão para o parâmetro.
      * `|` é um caso especial para segredos de Azure Key Vault para cadeias de conexão ou chaves.
   * `<name>` é o nome do parâmetro. Se estiver em branco, será usado o nome da propriedade. Se o valor começar com um caractere `-`, o nome será abreviado. Por exemplo, `AzureStorage1_properties_typeProperties_connectionString` seria abreviado como `AzureStorage1_connectionString`.
   * `<stype>` é o tipo de parâmetro. Se `<stype>` estiver em branco, o tipo padrão será `string` . Valores com suporte:,,,, `string` `securestring` `int` `bool` `object` `secureobject` e `array` .
* Especificar uma matriz no arquivo de definição indica que a propriedade correspondente no modelo é uma matriz. O Data Factory itera todos os objetos na matriz usando a definição especificada no objeto do runtime de integração da matriz. O segundo objeto, uma cadeia de caracteres, torna-se o nome da propriedade, que é usada como o nome do parâmetro para cada iteração.
* Uma definição não pode ser específica a uma instância de recurso. Qualquer definição se aplica a todos os recursos desse tipo.
* Por padrão, todas as cadeias de caracteres seguras, como segredos do Key Vault, e cadeias de caracteres seguras, como cadeias de conexão, chaves e tokens, são parametrizadas.
 
### <a name="sample-parameterization-template"></a>Modelo de parametrização de exemplo

Aqui está um exemplo de como seria a aparência de uma configuração de parâmetro do Resource Manager:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Esta é uma explicação de como o modelo anterior é construído, dividido por tipo de recurso.

#### <a name="pipelines"></a>Pipelines
    
* Qualquer propriedade no caminho `activities/typeProperties/waitTimeInSeconds` é parametrizada. Qualquer atividade em um pipeline que tenha uma propriedade de nível de código chamada `waitTimeInSeconds` (por exemplo, a atividade `Wait`) é parametrizada como um número, com um nome padrão. Mas ela não terá um valor padrão no modelo do Resource Manager. Será uma entrada obrigatória durante a implantação do Resource Manager.
* Da mesma forma, uma propriedade chamada `headers` (por exemplo, em uma atividade `Web`) é parametrizada com o tipo `object` (JObject). Ela tem um valor padrão, que é o mesmo valor do alocador de origem.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Todas as propriedades no caminho `typeProperties` são parametrizadas com os respectivos valores padrão. Por exemplo, há duas propriedades nas propriedades do tipo `IntegrationRuntimes`: `computeProperties` e `ssisProperties`. Ambos os tipos de propriedade são criados com os respectivos valores e tipos padrão (Object).

#### <a name="triggers"></a>Gatilhos

* Em `typeProperties`, duas propriedades são parametrizadas. A primeira é `maxConcurrency`, que é especificada para ter um valor padrão e é do tipo`string`. Ela tem o nome de parâmetro padrão `<entityName>_properties_typeProperties_maxConcurrency`.
* A propriedade `recurrence` também é parametrizada. Nela, todas as propriedades nesse nível são especificadas para serem parametrizadas como cadeias de caracteres, com valores padrão e nomes de parâmetro. Uma exceção é a propriedade `interval`, parametrizada como tipo `int`. O nome do parâmetro tem sufixo `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Da mesma forma, a propriedade `freq` é uma cadeia de caracteres e é parametrizada como uma cadeia de caracteres. No entanto, a propriedade `freq` é parametrizada sem um valor padrão. O nome é abreviado e sufixado. Por exemplo, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Os serviços vinculados são exclusivos. Como os serviços vinculados e os conjuntos de linhas têm uma ampla gama de tipos, você pode fornecer uma personalização específica de tipo. Neste exemplo, para todos os serviços vinculados do tipo `AzureDataLakeStore`, um modelo específico será aplicado. Para todos os outros (por meio de `*`), um modelo diferente será aplicado.
* A propriedade `connectionString` será parametrizada como um valor `securestring`. Ela não terá um valor padrão. Ele terá um nome de parâmetro abreviado com o sufixo `connectionString`.
* A propriedade `secretAccessKey` é uma `AzureKeyVaultSecret` (por exemplo, em um serviço vinculado do Amazon S3). Ela é parametrizada automaticamente como um segredo do Azure Key Vault e buscada do cofre de chaves configurada. Você também pode parametrizar o cofre de chaves em si.

#### <a name="datasets"></a>Conjunto de dados

* Embora a personalização específica de tipo esteja disponível para conjuntos de dados, você pode fornecer uma configuração sem ter explicitamente uma configuração de nível \*. No exemplo anterior, todas as propriedades de conjunto de dados em `typeProperties` são parametrizadas.

> [!NOTE]
> Os **alertas e as matrizes do Azure** , se configurados para um pipeline, não têm suporte atualmente como parâmetros para implantações do ARM. Para reaplicar os alertas e as matrizes em um novo ambiente, siga [Data Factory monitoramento, alertas e matrizes.](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#data-factory-metrics)
> 

### <a name="default-parameterization-template"></a>Modelo de parametrização padrão

Veja abaixo o modelo de parametrização padrão atual. Se você precisar adicionar apenas alguns parâmetros, editar esse modelo diretamente pode ser uma boa ideia, pois você não perderá a estrutura de parametrização existente.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "host": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString",
                    "hostKeyFingerprint": "="
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints": {
        "properties": {
            "*": "="
        }
    }
}
```

### <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>Exemplo: parametrizar uma ID de cluster interativa do Azure Databricks existente

O exemplo a seguir mostra como adicionar um valor ao modelo de parametrização padrão. Queremos apenas adicionar uma ID de cluster interativa do Azure Databricks existente para um serviço vinculado do Databricks ao arquivo de parâmetros. Observe que esse arquivo é o mesmo que o arquivo anterior, exceto pela adição de `existingClusterId` no campo de propriedades de `Microsoft.DataFactory/factories/linkedServices`.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Modelos Vinculados do Resource Manager

Se você tiver configurado o CI/CD para seus data factories, poderá exceder os limites de modelo do Azure Resource Manager à medida que sua fábrica aumentar. Por exemplo, um limite é o número máximo de recursos em um modelo do Resource Manager. Para acomodar alocadores maiores ao gerar o modelo completo do Resource Manager para um alocador, o Data Factory agora gera modelos do Resource Manager vinculados. Com esse recurso, todo o conteúdo do alocador é dividido em vários arquivos para que você não fique restrito pelos limites.

Se você configurou o Git, os modelos vinculados são gerados e salvos juntamente com os modelos completos do Resource Manager no branch adf_publish em uma nova pasta chamada linkedTemplates:

![Pasta de modelos vinculados do Resource Manager](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Os modelos vinculados do Resource Manager geralmente são compostos por um modelo mestre e um conjunto de modelos filho vinculados ao mestre. O modelo pai é chamado de ArmTemplate_master.json e os modelos filho são nomeados com o padrão ArmTemplate_0.json, ArmTemplate_1.json e assim por diante. 

Para usar modelos vinculados em vez do modelo completo do Resource Manager, atualize sua tarefa de CI/CD para apontar para o ArmTemplate_master.json em vez do ArmTemplateForFactory.json (o modelo completo do Resource Manager). O Resource Manager também requer que você carregue os modelos vinculados para uma conta de armazenamento para que o Azure possa acessá-los durante a implantação. Para obter mais informações, confira [Implantar modelos do Resource Manager vinculados com o VSTS](/archive/blogs/najib/deploying-linked-arm-templates-with-vsts).

Lembre-se de adicionar os scripts do Data Factory no pipeline de CI/CD antes e depois a tarefa de implantação.

Se o Git não tiver sido configurado, você poderá acessar os modelos vinculados por meio de **Exportar modelo do ARM** na lista de **Modelo do ARM**.

## <a name="hotfix-production-environment"></a>Ambiente de produção de hotfix

Se você implantar um alocador na produção e perceber que há um bug que precisa ser reparado imediatamente, mas você não conseguir implantar o branch de colaboração, talvez você precisará implantar um hotfix. Essa abordagem é conhecida como engenharia de correção rápida ou QFE.

1.    No Azure DevOps, acesse a versão que foi implantada em produção. Localize a última confirmação implantada.

2.    Na mensagem de confirmação, obtenha a ID de confirmação do branch de colaboração.

3.    Crie um branch de hotfix com base nessa confirmação.

4.    Acesse a UX do Azure Data Factory e alterne para o branch de hotfix.

5.    Usando a UX do Azure Data Factory, corrija o bug. Teste suas alterações.

6.    Depois que a correção for verificada, selecione **Exportar Modelo do ARM** para obter o modelo do Resource Manager de hotfix.

7.    Verifique manualmente esse build no branch adf_publish.

8.    Se você tiver configurado o pipeline de lançamento para disparar automaticamente com base em check-ins do adf_publish, uma nova versão será iniciada automaticamente. Caso contrário, enfileire manualmente uma versão.

9.    Implante a versão de hotfix nos alocadores de teste e produção. Essa versão contém o conteúdo de produção anterior e a correção que você fez na etapa 5.

10.   Adicione as alterações do hotfix ao branch de desenvolvimento para que as versões posteriores não incluam o mesmo bug.

Consulte o vídeo abaixo um tutorial em vídeo detalhado sobre como corrigir seus ambientes. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4I7fi]

## <a name="exposure-control-and-feature-flags"></a>Controle de exposição e sinalizadores de recurso

Ao trabalhar em uma equipe, há instâncias em que você pode mesclar alterações, mas não quer que elas sejam executadas em ambientes elevados, como PROD e QA. Para lidar com esse cenário, a equipe do ADF recomenda [o conceito de DevOps de uso de sinalizadores de recursos](/azure/devops/migrate/phase-features-with-feature-flags). No ADF, você pode combinar [parâmetros globais](author-global-parameters.md) e a [atividade If Condition](control-flow-if-condition-activity.md) para ocultar conjuntos de lógica com base nesses sinalizadores de ambiente.

Para saber como configurar um sinalizador de recurso, consulte o tutorial de vídeo abaixo:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IxdW]

## <a name="best-practices-for-cicd"></a>Práticas recomendadas para CI/CD

Se você está usando a integração do Git ao seu data factory e tem um pipeline de CI/CD que migra suas alterações do desenvolvimento para o teste e para a produção, recomendamos estas melhores práticas:

-   **Integração do Git**. Configure somente seu data factory de desenvolvimento com a integração do Git. Alterações no teste e na produção são implantadas por CI/CD e não precisam da integração do Git.

-   **Script pré e pós-implantação**. Antes da etapa de implantação do Resource Manager na CI/CD, você precisa concluir determinadas tarefas, como parar e reiniciar gatilhos e executar a limpeza. Recomendamos que você use scripts do PowerShell antes e depois da tarefa de implantação. Para obter mais informações, confira [Atualizar gatilhos ativos](#updating-active-triggers). A equipe de data factory [forneceu um script](#script) para uso localizado na parte inferior desta página.

-   **Runtimes de integração e compartilhamento**. Os runtimes de integração não são alterados com frequência e são semelhantes em todas as fases em sua CI/CD. Portanto, o Data Factory espera que você tenha o mesmo nome e tipo de runtime de integração em todas as fases da CI/CD. Se desejar compartilhar runtimes de integração em todas as fases, considere usar um alocador ternário apenas para conter os runtimes de integração compartilhados. Você pode usar esse alocador compartilhado em todos os seus ambientes como um tipo de runtime de integração vinculado.

-   **Implantação de ponto de extremidade privada gerenciado**. Se um ponto de extremidade privado já existir em uma fábrica e você tentar implantar um modelo de ARM que contenha um ponto de extremidade privado com o mesmo nome, mas com propriedades modificadas, a implantação falhará. Em outras palavras, você pode implantar com êxito um ponto de extremidade privado, contanto que ele tenha as mesmas propriedades que o que já existe na fábrica. Se qualquer propriedade for diferente entre ambientes, você poderá substituí-la parametrizando essa propriedade e fornecendo o respectivo valor durante a implantação.

-   **Key Vault**. Quando você usa serviços vinculados cujas informações de conexão são armazenadas no Azure Key Vault, é recomendável manter cofres de chaves separados para ambientes diferentes. Você também pode configurar níveis de permissão separados para cada cofre de chaves. Por exemplo, talvez você não queira que os membros da sua equipe tenham permissões para os segredos de produção. Se você seguir essa abordagem, recomendamos que você mantenha os mesmos nomes de segredo em todas as fases. Se você mantiver os mesmos nomes de segredo, não precisará parametrizar cada cadeia de conexão em ambientes de CI/CD porque a única coisa que é alterada é o nome do cofre de chaves, que é um parâmetro separado.

-  **Nomeação de recursos** Devido a restrições de modelo do ARM, podem surgir problemas na implantação se os recursos contiverem espaços no nome. A equipe de Azure Data Factory recomenda usar caracteres ' _ ' ou '-' em vez de espaços para recursos. Por exemplo, ' Pipeline_1 ' seria um nome preferível sobre ' pipeline 1 '.

## <a name="unsupported-features"></a>Recursos sem suporte

- Por design, o Data Factory não permite cherry-picking de confirmações nem a publicação seletiva de recursos. As publicações incluirão todas as alterações feitas no data factory.

    - As entidades do data factory dependem umas das outras. Por exemplo, os gatilhos dependem de pipelines e os pipelines dependem de conjuntos de dados e de outros pipelines. A publicação seletiva de um subconjunto de recursos pode levar a comportamentos e erros inesperados.
    - Em raras ocasiões em que você precisa de publicação seletiva, considere usar um hotfix. Para obter mais informações, consulte [hotfix Production Environment](#hotfix-production-environment).

- A equipe de Azure Data Factory não recomenda atribuir controles RBAC do Azure a entidades individuais (pipelines, conjuntos de valores, etc.) em um data factory. Por exemplo, se um desenvolvedor tiver acesso a um pipeline ou a um conjunto de dados, ele deverá conseguir acessar todos os pipelines ou conjuntos de dados no data factory. Se você achar que precisa implementar muitas funções do Azure em um data factory, examine a implantação de uma segunda data factory.

-   Não é possível publicar de branches particulares.

-   No momento, você não pode hospedar projetos no Bitbucket.

-   Atualmente, não é possível exportar e importar alertas e matrizes como parâmetros. 

## <a name="sample-pre--and-post-deployment-script"></a><a name="script"></a> Script pré e pós-implantação de exemplo

O script de exemplo a seguir pode ser usado para parar os gatilhos antes da implantação e reiniciá-los depois. O script também inclui código para excluir recursos que foram removidos. Salve o script em um repositório do git do Azure DevOps e referencie-o por meio de uma tarefa do Azure PowerShell versão 4.*.

Ao executar um script pré-implantação, você precisará especificar uma variação dos parâmetros a seguir no campo **Argumentos de Script**.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


Ao executar um script pós-implantação, você precisará especificar uma variação dos parâmetros a seguir no campo **Argumentos de Script**.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Tarefa do Azure PowerShell](media/continuous-integration-deployment/continuous-integration-image11.png)

Este é o script que pode ser usado para a pré e a pós-implantação. Ele considera os recursos excluídos e as referências de recursos.

  
```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    if ($trigger.Properties.DependsOn) {
        $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
            triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
        }
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersInTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNamesInTemplate = $triggersInTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}

$triggersDeployed = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

$triggersToStop = $triggersDeployed | Where-Object { $triggerNamesInTemplate -contains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToDelete = $triggersDeployed | Where-Object { $triggerNamesInTemplate -notcontains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToStart = $triggersInTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.name.Substring(37, $_.name.Length-40)
        TriggerType = $_.Properties.type
    }
}

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers`n"
    $triggersToStop | ForEach-Object {
        if ($_.TriggerType -eq "BlobEventsTrigger") {
            Write-Host "Unsubscribing" $_.Name "from events"
            $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Disabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Stopping trigger" $_.Name
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $triggersToDelete | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            if ($_.TriggerType -eq "BlobEventsTrigger") {
                Write-Host "Unsubscribing trigger" $_.Name "from events"
                $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                while ($status.Status -ne "Disabled"){
                    Start-Sleep -s 15
                    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                }
            }
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $triggersToStart | ForEach-Object { 
        if ($_.TriggerType -eq "BlobEventsTrigger") {
            Write-Host "Subscribing" $_.Name "to events"
            $status = Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Enabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Starting trigger" $_.Name
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
```
