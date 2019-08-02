---
title: Conectar-se para visualização da área de trabalho Virtual do Windows do Windows 10 ou Windows 7 – Azure
description: Como se conectar à visualização de área de trabalho Virtual do Windows do Windows 10 ou Windows 7.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: helohr
ms.openlocfilehash: 66b423ea27cc0f9112349964fcfd0d75f08c12bd
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620110"
---
# <a name="connect-from-windows-10-or-windows-7"></a>Conectar-se do Windows 10 ou Windows 7

> Aplica-se a: O Windows 7 e Windows 10.

Um cliente que pode ser baixado está disponível que fornece acesso aos recursos de visualização de área de trabalho Virtual do Windows dos dispositivos que executam o Windows 7 e Windows 10.

> [!IMPORTANT]
> Não use **RemoteApp e conexões de área de trabalho (RADC)** ou **Conexão de área de trabalho remota (MSTSC)** para acessar os recursos de área de trabalho Virtual do Windows porque a área de trabalho Virtual do Windows não dá suporte a qualquer cliente.

## <a name="install-the-client"></a>Instalar o cliente

[Baixe](https://go.microsoft.com/fwlink/?linkid=2068602) e instalar o cliente a seu computador local. A instalação requer direitos de administrador.

## <a name="subscribe-to-a-feed"></a>Inscrever-se em um feed

Obter a lista de recursos gerenciados disponíveis para você ao assinar o feed fornecido pelo seu administrador. Inscrever-se torna os recursos disponíveis em seu computador local.

Para se inscrever em um feed:

1. Iniciar o cliente da lista de todos os aplicativos, procure pela **área de trabalho remota**.
1. Selecione **Subscribe** na página principal para se conectar ao serviço e recuperar seus recursos.
1. **Entrar** com sua conta de usuário quando solicitado.

Depois de autenticar com êxito, agora você deve ver uma lista de recursos disponíveis para você.

Você pode iniciar recursos por um dos dois métodos.

- Na página principal do cliente, clique duas vezes em um recurso para iniciá-lo.
- Inicie um recurso, como normalmente faria com outros aplicativos no Menu Iniciar.
  - Você também pode procurar os aplicativos na barra de pesquisa.

Depois de inscrito em um feed, o conteúdo do feed é atualizado automaticamente regularmente. Recursos podem ser adicionados, alterados ou removidos com base nas alterações feitas pelo seu administrador.

## <a name="view-the-details-of-a-feed"></a>Exibir os detalhes de um feed

Após a assinatura, você pode exibir informações adicionais sobre o feed, acessando o painel de detalhes.

1. Na página principal do cliente, selecione as reticências ( **...** ) à direita do nome do feed.
1. No menu suspenso, selecione **detalhes**.
1. O painel de detalhes aparece no lado direito do cliente.

O painel de detalhes contém informações úteis sobre o feed:

- A URL e o nome de usuário usado para assinar
- O número de aplicativos e áreas de trabalho
- A data/hora da última atualização
- O status da última atualização

Se necessário, você pode iniciar uma atualização manual, selecionando na **atualizar agora**.

## <a name="unsubscribe-from-a-feed"></a>Cancelar a assinatura de um feed

Esta seção ensinará como cancelar a assinatura de um feed. Você pode cancelar a inscrição para assinar novamente com uma conta diferente ou remova os recursos do sistema.

1. Na página principal do cliente, selecione as reticências ( **...** ) à direita do nome do feed.
1. No menu suspenso, selecione **Unsubscribe**.
1. Examine e marque **continuar** na caixa de diálogo.

## <a name="update-the-client"></a>Atualizar o cliente

Quando uma nova versão do cliente estiver disponível, você será notificado pelo cliente e a Central de ações do Windows. Selecione a notificação para iniciar o processo de atualização.
