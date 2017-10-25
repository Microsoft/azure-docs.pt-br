---
title: "Configurar uma máquina virtual como um servidor do IPython Notebook | Microsoft Docs"
description: "Configure uma Máquina Virtual do Azure para uso em um ambiente de ciência de dados com o IPython Server para análise avançada."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 818617c1-048e-49c2-b941-f9a983f93998
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: xibingao;bradsev
ms.openlocfilehash: b32aa2325a507f18ffc8b47cddde8637a0a8aabf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-an-azure-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>Configurar uma máquina virtual do Azure como um servidor do IPython Notebook para análises avançadas
Este tópico mostra como provisionar e configurar uma máquina virtual do Azure para análise avançada, que pode ser usada como parte de um ambiente de ciência de dados. A máquina virtual do Windows está configurada com ferramentas de suporte como o IPython Notebook, o Gerenciador de Armazenamento do Azure e o AzCopy, bem como outros utilitários úteis para projetos de análise avançada. O Azure Storage Explorer e o AzCopy, por exemplo, fornecem maneiras convenientes para carregar dados no armazenamento de blob do Azure em seu computador local ou baixá-lo em seu computador local por meio do armazenamento de blob.

## <a name="create-vm"></a>Etapa 1: criar uma máquina virtual do Azure com finalidade geral
Se você já tiver uma máquina virtual do Azure e apenas deseja configurar um servidor do IPython Notebook nela, é possível ignorar esta etapa e prosseguir para a [Etapa 2: adicionar um ponto de extremidade para IPython Notebooks a uma máquina virtual existente](#add-endpoint).

Antes de iniciar o processo de criação de uma máquina virtual no Azure, você precisa determinar o tamanho da máquina que é necessária para processar os dados para o seu projeto. Máquinas menores têm menos memória e menos núcleos de CPU que máquinas maiores, mas elas também são mais baratas. Para obter uma lista dos tipos de máquinas e seus preços, consulte a página <a href="http://azure.microsoft.com/pricing/details/virtual-machines/" target="_blank">Preços de máquinas virtuais</a>

1. Faça logon no <a href="https://manage.windowsazure.com" target="_blank">Portal Clássico do Azure</a> e clique em **Novo** no canto inferior esquerdo. Uma janela será exibida. Selecione **COMPUTAÇÃO** -> **MÁQUINA VIRTUAL** -> **DA GALERA**.
   
    ![Criar espaço de trabalho][24]
2. Escolha uma das seguintes imagens:
   
   * Windows Server 2012 R2 Datacenter
   * Experiência do Windows Server Essentials (Windows Server 2012 R2)
     
     Em seguida, clique na seta apontando para a direita na parte inferior direita para ir para a próxima página de configuração.
     
     ![Criar espaço de trabalho][25]
3. Digite um nome para a máquina virtual que você deseja criar, selecione o tamanho da máquina (padrão: A3) com base no tamanho dos dados que a máquina processará e a potência que você deseja que a máquina tenha (tamanho da memória e número de núcleos de computação), digite um nome de usuário e senha para a máquina. Em seguida, clique na seta para a direita para ir para a próxima página de configuração.
   
    ![Criar espaço de trabalho][26]
4. Selecione a **REGIÃO/GRUPO DE AFINIDADE/REDE VIRTUAL** que contém a **CONTA DE ARMAZENAMENTO** que você planeja usar para a máquina virtual e, em seguida, selecione essa conta de armazenamento. Adicione um ponto de extremidade na parte inferior no campo **PONTOS DE EXTREMIDADE** digitando o nome do ponto de extremidade ("IPython" aqui). Você pode escolher qualquer cadeia de caracteres como o **NOME** do ponto de extremidade e qualquer inteiro entre 0 e 65536 que esteja **disponível** como a **PORTA PÚBLICA**. A **PORTA PRIVADA** deve ser **9999**. **Evite** usar portas públicas que já tenham sido atribuídas a serviços de Internet. As <a href="http://www.chebucto.ns.ca/~rakerman/port-table.html" target="_blank">Portas para Serviços de Internet</a> fornecem uma lista de portas que foram atribuídas e devem ser evitadas.
   
    ![Criar espaço de trabalho][27]
5. Clique na marca de seleção para iniciar o processo de provisionamento da máquina virtual.
   
    ![Criar espaço de trabalho][28]

Pode levar de 15 a 25 minutos para concluir o processo de provisionamento da máquina virtual. Depois que a máquina virtual tiver sido criada, o status dessa máquina deverá aparecer como **Executando**.

![Criar espaço de trabalho][29]

## <a name="add-endpoint"></a>Etapa 2: adicionar um ponto de extremidade para IPython Notebooks a uma máquina virtual existente
Se você criou a máquina virtual seguindo as instruções na Etapa 1, o ponto de extremidade para o IPython Notebook já foi adicionado e essa etapa pode ser ignorada.

Se a máquina virtual já existir e você precisar adicionar um ponto de extremidade ao IPython Notebook que será instalado na Etapa 3 abaixo, primeiro, faça logon no Portal Clássico do Azure, selecione a máquina virtual e adicione o ponto de extremidade ao servidor do IPython Notebook. A figura a seguir contém uma captura de tela do portal após o ponto de extremidade do IPython Notebook ter sido adicionado à máquina virtual do Windows.

![Criar espaço de trabalho][17]

## <a name="run-commands"></a>Etapa 3: instalar o IPython Notebook e outras ferramentas de suporte
Depois que a máquina virtual é criada, use o protocolo RDP para fazer logon na máquina virtual do Windows. Para obter instruções, veja [Como fazer logon em uma máquina virtual que executa o Windows Server](../../virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Abra o **Prompt de Comando** (**Não a janela de comando do Powershell**) como **Administrador** e execute o comando a seguir.

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Quando a instalação for concluída, o servidor do IPython Notebook será iniciado automaticamente no diretório *C:\\Users\\\<nome de usuário\>\\Documents\\IPython Notebooks*.

Quando solicitado, digite uma senha para o IPython Notebook e a senha de administrador do computador. Isso permite que o IPython Notebook seja executado como um serviço no computador.

## <a name="access"></a>Etapa 4: acesse o IPython Notebooks usando um navegador da Web
Para acessar o servidor do IPython Notebook, abra um navegador da Web e insira *https://&#60;nome DNS da máquina virtual>:&#60;número da porta pública>* na caixa de texto da URL. Aqui, o *&#60;número da porta pública>* deve ser o número da porta especificado quando o ponto de extremidade do IPython Notebook foi adicionado.

O *&#60;nome DNS da máquina virtual>* pode ser encontrado no Portal Clássico do Azure. Depois de fazer logon no Portal Clássico, clique em **MÁQUINAS VIRTUAIS**, selecione o computador que você criou e, em seguida, selecione **PAINEL**; o nome DNS será mostrado da seguinte forma:

![Criar espaço de trabalho][19]

Você encontrará um aviso informando que *Há um problema com o certificado de segurança deste site* (Internet Explorer) ou a *Sua conexão não é privada* (Chrome), conforme mostrado nas figuras a seguir. Clique em **Continuar neste site (não recomendado)** (Internet Explorer) ou **Avançado** e **Continuar em &#60;*Nome DNS*> (perigoso)** (Chrome) para continuar. Em seguida, insira a senha que você especificou anteriormente para acessar o IPython Notebooks.

**Internet Explorer:**
![Criar espaço de trabalho][20]

**Chrome:**
![Criar espaço de trabalho][21]

Depois de fazer logon no IPython Notebook, o diretório *DataScienceSamples* aparecerá no navegador. Esse diretório contém o IPython Notebooks de amostra que é compartilhado pela Microsoft para ajudar os usuários a realizar tarefas de ciência de dados. Esses IPython Notebooks de exemplo são verificados no [**repositório GitHub**](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) para as máquinas virtuais durante o processo de configuração do servidor do IPython Notebook. A Microsoft mantém e atualiza esse repositório com frequência. Os usuários podem visitar o repositório GitHub para obter a amostra do IPython Notebooks atualizada mais recentemente.
![Criar espaço de trabalho][18]

## <a name="upload"></a>Etapa 5: carregar um IPython Notebook existente de um computador local para o servidor do IPython Notebook
Os IPython Notebooks fornecem uma maneira fácil para que os usuários carreguem um IPython Notebook existente em suas máquinas locais para o servidor do IPython Notebook nas máquinas virtuais. Depois de fazer logon no IPython Notebook em um navegador da Web, clique no **diretório** no qual o IPython Notebook será carregado. Em seguida, selecione um arquivo IPYNB do IPython Notebook para carregá-lo do computador local no **Gerenciador de Arquivos**e arraste-o e solte-o no diretório IPython Notebook no navegador da Web. Clique no botão **Carregar** para carregar o arquivo IPYNB no servidor do IPython Notebook. Então, outros usuários podem começar a usá-lo em seus navegadores da web.

![Criar espaço de trabalho][22]

![Criar espaço de trabalho][23]

## <a name="shutdown"></a>Desligar e desalocar a máquina virtual quando ela não estiver em uso
A cobrança das máquinas virtuais do Azure ocorre na forma **pague somente pelo que usa**. Para garantir que você não esteja sendo cobrado quando não estiver usando sua máquina virtual, ela deverá estar no estado **Parado (Desalocado)** quando não estiver em uso.

> [!NOTE]
> Se você desligar a máquina virtual de dentro da VM (usando as opções de energia do Windows), a VM será interrompida, mas permanecerá alocada. Para garantir que você não continue sendo cobrado, sempre pare as máquinas virtuais no [Portal Clássico do Azure](http://manage.windowsazure.com/). Você também pode interromper a VM por meio do Powershell chamando **ShutdownRoleOperation** com "PostShutdownAction" igual a "StoppedDeallocated".
> 
> 

Para desligar e desalocar a máquina virtual:

1. Faça logon no [Portal Clássico do Azure](http://manage.windowsazure.com/) usando sua conta.  
2. Selecione **MÁQUINAS VIRTUAIS** na barra de navegação à esquerda.
3. Na lista de máquinas virtuais, clique no nome da máquina virtual e acesse a página **PAINEL** .
4. Na parte inferior da página, clique em **DESLIGAR**.

![Desligamento da VM][15]

A máquina virtual será desalocada, mas não excluída. É possível reiniciar a máquina virtual a qualquer momento no Portal Clássico do Azure.

## <a name="your-azure-vm-is-ready-to-use-whats-next"></a>Sua VM do Azure está pronta para uso: o que vem a seguir?
Agora, a sua máquina virtual está pronta para ser usada em seus exercícios de ciência de dados. A máquina virtual também está pronta para uso como um servidor do IPython Notebook para a exploração e o processamento de dados, bem como para outras tarefas em conjunto com o Azure Machine Learning e o Processo de Ciência de Dados de Equipe.

As próximas etapas no Processo de Ciência de Dados de Equipe são mapeadas no [Roteiro de Aprendizagem](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) e podem incluir etapas que movimentam dados para o HDInsight, que os processam e obtêm amostras deles como parte da preparação para o aprendizado com base nos dados com o Azure Machine Learning.

[15]: ./media/setup-virtual-machine/vmshutdown.png
[17]: ./media/setup-virtual-machine/add-endpoints-after-creation.png
[18]: ./media/setup-virtual-machine/sample-ipnbs.png
[19]: ./media/setup-virtual-machine/dns-name-and-host-name.png
[20]: ./media/setup-virtual-machine/browser-warning-ie.png
[21]: ./media/setup-virtual-machine/browser-warning.png
[22]: ./media/setup-virtual-machine/upload-ipnb-1.png
[23]: ./media/setup-virtual-machine/upload-ipnb-2.png
[24]: ./media/setup-virtual-machine/create-virtual-machine-1.png
[25]: ./media/setup-virtual-machine/create-virtual-machine-2.png
[26]: ./media/setup-virtual-machine/create-virtual-machine-3.png
[27]: ./media/setup-virtual-machine/create-virtual-machine-4.png
[28]: ./media/setup-virtual-machine/create-virtual-machine-5.png
[29]: ./media/setup-virtual-machine/create-virtual-machine-6.png
