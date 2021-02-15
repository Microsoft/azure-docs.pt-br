---
title: Configurar o armazenamento do Avere vFXT – Azure
description: Saiba como adicionar um sistema de armazenamento de back-end para um cluster no avere vFXT para Azure. Se você criou um contêiner de blob do Azure com o cluster, ele estará pronto para uso.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: c17d3c7cd2cf6fe5bca725cf94344b2d2cb50bf2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001517"
---
# <a name="configure-storage"></a>Configurar o armazenamento

Esta etapa configura um sistema de armazenamento de back-end para seu cluster do vFXT.

> [!TIP]
> Se você criou um novo contêiner de blob do Azure junto com o cluster avere vFXT, esse contêiner já está configurado e pronto para uso.

Siga estas instruções se você não tiver criado um novo contêiner de Blob com o cluster ou se quiser adicionar um hardware extra ou um sistema de armazenamento baseado em nuvem.

Há duas tarefas principais:

1. [Crie um Filer principal](#create-a-core-filer), que conecta o cluster vFXT a um sistema de armazenamento existente ou a um contêiner de conta de armazenamento do Azure.

1. [Criar uma junção de namespace](#create-a-junction), que define o caminho em que os clientes serão montados.

Essas etapas usam o Painel de Controle do Avere. Leia [Acessar o cluster do vFXT](avere-vfxt-cluster-gui.md) para aprender a usá-lo.

## <a name="create-a-core-filer"></a>Criar um arquivista central

O "principal Filer" é um termo vFXT para um sistema de armazenamento de back-end. O armazenamento pode ser um dispositivo de hardware, como NetApp ou Isilon, ou pode ser um armazenamento de objetos de nuvem. Mais informações sobre os Filers principais podem ser encontradas no [Guia de configurações do cluster avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers).

Para adicionar um arquivista central, escolha um dos dois tipos principais de arquivistas centrais:

* [Arquivista central NAS](#nas-core-filer) – descreve como adicionar um arquivista central NAS
* [Filer Core de nuvem de armazenamento do Azure](#azure-blob-storage-cloud-core-filer) -descreve como adicionar um contêiner de armazenamento de BLOBs do Azure como um Filer do Cloud Core

### <a name="nas-core-filer"></a>Arquivista central do NAS

Um Filer do NAS core pode ser um dispositivo NetApp ou Isilon local ou um ponto de extremidade NAS na nuvem. O sistema de armazenamento deve ter uma conexão confiável de alta velocidade com o cluster do Avere vFXT, por exemplo, conexão do ExpressRoute (não uma VPN) de 1 Gbps e deve fornecer acesso à raiz do cluster para as exportações NAS que estão sendo usadas.

Siga estas etapas para adicionar um Filer do NAS Core:

1. No Painel de Controle do Avere, clique na guia **Configurações** na parte superior.

1. Clique em **principal Filer**  >  **gerenciar os principais Filers** à esquerda.

1. Clique em **Criar**.

   ![Captura de tela da página Adicionar novo arquivador principal com um cursor sobre o botão Criar](media/avere-vfxt-add-core-filer-start.png)

1. Preencha as informações obrigatórias no assistente:

   * Dar um nome ao arquivista central.
   * Forneça um FQDN (nome de domínio totalmente qualificado) se disponível. Caso contrário, forneça um endereço IP ou nome de host que seja resolvido para seu arquivista central.
   * Escolha sua classe de arquivista na lista. Se não tiver certeza, escolha **Outro**.

     ![Captura de tela da página Adicionar novo arquivista central com o nome do arquivista central e seu nome de domínio totalmente qualificado](media/avere-vfxt-add-core-filer.png)
  
   * Clique em **Avançar** e escolha uma política de cache.
   * Clique em **Adicionar Arquivista**.
   * Para obter mais informações, veja [Como adicionar um novo arquivista central NAS](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) no guia de configurações de cluster do Avere.

Em seguida, vá para [Criar uma junção](#create-a-junction).  

### <a name="azure-blob-storage-cloud-core-filer"></a>Azure Blob Storage Cloud principal Filer

Para usar o armazenamento de BLOBs do Azure como o armazenamento de back-end do cluster vFXT, você precisa de um contêiner vazio para adicionar como um Filer principal.

Adicionar o Armazenamento de Blobs ao seu cluster requer estas tarefas:

* Criar uma conta de armazenamento (etapa 1, abaixo)
* Criar um contêiner de Blob vazio (etapas 2 e 3)
* Adicionar a chave de acesso de armazenamento como uma credencial de nuvem para o cluster vFXT (etapas 4 a 6)
* Adicionar o contêiner de Blob como um arquivista central para o cluster vFXT (etapas 7 a 9)
* Criar uma junção de namespace que os clientes usam para montar o arquivista central ([Criar uma junção](#create-a-junction), mesmo para armazenamento de hardware e de nuvem)

> [!TIP]
> Se você criar um novo contêiner de blob ao criar um avere vFXT para o cluster do Azure, o modelo de implantação configurará automaticamente o contêiner como um Filer central. (Isso também será verdadeiro se você usar o script de criação, que está disponível na solicitação.) Você não precisa configurar o filer principal posteriormente.
>
> A ferramenta de criação de cluster executa essas tarefas de configuração para você:
>
> * Cria um novo contêiner de blob na conta de armazenamento fornecida
> * Define o contêiner como um Filer principal
> * Cria uma junção de namespace para o contêiner
> * Cria um ponto de extremidade de serviço de armazenamento dentro da rede virtual do cluster

Para adicionar o Armazenamento de Blobs depois de criar o cluster, siga estas etapas.

1. Crie uma conta de armazenamento V2 de uso geral com estas configurações:

   * **Assinatura** – mesma que a do cluster do vFXT
   * **Grupo de recursos** – mesmo que o grupo de clusters do vFXT (opcional)
   * **Localização** – a mesma que a do cluster vFXT
   * **Desempenho** – padrão (não há suporte para armazenamento Premium)
   * **Tipo de conta**: uso geral V2 (StorageV2)
   * **Replicação** – LRS (armazenamento com redundância local)
   * **Camada de acesso** – frequente
   * **Transferência segura obrigatória** – desabilite essa opção (valor não padrão)
   * **Redes virtuais** – não obrigatórias

   Você pode usar o portal do Azure ou clicar no botão "Implantar no Azure" abaixo.

   [![botão para criar conta de armazenamento](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. Depois de criar a conta, navegue até a página da conta de armazenamento.

   ![Nova conta de armazenamento no portal do Azure](media/avere-vfxt-new-storage-acct.png)

1. Criar um novo contêiner de BLOBs: clique em **contêineres** na página Visão geral e, em seguida, clique em **+ contêiner**. Use qualquer nome de contêiner e verifique se o acesso é definido como **Privado**.

   ![Página blobs de armazenamento com o botão + contêiner circulado e um novo contêiner sendo criado em uma página pop-up](media/avere-vfxt-new-blob.png)

1. Obtenha a chave de conta de armazenamento do Azure clicando em **chaves de acesso** em **configurações**. Copie uma das chaves fornecidas.

   ![GUI do portal do Azure para copiar a chave](media/avere-vfxt-copy-storage-key.png)

1. Abra o Painel de Controle do Avere para seu cluster. Clique em **Configurações** e abra **Cluster** > **Credenciais de Nuvem** no painel de navegação esquerdo. Na página de credenciais de nuvem, clique em **Adicionar Credencial**.

   ![Clicar no botão Adicionar Credencial na página de configuração de Credenciais de Nuvem](media/avere-vfxt-new-credential-button.png)

1. Preencha as informações a seguir para criar uma credencial para o arquivista central de nuvem:

   | Campo | Valor |
   | --- | --- |
   | Nome da credencial | qualquer nome descritivo |
   | Tipo de serviço | (selecione a chave de acesso de armazenamento do Azure) |
   | Locatário | nome da conta de armazenamento |
   | Subscription | ID da assinatura |
   | Chave de Acesso de Armazenamento | Chave de conta de Armazenamento do Azure (copiada na etapa anterior) |

   Clique em **Enviar**.

   ![Concluído o formulário de credencial de nuvem no Painel de Controle do Avere](media/avere-vfxt-new-credential-submit.png)

1. Em seguida, crie o arquivista central. No lado esquerdo do painel de controle avere, clique em **principal Filer**  >   **gerenciar os principais Filers**.

1. Clique no botão **Criar** na página de configurações **Gerenciar Arquivistas Centrais**.

1. Preencher o assistente:

   * Selecione o tipo de arquivista **Nuvem**.
   * Dê um nome ao novo arquivista central e clique em **Avançar**.
   * Aceite a política de cache padrão e prossiga para a terceira página.
   * Em **Tipo de serviço**, escolha **Armazenamento do Azure**.
   * Escolha a credencial criada anteriormente.
   * Definir **Conteúdos do Bucket** como **Vazio**
   * Mude **Verificação de certificado** para **Desabilitada**
   * Mude **Modo de compactação** para **Nenhum**
   * Clique em **Próximo**.
   * Na quarta página, digite o nome do contêiner em **Nome do Bucket** como *storage_account_name*/*container_name*.
   * Opcionalmente, defina **Tipo de Criptografia** como **Nenhum**.  O Armazenamento do Azure é criptografado por padrão.
   * Clique em **Adicionar Arquivista**.

   Para obter informações mais detalhadas, leia [Como adicionar um novo arquivista central de nuvem](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) no guia de configuração de cluster do Avere.

A página será atualizada ou você poderá atualizar a página para exibir seu novo arquivista central.

Em seguida, você precisará [Criar uma junção](#create-a-junction).

## <a name="create-a-junction"></a>Criar uma junção

Uma junção é um caminho que você cria para os clientes. Os clientes montam o caminho e chegam ao destino escolhido.

Por exemplo, você pode criar `/vfxt/files` para mapear sua exportação `/vol0/data` do arquivista central NetApp e o subdiretório `/project/resources`.

Mais informações sobre junções podem ser encontradas na [seção de namespace do guia de configuração de cluster do Avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html).

Siga estas etapas na interface do painel de controle do avere:

* Clique **VServer** em  >  **namespace** vserver no canto superior esquerdo.
* Forneça um caminho de namespace que comece com / (barra), como ``/vfxt/data``.
* Escolha seu arquivista central.
* Escolha a exportação do arquivista central.
* Clique em **Próximo**.

  ![Captura de tela da página "Adicionar nova junção" com os campos preenchidos para junção, arquivista central e exportação](media/avere-vfxt-add-junction.png)

A junção será exibida após alguns segundos. Crie junções adicionais conforme necessário.

Depois que a junção tiver sido criada, os clientes usarão o caminho do namespace para acessar os arquivos do sistema de armazenamento.

## <a name="next-steps"></a>Próximas etapas

* [Montar o cluster do Avere vFXT](avere-vfxt-mount-clients.md)
* Aprenda maneiras eficientes de [mover dados para um novo contêiner de BLOBs](avere-vfxt-data-ingest.md)
