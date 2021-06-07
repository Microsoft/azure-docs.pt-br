---
title: Política de Mensagens
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre as políticas de mensagens SMS.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/19/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: bb9765c2620f45d67bf888f8bfe8a4dee450cfd6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646487"
---
# <a name="azure-communication-services-messaging-policy"></a>Política de Mensagens dos Serviços de Comunicação do Azure

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Os Serviços de Comunicação do Azure estão transformando a maneira como nossos clientes interagem com os clientes deles, criando experiências de comunicação personalizadas e avançadas que tiram proveito dos mesmos serviços de nível empresarial que formam a base do Microsoft Teams e do Skype. Integre a funcionalidade de mensagens SMS às suas soluções de comunicação para alcançar seus clientes a qualquer momento e em qualquer lugar em que precisem de suporte. Para começar, basta ter em mente alguns requisitos.

Sabemos que aprender os requisitos de mensagens pode parecer um desafio, mas basta se lembrar do acrônimo "CRCF":

- C – Consentimento
- R – Recusa
- C – Conteúdo da mensagem
- F – Falsificação

Desenvolvemos esta política de mensagens para ajudar você a atender aos requisitos regulatórios e se alinhar às melhores práticas. 

[!INCLUDE [Notice](../../includes/messaging-policy-include.md)]

## <a name="consent"></a>Consentimento 

### <a name="what-is-consent"></a>O que é consentimento?

Consentimento é um contrato entre você e o destinatário da mensagem que lhe permite enviar mensagens automatizadas a ele. Você precisa obter o consentimento antes de enviar a primeira mensagem e deve deixar claro para o destinatário que ele está concordando em receber mensagens suas. Esse procedimento é conhecido como obtenção de "consentimento prévio expresso" do indivíduo a quem você pretende enviar mensagens.

As mensagens enviadas devem ser do mesmo tipo que o destinatário concordou em receber e devem ser enviadas apenas ao número que ele forneceu a você. Se você pretende enviar mensagens informativas, como lembretes de compromissos ou alertas, o consentimento pode ser escrito ou oral. Se você pretende enviar mensagens promocionais, como mensagens de vendas ou de marketing que promovem um produto ou serviço, o consentimento deve ser escrito.

### <a name="how-do-you-obtain-consent"></a>Como obter consentimento?

É possível obter consentimento de várias maneiras, por exemplo:

- quando um usuário insere o número de telefone dele em um site, 
- quando um usuário inicia uma troca de mensagens de texto ou
- quando um usuário envia uma palavra-chave de inscrição para seu número de telefone. 
 
Independentemente da maneira como o consentimento é obtido, você e seus clientes precisam garantir que ele seja inequívoco. O escopo do consentimento deve ficar claro para o destinatário.


### <a name="consent-requirements"></a>Requisitos de consentimento:

- Forneça uma "chamada à ação" antes de obter consentimento. Você e seus clientes devem fornecer aos potenciais destinatários das mensagens uma "chamada à ação" que os convide a aceitar participar de seu programa de mensagens. A chamada à ação deve incluir, no mínimo: (1) a identidade do remetente da mensagem, (2) instruções claras de como aceitar, (3) instruções de recusar e (4) tarifas associadas às mensagens.
- O consentimento não pode ser transferido nem atribuído. O consentimento que um indivíduo fornece a você não pode ser transferido nem vendido para terceiros não afiliados. Se você obtiver o consentimento de um indivíduo para um terceiro, deverá identificar claramente o terceiro para o indivíduo. Você também deverá declarar que o consentimento obtido se aplica somente a comunicações do terceiro.
- O consentimento tem finalidade limitada. Um indivíduo que fornece um número para uma determinada finalidade consente em receber comunicações somente para essa finalidade específica e desse remetente de mensagens específico. Antes de obter consentimento, você deve notificar claramente o destinatário pretendido das mensagens se pretende enviar mensagens recorrentes ou mensagens de um afiliado.

### <a name="consent-best-practices"></a>Melhores práticas de consentimento:

Além dos requisitos de mensagens discutidos acima, você pode optar por implementar várias melhores práticas comuns, incluindo: 

- Informações detalhadas de "chamada à ação". Para garantir que você obtenha o consentimento apropriado, forneça
  - o nome ou a descrição do programa ou do produto de mensagens
  - os números dos quais os destinatários receberão mensagens e 
  - todos os termos e condições aplicáveis antes que um indivíduo aceite receber mensagens suas.
- Registros de consentimento precisos. Retenha os registros de consentimento que um indivíduo fornecer a você por pelo menos quatro anos. Os registros de consentimento podem incluir:
  - carimbos de data/hora
  - o meio pelo qual o consentimento foi obtido
  - a campanha específica para a qual o consentimento foi obtido
  - capturas de tela
  - a ID da sessão ou o endereço IP do indivíduo que está fornecendo consentimento.
- Políticas de privacidade e segurança. Os desenvolvedores são incentivados a fornecer políticas de privacidade simples que os destinatários das mensagens podem examinar antes de fornecer o consentimento. Também recomendamos a manutenção de controles de segurança proativos a fim de proteger as informações privadas dos indivíduos.


## <a name="double-opt-in-consent"></a>Consentimento com aceitação dupla:

Os Serviços de Comunicação do Azure recomendam que você use consentimento com aceitação dupla para todas as campanhas de mensagens. O consentimento com aceitação dupla é um processo de duas etapas no qual, primeiro, o indivíduo fornece consentimento para receber determinados tipos de mensagens de você. Em seguida, você envia uma mensagem de aceitação de acompanhamento para confirmar o consentimento. Você deverá enviar mais mensagens somente se o destinatário da mensagem confirmar o consentimento.

A mensagem de confirmação inicial enviada deve incluir sua identidade, a opção de recusar mensagens futuras (por exemplo, com o uso de um comando para "PARAR"), um número para chamada gratuita ou comando de "AJUDA" para obter informações adicionais, a notificação de que o indivíduo está inscrito em um programa de mensagens recorrentes, uma breve descrição do programa, a frequência com a qual você pretende enviar mensagens recorrentes e as tarifas associadas. 

### <a name="does-azure-communication-services-ever-require-double-opt-in-consent"></a>Há alguma situação em que os Serviços de Comunicação do Azure exigem consentimento com aceitação dupla?
Sim, embora o consentimento com aceitação dupla sempre seja recomendado, os Serviços de Comunicação do Azure exigem que você o utilize para alguns tipos de campanhas de mensagens devido ao uso frequente delas em esquemas de phishing ou à sua tendência de gerar reclamações do consumidor. Essas campanhas incluem:
- Mensagens de seguro de veículos
- Planos de saúde de curto prazo
- Mensagens de refinanciamento de dívidas ou de redução de taxas não enviadas por uma instituição financeira
- Mensagens de geração de vendas potenciais
- Sorteios, concursos e brindes
- Ofertas de trabalho em casa

As campanhas para as quais o consentimento com aceitação dupla é necessário estão sujeitas a alterações a critério dos Serviços de Comunicação do Azure.

### <a name="exceptions-to-traditional-consent-rules"></a>Exceções às regras de consentimento tradicionais:
Embora o consentimento prévio expresso normalmente seja necessário antes de enviar uma mensagem, há duas situações em que o consentimento para enviar mensagens um indivíduo é implícito.

- O destinatário inicia uma comunicação. Se um indivíduo iniciar uma comunicação enviando uma mensagem para você, você poderá fornecer informações relevantes em resposta a uma consulta ou solicitação específica contida na mensagem. No entanto, o consentimento implícito fornecido pelo indivíduo fica limitado à conversa que ele iniciou, a menos que você obtenha consentimento para comunicações adicionais.
- Isenções para serviços específicos. Há vários serviços específicos para os quais você pode obter consentimento implícito para iniciar uma mensagem. Os mais comuns são: 
- mensagens sobre entrega de pacotes
- mensagens de instituições financeiras relacionadas a tópicos sensíveis ao tempo (como transações potencialmente fraudulentas ou violações de dados)
- mensagens do prestador de serviços de saúde que incluem informações sensíveis ao tempo e com finalidade de tratamento (como lembretes de consultas ou exames, resultados de exames e notificações de prescrições). 
 
Nenhuma dessas mensagens pode incluir solicitações nem anúncios.


## <a name="opt-out"></a>Recusa

Os destinatários de mensagens podem revogar o consentimento e recusar o recebimento de mensagens futuras por meio de qualquer meio razoável. Você não pode designar um meio exclusivo para que os destinatários da mensagem revoguem o consentimento. 

### <a name="opt-out-requirements"></a>Requisitos de recusa:

Verifique se os destinatários de mensagens podem recusar mensagens futuras a qualquer momento. Você também deve oferecer várias opções de recusa. Após o destinatário da mensagem recusar, você não deverá enviar mensagens adicionais, a menos que o indivíduo forneça consentimento renovado.

Um dos mecanismos de recusa mais comuns é incluir uma palavra-chave para "PARAR" na mensagem inicial de cada nova conversa. Esteja preparado para remover clientes que responderem com "parar" em letras minúsculas ou com outras palavras-chave comuns, como "cancelar assinatura" ou "cancelamento". Após um indivíduo revogar o consentimento, você deverá removê-lo de todas as campanhas de mensagens recorrentes, a menos que ele opte expressamente por continuar recebendo mensagens de um programa específico.

### <a name="opt-out-best-practices"></a>Melhores práticas de recusa:

Além de palavras-chave, outros mecanismos comuns de recusa incluem fornecer aos clientes um endereço de email designado para recusa, o número de telefone da equipe de suporte ao cliente ou um link para cancelar a assinatura em sua página da Web. 


### <a name="how-we-handle-opt-out-requests"></a>Como tratamos solicitações de recusa:

Se um indivíduo solicitar a recusa de mensagens futuras de um número de chamada gratuita dos Serviços de Comunicação do Azure, todo o tráfego desse número será interrompido automaticamente. No entanto, você ainda precisa garantir que não enviará mensagens adicionais relacionadas a essa campanha de mensagens de números novos ou diferentes. Se você tiver obtido consentimento expresso separado para outra campanha de mensagens, poderá continuar enviando mensagens de um número diferente para essa campanha. Confira nossa seção de perguntas frequentes para saber mais sobre [Como tratar recusas](https://github.com/Prakulka/azure-docs-pr/blob/master/articles/communication-services/concepts/telephony-sms/sms-faq.md#how-can-i-receive-messages-using-azure-communication-services)

## <a name="message-content"></a>Conteúdo da mensagem

### <a name="adult-content"></a>Conteúdo para adulto:

Mensagens cujo conteúdo inclui elementos de sexo, ódio, álcool, armas de fogo, tabaco, jogos de azar ou sorteios e concursos podem exigir requisitos adicionais. Esse conteúdo é expressamente proibido em algumas jurisdições. Se você enviar uma mensagem incluindo esse conteúdo, será sua obrigação obedecer a todas as leis aplicáveis das jurisdições nas quais as comunicações forem recebidas. Mediante solicitação de órgãos de segurança pública ou dos Serviços de Comunicação do Azure, você deve estar preparado para fornecer prova de consentimento de acordo com a legislação local que regula conteúdo para adulto.

Mesmo que tal conteúdo não seja ilegal, você deverá incluir um mecanismo de verificação etária na aceitação para selecionar por idade os destinatários da mensagem pretendida com conteúdo para adulto. No Estados Unidos, requisitos legais adicionais se aplicam a comunicações de marketing direcionadas a crianças com menos de 13 anos de idade. 

### <a name="prohibited-content"></a>Conteúdo proibido:

Os Serviços de Comunicação do Azure proíbem determinados conteúdos de mensagem independentemente do consentimento. O conteúdo proibido inclui:
- Conteúdo que promove atividades ilegais (por exemplo, sonegação de impostos ou crueldade com animais nos Estados Unidos)
- Discurso de ódio, discurso difamatório, assédio ou outros discursos considerados claramente ofensivos
- Conteúdo pornográfico
- Conteúdo obsceno ou vulgar
- Intimidação e ameaças
- Conteúdo cuja finalidade seja fraudar, enganar, prejudicar ou obter indevidamente qualquer coisa de valor 
- Conteúdo que incita danos, discriminação ou violência
- Conteúdo que espalha malware
- Conteúdo que pretende contornar requisitos de seleção por idade

Reservamo-nos o direito de modificar a lista de conteúdos de mensagem proibidos a qualquer momento.

## <a name="spoofing"></a>Falsificação

Falsificação é o ato de fazer com que um número de origem enganoso ou incorreto seja exibido no dispositivo do destinatário de uma mensagem. Desencorajamos enfaticamente o envio de mensagens falsificadas por você e por qualquer provedor de serviços que você utilize. A falsificação encobre a identidade do remetente da mensagem e impede que os destinatários recusem com facilidade o recebimento de comunicações indesejadas. Também exigimos que você obedeça a todas as leis de falsificação aplicáveis.

## <a name="final-thoughts"></a>Considerações finais

### <a name="legal-responsibility"></a>Responsabilidade legal:

Esta Política de Mensagens não constitui orientação jurídica e nos reservamos o direito de modificá-la a qualquer momento. Os Serviços de Comunicação do Azure não são responsáveis por garantir que o conteúdo, a pontualidade ou os destinatários das mensagens de nossos clientes atendam a todos os requisitos legais aplicáveis. 

Nossos clientes são responsáveis por todos os requisitos de mensagens. Se você for um provedor de software ou uma plataforma que usa os Serviços de Comunicação do Azure para fins de envio de mensagens, deverá exigir que seus clientes também obedeçam a todos os requisitos abordados nesta Política de Mensagens. Para obter mais diretrizes, a CTIA fornece [Princípios e melhores práticas para o envio de mensagens](https://api.ctia.org/wp-content/uploads/2019/07/190719-CTIA-Messaging-Principles-and-Best-Practices-FINAL.pdf) que consideramos úteis.

### <a name="penalties"></a>Penalidades:

Incentivamos nossos clientes a desenvolver e implementar políticas e procedimentos com a finalidade de garantir a conformidade com todos os requisitos de mensagens. Violações dos requisitos de mensagens podem levar a multas substanciais que podem se somar rapidamente. É de seu interesse conhecer e obedecer a todos os requisitos de mensagens aplicáveis e desenvolver proteções de mitigação efetivas para conter e eliminar violações antes que elas se espalhem. Se você violar nossa Política de Mensagens ou outros requisitos legais, trabalharemos com você para garantir a conformidade futura. No entanto, nos reservamos o direito de remover da plataforma de Serviços de Comunicação do Azure qualquer cliente que demonstre um padrão de não conformidade com nossa Política de Mensagens ou requisitos legais.
