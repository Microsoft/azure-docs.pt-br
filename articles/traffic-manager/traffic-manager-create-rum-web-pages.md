---
title: "Medidas Reais de Usuário para o Gerenciador de Tráfego do Azure com páginas da Web | Microsoft Docs"
description: "Configurar suas páginas da Web para enviar as Medidas Reais de Usuário para o Gerenciador de Tráfego"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: ce5d333ad5d339b64d1fc1d081651052f71a8b67
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Como enviar as Medidas Reais de Usuário para o Gerenciador de Tráfego do Azure usando páginas da Web

>[!NOTE]
>O recurso de Medidas Reais de Usuário no Gerenciador de Tráfego está na visualização pública e pode não ter o mesmo nível de disponibilidade e confiabilidade que os recursos que já estão disponíveis. Não há suporte para o recurso, o recurso pode ter funcionalidades restritas e ele pode não estar disponível em todas as localizações do Azure. Para receber as notificações mais recentes sobre a disponibilidade e o status desse recurso, confira a página [Atualizações do Gerenciador de Tráfego do Azure](https://azure.microsoft.com/updates/?product=traffic-manager).

Você pode configurar suas páginas da Web para enviar as Medidas Reais de Usuário para o Gerenciador de Tráfego obtendo uma chave de RUM (Medidas Reais de Usuário) e inserindo o código gerado na página da Web.

## <a name="obtain-a-real-user-measurements-key"></a>Obter uma chave de Medidas Reais de Usuário

As medidas obtidas e enviadas para o Gerenciador de Tráfego do aplicativo cliente são identificadas pelo serviço usando uma cadeia de caracteres exclusiva, chamada de **Chave de RUM (Medidas Reais de Usuário)**. Você pode obter uma chave de RUM usando o portal do Azure, uma API REST ou a CLI do Azure ou do PowerShell.

Para obter a chave de RUM usando o portal do Azure:
1. Em um navegador, entre no portal do Azure. Se você ainda não tiver uma conta, inscreva-se para uma avaliação gratuita de um mês.
2. Na barra de pesquisa do portal, pesquise o nome do perfil do Gerenciador de Tráfego que deseja modificar e, em seguida, clique no perfil do Gerenciador de Tráfego nos resultados exibidos.
3. Na folha de perfil do Gerenciador de Tráfego, clique em **Medidas Reais de Usuário** em **Configurações**.
4. Clique em **Gerar Chave** para criar uma nova chave de RUM.
 
  ![Gerar chave de Medidas Reais de Usuário](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Figura 1: geração de chave de Medidas Reais de Usuário**

5. A folha agora exibe a chave de RUM gerada e um trecho de código JavaScript que precisa ser inserido na página HTML.
 
    ![Código Javascript da chave de Medidas Reais de Usuário](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Figura 2: chave de Medidas Reais de Usuário e JavaScript de medidas**
 
6.  Clique no botão **Copiar** para copiar o código JavaScript. 

>[!IMPORTANT]
> Use o JavaScript gerado para que o recurso de Medidas Reais de Usuário funcione corretamente. As alterações nesse script ou nos scripts usados pelo recurso de Medidas Reais de Usuário podem resultar em um comportamento imprevisível.

## <a name="embed-the-code-to-an-html-web-page"></a>Inserir o código em uma página da Web HTML

Depois de obter a chave de RUM, a próxima etapa será inserir esse JavaScript copiado em uma página HTML que seus usuários finais visitam. A edição do HTML pode ser feita de várias maneiras e usando diferentes ferramentas e fluxos de trabalho. Este exemplo mostra como atualizar uma página HTML para adicionar esse script. Você pode usar essas diretrizes para adaptá-lo ao seu fluxo de trabalho de gerenciamento de fonte de dados HTML.

1.  Abrir a página HTML em um editor de texto
2.  Cole o código JavaScript que foi copiado na etapa anterior na seção BODY do HTML (o código copiado está na linha 8 e 9, veja a Figura 3).
 
    ![Inserir o código Javascript na página da Web para as Medidas Reais de Usuário](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)  

    **Figura 3: HTML simples com JavaScript das Medidas Reais de Usuário inserido**

3.  Salvar o arquivo HTML
4. Na próxima vez em que essa página for renderizada em um navegador da Web, o JavaScript referenciado será baixado e o script executará as operações de medição e relatórios.


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre as [Medidas Reais de Usuário](traffic-manager-rum-overview.md)
- Saiba [como funciona o Gerenciador de Tráfego](traffic-manager-overview.md)
- Saiba mais sobre os [métodos de roteamento do tráfego](traffic-manager-routing-methods.md) com suporte pelo Gerenciador de Tráfego
- Aprenda a [criar um perfil do Gerenciador de Tráfego](traffic-manager-create-profile.md)

