---
title: Introdução - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Esta é uma visão geral mais detalhada que destaca o Threat Modeling Tool em ação.
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
ms.author: jegeib
ms.openlocfilehash: 6315e6d39a3b68854beb6563d075e3c79ca93a69
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610833"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Introdução ao Threat Modeling Tool

A Microsoft Threat Modeling Tool 2018 foi lançada em GA em setembro de 2018 como um componente gratuito do tipo **[clique para baixar](https://aka.ms/threatmodelingtool)**. A alteração no mecanismo de entrega nos permite efetuar push dos aprimoramentos mais recentes e correções de bug para os clientes toda vez que eles abrem a ferramenta, facilitando a manutenção e o uso da ferramenta.
Este artigo explica o processo de como começar a usar a abordagem de modelagem de ameaça do Microsoft Security Development Lifecycle (SDL), além de mostrar como usar a ferramenta para desenvolver excelentes modelos de ameaça como um alicerce do seu processo de segurança.

Este artigo se baseia no conhecimento existente da abordagem de modelagem de ameaça do SDL. Para fazer uma análise rápida, confira **[Aplicativos Web de modelagem de ameaça](https://msdn.microsoft.com/library/ms978516.aspx)** e uma versão arquivada do artigo do MSDN **[Descubra falhas de segurança usando a abordagem STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)**, publicado em 2006.

Em poucas palavras, a abordagem envolve a criação de um diagrama, a identificação de ameaças, a mitigação delas e a validação de cada mitigação. Veja um diagrama que destaca esse processo:

![Processo do SDL](./media/azure-security-threat-modeling-tool-feature-overview/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>Iniciando a processo de modelagem de ameaça

Ao iniciar o Threat Modeling Tool, você perceberá alguns itens, como pode ser visto na imagem:

![Página inicial em branco](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Seção de modelo de ameaça

| Componente                                   | Detalhes                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Botão Comentários, Sugestões e Problemas** | Direciona você para o **[Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** em relação a todos os assuntos sobre o SDL. Desse modo, você tem a oportunidade de saber o que os outros usuários estão fazendo, além de encontrar soluções alternativas e recomendações. Caso não encontre o que está procurando, envie um email para tmtextsupport@microsoft.com e nossa equipe de suporte ajudará você                                                                                                                            |
| **Criar um modelo**                          | Abre uma tela em branco para desenhar seu diagrama. Não se esqueça de escolher o padrão que deseja usar no seu modelo                                                                                                                                                                                                                                                                                                                                                                       |
| **Padrão para novos modelos**                 | Você deve escolher o padrão a ser usado antes de criar um modelo. Nosso modelo principal é Azure Threat Model Template, que contém estênceis, ameaças e mitigações específicos do Azure. Para modelos genéricos, escolha Base de Dados de Conhecimento de TM do SDL no menu suspenso. Deseja criar seu próprio modelo ou enviar um novo para todos os usuários? Confira nossa Página no GitHub, **[Repositório de Modelos](https://github.com/Microsoft/threat-modeling-templates)**, para saber mais                              |
| **Abrir um modelo**                            | <p>Abre modelos de ameaça salvos anteriormente. Caso você precise abrir os arquivos mais recentes, Modelos Abertos Recentemente é um excelente recurso. Ao passar o mouse sobre a seleção, você verá 2 maneiras de abrir modelos:</p><p><ul><li>Abrir deste Computador – modo clássico de abrir um arquivo usando o armazenamento local</li><li>Abrir do OneDrive – as equipes podem usar pastas no OneDrive para salvar e compartilhar seus modelos de ameaças em um único local, de modo a ajudar a aumentar a produtividade e a colaboração</li></ul></p> |
| **Guia de Introdução**                   | Abre a página principal do **[Microsoft Threat Modeling Tool](./azure-security-threat-modeling-tool.md)**                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Seção do modelo

| Componente               | Detalhes                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Criar Novo Modelo** | Abre um modelo em branco para que você possa criar um. A menos que você tenha vasto conhecimento na criação de modelos do zero, é recomendável criar um usando os existentes |
| **Abrir Modelo**       | Abre modelos existentes para que você possa fazer alterações neles                                                                                                              |

A equipe do Threat Modeling Tool trabalha continuamente para melhorar a funcionalidade e a experiência da ferramenta. Algumas pequenas alterações podem ocorrer ao longo do ano, mas todas as alterações importantes exigem reformulação no guia. Consulte-o com frequência para garantir que você fique a par dos anúncios mais recentes.

## <a name="building-a-model"></a>Criando um modelo

Nesta seção, vamos seguir:

- Cristina (uma desenvolvedora)
- Ricardo (um gerente de programas) e
- Ashish (um testador)

Eles passarão pelo processo de desenvolver o primeiro modelo de ameaça.

> Ricardo: Olá Cristina, estive trabalhando no diagrama do modelo de risco e quero me certificar de que entendemos os detalhes. Você pode me ajudar a examiná-lo?
> Cristina: Com certeza. Vamos conferir isso.
> Ricardo abre a ferramenta e compartilha sua tela com Cristina.

![Modelo Básico de Ameaça](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: Certo, parece claro, mas você pode me explicar?
> Ricardo: Certo! Esta é a explicação:
> - Nosso usuário humano é representado como uma entidade externa — um quadrado
> - Ele está enviando comandos para nosso servidor Web — o círculo
> - O servidor Web está consultando um banco de dados (duas linhas paralelas)

O que Ricardo acabou de mostrar a Cristina é um DFD, isto é, **[Diagrama de Fluxo de Dados](https://en.wikipedia.org/wiki/Data_flow_diagram)**. A ferramenta Threat Modeling Tool permite que os usuários especifiquem limites confiáveis, indicados pelas linhas pontilhadas vermelhas, para mostrar onde as diferentes entidades estão no controle. Por exemplo, os administradores de TI exigem um sistema do Active Directory para fins de autenticação, de modo que o Active Directory está fora do controle deles.

> Cristina: Para mim, parece que está tudo certo. E quanto às ameaças?
> Ricardo: Deixe-me mostrar.

## <a name="analyzing-threats"></a>Analisando ameaças

Quando ele clica no modo de exibição de análise na seleção de menu do ícone (arquivo com a lupa), ele vê uma lista de ameaças geradas que o Threat Modeling Tool encontrou com base no modelo padrão, que usa a abordagem SDL chamada **[STRIDE (Falsificação, Adulteração, Divulgação de Informações, Repúdio, Negação de Serviço e Elevação de Privilégio)](https://en.wikipedia.org/wiki/STRIDE_(security))**. A ideia é que o software incorra em um conjunto previsível de ameaças, que pode ser encontrado usando estas 6 categorias.

Pense nessa abordagem como se você estivesse protegendo sua casa, garantindo que cada porta e janela tenha um mecanismo de trava, antes de ter um sistema de alarme ou de registrar um boletim de ocorrência por roubo.

![Ameaças básicas](./media/azure-security-threat-modeling-tool-feature-overview/basicthreats.png)

Ricardo começa escolhendo o primeiro item na lista. Veja o que acontece:

Primeiro, a interação entre os dois estênceis é aprimorada

![Interação](./media/azure-security-threat-modeling-tool-feature-overview/interaction.png)

Segundo, informações adicionais sobre a ameaça aparecem na janela Propriedades da Ameaça

![Informações da Interação](./media/azure-security-threat-modeling-tool-feature-overview/interactioninfo.png)

A ameaça gerada ajuda ele a entender as possíveis falhas de design. A categorização STRIDE dá a ele uma ideia sobre os potenciais vetores de ataque, enquanto a descrição adicional diz exatamente o que está errado e as possíveis maneiras de mitigá-lo. Ele pode usar campos editáveis para fazer anotações nos detalhes da justificativa ou alterar as classificações de prioridade, dependendo da barra de bugs da organização.

Os modelos do Azure têm detalhes adicionais que ajudam os usuários a entender não apenas o que está errado, mas também como corrigir acrescentando descrições, exemplos e hiperlinks para documentação específica do Azure.

A descrição faz com que ele perceba a importância de adicionar um mecanismo de autenticação, a fim de impedir os usuários de falsificar, revelando a primeira ameaça na qual trabalhar. Depois do debate de alguns minutos com Cristina, eles entenderam a importância de implementar o controle de acesso e as funções. Ricardo fez algumas anotações rápidas para garantir essas implementações.

Depois que Ricardo verificou as ameaças na categoria de Divulgação de Informações, ele percebeu que o plano de controle de acesso exigia algumas contas somente leitura para auditoria e geração de relatório. Ele se perguntou se essa deveria ser uma nova ameaça, mas as mitigações eram as mesmas, de modo que ele anotou a ameaça corretamente.
Ele também pensou um pouco mais sobre a divulgação de informações e percebeu que as fitas de backup precisariam de criptografia, um trabalho para a equipe de operações.

As ameaças que não se aplicam ao design devido às mitigações existentes ou garantias de segurança podem ser alteradas para "Não Aplicável" no menu suspenso Status. Há três outras opções: Não iniciada – seleção padrão, Precisa de investigação – usada para acompanhamento de itens e Mitigada – depois que todo o trabalho possível foi realizado.

## <a name="reports--sharing"></a>Relatórios e compartilhamento

Depois que Ricardo percorre a lista com Cristina e adiciona observações importantes, mitigações/justificativas, prioridade e alterações de status, ele seleciona Relatórios -> Criar Relatório Completo -> Salvar Relatório, o que imprime um relatório para que ele examine com os colegas a fim de garantir que a segurança correta seja implementada.

![Informações da Interação](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

Se Ricardo quiser compartilhar o arquivo, ele pode fazer isso facilmente salvando-o em sua conta do OneDrive na organização. Fazendo isso, ele pode copiar o link do documento e compartilhá-lo com seus colegas. 

## <a name="threat-modeling-meetings"></a>Reuniões sobre a modelagem de ameaça

Quando Ricardo enviou seu modelo de ameaça para seu colega usando o OneDrive, Ashish, o testador, ficou desanimado. Parece que Ricardo e Cristina deixaram escapar alguns pontos importantes, que poderiam ser facilmente comprometidos. Sua ceticismo se juntou aos modelos de ameaça.

Nesse cenário, depois que Ashish assumiu o modelo de ameaça, ele foi convocado para duas reuniões sobre a modelagem de ameaça: uma reunião para sincronização do processo e apresentação dos diagramas e a segunda reunião para revisão da ameaça e aprovação.

Na primeira reunião, Ashish gastou 10 minutos explicando a cada um o processo de modelagem de ameaça do SDL. Em seguida, passou para o diagrama do modelo de ameaça e começou a explicá-lo em detalhes. Em cinco minutos, foi identificada a ausência de um componente importante.

Alguns minutos mais tarde, Ashish e Ricardo iniciaram uma longa discussão sobre como o servidor Web foi criado. Não é a maneira ideal de dar continuidade a uma reunião, mas, no fim, todos concordaram que descobrir a discrepância antecipadamente pouparia tempo no futuro.

Na segunda reunião, a equipe analisou as ameaças, discutiu algumas formas de solucioná-las e aprovou o modelo de ameaça. Ela verificou o documento no controle de código-fonte e prosseguiu com o desenvolvimento.

## <a name="thinking-about-assets"></a>Pensando sobre os ativos

Alguns leitores com a modelagem de ameaça podem notar que não falamos sobre ativos. Descobrimos que muitos engenheiros de software entendem melhor o respectivo software do que o conceito de ativos e em quais ativos um invasor pode estar interessado.

Considerando o modelo de ameaça de uma casa, você pode começar pensando na sua família, em fotos insubstituíveis ou em obras valiosas. Talvez você possa começar pensando sobre quem poderia invadi-la e no atual sistema de segurança. Ou ainda considerando os aspectos físicos, como a piscina ou a varanda. Esses exemplos são uma analogia aos ativos, invasores ou design de software. Qualquer uma dessas três abordagens funciona.

A abordagem à modelagem de ameaça que apresentamos aqui é consideravelmente simples, em comparação com o que a Microsoft fez no passado. Descobrimos que a abordagem de design de software funciona bem para muitas equipes. Esperamos que inclua a sua.

## <a name="next-steps"></a>Próximas etapas

Envie seus comentários, dúvidas e preocupações para tmtextsupport@microsoft.com. **[Baixe](https://aka.ms/threatmodelingtool)** a ferramenta Threat Modeling Tool para começar a usá-la hoje mesmo.
