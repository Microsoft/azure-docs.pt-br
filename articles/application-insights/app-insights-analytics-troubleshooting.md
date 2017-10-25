---
title: Solucionar problemas do Analytics no Azure Application Insights | Microsoft Docs
description: 'Problemas com a Application Insights Analytics? Comece por aqui. '
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 9bbd5859-3584-4d80-9b6d-d5910fa48baa
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/11/2016
ms.author: bwren
ms.openlocfilehash: 02df117908fc1790e8cfb9ec0a7218c1b8be856c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-analytics-in-application-insights"></a>Solução de problemas do Analytics no Application Insights
Problemas com a [Application Insights Analytics](app-insights-analytics.md)? Comece por aqui. A análise é a poderosa ferramenta de pesquisa do Azure Application Insights.

## <a name="limits"></a>Limites
* No momento, os resultados da consulta são limitados apenas a uma semana de dados anteriores.
* Navegadores em que testamos: edições mais recentes do Chrome, Edge e Internet Explorer.

## <a name="known-incompatible-browser-extensions"></a>Extensões do navegador incompatíveis conhecidas
* Ghostery

Desabilite a extensão ou use um navegador diferente.

## <a name="e-a"></a> "Erro inesperado"
![Tela de erro inesperado](./media/app-insights-analytics-troubleshooting/010.png)

Ocorreu um erro interno durante o tempo de execução do portal. Exceção sem tratamento.

* Limpe o cache do navegador. 

## <a name="e-b"></a>403... tentar recarregar
![403... tente recarregar](./media/app-insights-analytics-troubleshooting/020.png)

Ocorreu um erro de autenticação (durante a autenticação ou durante a geração de token de acesso). O portal pode não ter como se recuperar sem alterar as configurações do navegador.

* Verifique [se os cookies de terceiros estão habilitados](#cookies) no navegador. 

## <a name="authentication"></a>403... verificar zona de segurança
![403... verifique a zona de segurança](./media/app-insights-analytics-troubleshooting/030.png)

Ocorreu um erro de autenticação (durante a autenticação ou durante a geração de token de acesso). O portal pode não ter como se recuperar sem alterar as configurações do navegador.

1. Verifique [se os cookies de terceiros estão habilitados](#cookies) no navegador. 
2. Você usou um favorito, indicador ou link salvo para abrir o portal do Analytics? Você entrou com credenciais diferentes daquelas usadas ao salvar o link?
3. Tente usar uma janela do navegador privada/anônima (depois de fechar todas as janelas desse tipo). Você precisará fornecer suas credenciais. 
4. Abra outra janela do navegador (comum) e vá para [Azure](https://portal.azure.com). Saia. Em seguida, abra o link e entre com as credenciais corretas.
5. Os usuários do Edge e do Internet Explorer também podem receber esse erro quando não há suporte para as configurações de zona confiável.
   
    Verifique se o [portal Analytics](https://analytics.applicationinsights.io) e o [portal do Azure Active Directory](https://portal.azure.com) estão na mesma zona de segurança:
   
   * No Internet Explorer, abra **Opções da Internet**, **Segurança**, **Sites Confiáveis**, **Sites**:
     
     ![Caixa de diálogo Opções da Internet, adicionando um site aos Sites Confiáveis](./media/app-insights-analytics-troubleshooting/033.png)
     
     Na Lista de sites, se qualquer uma das seguintes URLs estiverem incluídas, verifique se as outras também estão:
     
     https://analytics.applicationinsights.io<br/>
     https://login.microsoftonline.com<br/>
     https://login.windows.net

## <a name="e-d"></a>404 ... Recurso não encontrado
![404... recurso não encontrado](./media/app-insights-analytics-troubleshooting/040.png)

O recurso de aplicativo foi excluído do Application Insights e não está mais disponível. Isso pode acontecer se você salvou a URL para a página do Analytics.

## <a name="e-e"></a>403 ... Sem autorização
![403 ... não autorizado](./media/app-insights-analytics-troubleshooting/050.png)

Você não tem permissão para abrir este aplicativo no Analytics.

* Você obteve o link com outra pessoa? Peça a ela para verificar se você está incluído como [leitor ou colaborador para esse grupo de recursos](app-insights-resources-roles-access-control.md).
* Você salvou o link usando credenciais diferentes? Abra o [Portal do Azure](https://portal.azure.com), saia e tente acessar novamente esse link fornecendo as credenciais corretas.

## <a name="html-storage"></a>403 ... Armazenamento HTML5
Nosso portal usa sessionStorage e localStorage do HTML5.

* Chrome: configurações, privacidade, configurações de conteúdo.
* Internet Explorer: Opções da Internet, guia Avançado, Segurança, Habilitar o armazenamento DOM

![403... tente habilitar o armazenamento HTML5](./media/app-insights-analytics-troubleshooting/060.png)

## <a name="e-g"></a>404 ... Assinatura não encontrada
![404 ... Assinatura não encontrada](./media/app-insights-analytics-troubleshooting/070.png)

A URL é inválida. 

* Abra o recurso de aplicativo no [Portal do Application Insights](https://portal.azure.com). Use então o botão Analytics.

## <a name="e-h"></a>404... a página não existe
![404 ... A página não existe](./media/app-insights-analytics-troubleshooting/080.png)

A URL é inválida.

* Abra o recurso de aplicativo no [Portal do Application Insights](https://portal.azure.com). Use então o botão Analytics.

## <a name="cookies"></a>Habilitar cookies de terceiros
  Consulte [como desabilitar cookies de terceiros](http://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), porém observe que precisamos **habilitá-los** .


[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

