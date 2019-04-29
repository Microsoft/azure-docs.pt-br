---
title: Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Conheça todos os recursos disponíveis no Threat Modeling Tool
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
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 601f3bf05388406c8f96a7351f7fb3aa4de2650a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60588801"
---
# <a name="threat-modeling-tool-feature-overview"></a>Visão geral do recurso Threat Modeling Tool

A ferramenta Threat Modeling Tool pode ajudá-lo com suas necessidades de modelagem de ameaças. Para obter uma introdução básica à ferramenta, consulte [Introdução ao Threat Modeling Tool](./azure-security-threat-modeling-tool-getting-started.md).

> [!NOTE]
>A Threat Modeling Tool é atualizada com frequência portanto, sempre verifique esse guia para ficar a par de nossos recursos e aprimoramentos mais recentes.

Para abrir uma página em branco, selecione **Criar um modelo**.

![Página em branco](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

Para ver os recursos disponíveis na ferramenta, use o modelo de ameaça criado pela nossa equipe no exemplo de [Introdução](./azure-security-threat-modeling-tool-getting-started.md).

![Modelo de ameaça básico](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navegação

Antes de discutirmos os recursos internos, vamos analisar os principais componentes encontrados na ferramenta.

### <a name="menu-items"></a>Itens de menu

A experiência é semelhante a de outros produtos da Microsoft. Vamos analisar os itens de menu de nível superior.

![Itens de menu](./media/azure-security-threat-modeling-tool-feature-overview/menuitems.png)

| Rótulo                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Arquivo** | <ul><li>Abrir, salvar e fechar arquivos</li><li>Entrar e sair das contas do OneDrive.</li><li>Compartilhar links (exibir e editar).</li><li>Exibir informações do arquivo.</li><li>Aplicar um novo modelo aos modelos já existentes.</li></ul> |
| **Editar** | Desfazer e refazer ações, bem como copiar, colar e excluir. |
| **Exibir** | <ul><li>Alternar entre as exibições de **Análise** e **Design**.</li><li>Abrir janelas fechadas (por exemplo, estênceis, propriedades do elemento e mensagens).</li><li>Redefinir layout para as configurações padrão.</li></ul> |
| **Diagrama** | Adicionar e excluir diagramas e navegar pelas guias dos diagramas. |
| **Relatórios** | Criar relatórios em HTML para compartilhar com outras pessoas. |
| **Ajuda** | Encontre guias para ajudar você a usar a ferramenta. |

Os símbolos são atalhos para os menus de nível superior:

| Símbolo                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Abrir** | Abre um novo arquivo. |
| **Salvar** | Salva o arquivo atual. |
| **Design** | Abre o modo de exibição de **Design**, no qual é possível criar modelos. |
| **Analise** | Mostra ameaças geradas e suas propriedades. |
| **Adicionar diagrama** | Adiciona um novo diagrama (semelhante às novas guias no Excel). |
| **Excluir Diagrama** | Exclui o diagrama atual. |
| **Copiar/Recortar/Colar** | Copia, recorta e cola elementos. |
| **Desfazer/Refazer** | Desfaz e refaz ações. |
| **Ampliar/Reduzir** | Amplia e reduz o diagrama para uma exibição melhor. |
| **Comentários** | Abre o Fórum do MSDN. |

### <a name="canvas"></a>Tela

A tela é o espaço onde você arrasta e solta elementos. Arrastar e soltar é a maneira mais rápida e eficiente de criar modelos. Você também pode clicar com o botão direito e selecionar itens do menu para adicionar versões genéricas de elementos, conforme mostrado:

#### <a name="drop-the-stencil-on-the-canvas"></a>Solte o estêncil na tela

![Descarte de tela](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Selecione o estêncil

![Propriedades do elemento](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Estênceis

Você pode encontrar todos os estênceis disponíveis para uso com base no modelo selecionado. Se você não conseguir localizar os elementos corretos, use outro modelo. Ou você pode modificar um modelo para atender às suas necessidades. No geral, você pode encontrar uma combinação de categorias, como as mostradas abaixo:

| Nome do estêncil                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Processo** | Aplicativos, plug-ins de navegador, threads, máquinas virtuais |
| **Interagente externo** | Provedores de autenticação, navegadores, usuários, aplicativos Web |
| **Armazenamento de dados** | Cache, armazenamento, arquivos de configuração, banco de dados, registro |
| **Fluxo de dados** | Binário, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, Pipe Nomeado, RPC/DCOM, SMB, UDP |
| **Limite de linha/borda de confiança** | Redes corporativas, Internet, computador, área restrita, modo de usuário/kernel |

### <a name="notesmessages"></a>Observações/mensagens

| Componente                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Mensagens** | Lógica de ferramenta interna que alerta os usuários sempre que houver um erro, como ausência de fluxos de dados entre elementos. |
| **Observações** | Anotações manuais são adicionadas ao arquivo pelas equipes de engenharia em todo o processo de design e revisão. |

### <a name="element-properties"></a>Propriedades do elemento

As propriedades do elemento variam de acordo com os elementos que você selecionar. Com exceção dos limites de confiança, todos os outros elementos contêm três seleções gerais:

| Propriedade do elemento                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Nome** | Útil para nomear seus processos, armazenamentos, interagentes e fluxos para serem facilmente reconhecidos. |
| **Fora do escopo** | Se selecionado, o elemento é retirado da matriz de geração de ameaças (não recomendado). |
| **Motivo para estar fora do escopo** | O campo de justificativa para permitir que os usuários saibam por que Fora do escopo foi selecionado. |

As propriedades são alteradas em cada categoria de elemento. Selecione cada elemento para verificar as opções disponíveis. Ou você pode abrir o modelo para obter mais informações. Vamos examinar os recursos.

## <a name="welcome-screen"></a>Tela de boas-vindas

Quando você abre o aplicativo, você verá a tela de **Boas-vindas**.

### <a name="open-a-model"></a>Abrir um modelo

Passe o mouse sobre **Abrir um Modelo** para mostrar duas opções: **Abrir Deste Computador** e **Abrir do OneDrive**. A primeira opção abre a tela de **Abertura de arquivo**. A segunda opção leva você através do processo de entrada para OneDrive. Após a autenticação bem-sucedida, você pode selecionar arquivos e pastas.

![Abrir modelo](./media/azure-security-threat-modeling-tool-feature-overview/openmodel.png)

![Abrir do computador ou do OneDrive](./media/azure-security-threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Comentários, sugestões e problemas

Quando você seleciona **Comentários, sugestões e problemas**, vá para o fórum do MSDN para as ferramentas do SDL. Você pode conferir o que as outras pessoas estão dizendo sobre a ferramenta, incluindo soluções alternativas e novas ideias.

![Comentários](./media/azure-security-threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Modo de exibição de Design

Quando você abrir ou criar um novo modelo, o modo de exibição de **Design** abre.

### <a name="add-elements"></a>Adicionar elementos

Você pode adicionar elementos na grade de duas maneiras:

- **Arrastar e soltar**: Arraste o elemento desejado para a grade. Em seguida, use as propriedades de elemento para fornecer informações adicionais.
- **Clicar com o botão direito do mouse**: Clique com o botão direito do mouse em qualquer lugar na grade e selecione os itens no menu suspenso. Uma representação genérica do elemento selecionado aparecerá na tela.

### <a name="connect-elements"></a>Conectar elementos

Você pode conectar elementos de duas maneiras:

- **Arrastar e soltar**: Arraste o fluxo de dados desejado para a grade e conecte ambas as extremidades aos elementos apropriados.
- **Clique + Shift**: Clique no primeiro elemento (enviando dados), pressione e segure a tecla Shift e selecione o segundo elemento (recebendo dados). Clique com o botão direito do mouse e selecione **Conectar**. Se você estiver usando um fluxo de dados bidirecional, a ordem não é tão importante.

### <a name="properties"></a>propriedades

 Para ver as propriedades que podem ser modificadas em estênceis, selecione o estêncil e as informações serão devidamente populadas. O exemplo abaixo mostra o antes e o depois que o estêncil de um **Banco de dados** é arrastado para o diagrama:

#### <a name="before"></a>Antes

![Antes](./media/azure-security-threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>após

![após](./media/azure-security-threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Mensagens

Se você criar um modelo de ameaça e se esquecer de conectar os fluxos de dados aos elementos, você receberá uma notificação. Você pode ignorar a mensagem ou seguir as instruções para corrigir o problema. 

![Mensagens](./media/azure-security-threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Observações

Para adicionar anotações ao seu diagrama, alterne entre a guia **Mensagens** e a guia **Notas**.

## <a name="analysis-view"></a>Modo de exibição de análise

Depois de criar seu diagrama, selecione o símbolo de **Análise** (lupa) na barra de ferramentas de atalhos para alternar para o modo de exibição de **Análise**.

![Modo de exibição de análise](./media/azure-security-threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Seleção de ameaça gerada

Quando você seleciona uma ameaça, você pode usar três funções diferentes:

| Recurso                               | Informações      |
| --------------------------------------- | ------------ |
| **Indicador de leitura** | <p>A ameaça é marcada como leitura, o que ajuda a acompanhar os itens que você leu.</p><p>![Indicador de lido/não lido](./media/azure-security-threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Foco de interação** | <p>A interação no diagrama que pertence a uma ameaça é realçada.</p><p>![Foco de interação](./media/azure-security-threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Propriedades da ameaça** | <p>Informações adicionais sobre a ameaça aparecem na janela **Propriedades da Ameaça**.</p><p>![Propriedades da ameaça](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Alteração de prioridade

Você pode alterar o nível de prioridade de cada ameaça gerada. Cores diferentes tornam mais fácil identificar ameças de prioridade alta, média e baixa.

![Alteração de prioridade](./media/azure-security-threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Campos editáveis das propriedades da ameaça

Conforme mostrado na imagem anterior, você pode alterar as informações geradas pela ferramenta. Você também pode adicionar informações a alguns campos, como a justificação. Esses campos são gerados pelo modelo. Se você precisar de mais informações para cada ameaça, você pode fazer modificações.

![Propriedades da ameaça](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Relatórios

Quando terminar de alterar as prioridades e atualizar o status de cada ameaça gerada, você poderá salvar o arquivo e/ou imprimir um relatório. Vá para **Relatório** > **Criar relatório completo**. Nomeie o relatório e você deverá ver algo semelhante à seguinte imagem:

![Relatório](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Próximas etapas

- Envie seus comentários, dúvidas e preocupações para tmtextsupport@microsoft.com. **[Baixe](https://aka.ms/threatmodelingtool)** a ferramenta Threat Modeling Tool para começar a usá-la hoje mesmo.
- Para contribuir com um modelo para a comunidade, visite nossa página no [GitHub](https://github.com/Microsoft/threat-modeling-templates).