---
title: Configurar uma identidade comum
titleSuffix: Azure Data Science Virtual Machine
description: Saiba como criar contas de usuário comuns que podem ser usadas em várias Máquinas Virtuais de Ciência de Dados. Você pode usar o Azure Active Directory ou um Active Directory local para autenticar usuários na Máquina Virtual de Ciência de Dados.
keywords: aprendizado profundo, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial, processo de ciência de dados da equipe
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 3dc6fb64f6e8695d84e292322293998e2f4cb0a3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009221"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Configurar uma identidade comum em um Máquina Virtual de Ciência de Dados

Em um Microsoft Azure VM (máquina virtual), incluindo um Máquina Virtual de Ciência de Dados (DSVM), você cria contas de usuário local ao provisionar a VM. Os usuários então se autenticam na VM usando essas credenciais. Se você tiver várias VMs que seus usuários precisam acessar, o gerenciamento de credenciais poderá ficar muito complicado. Uma solução excelente é implantar contas de usuário e gerenciamento comuns por meio de um provedor de identidade baseado em padrões. Por meio dessa abordagem, você pode usar um único conjunto de credenciais para acessar vários recursos no Azure, incluindo vários DSVMs.

Active Directory é um provedor de identidade popular e tem suporte no Azure como um serviço de nuvem e como um diretório local. Você pode usar o Azure AD (Azure Active Directory) ou o Active Directory local para autenticar usuários em uma DSVM autônoma ou em um cluster de DSVMs em um conjunto de dimensionamento de máquina virtual do Azure. Para isso, integre as instâncias de DSVM em um domínio do Active Directory.

Se você já tiver Active Directory, poderá usá-lo como seu provedor de identidade comum. Se você não tiver Active Directory, poderá executar uma instância de Active Directory gerenciada no Azure por meio de [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) (Azure AD DS).

A documentação do [Azure ad](../../active-directory/index.yml) fornece [instruções de gerenciamento](../../active-directory/hybrid/whatis-hybrid-identity.md)detalhadas, incluindo diretrizes sobre como conectar o Azure AD ao seu diretório local, se você tiver um.

Este artigo descreve como configurar um serviço de domínio Active Directory totalmente gerenciado no Azure usando o AD DS do Azure. Em seguida, você pode ingressar seu DSVMs no domínio Active Directory gerenciado. Essa abordagem permite que os usuários acessem um pool de DSVMs (e outros recursos do Azure) por meio de uma conta de usuário e credenciais comuns.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Configurar um domínio do Active Directory totalmente gerenciado no Azure

O Azure AD DS simplifica o gerenciamento de identidades, fornecendo um serviço totalmente gerenciado no Azure. Nesse domínio do Active Directory, você pode gerenciar usuários e grupos. Para configurar um domínio Active Directory hospedado pelo Azure e contas de usuário em seu diretório, siga estas etapas:

1. No portal do Azure, adicione o usuário ao Active Directory: 

   1. Entre no centro de [Administração do Azure Active Directory](https://aad.portal.azure.com) usando uma conta que seja um administrador global do diretório.
    
   1. Selecione **Azure Active Directory** e, em seguida, **Usuários e grupos**.
    
   1. Em **usuários e grupos**, selecione **todos os usuários** e, em seguida, selecione **novo usuário**.
   
        O painel **usuário** é aberto:
      
        ![O painel "Usuário"](./media/add-user.png)
    
   1. Insira os detalhes do usuário, como **Nome** e **Nome de usuário**. A parte de nome de domínio do nome de usuário deve ser o nome de domínio padrão inicial "[nome de domínio].onmicrosoft.com" ou um [nome de domínio personalizado](../../active-directory/fundamentals/add-custom-domain.md) verificado e não federado, como "contoso.com".
    
   1. Copie ou anote a senha de usuário gerada para que você possa fornecê-la ao usuário depois que esse processo estiver concluído.
    
   1. Opcionalmente, você pode abrir e preencher as informações em **Perfil**, **Grupos** ou **Função do diretório** para o usuário. 
    
   1. Em **usuário**, selecione **criar**.
    
   1. Distribua com segurança a senha gerada para o novo usuário para que ele possa entrar.

1. Crie uma instância do Azure AD DS. Siga as instruções em  [habilitar Azure Active Directory Domain Services usando o portal do Azure](../../active-directory-domain-services/tutorial-create-instance.md) (a seção "criar uma instância e definir configurações básicas"). É importante atualizar as senhas de usuários existentes no Active Directory para que a senha do Azure AD DS fique sincronizada. Também é importante adicionar o DNS ao Azure AD DS, conforme descrito em "concluir os campos na janela noções básicas do portal do Azure para criar uma instância de AD DS do Azure" nessa seção.

1. Crie uma sub-rede DSVM separada na rede virtual criada na seção "criar e configurar a rede virtual" da etapa anterior.
1. Crie uma ou mais instâncias DSVM na sub-rede DSVM.
1. Siga as [instruções](../../active-directory-domain-services/join-ubuntu-linux-vm.md) para adicionar o DSVM ao Active Directory. 
1. Monte um compartilhamento de arquivos do Azure para hospedar seu diretório base ou notebook para que seu espaço de trabalho possa ser montado em qualquer computador. (Se precisar de permissões rígidas em nível de arquivo, você precisará do NFS (sistema de arquivos de rede) em execução em uma ou mais VMs.)

   1. [Criar um compartilhamento de arquivos do Azure](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Monte esse compartilhamento no DSVM do Linux. Quando você seleciona **conectar** para o compartilhamento de arquivos do Azure em sua conta de armazenamento no portal do Azure, o comando a ser executado no Shell do bash no DSVM do Linux é exibido. O comando se parece com o seguinte:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Por exemplo, suponha que você montou o compartilhamento de arquivos do Azure no/data/Workspace. Agora, crie diretórios para cada um de seus usuários no compartilhamento:/data/Workspace/user1,/data/Workspace/user2 e assim por diante. Criar um diretório `notebooks` no workspace de cada usuário. 
1. Criar links simbólicos para `notebooks` em `$HOME/userx/notebooks/remote`.   

Agora você tem os usuários em sua instância de Active Directory hospedados no Azure. Usando credenciais de Active Directory, os usuários podem entrar em qualquer DSVM (SSH ou JupyterHub) que tenha ingressado no Azure AD DS. Como o workspace do usuário está em um compartilhamento de Arquivos do Azure, os usuários têm acesso a seus notebooks e outros trabalhos de qualquer DSVM quando eles usam o JupyterHub.

Para o dimensionamento automático, você pode usar um conjunto de dimensionamento de máquinas virtuais para criar um pool de VMs que ingressaram no domínio dessa maneira e com o disco compartilhado montado. Os usuários podem entrar em qualquer computador disponível no conjunto de dimensionamento de máquinas virtuais e ter acesso ao disco compartilhado onde seus blocos de anotações são salvos. 

## <a name="next-steps"></a>Próximas etapas

* [Armazenar com segurança as credenciais para acessar recursos de nuvem](dsvm-secure-access-keys.md)