---
title: Como verificar várias fontes do Azure
description: Saiba como digitalizar uma assinatura ou um grupo de recursos do Azure inteiro em seu catálogo de dados do Azure alcance.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/26/2021
ms.openlocfilehash: 098f62365971fd634001706ab99fd414a6b25056
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123557"
---
# <a name="register-and-scan-azure-multiple-sources"></a>Registrar e examinar várias fontes do Azure

Este artigo descreve como registrar uma origem de várias fontes do Azure (assinaturas do Azure ou grupos de recursos) no alcance e configurar uma verificação nela.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

As várias fontes do Azure dão suporte a verificações para capturar metadados e esquemas na maioria dos tipos de recursos do Azure aos quais o alcance dá suporte. Ele também classifica os dados automaticamente com base em regras de classificação personalizada e do sistema.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de registrar as fontes de dados, crie uma conta do Azure alcance. Para obter mais informações sobre como criar uma conta do alcance, consulte [início rápido: criar uma conta do Azure alcance](create-catalog-portal.md).
- Você precisa ser um administrador de fonte de dados do Azure alcance
- Configurando a autenticação conforme descrito nas seções abaixo

### <a name="setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group"></a>Configurando a autenticação para enumerar recursos em uma assinatura ou grupo de recursos

1. Navegue até a assinatura ou o grupo de recursos no portal do Azure.  
1. Selecione **controle de acesso (iam)**   no menu de navegação à esquerda 
1. Você deve ser proprietário ou administrador de acesso do usuário para adicionar uma função na assinatura ou no grupo de recursos. Selecione o botão *+ Adicionar* . 
1. Defina a função **leitor** e insira o nome da conta do Azure alcance (que representa seu msi) em selecionar caixa de entrada. Clique em *salvar* para concluir a atribuição de função.

### <a name="setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group"></a>Configurando a autenticação para verificar os recursos em uma assinatura ou grupo de recursos

Há duas maneiras de configurar a autenticação para várias fontes do Azure:

- Identidade Gerenciada
- Entidade de Serviço

> [!NOTE]
> Você deve configurar a autenticação em cada recurso dentro de sua assinatura ou grupo de recursos, que você pretende registrar e verificar. Os tipos de recursos de armazenamento do Azure (armazenamento de BLOBs do Azure e Azure Data Lake Storage Gen2) facilitam a adição do MSI ou da entidade de serviço no nível de assinatura/grupo de recursos como leitor de dados de blob de armazenamento. Em seguida, as permissões tricklem para cada conta de armazenamento dentro dessa assinatura ou grupo de recursos. Para todos os outros tipos de recursos, você deve aplicar o MSI ou a entidade de serviço em cada recurso ou dispositivo um script para fazer isso. Veja como adicionar permissões em cada tipo de recurso em uma assinatura ou grupo de recursos.
    
- [Armazenamento de Blobs do Azure](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Banco de Dados SQL do Azure](register-scan-azure-sql-database.md)
- [Instância Gerenciada do Banco de Dados SQL do Azure](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
 
## <a name="register-an-azure-multiple-source"></a>Registrar uma fonte múltipla do Azure

Para registrar uma nova origem do Azure em seu catálogo de dados, faça o seguinte:

1. Acesse sua conta do Purview
1. Selecionar **Fontes** no painel de navegação à esquerda
1. Escolha **Registrar**
1. Em **registrar fontes**, selecione **Azure (vários)**
1. Selecione **Continuar**

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="Registrar várias fontes do Azure":::

Na tela **registrar fontes (vários itens do Azure)** , faça o seguinte:

1. Insira um **nome** no qual a fonte de dados será listada no catálogo 
1. Opcionalmente, escolha um **grupo de gerenciamento** para o qual filtrar
1. **Selecione uma assinatura ou um grupo de recursos específico** em uma determinada assinatura na lista suspensa. O escopo de registro será definido como a assinatura selecionada ou o grupo de recursos  
1. Selecionar uma **coleção** ou criar uma nova (opcional)
1. **Concluir** para registrar a fonte de dados

   :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="Configurar a fonte múltipla do Azure":::

## <a name="creating-and-running-a-scan"></a>Criando e executando uma verificação

Para criar e executar uma nova verificação, faça o seguinte:

1. Navegue até a seção de **fontes**

1. Selecione a fonte de dados que você registrou

1. Clique em Exibir detalhes e selecione **+ nova verificação** ou use o ícone de verificação rápida de ação no bloco origem

1. Preencha o *nome* e selecione todos os tipos de recurso que você deseja verificar nessa fonte

    1. Você pode deixá-lo como tudo (isso inclui *os* tipos de recursos futuros que podem não existir no momento dentro dessa assinatura ou grupo de recursos)
    1. Você pode **selecionar especificamente os tipos de recursos** que deseja verificar. Se você escolher essa opção, os tipos de recursos futuros que podem ser criados nessa assinatura ou no grupo de recursos não serão incluídos para verificações, a menos que a verificação seja explicitamente editada no futuro
    
    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="Verificação de várias origens do Azure":::

1. Selecione a credencial para se conectar aos recursos em sua fonte de dados. 
    1. Você pode selecionar uma **credencial no nível pai** como msi ou uma credencial de tipo de entidade de serviço específica, que você pode optar por usar para todos os tipos de recurso na assinatura ou grupo de recursos
    1. Você também pode **selecionar especificamente o tipo de recurso e aplicar uma credencial diferente** para esse tipo de recurso
    1. Cada credencial será considerada como o método de autenticação para todos os recursos em um determinado tipo
    1. Você deve definir a credencial escolhida nos recursos para examiná-las com êxito, conforme descrito nesta [seção](#setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group) acima
1. Dentro de cada tipo, você pode selecionar para verificar todos os recursos ou um subconjunto deles por nome.
    1. Se você deixar a opção como **todos os** recursos futuros desse tipo também serão verificados em execuções futuras de verificação
    1. Se você selecionar contas de armazenamento ou bancos de dados SQL específicos, os recursos futuros criados nesse tipo dentro dessa assinatura ou grupo de recursos não serão incluídos para verificações, a menos que a verificação seja explicitamente editada no futuro
 
1.  Clique em **Continuar** para prosseguir. Vamos testar o acesso para verificar se você aplicou o MSI alcance como um leitor na assinatura ou no grupo de recursos. Se uma mensagem de erro for gerada, siga as instruções [aqui](#setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group)

1.  Selecione **verificar conjuntos de regras** para cada tipo de recurso escolhido na etapa anterior. Você também pode criar conjuntos de regras de verificação embutidos.
  :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="Seleção de conjunto de regras de verificação múltipla do Azure":::

1. Escolha o gatilho da verificação. Você pode agendá-lo para ser executado **semanalmente/mensalmente** ou **uma vez**

1. Examine sua verificação e selecione salvar para concluir a configuração   

## <a name="viewing-your-scans-and-scan-runs"></a>Exibindo as verificações e as execuções de verificação

1. Exiba os detalhes da origem clicando em **Exibir detalhes** no bloco sob a seção fontes. 

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="Detalhes de várias fontes do Azure"::: 

1. Exiba os detalhes da execução da verificação navegando até a página de **detalhes da verificação** .
    1. A *barra de status* é um breve resumo do status de execução dos recursos filhos. Ele será exibido na assinatura ou no nível do grupo de recursos
    1. Verde significa êxito, enquanto o vermelho significa falha. Cinza significa que a verificação ainda está em andamento
    1. Você pode clicar em cada verificação para exibir detalhes refinados mais detalhados

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="Detalhes da verificação múltipla do Azure":::

1. Exiba um resumo de execuções de verificação com falha recentes na parte inferior da página de detalhes de origem. Você também pode clicar em para exibir detalhes mais granulares referentes a essas execuções.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Gerenciar suas verificações – editar, excluir ou cancelar
Para gerenciar ou excluir uma verificação, faça o seguinte:

- Navegue até o centro de gerenciamento. Selecione fontes de dados na seção fontes e varredura e, em seguida, selecione na fonte de dados desejada

- Selecione a verificação que você deseja gerenciar. Você pode editar a verificação selecionando editar

- Você pode excluir sua verificação selecionando excluir
- Se uma verificação estiver em execução, você também poderá cancelá-la

## <a name="next-steps"></a>Próximas etapas

- [Navegar pelo Catálogo de Dados do Azure Purview](how-to-browse-catalog.md)
- [Pesquisar no Catálogo de Dados do Azure Purview](how-to-search-catalog.md)    
