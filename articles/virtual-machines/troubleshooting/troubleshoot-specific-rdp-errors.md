---
title: Mensagens de erro específicas do RDP para VMs do Azure | Microsoft Docs
description: Entender as mensagens de erro específicas que você pode receber ao tentar usar a conexão de Área de Trabalho Remota para uma máquina virtual Windows no Azure
keywords: Erro de área de trabalho remota, erro de conexão de área de trabalho remota, não é possível conectar-se à VM, solução de problemas da área de trabalho remota
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ea8a2fa3a37815f3a7a48078e408e6607dc37eb4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709278"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Solucionar problemas de mensagens de erro específicas ao RDP para uma VM do Windows no Azure
Você pode receber mensagens de erro específicas ao usar a conexão de Área de Trabalho Remota para uma VM (máquina virtual) do Windows no Azure. Este artigo detalha algumas das mensagens de erro mais comuns encontradas, junto com as etapas para resolvê-las. Se você estiver enfrentando problemas para se conectar à sua VM usando o RDP, mas não encontrar uma mensagem de erro específica, consulte o [guia de solução de Área de Trabalho Remota](troubleshoot-rdp-connection.md).

Para obter informações sobre mensagens de erro específicas, consulte o seguinte:

* [A sessão remota foi desconectada porque não há Servidores de Licença da Área de Trabalho Remota disponíveis para fornecer uma licença](#rdplicense).
* [A Área de Trabalho Remota não consegue localizar o "nome" do computador](#rdpname).
* [Ocorreu um erro de autenticação. A Autoridade de Segurança Local não pode ser contatada](#rdpauth).
* [Erro de segurança do Windows: suas credenciais não funcionaram](#wincred).
* [Este computador não pode se conectar ao computador remoto](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>A sessão remota foi desconectada porque não há Servidores de Licença da Área de Trabalho Remota disponíveis para fornecer uma licença.
Causa: O período de cortesia de licenciamento de 120 dias para a função de servidor de área de trabalho remota expirou e você precisa instalar licenças.

Como alternativa, salve uma cópia local do arquivo RDP do portal e execute este comando no prompt de comando do PowerShell para conectar-se. Essa etapa desabilita o licenciamento apenas para esta conexão:

        mstsc <File name>.RDP /admin

Se não precisar realmente de mais de duas conexões simultâneas de Área de Trabalho Remota à VM, você poderá usar o Gerenciador do Servidor para remover a função de Servidor de Área de Trabalho Remota.

Para saber mais, confira a postagem no blog [Azure VM fails with "No Remote Desktop License Servers available" (A VM do Azure falha com a mensagem "Nenhum Servidor de Licença da Área de Trabalho Remota disponível")](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>A Área de Trabalho Remota não consegue localizar o “nome” do computador.
Causa: O cliente de área de trabalho remota em seu computador não é possível resolver o nome do computador nas configurações do arquivo RDP.

Soluções possíveis:

* Se você estiver na intranet de uma organização, certifique-se que o computador tem acesso ao servidor proxy e que pode enviar o tráfego HTTPS para ele.
* Se você estiver usando um arquivo RDP armazenado localmente, tente usar aquele gerado pelo portal. Esta etapa garante que você tenha o nome DNS correto para a máquina virtual ou para o serviço de nuvem e a porta do ponto de extremidade da VM. Aqui está um arquivo RDP de exemplo gerado pelo portal:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

A parte do endereço desse arquivo RDP tem:

* O nome de domínio totalmente qualificado do serviço de nuvem que contém a VM ("tailspin-azdatatier.cloudapp.net" neste exemplo).
* A porta TCP externa do ponto de extremidade do tráfego de Área de Trabalho Remota (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Ocorreu um erro de autenticação. A Autoridade de Segurança Local não pode ser contatada.
Causa: A VM de destino não é possível localizar a autoridade de segurança na parte do nome de usuário de suas credenciais.

Quando seu nome de usuário está no formato *SecurityAuthority*\\*UserName* (exemplo: CORP\User1), o *SecurityAuthority* parte é o nome do computador da VM (para a autoridade de segurança local) ou um nome de domínio do Active Directory.

Soluções possíveis:

* Se a conta for local para a VM, verifique se o nome da VM está escrito corretamente.
* Se a conta estiver em um domínio do Active Directory, verifique a ortografia do nome do domínio.
* Se ela for uma conta de domínio do Active Directory e o nome de domínio estiver digitado corretamente, verifique se há um controlador de domínio disponível nesse domínio. É um problema comum em redes virtuais do Azure que contêm controladores de domínio que um controlador de domínio não esteja disponível porque ele ainda não foi iniciado. Como alternativa, você pode usar uma conta de administrador local em vez de uma conta de domínio.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Erro de segurança do Windows: Suas credenciais não funcionaram.
Causa: A VM de destino não pode validar seu nome de conta e senha.

Um computador baseado em Windows pode validar as credenciais de uma conta local ou de uma conta de domínio.

* Para contas locais, use o *NomeDoComputador*\\*UserName* sintaxe (exemplo: SQL1\Admin4798).
* Para contas de domínio, use o *nome_do_domínio*\\*UserName* sintaxe (exemplo: Contoso\fabiopena).

Se você promoveu sua VM a um controlador de domínio em uma nova floresta do Active Directory, a conta de administrador local à qual você está conectado também será convertida em uma conta equivalente com a mesma senha na nova floresta e domínio. A conta local é então excluída.

Por exemplo, se você estiver conectado à conta local DC1\DCAdmin e tiver promovido a máquina virtual como um controlador de domínio em uma nova floresta para o domínio corp.contoso.com, a conta local DC1\DCAdmin será excluída, e uma nova conta de domínio (CORP\DCAdmin) será criada com a mesma senha.

Verifique o nome da conta para garantir que ele é um nome que a máquina virtual pode verificar como uma conta válida e que a senha está correta.

Se você precisar alterar a senha da conta de administrador local, consulte [Como redefinir uma senha ou o serviço de Área de Trabalho Remota para máquinas virtuais do Windows](reset-rdp.md).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Este computador não pode se conectar ao computador remoto.
Causa: A conta que é usada para se conectar não tem direitos de entrada de área de trabalho remota.

Todo computador Windows tem um grupo local Usuários da Área de Trabalho Remota que contém as contas e os grupos que podem se conectar remotamente. Os membros do grupo local de administradores também têm acesso, mesmo que essas contas não sejam listadas no grupo local de usuários da Área de Trabalho Remota. Para computadores que ingressaram no domínio, o grupo local de administradores também contém os administradores de domínio para o domínio.

Certifique-se que a conta que você está usando para conectar-se tem direitos de entrada da Área de Trabalho Remota. Como alternativa, use uma conta de administrador local ou domínio para se conectar na Área de Trabalho Remota. Para adicionar a conta desejada ao grupo local de usuários da Área de Trabalho Remota, use o snap-in do Console de Gerenciamento Microsoft (**Ferramentas do sistema > Usuários e Grupos Locais > Grupos > Usuários da Área de Trabalho Remota**).

## <a name="next-steps"></a>Próximas etapas
Se nenhum desses erros ocorrerem e você tiver um problema desconhecido com a conexão usando o RDP, consulte o [guia de solução da Área de Trabalho Remota](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Para obter etapas de solução de problema de acesso a aplicativos em execução, consulte [Solucionar problemas de acesso a um aplicativo em execução em uma VM do Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Se você estiver enfrentando problemas ao usar o Secure Shell (SSH) para se conectar a uma VM do Linux no Azure, consulte [Solucionar problemas de conexões SSH a uma VM do Linux no Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

