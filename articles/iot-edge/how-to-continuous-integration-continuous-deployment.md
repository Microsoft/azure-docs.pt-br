---
title: Integração contínua e implantação contínua em dispositivos Azure IoT Edge-Azure IoT Edge
description: Configurar a integração contínua e a implantação contínua usando o YAML-Azure IoT Edge com o DevOps do Azure, Azure Pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d9db9997af20fee226214eb12ad32729cab55caa
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199244"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices"></a>Integração contínua e implantação contínua em dispositivos Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Você pode facilmente adotar o DevOps com seus aplicativos do Azure IoT Edge com as tarefas internas do Azure IoT Edge no Azure Pipelines. Este artigo demonstra como você pode usar os recursos de integração contínua e implantação contínua do Azure Pipelines para compilar, testar e implantar aplicativos de forma rápida e eficiente em seu Azure IoT Edge usando o YAML. Como alternativa, você pode [usar o editor clássico](how-to-continuous-integration-continuous-deployment-classic.md).

![Diagrama – ramificações de CI e CD para desenvolvimento e produção](./media/how-to-continuous-integration-continuous-deployment/model.png)

Neste artigo, você aprenderá a usar as tarefas internas de [Azure IOT Edge](/azure/devops/pipelines/tasks/build/azure-iot-edge) para Azure pipelines criar pipelines de Build e versão para sua solução de IOT Edge. Cada tarefa de Azure IoT Edge adicionada ao seu pipeline implementa uma das quatro ações a seguir:

 | Ação | Descrição |
 | --- | --- |
 | Compilar imagens de módulo | Pega o código da solução IoT Edge e cria as imagens de contêiner.|
 | Imagens do módulo push | Envia imagens de módulo para o registro de contêiner especificado. |
 | Gerar manifesto de implantação | Pega um deployment.template.jsno arquivo e nas variáveis e gera o arquivo de manifesto de implantação final IoT Edge. |
 | Implantar em dispositivos IoT Edge | Cria IoT Edge implantações em um ou mais dispositivos IoT Edge. |

A menos que especificado de outra forma, os procedimentos neste artigo não exploram toda a funcionalidade disponível por meio de parâmetros de tarefa. Para saber mais, consulte o seguinte:

* [Versão da tarefa](/azure/devops/pipelines/process/tasks?tabs=yaml#task-versions)
* **Avançado** – se aplicável, especifique os módulos que você não deseja criar.
* [Opções de controle](/azure/devops/pipelines/process/tasks?tabs=yaml#task-control-options)
* [Variáveis de ambiente](/azure/devops/pipelines/process/variables?tabs=yaml#environment-variables)
* [Variáveis de saída](/azure/devops/pipelines/process/variables?tabs=yaml#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Pré-requisitos

* Um repositório do Azure Repos. Se você não tem um, [crie um novo repositório Git no seu projeto](/azure/devops/repos/git/create-new-repo). Neste artigo, criamos um repositório chamado **IoTEdgeRepo**.
* Uma solução do IoT Edge confirmada e enviada para seu repositório. Se você quiser criar uma nova solução de amostra para testar este artigo, siga as etapas em [Desenvolver e depurar módulos no Visual Studio Code](how-to-vs-code-develop-module.md) ou [Desenvolver e depurar módulos C# no Visual Studio](./how-to-visual-studio-develop-module.md). Para este artigo, criamos uma solução em nosso repositório chamada **IoTEdgeSolution**, que tem o código para um módulo chamado **filtermodule**.

   Para este artigo, você só precisa da pasta da solução criada pelos modelos do IoT Edge no Visual Studio Code ou no Visual Studio. Não é necessário compilar, efetuar push, implantar ou depurar esse código antes de continuar. Você configurará esses processos em Azure Pipelines.

   Se você está criando uma nova solução, clone o repositório localmente primeiro. Depois, ao criar a solução, escolha criá-la diretamente na pasta do repositório. É possível confirmar e efetuar push dos novos arquivos facilmente por lá.

* Um registro de contêiner em que você possa efetuar push de imagens de módulo. Você pode usar um [Registro de Contêiner do Azure](../container-registry/index.yml) ou um registro de terceiros.
* Um [Hub IOT](../iot-hub/iot-hub-create-through-portal.md) ativo do Azure com pelo menos dois dispositivos IOT Edge para testar os estágios de implantação de teste e produção separados. Você pode seguir os artigos de início rápido para criar um dispositivo do IoT Edge no [Linux](quickstart-linux.md) ou [Windows](quickstart.md)

Para saber mais sobre como usar o Azure Repos, confira [Compartilhar seu código com o Visual Studio e o Azure Repos](/azure/devops/repos/git/share-your-code-in-git-vs).

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Criar um pipeline de compilação para integração contínua

Nesta seção, você criará um novo pipeline de build. Configure o pipeline para ser executado automaticamente quando você fizer check-in de qualquer alteração no IoT Edge solução de exemplo e publicar logs de compilação.

1. Entre em sua organização do DevOps do Azure ( `https://dev.azure.com/{your organization}` ) e abra o projeto que contém seu repositório de solução de IOT Edge.

   ![Abrir seu projeto do DevOps](./media/how-to-continuous-integration-continuous-deployment/initial-project.png)

2. No menu do painel esquerdo em seu projeto, selecione **pipelines**. Selecione **criar pipeline** no centro da página. Ou, se você já tiver pipelines de compilação, selecione o botão **novo pipeline** no canto superior direito.

    ![Criar um novo pipeline de Build usando o botão novo pipeline](./media/how-to-continuous-integration-continuous-deployment/add-new-pipeline.png)

3. Na página **onde está seu código?** , selecione **Azure Repos git `YAML`**. Se você quiser usar o editor clássico para criar pipelines de Build do projeto, consulte o [Guia do editor clássico](how-to-continuous-integration-continuous-deployment-classic.md).

4. Selecione o repositório para o qual você está criando um pipeline.

    ![Selecione o repositório para o pipeline de compilação](./media/how-to-continuous-integration-continuous-deployment/select-repository.png)

5. Na página **configurar seu pipeline** , selecione **pipeline de início**. Se você tiver um arquivo de Azure Pipelines YAML preexistente que deseja usar para criar esse pipeline, poderá selecionar o **arquivo existente Azure pipelines YAML** e fornecer a ramificação e o caminho no repositório para o arquivo.

    ![Selecione o pipeline inicial ou o arquivo de Azure Pipelines YAML existente para iniciar o pipeline de compilação](./media/how-to-continuous-integration-continuous-deployment/configure-pipeline.png)

6. Na página **examinar o pipeline YAML** , você pode clicar no nome padrão `azure-pipelines.yml` para renomear o arquivo de configuração do pipeline.

   Selecione **Mostrar assistente** para abrir a paleta **tarefas** .

    ![Selecione Mostrar assistente para abrir a paleta de tarefas](./media/how-to-continuous-integration-continuous-deployment/show-assistant.png)

7. Para adicionar uma tarefa, coloque o cursor no final do YAML ou onde desejar que as instruções da tarefa sejam adicionadas. Procure e selecione **Azure IOT Edge**. Preencha os parâmetros da tarefa da seguinte maneira. Em seguida, selecione **Adicionar**.

   | Parâmetro | Descrição |
   | --- | --- |
   | Ação | Selecione **Compilar imagens de módulo**. |
   | .template.jsno arquivo | Forneça o caminho para a **deployment.template.jsno** arquivo no repositório que contém sua solução de IOT Edge. |
   | Plataforma padrão | Selecione o sistema operacional apropriado para seus módulos com base em seu dispositivo de IoT Edge de destino. |

    ![Usar a paleta tarefas para adicionar tarefas ao seu pipeline](./media/how-to-continuous-integration-continuous-deployment/add-build-task.png)

   >[!TIP]
   > Depois que cada tarefa for adicionada, o editor irá realçar automaticamente as linhas adicionadas. Para evitar a substituição acidental, desmarque as linhas e forneça um novo espaço para a próxima tarefa antes de adicionar tarefas adicionais.

8. Repita esse processo para adicionar mais três tarefas com os seguintes parâmetros:

   * Tarefa: **Azure IOT Edge**

       | Parâmetro | Descrição |
       | --- | --- |
       | Ação | Selecione **imagens do módulo Push**. |
       | Tipo de registro de contêiner | Use o tipo padrão: **registro de contêiner do Azure**. |
       | Assinatura do Azure | Selecione sua assinatura. |
       | Registro de Contêiner do Azure | Escolha o registro que você deseja usar para o pipeline. |
       | .template.jsno arquivo | Forneça o caminho para a **deployment.template.jsno** arquivo no repositório que contém sua solução de IOT Edge. |
       | Plataforma padrão | Selecione o sistema operacional apropriado para seus módulos com base em seu dispositivo de IoT Edge de destino. |

   * Tarefa: **copiar arquivos**

       | Parâmetro | Descrição |
       | --- | --- |
       | Pasta de Origem | A pasta de origem da qual copiar. Empty é a raiz do repositório. Use variáveis se os arquivos não estiverem no repositório. Exemplo: `$(agent.builddirectory)`.
       | Sumário | Adicione duas linhas: `deployment.template.json` e `**/module.json` . |
       | Pasta de destino | Especifique a variável `$(Build.ArtifactStagingDirectory)` . Consulte [criar variáveis](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables) para saber mais sobre a descrição. |

   * Tarefa: **publicar artefatos de compilação**

       | Parâmetro | Descrição |
       | --- | --- |
       | Caminho para publicar | Especifique a variável `$(Build.ArtifactStagingDirectory)` . Consulte [criar variáveis](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables) para saber mais sobre a descrição. |
       | Nome do artefato | Especifique o nome padrão: `drop` |
       | Local de publicação do artefato | Usar o local padrão: `Azure Pipelines` |

9. Selecione **salvar** na lista suspensa **salvar e executar** no canto superior direito.

10. O gatilho para integração contínua é habilitado por padrão para seu pipeline YAML. Se você quiser editar essas configurações, selecione seu pipeline e clique em **Editar** no canto superior direito. Selecione **mais ações** ao lado do botão **executar** no canto superior direito e vá para **gatilhos**. A **integração contínua** é mostrada como habilitada sob o nome do pipeline. Se você quiser ver os detalhes do gatilho, marque a caixa **substituir o gatilho de integração contínua do YAML aqui** .

    ![Para examinar as configurações de gatilho do pipeline, consulte gatilhos em mais ações](./media/how-to-continuous-integration-continuous-deployment/check-trigger-settings.png)

Acesse a próxima seção para compilar o pipeline de lançamento.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Próximas etapas

* IoT Edge exemplo de práticas recomendadas do DevOps no [iniciador DevOps do Azure para IOT Edge](how-to-devops-starter.md)
* Entenda a implantação do IoT Edge em [Noções básicas sobre implantações do IoT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md)
* Siga as etapas para criar, atualizar ou excluir uma implantação em [Implantar e monitorar os módulos do IoT Edge em larga escala](how-to-deploy-at-scale.md).