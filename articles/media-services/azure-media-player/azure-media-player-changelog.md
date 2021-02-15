---
title: Log de mudanças do Player de Mídia do Azure
description: Log de mudanças do Player de Mídia do Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 09/23/2020
ms.openlocfilehash: 9d126f591da1bffebbfd29119c56927cfbb21dcd
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370569"
---
# <a name="changelog"></a>Log de alteração

## <a name="236-official-update-september-21-2020"></a>2.3.6 (atualização oficial de 21 de setembro de 2020)

### <a name="features-236"></a>Recursos do 2.3.6

Adicionado suporte somente de áudio para a azureHtml5JS tech (DASH) Suporte ao início tardio da transcrição ao vivo Alteração no suporte a idioma na transcrição ao vivo

### <a name="bug-fixes-236"></a>Correções de bugs da versão 2.3.6

Ao usar "playsinline" em reproduções de HLS em dispositivos Apple, clicar no botão "LIVE" faz com que o vídeo seja reiniciado A imagem do pôster de AMP às vezes causa uma exceção O botão Volume estava ausente ao reproduzir as FairPlay do HLS [Acessibilidade] Dicas de ferramentas não definidas para botões quando o teclado é usado [Acessibilidade] A taxa de luminosidade é menor que 1,3:1 para a barra de progresso [Acessibilidade] O foco do teclado às vezes não retorna ao botão de qualidade do vídeo [Acessibilidade] Os controles não estão visíveis na tela Vídeo, impedindo o narrador de encontrá-los

### <a name="changes-236"></a>Alterações na versão 2.3.6

Retornar erros KeyDelivery significativos para chamar aplicativos

## <a name="235-official-update-june-1-2020"></a>2.3.5 (atualização oficial de 1° de junho de 2020)

### <a name="bug-fixes-235"></a>Correções de bugs da versão 2.3.5

- [Acessibilidade] O ouvinte da tecla Esc no painel de Opções está anexado ao documento
- [Acessibilidade] Impedir que a interface do usuário do player desapareça se a barra de controle ou o menu de opções tiver o foco
- A barra de controle mostra o horário incorreto no relógio de parede quando as Configurações de Exibição de Hora do Relógio estão habilitadas

### <a name="changes-235"></a>Alterações na versão 2.3.5

- Adicionada e documentada mensagem de erro para o código de erro 0x00400005

## <a name="234-official-update-march-4-2020"></a>2.3.4 (atualização oficial de 4 de março de 2020)

### <a name="bug-fixes-234"></a>Correções de bugs da versão 2.3.4

- Não é possível definir PlayReady overrideLicenseAcquistionUrl
- Não é possível reproduzir conteúdo com descontinuidades
- [Acessibilidade] O valor do atributo de ID do alerta do leitor de tela deve ser exclusivo
- [Acessibilidade] Ao navegar na caixa de diálogo das configurações de Legenda Oculta, o foco é retirado da caixa de diálogo

### <a name="changes-234"></a>Alterações na versão 2.3.4

- Registrar o tamanho do conteúdo após um download bem-sucedido para ajudar a analisar os erros de descriptografia 2.3.3 (atualização oficial de 12 de novembro de 2019)

### <a name="features-234"></a>Recursos da versão 2.3.4

- Adicionado suporte para exibir o horário no relógio de um vídeo como uma sobreposição e na barra de controle

### <a name="bug-fixes-234"></a>Correções de bugs da versão 2.3.4

- A opção de faixa de áudio funciona, mas gera o erro "O objeto não dá suporte à propriedade nem ao método 'habilitado'" no IE11 e no Windows 7
- A alternância de faixas de áudio falha quando o buffer está totalmente carregado
- A alternância de faixas de áudio falha quando o usuário pausa o vídeo e alterna entre as faixas de áudio muito rapidamente
- [Acessibilidade] Dicas de ferramentas não definidas para o Controle de Vídeo no Player de Vídeo
- Botões de volume ausentes no HTML5 dependendo de quando 'loadstart' é recebido
- [Acessibilidade] Não há como definir o texto Alt para a imagem do pôster
- [Acessibilidade] O foco do aplicativo é perdido após selecionar "Concluído" na caixa de diálogo de configurações de legendas
- [Acessibilidade] Atributos ARIA incorretos são definidos para "vídeo" na "visualização de segmentos"

### <a name="changes-234"></a>Alterações na versão 2.3.4

- Removido o rótulo/faixa da legenda vazia ao reproduzir HLS no iOS e no macOS Safari
- Reduzido do número de 412s para legendas de IMSC1
- Aviso de saída no console para 10 respostas de legenda IMSC1 consecutivas vazias para ajudar na depuração dinâmica

## <a name="232-official-update-october-9-2019"></a>2.3.2 (atualização oficial de 9 de outubro de 2019)

### <a name="features-232"></a>Recursos do 2.3.2

– Adicionado suporte de PlayReady para reprodução de DASH para O navegador Chromium Edge

### <a name="bug-fixes-232"></a>Correções de bugs da versão 2.3.2

- A velocidade de reprodução atual não é mostrada visualmente no menu velocidade de reprodução a menos que o usuário a defina manualmente
- [Acessibilidade] O painel 'Configurações' não é sendo recolhido com o uso da tecla 'Esc'
- [Acessibilidade] A tecla de atalho do AMP 'M' não funciona quando o Narrador está ligado

### <a name="changes-232"></a>Alterações na versão 2.3.2

- Para navegadores que não dão suporte ao codec de áudio E-AC3, as faixas de áudio do E-AC3 ficam ocultas no menu de faixa de áudio
- Para navegadores que dão suporte ao codec de áudio E-AC3, uma faixa de áudio E-AC3 é selecionada por padrão
- Para navegadores que não dão suporte à alternância de codecs de áudio, as faixas de áudio com um codec diferente da faixa selecionada ficam ocultas no menu faixa de áudio

## <a name="231-official-update-august-12-2019"></a>2.3.1 (atualização oficial de 12 de agosto de 2019)

### <a name="features-231"></a>Recursos da versão 2.3.1

- Sinalizar um evento quando caixas de EMSG são recebidas na reprodução de DASH – Adicionado suporte para mostrar as faixas de áudio do EC-3 no menu de áudio em navegadores que dão suporte a EC-3 e permitem alternar a faixa de áudio do AAC para o EC3 e vice-versa, somente no navegador do Microsoft Edge baseado em Chromimum

### <a name="bug-fixes-231"></a>Correções de bugs da versão 2.3.1

- O menu de faixa de áudio é corrompido após a remoção de faixas do EC-3
- A hora atual pode ser maior do que a duração do vídeo
- Definir a velocidade de reprodução via initialSpeed não funciona
- Às vezes, após uma busca, o player parece travado
- No Microsoft Edge e no IE em uma tela touch, depois de ampliar uma página, pressionar ou passar o mouse sobre a barra de pesquisa não obtém o segmento correto do vídeo de maneira precisa
- [Acessibilidade] O rótulo do Aria para Executar/Pausar não é descritivo para o erro do player de vídeo Mapear segmento dinâmico não encontrado para o flashSS para o erro do amp correto
- [Acessibilidade] As funções do Aria usadas para Reproduzir/Pausar devem estar em conformidade com os valores válidos (.vjs-text-track-display)
- [Acessibilidade] Determinadas funções do ARIA devem estar contidas por pais específicos
- [Acessibilidade] Não há nenhuma dica de ferramenta definida para o botão reproduzir/pausar As legendas IMSC1 do player de vídeo pode desaparecer após a busca no buffer de áudio/vídeo atual

### <a name="changes-231"></a>Alterações na versão 2.3.1

- Após obter um segmentDecryptError e o player já estar no Live Edge, o player agora atualiza o manifesto em vez de tentar o próximo segmento
- Adicionado mais registro em log para diagnóstico
- Atualizada a documentação para incluir o suporte ao FairPlay para o iOS Safari
- Adicionado um exemplo para o "srclang" da opção IMSC1
- Adicionados preenchimento, textPadding, substituições de boxShadow para faixas de texto.
- Adicionado um código de erro (0x0020025B) para diferenciar a falha de download devido à falta de Internet, em vez de apenas lançar 0x00200259

## <a name="230-official-release-april-30-2019"></a>2.3.0 (lançamento oficial de 30 de abril de 2019)

### <a name="features-230"></a>Recursos da versão 2.3.0

- Adicionado para legendas do IMSC1 para DASH
- Adicionado suporte para ativos somente de vídeo para DASH
- Adicionada a API presentationTimeOffsetInSec

### <a name="bug-fixes-230"></a>Correções de bugs da versão 2.3.0

- O perfil de heurística LowLatency do AMP interfere nas funções de "ativar mudo" e "desativar mudo" na reprodução de vídeo do iOS em que alguns idiomas têm traduções erradas
- Às vezes, o valor aria-valuenow do controle deslizante da barra de progresso fica incorreto
- O valor da função de aria da exibição de faixa de texto está incorreto

### <a name="changes-230"></a>Alterações na versão 2.3.0

- Agora, os logs incluem o tamanho dos fragmentos de mídia baixados
- Removido o suporte para IE 9 e IE 10
- Atualizado o exemplo do CEA708 para mostrar legendas alinhadas à esquerda
- Incluir MediaError.message em logs para falhas de reprodução

## <a name="224-official-update-february-22-2019"></a>2.2.4 (atualização oficial de 22 de fevereiro de 2019) ##

### <a name="bug-fixes-224"></a>Correções de bug na versão 2.2.4 ###

- [Correção de bug][AMP][Acessibilidade] Remoção de uma guia fantasma acessível quando a tela de erro é exibida
- [Correção de bug][AMP] Correção da tecla de atalho 'M' no IE11 e no Microsoft Edge
- [Correção de bug][AMP] Correção de uma exceção em legendas CEA-708
- [Correção de bug][AMP] Correção de um problema de congelamento de vídeo no navegador Microsoft Edge

### <a name="changes-224"></a>Alterações na versão 2.2.4 ###

- [Alteração][AMP] Quando ocorre um erro de descriptografia de fragmento, o player repete os vários fragmentos atuais para recuperar a reprodução
- [Alteração][AMP] Aprimoramento da tolerância do AMP na sobreposição de fragmentos de vídeo ou áudio

## <a name="223-official-update-january-9-2019"></a>2.2.3 (atualização oficial de 9 de janeiro de 2019) ##

### <a name="features-223"></a>Recursos da versão 2.2.3 ###

- [Recurso][HLS] Adição do menu de faixas de áudio para reprodução do HLS no Safari

### <a name="bug-fixes-223"></a>Correções de bug na versão 2.2.3 ###

- [Correção de bug][AMP][Acessibilidade] Durante as reproduções de difusão ao vivo, o botão "Ao Vivo" não pode ser selecionado com o teclado
- [Correção de bug][AMP] Correção de erros 0x0400003 falsos positivos devido a uma falha no teste do MSE
- [Correção de bug][AMP] Correção de um problema em que o vídeo pode congelar ao iniciar uma transmissão ao vivo

### <a name="changes-223"></a>Alterações na versão 2.2.3 ###

- [Alteração][AMP] Adição de mais informações no log para permitir um melhor diagnóstico
- [Alteração][AMP] Quando mais de uma taxa de bits está disponível na mesma resolução de tela, todas as taxas de bits ficam disponíveis para seleção

## <a name="222-official-update"></a>2.2.2 (atualização oficial) ##

### <a name="bug-fixes-222"></a>Correções de bug na versão 2.2.2 ###

- [Correção de bug][AMP] Quando o player encontra uma interrupção transitória na rede, ele interrompe a reprodução imediatamente
- [Correção de bug][AMP][Acessibilidade] A caixa de diálogo de erro não está acessível pelo teclado
- [Correção de bug][AMP] Exibição de um controle giratório infinito ao reproduzir o ativo somente áudio em vez de um erro sem suporte

### <a name="changes-222"></a>Alterações na versão 2.2.2 ###

- [Alteração][AMP] Adição de cadeias de caracteres localizadas à interface do usuário do anúncio

## <a name="221-official-update"></a>2.2.1 (atualização oficial) ##

### <a name="features-221"></a>Recursos da versão 2.2.1 ###

- [Recurso][CMAF] Adição de suporte ao HLS CMAF

### <a name="bug-fixes"></a>Correções de bugs ###

- [Correção de bug][AMP] Temporizadores desmarcados na lógica de repetição gerando erros de reprodução
- [Correção de bug][AMP][Firefox] O evento encerrado não é disparado no Firefox e no Chrome quando o programa ao vivo é interrompido
- [Correção de bug][AMP] Exibição de controles após SetSource, mesmo quando os controles são definidos como false nas opções do player

### <a name="changes"></a>Alterações ###

- [Alteração][Legenda ao vivo] Alteração do nome da API para legendas CEA de 608 para 708. Para obter mais informações, confira [Configurações de legendas do CEA-708](/javascript/api/azuremediaplayer/cea708captionssettings)-->

## <a name="220-official-release"></a>2.2.0 (versão oficial) ##

### <a name="features-220"></a>Recursos da versão 2.2.0 ###

- [Recurso][Azurehtml5JS][Flash][LiveCaptions] Suporte à legendagem CEA-708 no Azurehtml5JS e na tecnologia FlashSS para conteúdo não criptografado e AES.

### <a name="bug-fixes-220"></a>Correções de bug na versão 2.2.0 ###

- [Correção de bug] A detecção de versão do Flash não funciona no Chrome/no Microsoft Edge

### <a name="changes-220"></a>Alterações na versão 2.2.0 ###

- [Alteração][AMP][Heurística] Alteração do nome do perfil heurístico de QuickStartive para LowLatency
- [Alteração][Flash] Alteração no Flash Player para detecção de versão a fim de permitir a reprodução de conteúdo AES com a nova atualização do Adobe Flash.

## <a name="219-official-hotfix"></a>2.1.9 (hotfix oficial) ##

### <a name="bug-fixes-219"></a>Correções de bug na versão 2.1.9 ###

- [Correção de bug][Ao vivo] Exceção quando as transmissões ao vivo fazem a transição para vídeo por demanda/arquivos ao vivo

### <a name="changes-219"></a>Alterações na versão 2.1.9 ###

- [Alteração][Flash][AES] Modificação da lógica da tecnologia Flash para não usar sharedbytearrays na descriptografia AES, pois o uso delas foi bloqueado pela Adobe no Flash 30 em diante. Observe que a reprodução só funcionará quando a Adobe implantar uma nova versão do Flash, devido a um bug na v30. Confira [Problemas conhecidos](azure-media-player-known-issues.md) para obter mais detalhes

## <a name="218-official-update"></a>2.1.8 (atualização oficial) ##

### <a name="bug-fixes-218"></a>Correções de bug na versão 2.1.8 ###

- [Correção de bug] O controle giratório ocasionalmente não mostra a pós-busca e a pré-reprodução
- [Correção de bug] O player não é iniciado sem som quando a opção Sem Som está habilitada
- [Correção de bug] O controle deslizante de volume é exibido quando os controles são definidos como false
- [Correção de bug] A reprodução é repetida ocasionalmente quando o usuário passa para a borda ao vivo
- [Correção de bug][Firefox] Ocasionalmente, o player gera uma exceção de JavaScript no carregamento
- [Correção de bug][Acessibilidade] O botão Reproduzir/Pausar/Volume perde o contorno do foco quando selecionado com os controles do teclado
- [Correção de bug] A perda de memória fixa no player é descartada
- [Correção de bug] A chamada de src() após erros do player não redefine a origem
- [Correção de bug][Ao vivo] O AMP está no estado de carregamento constante quando o usuário clica no botão Ao Vivo após o término da difusão
- [Correção de bug][Chrome] O player trava e a reprodução falha quando o navegador é minimizado para a tela de fundo.

### <a name="changes-218"></a>Alterações na versão 2.1.8 ###

- [Alteração] O erro 0x0600001 foi atualizado para exibir quando o conteúdo AES for reproduzido com o Flash 30, pois não há suporte para ele no momento. Confira [Problemas conhecidos](azure-media-player-known-issues.md) para obter mais detalhes
- [Alteração] Adição de outras repetições para cenários ao vivo quando o manifesto solicita 404 ou retorna manifestos vazios.

## <a name="217-official-update"></a>2.1.7 (atualização oficial) ##

### <a name="features-217"></a>Recursos da versão 2.1.7 ###

- [Recurso][AzureHtml5JS] Adição da opção de configuração para liberar dados obsoletos no buffer de origem de mídia

### <a name="bug-fixes-217"></a>Correções de bug na versão 2.1.7 ###

- [Correção de bug][Acessibilidade][Leitor de tela] Remoção do cabeçalho em branco do player quando o título não está definido
- [Correção de bug][UWA] O AMP gera uma exceção na reprodução no aplicativo universal do Windows
- [Correção de bug][OSX] setActiveTextTrack() não funciona no Safari no OSx
- [Correção de bug][Ao vivo] Um clique na borda ao vivo após o descarte e a reinicialização do player produz uma exceção
- [Correção de bug][Capa] Tempo atual truncado em alguns ativos
- [Correção de bug][DRM] Correção incluída para dar suporte à reprodução em navegadores que dão suporte a vários CENC DRMs

### <a name="changes-217"></a>Alterações na versão 2.1.7 ###

- [Alteração][Amostras][Acessibilidade] Adição da marca de idioma a todas as amostras

## <a name="216-official-update"></a>2.1.6 (atualização oficial) ##

### <a name="bug-fixes-216"></a>Correções de bug na versão 2.1.6 ###

- [Correção de bug] O AMP exibe a duração incorreta para o ativo específico
- [Correção de bug][FairPlay – HLS] Os erros do FairPlay não são propagados para a interface do usuário
- [Correção de bug] As propriedades heurísticas personalizadas são ignoradas no AMP 2.1.5.

### <a name="changes-216"></a>Alterações na versão 2.1.6 ###

- [Alteração] [FairPlayDRM] Remoção do tempo limite da solicitação de certificado e solicitação de licença para FairPlay a fim de manter a paridade com as implementações do PlayReady e do Widevine
- [Alteração] Aprimoramentos diversos de heurística para combater o conteúdo desfocado

### <a name="features-216"></a>Recursos da versão 2.1.6 ###

- [Recurso] Adição do suporte ao formato mpd-time-cmaf

## <a name="215-official-hotfix"></a>2.1.5 (hotfix oficial) ##

### <a name="bug-fixes-215"></a>Correções de bug na versão 2.1.5 ###

- [Correção de bug][Legendas] Estilo de VTT não renderizado corretamente pelo player
- [Correção de bug][Acessibilidade] O botão Ao Vivo não tem um rótulo Aria

## <a name="214-official-update"></a>2.1.4 (atualização oficial) ##

### <a name="bug-fixes-214"></a>Correções de bug na versão 2.1.4 ###

- [Correção de bug][Acessibilidade][Foco] Os usuários não podem usar a tecla TAB para focalizar os botões personalizados adicionados à direita do botão de tela inteira na barra de controle
- [Correção de bug][IE11][Barra de volume] A tabulação no pop-up de volume faz com que toda a tela de vídeo pisque no IE11 no modo de tela inteira
- [Correção de bug][Capa|Liberação] Um espaço é exibido entre a barra de controle e o pop-up da barra de volume
- [Correção de bug][AMP][Legendas] As faixas inseridas antigas não são limpas quando a origem é alterada em um player existente
- [Correção de bug][Acessibilidade][Narrador] O leitor de tela lê o controle de volume incorretamente
- [Correção de bug][FlashSS] O evento de reprodução ocasionalmente não é acionado pela tecnologia Flash
- [Correção de bug][AMP][Foco] O botão Reproduzir/Pausar exige dois cliques quando o player tem o foco e está no modo de tela inteira
- [Correção de bug][AMP][Capa] Exibição de duração incorreta na barra de progresso de um ativo específico
- [Correção de bug][Anúncios][Anúncio de Butler] O analisador VAST não trata um arquivo VAST que não tem um evento de progresso
- [Correção de bug][SDN][AMP 2.1.1] Correção de problema para o suporte do plug-in de SDN do Hive
- [Correção de bug][Acessibilidade] O narrador lê "botão Sem Som meia-noite" quando o usuário tem o foco no botão de volume

### <a name="changes-214"></a>Alterações na versão 2.1.4 ###

- [Alteração][Acessibilidade][Tecnologia Adaptativa] Agora, os botões têm a propriedade aria-live para aprimorar a experiência com a tecnologia adaptativa
- [Alteração][Acessibilidade][Botão de volume|Narrador] Aprimoramento da acessibilidade do botão de volume modificando a funcionalidade de tabulação e o comportamento do controle deslizante. Essas alterações facilitam a modificação do volume do player pelos usuários de teclado
- [Alteração] Aumento do tempo limite do menu de contexto de inatividade de 3 para 5 segundos
- [Alteração][Acessibilidade][Luminosidade] Aprimoramento do índice de contraste de luminosidade nos menus suspensos nas configurações de legendas

## <a name="213-official-update"></a>2.1.3 (atualização oficial) ##

### <a name="bug-fixes-213"></a>Correções de bug na versão 2.1.3 ###

- [Correção de bug][Plug-ins|Sobreposição de Título] O plug-in Sobreposição de Título gera exceções de JS no AMP v2.X e posterior
- [Correção de bug] O evento SourceSet é enviado ao console do JavaScript mesmo quando o log está desativado
- [Correção de bug][Capa] As dicas de tempo do player são renderizadas fora do contexto do player ao posicionar o cursor na barra de duração final
- [Correção de bug][Acessibilidade][Leitor de tela] O narrador lê "ponto de referência de região" ou "ponto de referência de região do player de vídeo" quando o espectador tem o foco no player
- [Correção de bug][AMP] Não é possível desabilitar o contorno do player por meio de CSS
- [Correção de bug][Acessibilidade] Não é possível usar a tecla TAB para focalizar o player inteiro quando o usuário está no modo de tela inteira
- [Correção de bug][Capa][Ao Vivo] A capa não responde ao texto AO VIVO localizado em japonês
- [Correção de bug][Capa] A duração e a hora atual são cortadas quando o fluxo > 60 min -[Correção de bug][iPhone Ao Vivo] O player mostra o texto da duração/hora atual na barra de controle
- [Correção de bug][AMP] A chamada de APIs de heurística do player produz exceções de JavaScript
- [Correção de bug][HTML5 nativo|iOS] A propriedade de marca de vídeo "playsinline" não é propagada para o player
- [Correção de bug][iOS iframe] O player não poderá entrar em tela inteira no iPhone se for carregado em um iframe
- [Correção de bug][AMP][Heurística] O AMP sempre opera com o perfil híbrido independentemente das opções do player
- [Correção de bug][AMP|Win 8.1] Gera quando hospedado no aplicativo Win 8.1 com um modo de exibição da Web

### <a name="changes-213"></a>Alterações na versão 2.1.3 ###

- [Alteração][AMP] Adição das informações de ponto de extremidade CDN no evento FragmentDownloadComplete
- [Alteração][AMP][Ao Vivo] Aprimoramento e otimização da latência de transmissão ao vivo

## <a name="212-official-hotfix"></a>2.1.2 (hotfix oficial) ##

### <a name="bug-fixes-212"></a>Correções de bug na versão 2.1.2 ####

- [Correção de bug][Acessibilidade][Narrador do Windows] O narrador lê "andamento meia-noite" quando o usuário tem o contexto da barra de progresso e a hora atual é 0h00
- [Correção de bug][Capa] O tamanho do logotipo é embutido no código JavaScript
- [Acessibilidade][Teclas de atalho] As teclas de atalho não são habilitadas quando o player recebe um clique.

### <a name="changes-212"></a>Alterações na versão 2.1.2 ####

- [Alteração][Log] URL do manifesto de log quando o player falhar ao carregar o manifesto

### <a name="features-212"></a>Recursos da versão 2.1.2 ###

- [Alteração][Desempenho][Otimização] Aprimoramento na carga do player e dos tempos de inicialização

## <a name="211-official-update"></a>2.1.1 (atualização oficial) ##

### <a name="bug-fixes-211"></a>Correções de bug na versão 2.1.1 ####

- [Correção de bug][iOS] A definição da reprodução automática como false produz um controle giratório infinito no Safari para iOS
- [Correção de bug] A busca de uma hora maior que a duração do conteúdo produz um controle giratório infinito
- [Correção de bug] As teclas de acesso exigem várias guias do teclado o funcionamento do contexto do player
- [Correção de bug] O vídeo congela por alguns segundos após o redimensionamento do player em alguns ativos
- [Correção de bug] Controle giratório infinito (após a conclusão da busca) quando o usuário faz várias buscas rapidamente
- [Correção de bug] A barra de controle não fica oculta durante a inatividade
- [Correção de bug] A abertura de um aplicativo Web que hospeda o AMP pode fazer com que a página da Web seja carregada duas vezes
- [Correção de bug] Infinito ao reproduzir conteúdo de alguns ativos por meio da tecnologia Flash
- [Correção de bug] O menu Mais Opções não é exibido com plugins de terceiros
- [Correção de bug][Capa|Tubo][Ao Vivo] Dois ícones dinâmicos são exibidos quando o player está na borda ao vivo de um programa
- [Correção de bug][Capa] O logotipo não pode ser desabilitado
- [Correção de bug][Conteúdo de DD+] O controle giratório contínuo é exibido para os ativos que contêm uma faixa de áudio Dolby Digital
- [Correção de bug] O AMP mais recente congela ao alternar faixas de idioma do áudio durante a transmissão ao vivo
- [Correção de bug] Desaparecimento da tela de fundo fixa do controle giratório
- [Correção de bug] Correções de bug do controle giratório infinito em amostras estáticas do token Flash AES

### <a name="changes-211"></a>Alterações na versão 2.1.1 ####

- [Alteração] Adição do código de erro ao requisito de HTTPS do Widevine: no Chrome v58 em diante, o conteúdo do Widevine precisa ser carregado/reproduzido por meio do protocolo `https://`, caso contrário, a reprodução falhará.
- [Alteração] Adição do rótulo Aria para carregar o controle giratório, de modo que a tecnologia adaptativa possa narrar o "carregamento de vídeo" quando o conteúdo estiver sendo carregado  

## <a name="210-official-release"></a>2.1.0 (versão oficial) ##

### <a name="features-210"></a>Recursos da versão 2.1.0 ###

- [Recurso][AzureHtml5JS] Suporte de anúncio do VOD para reversões anteriores, durante e posteriores
- [Recurso][Beta][AzureHtml5JS] Suporte ao anúncio ao vivo para reversões anteriores, durante e posteriores
- [Recurso] Adição de uma nova opção de capa – liberação do AMP
- [Recurso] Adição de rótulos Aria aprimorados para melhor integração aos leitores de tela/à tecnologia adaptativa
- [Recurso][Capa] A capa agora mostra todos os ícones e botões claramente no modo de alto contraste

### <a name="bug-fixes-210"></a>Correções de bug na versão 2.1.0 ###

- [Correção de bug] Várias correções de acessibilidade e interface do usuário
- [Correção de bug] O AMP não é carregando corretamente no IE9

### <a name="changes-210"></a>Alterações na versão 2.1.0 ###

- [Alteração] Reestruturação de elementos DOM no player para acomodar o trabalho de anúncios
- [Alteração] Alternância de CSS para SCSS no desenvolvimento da capa
- [Alteração][Amostras] Adição de amostra para anúncios do VOD
- [Alteração][Amostras] Adição de amostra para velocidade de reprodução
- [Alteração][Amostras] Adição de amostra para a liberação da capa

## <a name="200-beta-release"></a>2.0.0 (versão beta) ##

- [Alteração] Atualização para o VJS5
- [Recurso] Adição de uma nova API de fluido para o fluido da capacidade de resposta do player
- [Recurso] Velocidade de reprodução
- [Alteração] Alternância de CSS para SCSS na capa

## <a name="183-official-hotfix-update"></a>1.8.3 (atualização oficial do hotfix) ##

### <a name="bug-fixes-183"></a>Correções de bug na versão 1.8.3 ###

- [Correção de bug][AzureHtml5JS] Alguns ativos com DTS negativo não são reproduzidos no Chrome

## <a name="182-official-hotfix-update"></a>1.8.2 (atualização oficial do hotfix) ##

### <a name="bug-fixes-182"></a>Correções de bug na versão 1.8.2 ###

- [Correção de bug][AzureHtml5JS] Taxas de bits de áudio maiores não são reproduzidas por meio do AzureHtml5JS

## <a name="181-official-update"></a>1.8.1 (atualização oficial) ##

### <a name="bug-fixes-181"></a>Correções de bug na versão 1.8.1 ###

- [Correção de bug][iOS] As legendas/os subtítulos não são exibidos no player nativo
- [Correção de bug][AMP] As URLs de streaming com suporte da CDN acrescentadas com tokens de autenticação não são reproduzidas
- [Correção de bug][FairPlay] Código de erro do FairPlay com ID de tecnologia ausente (bits [31-28] do ErrorCode); confira Códigos de erro para obter mais detalhes
- [Correção de bug][Safari][PlayReady] O conteúdo do PlayReady no Safari gera um controle giratório infinito

### <a name="changes-181"></a>Alterações na versão 1.8.1 ###

- [Alteração][HTML5] Alteração dos logs detalhados do tecnologia HTML5 para conter eventos da VideoTag

## <a name="180-official-update"></a>1.8.0 (atualização oficial) ##

### <a name="features-180"></a>Recursos da versão 1.8.0 ###

- [Recursos][DRM] Adição de suporte ao FairPlay (confira [Conteúdo protegido](azure-media-player-protected-content.md) para obter mais informações)

### <a name="bug-fixes-180"></a>Correções de bug na versão 1.8.0 ###

- [Correção de bug][AMP] A busca do usuário não dispara um evento de espera quando a rede é limitada
- [Correção de bug][FlashSS] A seleção da qualidade na tecnologia Flash gera uma exceção
- [Correção de bug][AMP] A seleção dinâmica da qualidade é mostrada no menu de contexto
- [Correção de bug][Capa] Dificuldade na seleção do último item de menu dos menus de contexto

### <a name="changes-180"></a>Alterações na versão 1.8.0 ###

- [Alteração] Atualização do player para os requisitos atuais do EME no Chrome
- [Alteração] A techOrder padrão foi alterada para acomodar a nova tecnologia html5FairPlayHLS (confira [Conteúdo protegido](azure-media-player-protected-content.md) para obter mais informações)
- [Alteração][AzureHtml5JS] Habilitação da reprodução de MPEG-Dash no Safari
- [Alteração][Amostras] Alteração das amostras de vários DRM para acomodar o FairPlay

## <a name="174-official-hotfix-update"></a>1.7.4 (atualização oficial do hotfix) ##

### <a name="bug-fixes-174"></a>Correções de bug na versão 1.7.4 ###

- [Correção de bug][Chrome] Um contorno azul aparece em torno do identificador de busca quando o usuário tem o contexto do player
- [Correção de bug][IE9] Uma exceção de JavaScript é gerada quando o player é carregado no IE9

## <a name="173-official-hotfix-update"></a>1.7.3 (atualização oficial do hotfix) ##

### <a name="bug-fixes-173"></a>Correções de bug na versão 1.7.3 ###

- [Correção de bug][AzureHtml5JS] Tempo limite do player em redes restritas

### <a name="changes-173"></a>Alterações na versão 1.7.3 ###

- [Alteração] Habilitação do Webcrypto no Microsoft Edge para descriptografar o conteúdo AES
- [Alteração] Otimização da heurística do AMP para considerar as partes armazenadas em cache
- [Alteração][AzureHtml5JS] Otimização da heurística com a redução da latência de estimativa de largura de banda

## <a name="172-official-hotfix-update"></a>1.7.2 (atualização oficial do hotfix) ##

### <a name="features-172"></a>Recursos da versão 1.7.2 ###
<!---API needs onboarding. Removed link to API until remedied.--->
- [Recurso][AzureHtml5JS|Firefox] Habilitação da reprodução do Widevine com o EME para Firefox 47 e posterior
- [Recurso] Adição de evento para descarte do player
<!-- ([disposing](index.html#static-amp.eventname.disposing)) -->

### <a name="bug-fixes-172"></a>Correções de bug na versão 1.7.2 ###

- [Correção de bug] Os parâmetros codificados de consulta da URL da CDN da Akamai não são decodificados corretamente
- [Correção de bug] Exceção é gerada em manifestPlayableWindowLength()
- [Correção de bug] O espectador não pode sempre clicar em Reproduzir no vídeo após o término dele para assisti-lo novamente
- [Correção de bug] O dimensionamento dinâmico não está em conformidade com as rápidas alterações de tamanho da janela
- [Correção de bug][Microsoft Edge|IE] O dimensionamento dinâmico não tem efeito no carregamento de página para width=x, height=auto
- [Correção de bug][Android|Chrome] O Chrome pede permissões para reproduzir o conteúdo do DRM quando o conteúdo não está criptografado
- [Correção de bug][Acessibilidade][Microsoft Edge] Os controles de teclado não selecionam os itens de menu de contexto corretamente
- [Correção de bug][Acessibilidade] Borda ausente no modo de alto contraste
- [Correção de bug][FlashSS] Ouvinte do evento de liberação do mouse não removido após descarte do player causa uma exceção
- [Correção de bug][FlashSS] Problema ao analisar a URL do manifesto com espaços codificados
- [Correção de bug][iOS] Erro de tipo ao avaliar tech.featuresVolumeControl

### <a name="changes-172"></a>Alterações na versão 1.7.2 ###

- [Alteração][DRM] Movimentação das verificações do DRM após a definição da origem somente para verificar quando o conteúdo está criptografado
- [Alteração][AES] Remoção do corpo indefinido do tipo/sem formatação da solicitação de entrega de chave
- [Alteração][Acessibilidade] O Narrador do Windows agora lê "Player de Mídia" quando o contexto está no player em vez das propriedades

## <a name="171-official-hotfix-update"></a>1.7.1 (atualização oficial do hotfix) ##

### <a name="features-171"></a>Recursos da versão 1.7.1 ###

- [Recurso] Adição de uma opção para o perfil Heurístico Híbrido (esse perfil está definido por padrão)

### <a name="bug-fixes-171"></a>Correções de bug na versão 1.7.1 ###

- [Correção de bug] O design dinâmico não funciona de acordo com o padrão HTML5 (largura = 100%, altura = automática)
- [Correção de bug] Valores percentuais para largura e altura não se comportam como esperado na v1.7.0

## <a name="170-official-update"></a>1.7.0 (atualização oficial) ##

### <a name="features-170"></a>Recursos da versão 1.7.0 ###
<!---API needs onboarding. Removed link until remedied.--->
- [Recurso][AzureHtml5JS][FlashSS] Adição de currentMediaTime() para obter o tempo de mídia do codificador do tempo atual em segundos
- [Recurso][FlashSS] Implementação de APIs de telemetria de download com videoBufferData() e audioBufferData()<!-- (see [BufferData](index.html#amp.bufferdata) for more details) -->
- [Recurso][FlashSS] Adição de evento 'downloadbitratechanged'
- [Recurso] Aprimoramento do tempo de carregamento em comparação com as versões mais antigas do player
- [Recurso] Os erros são registrados em log no console do JavaScript

### <a name="bug-fixes-170"></a>Correções de bug na versão 1.7.0 ###

- [Correção de bug] A URL de cartaz codificada com parâmetros de cadeia de caracteres de consulta não é exibida no player
- [Correção de bug] Exceção é gerada quando nenhuma tecnologia é carregada e a API amp.Player.poster() é chamada
- [Correção de bug] Exceção é gerada quando as funções tentam acessar o player após o descarte
- [Correção de bug][Acessibilidade] Contorno ausente no foco no cabeçalho da busca da barra de progresso
- [Correção de bug][Acessibilidade] Os menus de contexto têm uma sombra no modo de alto contraste
- [Correção de bug][iOS] A reprodução de legendas WebVTT do player nativo não funciona
- [Correção de bug][AzureHtml5JS] O erro 0x0100002 deve ser mostrado ao reproduzir o fluxo HTTP em sites HTTPS que, em vez disso, produz um controle giratório infinito como resultado do conteúdo misto
- [Correção de bug][AzureHtml5JS] Segmento final ausente que causa um erro de verificação de integridade de loop exibindo um estado de buffer infinito percebido
- [Correção de bug][AzureHtml5JS] Nome incorreto da faixa de áudio no menu quando useManifestForLabel=false e códigos de idioma de três letras são usados
- [Correção de bug][AzureHtml5JS|Chrome] Estado de buffer infinito percebido no final do conteúdo causado pela imprecisão do ponto flutuante na duração com o JavaScript no Chrome
- [Correção de bug][FlashSS] Erro intermitente não fatal exibido momentaneamente quando o Flash Player é criado
- [Correção de bug][FlashSS] Falha na reprodução quando os fluxos de vídeo e áudio usam escalas de tempo diferentes devido à falha de imprecisão de arredondamento com "Falha na URL do fragmento (...) ao gerar FLVTags"
- [Correção de bug][FlashSS] Problemas ao analisar as URLs do manifesto com espaços codificados
- [Correção de bug][FlashSS] Verificação ausente para determinar se a versão do Flash Player >= 11.4 que causa um erro na reprodução em vez de recorrer à próxima tecnologia na techOrder
- [Correção de bug][FlashSS][AES] Problemas ao aceitar tokens AES com sublinhados
- [Correção de bug][SilverlightSS|OSX] O "//" como prefixo de um manifesto em vez do protocolo (HTTP ou HTTPS) é reconhecido como um arquivo local, gerando um controle giratório infinito

### <a name="changes-170"></a>Alterações na versão 1.7.0 ###

- [Alteração][FlashSS] Mesclagem dos scripts SWF ("MSAdaptiveStreamingPlugin-osmf2.0.swf" e "StrobeMediaPlayback.2.0.swf") em um só SWF chamado "StrobeMediaPlayback.2.0.swf"
- [Alteração][FlashSS] Atualização da propagação do código de erro para obter códigos de erro mais precisos (por exemplo, os erros 404 agora resultam em 0x30200194 em vez de um erro genérico 0x30200000)

## <a name="163-official-hotfix-update"></a>1.6.3 (atualização oficial do hotfix) ##

### <a name="bug-fixes-163"></a>Correções de bug na versão 1.6.3 ###

- [Correção de bug] Exceção de runtime do JavaScript quando o manipulador de eventos de teclas de atalho é executado após o descarte do player
- [Correção de bug][Android][AzureHtml5JS] Nenhuma reprodução em dispositivos móveis que usam a rede celular
- [Correção de bug] Atualização do Forge para ser executado como um web worker para liberar a interface do usuário

## <a name="162-official-hotfix-update"></a>1.6.2 (atualização oficial do hotfix) ##

### <a name="features-162"></a>Recursos da versão 1.6.2 ###

- [Recurso] Adição de idiomas adicionais para localização (confira a documentação para obter mais detalhes)

### <a name="bug-fixes-162"></a>Correções de bug na versão 1.6.2 ###

- [Correção de bug][IE9-10] Um clique nas áreas em torno do player minimizava a janela do navegador devido ao bug do IE9/IE10 que causa uma minimização em window.blur()
- [Correção de bug][FlashSS] Não aceitação de tokens AES com sublinhados

## <a name="161-official-hotfix-update"></a>1.6.1 (atualização oficial do hotfix) ##

### <a name="bug-fixes-161"></a>Correções de bug na versão 1.6.1 ###

- [Correção de bug][FlashSS|Microsoft Edge, IE][SilverlightSS|IE] Não é possível obter o foco em outros elementos da interface do usuário em entradas ou outros no IE/Microsoft Edge
- [Correção de bug] Falha na reprodução do AES quando o Forge está indefinido
- [Correção de bug][Android][AzureHtml5JS|Chrome] O controle giratório contínuo não reproduz o conteúdo quando está no loop de verificação de integridade
- [Correção de bug][IE9] O não suporte de console.log() no IE 9 causa uma exceção

## <a name="160-official-update"></a>1.6.0 (atualização oficial) ##

### <a name="features-160"></a>Recursos da versão 1.6.0 ###

- [Recurso] Redução de 33% no tamanho de azuremediaplayer.min.js
- [Recurso][AzureHtml5JS|Microsoft Edge][Não testado] Suporte para os fluxos de áudio DD+ no Microsoft Edge (sem alternância de codec após a escolha inicial). O aplicativo precisa selecionar o fluxo de áudio correto neste momento.
- [Recurso] Controles de tecla de atalho (confira a documentação para obter mais detalhes)
- [Recurso] Focalização da dica de tempo de progresso para a busca precisa do tempo
- [Recurso] Permissão da detecção assíncrona de plug-ins se o método setupDone existir no plug-in

### <a name="bug-fixes-160"></a>Correções de bug na versão 1.6.0 ###

- [Correção de bug] O log de memória não é liberado em getMemoryLog(true)
- [Correção de bug] A caixa de seleção de taxa de bits é redefinida na movimentação do mouse causando um problema de seleção de taxas de bits inferiores por meio do controle do mouse
- [Correção de bug] O Mac Office no aplicativo falha ao executar a verificação do DRM
- [Correção de bug] As classes CSS são facilmente substituídas acidentalmente
- [Correção de bug][Chrome] A atualização da identificação do navegador de cadeia de caracteres do agente do usuário é o Microsoft Edge
- [Correção de bug][AzureHtml5JS] O botão Legendas não é exibido na barra de ferramentas do Microsoft Edge (Win10) ou do Chrome (Mac)
- [Correção de bug][Android][AzureHtml5JS|Chrome] Exceção InvalidStateError na chamada endOfStream() em vídeos curtos
- [Correção de bug][Firefox] Remoção do aviso do DRM causado pelo Firefox ao verificar as funcionalidades do navegador
- [Correção de bug][HTML5] Subtítulos/legendas não mostrados com conteúdo MP4 progressivo
- [Correção de bug][FlashSS] Mensagens com carimbos de data/hora correspondentes eram registradas em log na ordem inversa
- [Correção de bug][Acessibilidade][Chrome|Firefox] Os controles da guia e de seleção selecionam automaticamente o primeiro item de menu
- [Correção de bug][Acessibilidade] Guia para controlar o botão de volume

### <a name="changes-160"></a>Alterações na versão 1.6.0 ###

- [Alteração] Uso do tempo de descriptografia AES na seleção do nível de qualidade
- [Alteração] Atualização do rewriter de URL para usar o HLS v4 antes do HLS v3 em fluxos de vários áudios
- [Alteração] Definição de nativeControlsForTouch como false como padrão (ele precisa ser false para funcionar corretamente)

## <a name="150-official-update"></a>1.5.0 (atualização oficial) ##

### <a name="features-150"></a>Recursos da versão 1.5.0 ###

- [Recurso] Aprimoramentos na segurança geral na Web (prevenção contra injeção, XSS etc.)
- [Recurso] Ganchos de integração do plug-in do SDN para o evento sourceset e options.sdn
- [Recurso] Tratamento da robustez de erros 5XX e 4XX durante a reprodução

### <a name="bug-fixes-150"></a>Correções de bug na versão 1.5.0 ###

- [Correção de bug] Atualização da minificação CSS para usar códigos de fonte de entidade HTML em botões em vez de Unicode
- [Correção de bug][AzureHtml5JS] O conteúdo de vários DRMs sempre selecionava o token do primeiro elemento de protectionInfo, causando a falha do segundo DRM
- [Correção de bug][AzureHtml5JS] A busca nunca é concluída em uma área com segmentos ausentes.
- [Correção de bug][AzureHtml5JS|Microsoft Edge] Habilitação do EME prefixado na atualização do Microsoft Edge para reprodução do PlayReady
- [Correção de bug][AzureHtml5JS|Firefox] Atualização da verificação do EME para permitir que o Firefox v42 e posterior (com MSE) faça fallback para o Silverlight no conteúdo protegido
- [Correção de bug][FlashSS] Atualização da mensagem de erro de número para cadeia de caracteres detalhada

### <a name="changes-150"></a>Alterações na versão 1.5.0 ###

- [Alteração] Atualmente, os cartazes só funcionam como URLs absolutas.

## <a name="140-official-update"></a>1.4.0 (atualização oficial) ##

### <a name="features-140"></a>Recursos da versão 1.4.0 ###

- [Recurso][AzureHtml5JS|Chrome] Suporte simples ao DRM no Widevine
- [Recurso][AzureHtml5JS] Tratamento da robustez de erros 404/412 durante a reprodução

### <a name="bug-fixes-140"></a>Correções de bug na versão 1.4.0 ###

- [Correção de bug][FlashSS] Aprimoramento da validação de parâmetro

## <a name="130-official-update"></a>1.3.0 (atualização oficial) ##

### <a name="features-130"></a>Recursos da versão 1.3.0 ###

- [Recurso][AzureHtml5JS][FlashSS] Troca de áudio do mesmo conteúdo de vários áudios do codec

### <a name="bug-fixes-130"></a>Correções de bug na versão 1.3.0 ###

- [Correção de bug][AzureHtml5JS|Chrome] Controle giratório infinito intermitente
- [Correção de bug][AzureHtml5JS|IE][Windows Phone] Exceção que causa problemas de reprodução no Windows Phone
- [Correção de bug][FlashSS] A reprodução automática definida como false falha em instâncias adicionais
- [Correção de bug] Problemas de dimensionamento de menu da interface do usuário

## <a name="120-official-update"></a>1.2.0 (atualização oficial) ##

### <a name="features-120"></a>Recursos da versão 1.2.0 ###

- [Recurso][AzureHtml5JS|Firefox] Suporte quando o MSE está habilitado
- [Recurso] Remoção da necessidade de que o aplicativo forneça caminhos para binários da tecnologia de fallback (swf, xap). O caminho é relativo ao script do Player de Mídia do Azure.

### <a name="bug-fixes-120"></a>Correções de bug na versão 1.2.0 ###

- [Correção de bug][AzureHtml5JS|Chrome] Descompassos do player por trás da borda ao vivo quando o player está em segundo plano
- [Correção de bug][AzureHtml5JS|Microsoft Edge] A tela inteira não funciona
- [Correção de bug][AzureHtml5JS] O log não era habilitado corretamente quando definido nas opções
- [Correção de bug][Flash] Exibição de "Armazenando em buffer" e o ícone de buffer durante o evento de espera
- [Correção de bug] Permissão da continuação da reprodução se a solicitação de largura de banda inicial falhar
- [Correção de bug] Falha de carregamento do player quando inicializado com opções indefinidas
- [Correção de bug] Ao tentar descartar o player depois que ele já foi descartado, uma exceção de vdata ocorre
- [Correção de bug] Ícones da barra de qualidade mapeados incorretamente

## <a name="111-official-hotfix-update"></a>1.1.1 (atualização oficial do hotfix) ##

### <a name="bug-fixes-111"></a>Correções de bug na versão 1.1.1 ###

- [Correção de bug] Problema mais antigo de tela inteira do IE
- [Correção de bug] Os plug-ins não são mais substituídos

## <a name="110-official-update"></a>1.1.0 (atualização oficial) ##

### <a name="features-110"></a>Recursos da versão 1.1.0 ###

- [Recurso] Atualização de cadeias de localização da interface do usuário

### <a name="bug-fixes-110"></a>Correções de bug na versão 1.1.0 ###

- [Correção de bug] O botão grande Reproduzir não tem contraste suficiente
- [Correção de bug] Indicador visual de foco da guia
- [Correção de bug] O menu Selecionar Taxa de Bits agora usa informações de resolução corretas
- [Correção de bug] O menu Mais opções agora é dimensionado dinamicamente
- [Correção de bug] Vários problemas de interface do usuário

## <a name="100-official-release"></a>1.0.0 (versão oficial) ##

### <a name="features-100"></a>Recursos da versão 1.0.0 ###

- [Recurso] Teste de acessibilidade básico para controle guia, controle de foco, leitor de tela e interface do usuário de alto contraste
- [Recurso] Atualização da interface do usuário
- [Recurso] Log do desenvolvedor
- [Recurso] API para configurar dinamicamente as faixas de legendas/subtítulos
- [Recurso] Recursos básicos de localização
- [Recurso] Consolidação de códigos de erro entre tecnologias
- [Recurso] Novo código de erro para quando os plug-ins (como Flash ou Silverlight) não estão instalados
- [Recurso][AzureHtml5JS] Implementação dos eventos de diagnóstico básicos

### <a name="bug-fixes-100"></a>Correções de bug na versão 1.0.0 ###
<!---What is that actually supposed to say?--->
- [Correção de bug][AzureHtml5JS] A reprodução ao vivo congela nas atualizações do MPD quando há pequenas imprecisões no carimbo de data/hora
- [Correção de bug][AzureHtml5JS] Mitigação de vários problemas de reprodução ao vivo
- [Correção de bug][AzureHtml5JS] A liberação é armazenada em buffer quando a heurística do tamanho da janela está ativada e é colocada em uma tela de resolução mais alta
- [Correção de bug][AzureHtml5JS] O Chrome agora mostra o evento encerrado corretamente. Vinculado ao problema conhecido anterior *Chrome não envia corretamente o evento finalizado quando AzureHtml5JS é usado. Há um problema no navegador subjacente.*
- [Correção de bug][AzureHtml5JS] Safari desabilitado para esta tecnologia para resolver o *Problema de reprodução no OSX Yosemite com a tecnologia AzureHtml5JS. Há problemas de implementação do MSE. Mitigação temporária: forçar flashSS, silverlightSS como ordem de tecnologia para esses agentes de usuário*
- [Correção de bug][FlashSS] Acionamento de início da carga após um erro

## <a name="020-beta"></a>0.2.0 (beta) ##

### <a name="features-020"></a>Recursos da versão 0.2.0 ###

- [Recurso] Conclusão do teste no PlayReady e no AES para sob demanda e ao vivo – confira a matriz de compatibilidade
- [Recurso] Tratamento de descontinuidades
- [Recurso] Suporte para carimbos de data/hora superiores a 2^53
- [Recurso] O parâmetro de consulta de URL persiste à solicitação de manifesto
- [Recurso][Não testado] Suporte para os perfis de heurística `QuickStart` e `HighQuality`
- [Recurso][Não testado] Exposição de informações de fluxo de vídeo para taxas de bits, largura e altura no AzureHtml5JS e no FlashSS
- [Recurso][Não testado] Seleção da taxa de bits no AzureHtml5JS e no FlashSS (confira a documentação da API)

### <a name="bug-fixes-020"></a>Correções de bug na versão 0.2.0 ###

- [Correção de bug] O botão grande Reproduzir agora é visível no WP 8.1
- [Correção de bug] Correção de vários problemas de reprodução ao vivo
- [Correção de bug] O botão desativar mudo agora funciona na interface do usuário
- [Correção de bug] Atualização da experiência de carregamento da interface do usuário atualizada para o modo de reprodução automática
- [Correção de bug] Problema do carregador AMD e definição de conflitos de método
- [Correção de bug] Problema de carregamento do aplicativo Cordova no WP 8.1
- [Correção de bug] O conteúdo protegido consulta o ProtectionType compatível com a plataforma/a tecnologia para selecionar a tecnologia apropriada para reprodução.  Corrige o problema conhecido anterior ' _Atualmente, o conteúdo do PlayReady no Chrome (desktop)/Safari 8 (no OSX Yosemite) não faz fallback para o player do Silverlight_ '
- [Correção de bug] Exceção não capturada no WinServer 2012 R2 devido ao Media Foundation não estar instalado neste computador por padrão.  Tentativa de usar APIs de marca de vídeo HTML, que não são implementadas, gerando assim um erro. A mitigação atual é capturar esse erro e retornar false em vez de gerar o erro.
- [Correção de bug] O segmento inicial sempre e obtido após a busca ou uma falha de HTTP para evitar falhas durante a reprodução
- [Correção de bug] Desativação do acompanhamento do progresso simulado e das atualizações de tempo quando o erro ocorria.
- [Correção de bug] Remoção do menu de atalho
- [Correção de bug][AzureHtml5JS] A mensagem de erro não é exibida quando há um token inválido definido para o conteúdo do PlayReady
- [Correção de bug][AzureHtml5JS] A escolha da tela inteira durante a reprodução ao vivo não levava em conta a heurística do tamanho da janela
- [Correção de bug][FlashSS] Remoção das mensagens exibidas do Strobe Media Player para que apenas as mensagens do Player de Mídia do Azure sejam mostradas
- [Correção de bug][SilverlightSS] O evento buscado não é obtido na busca além da duração ou em uma duração inferior a 0

## <a name="010-beta-release"></a>0.1.0 (versão beta) ##

Pré-lançamento inicial

## <a name="next-steps"></a>Próximas etapas ##

- [Início rápido do Player de Mídia do Azure](azure-media-player-quickstart.md)
