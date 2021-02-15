---
title: Conectar-se à área de trabalho virtual do Windows (clássico) Windows 10 ou 7-Azure
description: Como se conectar à área de trabalho virtual do Windows (clássico) usando o cliente de área de trabalho do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 07/16/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 420c507361e3e2437366e6ccf2d46a8b78684e59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008349"
---
# <a name="connect-with-the-windows-desktop-classic-client"></a>Conectar-se ao cliente do Windows Desktop (clássico)

> Aplica-se a: Windows 7, Windows 10 e Windows 10 IoT Enterprise

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager, confira [este artigo](../connect-windows-7-10.md).

Você pode acessar os recursos da área de trabalho virtual do Windows em dispositivos com Windows 7, Windows 10 e Windows 10 IoT Enterprise usando o cliente de área de trabalho do Windows. O cliente não dá suporte ao Windows 8 ou Windows 8.1.

>[!NOTE]
>O cliente Windows usa automaticamente como padrão a área de trabalho virtual do Windows (clássico). No entanto, se o cliente detectar que o usuário também tem recursos de Azure Resource Manager, ele adicionará automaticamente os recursos ou notificará o usuário de que eles estão disponíveis.

> [!IMPORTANT]
> A Área de Trabalho Virtual do Windows não dá suporte ao cliente de RADC (Conexões de RemoteApp e Área de Trabalho) nem ao cliente de Conexão de Área de Trabalho Remota (MSTSC).

> [!IMPORTANT]
> No momento, a Área de Trabalho Virtual do Windows não dá suporte ao cliente da Área de Trabalho Remota da Microsoft Store.

## <a name="install-the-windows-desktop-client"></a>Instalar o cliente de área de trabalho do Windows

Escolha o cliente que corresponda à sua versão do Windows:

- [Windows 64 bits](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 bits](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

É possível instalar o cliente do usuário atual, que não requer direitos de administrador, ou seu administrador pode instalar e configurar o cliente para que todos os usuários no dispositivo possam acessá-lo.

Depois de instalado, o cliente pode ser iniciado no menu Iniciar pesquisando **Área de Trabalho Remota**.

## <a name="subscribe-to-a-workspace"></a>Assinar um Workspace

Há duas maneiras de assinar um workspace. O cliente pode tentar descobrir os recursos disponíveis para você por meio da sua conta corporativa ou de estudante, ou, caso ele não consiga encontrá-los, você pode especificar diretamente a URL onde os recursos estão. Depois da assinatura de um workspace, é possível iniciar recursos com um dos seguintes métodos:

- Acesse a Central de Conexão e clique duas vezes em um recurso para iniciá-lo.
- Também é possível acessar o menu Iniciar e procurar uma pasta com o nome do Workspace ou inserir o nome do recurso na barra de pesquisa.

### <a name="subscribe-with-a-user-account"></a>Assinar com uma conta de usuário

1. Na página principal do cliente, selecione **assinar**.
2. Entre com sua conta quando solicitado.
3. Os recursos aparecerão no centro de conexões e serão agrupados por espaço de trabalho.

### <a name="subscribe-with-a-url"></a>Assinar com uma URL

1. Na página principal do cliente, selecione **assinar com a URL**.
2. Insira a URL do workspace ou o endereço de email:
   - Se você usar a **URL do workspace**, use aquela que o administrador lhe forneceu. Se estiver acessando recursos da Área de Trabalho Virtual do Windows, será possível usar uma das seguintes URLs:
     - Área de Trabalho Virtual do Windows (clássica): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`
     - Área de Trabalho Virtual do Windows: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`
   - Se você estiver usando o campo **email** , insira seu endereço de email. Isso instrui o cliente a procurar uma URL associada ao seu endereço de email se seu administrador tiver configurado a [descoberta de email](/windows-server/remote/remote-desktop-services/rds-email-discovery).
3. Selecione **Avançar**.
4. Entre com sua conta quando solicitado.
5. Os recursos devem aparecer no centro de conexões, agrupados por espaço de trabalho.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o cliente de área de trabalho do Windows, confira [introdução ao cliente de desktop do Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).
