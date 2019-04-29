---
title: Referência de domínio predefinido
titleSuffix: Azure
description: Referência para os domínios predefinidos, que são conjuntos predefinidos de tentativas e entidades de idioma Noções básicas sobre inteligente serviços (LUÍS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: e1e579233a5ad1af1ef8ee84019cd995959d3b2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712594"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Referência de domínio predefinida para seu aplicativo LUIS
Esta referência contém informações sobre o [domínios pré-compilada](luis-how-to-use-prebuilt-domains.md), que são conjuntos predefinidos de entidades que oferece LUÍS e tentativas.

[Domínios personalizados](luis-how-to-start-new-app.md), por outro lado, iniciar sem tentativas e modelos. Você pode adicionar quaisquer tentativas de domínio pré-compilada e entidades a um modelo personalizado.

## <a name="list-of-prebuilt-domains"></a>Lista de domínios predefinidos
LUIS oferece 20 domínios predefinidos. 

| Domínio predefinido | DESCRIÇÃO | Idiomas com suporte |
| ---------------- |-----------------------|:------:|
| Calendário | O domínio de calendário fornece a intenção e entidades para adicionar, excluir, ou editar um compromisso, verificando a disponibilidade de participantes e localizar informações sobre um evento de calendário.| en-US<br/> zh-CN |
| Câmera | O domínio de câmera fornece entidades e tentativas para tirar fotos, vídeos de gravação e transmitindo vídeo para um aplicativo.| en-US |
| Comunicação | Envio de mensagens e chamadas telefônicas.| en-US <br/> zh-CN |
| Entretenimento  | Tratamento de consultas relacionadas a música, filmes e TV.| en-US |
| Eventos | Reserva de tíquetes de concertos, festivais, esportes e comédia mostra.| en-US |
| Adequação | Tratar as solicitações relacionadas ao controle de atividades de adequação.| en-US |
| Jogos | Tratar as solicitações relacionadas a uma parte de jogo em um jogo.| en-US |
| HomeAutomation | Controlando dispositivos inteligentes de base como luzes e dispositivos.| en-US<br/> zh-CN |
| MovieTickets | Permissões para filmes em um cinema de reserva.| en-US |
| Música | Reproduzir música em um player de música.| en-US<br/> zh-CN |
| Observação | O domínio de Observação fornece entidades relacionadas à criação, edição e localizar anotações e tentativas.| en-US<br/> zh-CN |
| OnDevice | O domínio OnDevice fornece tentativas e entidades relacionadas ao controle do dispositivo.| en-US<br/> zh-CN |
| Locais  | Manipulação de consultas relacionadas aos locais como as empresas, instituições, restaurantes, espaços públicos e endereços.| en-US<br/> zh-CN |
| Reminder | Tratar as solicitações relacionadas à criação, edição e localizar lembretes.| en-US<br/> zh-CN |
| RestaurantReservation | Tratamento de solicitações para gerenciar reservas restaurante.| en-US<br/> zh-CN |
| táxi | Reservas de tratamento para um táxi.| en-US<br/> zh-CN |
| Translate | Convertendo texto para um idioma de destino.| en-US<br/> zh-CN |
| TV | Controlando TVs.| en-US |
| Utilidades  | Solicitações de manipulação que são comuns em vários domínios, como "Ajuda", "Repetir", "recomeçar".| en-US |
| Clima | Obtendo boletins meteorológicos e previsões.| en-US<br/> zh-CN |
| Web | Navegar para um site.| en-US<br/> zh-CN |

Para obter mais detalhes sobre cada domínio, consulte as seções a seguir.

## <a name="calendar"></a>Calendário 

O domínio de calendário fornece tentativas e entidades relacionadas a entradas do calendário. As tentativas de calendário incluem adicionar, excluir ou editar um compromisso, verificando a disponibilidade e localizar informações sobre uma entrada do calendário ou um compromisso.

### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Adicionar | Adicione um novo item de uso único para o calendário.| Faça um compromisso com Lisa às 14: 00 no domingo <br/><br/>Desejo agendar uma reunião<br/><br/>Preciso configurar uma reunião|
| CheckAvailability | Localize a disponibilidade de um compromisso ou reunião no calendário do usuário ou outra pessoa.| Quando é Jim disponível para atender a? <br/><br/>Mostrar quando Carol amanhã está disponível<br/><br/>Carlos é gratuito no sábado?|
| Excluir | Solicitação para excluir uma entrada do calendário.| Cancele meu compromisso com a Carol. <br/><br/>Excluir minha reunião de 9:00<br/>|
| Editar | Solicitação para alterar uma reunião existente ou a entrada do calendário.| Mova minha reunião de 9: 00 às 10 horas.<br/><br/>Desejo atualizar minha agenda.<br/><br/>Reagende a reunião com Ryan.|
| Localizar | Exiba meu calendário semanal.| Encontre a consulta de revisão do dentista. <br/><br/>Mostrar meu calendário<br/>|

### <a name="entities"></a>Entidades
| Nome da entidade | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Local padrão | Local do item de calendário, reunião ou compromisso. Endereços, cidades e regiões são bons exemplos de locais.| Academia 209 Nashville <br/><br/>897 casa de panqueca<br/><br/>Garagem|
| Subject | O título de uma reunião ou compromisso.| Consulta odontológica <br/><br/>Almoçar com Julia<br/><br/>Consulta médica|

## <a name="camera"></a>Câmera 
O domínio da câmera fornece intenções e entidades relacionadas ao uso de uma câmera. As intenções cobrem a captura de uma foto, selfie, captura de tela ou vídeo e transmissão de vídeo para um aplicativo.

### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| CapturePhoto| Capture uma foto.| Tirar uma foto<br/><br/>Captura|
| CaptureScreenshot | Fazer uma captura de tela.| Tirar instantâneo.<br/><br/>capturar a tela.|
| CaptureSelfie | Capture uma selfie.| Tirar uma selfie <br/><br/>tirar uma foto minha |
| CaptureVideo | Inicie a gravação de vídeo.| Iniciar gravação <br/><br/>Iniciar a gravação|
| StartBroadcasting| Inicie o vídeo de difusão.| Iniciar a transmissão para Facebook|
| StopBroadcasting| Pare o vídeo de difusão.| Parar a difusão|
| StopVideoRecording| Pare a gravação de um vídeo.| Isso é o suficiente<br/><br/>Parar a gravação|

### <a name="entities"></a>Entidades
| Nome da entidade | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| AppName | O nome de um aplicativo para transmissão de vídeo.| OneNote<br/><br/>Facebook<br/><br/>Skype|


## <a name="communication"></a>Comunicação 
O domínio de comunicação fornece tentativas e entidades relacionadas a email, mensagens e chamadas telefônicas.

### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| AddContact| Adicione um novo contato à lista de contatos do usuário.|Adicionar novo contato <br/><br/>Salve esse número e coloque-o como Carol|
| Adicionar mais| Adicione mais a um email ou texto, como parte de uma composição de texto ou email passo a passo.|Adicionar mais texto <br/><br/>Adicionar mais para o corpo do email|
| Resposta| Atenda uma chamada telefônica recebida.|Responder à chamada <br/><br/>Abrir|
| AssignContactNickname| Atribua um apelido para um contato.|Alterar Isaac para pai <br/>Editar o apelido do Jim<br/>Adicionar apelido para Patti Owens|
| CallVoiceMail| Conecte-se a mensagem de voz do usuário.|Conectar-me a minha caixa de correio de voz <br/>Mensagem de voz<br/>Chamada de correio de voz|
| CheckIMStatus| Verifique o status de um contato do Skype.|O status online do Jim está definido como ausente? <br/>Carol está disponível para conversar?|
| Confirmar| Confirme uma ação.|Sim<br/>Okey<br/>Está bem<br/>Confirmo que desejo enviar este email.<br/>|
| Discagem| Fazer uma chamada telefônica.|Chamar Jim<br/>Por favor, disque 311<br/>|
| FindContact| Localize informações de contato por nome.|Localizar o número de Carol<br/>Mostre-me o número de Carol<br/>|
| FindSpeedDial| Localize o número de discagem de velocidade que um número de telefone está definido como e vice-versa.|Qual é o meu número de discagem 5?<br/>É necessário velocidade discar conjunto?<br/>O que é o número de discagem para 941-5555-333?|
| GetForwardingsStatus| Obter o status atual de encaminhamento de chamada.|O encaminhamento de chamada está ativado?<br/>Quero saber se meu status de chamada está ativada ou desativada<br/>|
| GoBack| Volte para a etapa anterior.|Voltar ao twitter<br/>Voltar uma etapa<br/>Voltar|
| Ignorar| Ignore uma chamada recebida.|Não responder<br/>Ignorar chamada|
| IgnoreWithMessage| Ignorar uma chamada de entrada e responder com texto em vez disso.|Não responder a chamada, mas enviar uma mensagem em vez disso.<br/>Ignorar e enviar um texto de volta.|
| PressKey| Pressione um botão ou um número no teclado.|Estrela de discagem.<br/>Pressione 1 2 3.|
| ReadAloud| Ler um email ou mensagem para o usuário.|Leia o texto.<br/>O que ela disse na mensagem?|
| TurnForwardingOff| Fazer uma chamada telefônica.|<br/><br/>|
| Disque novamente| Disque novamente ou ligue para um número novamente.|Disque novamente.<br/>Disque novamente minha última chamada.|
| Rejeitar| Rejeite uma chamada de entrada.|Rejeitar chamada<br/>Não é possível responder agora<br/>Não disponível no momento e chamará novamente mais tarde.|
| SendEmail| Envie um email. Essa tentativa se aplica ao email, mas não as mensagens de texto.|Email para Mike Waters: Mike, o jantar semana passada foi excelente.<br/>Enviar um email para Bob<br/>|
| SendMessage| Envie uma mensagem de texto ou uma mensagem instantânea.|Enviar texto para Chris e Carol|
| SetSpeedDial| Defina um atalho de discagem de velocidade para o número de telefone do contato.|Conjunto de discagem um para Carol.<br/>Configure discagem do mom.|
| ShowNext| Consulte o próximo item, por exemplo, uma lista de mensagens de texto ou emails.|Mostre o outro.<br/>Vá para a próxima página.|
| ShowPrevious| Consulte o item anterior, por exemplo, uma lista de mensagens de texto ou emails.|Mostre anterior.<br/>Voltar<br/>Ir para o anterior.|
| StartOver| Iniciar o sistema novamente ou iniciar uma nova sessão.|Começar novamente<br/>Nova sessão<br/>restart|
| TurnForwardingOff| Desative o encaminhamento de chamada.|Interromper o encaminhamento Minhas chamadas<br/>Desativar o encaminhamento de chamada|
| TurnForwardingOn| Desligue o viva-voz.|Meu chamadas para 3333 de encaminhamento<br/>Ativar o encaminhamento de chamada para 3333|
| TurnSpeakerOff| Desligue o viva-voz.|Tire-me do alto-falante.<br/>Desative viva-voz.<br/>|
| TurnSpeakerOn| Ativar o viva-voz.|Modo de viva-voz.<br/>Coloque viva-voz em.<br/>|

### <a name="entities"></a>Entidades
| Nome da entidade | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| AudioDeviceType | Tipo de dispositivo de áudio (locutor ouvido, microfone, etc).| Palestrante<br/>Viva-voz<br/>Bluetooth|
| Category | A categoria de um email ou mensagem.| Importante<br/>Prioridade alta|
| ContactAttribute | Um atributo de entre em contato com que o usuário solicita informações sobre.| Aniversários<br/>Endereço<br/>Número de telefone|
| ContactName | O nome de um destinatário de mensagem ou contato.| Carol<br/>Jim<br/>Chris|
| EmailSubject | O texto usado como a linha de assunto de um email.| RE: história interessante|
| Linha | A linha que o usuário deseja usar para fazer uma chamada ou enviar um email/de texto de.| Linha de trabalho<br/>Célula Britânica<br/>Skype|
| Mensagem | A mensagem a enviar um email ou texto.| Era grande reunião hoje. Vejo você em breve!|
| messageType | O nome de um destinatário de mensagem ou contato.| Text<br/>Email|
| OrderReference | A posição ordinal ou relativa em uma lista, identificando um item a ser recuperado. Por exemplo, "last" ou "recent" em "Qual foi a última mensagem que enviei?"| Último<br/>Recente|
| SenderName | O nome do remetente.| Patti Owens|

## <a name="entertainment"></a>Entretenimento  
Mostra o domínio fornece entidades relacionadas à pesquisa de filmes, músicas, jogos e TV e tentativas de entretenimento.

### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Search| A pesquisa de filmes, música, aplicativos, jogos e TV mostra.|Procure Halo na loja.<br/>Procure o Avatar.|

### <a name="entities"></a>Entidades
| Nome de entidades | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| ContentRating | Classificação de conteúdo de mídia como G ou R para filmes.|Vídeo de crianças.<br/>PG classificado.|
| Gênero | Gênero de filme, jogos, aplicativos ou música.|Comédias<br/>Dramas<br/>Cômicos|
| Palavra-chave| Uma palavra-chave de pesquisa genérica especificando um atributo de não existe nos slots de mídia mais específicos.|Trilhas sonoras<br/>Rio de lua<br/>Amelia Earhart|
| Linguagem | Linguagem usada na mídia, como a linguagem falada de filmes ou músicas.|Francês<br/>Inglês<br/>Coreano|
| MediaFormat | O tipo técnico especial adicional em que a mídia é formatada.|Vídeos de alta definição<br/>Filmes 3D<br/>Para download|
| MediaSource | O repositório ou marketplace para adquirir a mídia.|Netflix<br/>Prime|
| MediaSubTypes| Tipos de mídia menores do que filmes e jogos.|Demonstrações<br/>DLC<br/>Trailers|
| Nacionalidade| O país em que foi criada um filme, mostrar ou música.|Francês<br/>Alemão<br/>Coreano|
| Pessoa| O ator, diretor, produtor, músico ou artista associado a um filme, um aplicativo, jogos ou programa de TV.|Madonna<br/>Stanley Kubrick|
| Função| Função desempenhada por uma pessoa na criação de mídia.|Faz grandes<br/>Direcionado por<br/>Por|
| Title| O nome de um filme, um aplicativo, jogo, programa de TV ou música.|Friends<br/>Minecraft|
| Type| O formato de tipo ou a mídia de um filme, um aplicativo, jogo, programa de TV ou música.|Música<br/>MovieTV <br/>programas|
| UserRating| Estrela de usuário ou de classificação de miniaturas.|5 estrelas<br/>3 estrelas<br/>4 estrelas|

## <a name="events"></a>Eventos 
O domínio de eventos fornece tentativas e entidades relacionadas ao reservar passagens para eventos como concertos, festivais, esportes e comédia mostra.

### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Livro| Permissões para um evento de compra.|Gostaria de comprar um tíquete para o symphony esta semana.|


### <a name="entities"></a>Entidades
| Nome de entidades | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Endereço | Local do evento ou o endereço. |Palo Alto<br/>300 112th Ave SE <br/> Seattle |
| NOME | O nome de um evento.|Shakespeare no parque|
| Colocar nome| O nome do evento local.|Louvre<br/>Casa de ópera<br/>Broadway|
| PlaceType | O tipo do local em que o evento será realizado.|Cafe<br/>Teatro<br/>Biblioteca|
| Type | O tipo de um evento.|Concerto<br/>Jogo de esportes|

## <a name="fitness"></a>Adequação 
O domínio de adequação fornece entidades relacionadas ao controle de atividades de adequação e tentativas. As tentativas de incluem salvando-as, o restante do tempo ou distância ou salvar os resultados da atividade.

### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| AddNote| Adiciona anotações complementares a uma atividade controlada.|A dificuldade dessa execução foi 6/10<br/>O terreno em que estou correndo é asfalto<br/>Estou usando uma bicicleta 3 velocidade|
|GetRemaining| Obtém o tempo restante ou a distância de uma atividade.|Quanto tempo até a próxima volta?<br/>Quantos milhas permanecem em meu execução? Quanto tempo para a separação?|
| LogActivity| Salve ou registre os resultados da atividade concluída.|Salve minha última corrida<br/>Log minha caminhada matinal de sábado<br/>guardar o meu mergulho anterior|
| LogWeight| Salvar ou registrar o peso atual do usuário.|Salve meu peso atual<br/>registre meu peso agora<br/>armazenar meu peso corporal atual|

### <a name="entities"></a>Entidades
| Nome de entidades | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| activityType | O tipo de atividade para rastrear. |Executar<br/>Percorrer<br/>Nadar<br/>Ciclo |
| Alimentos | Um tipo de alimentos para controlar em um aplicativo de adequação. |Banana<br/>Salmão<br/>Shake proteico|
| Tipo de Refeição| O tipo de refeição para acompanhar em um aplicativo de saúde ou fitness.|Café da manhã<br/>Refeição<br/>Almoçar<br/>Supper|
| Medida| Um tipo de medidas de tempo, distância ou peso, para uso em um aplicativo de fitness ou saúde.|quilômetros<br/>milhas<br/>minutos<br/>kg|
| Número | Uma quantidade numérica para uso em um aplicativo de fitness ou saúde.|19<br/>três<br/>200<br/>one|
| StatType | Um tipo de estatística em dados agregados, para uso em um aplicativo de fitness ou saúde.|Soma<br/>Média<br/>Máximo<br/>Mínimo|

## <a name="gaming"></a>Jogos 
O domínio de jogos fornece entidades relacionadas ao gerenciamento de uma parte de jogo em um jogo e tentativas.

### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| InviteParty| Convide um contato para ingressar em uma parte de jogos.|Convidar este player para minha empresa<br/>Venha a minha empresa<br/>Unir meu proveito|
|LeaveParty| Obtém o tempo restante ou a distância de uma atividade.|Estou fora<br/>Estou deixando essa parte para outra<br/>Estou desistindo|
| StartParty| Inicie um parceiro comercial em um jogo.|Cara vamos começar uma entidade<br/>Comece uma entidade<br/>deve iniciar um proveito hoje|

### <a name="entities"></a>Entidades
| Nome da entidade | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Contato| Um nome de contato para usar em um jogo.|Carol<br/>Jim|


## <a name="homeautomation"></a>HomeAutomation 
O domínio HomeAutomation fornece tentativas e entidades relacionadas ao controlar dispositivos inteligentes de base como luzes e dispositivos.

### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Desativação| Desativar, fechar ou desbloquear um dispositivo.|Desligue o firewall<br/>Parar a máquina de café<br/>Fechar a porta da garagem|
|Ativação| Ativar um dispositivo ou defina o dispositivo como uma configuração específica ou um modo.|ligar minha cafeteira<br/>você pode ligar minha cafeteira?<br/>Defina o termostato a 72 graus.|


### <a name="entities"></a>Entidades
| Nome da entidade | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Dispositivo | Um tipo de dispositivo que pode ser ativado ou desativado.|Cafeteira<br/>termostato<br/>Luzes|
| Operação | O estado do dispositivo.|lock<br/>Abrir<br/>em<br/>Desligar|
| Sala | O local ou a sala em que o dispositivo está.|Sala de estar<br/>quarto<br/>cozinha|

## <a name="movietickets"></a>MovieTickets 
O domínio MovieTickets fornece entidades relacionadas a tíquetes de reserva para filmes em um cinema e tentativas.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Reserve dois ingressos para Os Três Mosqueteiros|
|Cancelar ingressos|
|Quando o capitão Omar está aparecendo?|

### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Livro | Comprar tíquetes do filme.|Reserve-me dois ingressos para o capitão Omar e os dois mosqueteiros<br/>Eu quero comprar um ingresso para o filme de amanhã<br/>Eu quero um tíquete para Captain Omar parte 2 próxima quarta-feira|
|GetShowTime| Obter o showtime de um filme.|Quando o capitão Omar está aparecendo?|


### <a name="entities"></a>Entidades
| Nome da entidade | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Endereço | O endereço de um cinema.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| MovieTitle | O título de um filme.|Vida de pi<br/>Jogos Vorazes<br/>Começo|
| Colocar nome | O nome de uma sala de cinema ou cinema.|Cinema Amir<br/>Teatro de Alexandria<br/>Teatro de Nova York|
| PlaceType | O tipo de localização em que um filme está sendo exibido.|cinema<br/>teatro<br/>Cinema IMAX|

## <a name="music"></a>Música 
O domínio Música fornece intenções e entidades relacionadas à reprodução de música em um player de música.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|reproduzir Beethoven|
|Aumentar o volume da faixa|
|Vá para a próxima música|

### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| DecreaseVolume | Diminua o volume do dispositivo.|diminuir o volume da faixa<br/>volume baixo|
| IncreaseVolume | Aumente o volume do dispositivo.|aumentar o volume da faixa<br/>aumentar volume|
| Mudo |Silencie a música tocando.|Ativar mudo<br/>Coloque a faixa no mudo<br/>Música muda |
| Pausar | Pause a reprodução da música.|Pausar<br/>Pausar música<br/>Pausar faixa|
| PlayMusic | Reproduza música em um dispositivo.|reproduzir Kevin Durant<br/>reproduzir Paradise de Coldplay<br/>reproduzir a saudação de Adele|
| Repetir |Repita a música em execução.|Repita música<br/>Executar o ganho de faixa<br/>Repita música|
| Continuar | Continue a reproduzir música.|Retomar música<br/>Iniciar música novamente<br/>Retomar|
| SkipBack | Pule para trás uma faixa.|Vá para a próxima música<br/>Executar a próxima música|
| SkipForward |Pule uma faixa.|Reproduzir música anterior<br/>Volte para a etapa anterior |
| Stop | Pare uma ação relacionadas a reprodução de música. |Pare a execução desse álbum.|
| Ativar mudo | Desativar o som de um dispositivo de reprodução de música.| Desativar mudo.|

### <a name="entities"></a>Entidades
| Nome da entidade | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| ArtistName | O ator, diretor, produtor, gravador, músico ou artista associado a mídia para executar em um dispositivo.|Elvis Presley<br/>Taylor Swift<br/>Adele<br/>Mozart|
| Gênero | Gênero da música que está sendo solicitado.|Música de país<br/>Clássicos Broadway<br/>Tocar minha música clássica do período barroco|

## <a name="note"></a>Observação 
O domínio de Observação fornece entidades relacionadas à criação, edição e localizar anotações e tentativas.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Adicionar aos meus mantimentos nota alface tomate pão café|
|Marcar bananas da minha lista de compras|
|Remover todos os itens da minha lista de férias|

### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| AddToNote | Adicione informações a uma anotação.|Adicionar aos meus mantimentos nota alface tomate pão café<br/>Adicionar à lista de tarefas<br/>Adicionar Deliciosos ao meu Wunderlist|
| CheckOffItem | Marque os itens de uma anotação já existente.|Marcar bananas da minha lista de compras<br/>Marcar cheese cake na minha lista de compras de férias como feito|
| Limpar | Limpar todos os itens de uma nota pré-existente.|Remover todos os itens da minha lista de férias<br/>Limpar tudo da minha lista de leitura|
| Confirmar | Confirme uma ação relacionada a uma nota.|Está okey para mim<br/>Sim<br/>Estou confirmando a manter todos os itens na listas|
| Criar | Crie um novo bloco de anotações. | Cria uma lista<br/>Observação para lembrar-me que Jason é da cidade primeira semana de maio|
| Excluir | Exclua toda a anotação. |Excluir minha lista de férias <br/>excluir minha nota de compra|
| DeleteNoteItem | Deletar itens de uma nota pré-existente.| Excluir chips da minha lista de compras<br/>Remover canetas da minha lista de compras da escola|
| ReadAloud | Leia uma lista em voz alta.|Leia-me primeiro<br/>Leia-me em detalhes|
| ShowNext | Consulte o próximo item em uma lista de anotações.|Mostre o outro<br/>Próxima página<br/>Avançar|

### <a name="entities"></a>Entidades
| Nome da entidade | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| AppName | O nome do aplicativo de anotações.|Wunderlist<br/>OneNote|
| ContactName | O nome de um contato em uma anotação.|Carol<br/>Jim<br/>Chris|
| DataSource | Localização das notas.|OneDrive<br/>Documentos do Google<br/>Meu Computador|
| Tipo de dados | O tipo de arquivo ou documento, geralmente associado a programas de software específicos.|Slides<br/>Planilha<br/>Planilha|
| Text | O texto de uma nota ou lembrete.|esticar antes de caminhar<br/>longo prazo amanhã|
| Title | Título de uma nota.|mantimentos<br/>pessoas para ligar<br/>tarefas pendentes|

## <a name="ondevice"></a>OnDevice 
O domínio OnDevice fornece tentativas e entidades relacionadas ao controle do dispositivo.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Fechar o player de vídeo|
|Cancelar reprodução|
|Você pode tornar a tela mais brilhante?|


### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| AreYouListening | Pergunte se o dispositivo está escutando.|isso está ligado?<br/>você está ouvindo?|
|Fechar aplicação|Feche o aplicativo do dispositivo.|fechar video player|
|FileBug|Arquive um bug no dispositivo.|arquivar um bug por favor<br/>Você pode arquivar um bug para mim?<br/>Deixe-me relatar esse bug|
|GoBack|Peça para voltar um passo ou voltar ao passo anterior.|Volte por favor<br/>Ir para a tela anterior<br/>Volte a parar de ouvir|
|Ajuda| Solicite ajuda.|Ajuda por favor<br/>Olá<br/>O que você pode fazer?<br/>eu preciso de ajuda| 
|LocateDevice|Localize o dispositivo.|Você pode localizar meu telefone<br/>Localize o iphone do tom<br/>Localizar meu telefone|
|LogIn|Faça logon de um serviço usando o dispositivo.|Login por favor<br/>Entrar no Facebook<br/>Entre no LinkedIn|
|LogOut|Efetue logout de um serviço usando o dispositivo.|Faça logoff do meu telefone<br/>Faça o login no twitter<br/>Faça logoff|
|MainMenu|Exiba o menu principal de um dispositivo.|Exibir Menu.|
|OpenApplication|Abra um aplicativo no dispositivo.|Abra o alarme por favor<br/>Ativar a câmera<br/>Iniciar o calendário|
|OpenSetting|Abra uma configuração no dispositivo.|Configurações de rede.|
|PairDevice|Par de dispositivo.|Você pode me ajudar no emparelhamento do sinal Bluetooth para o telefone<br/>Ligue o bluetooth e combine-o com o laptop<br/>Emparelhar sinal Bluetooth para o meu laptop|
|PowerOff | Desative o dispositivo.|Você pode desligar meu computador<br/>Shutdown<br/>Desativar o meu celular|
|QueryBattery|Obter informações sobre a vida útil da bateria.|Mostre-me a vida útil da bateria.<br/>Qual é o status da bateria<br/>Quanta bateria resta agora?<br/>Me mostre bateria|
|QueryWifi|Obtenha informações sobre o WiFi.|Obtenha informações sobre WiFi.|
|Reiniciar|Reinicie o dispositivo.|Reinicie-o por favor.|
|RingDevice| Peça ao dispositivo para tocar, para encontrá-lo quando estiver perdido.|Ligue meu telefone.| 
|SetBrightness|Definir o brilho do dispositivo.|Definir brilho para médio<br/>Definir o brilho para alto<br/>Definir o brilho para baixo|
|SetupDevice|Inicie a configuração do dispositivo.|Eu quero instalar a configuração do sistema operacional<br/>Setup por favor<br/>Faça a configuração para mim|
|ShowAppBar|Mostrar uma barra de aplicativos.|Mostre-me a barra de aplicativos<br/>Barra de aplicação por favor<br/>Mostre-me a barra de aplicativos|
|ShowContextMenu|Mostrar um menu de contexto.|Deixe-me ver o menu de contexto<br/>Menu de contexto por favor<br/>Você pode me mostrar o menu de contexto|
|Modo de suspensão|Coloque o dispositivo para dormir.|Vá dormir<br/>Modo de suspensão<br/>Meu computador em suspensão|
|SwitchApplication|Alterne o aplicativo para usar no dispositivo.|Alterne para o media player.|
|TurnDownBrightness|Diminua o brilho do dispositivo.|Dim a tela.|
|TurnOffSetting|Desative uma configuração de dispositivo.|Desativar Bluetooth<br/>Desabilitar dados<br/>Desconectar bluetooth|
|TurnOnSetting|Ative uma configuração de dispositivo.|Por <br/> Desativar|
|TurnUpBrightness|Aumentar o brilho do dispositivo.|Você pode tornar a tela mais brilhante?|

### <a name="entities"></a>Entidades
| Nome da entidade | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| AppName | Nome de um aplicativo no dispositivo.|SoundCloud<br/>YouTube|
| BrightnessLevel | Defina o nível de brilho no dispositivo.|Cem por cento<br/>Cinquenta<br/>40%|
| ContactName | O nome de um contato no dispositivo.|Paul<br/>Marlen Max|
| DeviceType | O tipo de dispositivo. |Telefone<br/>Kindle<br/>Laptop|
| MediaType | O tipo de mídia manipulado pelo dispositivo.|Música<br/>Filme<br/>Programas de TV|
| SettingType | Um tipo de configuração ou o painel de configurações que o usuário deseja editar.|Wi-Fi<br/>Rede sem fio<br/>Esquema de cores<br/>Central de notificações|

## <a name="places"></a>Locais  
Locais de domínio fornece tentativas para manipular consultas relacionadas aos locais como as empresas, instituição, restaurantes, espaços públicos e endereços.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Salve este local nos meus favoritos|
|A que distância fica o Holiday Inn?|
|A que horas o Safeway fecha?|


### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| AddFavoritePlace | Adicione um local à lista de favoritos do usuário.|Salve este local nos meus favoritos<br/>Adicionar esse endereço em Meus Favoritos|
|CheckAccident|Pergunte se há um acidente em um caminho especificado.|Há um acidente na 880?<br/>Mostrar informações de acidente|
|CheckAreaTraffic|Verifique se o tráfego para uma área geral ou estrada, não em uma rota especificada.|O tráfego em Seattle<br/>Como é o trânsito em Seattle?|
|CheckIntoPlace|Check-in em um local usando a mídia social.|Verifique-me no Foursquare<br/>Check-in aqui|
|CheckRouteTraffic| Verifique o tráfego de uma rota específica especificada pelo usuário.|Como é o tráfego para Mashiko?<br/>Mostre-me o tráfego para Kirkland<br/>Como é o tráfego para Seattle?| 
|Confirmar|Confirme uma ação relacionada a um local.|Confirme minha reserva no restaurante.|
|Sair|Ação de uma tarefa relacionada a um local de saída.|Pare por favor<br/>Pare de me dar instruções|
|FindPlace|Procurar um local (business, instituição, restaurante, espaço público, endereço).|Qual é a biblioteca mais próxima?<br/>Me localize um restaurante italiano BOM na exibição de Mountain Bike|
|GetAddress| Peça para o endereço de um local.|Mostre-me o endereço de Guu na rua Robson<br/>Qual é o endereço do Starbucks mais próximo?| 
|GetDistance|Pergunte distância para um local específico.|A que distância fica o Holiday Inn?<br/>Quão longe é a praça Bellevue daqui<br/>qual a distância para Tahoe|
|GetHours|Pergunte sobre as horas de funcionamento de um lugar.|A que horas o Safeway fecha?<br/>Quais são as horas para o Home Depot?<br/>A Starbucks ainda está aberta?|
|GetMenu|Peça os itens do menu para um restaurante.|A Zucca serve algo vegano?<br/>O que há no cardápio da Sizzler<br/>Mostre-me o menu do Applebee|
|GetPhoneNumber| Peça o número de telefone de um lugar.|Qual é o número de telefone do Starbucks mais próximo?<br/>Indique o número para o Home Depot| 
|GetPriceRange| Solicita a faixa de preço de um lugar.|Zucca é barato?<br/>A metade do preço do Cineplex é às quartas-feiras?<br/>Quanto custa um jantar inteiro de lagosta na Sizzler?|
|GetReviews|Peça opiniões de um lugar.|Mostre-me as revisões para fábrica de bolos<br/>Leia revisões Cineplex no Yelp|
|GetRoute|Peça indicações para um lugar.|Como caminhar até a praça Bellevue<br/>Mostre-me o caminho mais curto de 8th e 59th daqui<br/>Obter direções para Mountain View CA|
|GetStarRating|Peça a classificação por estrelas de um lugar.|Como a Zucca é avaliada de acordo com o Yelp?<br/>Quantas estrelas tem a lavanderia francesa?<br/>O aquário em Monterrey é bom?|
|GetTransportationSchedule|Obter o horário de ônibus para um lugar.|A que horas é o próximo ônibus para o centro?<br/>Mostra-me os ônibus em King County|
|GetTravelTime|Peça o tempo de viagem para um destino especificado.|Quanto tempo vai demorar para chegar a San Francisco daqui<br/>Qual é o tempo de condução para Denver a partir de SF|
|MakeCall|Faça uma ligação para um lugar.|Chame o mom<br/>Eu gostaria de fazer uma ligação pelo Skype para Anna<br/>Chamar Jim|
|MakeReservation|Solicite uma reserva para um restaurante ou outro negócio.|Reserve na Zucca para duas pessoas esta noite<br/>Reserve uma mesa para amanhã<br/>Tabela para 3 pessoas em Palo Alto às 8|
|MapQuestions|Solicitar informações sobre rotas ou se uma estrada específica vai para um destino.|13 atravessa o centro da cidade?<br/>Posso pegar a 880 para Oakland?|
|Classificação|Obter a descrição da classificação de um restaurante ou local.|Quantas estrelas tem a Pousada Contoso?|
|ReadAloud|Leia uma lista de lugares em voz alta.|Leia-me primeiro<br/>Leia-me em detalhes|
|SelectItem|Escolha um item de uma lista de opções relacionadas a um local ou lugares.|Escolha o segundo<br/>Selecione o primeiro|
|Mostrar mapa|Mostrar um mapa de uma área.|Mostrar um mapa para o segundo<br/>Mostrar mapa<br/>Encontre São Francisco no mapa|
|ShowNext|Mostrar o próximo item de uma série.|Mostre o outro<br/>Vá para a página seguinte|
|ShowPrevious|Mostrar o item anterior de uma série.|mostre o anterior<br/>previous<br/>go to previous|
|StartOver|Reinicie o aplicativo ou iniciar uma nova sessão.|Começar novamente<br/>Nova sessão<br/>
restart|
|TakesReservations|Pergunte se um local aceita reservas.|A galeria de arte aceita reservas<br/>É possível fazer uma reserva no Olive Garden

### <a name="entities"></a>Entidades
| Nome da entidade | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| AbsoluteLocation | A localização ou endereço de um local.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| Facilidades | As características / benefícios objetivos de um lugar.|as crianças comem de graça<br/>beira<br/>estacionamento grátis|
| Atmosfera | A atmosfera de um lugar.|crianças<br/>restaurante casual<br/>desportivo|
| Cozinha | A cozinha do lugar. |Mediterrâneo<br/>Italiano<br/>Indiana|
| DestinationAddress| Um local de destino ou endereço.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| DestinationPlaceName| O nome de um destino que é uma empresa, um restaurante, uma atração pública ou uma instituição.|central park<br/>safeway<br/>walmart|
| DestinationPlaceType | O tipo de destino que é uma empresa local, um restaurante, uma atração pública ou uma instituição. |Restaurante<br/>Opera<br/>Cinema|
| Distância | A distância até um lugar.|15 milhas<br/>5 milhas<br/>10 milhas de distância|
| Tipo de Refeição | Tipo de refeição como da manhã ou almoçar. |Café da manhã<br/>Refeição<br/>Almoçar<br/>Supper|
| OpenStatus | Indica se um local é aberto ou fechado.|Aberto<br/>closed<br/>Abrindo|
| Colocar nome | O nome de um local.|Fábrica de cheesecake|
| PlaceType | O tipo de lugar.|Cafe<br/>Teatro<br/>Biblioteca|
| PreferredRoute | A rota preferida especificada pelo usuário. | 101 <br/>202 <br/>Rota 401|
| Produto | O produto oferecido por um lugar. | Roupas<br/>Câmeras ASR Digitais<br/>Peixe fresco | 
| PublicTransportationRoute | O nome da rota de transporte público que o usuário está pesquisando. | Corredor Nordeste<br/>Rota de ônibus 3X |
| Classificação | A classificação de um lugar. | 5 estrelas<br/>3 estrelas<br/>4 estrelas|
| RouteAvoidanceCriteria | Critérios para evitar rotas específicas como evitar acidentes, construções ou pedágios | Pedágio <br/>Construções<br/>Rota 11|
| ServiceProvided | Este é o serviço prestado por uma empresa ou local, como corte de cabelo, aração de neve, paisagismo. | corte de cabelo<br/>mecânico<br/>técnico|
| TransportationCompany | O nome de um provedor de transporte.|Amtrak<br/>Acela<br/>Raça|
| TransportationType | O tipo de transporte.|ônibus<br/>Treinar<br/>Automóvel|

## <a name="reminder"></a>Reminder 
O domínio de lembrete fornece intenções e entidades para criar, editar e encontrar lembretes.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Mude minha entrevista para as 9 da manhã de amanhã|
|Lembre-me de comprar leite no caminho de casa|
|Você pode verificar se eu tenho um lembrete sobre o aniversário de Christine?|


### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Alterar| Altere um lembrete.|Mude minha entrevista para as 9 da manhã de amanhã<br/>Mover meu lembrete de atribuição para amanhã|
| Criar| Crie um novo lembrete.|Crie um lembrete<br/>Lembre-me de comprar leite<br/>Eu quero lembrar de ligar para Rebecca quando estou em casa|
| Excluir | Excluir um lembrete.|Excluir meu lembrete de imagem<br/>Excluir este lembrete|
| Localizar | Encontre um lembrete.|Eu tenho um lembrete sobre o meu aniversário?<br/>Você pode verificar se eu tenho um lembrete sobre o aniversário de Christine?|

### <a name="entities"></a>Entidades
| Nome da entidade | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Text | A descrição de texto de um lembrete.|pegar a limpeza a seco<br/>largando meu carro no centro de serviço|

## <a name="restaurantreservation"></a>RestaurantReservation 
O domínio RestaurantReservation fornece intenções e entidades relacionadas ao gerenciamento de reservas em restaurantes.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Reserve na Zucca para duas pessoas esta noite|
|Reserve uma mesa no BJ para amanhã|
|Mesa para 3 pessoas em Palo Alto às 7|

### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Reservar | Solicite uma reserva para um restaurante. |Reserve na Zucca para duas pessoas esta noite<br/>Reserve uma mesa para amanhã<br/>Mesa para 3 pessoas em Palo Alto às 7|

### <a name="entities"></a>Entidades
| Nome da entidade | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Endereço| Um local de evento ou endereço para uma reserva.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| Facilidades | Um local de evento ou endereço para uma reserva.|vista do oceano<br/>não fumante|
| AppName | O nome de um aplicativo para fazer reservas.|Mesa aberta<br/>Yelp<br/>TripAdvisor|
| Atmosfera | Uma descrição da atmosfera de um restaurante ou outro local.|romântico<br/>casual<br/>bom para grupos|
| Cozinha | Um tipo de comida, cozinha ou nacionalidade culinária. |Chinês<br/>Italiano<br/>Mexicana|
| Tipo de Refeição | Um tipo de refeição associado a uma reserva.|Café da manhã<br/>Refeição<br/>Almoçar<br/>Supper|
| Colocar nome | O nome de uma empresa local, restaurante, atração pública ou instituição.|IHOP<br/>Fábrica de cheesecake<br/>Louvre|
| PlaceType | O tipo de empresa local, restaurante, atração pública ou instituição.|restaurante<br/>ópera<br/>cinema|
| Classificação | A classificação de um lugar ou restaurante.|5 estrelas<br/>3 estrelas<br/>4 estrelas|

## <a name="taxi"></a>táxi 
 
O domínio Taxi fornece intenções e entidades para criar e gerenciar reservas de táxi.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Chame um táxi para as 3|
|Quanto tempo tenho que esperar pelo meu táxi?|
|Cancelar meu Uber|

### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Livro | Chame um táxi. |Me pegue um CAB<br/>Encontre um taxi<br/>Reserve-me um uber x|
| Cancelar | Cancele uma ação relacionada à reserva de um táxi.|Cancelar meu táxi<br/>Cancelar meu Uber|
| Rastrear | Rastrear uma rota de táxi.|Quanto tempo tenho que esperar pelo meu táxi?<br/>Onde está meu Uber?|

### <a name="entities"></a>Entidades
| Nome da entidade | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Endereço| O endereço associado à reserva de um táxi. |Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| DestinationAddress| Um local de destino ou endereço. |Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| DestinationPlaceName | O nome de um destino que é uma empresa local, um restaurante, uma atração pública ou uma instituição. |Central Park<br/>Safeway<br/>Walmart|
| DestinationPlaceType | O tipo de destino que é uma empresa local, um restaurante, uma atração pública ou uma instituição. |Restaurante<br/>Opera<br/>Cinema|
| Colocar nome | Nome da empresa local, restaurante, atração pública ou instituição. |Central Park<br/>Safeway<br/>Walmart|
| PlaceType| O tipo de lugar em um pedido para reservar um táxi.|Restaurante<br/>Opera<br/>Cinema|
| TransportationCompany | O nome de um provedor de transporte.|Amtrak<br/>Acela<br/>Raça|
| TransportationType | O tipo de transporte.|ônibus<br/>Treinar<br/>Automóvel|

## <a name="translate"></a>Translate 
O domínio do Google Tradutor fornece intenções e entidades relacionadas à tradução de texto para um idioma de destino.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Traduzir para francês|
|Traduzir olá para alemão|
|Traduzir esta frase para inglês|


### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Translate| Traduzir texto para outro idioma.|Traduzir para francês<br/>Traduzir olá para alemão|


### <a name="entities"></a>Entidades
| Nome da entidade | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| TargetLanguage | O idioma de destino de uma tradução.|Francês<br/>Alemão<br/>Coreano|
| Text | O texto para traduzir.|Olá Mundo<br/>Bom Dia<br/>Boa noite|

## <a name="tv"></a>TV 
 
O domínio da TV fornece intenções e entidades para o controle de TVs.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Mudar de canal para a BBC|
|Mostrar guia de TV|
|Assista National Geographic|

### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| ChangeChannel| Alterar um canal em uma TV.|Mude de canal para CNN<br/>Mudar de canal para a BBC<br/>Vá para o canal 4|
| ShowGuide| Mostrar o guia da TV.|Mostrar guia de TV<br/>o que está no canal de filmes agora?<br/>mostre minha lista de programa|
| WatchTV| Peça para assistir a um canal de TV.|Eu quero assistir o canal da Disney<br/>vá para a TV por favor<br/>Assista National Geographic|

### <a name="entities"></a>Entidades
| Nome da entidade | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| ChannelName | O nome de um canal de TV.|CNN<br/>BBC<br/>Canal de filme|

## <a name="utilities"></a>Utilidades  
O domínio Utilitários fornece intenções para tarefas comuns a muitas tarefas, como saudações, cancelamento, confirmação, ajuda, repetição, navegação, início e parada.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Volte para o Twitter|
|Por favor ajude|
|Repetir a última pergunta por favor|


### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Cancelar | Cancelar uma ação.|Cancelar a mensagem<br/>Eu não quero mais enviar o email|
| Confirmar | Confirme uma ação.|Sim, eu confirmo<br/>Bom eu estou confirmando<br/>Ok estou confirmando|
| FinishTask | Conclua uma tarefa que o usuário iniciou.|Terminei<br/>Terminei<br/>Isso está feito|
| GoBack | Volte um passo ou retorne a um passo anterior.|Volte para o Twitter<br/>Voltar uma etapa<br/>Voltar|
| Ajuda | Peça ajuda.|Por favor ajude<br/>ajuda aberta<br/>ajuda|
| Repetir | Repita uma ação.|Repetir a última pergunta por favor<br/>repetir a última música|
| ShowNext | Mostrar o próximo item de uma série. |Mostre o outro<br/>Vá para a página seguinte|
| ShowPrevious | Mostrar o item anterior de uma série.|mostre o anterior|
| StartOver | Reinicie o aplicativo ou iniciar uma nova sessão.|Começar novamente<br/>Nova sessão<br/>restart|
| Stop | Pare uma ação.| Pare de dizer isso por favor<br/>Cale-se<br/>Pare por favor|

## <a name="weather"></a>Clima 
O domínio Meteorologia fornece intenções e entidades para obter relatórios e previsões do tempo.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|clima em Londres em setembro|
|Qual é a previsão para 10 dias?|
|Qual é a temperatura média na Índia em setembro?|


### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| GetCondition | Obtenha fatos históricos relacionados ao clima. |tempo em Londres em setembro<br/>Qual é a temperatura média na Índia em setembro?|
| GetForecast | Obter o tempo atual e previsão para os próximos dias. |Como está o tempo hoje?<br/>Qual é a previsão de 10 dias?<br/>Como será o clima neste fim de semana?|

### <a name="entities"></a>Entidades
| Nome da entidade | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Local padrão| A localização absoluta para um pedido de tempo.|Seattle<br/>Paris<br/>Palo Alto|

## <a name="web"></a>Web 
O domínio da Web fornece uma intenção de navegar para um site.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Navegue para facebook.com|
|Vá para www.twitter.com|
|Navegue até www.bing.com|

### <a name="intents"></a>Intenções
| Nome do método | DESCRIÇÃO | Exemplos |
| ---------------- |-----------------------|----|
| Navegar | Um pedido para navegar para um site especificado. |Navegue para facebook.com<br/>Vá para www.twitter.com|

