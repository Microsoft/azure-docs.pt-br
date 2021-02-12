---
title: Configurações de push para configuração de aplicativo com Azure Pipelines
description: Aprenda a usar Azure Pipelines para enviar por push valores de chave para um repositório de configuração de aplicativo
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 07/27/2020
ms.author: alkemper
ms.openlocfilehash: c5e0cc3eb29fb612460b16d8de9dee62949b5bd2
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979599"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>Configurações de push para configuração de aplicativo com Azure Pipelines

A tarefa [push de configuração de Azure app](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) envia valores de chave de um arquivo de configuração para o repositório de configurações do aplicativo. Essa tarefa habilita a funcionalidade de círculo completo no pipeline, já que você pode efetuar pull das configurações do repositório de configuração do aplicativo, bem como as configurações de push para o repositório de configuração do aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- Recurso de configuração de aplicativo – crie um gratuitamente no [portal do Azure](https://portal.azure.com).
- Projeto DevOps do Azure – [crie um gratuitamente](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure App tarefa de push de configuração – Baixe gratuitamente do [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push).

## <a name="create-a-service-connection"></a>Criar uma conexão de serviço

Uma [conexão de serviço](/azure/devops/pipelines/library/service-endpoints) permite que você acesse recursos em sua assinatura do Azure de seu projeto DevOps do Azure.

1. No Azure DevOps, vá para o projeto que contém o pipeline de destino e abra as **configurações do projeto** na parte inferior esquerda.
1. Em **pipelines** , selecione **conexões de serviço** e selecione **nova conexão de serviço** no canto superior direito.
1. Selecione **Azure Resource Manager**.
1. Selecione a **entidade de serviço (automática)**.
1. Preencha sua assinatura e recurso. Dê um nome à sua conexão de serviço.

Agora que a conexão de serviço foi criada, localize o nome da entidade de serviço atribuída a ela. Você adicionará uma nova atribuição de função a essa entidade de serviço na próxima etapa.

1. Vá para **configurações do projeto**  >  **conexões de serviço**.
1. Selecione a conexão de serviço que você criou na seção anterior.
1. Selecione **gerenciar entidade de serviço**.
1. Observe o **nome de exibição** listado.

## <a name="add-role-assignment"></a>Adicionar atribuição de função

Atribua as atribuições de função de configuração de aplicativo adequadas às credenciais que estão sendo usadas dentro da tarefa para que a tarefa possa acessar o repositório de configuração de aplicativo.

1. Navegue até o repositório de configuração do aplicativo de destino. 
1. À esquerda, selecione **controle de acesso (iam)**.
1. Na parte superior, selecione **+ Adicionar** e escolha **Adicionar atribuição de função**.
1. Em **função**, selecione **proprietário de dados de configuração do aplicativo**. Essa função permite que a tarefa Leia e grave no repositório de configuração do aplicativo. 
1. Selecione a entidade de serviço associada à conexão de serviço que você criou na seção anterior.
  
## <a name="use-in-builds"></a>Usar em compilações

Esta seção abordará como usar a tarefa push de configuração de Azure App em um pipeline de compilação DevOps do Azure.

1. Navegue até a página Criar pipeline **clicando em pipelines pipelines**  >  . A documentação para pipelines de compilação pode ser encontrada [aqui](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2).
      - Se você estiver criando um novo pipeline de compilação, selecione **Mostrar assistente** no lado direito do pipeline e procure a tarefa de **configuração de envio por push do Azure app** .
      - Se você estiver usando um pipeline de Build existente, navegue até a guia **tarefas** ao editar o pipeline e procure a tarefa **configuração de envio por push do Azure app** .
2. Configure os parâmetros necessários para a tarefa Enviar por push os valores de chave do arquivo de configuração para o repositório de configurações de aplicativo. O parâmetro de **caminho do arquivo de configuração** começa na raiz do repositório de arquivos.
3. Salve e enfileirar uma compilação. O log de compilação exibirá todas as falhas ocorridas durante a execução da tarefa.

## <a name="use-in-releases"></a>Usar em versões

Esta seção abordará como usar a tarefa de push de configuração de Azure App em um pipeline de versão do Azure DevOps.

1. Navegue até a página de pipeline de liberação selecionando versões de **pipelines**  >  . A documentação para pipelines de versão pode ser encontrada [aqui](/azure/devops/pipelines/release).
1. Escolha um pipeline de lançamento existente. Se você não tiver um, selecione **+ novo** para criar um novo.
1. Selecione o botão **Editar** no canto superior direito para editar o pipeline de liberação.
1. Escolha o **estágio** para adicionar a tarefa. Mais informações sobre os estágios podem ser encontradas [aqui](/azure/devops/pipelines/release/environments).
1. Selecione **+** para esse trabalho e, em seguida, adicione a tarefa **push de configuração de Azure app** na guia **implantar** .
1. Configure os parâmetros necessários na tarefa para enviar por push seus valores de chave do arquivo de configuração para o repositório de configurações do aplicativo. As explicações dos parâmetros estão disponíveis na seção **parâmetros** abaixo e nas dicas de ferramentas ao lado de cada parâmetro.
1. Salve e enfileirar uma versão. O log de liberação exibirá todas as falhas encontradas durante a execução da tarefa.

## <a name="parameters"></a>Parâmetros

Os parâmetros a seguir são usados pela tarefa de push de configuração de aplicativo:

- **Assinatura do Azure**: uma lista suspensa que contém as conexões de serviço do Azure disponíveis. Para atualizar e atualizar sua lista de conexões de serviço do Azure disponíveis, pressione o botão **Atualizar assinatura do Azure** à direita da caixa de texto.
- **Nome da configuração do aplicativo**: uma lista suspensa que carrega os armazenamentos de configuração disponíveis na assinatura selecionada. Para atualizar e atualizar sua lista de repositórios de configuração disponíveis, pressione o botão **Atualizar nome de configuração do aplicativo** à direita da caixa de texto.
- **Caminho do arquivo de configuração**: o caminho para o arquivo de configuração. Você pode navegar pelo seu artefato de compilação para selecionar um arquivo de configuração. ( `...` botão à direita da caixa de texto).
- **Separador**: o separador usado para mesclar arquivos. JSON e. yml.
- **Profundidade**: a profundidade para a qual os arquivos. JSON e. yml serão mesclados.
- **Prefix**: uma cadeia de caracteres que é acrescentada ao início de cada chave enviada por push para o repositório de configuração do aplicativo.
- **Rótulo**: uma cadeia de caracteres que é adicionada a cada chave-valor como o rótulo no repositório de configuração do aplicativo.
- **Tipo de conteúdo**: uma cadeia de caracteres que é adicionada a cada chave-valor como o tipo de conteúdo no repositório de configuração do aplicativo.
- **Marcas**: um objeto JSON no formato de `{"tag1":"val1", "tag2":"val2"}` , que define as marcas que são adicionadas a cada chave-valor enviado por push para o repositório de configurações do aplicativo.
- **Exclua todas as outras Key-Values no repositório com o prefixo e o rótulo especificados: o** valor padrão está **desmarcado**.
  - **Marcado**: Remove todos os valores de chave no repositório de configuração de aplicativo que correspondem ao prefixo e ao rótulo especificados antes de enviar por push novos valores de chave do arquivo de configuração.
  - **Desmarcado**: envia por push todos os valores de chave do arquivo de configuração para o repositório de configuração do aplicativo e deixa tudo o mais no repositório de configuração do aplicativo intacto.

Depois de preencher os parâmetros necessários, execute o pipeline. Todos os valores de chave no arquivo de configuração especificado serão carregados para a configuração do aplicativo.

## <a name="troubleshooting"></a>Solução de problemas

Se ocorrer um erro inesperado, os logs de depuração poderão ser habilitados definindo a variável de pipeline `system.debug` como `true` .

## <a name="faq"></a>Perguntas frequentes

**Como posso carregar vários arquivos de configuração?**

Crie várias instâncias do Azure App tarefa de push de configuração no mesmo pipeline para enviar vários arquivos de configuração para o repositório de configurações de aplicativo.

**Por que estou recebendo um erro 409 ao tentar enviar por push valores de chave para meu repositório de configurações?**

Uma mensagem de erro de conflito 409 ocorrerá se a tarefa tentar remover ou substituir um valor de chave que esteja bloqueado no repositório de configuração de aplicativo.
