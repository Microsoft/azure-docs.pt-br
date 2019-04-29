---
title: Versões da Threat Modeling Tool – Microsoft Threat Modeling Tool Azure | Microsoft Docs
description: Documentando as notas sobre a versão da Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2019
ms.author: jegeib
ms.openlocfilehash: bdf8b701567aaa3a0d9006333557bcec4f312723
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586371"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Versão 7.1.50911.2 da Threat Modeling Tool de disponibilidade geral – 12/09/2018

Estamos felizes em anunciar que a Microsoft Threat Modeling Tool agora está disponível para download como uma versão de disponibilidade geral com suporte. Essa versão contém atualizações importantes de privacidade e segurança, além de correções de bug, atualizações de recurso e melhorias de estabilidade. Os usuários existentes da versão prévia de 2017 serão solicitados a atualizar para a versão mais recente usando a tecnologia ClickOnce depois de abrir o cliente. Os novos usuários da ferramenta devem [clicar aqui para baixar o cliente](https://aka.ms/threatmodelingtool).

Com esta versão, estamos encerrando o suporte da versão prévia de 2017 e recomendamos que todos os usuários dela atualizem para a versão de disponibilidade geral. Em 15 de outubro de 2018 ou após essa data, definiremos a versão mínima do ClickOnce necessária para a Threat Modeling Tool. Todos os clientes da versão prévia deverão atualizar.

A Microsoft Threat Modeling Tool 2016, que está disponível no [Centro de Download da Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=49168), permanece com suporte até 1º de outubro de 2019 somente para correções de segurança críticas.

## <a name="feature-changes"></a>Alterações de recurso

### <a name="azure-stencil-updates"></a>Atualizações de estêncil do Azure

Os estênceis adicionais do Azure e suas ameaças e mitigações associadas foram adicionados ao conjunto de estênceis enviado com esta versão. Foram feitas alterações significativas nas áreas de foco dos "Serviços de Aplicativos do Azure", nas "Ofertas de banco de dados do Azure" e no "Armazenamento do Azure".

![Atualizações de estêncil do Azure](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>Recurso de integração do OneDrive removido

Os recursos "Salvar no OneDrive", "Abrir do OneDrive" e "Compartilhar um Link" da versão prévia foram removidos. Os usuários do OneDrive são incentivados a usar o cliente [OneDrive para Windows](https://onedrive.live.com/about/en-us/download/) da Microsoft para acessar seus arquivos armazenados no OneDrive por meio das caixas de diálogo padrão de abertura e salvamento.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Bugs notáveis relatados por clientes corrigidos

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>Na versão prévia da TMT, a ferramenta falha ao usar o modelo padrão

- Quando um estêncil genérico (por exemplo o "fluxo de dados genérico") é adicionado à superfície de desenho e gera as ameaças, a ferramenta podia falhar. Esse problema foi corrigido.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>Na versão prévia da TMT, ao salvar um relatório ou copiar as ameaças, os níveis de risco ficavam incorretos

- Se um usuário modificava o nível de risco de ameaças específicas e, em seguida, salvava um relatório ou copiava os riscos, o nível de risco podia ser revertido para "Alto". Esse problema foi corrigido.

## <a name="known-issues-and-faq"></a>Problemas conhecidos e perguntas frequentes

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>Os usuários de telas de alta resolução podem visualizar um texto pequeno nas propriedades da ameaça

#### <a name="issue"></a>Problema

Na Exibição de Análise da ferramenta, se o usuário usa uma tela de alta resolução definida por padrão para ampliar e facilitar a leitura no Windows, a seção "Mitigações possíveis" de uma ameaça pode aparecer com texto pequeno.

![Problema conhecido com telas de alta resolução](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Solução alternativa

O usuário pode clicar no texto de mitigação e usar o controle de zoom padrão do Windows (Ctrl + roda do mouse para cima) para aumentar a ampliação dessa seção.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Os arquivos na seção "Modelos abertos recentemente" da janela principal podem falhar ao abrir

#### <a name="issue"></a>Problema

O recurso "Abrir do OneDrive" da versão prévia foi removido. Os usuários com "Modelos abertos recentemente" salvos no OneDrive receberão o erro a seguir.

![Recurso do OneDrive removido](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Solução alternativa

Os usuários do OneDrive são incentivados a usar o cliente [OneDrive para Windows](https://onedrive.live.com/about/en-us/download/) da Microsoft para acessar seus arquivos armazenados no OneDrive por meio da caixa de diálogo padrão “Abrir um modelo”.

![Recurso do OneDrive removido](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>Minha organização usa a versão de 2016 da ferramenta, posso usar o conjunto de estênceis do Azure?

Sim, você pode. O [conjunto de estênceis do Azure está disponível no github](https://github.com/Microsoft/threat-modeling-templates/) e pode ser carregado na versão de 2016 da ferramenta. Para criar um modelo com o conjunto de estênceis do Azure, use a caixa de diálogo "Modelo para novos modelos" na tela do menu principal. A TMT 2016 não pode renderizar os links encontrados nos campos "Possíveis Mitigações" do conjunto de estênceis do Azure, portanto os links poderão ser exibidos como marcas HTML.

![Atualizações de estêncil do Azure no cliente de 2016](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Requisitos do sistema

- Sistemas operacionais com suporte
  - Microsoft Windows 10
- Versão necessária do .NET
  - .NET 3.5.2
- Requisitos adicionais
  - Uma conexão de Internet é necessária para receber as atualizações da ferramenta e também os modelos.

## <a name="documentation-and-feedback"></a>Documentação e comentários

- A documentação da Threat Modeling Tool está localizada em [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) e inclui informações [sobre como usar a ferramenta](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Próximas etapas

Baixe a versão mais recente da [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
