---
title: Provisionar uma Máquina Virtual de Inteligência Artificial Geográfica no Azure – Azure | Microsoft Docs
description: Saiba como criar e configurar a Máquina Virtual de Ciência de Dados de AI Geográfica. A Máquina Virtual de Ciência de Dados de AI Geográfica fornece as ferramentas para criar soluções de ML e AI usando dados geográficos.
keywords: aprendizado profundo, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise Geoespacial
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: dde9b71c3615a592f8c08e040c5e9ba7bc756bd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578500"
---
# <a name="provision-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Provisionar uma Máquina Virtual de Inteligência Artificial Geográfica no Azure 

A Geo-DSVM (Máquina Virtual de Ciência de Dados de IA Geográfica) é uma extensão da popular [Máquina Virtual de Ciência de Dados do Azure](https://aka.ms/dsvm) configurada especialmente para combinar a IA com análise geoespacial. A análise geoespacial na VM é fornecida pelo [ArcGIS Pro](https://www.arcgis.com/features/index.html). A VM de Ciência de Dados permite o treinamento rápido de modelos de aprendizado de máquina e até mesmo de modelos de aprendizado profundo, nos dados que são aprimorados com informações geográficas. Ela é compatível apenas com a Windows 2016 DSVM. 

A Geo-DSVM contém várias ferramentas para IA, incluindo:

- Edições de GPU de estruturas de aprendizado populares, como o Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2 e Chainer; 
- ferramentas para adquirir e pré-processar imagens e dados textuais, 
- ferramentas para atividades de desenvolvimento, como o Microsoft R Server Developer Edition, Anaconda Python, Jupyter Notebooks para Python e R, IDEs para Python e R, bancos de dados SQL
- Software de área de trabalho do ArcGIS Pro do ESRI, junto com as interfaces do Python e R que podem trabalhar com os dados geoespaciais por meio dos aplicativos de IA. 
 

## <a name="create-your-geo-ai-data-science-vm"></a>Criar sua VM de Ciência de Dados de IA Geográfica

Este é o procedimento para criar uma instância da VM de Ciência de Dados de IA Geográfica: 


1. Navegue até a máquina virtual no [portal do Azure](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
2. Selecione o botão **Criar** na parte inferior para ser levado para um assistente.
![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)
3. O assistente usado para criar a Geo-DSVM exige **entradas** para cada uma das **quatro etapas** enumeradas à direita desta figura. Aqui estão as entradas necessárias para configurar cada uma das seguintes etapas:



   - **Noções básicas**

      1. **Nome**: Nome do servidor de ciência de dados que está sendo criado.

      2. **Nome de Usuário**: Identificação de logon da conta do administrador.

      3. **Senha**: Senha da conta do administrador.

      4. **Assinatura**: Se você tiver mais de uma assinatura, selecione aquela em que o computador será criado e cobrado.

      5. **Grupo de Recursos**: É possível criar um novo ou usar um grupo de recursos existente **vazio** do Azure na assinatura.

      6. **Localização**: Selecione o data center mais apropriado. Normalmente, é o datacenter que contém a maioria dos seus dados ou que está mais próximo de sua localização física para o acesso mais rápido à rede. Caso precise executar um aprendizado profundo na GPU, escolha um dos locais no Azure que têm as instâncias de VM da GPU da Série NC. Atualmente, as localizações que têm VMs de GPU são: **Leste dos EUA, Centro-Norte dos EUA, Centro-Sul dos EUA, Oeste dos EUA 2, Europa Setentrional, Europa Ocidental**. Para obter a lista mais recente, verifique os [Produtos do Azure pela página da região](https://azure.microsoft.com/regions/services/) e procure **Série NC** em **Computação**. 


   - **Configurações**: Selecione um dos tamanhos de máquina virtual da GPU da Série NC, caso planeje executar um aprendizado profundo na GPU da DSVM Geográfica. Caso contrário, escolha uma das instâncias baseadas em CPU.  Crie uma conta de armazenamento para sua VM. 
   
   - **Resumo**: Verifique se todas as informações inseridas estão corretas.

   - **Comprar**: Clique em **Comprar** para iniciar o provisionamento. Um link para os termos do serviço é fornecido. A VM não tem encargos adicionais além dos de computação para o tamanho do servidor que você escolheu na etapa **Tamanho** . 
 
>[!NOTE]
> O provisionamento deve levar cerca de 20 a 30 minutos. O status do provisionamento é exibido no Portal do Azure.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Como acessar a Máquina Virtual de Ciência de Dados de IA Geográfica

 Depois de criar a VM, você estará pronto para começar a usar as ferramentas que estão instaladas e pré-configuradas nela. Há blocos do menu Iniciar e ícones da área de trabalho para várias das ferramentas. Você poderá entrar na área de trabalho remota usando as credenciais da conta do Administrador configurada anteriormente na seção anterior **Informações básicas**. 

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>Usando o ArcGIS Pro instalado na VM

A Geo-DSVM já tem a área de trabalho do ArcGIS Pro pré-instalada e o ambiente pré-configurado para trabalhar com todas as ferramentas na DSVM. Quando você inicia o ArcGIS, ele solicita um logon para sua conta do ArcGIS. Se você já tiver uma conta do ArcGIS e as licenças do software, use suas credenciais existentes.  

![Arc-GIS-Logon](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Caso contrário, inscreva-se para uma nova conta do ArcGIS e a licença ou obtenha uma [avaliação gratuita](https://www.arcgis.com/features/free-trial.html). 

![ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Após a inscrição para qualquer uma paga ou gratuita ArcGIS conta de avaliação, você pode autorizar ArcGIS Pro para sua conta seguindo as instruções em [Introdução ao ArcGIS Pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf). 

Depois de entrar na área de trabalho do ArcGIS Pro com sua conta do ArcGIS, você estará pronto para começar a usar as ferramentas de ciência de dados instaladas e configuradas na VM para seus projetos de aprendizado de máquina e análise Geoespacial.

## <a name="next-steps"></a>Próximas etapas

Comece a usar a VM de Ciência de Dados de IA Geográfica com diretrizes dos seguintes tópicos:

* [Usar a VM de Ciência de Dados de IA Geográfica](use-geo-ai-dsvm.md)
