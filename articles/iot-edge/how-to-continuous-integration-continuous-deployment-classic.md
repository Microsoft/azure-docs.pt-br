---
title: Integração contínua e implantação contínua em dispositivos Azure IoT Edge (editor clássico)-Azure IoT Edge
description: Configurar a integração contínua e a implantação contínua usando o editor clássico-Azure IoT Edge com o Azure DevOps, Azure Pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b9b842b94d66cf91ad836b8ae61df1b3d3f34293
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435936"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>Integração contínua e implantação contínua em dispositivos Azure IoT Edge (editor clássico)

Você pode facilmente adotar o DevOps com seus aplicativos do Azure IoT Edge com as tarefas internas do Azure IoT Edge no Azure Pipelines. Este artigo demonstra como você pode usar os recursos de integração contínua e implantação contínua do Azure Pipelines para compilar, testar e implantar aplicativos de forma rápida e eficiente em seu Azure IoT Edge usando o editor clássico. Como alternativa, você pode [usar YAML](how-to-continuous-integration-continuous-deployment.md).

![Diagrama – ramificações de CI e CD para desenvolvimento e produção](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

Neste artigo, você aprenderá a usar as tarefas internas de [Azure IOT Edge](/azure/devops/pipelines/tasks/build/azure-iot-edge) para Azure pipelines criar pipelines de Build e versão para sua solução de IOT Edge. Cada tarefa de Azure IoT Edge adicionada ao seu pipeline implementa uma das quatro ações a seguir:

 | Ação | Descrição |
 | --- | --- |
 | Compilar imagens de módulo | Pega o código da solução IoT Edge e cria as imagens de contêiner.|
 | Imagens do módulo push | Envia imagens de módulo para o registro de contêiner especificado. |
 | Gerar manifesto de implantação | Pega um deployment.template.jsno arquivo e nas variáveis e gera o arquivo de manifesto de implantação final IoT Edge. |
 | Implantar em dispositivos IoT Edge | Cria IoT Edge implantações em um ou mais dispositivos IoT Edge. |

A menos que especificado de outra forma, os procedimentos neste artigo não exploram toda a funcionalidade disponível por meio de parâmetros de tarefa. Para saber mais, consulte o seguinte:

* [Versão da tarefa](/azure/devops/pipelines/process/tasks?tabs=classic#task-versions)
* **Avançado** – se aplicável, especifique os módulos que você não deseja criar.
* [Opções de controle](/azure/devops/pipelines/process/tasks?tabs=classic#task-control-options)
* [Variáveis de ambiente](/azure/devops/pipelines/process/variables?tabs=classic#environment-variables)
* [Variáveis de saída](/azure/devops/pipelines/process/variables?tabs=classic#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Pré-requisitos

* Um repositório do Azure Repos. Se você não tem um, [crie um novo repositório Git no seu projeto](/azure/devops/repos/git/create-new-repo). Neste artigo, criamos um repositório chamado **IoTEdgeRepo**.
* Uma solução do IoT Edge confirmada e enviada para seu repositório. Se você quiser criar uma nova solução de amostra para testar este artigo, siga as etapas em [Desenvolver e depurar módulos no Visual Studio Code](how-to-vs-code-develop-module.md) ou [Desenvolver e depurar módulos C# no Visual Studio](./how-to-visual-studio-develop-module.md). Para este artigo, criamos uma solução em nosso repositório chamada **IoTEdgeSolution**, que tem o código para um módulo chamado **filtermodule**.

   Para este artigo, você só precisa da pasta da solução criada pelos modelos do IoT Edge no Visual Studio Code ou no Visual Studio. Não é necessário compilar, efetuar push, implantar ou depurar esse código antes de continuar. Você configurará esses processos em Azure Pipelines.

   Se você está criando uma nova solução, clone o repositório localmente primeiro. Depois, ao criar a solução, escolha criá-la diretamente na pasta do repositório. É possível confirmar e efetuar push dos novos arquivos facilmente por lá.

* Um registro de contêiner em que você possa efetuar push de imagens de módulo. Você pode usar um [Registro de Contêiner do Azure](../container-registry/index.yml) ou um registro de terceiros.
* Um [Hub IOT](../iot-hub/iot-hub-create-through-portal.md) ativo do Azure com pelo menos dois dispositivos IOT Edge para testar os estágios de implantação de teste e produção separados. Você pode seguir os artigos de início rápido para criar um dispositivo do IoT Edge no [Linux](quickstart-linux.md) ou [Windows](quickstart.md)

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Criar um pipeline de compilação para integração contínua

Nesta seção, você criará um novo pipeline de build. Configure o pipeline para ser executado automaticamente quando você fizer check-in de qualquer alteração no IoT Edge solução de exemplo e publicar logs de compilação.

1. Entre em sua organização do DevOps do Azure ( `https://dev.azure.com/{your organization}` ) e abra o projeto que contém seu repositório de solução de IOT Edge.

    ![Abrir seu projeto do DevOps](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. No menu do painel esquerdo em seu projeto, selecione **pipelines**. Selecione **criar pipeline** no centro da página. Ou, se você já tiver pipelines de compilação, selecione o botão **novo pipeline** no canto superior direito.

    ![Criar um novo pipeline de build](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. Na parte inferior da página **onde está seu código?** , selecione **usar o editor clássico**. Se você quiser usar o YAML para criar pipelines de Build do projeto, consulte o [Guia do YAML](how-to-continuous-integration-continuous-deployment.md).

    ![Selecione usar o editor clássico](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. Siga os prompts para criar o pipeline.

   1. Forneça as informações de origem para o novo pipeline de build. Selecione **Git do Azure Repos** como a origem, depois selecione o projeto, o repositório e o branch em que se encontra o código da solução do IoT Edge. Em seguida, selecione **continuar**.

      ![Selecionar a fonte do pipeline](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. Selecione **Trabalho vazio** ao invés de um modelo.

      ![Iniciar com um trabalho vazio para o pipeline de compilação](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. Após a criação do pipeline, você será direcionado para o editor de pipeline. Aqui, você pode alterar o nome do pipeline, o pool de agentes e a especificação do agente.

   Você pode selecionar um pool hospedado pela Microsoft ou um pool de hospedagem interna que você gerencia.

   Na descrição do seu pipeline, escolha a especificação correta do agente com base na sua plataforma de destino:

   * Se você quiser criar seus módulos na plataforma AMD64 para contêineres do Linux, escolha **Ubuntu-16, 4**

   * Se quiser criar os módulos na plataforma amd64 para contêineres do Windows 1809, você precisará [configurar o agente auto-hospedado no Windows](/azure/devops/pipelines/agents/v2-windows).

   * Se você quiser criar seus módulos na plataforma arm32v7 ou arm64 para contêineres do Linux, precisará [Configurar o agente auto-hospedado no Linux](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent).

    ![Configurar especificação do agente de compilação](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. O pipeline vem pré-configurado com um trabalho chamado **Trabalho do agente 1**. Selecione o sinal de adição ( **+** ) para adicionar quatro tarefas ao trabalho: **Azure IOT Edge** duas vezes, **copiar arquivos** uma vez e **publicar artefatos de compilação** uma vez. Procure cada tarefa e passe o mouse sobre o nome da tarefa para ver o botão **Adicionar** .

   ![Adicionar tarefa do Azure IoT Edge](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   Quando todas as quatro tarefas forem adicionadas, seu trabalho do Agent será semelhante ao exemplo a seguir:

   ![Quatro tarefas no pipeline de compilação](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. Selecione a primeira tarefa **Azure IoT Edge** para editá-la. Essa tarefa compila todos os módulos na solução com a plataforma de destino que você especificar. Edite a tarefa com os seguintes valores:

    | Parâmetro | Descrição |
    | --- | --- |
    | Nome de exibição | O nome de exibição é atualizado automaticamente quando o campo de ação é alterado. |
    | Ação | Selecione **Compilar imagens de módulo**. |
    | .template.jsno arquivo | selecione as reticências (**...**) e navegue até o arquivo **deployment.template.json** no repositório que contém sua solução do IoT Edge. |
    | Plataforma padrão | Selecione o sistema operacional apropriado para seus módulos com base em seu dispositivo de IoT Edge de destino. |
    | Variáveis de saída | Forneça um nome de referência para associar ao caminho do arquivo onde o deployment.jsno arquivo é gerado, como **Edge**. |

   Essas configurações usam o repositório de imagens e a marca que são definidos no `module.json` arquivo para nomear e marcar a imagem do módulo. As **imagens de módulo de compilação** também ajudam a substituir as variáveis pelo valor exato definido no `module.json` arquivo. No Visual Studio ou Visual Studio Code, você está especificando o valor real em um `.env` arquivo. Em Azure Pipelines, defina o valor na guia **variáveis de pipeline** . Selecione a guia **variáveis** no menu do editor de pipeline e configure o nome e o valor da seguinte maneira:

    * **ACR_ADDRESS**: o valor do **servidor de logon** do registro de contêiner do Azure. Você pode recuperar o servidor de Logon da página Visão Geral do seu registro de contêiner no portal do Azure.

    Se você tiver outras variáveis em seu projeto, poderá especificar o nome e o valor nessa guia. as **imagens do módulo de compilação** reconhecem apenas as variáveis que estão no `${VARIABLE}` formato. Certifique-se de usar esse formato em seus `**/module.json` arquivos.

8. Selecione a segunda tarefa **Azure IoT Edge** para editá-la. Essa tarefa efetua push de todas as imagens de módulo para o registro de contêiner selecionado.

    | Parâmetro | Descrição |
    | --- | --- |
    | Nome de exibição | O nome de exibição é atualizado automaticamente quando o campo de ação é alterado. |
    | Ação | Selecione **imagens do módulo Push**. |
    | Tipo de registro de contêiner | Use o tipo padrão: `Azure Container Registry` . |
    | Assinatura do Azure | Escolha sua assinatura. |
    | Registro de Contêiner do Azure | selecione o tipo de registro de contêiner usado para armazenar as imagens de módulo. Dependendo do tipo de registro escolhido, o formulário é alterado. Se você escolher **Registro de Contêiner do Azure**, use as listas suspensas para selecionar a assinatura do Azure e o nome do registro de contêiner. Se você escolher **Registro de Contêiner Genérico**, selecione **Novo** para criar uma conexão de serviço de registro. |
    | .template.jsno arquivo | selecione as reticências (**...**) e navegue até o arquivo **deployment.template.json** no repositório que contém sua solução do IoT Edge. |
    | Plataforma padrão | Selecione o sistema operacional apropriado para seus módulos com base em seu dispositivo de IoT Edge de destino. |
    | Adicionar credencial do registro ao manifesto de implantação | Especifique true para adicionar a credencial do registro para enviar imagens do Docker ao manifesto de implantação. |

   Se você tiver vários registros de contêiner para hospedar suas imagens de módulo, você precisará duplicar essa tarefa, selecionar registro de contêiner diferente e usar **módulo (s) de bypass** nas configurações **avançadas** para ignorar as imagens que não são para esse registro específico.

9. Selecione a tarefa **copiar arquivos** para editá-la. Use essa tarefa para copiar arquivos para o diretório de preparo do artefato.

    | Parâmetro | Descrição |
    | --- | --- |
    | Nome de exibição | Usar o nome padrão ou personalizar |
    | Pasta de origem | A pasta com os arquivos a serem copiados. |
    | Sumário | Adicione duas linhas: `deployment.template.json` e `**/module.json` . Esses dois arquivos servem como entradas para gerar o manifesto de implantação IoT Edge. |
    | Pasta de destino | Especifique a variável `$(Build.ArtifactStagingDirectory)` . Consulte [criar variáveis](/azure/devops/pipelines/build/variables#build-variables) para saber mais sobre a descrição. |

10. Selecione a tarefa **Publicar artefatos de build** para editá-la. Forneça o caminho do diretório de preparo do artefato para a tarefa para que o caminho possa ser publicado no pipeline de liberação.

    | Parâmetro | Descrição |
    | --- | --- |
    | Nome de exibição | Use o nome padrão ou personalizar. |
    | Caminho para publicar | Especifique a variável `$(Build.ArtifactStagingDirectory)` . Consulte [criar variáveis](/azure/devops/pipelines/build/variables#build-variables) para saber mais. |
    | Nome do artefato | Use o nome padrão: **drop** |
    | Local de publicação do artefato | Usar o local padrão: **Azure pipelines** |

11. Abra a guia **Gatilhos** e marque a caixa **Ativar integração contínua**. Verifique se o branch que contém seu código está incluído.

    ![Ativar o gatilho de integração contínua](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. Selecione **salvar** na lista suspensa **salvar & fila** .

Agora esse pipeline está configurado para ser executado automaticamente quando você efetuar push do novo código para seu repositório. A última tarefa, publicar os artefatos do pipeline, dispara um pipeline de lançamento. Acesse a próxima seção para compilar o pipeline de lançamento.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>Se você quiser usar **implantações em camadas** em seu pipeline, as implantações em camadas ainda não têm suporte em Azure IOT Edge tarefas no Azure DevOps.
>
>No entanto, você pode usar uma [tarefa CLI do Azure no Azure DevOps](/azure/devops/pipelines/tasks/deploy/azure-cli) para criar sua implantação como uma implantação em camadas. Para o valor de **script embutido** , você pode usar o [comando AZ IOT Edge Deployment Create](/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment):
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Próximas etapas

* IoT Edge exemplo de práticas recomendadas do DevOps no [iniciador DevOps do Azure para IOT Edge](how-to-devops-starter.md)
* Entenda a implantação do IoT Edge em [Noções básicas sobre implantações do IoT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md)
* Siga as etapas para criar, atualizar ou excluir uma implantação em [Implantar e monitorar os módulos do IoT Edge em larga escala](how-to-deploy-at-scale.md).