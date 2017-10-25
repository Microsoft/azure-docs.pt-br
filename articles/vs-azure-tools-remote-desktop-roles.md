---
title: "Usando a Área de Trabalho Remota com funções do Azure | Microsoft Docs"
description: "Usando a Área de Trabalho Remota com as funções do Azure"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: eab135d10c0d6df8ca72ac47d6804017a998a3d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-remote-desktop-with-azure-roles"></a>Usando a Área de Trabalho Remota com as funções do Azure
Usando o SDK do Azure e os Serviços de Área de Trabalho Remota, você pode acessar as funções do Azure e máquinas virtuais que são hospedadas pelo Azure. No Visual Studio, você pode configurar os Serviços de Área de Trabalho Remota por meio de um projeto de serviço de nuvem do Azure. Para habilitar os Serviços de Área de Trabalho Remota, você deve criar um projeto de trabalho contendo uma ou mais funções e, em seguida, publicá-lo no Azure.

> [!IMPORTANT]
> Você deve acessar uma função do Azure apenas para desenvolvimento ou solução de problemas. A finalidade de cada máquina virtual é executar uma função específica em seu aplicativo Azure; a finalidade não é, portanto, executar outros aplicativos cliente. Se desejar usar o Azure para hospedar uma máquina virtual que você possa usar para qualquer finalidade, consulte Acessando máquinas virtuais do Azure por meio do Gerenciador de Servidores.
> 
> 

## <a name="to-enable-and-use-remote-desktop-for-an-azure-role"></a>Para habilitar e usar a Área de Trabalho Remota para uma Função do Azure
1. No Gerenciador de Soluções, abra o menu de atalho para o seu projeto do serviço de nuvem e escolha **Publicar**.
   
    O assistente **Publicar Aplicativo do Azure** é exibido.
   
    ![Publicar o comando para um projeto de Serviço de Nuvem](./media/vs-azure-tools-remote-desktop-roles/IC799161.png)
2. Na parte inferior da página **Configurações de Publicação do Microsoft Azure** do assistente, marque a caixa de seleção **Habilitar Área de Trabalho Remota** para todas as funções. 
   
    A caixa de diálogo **Configuração da Área de Trabalho Remota** é exibida.
3. Na parte inferior da caixa de diálogo **Configuração da Área de Trabalho Remota**, escolha o botão **Mais Opções**. 
   
    Isso exibe uma caixa de listagem suspensa que permite que você crie ou escolha um certificado para que você possa criptografar informações de credenciais ao conectar-se via área de trabalho remota.
4. Na lista suspensa, escolha **&lt;Criar>** ou escolha um já existente na lista. 
   
    Se você escolher um certificado existente, ignore as etapas a seguir.
   
   > [!NOTE]
   > Os certificados necessários para uma conexão de área de trabalho remota são diferentes dos certificados que você usa para outras operações do Azure. O certificado de acesso remoto deve ter uma chave privada.
   > 
   > 
   
    A caixa de diálogo **Criar Certificado** é exibida.
   
   1. Forneça um nome amigável para o novo certificado e, em seguida, escolha o botão **OK** . O novo certificado é exibido na caixa de listagem suspensa.
   2. Na caixa de diálogo **Configuração da Área de Trabalho Remota** , forneça um nome de usuário e uma senha.
      
       Você não pode usar uma conta existente. Não especifique Administrador como o nome de usuário para a nova conta.
      
      > [!NOTE]
      > Se a senha não atende aos requisitos de complexidade, um ícone vermelho aparece ao lado da caixa de texto de senha. A senha deve incluir letras maiúsculas, letras minúsculas e números ou símbolos.
      > 
      > 
   3. Escolha uma data em que a conta vai expirar e após a qual conexões de área de trabalho remota serão bloqueadas.
   4. Depois de ter fornecido todas as informações necessárias, escolha o botão **OK** .
      
       Várias configurações que habilitam os Serviços de Acesso Remoto são adicionadas aos arquivos .cscfg e .csdef.
5. No assistente **Configurações de publicação do Microsoft Azure**, escolha o botão **OK** quando você estiver pronto para publicar o serviço de nuvem.
   
    Se você não estiver pronto para publicar, escolha o botão **Cancelar** . As configurações são salvas, e você pode publicar o serviço de nuvem mais tarde.

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Conectar a uma Função do Azure usando a Área de Trabalho Remota
Depois de publicar o serviço de nuvem no Azure, você pode usar o Gerenciador de Servidores para fazer logon em máquinas virtuais hospedadas pelo Azure. 

1. No Gerenciador de Servidores, expanda o nó **Azure** e, em seguida, expanda o nó para um serviço de nuvem e uma de suas funções para exibir uma lista de instâncias.
2. Abra o menu de atalho de um nó de instância e, em seguida, escolha **Conectar Usando a Área de Trabalho Remota**.
   
    ![Conectando-se via área de trabalho remota](./media/vs-azure-tools-remote-desktop-roles/IC799162.png)
3. Insira o nome de usuário e a senha que você criou anteriormente. Agora você está conectado na sessão remota.

