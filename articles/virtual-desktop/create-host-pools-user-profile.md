---
title: Configure um compartilhamento de perfil do usuário para um grupo de host de visualização de área de trabalho Virtual do Windows - Azure
description: Como configurar um contêiner de perfil FSLogix para um pool de host de visualização de área de trabalho Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: 692902c28b336dd46a7c6f00d5cf5a61ee9f7328
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619113"
---
# <a name="set-up-a-user-profile-share-for-a-host-pool"></a>Configurar um compartilhamento de perfil do usuário para um pool de host

O serviço de visualização de área de trabalho Virtual do Windows oferece FSLogix contêineres de perfil como a solução de perfil do usuário recomendada. Não recomendamos usando a solução de disco de perfil de usuário (UDP), que será preterida em futuras versões do Windows de área de trabalho Virtual.

Esta seção informa como configurar um compartilhamento de contêiner FSLogix perfil para um pool de host. Para obter documentação geral sobre FSLogix, consulte o [FSLogix site](https://docs.fslogix.com/).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Criar uma nova máquina virtual que atuará como um compartilhamento de arquivos

Ao criar a máquina virtual, certifique-se de colocá-lo sobre a rede virtual mesma que as máquinas de virtuais do pool de host ou em uma rede virtual que tenha conectividade com as máquinas de virtuais do pool de host. Você pode criar uma máquina virtual de várias maneiras:

- [Criar uma máquina virtual de uma imagem de galeria do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Criar uma máquina virtual de uma imagem gerenciada](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Criar uma máquina virtual a partir de uma imagem não gerenciada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Depois de criar a máquina virtual, uni-lo ao domínio, fazendo o seguinte:

1. [Conectar-se à máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) com as credenciais que você forneceu ao criar a máquina virtual.
2. Na máquina virtual, inicie **painel de controle** e selecione **sistema**.
3. Selecione **nome do computador**, selecione **alterar as configurações**e, em seguida, selecione **alteração...**
4. Selecione **domínio** e, em seguida, insira o domínio do Active Directory em uma rede virtual.
5. Autenticar com uma conta de domínio que tenha privilégios para ingressar computadores em domínio.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Preparar a máquina virtual para atuar como um compartilhamento de arquivos para perfis de usuário

A seguir estão as instruções gerais sobre como preparar uma máquina virtual para atuar como um compartilhamento de arquivos para perfis de usuário:

1. Adicionar os usuários do Active Directory do Windows Virtual da área de trabalho para um [grupo de segurança do Active Directory](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups). Esse grupo de segurança será usado para autenticar os usuários da área de trabalho Virtual do Windows para a máquina virtual de compartilhamento de arquivo que acabou de criar.
2. [Conectar-se à máquina de virtual do compartilhamento de arquivo](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine).
3. Na máquina virtual do compartilhamento de arquivo, crie uma pasta na **unidade C** que será usado como o compartilhamento de perfil.
4. Clique com botão direito na nova pasta, selecione **propriedades**, selecione **Sharing**, em seguida, selecione **compartilhamento avançado...** .
5. Selecione **compartilhar esta pasta**, selecione **permissões...** , em seguida, selecione **adicionar...** .
6. Pesquise o grupo de segurança ao qual você adicionou os usuários da área de trabalho Virtual do Windows e, em seguida, verifique se esse grupo tem **controle total**.
7. Depois de adicionar o grupo de segurança, clique com botão direito na pasta, selecione **propriedades**, selecione **compartilhamento**, em seguida, copie o **caminho de rede** usar para uso posterior.

Para obter mais informações sobre permissões, consulte a [FSLogix documentação](https://docs.fslogix.com/display/20170529/Requirements%2B-%2BProfile%2BContainers).

## <a name="configure-the-fslogix-profile-container"></a>Configurar o contêiner de perfil FSLogix

Para configurar as máquinas virtuais com o software FSLogix, faça o seguinte em cada computador registrado para o pool de host:

1. [Conectar-se à máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) com as credenciais que você forneceu ao criar a máquina virtual.
2. Inicie um navegador da internet e navegue até [esse link](https://go.microsoft.com/fwlink/?linkid=2084562) para baixar o agente FSLogix. Como parte da demonstração pública do Windows de área de trabalho Virtual, você obterá uma chave de licença para ativar o software FSLogix. A chave é o arquivo LicenseKey.txt incluído no arquivo. zip FSLogix agente.
3. Navegue até um \\ \\Win32\\versão ou \\ \\X64\\versão no arquivo. zip e execute **FSLogixAppsSetup** para instalar o agente FSLogix.
4. Navegue até **Program Files** > **FSLogix** > **aplicativos** para confirmar se o agente instalado.
5. No menu Iniciar, executar **RegEdit** como administrador. Navegue até **computador\\HKEY_LOCAL_MACHINE\\software\\FSLogix**.
6. Crie uma chave nomeada **perfis**.
7. Crie os seguintes valores para a chave de perfis:

| NOME                | Tipo               | Dados/valor                        |
|---------------------|--------------------|-----------------------------------|
| Enabled             | DWORD              | 1                                 |
| VHDLocations        | Valor de cadeia de caracteres múltipla | "Caminho de rede para o compartilhamento de arquivos"     |

>[!IMPORTANT]
>Para ajudar a proteger seu ambiente da Área de Trabalho Virtual do Windows no Azure, recomendamos que você não abra a porta de entrada 3389 nas VMs. A Área de Trabalho Virtual do Windows não exige uma porta de entrada 3389 aberta para que os usuários acessem as VMs do pool de hosts. Caso você precise abrir a porta 3389 para fins de solução de problemas, recomendamos o uso do [acesso just-in-time à VM](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).