---
title: "O que há de novo nas Coleções de Espaços de Trabalho do Power BI"
description: "Obtenha as informações mais recentes sobre as Coleções de Espaços de Trabalho do Power BI"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 2794ae98-b9a7-45df-b6e1-962a395b91fa
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: a2faf610ca50acdb54353ade7c7a4ecabd314347
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="whats-new-in-power-bi-workspace-collections"></a>O que há de novo nas Coleções de Espaços de Trabalho do Power BI

Atualizações das **Coleções de Espaços de Trabalho do Power BI** são lançadas regularmente. No entanto, nem todas as versões incluem novos recursos voltados para o usuário; algumas versões se concentram em recursos de serviço de back-end. Destacaremos novos recursos voltados para o usuário aqui.

> [!IMPORTANT]
> As Coleções do Espaço de Trabalho do Power BI foram preteridas e estarão disponíveis até junho de 2018 ou conforme a indicação do seu contrato. Recomendamos planejar a migração para o Power BI Embedded a fim de evitar interrupções em seu aplicativo. Para saber mais sobre como migrar seus dados para o Power BI Embedded, confira [Como migrar o conteúdo das Coleções do Espaço de Trabalho do Power BI para o Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="march-2017"></a>Março de 2017

**Recursos de autoatendimento**

* [Criar novo relatório](create-report-from-dataset.md)
* [Relatório SaveAs](save-reports.md)
* Inserir o relatório no modo de Leitura/Editar/Criar novo 
* [Alternar entre os modos de edição/leitura do relatório](toggle-mode.md)

**Conectividade de dados com APIs REST**

* [Criar conjunto de dados](https://msdn.microsoft.com/library/azure/mt778875.aspx)
* Enviar dados por push 

**APIs de gerenciamento**

* Clonar relatório e conjunto de dados
* Associar um relatório a um conjunto de dados diferente

**Exemplos**

* [Amostra de inserção de relatório JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo) atualizada

## <a name="december-2016"></a>Dezembro de 2016

* [Novo exemplo inserido de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)

## <a name="october-2016"></a>Outubro de 2016

* [Análise avançada com Coleções de Espaços de Trabalho do Power BI e R](https://powerbi.microsoft.com/blog/r-in-pbie/)

## <a name="august-31-2016"></a>31 de agosto de 2016
Incluído nesta versão:

* Todos os novos SDKs do JavaScript que dão suporte à [filtragem avançada e à navegação de página](interact-with-reports.md).
* Agora as Coleções de Espaços de Trabalho do Power BI têm suporte no datacenter Canadá Central. Verifique o [status do datacenter](https://azure.microsoft.com/status/).

## <a name="july-11-2016"></a>11 de julho de 2016
Incluído nesta versão:

* **Ótimas notícias!** O serviço de Coleções de Espaços de Trabalho do Power BI não está mais na versão prévia, agora está com GA (disponibilidade geral).  
* Todas as APIs REST foram movidas de **/beta** para **/v1.0**.
* Os SDKs de JavaScript e .NET foram atualizados para **v1.0**.
* Chamadas à API do Power BI agora podem ser autenticadas usando chaves de API. Tokens de Aplicativo são necessários somente para incorporação. Como parte desse processo, tokens de provisionamento e desenvolvimento foram preteridos nas APIs da v1.0, mas eles continuarão a funcionar na versão beta até 30 de dezembro de 2016. Para saber mais, confira [Autenticando e autorizando com Coleções de Espaços de Trabalho do Power BI](app-token-flow.md).
* Suporte a RLS (segurança em nível de linha) para tokens de aplicativo e relatórios incorporados. Para saber mais, confira [Segurança em nível de linha com as Coleções de Espaços de Trabalho do Power BI](row-level-security.md).
* Atualizado o aplicativo de exemplo para todas as chamadas à API da **v1.0** .
* As Coleções de Espaços de Trabalho do Power BI dão suporte ao SDK do Azure, PowerShell e CLI.
* Os usuários podem exportar dados de visualização para um **.csv**.
* As Coleções de Espaços de Trabalho do Power BI agora têm suporte em todos os mesmos idiomas/localidades que o Microsoft Azure. Para saber mais, confira [Azure – Idiomas](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx).

