---
title: Implementar a QoS (qualidade de serviço) para a área de trabalho virtual do Windows (versão prévia)
titleSuffix: Azure
description: Como configurar a QoS (versão prévia) para a área de trabalho virtual do Windows.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: b61faf74d96e2571e91f7bf9d10eac88cdbf8345
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94639159"
---
# <a name="implement-quality-of-service-qos-for-windows-virtual-desktop-preview"></a>Implementar a QoS (qualidade de serviço) para a área de trabalho virtual do Windows (versão prévia)

> [!IMPORTANT]
> O suporte à política de QoS (qualidade de serviço) para a área de trabalho virtual do Windows está atualmente em visualização pública.
> Essa visualização é fornecida sem um contrato de nível de serviço e não é recomendável usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O [RDP Shortpath](./shortpath.md) fornece um transporte baseado em UDP direto entre área de trabalho remota host de sessão e cliente. O RDP Shortpath permite a configuração de políticas de QoS (qualidade de serviço) para os dados RDP.
A QoS na área de trabalho virtual do Windows permite o tráfego RDP em tempo real que é sensível aos atrasos de rede para "recortar na linha" na frente do tráfego que é menos sensível. Um exemplo desse tráfego menos confidencial seria o download de um novo aplicativo, em que um segundo adicional para baixar não é um grande problema. A QoS usa objetos do Windows Política de Grupo para identificar e marcar todos os pacotes em fluxos em tempo real e ajudar sua rede a dar ao tráfego RDP uma parte dedicada da largura de banda.

Se você der suporte a um grande grupo de usuários enfrentando qualquer um dos problemas descritos neste artigo, provavelmente precisará implementar o QoS. Uma pequena empresa com poucos usuários pode não precisar de QoS, mas ela deve ser útil até lá.

Sem alguma forma de QoS, você poderá ver os seguintes problemas:

* Tremulação – pacotes RDP chegando a taxas diferentes, o que pode resultar em falhas visuais e de áudio
* Perda de pacotes – pacotes descartados, o que resulta em retransmissão que requer tempo adicional
* RTT (tempo de ida e volta) atrasado – pacotes RDP demoram muito para atingir seus destinos, resultando em atrasos perceptíveis entre a entrada e a reação do aplicativo remoto.

A maneira menos complicada de resolver esses problemas é aumentar o tamanho das conexões de dados, internamente e horizontalmente para a Internet. Como isso costuma ser proibitivo, a QoS fornece uma maneira de gerenciar os recursos que você tem em vez de adicionar largura de banda com mais eficiência. Para resolver problemas de qualidade, recomendamos que você primeiro use QoS e, em seguida, adicione a largura de banda somente quando necessário.

Para que a QoS seja eficaz, você deve aplicar configurações de QoS consistentes em toda a sua organização. Qualquer parte do caminho que não dê suporte às suas prioridades de QoS pode degradar a sessão RDP de qualidade.

## <a name="introduction-to-qos-queues"></a>Introdução às filas de QoS

Para fornecer QoS, os dispositivos de rede devem ter uma maneira de classificar o tráfego e devem ser capazes de distinguir o RDP de outro tráfego de rede.

Quando o tráfego de rede entra em um roteador, o tráfego é colocado em uma fila. Se uma política de QoS não estiver configurada, haverá apenas uma fila e todos os dados serão tratados como primeiro a entrar, primeiro a sair com a mesma prioridade. Isso significa que o tráfego de RDP pode ficar preso atrás do tráfego em que alguns milissegundos extras de atraso não seriam um problema.

Ao implementar o QoS, você define várias filas usando um dos vários recursos de gerenciamento de congestionamento, como o enfileiramento de prioridade da Cisco e os recursos de [CBWFQ (fila ponderada) de peso baseado em classe](https://www.cisco.com/en/US/docs/ios/12_0t/12_0t5/feature/guide/cbwfq.html#wp17641) e de contenção de congestionamento, como o [WRED (detecção antecipada aleatória ponderada)](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/qos_conavd/configuration/15-mt/qos-conavd-15-mt-book/qos-conavd-cfg-wred.html).

Uma analogia simples é que a QoS cria "pistas anda" virtuais em sua rede de dados. Assim, alguns tipos de dados nunca ou raramente encontram um atraso. Depois de criar essas pistas, você pode ajustar seu tamanho relativo e gerenciar de maneira muito mais eficiente a largura de banda de conexão que ainda oferece experiências de nível empresarial para os usuários da sua organização.

## <a name="qos-implementation-checklist"></a>Lista de verificação de implementação de QoS

Em um alto nível, faça o seguinte para implementar a QoS:

1. [Verifique se sua rede está pronta](#make-sure-your-network-is-ready)
2. [Verifique se o Shortpath de RDP está habilitado](./shortpath.md) -as políticas de QoS não têm suporte para o transporte de conexão reversa
3. [Implementar a inserção de marcadores de DSCP](#insert-dscp-markers) em hosts de sessão

Ao se preparar para implementar o QoS, tenha em mente as seguintes diretrizes:

* O caminho mais curto para o host de sessão é o melhor
* Os obstáculos entre, como proxies ou dispositivos de inspeção de pacotes, não são recomendados

## <a name="make-sure-your-network-is-ready"></a>Verifique se sua rede está pronta

Se estiver considerando uma implementação de QoS, você já deve ter determinado os requisitos de largura de banda e outros [requisitos de rede](/windows-server/remote/remote-desktop-services/network-guidance?context=/azure/virtual-desktop/context/context).
  
O congestionamento de tráfego em uma rede terá impacto significativo na qualidade da mídia. A falta de largura de banda leva à degradação do desempenho e uma experiência de usuário ruim. À medida que a adoção e o uso da área de trabalho virtual do Windows crescem, use [log Analytics](./diagnostics-log-analytics.md) para identificar problemas e fazer ajustes usando a QoS e as adições de largura de banda seletiva.

### <a name="vpn-considerations"></a>Considerações sobre VPN

O QoS funciona apenas conforme o esperado quando implementado em todos os links entre clientes e hosts de sessão. Se você usar o QoS em uma rede interna e um usuário entrar em um local remoto, você só poderá priorizar dentro da rede gerenciada interna. Embora locais remotos possam receber uma conexão gerenciada implementando uma VPN (rede virtual privada), uma VPN adiciona inerentemente a sobrecarga de pacotes e cria atrasos no tráfego em tempo real.

Em uma organização global com links gerenciados que abrangem continentes, é altamente recomendável QoS porque a largura de banda para esses links é limitada em comparação com a LAN.

## <a name="insert-dscp-markers"></a>Inserir marcadores de DSCP

Você pode implementar a QoS usando um objeto de Política de Grupo (GPO) para direcionar hosts de sessão para inserir um marcador DSCP em cabeçalhos de pacotes IP identificando-o como um tipo específico de tráfego. Roteadores e outros dispositivos de rede podem ser configurados para reconhecer esses marcações e colocar o tráfego em uma fila separada de prioridade mais alta.

Você pode comparar marcações de DSCP com carimbos de postagem que indicam aos funcionários de pessoas em quão urgente a entrega é e qual a melhor classificação para entrega rápida. Depois de configurar sua rede para dar prioridade aos fluxos RDP, os pacotes perdidos e os pacotes atrasados devem diminuir significativamente.

Depois que todos os dispositivos de rede estiverem usando as mesmas classificações, marcações e prioridades, será possível reduzir ou eliminar atrasos, pacotes eliminados e Tremulação. Da perspectiva do RDP, a etapa de configuração essencial é a classificação e a marcação de pacotes. No entanto, para que a QoS de ponta a ponta seja bem-sucedida, você também precisa alinhar a configuração de RDP com a configuração de rede subjacente com cuidado.
O valor DSCP informa uma rede configurada de forma correspondente qual prioridade para fornecer um pacote ou fluxo.

É recomendável usar o valor de DSCP 46 que mapeia para a classe DSCP **do EF (encaminhamento acelerado)** .

### <a name="implement-qos-on-session-host-using-group-policy"></a>Implementar QoS no host da sessão usando Política de Grupo

Você pode usar a QoS (qualidade de serviço) baseada em políticas em Política de Grupo para definir o valor DSCP predefinido.

Para criar uma política de QoS para hosts de sessão ingressados no domínio, primeiro, entre em um computador no qual o gerenciamento de Política de Grupo foi instalado. Abra o gerenciamento de Política de Grupo (selecione Iniciar, aponte para ferramentas administrativas e, em seguida, selecione gerenciamento de Política de Grupo) e, em seguida, conclua as seguintes etapas:

1. No gerenciamento de Política de Grupo, localize o contêiner em que a nova política deve ser criada. Por exemplo, se todos os computadores hosts de sua sessão estiverem localizados em uma UO chamada **"hosts de sessão"**, a nova política deverá ser criada na UO hosts de sessão.

2. Clique com o botão direito do mouse no contêiner apropriado e selecione **criar um GPO nesse domínio e vincule-o aqui**.

3. Na caixa de diálogo **novo GPO** , digite um nome para o novo objeto política de grupo na caixa **nome** e selecione **OK**.

4. Clique com o botão direito do mouse na política recém-criada e selecione **Editar**.

5. No Editor de Gerenciamento de Política de Grupo, expanda **configuração do computador**, expanda **configurações do Windows**, clique com o botão direito do mouse em **QoS baseada em políticas** e selecione **criar nova política**.

6. Na caixa de diálogo **QoS baseada em políticas** , na página de abertura, digite um nome para a nova política na caixa **nome** . Selecione **especificar valor DSCP** e defina o valor como **46**. Deixe a seleção **especificar taxa de aceleração de saída** desmarcada e, em seguida, selecione **Avançar**.

7. Na página seguinte, selecione **somente aplicativos com esse nome executável** e insira o nome **svchost.exe** e, em seguida, selecione **Avançar**. Essa configuração instrui a política a priorizar apenas o tráfego de correspondência do serviço de Área de Trabalho Remota.

8. Na terceira página, verifique se **qualquer endereço IP de origem** e **qualquer endereço IP de destino** estão selecionados e, em seguida, selecione **Avançar**. Essas duas configurações garantem que os pacotes serão gerenciados independentemente de qual computador (endereço IP) enviou os pacotes e qual computador (endereço IP) receberá os pacotes.

9. Na página quatro, selecione **UDP** na lista suspensa **selecionar o protocolo que essa política de QoS se aplica** .

10. No título, **especifique o número da porta de origem**, selecione **nessa porta ou intervalo de origem**. Na caixa de texto que acompanha, digite **3390**. Selecione **Concluir**.

As novas políticas que você criou não entrarão em vigor até que Política de Grupo sejam atualizadas nos computadores host da sessão. Embora Política de Grupo seja atualizado periodicamente por conta própria, você pode forçar uma atualização imediata seguindo estas etapas:

1. Em cada host de sessão para o qual você deseja atualizar Política de Grupo, abra um prompt de comando como administrador (*Executar como administrador*).

1. No prompt de comando, digite

   ```console
   gpupdate /force
   ```

### <a name="implement-qos-on-session-host-using-powershell"></a>Implementar QoS no host da sessão usando o PowerShell

Você pode definir a QoS para Shortpath de RDP usando o cmdlet do PowerShell abaixo:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="related-articles"></a>Artigos relacionados

* [Política de QoS (qualidade de serviço)](/windows-server/networking/technologies/qos/qos-policy-top)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os requisitos de largura de banda para a área de trabalho virtual do Windows, confira [noções básicas sobre os requisitos de largura de banda protocolo RDP (RDP)](rdp-bandwidth.md)
* Para saber mais sobre conectividade de rede de área de trabalho virtual do Windows, consulte [noções básicas sobre conectividade de rede de área de trabalho virtual](network-connectivity.md)
