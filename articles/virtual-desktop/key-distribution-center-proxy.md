---
title: Configurar o Kerberos centro de distribuição de chaves proxy da área de trabalho virtual do Windows-Azure
description: Como configurar um pool de hosts de área de trabalho virtual do Windows para usar um proxy de centro de distribuição de chaves Kerberos.
author: Heidilohr
ms.topic: how-to
ms.date: 03/20/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 876564934b1ccbffa19c318a2d2c8393e5dca54e
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023972"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Configurar um proxy de centro de distribuição de chaves Kerberos (versão prévia)

> [!IMPORTANT]
> Esse recurso está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendamos usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Clientes preocupados com a segurança, como organizações financeiras ou governamentais, geralmente se conectam usando cartões inteligentes. Os cartões inteligentes tornam as implantações mais seguras exigindo a MFA (autenticação multifator). No entanto, para a parte de RDP de uma sessão de área de trabalho virtual do Windows, os cartões inteligentes exigem uma conexão direta ou uma "linha de visão" com um controlador de domínio Active Directory (AD) para autenticação Kerberos. Sem essa conexão direta, os usuários não podem entrar automaticamente na rede da organização de conexões remotas. Os usuários em uma implantação de área de trabalho virtual do Windows podem usar o serviço de proxy KDC para fazer proxy desse tráfego de autenticação e entrar remotamente. O proxy KDC permite a autenticação para o protocolo RDP de uma sessão de área de trabalho virtual do Windows, permitindo que o usuário entre com segurança. Isso torna o trabalho de casa muito mais fácil e permite que certos cenários de recuperação de desastres sejam executados com mais tranqüilidade.

No entanto, a configuração do proxy KDC geralmente envolve a atribuição da função de gateway do Windows Server no Windows Server 2016 ou posterior. Como usar uma função de Serviços de Área de Trabalho Remota para entrar na área de trabalho virtual do Windows? Para responder isso, vamos dar uma olhada rápida nos componentes.

Há dois componentes para o serviço de área de trabalho virtual do Windows que precisam ser autenticados:

- O feed no cliente de área de trabalho virtual do Windows que fornece aos usuários uma lista de áreas de trabalho ou aplicativos disponíveis aos quais eles têm acesso. Esse processo de autenticação ocorre em Azure Active Directory, o que significa que esse componente não é o foco deste artigo.
- A sessão RDP que resulta de um usuário selecionando um desses recursos disponíveis. Esse componente usa a autenticação Kerberos e requer um proxy KDC para usuários remotos.

Este artigo mostrará como configurar o feed no cliente de área de trabalho virtual do Windows no portal do Azure. Se você quiser saber como configurar a função de gateway de área de trabalho remota, consulte [implantar a função de gateway de área de trabalho remota](/windows-server/remote/rd-gateway-role).

## <a name="requirements"></a>Requisitos

Para configurar um host de sessão de área de trabalho virtual do Windows com um proxy KDC, você precisará dos seguintes itens:

- Acesso ao portal do Azure e a uma conta de administrador do Azure.
- Os computadores cliente remotos devem estar executando o Windows 10 ou o Windows 7 e ter o [cliente de área de trabalho do Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop) instalado.
- Você deve ter um proxy KDC já instalado em seu computador. Para saber como fazer isso, consulte [Configurar a função de gateway de RD para área de trabalho virtual do Windows](rd-gateway-role.md).
- O sistema operacional do computador deve ser o Windows Server 2016 ou posterior.

Depois de verificar se você atende a esses requisitos, você está pronto para começar.

## <a name="how-to-configure-the-kdc-proxy"></a>Como configurar o proxy KDC

Para configurar o proxy KDC:

1. Entre no portal do Azure como administrador.

2. Vá para a página da área de trabalho virtual do Windows.

3. Selecione o pool de hosts para o qual você deseja habilitar o proxy KDC e, em seguida, selecione **Propriedades de RDP**.

    > [!div class="mx-imgBorder"]
    > ![Uma captura de tela da página portal do Azure mostrando um usuário selecionando pools de hosts, depois o nome do pool de hosts de exemplo e as propriedades de RDP.](media/rdp-properties.png)

4. Selecione a guia **avançado** e insira um valor no seguinte formato sem espaços:

    
    > kdcproxyname: s:\<fqdn\>
    

    > [!div class="mx-imgBorder"]
    > ![Uma captura de tela mostrando a guia Avançado selecionada, com o valor inserido conforme descrito na etapa 4.](media/advanced-tab-selected.png)

5. Selecione **Salvar**.

6. O pool de hosts selecionado agora deve começar a emitir arquivos de conexão RDP que incluem o valor de kdcproxyname que você inseriu na etapa 4.

## <a name="next-steps"></a>Próximas etapas

Para saber como gerenciar o lado de Serviços de Área de Trabalho Remota do proxy KDC e atribuir a função de gateway de área de trabalho remota, consulte [implantar a função de gateway de área de trabalho remota](rd-gateway-role.md).

Se você estiver interessado em Dimensionar seus servidores proxy KDC, saiba como configurar a alta disponibilidade para o proxy KDC em [Adicionar alta disponibilidade para a Web da área de trabalho remota e Web de gateway](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha).
