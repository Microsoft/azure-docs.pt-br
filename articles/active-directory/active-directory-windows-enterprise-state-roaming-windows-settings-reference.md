---
title: "Referência de configurações de roaming do Windows 10 | Microsoft Docs"
description: "Uma lista completa de todas as configurações que serão ser movidas ou armazenadas em backup no Windows 10."
services: active-directory
keywords: enterprise state roaming, nuvem do window
documentationcenter: 
author: tanning
manager: femila
editor: curtand
ms.assetid: 17cffc3e-2928-4235-91f7-a685bd6bdcbf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: 5082ed8d2f41e72fa979b978e2ac0b0840fdcdac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="windows-10-roaming-settings-reference"></a>Referência de configurações de roaming do Windows 10
A seguir, uma lista completa de todas as configurações que serão ser movidas ou armazenadas em backup no Windows 10. 

## <a name="devices-and-endpoints"></a>Dispositivos e pontos de extremidade
Veja a tabela a seguir para obter um resumo dos dispositivos e dos tipos de conta compatíveis com a estrutura de sincronização, de backup e de restauração no Windows 10.

| Tipo e operação de conta | Desktop | Móvel |
| --- | --- | --- |
| Active Directory do Azure: sincronização |Sim |Não |
| Active Directory do Azure: backup/restauração |Não |Não |
| Conta da Microsoft: sincronização |Sim |Sim |
| Conta da Microsoft: backup/restauração |Não |Sim |

## <a name="what-is-backup"></a>O que é backup?
As configurações do Windows geralmente são sincronizadas por padrão, mas algumas configurações são incluídas apenas no backup, como a lista de aplicativos instalados em um dispositivo. O backup é destinado somente a dispositivos móveis e atualmente não está disponível para usuários do Enterprise State Roaming. O backup usa uma conta da Microsoft e armazena as configurações e dados do aplicativo no OneDrive. Se um usuário desabilitar a sincronização no dispositivo usando o aplicativo Configurações, os dados de aplicativo que normalmente são sincronizados se tornarão somente backup. Os dados de backup só podem ser acessados por meio da operação de restauração durante a primeira execução de um novo dispositivo. Os backups podem ser desabilitados por meio de configurações do dispositivo e podem ser gerenciados e excluídos por meio da conta do OneDrive do usuário.

## <a name="windows-settings-overview"></a>Visão geral das configurações do Windows
Os grupos de configurações a seguir estão disponíveis para que os usuários finais habilitem/desabilitem a sincronização de configurações em dispositivos Windows 10.

* Tema: plano de fundo da área de trabalho, bloco de usuário, posição da barra de tarefas etc. 
* Configurações do Internet Explorer: histórico de navegação, URLs tipadas, favoritos etc.Configurações do Internet Explorer: histórico de navegação, URLs tipadas, favoritos etc. 
* Senhas: [cofre de credenciais do Windows](https://technet.microsoft.com/library/jj554668.aspx), incluindo perfis de Wi-Fi 
* Preferências de Idioma: dicionário de ortografia, configurações de idioma do sistema 
* Facilidade de Acesso: narrador, teclado virtual, lente de aumento 
* Outras Configurações do Windows: veja Detalhes das configurações do Windows

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-individual-sync-settings.png)

A sincronização do grupo de configuração (favoritos, lista de leitura) do navegador Edge pode ser habilitada ou desabilitada por usuários finais por meio da opção de menu Configurações do navegador Edge.

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-sync-content.png)

## <a name="windows-settings-details"></a>Detalhes das configurações do Windows
Na tabela a seguir, Outras entradas na coluna Grupo de Configurações refere-se às configurações que podem ser desabilitadas, vá para Configurações > Contas > Sincronizar as configurações > Outras configurações do Windows. 

As entradas internas na coluna Grupo de Configurações referem-se às configurações e aos aplicativos que só podem ter a sincronização desabilitada no próprio aplicativo ou por meio da desabilitação da sincronização do dispositivo inteiro usando o gerenciamento de dispositivos móveis (MDM) ou as configurações da Política de Grupo.
As configurações que não forem movidas ou sincronizadas não pertencerão a um grupo.

| Configurações | Desktop | Móvel | Agrupar |
| --- | --- | --- | --- |
| **Contas**: imagem da conta |sync |X |Tema |
| **Contas**: outras configurações de conta |X |X | |
| **Banda larga móvel avançada**: nome de rede de compartilhamento de conexão com a Internet (habilita a detecção automática de hotspots Wi-Fi móveis via Bluetooth) |X |X |Senhas |
| **Dados de aplicativo**: aplicativos individuais podem sincronizar dados |sincronizar backup |sincronizar backup |interno |
| **Lista de aplicativos**: lista de aplicativos instalados |X |backup |Outros |
| **Bluetooth**: todas as configurações do Bluetooth |X |X | |
| **Prompt de comando**: configurações “Padrão” do prompt de comando |sync |X | |
| **Credenciais**: Cofre de Credenciais |sync |sync |Senha |
| **Data, Hora e Região**: horário automático (sincronização de horário da Internet) |sync |sync |idioma |
| **Data, Hora e Região**: relógio de 24 horas |sync |X |idioma |
| **Data, Hora e Região**: data e hora |sync |X |idioma |
| **Data, Hora e Região**: fuso horário | |X |idioma |
| **Data, Hora e Região**: horário de verão |sync |X |idioma |
| **Data, Hora e Região**: país/região |sync |X |idioma |
| **Data, Hora e Região**: primeiro dia da semana |sync |X |idioma |
| **Data, Hora e Região**: formato de região (localidade) |sync |X |idioma |
| **Data, Hora e Região**: data abreviada |sync |X |idioma |
| **Data, Hora e Região**: data longa |sync |X |idioma |
| **Data, Hora e Região**: hora abreviada |sync |X |idioma |
| **Data, Hora e Região**: hora longa |sync |X |idioma |
| **Personalização da área de trabalho**: tema da área de trabalho (tela de fundo, cor do sistema, sons do sistema padrão, proteção de tela) |sync |X |Tema |
| **Personalização da área de trabalho**: papel de parede de apresentação de slides |sync |X |Tema |
| **Personalização da área de trabalho**: configurações da barra de tarefas (posição, ocultar automaticamente etc.) |sync |X |Tema |
| **Personalização da área de trabalho**: iniciar o layout da tela |X |backup | |
| **Dispositivos**: impressoras compartilhadas às quais você se conectou |X |X |Outros |
| **Navegador Edge**: lista de leitura |sync |sync |interno |
| **Navegador Edge**: favoritos |sync |sync |interno |
| **Navegador Edge**: principais sites <sup>[[1]](#footnote-1)</sup> |sync |sync |interno |
| **Navegador Edge**: URLs digitadas <sup>[[1]](#footnote-1)</sup> |sync |sync |interno |
| **Navegador Edge**: configurações da barra de favoritos <sup>[[1]](#footnote-1)</sup> |sync |sync |interno |
| **Navegador Edge**: mostrar o botão home <sup>[[1]](#footnote-1)</sup> |sync |sync |interno |
| **Navegador Edge**: bloquear pop-ups <sup>[[1]](#footnote-1)</sup> |sync |sync |interno |
| **Navegador Edge**: perguntar-me o que fazer com cada download <sup>[[1]](#footnote-1)</sup> |sync |sync |interno |
| **Navegador Edge**: oferecer para salvar senhas <sup>[[1]](#footnote-1)</sup> |sync |sync |interno |
| **Navegador Edge**: enviar solicitações para não rastrear <sup>[[1]](#footnote-1)</sup> |sync |sync |interno |
| **Navegador Edge**: salvar entradas de formulário <sup>[[1]](#footnote-1)</sup> |sync |sync |interno |
| **Navegador Edge**: mostrar sugestões de pesquisa e de site conforme eu digitar <sup>[[1]](#footnote-1)</sup> |sync |sync |interno |
| **Navegador Edge**: preferência de cookies <sup>[[1]](#footnote-1)</sup> |sync |sync |interno |
| **Navegador Edge**: permitir que sites salvem licenças de mídia protegida em meu dispositivo <sup>[[1]](#footnote-1)</sup> |sync |sync |interno |
| **Navegador Edge**: configuração de leitor de tela <sup>[[1]](#footnote-1)</sup> |sync |sync |interno |
| **Alto Contraste**: ativado ou desativado |sync |X |facilidade de acesso |
| **Alto Contraste**: configurações do Tema |sync |X |facilidade de acesso |
| **Internet Explorer**: abrir guias (URL e título) |sync |sync |Internet Explorer |
| **Internet Explorer**: lista de leitura |sync |sync |Internet Explorer |
| **Internet Explorer**: URLs tipadas |sync |sync |Internet Explorer |
| **Internet Explorer**: histórico de procura |sync |sync |Internet Explorer |
| **Internet Explorer**: favoritos |sync |sync |Internet Explorer |
| **Internet Explorer**: URLs excluídas |sync |sync |Internet Explorer |
| **Internet Explorer**: home pages |sync |sync |Internet Explorer |
| **Internet Explorer**: sugestões de domínio |sync |sync |Internet Explorer |
| **Teclado**: os usuários podem ativar/desativar o teclado virtual |sync |X |facilidade de acesso |
| **Teclado**: ativar adesivo (desativado por padrão) |sync |X |facilidade de acesso |
| **Teclado**: ativar teclas de filtro (desativado por padrão) |sync |X |facilidade de acesso |
| **Teclado**: ativar teclas de alternância (desativado por padrão) |sync |X |facilidade de acesso |
| **Internet Explorer**: idioma do domínio: chinês (CHS) QWERTY - habilitar a aprendizagem automática |sync |X |idioma |
| **Idioma**: CHS QWERTY - habilitar a classificação dinâmica de candidatos |sync |X |idioma |
| **Idioma**: QWERTY CHS - conjunto de caracteres do chinês simplificado |sync |X |idioma |
| **Idioma**: QWERTY CHS - conjunto de caracteres do chinês tradicional |sync |X |idioma |
| **Idioma**: CHS QWERTY - pinyin difuso |sync |backup |Linguagem |
| **Idioma**: CHS QWERTY - pares difusos |sync |backup |Linguagem |
| **Idioma**: CHS QWERTY - pinyin completo |sync |X |idioma |
| **Idioma**: CHS QWERTY - pinyin duplo |sync |X |idioma |
| **Idioma**: CHS QWERTY - correção automática de leitura |sync |X |idioma |
| **Idioma**: CHS QWERTY - tecla de alternância C/E, shift |sync |X |idioma |
| **Idioma**: CHS QWERTY - tecla de alternância C/E, Ctrl |sync |X |idioma |
| **Idioma**: CHS WUBI - modo de entrada de caractere único |sync |X |idioma |
| **Idioma**: CHS WUBI - mostrar a codificação restante do candidato |sync |X |idioma |
| **Idioma**: CHS WUBI - um aviso sonoro quando a codificação 4 é inválida |sync |X |Linguagem |
| **Idioma**: CHT Bopomofo; inclui CJK Ext-A |sync |X |idioma |
| **Idioma**: IME japonês - digitação preditiva e palavras personalizadas |sync |sync |idioma |
| **Idioma**: IME coreano (KOR) |X |X |idioma |
| **Idioma**: reconhecimento de manuscrito |X |X |idioma |
| **Idioma**: perfil de idioma |sync |backup |idioma |
| **Idioma**: verificação ortográfica - autocorreção e realçar erros de ortografia |sync |backup |idioma |
| **Idioma**: lista de teclados |sync |backup |idioma |
| **Tela de bloqueio**: todas as configurações de tela de bloqueio |X |X | |
| **Lupa**: ativada ou desativada (alternância mestre) |X |X |facilidade de acesso |
| **Lupa**: ativar ou desativar a cor de inversão (desativado por padrão) |sync |X |facilidade de acesso |
| **Lupa**: controle - acompanhar o foco do teclado |sync |X |facilidade de acesso |
| **Lupa**: controle - acompanhar o cursor do mouse |sync |X |facilidade de acesso |
| **Magnifier**: iniciar quando os usuários entram (desativado por padrão) |sync |X |facilidade de acesso |
| **Mouse**: alterar o tamanho do cursor do mouse |sync |X |Outros |
| **Mouse**: alterar a cor do cursor do mouse |sync |X |Outros |
| **Mouse**: todas as outras configurações |X |X | |
| **Narrador**: início rápido |sync |X |facilidade de acesso |
| **Narrador**: os usuários podem alterar o tom de fala do Narrador |sync |X |facilidade de acesso |
| **Narrador**: os usuários podem ativar ou desativar as dicas de leitura do Narrador para itens comuns (ativado por padrão) |sync |X |facilidade de acesso |
| **Narrador**: os usuários podem ativar ou desativar se eles podem ouvir os caracteres digitados (ativado por padrão) |sync |X |facilidade de acesso |
| **Narrador**: os usuários podem ativar ou desativar se eles podem ouvir as palavras digitadas (ativado por padrão) |sync |X |facilidade de acesso |
| **Narrador**: fazer o cursor de inserção seguir o Narrador (ativado por padrão) |sync |X |facilidade de acesso |
| **Narrador**: habilitar o realce visual do cursor do Narrador (ativado por padrão) |sync |X |facilidade de acesso |
| **Narrador**: reproduzir as indicações de áudio (ativado por padrão) |sync |X |facilidade de acesso |
| **Narrador**: ativar as teclas do teclado de toque quando você levanta seu dedo (desativado por padrão) |sync |X |facilidade de acesso |
| **Facilidade de acesso**: definir a espessura do cursor intermitente |sync |X |facilidade de acesso |
| **Facilidade de acesso**: remover as imagens da tela de fundo (desativado por padrão) |sync |X |facilidade de acesso |
| **Ligar e Suspensão**: todas as configurações |X |X | |
| **Iniciar a personalização de tela**: cor de ênfase (apenas telefone) |X |sync |Tema |
| **Digitação**: dicionário de ortografia |sync |backup |idioma |
| **Digitação**: autocorreção de palavra incorreta |sync |backup |idioma |
| **Digitação**: realçar as palavras incorretas |sync |backup |idioma |
| **Digitação**: mostrar sugestões de texto ao digitar |sync |backup |idioma |
| **Digitação**: adicionar um espaço depois de escolher uma sugestão de texto |sync |backup |idioma |
| **Digitação**: adicionar um ponto depois de um toque duplo na barra de espaços |sync |backup |idioma |
| **Digitação**: colocar a primeira letra de cada frase em maiúscula |sync |backup |idioma |
| **Digitação**: usar todas as letras maiúsculas quando tocar duas vezes na tecla shift |sync |backup |idioma |
| **Digitação**: tocar sons de tecla ao digitar |sync |backup |idioma |
| **Digitação**: dados de personalização para teclado de toque |sync |backup |idioma |
| **Wi-Fi**: perfis de Wi-Fi (somente WPA) |sync |sync |Senhas |

###### <a name="footnote-1"></a>Nota de rodapé 1
Versão mínima do SO com suporte da Atualização do Windows para Criadores (Build 15063). 

## <a name="related-topics"></a>Tópicos relacionados
* [Visão geral do enterprise state roaming](active-directory-windows-enterprise-state-roaming-overview.md)
* [Habilitar o enterprise state roaming no Active Directory do Azure](active-directory-windows-enterprise-state-roaming-enable.md)
* [Perguntas frequentes sobre configurações e roaming de dados](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Política de grupo e as configurações do MDM para a sincronização de configurações](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Solução de problemas](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
