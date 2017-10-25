---
title: "Aprender a usar uma área restrita do Hadoop – emulador – Azure HDInsight | Microsoft Docs"
description: "Para começar a aprender a usar o ecossistema Hadoop, você pode definir uma área restrita do Hadoop da Hortonworks em uma máquina virtual do Azure. "
keywords: "emulador do hadoop,área restrita do hadoop"
editor: cgronlun
manager: jhubbard
services: hdinsight
author: nitinme
documentationcenter: 
tags: azure-portal
ms.assetid: 6ad5bb58-8215-4e3d-a07f-07fcd8839cc6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 40706ca30795d72120ff3edd728087d025a8b6c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-a-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Introdução a uma área restrita Hadoop, um emulador em uma máquina virtual

Saiba como instalar uma área restrita do Hadoop da Hortonworks em uma máquina virtual para saber mais sobre o ecossistema do Hadoop. A área restrita fornece um ambiente de desenvolvimento local para saber mais sobre o Hadoop, o HDFS (Sistema de Arquivos Distribuído Hadoop) e o envio de trabalhos. Quando estiver familiarizado com o Hadoop, você poderá começar a usar o Hadoop no Azure, criando um cluster do HDInsight. Para saber mais sobre como começar, confira [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Pré-requisitos
* [Oracle VirtualBox](https://www.virtualbox.org/). Baixe-o e instale-o [aqui](https://www.virtualbox.org/wiki/Downloads).



## <a name="download-and-install-the-virtual-machine"></a>Baixar e instalar a máquina virtual
1. Navegue até os [downloads da Hortonworks](http://hortonworks.com/downloads/#sandbox).

2. Clique em **BAIXAR PARA VIRTUALBOX** para baixar a Área Restrita da Hortonworks mais recente em uma VM. Será solicitado que você se registre na Hortonworks antes de iniciar o download. O download leva de uma a duas horas, dependendo da velocidade da sua rede.
   
    ![Imagem de link para download da Hortonworks Sandbox para VirtualBox](./media/hdinsight-hadoop-emulator-get-started/download-sandbox.png)
3. Na mesma página da Web, clique no link **Import on Virtual Box** para baixar um PDF contendo instruções de instalação para a máquina virtual.

Para baixar uma área restrita da versão mais antiga do HDP, expanda o arquivamento:

![Arquivamento da Área Restrita da Hortonworks](./media/hdinsight-hadoop-emulator-get-started/hortonworks-sandbox-archive.png)


## <a name="start-the-virtual-machine"></a>Iniciar a máquina virtual

1. Abra o Oracle VM VirtualBox.
2. No menu **Arquivo**, clique em **Importar Dispositivo** e especifique a imagem da Área Restrita da Hortonworks.
1. Selecione a Área Restrita da Hortonworks, clique em **Iniciar** e em **Início Normal**. Quando a máquina virtual tiver terminado o processo de inicialização, ela exibirá instruções de logon.
   
    ![Início Normal](./media/hdinsight-hadoop-emulator-get-started/normal-start.png)
2. Abra um navegador da Web e acesse a URL exibida (geralmente http://127.0.0.1:8888).

## <a name="set-sandbox-passwords"></a>Definir senhas da Área Restrita

1. Na etapa de **introdução** da página da Hortonworks Sandbox, selecione **Exibir Opções Avançadas**. Use as informações desta página para fazer logon na área restrita usando SSH. Use o nome e a senha fornecidos.
   
   > [!NOTE]
   > Se você não tiver um cliente SSH instalado, use o SSH baseado na Web fornecido pela máquina virtual em **http://localhost:4200/**.
   > 
   
    Na primeira vez que você se conectar usando SSH, você receberá uma solicitação para alterar a senha da conta raiz. Insira uma nova senha, que você usa quando faz logon usando SSH.

2. Depois de conectado, digite o seguinte comando:
   
        ambari-admin-password-reset
   
    Quando receber uma solicitação, forneça uma senha para a conta de administrador do Ambari. Isso é usado quando você acessa a interface do usuário da Web do Ambari.

## <a name="use-hive-commands"></a>Usar comandos do Hive

1. De uma conexão SSH com a área restrita, use o seguinte comando para iniciar o shell do Hive:
   
        hive
2. Quando o shell for iniciado, use o seguinte para exibir as tabelas que são fornecidas com a área restrita:
   
        show tables;
3. Use o seguinte para recuperar 10 linhas da tabela `sample_07` :
   
        select * from sample_07 limit 10;

## <a name="next-steps"></a>Próximas etapas
* [Aprenda a usar o Visual Studio com a Hortonworks Sandbox](hdinsight-hadoop-emulator-visual-studio.md)
* [Learning the ropes of the Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop tutorial - Getting started with HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

