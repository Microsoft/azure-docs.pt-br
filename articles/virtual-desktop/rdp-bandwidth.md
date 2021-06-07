---
title: Protocolo RDP requisitos de largura de banda área de trabalho virtual do Windows – Azure
titleSuffix: Azure
description: Noções básicas sobre requisitos de largura de banda RDP para área de trabalho virtual do Windows
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: c9ef72241ac62efd8555de59bb52949321364035
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96325172"
---
# <a name="remote-desktop-protocol-rdp-bandwidth-requirements"></a>Requisitos de largura de banda de protocolo RDP (RDP)

O protocolo RDP (RDP) é uma tecnologia sofisticada que usa várias técnicas para aperfeiçoar a entrega de gráficos remotos do servidor para o dispositivo cliente. Dependendo do caso de uso, da disponibilidade dos recursos de computação e da largura de banda da rede, o RDP ajusta dinamicamente vários parâmetros para fornecer a melhor experiência do usuário.

Protocolo RDP multiplexa vários canais virtuais dinâmicos (DVCs) em um único canal de dados Enviado por diferentes transportes de rede. Há DVCs separadas para gráficos remotos, entrada, redirecionamento de dispositivo, impressão e outros. Os parceiros do WVD também podem implementar suas extensões que usam interfaces DVC.
A quantidade de dados enviados por RDP depende da atividade do usuário. Por exemplo, um usuário pode trabalhar com conteúdo textual básico para a maior parte da sessão e consumir largura de banda mínima, mas, em seguida, gerar uma cópia impressa de um documento da página 200 para a impressora local. Esse trabalho de impressão usará uma quantidade significativa de largura de banda de rede.

Ao usar uma sessão remota, a largura de banda disponível da sua rede afeta drasticamente a qualidade da sua experiência. Diferentes aplicativos e resoluções de exibição exigem configurações de rede diferentes, portanto, é essencial garantir que sua configuração de rede atenda às suas necessidades.

## <a name="estimating-bandwidth-utilization"></a>Estimando a utilização da largura de banda

O RDP usa vários algoritmos de compactação para diferentes tipos de dados. A tabela abaixo guia a estimativa das transferências de dados:

| Tipo de dados | Direção | Como estimar |
|---|---|---|
| Gráficos remotos | Host de sessão para o cliente | [Consulte as diretrizes detalhadas](#estimating-bandwidth-used-by-remote-graphics) |
| Pulsações | Ambas as direções | ~ 20 bytes a cada 5 segundos  |
| Entrada | Cliente para o host de sessão | A quantidade de dados é baseada na atividade do usuário, com menos de 100 bytes para a maioria das operações  |
| Transferências de arquivos | Ambas as direções | As transferências de arquivos estão usando a compactação em massa. Usar compactação. zip para aproximação |
| Imprimindo | Host de sessão para o cliente | A transferência do trabalho de impressão depende do driver e do uso da compactação em massa, use a compactação. zip para a aproximação |

Outros cenários podem ter seus requisitos de largura de banda alterados, dependendo de como você os usa, como:

* Conferência de voz ou vídeo
* Comunicação em tempo real
* Vídeo de 4K em streaming

## <a name="estimating-bandwidth-used-by-remote-graphics"></a>Estimando a largura de banda usada por gráficos remotos

É difícil prever o uso da largura de banda pela área de trabalho remota. As atividades do usuário geram a maior parte do tráfego da área de trabalho remota. Cada usuário é exclusivo e as diferenças em seus padrões de trabalho podem alterar significativamente o uso da rede.

A melhor maneira de entender os requisitos de largura de banda é monitorar as conexões de usuário reais. O monitoramento pode ser executado pelos contadores de desempenho internos ou pelo equipamento de rede.

No entanto, em muitos casos, você pode estimar a utilização da rede compreendendo como protocolo RDP funciona e analisando os padrões de trabalho dos usuários.

O protocolo remoto fornece os gráficos gerados pelo servidor remoto para exibi-los em um monitor local. Mais especificamente, ele fornece o bitmap de área de trabalho totalmente composto no servidor.
Embora o envio de um bitmap de área de trabalho pareça uma tarefa simples na primeira abordagem, ele requer uma quantidade significativa de recursos. Por exemplo, uma imagem de área de trabalho 1080p em seu formato descompactado é de cerca de 8Mb de tamanho. A exibição dessa imagem no monitor conectado localmente com uma taxa de atualização de tela modesto de 30 Hz requer largura de banda de aproximadamente 237 MB/s.

Para reduzir a quantidade de dados transferidos pela rede, o RDP usa a combinação de várias técnicas, incluindo, mas não se limitando a

* Otimizações de taxa de quadros
* Classificação de conteúdo da tela
* Codecs específicos de conteúdo
* Codificação de imagem progressiva
* Armazenamento em cache do lado do cliente

Para entender melhor os gráficos remotos, considere o seguinte:

* Quanto mais ricos forem os gráficos, mais largura de banda será tomada
  * Texto, elementos da interface do usuário da janela e áreas de cor sólida estão consumindo menos largura de banda do que qualquer outra coisa.
  * As imagens naturais são os colaboradores mais significativos para uso de largura de banda. Mas o armazenamento em cache do lado do cliente ajuda com sua redução.
* Somente partes alteradas da tela são transmitidas. Se não houver nenhuma atualização visível na tela, nenhuma atualização será enviada.
* A reprodução de vídeo e outros conteúdos com alta taxa de quadros são essencialmente uma apresentação de slides de imagem. O RDP usa dinamicamente os codecs de vídeo apropriados para entregá-los com a taxa de quadros próxima ao original. No entanto, ainda é gráfico, e ainda é o colaborador mais significativo da utilização da largura de banda.
* Tempo ocioso na área de trabalho remota significa não há atualizações de tela mínimas; Portanto, o uso da rede é mínimo durante os horários ociosos.
* Quando a janela do cliente da área de trabalho remota é minimizada, nenhuma atualização gráfica é enviada do host da sessão.

Tenha em mente que o estresse colocado em sua rede depende tanto da taxa de quadros de saída da carga de trabalho do aplicativo quanto da sua resolução de vídeo. Se a taxa de quadros ou a resolução de vídeo aumentar, o requisito de largura de banda também aumentará. Por exemplo, uma carga de trabalho leve com uma exibição de alta resolução exige mais largura de banda disponível do que uma carga de trabalho leve com resolução regular ou baixa. Resoluções de vídeo diferentes exigem larguras de banda disponíveis diferentes.

A tabela abaixo guia a estimativa dos dados usados pelos diferentes cenários gráficos. Esses números se aplicam a uma única configuração de monitor com a resolução 1920 x 1080 e com o modo gráfico padrão e o modo gráfico H. 264/AVC 444.

| Cenário | Modo padrão | Modo H. 264/AVC 444 | Thumbnail | Descrição do cenário |
|:---|---:|---:|---|---|
| Ocioso | 0,3 kbps | 0,3 kbps |:::image type="content" source="media/idle.png" alt-text="Captura de tela da conexão ociosa":::| O usuário está em pausa no trabalho e não há atualizações de tela ativas |
| Microsoft Word | 100-150 kbps | 200-300 kbps |:::image type="content" source="media/word.gif" alt-text="Animação do Microsoft Word":::| O usuário está trabalhando ativamente com o Microsoft Word, digitando, colando elementos gráficos e alternando entre documentos |
| Microsoft Excel | 150-200 Kbps | 400-500 kbps |:::image type="content" source="media/excel.gif" alt-text="Animação do Microsoft Excel":::| O usuário está trabalhando ativamente com o Microsoft Excel, várias células com fórmulas e gráficos são atualizados simultaneamente |
| Microsoft PowerPoint | 4 a 4,5 Mbps | 1.6-1.8 Mbps |:::image type="content" source="media/powerpoint.gif" alt-text="Animação do Microsoft PowerPoint":::| O usuário está trabalhando ativamente com o Microsoft PowerPoint, digitando e colando. O usuário também está modificando gráficos avançados e usando efeitos de transição de slides |
| Navegação na Web | 6 a 6,5 Mbps | 0,9-1 Mbps |:::image type="content" source="media/web.gif" alt-text="Animação de navegação na Web":::| O usuário está trabalhando ativamente com um site rico graficamente que contém várias imagens estáticas e animadas. O usuário rola as páginas horizontal e verticalmente |
| Galeria de imagens | 3.3-3,6 Mbps | 0,7-0,8 Mbps |:::image type="content" source="media/image-gallery.gif" alt-text="Animação da Galeria de imagens":::| O usuário está trabalhando ativamente com o aplicativo da Galeria de imagens. navegando, aplicando zoom, redimensionando e girando imagens |
| Reprodução de vídeo | 8,5-9,5 Mbps | 2,5-2,8 Mbps |:::image type="content" source="media/video.gif" alt-text="Animação de reprodução de vídeo":::| O usuário está assistindo a um vídeo de 30 FPS que consome 1/2 da tela |
| Reprodução de vídeo em tela inteira | 7.5-8,5 Mbps | 2,5-3,1 Mbps |:::image type="content" source="media/fullscreen-video.gif" alt-text="Animação de reprodução de vídeo em tela inteira":::| O usuário está assistindo a um vídeo de 30 FPS que foi maximizado para uma tela inteira |

## <a name="dynamic-bandwidth-allocation"></a>Alocação de largura de banda dinâmica

Protocolo RDP é um protocolo moderno projetado para se ajustar às condições de rede em alteração dinamicamente.
Em vez de usar os limites rígidos de utilização de largura de banda, o RDP usa a detecção de rede contínua que monitora ativamente a largura de banda de rede e o tempo de ida e volta do pacote Com base nas conclusões, o RDP seleciona dinamicamente as opções de codificação gráfica e aloca largura de banda para o redirecionamento de dispositivo e outros canais virtuais.  
Essa tecnologia permite que o RDP use o pipe de rede completo quando disponível e retorne rapidamente quando a rede for necessária para outra coisa.
O RDP detecta que e ajusta a qualidade da imagem, a taxa de quadros ou os algoritmos de compactação se outros aplicativos solicitarem a rede.

## <a name="limit-network-bandwidth-use-with-throttle-rate"></a>Limitar o uso de largura de banda de rede com taxa de limitação

Na maioria dos cenários, não há necessidade de limitar a utilização da largura de banda, pois a limitação pode afetar a experiência do usuário. Ainda em redes restritas, talvez você queira limitar a utilização da rede. Outro exemplo são redes alugadas que são cobradas pela quantidade de tráfego usada.

Nesses casos, você pode limitar um tráfego de rede de saída de RDP especificando uma taxa de aceleração na política de QoS.

  >[!NOTE]
  > [Verifique se o Shortpath do RDP está habilitado](./shortpath.md) -a limitação da taxa de aceleração não tem suporte para o transporte de conexão reversa.

### <a name="implement-throttle-rate-limiting-on-session-host-using-group-policy"></a>Implementar limitação de taxa de aceleração no host de sessão usando Política de Grupo

Você pode usar a QoS (qualidade de serviço) baseada em políticas em Política de Grupo para definir a taxa de limitação predefinida.

Para criar uma política de QoS para hosts de sessão ingressados no domínio, primeiro, entre em um computador no qual o gerenciamento de Política de Grupo foi instalado. Abra o gerenciamento de Política de Grupo (selecione Iniciar, aponte para ferramentas administrativas e, em seguida, selecione gerenciamento de Política de Grupo) e, em seguida, conclua as seguintes etapas:

1. No gerenciamento de Política de Grupo, localize o contêiner em que a nova política deve ser criada. Por exemplo, se todos os computadores hosts de sua sessão estiverem localizados em uma UO chamada **hosts de sessão**, a nova política deverá ser criada na UO hosts de sessão.

2. Clique com o botão direito do mouse no contêiner apropriado e selecione **criar um GPO nesse domínio e vincule-o aqui**.

3. Na caixa de diálogo **novo GPO** , digite um nome para o novo objeto política de grupo na caixa **nome** e selecione **OK**.

4. Clique com o botão direito do mouse na política recém-criada e selecione **Editar**.

5. No Editor de Gerenciamento de Política de Grupo, expanda **configuração do computador**, expanda **configurações do Windows**, clique com o botão direito do mouse em **QoS baseada em políticas** e selecione **criar nova política**.

6. Na caixa de diálogo **QoS baseada em políticas** , na página de abertura, digite um nome para a nova política na caixa **nome** . Selecione **especificar taxa de aceleração de saída** e defina o valor necessário e, em seguida, selecione **Avançar**.

7. Na página seguinte, selecione **somente aplicativos com esse nome executável** e insira o nome **svchost.exe** e, em seguida, selecione **Avançar**. Essa configuração instrui a política a priorizar apenas o tráfego de correspondência do serviço de Área de Trabalho Remota.

8. Na terceira página, certifique-se de que **qualquer endereço IP de origem** e **qualquer endereço IP de destino** sejam selecionados. Selecione **Avançar**. Essas duas configurações garantem que os pacotes serão gerenciados independentemente de qual computador (endereço IP) enviou os pacotes e qual computador (endereço IP) receberá os pacotes.

9. Na página quatro, selecione **UDP** na lista suspensa **selecionar o protocolo que essa política de QoS se aplica** .

10. No título, **especifique o número da porta de origem**, selecione **nessa porta ou intervalo de origem**. Na caixa de texto que acompanha, digite **3390**. Selecione **Concluir**.

As novas políticas que você criou não entrarão em vigor até que Política de Grupo sejam atualizadas nos computadores host da sessão. Embora Política de Grupo seja atualizado periodicamente por conta própria, você pode forçar uma atualização imediata seguindo estas etapas:

1. Em cada host de sessão para o qual você deseja atualizar Política de Grupo, abra um prompt de comando como administrador (*Executar como administrador*).

2. No prompt de comando, digite

   ```console
   gpupdate /force
   ```

### <a name="implement-throttle-rate-limiting-on-session-host-using-powershell"></a>Implementar limitação de taxa de aceleração no host de sessão usando o PowerShell

Você pode definir a taxa de limitação para Shortpath de RDP usando o cmdlet do PowerShell abaixo:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os requisitos de largura de banda para a área de trabalho virtual do Windows, confira [noções básicas sobre os requisitos de largura de banda protocolo RDP (RDP)](rdp-bandwidth.md)
* Para saber mais sobre conectividade de rede de área de trabalho virtual do Windows, consulte [noções básicas sobre conectividade de rede de área de trabalho virtual](network-connectivity.md)
* Para começar a usar a qualidade de serviço (QoS) para área de trabalho virtual do Windows, consulte [implementar a QoS (qualidade de serviço) para a área de trabalho virtual do Windows](rdp-quality-of-service-qos.md).
