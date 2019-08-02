---
title: Não é possível acessar este erro de aplicativo corporativo com o aplicativo proxy de aplicativo | Microsoft Docs "
description: Como resolver problemas comuns de acesso com aplicativos do Proxy de Aplicativo do Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e54b54f592082ad998e1f5dfbdcb5ed30e6dc4a
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381407"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Erro "Não é possível Acessar este Aplicativo Corporativo" ao usar um aplicativo de Proxy de Aplicativo

Este artigo ajuda-o a solucionar problemas comuns do erro "Este aplicativo corporativo não pode ser acessado" em um aplicativo de Proxy de Aplicativo do Azure AD.

## <a name="overview"></a>Visão geral

Quando você vir esse erro, localize o código de status na página de erro. Esse código provavelmente é um dos seguintes códigos de status:

- **Tempo Limite do Gateway**: O serviço de Proxy do Aplicativo não consegue alcançar o conector. Esse erro normalmente indica um problema com a atribuição do conector, o próprio conector ou as regras de rede do conector.
- **Gateway Incorreto**: O conector não consegue alcançar o aplicativo back-end. Esse erro indicar uma configuração incorreta do aplicativo.
- **Proibido**: O usuário não está autorizado a acessar o aplicativo. Esse erro pode acontecer quando o usuário não está atribuído ao aplicativo no Azure Active Directory ou, se no back-end o usuário não tiver permissão para acessar o aplicativo.

Para localizar o código observe no texto, na parte inferior esquerda da mensagem de erro, o campo "Código de Status". Além disso, procure quaisquer dicas adicionais na parte inferior da página.

![Exemplo: Erro de tempo limite de gateway](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Para obter detalhes sobre como solucionar a causa raiz desses erros e obter mais detalhes sobre as correções sugeridas, consulte a seção correspondente abaixo.

## <a name="gateway-timeout-errors"></a>Erros de Tempo limite de gateway

Um tempo limite de gateway ocorre quando o serviço tenta alcançar o conector e não é possível dentro da janela de tempo limite. Esse erro geralmente é causado por um aplicativo atribuído a um Grupo de Conectores sem conectores funcionando ou, algumas portas exigidas pelo Conector não estão abertas.

## <a name="bad-gateway-errors"></a>Erros de gateway incorreto

Um erro de gateway incorreto indica que o conector não consegue alcançar o aplicativo back-end. certifique-se de que você publicou o aplicativo correto. Os erros comuns que causam esse erro são:

- Um erro de digitação ou erro na URL interna
- Não publicar a raiz do aplicativo. Por exemplo, publicar <http://expenses/reimbursement> mas tentar acessar <http://expenses>
- Problemas com a configuração Delegação Restrita de Kerberos (KCD)
- Problemas com o aplicativo back-end

## <a name="forbidden-errors"></a>Erros proibidos

Se você se deparar com um erro proibido, significa que o usuário não foi atribuído ao aplicativo. Esse erro pode ser no Azure Active Directory ou no aplicativo back-end.

Para saber como atribuir usuários ao aplicativo no Azure, consulte a [documentação de configuração](application-proxy-add-on-premises-application.md#test-the-application).

Se você confirmar que o usuário está atribuído ao aplicativo no Azure, verifique a configuração do usuário no aplicativo back-end. Se você estiver usando Delegação Restrita de Kerberos/Autenticação Integrada do Windows, consulte a página Solucionar problemas de KCD para ver diretrizes.

## <a name="check-the-applications-internal-url"></a>Verificar a URL interna do aplicativo

Como uma primeira etapa rápida, verifique novamente e corrija a URL interna, abrindo o aplicativo pelo **Aplicativos Empresariais** e, em seguida, selecionando o menu **Proxy de Aplicativo**. Verifique se a URL interna é a usada de sua rede local para acessar o aplicativo.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Verificar se o aplicativo está atribuído a um Grupo de Conectores de trabalho

Para verificar se o aplicativo está atribuído a um Grupo de Conectores de trabalho:

1. Abra o aplicativo no portal, acessando o **Azure Active Directory**, clicando em **Aplicativos Empresariais** e, em seguida, em **Todos os Aplicativos.** Abra o aplicativo e, em seguida, selecione **Proxy de aplicativo** no menu esquerdo.
1. Analise o campo Grupo de Conectores. Se não houver conectores ativos no grupo, você verá um aviso. Se você não vir nenhum aviso, passe para verificar se todas as portas necessárias são permitidas.
1. Se o Grupo de Conectores incorreto for exibido, use o menu suspenso para selecionar o grupo correto e confirme se nenhum aviso é exibido. Se o Grupo de Conectores pretendido for exibido, clique na mensagem de aviso para abrir a página com o gerenciamento do Conector.
1. A partir daqui, há algumas maneiras de aprofundar-se ainda mais:

   - Mover um conector ativo para o grupo: Se você tiver um conector ativo que deve pertencer ao grupo e tem metas claras para o aplicativo de back-end de destino, você poderá mover o conector para o grupo atribuído. Para fazer isso, clique no Conector. No campo "Grupo de Conectores", use a lista suspensa para selecionar o grupo correto e clique em Salvar.
   - Baixar um novo conector para o grupo: Nesta página, você pode obter o link para [baixar um novo Conector](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Instale o Conector em um computador com a linha direta de visão para o aplicativo de back-end. Normalmente, o conector é instalado no mesmo servidor que o aplicativo. Use o link de download do Connector para baixar de um conector no computador de destino. Em seguida, clique no Conector e use o menu suspenso "Grupo de Conectores" para certificar-se de que ele pertence ao grupo correto.
   - Investigar um conector inativo: Se um conector for exibido como inativo, ele não conseguirá alcançar o serviço. Esse erro geralmente é devido a algumas portas exigidas sendo bloqueadas. Para resolver esse problema, vá para verificar se todas as portas necessárias são permitidas.

Após usar essas etapas para garantir que o aplicativo está atribuído a um grupo com Conectores funcionando, teste o aplicativo novamente. Se ainda não estiver funcionando, continue na próxima seção.

## <a name="check-all-required-ports-are-open"></a>Verifique se todas as portas necessárias estão abertas

Para verificar se todas as exigidas portas estão abertas, consulte a documentação sobre a abertura de portas. Se todas as portas exigidas estiver abertas, siga para a próxima seção.

## <a name="check-for-other-connector-errors"></a>Verifique se há outros Erros de Conectores

Se nenhuma das opções acima resolver o problema, a próxima etapa será procurar problemas ou erros do próprio conector. É possível ver alguns erros comuns no [Documento de solução de problemas](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors).

Você também pode examinar diretamente os logs do Conector para identificar quaisquer erros. Muitas das mensagens de erro compartilham recomendações específicas para correções. Para exibir os logs, consulte a [documentação de conectores](application-proxy-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>Resoluções Adicionais

Se os itens acima não corrigirem o problema, há algumas causas possíveis diferentes. Para identificar o problema:

Se seu aplicativo é configurado para usar a Autenticação Integrada do Windows (IWA), teste o aplicativo sem logon único. Se não, avance para o próximo parágrafo. Para verificar o aplicativo sem logon único, abra o aplicativo pelo **Aplicativos Empresariais,** e vá para o menu **Logon Único**. Altere a lista suspensa de "Autenticação Integrada do Windows" para "Logon único do Azure AD desabilitado".

Agora, abra um navegador e tente acessar o aplicativo novamente. Você deve ser solicitado a fazer autenticação e entrar no aplicativo. Se você conseguir autenticar, isso significa que o problema está na configuração de Delegação Restrita de Kerberos (KCD) que habilita o logon único. Para obter mais informações, consulte a página Solução de Problemas de KCD.

Se você continuar vendo o erro, vá para o computador no qual o Conector está instalado, abra um navegador e tente alcançar a URL interna usada para o aplicativo. O Conector atua como outro cliente do mesmo computador. Se você não conseguir alcançar o aplicativo, investigue por que esse computador não consegue alcançar o aplicativo ou use um conector em um servidor que possa acessar o aplicativo.

Se você pode alcançar o aplicativo do computador, pesquise problemas ou erros com o próprio Conector. É possível ver alguns erros comuns no [Documento de solução de problemas](application-proxy-troubleshoot.md#connector-errors). Você também pode examinar diretamente os logs do Conector para identificar quaisquer erros. Muitas de nossas mensagens de erro podem compartilhar recomendações mais específicas para correções. Para saber como exibir os logs, consulte [nossa documentação de conectores](application-proxy-connectors.md#under-the-hood).

## <a name="next-steps"></a>Próximas etapas

[Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-connectors.md)
