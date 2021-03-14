---
title: Implantar o micro Focus Enterprise Server 5,0 em AKS | Microsoft Docs
description: Rehospede suas cargas de trabalho de mainframe do IBM z/OS usando o ambiente de desenvolvimento e teste micro Focus em VMs (máquinas virtuais) do Azure.
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 9e5b3857c2252a939080206fb1f92cc422f326fc
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564349"
---
# <a name="deploy-micro-focus-enterprise-server-50-to-aks"></a>Implantar o micro Focus Enterprise Server 5,0 em AKS

Em outro [artigo](./run-enterprise-server-container.md), descrevi as etapas para executar o micro Focus Enterprise Server 5,0 em um contêiner do Docker. Como um acompanhamento, gostaria de mostrar a você como levá-lo uma etapa além e implantar essa imagem do Docker que você criou no serviço kubernetes do Azure (AKS).

O serviço kubernetes do Azure é um serviço de orquestração gerenciado baseado em kubernetes. Ele permite que você implante, dimensione e gerencie contêineres do Docker (e outros aplicativos baseados em contêiner) em um cluster de hosts de contêiner.

Esse é um processo de três etapas. Você precisa:

1.  Crie um registro de contêiner do Azure para armazenar a imagem do Docker.

2.  Crie um cluster kubernetes do Azure para executar a imagem do Docker.

3.  Execute o aplicativo.

Isso permite que você Escale horizontalmente (e reduza verticalmente) suas cargas de trabalho de modernização de mainframe no Azure, aproveitando a verdadeira vantagem da plataforma em nuvem.

Esteja? Vamos começar!

## <a name="create-the-azure-container-registry"></a>Criar o registro de contêiner do Azure

No portal do Azure, selecione **criar um recurso** no canto superior esquerdo. No painel do Marketplace, selecione **contêineres e** **registro de contêiner**. Isso leva você para o painel **criar registro de contêiner** , onde você precisa preencher o **nome do registro**, a **assinatura do Azure**, o **grupo de recursos** e o **local**. O **nome do registro** precisa ser resolvido e, portanto, deve ser exclusivo. Selecione o **grupo de recursos** que você usou na postagem anterior do blog e o mesmo **local** correspondente. Selecione **habilitar** para **usuário administrador** e **básico** para a **SKU**. Depois de ter tudo preenchido, selecione **criar**.

![Criar interface de registro de contêiner](media/deploy-image-1.png)

Depois que o registro tiver sido implantado, selecione **ir para o recurso**. Isso levará você à folha principal para o registro de contêiner do Azure. Um bom recurso aqui é a opção de menu **início rápido** . Selecione-o e você verá instruções sobre o que precisa ser feito para enviar por push e efetuar pull de imagens de e para o registro. Vamos embora:

1.  **Instalar o Docker** – você não precisa se preocupar com isso porque ele já foi feito.

2.  **Executar a imagem de base "Olá – mundo"** – novamente, não é necessário, mas fique à vontade para experimentá-la.

3.  **Faça logon no registro de contêiner** – você precisa fazer isso na VM (máquina virtual). Copie o comando para a área de transferência ou o bloco de notas.

    Para o registro que criei, o comando é: **Docker login acrmf50.azurecr.Io**

4.  **Enviar por push para o registro** – você precisa fazer isso para a imagem micro Focus, assim que tiver feito logon na VM.

5.  Efetuar **pull do registro** – não é relevante para este passo a passo, mas é bom saber se você precisa executar outra imagem do Docker.

Antes de sair do portal, você precisa obter as credenciais do registro para que possa fazer logon. Saia da folha **início rápido** e selecione chaves de **acesso** no menu registro. Copie o **nome de usuário** e uma das **senhas** (há dois) para a área de transferência ou o bloco de notas. Você precisará delas mais tarde para fazer logon.

Agora que você sabe o que precisa ser feito, faça logon na VM.

## <a name="rdp-to-the-virtual-machine-you-used-to-create-the-docker-image"></a>RDP para a máquina virtual que você usou para criar a imagem do Docker

Como você já criou a imagem do Docker em um servidor Windows 2016, você precisa fazer logon nessa VM. Nessa VM, você pode enviar a imagem para o registro de contêiner do Azure que acabou de criar. Navegue até a VM na portal do Azure e, em seguida, selecione **visão geral** e **conectar**. Isso conecta você à VM por meio do RDP (Remote Desktop Protocol). Você precisa usar as credenciais de quando criou a VM.

## <a name="log-in-and-push-the-image-to-the-registry"></a>Faça logon e envie a imagem por push para o registro

Depois de fazer logon, abra um prompt de comando e inicie os seguintes comandos do Docker:

-   **imagens do Docker** – mostra uma lista de todas as imagens atualmente instaladas na VM. Anote o **subfocus/es-acctdemo** porque esse é aquele com o qual você trabalhará.

-   **Acrmf50.azurecr.Io de logon do Docker** – o formato correto aqui é o *\<registry name\> logon do Docker*. Substitua qualquer nome que você usou ao criar o registro.

    -   Será necessário o **nome de usuário** e a **senha** que você copiou do portal do Azure. Você deverá ver algo como a imagem a seguir.

    ![Captura de tela de um prompt de comando do administrador](media/deploy-image-2.png)

-   **microfoco de marca do Docker/es-acctdemo acrmf50.azurecr.Io/es-acctdemo** – isso marca a imagem apropriada com o nome do repositório. **Observação**: se o nome não \<microfocus/es-acctdemo\> funcionar, tente usar a ID da imagem completa. Depois de executar o comando, digite **imagens do Docker – no-trunc**. Você deve ver algo como a próxima imagem. Observe que a imagem está marcada corretamente.

    ![Selecionar tela de prompt de comando do administrador](media/deploy-image-3.png)

-   **Docker push acrmf50.azurecr.Io/es-acctdemo** – isso inicia o push real para seu repositório. Como o tamanho é de 15 GB, demora alguns minutos para ser executado. Se tudo der certo, você verá algo parecido com a imagem a seguir.

    ![Tela de prompt de comando do administrador](media/deploy-image-4.png)

Agora, volte para o portal do Azure, especificamente para o **repositório**. No menu do **repositório**, selecione **repositórios** e você deverá ver **es-acctdemo** listadas. Agora, crie o cluster AKS.

## <a name="create-the-azure-kubernetes-aks-cluster"></a>Criar o cluster kubernetes (AKS) do Azure

No portal do Azure, selecione **criar um recurso** e, em seguida, **contêineres/serviço kubernetes** no menu do **Marketplace** . Em seguida, você precisa preencher a folha **criar cluster kubernetes** . Lembre-se de manter o cluster na mesma região e no mesmo grupo de recursos que você está usando. Você pode aceitar o restante dos padrões, exceto a contagem de **nós,** que só precisa ser 1. Quando terminar, selecione **Revisar + criar**.

![Tela criar cluster do kubernetes](media/deploy-image-5.png)

Ao concluir, a implantação posicionará os artefatos do **serviço kubernetes** no **grupo de recursos** selecionado na folha. No entanto, o cluster real terá seu próprio grupo de recursos criado durante a implantação. Se você selecionar **grupos de recursos** no menu à esquerda, poderá encontrá-lo com base na Convenção de nomenclatura. Aqui está uma imagem do meu – é a última na lista.

![Captura de tela de grupos de recursos](media/deploy-image-6.png)

**Executar a imagem**

Agora é hora de puxar a imagem e executá-la em AKS. A maneira mais fácil de fazer isso da portal do Azure é usar o Cloud Shell. Você encontrará o ícone na parte superior direita da portal do Azure. Observe que, para este passo a passos, estou usando o shell bash.

![Captura de tela do ícone de Cloud Shell](media/deploy-image-7.png)

Depois que o Shell for carregado, digite o seguinte comando:

**kubectl executar es-acctdemo--Image acrmf50.azurecr.io/es-acctdemo--Port = 9040**

Isso efetua pull da imagem do repositório **acrmf50.azurecr.Io** e a carrega em AKs. Em seguida, ele executa a imagem com a porta 9040 aberta. Você pode se lembrar de que essa era a porta que você abriu para a imagem do Docker. É necessário que o acesse o Enterprise Server.

Kubernetes deve responder com uma mensagem informando que a implantação foi criada.

![Captura de tela de uma mensagem de implantação](media/deploy-image-8.jpg)

Para ver se o contêiner está realmente em execução, digite: **kubectl Get pods**.

Você deve ver es-acctdemo como um pod em execução, como na imagem a seguir.

![Captura de tela es-acctdemo como um pod em execução](media/deploy-image-9.png)

Parabéns! Agora você está executando o servidor corporativo no serviço kubernetes do Azure. No próximo artigo, mostrarei como acessar o console administrativo do servidor corporativo e também como aproveitar o kubernetes para escalar horizontalmente sua implantação.
