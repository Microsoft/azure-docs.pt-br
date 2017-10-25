---
title: "Usar o serviço de renderização do Lote do Azure para renderizar na nuvem | Microsoft Docs"
description: "Processa trabalhos em máquinas virtuais do Azure diretamente do Maya e com base em pagamento por uso."
services: batch
author: v-dotren
manager: timlt
ms.service: batch
ms.topic: hero-article
ms.date: 09/14/2017
ms.author: danlep
ms.openlocfilehash: 47ccbd89d5abf04034196ab735c6740d57099023
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-batch-rendering-service"></a>Introdução ao serviço de renderização em lotes

O serviço de renderização do Lote do Azure oferece recursos de renderização em escala de nuvem com base em pagamento por uso. O serviço de renderização em lotes controla o agendamento e enfileiramento de trabalhos, gerenciando falhas e tentativas e fazendo o dimensionamento automático dos trabalhos de renderização. O Serviço de Renderização do Lote dá suporte a [Autodesk Maya](https://www.autodesk.com/products/maya/overview), [3ds Max](https://www.autodesk.com/products/3ds-max/overview), [Arnold](https://www.autodesk.com/products/arnold/overview) e [V-Ray](https://www.chaosgroup.com/vray/maya). O plug-in do Lote para o Maya 2017 facilita iniciar um trabalho de renderização no Azure diretamente da sua área de trabalho.

Com o Maya e o 3ds Max, você pode executar trabalhos usando o aplicativo de área de trabalho [Batch Labs](https://github.com/Azure/BatchLabs) ou a [CLI de Modelos do Lote](batch-cli-templates.md). Usando a CLI do Lote do Azure, você pode executar trabalhos do Lote sem codificar. Em vez disso, você pode usar arquivos de modelo para criar pools, trabalhos e tarefas do Lote. Para saber mais, confira [Usar modelos da CLI do Lote do Azure e Transferência de Arquivos](batch-cli-templates.md).


## <a name="supported-applications"></a>Aplicativos com suporte

O serviço de renderização em lotes atualmente dá suporte aos seguintes aplicativos:

- Autodesk Maya
- Autodesk 3ds Max
- Autodesk Arnold para Maya
- Autodesk Arnold para 3ds Max
- Chaos Group V-Ray para Maya
- Chaos Group V-Ray para 3ds Max

## <a name="prerequisites"></a>Pré-requisitos

Para usar o serviço de renderização em lotes, você precisa ter:

- Uma [conta do Azure](https://azure.microsoft.com/free/).
- **Uma conta do Lote do Azure.** Para obter orientação sobre como criar uma conta do Lote no portal do Azure, confira [Criar uma conta do Lote com o portal do Azure](batch-account-create-portal.md).
- **Uma conta de Armazenamento do Azure.** Os recursos usados no trabalho de renderização são armazenados no Armazenamento do Azure. Você poderá criar uma conta de armazenamento automaticamente ao configurar a sua conta do Lote. Você também pode usar uma conta de armazenamento existente. Para saber mais sobre contas de armazenamento, confira [Como criar, gerenciar ou excluir uma conta de armazenamento no portal do Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

Para usar o plug-in do Lote para Maya, você precisa ter:

- [Autodesk Maya 2017](https://www.autodesk.com/products/maya/overview).
- Um renderizador com suporte, como Arnold para Maya ou V-Ray para Maya.

## <a name="basic-batch-concepts"></a>Conceitos básicos do Lote

Antes de começar a usar o serviço de renderização em lotes, é útil estar familiarizado com alguns conceitos do Lote, incluindo trabalhos, pools e nós de computação. Para saber mais sobre o Lote do Azure em geral, confira [Executar cargas de trabalho intrinsecamente paralelas com o Lote](batch-technical-overview.md).

### <a name="pools"></a>Pools

O Lote é um serviço de plataforma para executar o trabalho de computação intensa, como renderização, em um **pool** de **nós de computação**. Cada nó de computação em um pool é uma VM (máquina virtual do Azure) que executa Linux ou Windows. 

Para saber mais sobre pools do Lote e nós de computação, confira as seções [Pool](batch-api-basics.md#pool) e [Nó de computação](batch-api-basics.md#compute-node) em [Desenvolver soluções de computação paralela em grande escala com o Lote](batch-api-basics.md).

### <a name="jobs"></a>Trabalhos

Um **trabalho** do Lote é uma coleção de tarefas que são executadas em nós de computação em um pool. Quando você envia um trabalho de renderização, o Lote divide o trabalho em tarefas e distribui as tarefas entre os nós de computação no pool para execução.

Você pode usar o [Portal do Azure](https://ms.portal.azure.com/) para monitorar trabalhos e diagnosticar tarefas com falha baixando os logs de aplicativo e conectando-se remotamente a VMs individuais usando RDP ou SSH. Você também pode gerenciar, monitorar e depurar usando o [cliente Batch Labs](https://github.com/Azure/BatchLabs).

Para saber mais sobre trabalhos do Lote, confira a seção [Trabalho](batch-api-basics.md#job) em [Desenvolver soluções de computação paralela em grande escala com o Lote](batch-api-basics.md).

## <a name="options-for-provisioning-required-applications"></a>Opções para provisionar os aplicativos obrigatórios

Vários aplicativos podem ser necessários para renderizar um trabalho, por exemplo, a combinação de Maya e Arnold ou 3ds Max e V-Ray, bem como outros plug-ins de terceiros, se for o caso. Além disso, alguns clientes podem exigir versões específicas desses aplicativos. Assim, há vários métodos disponíveis para o provisionamento de software e aplicativos obrigatórios:

### <a name="pre-configured-vm-images"></a>Imagens de VM pré-configuradas

O Azure fornece imagens Windows e Linux, cada com uma única versão de Maya, 3ds Max, Arnold e V-Ray previamente instalada e pronta para usar. Você pode selecionar essas imagens no [portal do Azure](https://portal.azure.com), o plug-in Maya ou o [Batch Labs](https://github.com/Azure/BatchLabs) ao criar um pool.

No portal do Azure e no Batch Labs, você pode instalar uma das imagens de VM com aplicativos pré-instalados da seguinte maneira: na seção Pools da sua conta do Lote, selecione **Novo** e, em **Adicionar Pool**, selecione **Gráficos e Renderização (Linux/Windows)** na lista suspensa **Tipo de imagem**:

![Selecionar tipo de imagem para a conta do Lote](./media/batch-rendering-service/add-pool.png)

Role para baixo e clique em **Licenças de gráficos e renderização** para abrir a folha **Escolher licenças** e selecione uma ou mais das licenças de software:

![Selecionar a licença de gráficos e renderização para o pool](./media/batch-rendering-service/graphics-licensing.png)

As versões de licença específicas fornecidas são as seguintes:

- Maya 2017
- 3DS Max 2018
- Arnold para Maya 5.0.1.1
- Arnold para 3ds Max 1.0.836
- V-Ray para Maya 3.52.03
- V-Ray para 3ds Max 3.60.01

### <a name="custom-images"></a>Imagens personalizadas

O Lote do Azure permite que você forneça sua própria imagem personalizada. Usando essa opção, você pode configurar sua VM exatamente com os aplicativos e as versões específicas de que você precisa. Para saber mais, confira [Usar uma imagem personalizada para criar um pool de máquinas virtuais](https://docs.microsoft.com/en-us/azure/batch/batch-custom-images). Observe que o Autodesk e o Chaos Group modificaram o Arnold e o V-Ray, respectivamente, para validar nosso próprio serviço de licenciamento. Você precisará ter as versões desses aplicativos com esse suporte; caso contrário, o licenciamento de pagamento por uso não funcionará. Essa validação de licença não é necessária para Maya ou 3ds Max, já que as versões atuais de publicadas não requerem um servidor de licença durante a execução sem cabeçalho (no modo lote/linha de comando). Entre em contato com o suporte do Azure se você não tiver certeza sobre como prosseguir com essa opção.

## <a name="options-for-submitting-a-render-job"></a>Opções para enviar um trabalho de renderização

Dependendo do aplicativo 3D que você usar, existem várias opções para enviar trabalhos de renderização para o serviço:

### <a name="maya"></a>Maya

Com o Maya, você pode usar:

- O [Plug-in do Lote para Maya](https://docs.microsoft.com/en-us/azure/batch/batch-rendering-service#use-the-batch-plug-in-for-maya-to-submit-a-render-job)
- O aplicativo de área de trabalho[Batch Labs](https://github.com/Azure/BatchLabs)
- [CLI dos modelos do Lote](batch-cli-templates.md)

### <a name="3ds-max"></a>3ds Max

Com o 3ds Max, você pode usar:

- O aplicativo de área de trabalho[Batch Labs](https://github.com/Azure/BatchLabs) (confira [Dados do Batch Labs](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax) para obter orientação sobre como usar modelos de Batch Labs do 3ds Max)
- [CLI dos modelos do Lote](batch-cli-templates.md)

Os modelos de Batch Labs do 3ds Max permitem processar cenas VRay e Arnold usando o Serviço de Renderização do Lote do Azure. Há duas variações do modelo para VRay e Arnold, uma para cenas padrão e outra para cenas mais complexas que exigem um arquivo de caminho 3ds Max para ativos e texturas (arquivo .mxp). Para saber mais sobre os modelos de Batch Labs do 3ds Max, confira o repositório [Dados do Batch Labs](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax) no GitHub.

Além disso, você pode usar o [SDK em Python do Lote](https://docs.microsoft.com/en-us/azure/batch/batch-python-tutorial) para integrar o serviço de renderização de pipeline existente.


## <a name="use-the-batch-plug-in-for-maya-to-submit-a-render-job"></a>Usar o plug-in do Lote para Maya a fim de enviar um trabalho de renderização

Com o plug-in do Lote para Maya, você pode enviar um trabalho para o serviço de renderização do Lote diretamente do Maya. As seções a seguir descrevem como configurar o trabalho de plug-in e enviá-lo. 

### <a name="load-the-batch-plug-in-for-maya"></a>Carregar o plug-in do Lote para Maya

O plug-in do Lote está disponível no [GitHub](https://github.com/Azure/azure-batch-maya/releases). Descompacte o arquivo em um diretório à sua escolha. Você pode carregar o plug-in diretamente do diretório *azure_batch_maya*.

Para carregar o plug-in no Maya:

1. Execute o Maya.
2. Abra **Janela** > **Configurações/Preferências** > **Gerenciador de Plug-in**.
3. Clique em **Procurar**.
4. Navegue até o local e selecione *azure_batch_maya/plug-in/AzureBatch.py*.

### <a name="authenticate-access-to-your-batch-and-storage-accounts"></a>Autenticar o acesso às suas contas do Lote e do Armazenamento

Para usar o plug-in, você precisa autenticar usando suas chaves de conta do Lote do Azure e do Armazenamento do Azure. Para recuperar as chaves da conta:

1. Exiba o plug-in no Maya e selecione a guia **Config**.
2. Navegue até o [Portal do Azure](https://portal.azure.com).
3. Selecione **Contas do Lote** no menu à esquerda. Se necessário, clique em **Mais Serviços** e filtre com _Lote_.
4. Localize a conta do Lote desejada na lista.
5. Selecione o item de menu **Chaves** para exibir o nome da conta, a URL de conta e as chaves de acesso:
    - Cole a URL de conta do Lote no campo **Serviço** no plug-in do Lote.
    - Cole o nome da conta no campo **Conta do Lote**.
    - Cole a chave de conta principal no campo **Chave do Lote**.
7. Selecione as Contas de Armazenamento no menu à esquerda. Se necessário, clique em **Mais Serviços** e filtre com _Armazenamento_.
8. Localize a conta de Armazenamento desejada na lista.
9. Selecione o item de menu **Chaves de Acesso** para exibir o nome de conta de armazenamento e as chaves.
    - Cole o nome da conta de armazenamento no campo **Conta do Armazenamento** no plug-in do Lote.
    - Cole a chave de conta principal no campo **Chave do Armazenamento**.
10. Clique em **Autenticar** para verificar se o plug-in pode acessar as duas contas.

Depois de autenticado com êxito, o plug-in define o campo de status como **Autenticado**: 

![Autenticar suas contas do Lote e do Armazenamento](./media/batch-rendering-service/authentication.png)

### <a name="configure-a-pool-for-a-render-job"></a>Configurar um pool para um trabalho de renderização

Depois que você autentica suas contas do Lote e do Armazenamento, configure um pool para o trabalho de renderização. O plug-in salva suas seleções entre sessões. Depois de definir sua configuração preferencial, você não precisará modificá-la se ela não for alterada.

As seções abaixo mostram as opções disponíveis, na guia **Enviar**:

#### <a name="specify-a-new-or-existing-pool"></a>Especificar um pool novo ou existente

Para especificar um pool no qual executar o trabalho de renderização, selecione a guia **Enviar**. Esta guia oferece opções para criar um pool ou selecionar um pool existente:

- Você pode **provisionar um pool para este trabalho automaticamente** (a opção padrão). Quando você escolhe essa opção, o Lote cria o pool exclusivamente para o trabalho atual e exclui o pool automaticamente quando o trabalho de renderização é concluído. Essa opção é recomendada quando você tem um único trabalho de renderização para concluir.
- Você pode **reutilizar um pool persistente existente**. Se você tiver um pool existente que esteja ocioso, poderá especificar esse pool para executar o trabalho de renderização selecionando-o na lista suspensa. A reutilização de um pool persistente existente economiza o tempo que seria gasto para provisionar o pool.  
- Você pode **criar um novo pool persistente**. Essa opção cria um novo pool para executar o trabalho. Ele não exclui o pool quando o trabalho é concluído, para que você possa reutilizá-lo em outros trabalhos. Selecione essa opção quando você precisar executar trabalhos de renderização de forma contínua. Em trabalhos subsequentes, você pode selecionar **Reutilizar um pool persistente existente** para usar o pool persistente criado para o primeiro trabalho.

![Especificar pool, imagem do sistema operacional, tamanho da VM e licença](./media/batch-rendering-service/submit.png)

Para saber mais sobre como as cobranças se acumulam em relação a máquinas virtuais do Azure, confira as [Perguntas frequentes sobre preços Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#faq) e [Perguntas frequentes sobre preços Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/#faq).

#### <a name="specify-the-os-image-to-provision"></a>Especifique a imagem do sistema operacional para provisionar

Você pode especificar o tipo de imagem do sistema operacional para usar no provisionamento de nós de computação no pool na guia **Amb** (Ambiente). O Lote atualmente dá suporte às seguintes opções de imagem para trabalhos de renderização:

|Sistema operacional  |Imagem  |
|---------|---------|
|Linux     |Versão prévia do Lote CentOS |
|Windows     |Versão prévia do Lote Windows |

#### <a name="choose-a-vm-size"></a>Escolher um tamanho de VM

Você pode especificar o tamanho da VM na guia **Amb**. Para saber mais sobre tamanhos de VM disponíveis, confira [Tamanhos de VM Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) e [Tamanhos de VM Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes). 

![Especificar a imagem do sistema operacional e o tamanho da VM na guia Amb](./media/batch-rendering-service/environment.png)

#### <a name="specify-licensing-options"></a>Especificar opções de licenciamento

Você pode especificar as licenças que deseja usar na guia **Amb**. As opções incluem:

- **Maya**, que é habilitado por padrão.
- **Arnold**, que será habilitado se for detectado como o mecanismo de renderização ativo no Maya.

 Se você deseja renderizar usando sua própria licença, pode configurar o ponto de extremidade da licença adicionando as variáveis de ambiente apropriadas à tabela. Não deixe de desmarcar as opções de licenciamento padrão, se for o caso.

> [!IMPORTANT]
> Você será cobrado pelo uso das licenças durante a execução das máquinas virtuais no pool, mesmo se as VMs não estiverem sendo usadas para renderização no momento. Para evitar cobranças adicionais, navegue até a guia **Pools** e redimensione o pool para 0 nós até estar pronto para executar outro trabalho de renderização. 
>
>

#### <a name="manage-persistent-pools"></a>Gerenciar pools persistentes

Você pode gerenciar um pool persistente existente na guia **Pools**. A seleção de um pool na lista exibe o estado atual do pool.

Na guia **Pools**, você também pode excluir o pool e redimensionar o número de VMs no pool. Você pode redimensionar um pool para 0 nós a fim de evitar incorrer em custos entre cargas de trabalho.

![Exibir, redimensionar e excluir pools](./media/batch-rendering-service/pools.png)

### <a name="configure-a-render-job-for-submission"></a>Configurar um trabalho de renderização para envio

Depois de especificar os parâmetros para o pool que executará o trabalho de renderização, configure o trabalho propriamente dito. 

#### <a name="specify-scene-parameters"></a>Especificar parâmetros de cena

O plug-in do Lote detecta qual mecanismo de renderização você está usando atualmente no Maya e exibe as configurações de renderização na guia **Enviar**. Essas configurações incluem o quadro inicial, o quadro final, o prefixo de saída e a etapa do quadro. Você pode substituir as configurações de renderização do arquivo de cena especificando configurações diferentes no plug-in. As alterações feitas às configurações do plug-in não são persistidas para as configurações de renderização do arquivo de cena, para que você possa fazer alterações com base em cada trabalho sem a necessidade de recarregar o arquivo de cena.

O plug-in avisa se o mecanismo de renderização selecionado no Maya não tem suporte.

Se você carregar uma nova cena enquanto o plug-in estiver aberto, clique no botão **Atualizar** para verificar se as configurações foram atualizadas.

#### <a name="resolve-asset-paths"></a>Resolver caminhos de ativos

Quando você carrega o plug-in, ele examina referências a arquivos externos no arquivo de cena. Essas referências são exibidas na guia **Ativos**. Se um caminho de referência não pode ser resolvido, o plug-in tenta localizar o arquivo em alguns locais padrão, incluindo:

- O local do arquivo de cena 
- O diretório _sourceimages_ do projeto atual
- O diretório de trabalho atual. 

Se o ativo ainda não puder ser localizado, ele será listado com um ícone de aviso:

![Os ativos ausentes são exibidos com um ícone de aviso](./media/batch-rendering-service/missing_assets.png)

Se você souber a localização de uma referência de arquivo não resolvida, poderá clicar no ícone de aviso para poder adicionar um caminho de pesquisa. O plug-in, em seguida, usará esse caminho de pesquisa para tentar resolver os recursos ausentes. Você pode adicionar quantos caminhos de pesquisa adicionais quiser.

Quando uma referência é resolvida, ela é listada com um ícone de luz verde:

![Ativos resolvidos mostram um ícone verde claro](./media/batch-rendering-service/found_assets.png)

Se sua cena exigir outros arquivos que o plug-in não detectou, você poderá adicionar outros arquivos ou diretórios. Use os botões **Adicionar Arquivos** e **Adicionar Diretório**. Se você carregar uma nova cena enquanto o plug-in estiver aberto, não deixe de clicar em **Atualizar** para atualizar as referências da cena.

#### <a name="upload-assets-to-an-asset-project"></a>Carregar ativos em um projeto de ativo

Quando você envia um trabalho de renderização, os arquivos de referência exibidos na guia **Ativos** são automaticamente carregados no Armazenamento do Azure como um projeto de ativo. Você também pode carregar os arquivos de ativos independentemente de uma renderização de trabalho, usando o botão **Carregar** na guia **Ativos**. O nome do projeto de ativo é especificado no campo **Projeto** e recebe o nome de acordo com o projeto atual do Maya por padrão. Quando arquivos de ativo são carregados, a estrutura do arquivo local é preservada. 

Uma vez carregados, os ativos podem ser referenciados por qualquer quantidade de trabalhos de renderização. Todos os ativos carregados estão disponíveis para qualquer trabalho que faça referência ao projeto de ativo, independentemente de estarem ou não incluídos na cena. Para alterar o projeto de ativo referenciado pelo próximo trabalho, altere o nome no campo **Projeto** na guia **Ativos**. Se houver arquivos de referência que você deseja excluir do carregamento, desmarque-os usando o botão verde ao lado do item na lista.

#### <a name="submit-and-monitor-the-render-job"></a>Enviar e monitorar o trabalho de renderização

Depois de configurar o trabalho de renderização no plug-in, clique no botão **Enviar Trabalho** na guia **Enviar** a fim de enviar o trabalho para o Lote:

![Enviar o trabalho de renderização](./media/batch-rendering-service/submit_job.png)

Você pode monitorar um trabalho em andamento na guia **Trabalhos** no plug-in. Selecione um trabalho na lista para exibir o estado atual dele. Você também pode usar essa guia para cancelar e excluir trabalhos, e também para baixar as saídas e logs de renderização. 

Para baixar as saídas, modifique o campo **Saídas** para definir o diretório de destino desejado. Clique no ícone de engrenagem para iniciar um processo em segundo plano que observa o trabalho e baixa as saídas conforme o andamento: 

![Exibir status do trabalho e baixar saídas](./media/batch-rendering-service/jobs.png)

Você pode fechar o Maya sem interromper o processo de download.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Lote, confira [Executar cargas de trabalho intrinsecamente paralelas com o Lote](batch-technical-overview.md).