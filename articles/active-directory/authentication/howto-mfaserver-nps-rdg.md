---
title: RDG e servidor MFA do Azure usando RADIUS-Azure Active Directory
description: Esta é a página da Autenticação Multifator do Azure que ajudará na implantação do Gateway de Área de Trabalho Remota e do Servidor de Autenticação Multifator do Azure usando RADIUS.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff89e8c803e0edf5245a62d625a6367d68de96ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742061"
---
# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>Gateway de Área de Trabalho Remota e Servidor de Autenticação Multifator do Azure usando RADIUS

Geralmente, o gateway de Área de Trabalho Remota (RD) usa os [serviços de diretiva de rede (NPS)](/windows-server/networking/core-network-guide/core-network-guide#BKMK_optionalfeatures) locais para autenticar usuários. Este artigo descreve como rotear a solicitação RADIUS do Gateway de Área de Trabalho Remota (por meio do NPS local) até o Servidor de Autenticação Multifator. A combinação do Azure MFA e do Gateway de Área de Trabalho Remota significa que os usuários podem acessar seus ambientes de trabalho de qualquer lugar mantendo uma autenticação forte.

Como não há suporte para a Autenticação do Windows para serviços de terminal no Server 2012 R2, use RADIUS e Gateway de RD para integrar com o Servidor de MFA.

Instale o Servidor de Autenticação Multifator em um servidor separado, que envia a solicitação RADIUS de volta ao NPS no Servidor de Gateway de Área de Trabalho Remota. Após o NPS validar o nome de usuário e a senha, ele retorna uma resposta para o Servidor de Autenticação Multifator. Em seguida, o servidor MFA realiza o segundo fator de autenticação e retorna um resultado para o gateway.

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferece mais o servidor MFA para novas implantações. Novos clientes que desejam exigir a MFA (autenticação multifator) durante eventos de entrada devem usar a autenticação multifator do Azure AD baseada em nuvem.
>
> Para começar a usar a MFA baseada em nuvem, consulte [tutorial: proteger eventos de entrada de usuário com a autenticação multifator do Azure ad](tutorial-enable-azure-mfa.md).
>
> Se você usar MFA baseada em nuvem, consulte como [integrar com autenticação RADIUS para autenticação multifator do Azure](howto-mfa-nps-extension.md).
>
> Os clientes existentes que ativaram o servidor MFA antes de 1º de julho de 2019 podem baixar a versão mais recente, atualizações futuras e gerar credenciais de ativação como de costume.

## <a name="prerequisites"></a>Pré-requisitos

- Um servidor de MFA do Azure ingressado em um domínio. Se você ainda não tiver um instalado, execute as etapas em [Introdução ao Servidor de Autenticação Multifator do Azure](howto-mfaserver-deploy.md).
- Um servidor NPS existente configurado.
- Um Gateway de Área de Trabalho Remota que autentica com os Serviços de Política de Rede.

> [!NOTE]
> Esse artigo deve ser usado com implantações apenas do MFA Server, não Azure MFA (baseado em nuvem).

## <a name="configure-the-remote-desktop-gateway"></a>Configurar o Gateway de Área de Trabalho Remota

Configure o Gateway de RD para enviar a autenticação RADIUS a um Servidor de Autenticação Multifator do Azure.

1. No Gerenciador de Gateway de RD, clique no nome do servidor e selecione **Propriedades**.
2. Acesse a guia **Repositório RD CAP** e selecione **Servidor central executando NPS**.
3. Adicione um ou mais Servidores Azure de Autenticação Multifator como servidores RADIUS e especifique o nome ou endereço IP de cada servidor.
4. Crie um segredo compartilhado para cada servidor.

## <a name="configure-nps"></a>Configurar o NPS

O Gateway de Área de Trabalho Remota usa o NPS para enviar a solicitação RADIUS ao Autenticação Multifator do Azure. Para configurar o NPS, primeiro altere as configurações de tempo limite a fim de evitar que o Gateway de RD atinja o tempo limite antes da conclusão da verificação em duas etapas. Depois, atualize o NPS para receber autenticações RADIUS do Servidor de MFA. Use o procedimento a seguir para configurar o NPS:

### <a name="modify-the-timeout-policy"></a>Modificar a política de tempo limite

1. No NPS, abra o menu **Clientes e Servidor RADIUS** na coluna esquerda e selecione **Grupos de Servidores Remotos RADIUS**.
2. Selecione o **GRUPO DE SERVIDORES DE GATEWAY TS**.
3. Acesse a guia **Balanceamento de Carga**.
4. Altere o **Tempo de espera sem resposta antes que uma solicitação seja ignorada (em segundos)** e o **Tempo de espera entre solicitações para identificar o servidor como não disponível (em segundos)** para um valor entre 30 e 60 segundos. (Se você perceber que o tempo limite do servidor ainda expira durante a autenticação, volte aqui e aumente o número de segundos.)
5. Acesse a guia **Autenticação/Conta** e verifique se as portas RADIUS especificadas correspondem às portas nas quais o Servidor de Autenticação Multifator está escutando.

### <a name="prepare-nps-to-receive-authentications-from-the-mfa-server"></a>Preparar o NPS para receber autenticações do Servidor de MFA

1. Clique com botão direito em **Clientes RADIUS** abaixo de Clientes e Servidores RADIUS na coluna esquerda e selecione **Novo**.
2. Adicione o Servidor de Autenticação Multifator do Azure como um cliente RADIUS. Escolha um nome amigável e especifique um segredo compartilhado.
3. Abra o menu **Políticas** na coluna esquerda e selecione **Políticas de Solicitação de Conexão**. Você deve ver uma política chamada POLÍTICA DE AUTORIZAÇÃO DO GATEWAY de TS que foi criada quando o Gateway de RD foi configurado. Essa política encaminha as solicitações RADIUS para o Servidor de Autenticação Multifator.
4. Clique com botão direito em **POLÍTICA DE AUTORIZAÇÃO DE GATEWAY TS** e selecione **Duplicar Política**.
5. Abra a nova política e acesse a guia **Condições**.
6. Adicione uma condição que corresponda o Nome Amigável do Cliente ao Nome amigável definido na etapa 2 acima para o cliente RADIUS do Servidor de Autenticação Multifator do Azure.
7. Acesse a guia **Configurações** e selecione **Autenticação**.
8. Altere o provedor de autenticação para **Autenticar solicitações neste servidor**. Essa política garante que quando o NPS recebe uma solicitação RADIUS do Servidor de Autenticação Multifator do Azure, a autenticação ocorre localmente em vez de enviar uma solicitação RADIUS de volta ao Servidor de Autenticação Multifator do Azure, que resulta em uma condição de loop.
9. Para evitar uma condição de loop, certifique-se de que a nova política fique ACIMA da política original no painel **Políticas de Solicitação de Conexão**.

## <a name="configure-azure-multi-factor-authentication"></a>Configurar a Autenticação Multifator do Azure

O Servidor de Autenticação Multifator do Azure é configurado como um proxy RADIUS entre o Gateway de Área de Trabalho Remota e o NPS.  Ele deve ser instalado em um servidor de domínio que esteja separado do servidor do Gateway de Área de Trabalho Remota. Use o procedimento a seguir para configurar o Servidor de Autenticação Multifator do Azure.

1. Abra o Servidor de Autenticação Multifator do Azure e selecione o ícone Autenticação RADIUS.
2. marque a caixa de seleção **Habilitar autenticação RADIUS**.
3. Na guia Clientes, verifique se as portas correspondem ao que foi configurado no NPS e selecione **Adicionar**.
4. Adicione o endereço IP, o nome do aplicativo (opcional) e um segredo compartilhado do servidor do Gateway de Área de Trabalho Remota. O segredo compartilhado deve ser o mesmo no Servidor de Autenticação Multifator do Azure e Gateway de RD.
3. Acesse a guia **Destino** e escolha o botão de opção **Servidores RADIUS**.
4. Selecione **Adicionar** e insira o endereço IP, o segredo compartilhado e as portas do servidor NPS. A não ser que esteja usando um NPS central, o cliente RADIUS e o destino de RADIUS serão iguais. O segredo compartilhado deve corresponder ao configurado na seção do cliente RADIUS no servidor NPS.

![Autenticação RADIUS no servidor MFA](./media/howto-mfaserver-nps-rdg/radius.png)

## <a name="next-steps"></a>Próximas etapas

- Integrar o Azure MFA e [aplicativos web do IIS](howto-mfaserver-iis.md)

- Obtenha respostas no [FAQ de autenticação multifator do Azure](multi-factor-authentication-faq.md)