---
title: "Como implantar a Sincronização de Arquivo do Azure (versão prévia) | Microsoft Docs"
description: "Saiba como implantar a Sincronização de Arquivo do Azure do início ao fim."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 9f9ff0674fd4e3f9b0598a982d81681eaa6d1997
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deploy-azure-file-sync-preview"></a>Como implantar a Sincronização de Arquivo do Azure (versão prévia)
A Sincronização de Arquivos do Azure (versão prévia) permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. Ele faz isso, transformando Windows Servers em um cache rápido do seu compartilhamento de Arquivos do Azure. Você pode usar qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS) e você pode ter todos os caches que precisar em todo o mundo.

É altamente recomendável ler [Planning for an Azure Files deployment](storage-files-planning.md) (Planejando uma implantação de Arquivos do Azure) e [Planning for an Azure File Sync deployment](storage-sync-files-planning.md) (Planejando uma implantação de Sincronização de Arquivo do Azure) antes de seguir as etapas neste guia.

## <a name="prerequisites"></a>Pré-requisitos
* Uma conta de armazenamento e um compartilhamento do Arquivo do Azure na mesma região em que você deseja implantar a Sincronização de Arquivo do Azure. Para obter mais informações, consulte:
    - [Disponibilidade de região](storage-sync-files-planning.md#region-availability) da Sincronização de Arquivo do Azure,
    - [Criar uma conta de armazenamento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para encontrar orientações passo a passo sobre como criar uma conta de armazenamento e
    - [Criar um compartilhamento de arquivos](storage-how-to-create-file-share.md) para obter instruções passo a passo sobre como criar um compartilhamento de arquivos.
* Pelo menos um cluster do Windows Server ou Windows Server com suporte para sincronizar com a Sincronização de Arquivo do Azure. Consulte [Interoperabilidade com o Windows Server](storage-sync-files-planning.md#azure-file-sync-interoperability) para obter mais informações sobre versões com suporte do Windows Server.

## <a name="deploy-the-storage-sync-service"></a>Implantar o Serviço de Sincronização de Armazenamento 
O Serviço de Sincronização de Armazenamento é o recurso do Azure de nível superior que representa a Sincronização de Arquivo do Azure. Para implantar um Serviço de Sincronização de Armazenamento, navegue até o [Portal do Azure](https://portal.azure.com/) e pesquise a Sincronização de Arquivo do Azure. Depois de selecionar "Sincronização de Arquivo do Azure (versão prévia)" nos resultados da pesquisa, selecione "Criar" para abrir a guia “Implantar Sincronização de Armazenamento”.

A folha resultante solicita as seguintes informações:

- **Nome**: um nome exclusivo (por assinatura) para o Serviço de Sincronização de Armazenamento.
- **Assinatura**: a assinatura na qual o Serviço de Sincronização de Armazenamento será criado. Dependendo da estratégia de configuração da sua empresa, você pode ter acesso a uma ou mais assinaturas. Uma Assinatura do Azure é o contêiner mais básico de cobrança para cada serviço de nuvem (como Arquivos do Azure).
- **Grupo de recursos**: um grupo de recursos é um grupo lógico de recursos do Azure, como uma conta de armazenamento ou um Serviço de Sincronização de Armazenamento. Você pode criar um novo grupo de recursos ou usar um grupo de recursos existente para a Sincronização de Arquivo do Azure (é recomendável usar grupos de recursos como contêineres usados para isolar recursos logicamente para sua organização, como para agrupar recursos de RH ou recursos de um projeto específico).
- **Localização**: a região na qual você deseja implantar a Sincronização de Arquivo do Azure. Somente as regiões com suporte estão disponíveis nessa lista.

Depois que o formulário "Implantar Sincronização de Armazenamento" estiver concluído, cliquem em "Criar" para implantar o Serviço de Sincronização de Armazenamento.

## <a name="prepare-windows-servers-for-use-with-azure-file-sync"></a>Preparar servidores Windows para uso com Sincronização de Arquivo do Azure
Para cada servidor com o qual você pretende usar a Sincronização de Arquivo do Azure, incluindo nós de servidor em um Cluster de Failover, conclua as seguintes etapas:

Para cada servidor, incluindo nós do servidor em um Cluster de Failover, que você pretende usar com a Sincronização de Arquivo do Azure, conclua as seguintes etapas:

1. Desabilite a Configuração de Segurança Reforçada do Internet Explorer. Isso é necessário apenas para o registro inicial do servidor e pode ser habilitado novamente após o servidor ter sido registrado.
    1. Abra o Gerenciador de Servidor.
    2. Clique em **Servidor Local**:  
        !["Servidor Local" no lado esquerdo da interface do usuário do Gerenciador do Servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. Selecione o link de **Configuração de Segurança Reforçada do IE** no subpainel Propriedades:  
        !["Configuração de Segurança Reforçada do IE" na interface do usuário do Gerenciador do Servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. Selecione **Desativado** para Administradores e Usuários na janela pop-up Configuração de Segurança Reforçada do Internet Explorer:  
        ![A janela pop-up Configuração de Segurança Reforçada do Internet Explorer com "Desativado" selecionado](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. Certifique-se de que você está executando pelo menos PowerShell 5.1.\* (O PowerShell 5.1 é o padrão no Windows Server 2016). Você pode verificar se está executando o PowerShell 5.1.\* examinando o valor da propriedade PSVersion do objeto $PSVersionTable:

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    - Se PSVersion for menor que 5.1.\*, como será o caso com a maioria das instalações do Windows Server 2012 R2, você poderá atualizar facilmente baixando e instalando o [WMF (Windows Management Framework) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). O pacote apropriado para baixar e instalar o Windows Server 2012 R2 é **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**.

3. [Instale e configure o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). É sempre recomendável usar a versão mais recente dos módulos do Azure PowerShell.

## <a name="install-the-azure-file-sync-agent"></a>Instalar o agente de Sincronização de Arquivo do Azure
O agente de Sincronização de Arquivo do Azure é um pacote baixável que permite que um Windows Server seja sincronizado com um compartilhamento de Arquivo do Azure. O agente pode ser baixado do [Centro de Download da Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Após o download, clique duas vezes no pacote MSI para iniciar a instalação do agente de Sincronização de Arquivo do Azure.

> [!Important]  
> Se você pretende usar a Sincronização de Arquivo do Azure com um Cluster de Failover, o agente de Sincronização de Arquivo do Azure precisa ser instalado em cada nó no cluster.

O pacote de instalação do agente de Sincronização de Arquivo do Azure deve ser instalado relativamente rápido sem muitos prompts adicionais. Recomendamos o seguinte:
- Deixe o caminho de instalação padrão `C:\Program Files\Azure\StorageSyncAgent`) para simplificar a solução de problemas e a manutenção do servidor.
- Habilitar o Microsoft Update para manter a Sincronização de Arquivo do Azure atualizada. Todas as atualizações, incluindo hotfixes e atualizações de recursos, para o agente de Sincronização de Arquivo do Azure, ocorrerão por meio do Microsoft Update. É sempre recomendável fazer a atualização mais recente para a Sincronização de Arquivo do Azure. Consulte a [Política de atualização da Sincronização de Arquivo do Azure](storage-sync-files-planning.md#azure-file-sync-agent-update-policy) para obter mais informações.

Na conclusão da instalação do agente de Sincronização de Arquivo do Azure, a interface do usuário de Registro do Servidor será iniciada automaticamente. Consulte a próxima seção para saber como registrar este servidor com a Sincronização de Arquivo do Azure.

## <a name="register-windows-server-with-storage-sync-service"></a>Registrar o Windows Server com o Serviço de Sincronização de Armazenamento
Registrar seu Windows Server com um Serviço de Sincronização de Armazenamento estabelece uma relação de confiança entre seu servidor (ou cluster) e o Serviço de Sincronização de Armazenamento. A interface do usuário de Registro do Servidor deverá ser iniciada automaticamente após a instalação do agente de Sincronização de Arquivo do Azure, mas se isso não ocorrer, é possível abri-la manualmente usando sua localização: `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`. Quando a interface do usuário de Registro do Servidor abrir, clique em **Entrar** para começar.

Depois de entrar, você deverá fornecer as seguintes informações:

![Uma captura de tela da interface do usuário de Registro do Servidor](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Assinatura do Azure**: a assinatura que contém o Serviço de Sincronização de Armazenamento (consulte [Implantar o Serviço de Sincronização de Armazenamento](#deploy-the-storage-sync-service) acima). 
- **Grupo de recursos**: o grupo de recursos que contém o Serviço de Sincronização de Armazenamento.
- **Serviço de Sincronização de Armazenamento**: o nome do Serviço de Sincronização de Armazenamento com o qual você deseja registrar.

Depois de selecionar as informações apropriadas nos menus suspensos, clique em **Registrar** para concluir o registro do servidor. Como parte do processo de registro, você será solicitado a realizar uma entrada adicional.

## <a name="create-a-sync-group"></a>Criar um Grupo de Sincronização
Um Grupo de Sincronização define a topologia de sincronização para um conjunto de arquivos. Os pontos de extremidade em um Grupo de Sincronização serão mantidos em sincronia entre si. Um Grupo de Sincronização deve conter pelo menos um ponto de extremidade de Nuvem, que representa um compartilhamento de Arquivo do Azure e um ponto de extremidade do servidor, que representa um caminho em um Windows Server. Para criar um Grupo de Sincronização, navegue até o Serviço de Sincronização de Armazenamento no [Portal do Azure](https://portal.azure.com/) e clique em **+ Grupo de sincronização**:

![Criar um Grupo de Sincronização no Portal do Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

O painel resultante solicita as informações a seguir para criar um Grupo de Sincronização com um ponto de extremidade de nuvem:

- **Nome do Grupo de Sincronização**: o nome do Grupo de Sincronização a ser criado. Esse nome deve ser exclusivo dentro do Serviço de Sincronização de Armazenamento, mas pode ser qualquer nome lógico para você.
- **Assinatura**: a assinatura na qual você implantou o Serviço de Sincronização de Armazenamento em [Implantar o Serviço de Sincronização de Armazenamento](#deploy-the-storage-sync-service) acima.
- **Conta de armazenamento**: clicar em "Selecionar conta de armazenamento" abrirá um painel adicional permitindo que você selecione a conta de armazenamento que contém o Compartilhamento de Arquivo do Azure com o qual você deseja sincronizar.
- **Compartilhamento de Arquivo do Azure**: o nome do Compartilhamento de Arquivo do Azure com o qual você deseja sincronizar.

Para adicionar um ponto de extremidade do servidor, navegue até o Grupo de Sincronização recém-criado e clique em "Adicionar ponto de extremidade do servidor".

![Adicionar um novo ponto de extremidade do servidor no painel Grupo de Sincronização](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

O painel "Adicionar ponto de extremidade do servidor" resultante requer as seguintes informações para criar um ponto de extremidade do servidor:

- **Servidor Registrado**: o nome do servidor ou cluster no qual criar o ponto de extremidade do servidor.
- **Caminho**: o caminho no Windows Server a ser sincronizado como parte do grupo de sincronização.
- **Camadas de Nuvem**: uma opção para habilitar ou desabilitar as camadas de nuvem, o que permite que os arquivos pouco usados ou acessados sejam dispostos em camadas para os Arquivos do Azure.
- **Espaço Livre no Volume**: a quantidade de espaço livre para reservar no volume no qual reside o ponto de extremidade do servidor. Por exemplo, se o Espaço Livre do Volume estiver definido como 50% em um volume com um único ponto de extremidade do servidor, aproximadamente metade da quantidade de dados será disposta em camadas para os Arquivos do Azure. Observe que, independentemente de as camadas na nuvem estarem habilitadas, o Compartilhamento de Arquivos do Azure sempre terá uma cópia completa dos dados no Grupo de Sincronização.

Clique em "Criar" para adicionar o ponto de extremidade do servidor. Os arquivos agora serão mantidos em sincronia entre o Compartilhamento de Arquivos do Azure e o Windows Server. 

## <a name="next-steps"></a>Próximas etapas
- [Adicionar/remover um ponto de extremidade do Servidor de Sincronização de Arquivo do Azure](storage-sync-files-server-endpoint.md)
- [Registrar/cancelar registro de um servidor com a Sincronização de Arquivo do Azure](storage-sync-files-server-registration.md)