---
title: Como usar o Twilio para voz e SMS (Python) | Microsoft Docs
description: "Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Exemplos de códigos escritos em Python."
services: 
documentationcenter: python
author: devinrader
manager: twilio
editor: 
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: f4a02bb7a7c46e7a0e3c75b870c522eae8294339
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Como usar o Twilio para obter recursos de voz e SMS no Python
Este guia demonstra como executar tarefas comuns de programação com o serviço de API do Twilio no Azure. Os cenários abrangidos incluem fazer uma chamada telefônica e enviar uma mensagem serviço de mensagem curta (SMS). Para obter mais informações sobre o Twilio e o uso de voz e SMS em seus aplicativos, consulte a seção [Próximas etapas](#NextSteps) .

## <a id="WhatIs"></a>O que é Twilio?
Twilio é alimentar o futuro das comunicações de negócios, que permite aos desenvolvedores incorporar voz, VoIP e mensagens em aplicativos. Eles virtualizam toda a infra-estrutura necessária em um ambiente baseado em nuvem, global, expondo-as por meio da plataforma de comunicações API Twilio. Os aplicativos são simples de criar e dimensionável. Aproveite a flexibilidade com pagamento medida que vá preços e se beneficiar da confiabilidade da nuvem.

**Twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas.
**SMS da Twilio** permite que seu aplicativo envie e receba mensagens de texto.
**Cliente de Twilio** permite que você faça chamadas VoIP de qualquer telefone, tablet ou navegador e oferece suporte a WebRTC.

## <a id="Pricing"></a>Preços e ofertas especiais da Twilio
Os clientes do Azure recebem uma [oferta especial][special_offer] de US$ 10 de crédito do Twilio quando você atualiza sua conta do Twilio. Esse crédito de Twilio pode ser aplicado a qualquer uso de Twilio (equivalente a até 1.000 mensagens SMS de envio ou recebimento de até 1000 minutos de voz entrados, dependendo da localização do seu destino de chamada e mensagem ou número de telefone de crédito de US $10). Resgate esse [crédito do Twilio][special_offer] e comece a usar.

Twilio é um serviço flexível. Não há nenhuma taxa de configuração e você pode fechar sua conta a qualquer momento. Você pode encontrar mais detalhes em [Preços do Twilio][twilio_pricing].

## <a id="Concepts"></a>Conceitos
A API do Twilio é uma API RESTful que fornece os recursos de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas. Para obter uma lista, consulte [Bibliotecas de API do Twilio][twilio_libraries].

Os principais aspectos da API do Twilio são os verbos Twilio e a TwiML (Linguagem de Marcação do Twilio).

### <a id="Verbs"></a>Verbos da Twilio
A API usa os verbos do Twilio; por exemplo, o verbo **&lt;Say&gt;** instrui o Twilio a fornecer de forma audível uma mensagem em uma chamada.

A seguir está uma lista de verbos do Twilio. Saiba mais sobre os outros verbos e recursos na [Documentação da linguagem de marcação da Twilio][twiml].

* **&lt;Dial&gt;**: conecta o chamador com outro telefone.
* **&lt;Gather&gt;**: coleta os dígitos numéricos inseridos no teclado numérico de telefone.
* **&lt;Hangup&gt;**: termina uma chamada.
* **&lt;Pause&gt;**: espera silenciosamente por um número especificado de segundos.
* **&lt;Play&gt;**: reproduz um arquivo de áudio.
* **&lt;Fila&gt;**: adicionar a chamada a uma fila de chamadores.
* **&lt;Record&gt;**: grava a voz do chamador e retorna uma URL de um arquivo que contém a gravação.
* **&lt;Redirect&gt;**: transfere o controle de uma chamada ou SMS para TwiML em um URL diferente.
* **&lt;Reject&gt;**: rejeita uma chamada recebida para o número do Twilio sem cobrar você.
* **&lt;Say&gt;**: converte o texto em fala que é executada em uma chamada.
* **&lt;Sms&gt;**: envia uma mensagem SMS.

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções em XML com base nos verbos do Twilio que informam o Twilio como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML converteria a mensagem **Olá, mundo** em fala.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Quando o aplicativo chama a API Twilio, um dos parâmetros a API é a URL que retorna a resposta TwiML. Para fins de desenvolvimento, você pode usar URLs fornecidos Twilio para fornecer as respostas de TwiML usadas por seus aplicativos. Você também pode hospedar seus próprio URLs para produzir as respostas TwiML e outra opção é usar o objeto `TwiMLResponse`.

Para obter mais informações sobre os verbos do Twilio, seus atributos e o TwiML, consulte [TwiML][twiml]. Para obter mais informações sobre a API do Twilio, consulte [API do Twilio][twilio_api].

## <a id="CreateAccount"></a>Criar uma conta na Twilio
Quando você estiver pronto para obter uma conta do Twilio, inscreva-se em [Experimentar o Twilio][try_twilio]. Você pode começar com uma conta gratuita e atualizá-la depois.

Ao se inscrever em uma conta do Twilio, você receberá uma SID de conta e um token de autenticação. Eles serão necessários para fazer chamadas de API do Twilio. Para evitar o acesso não autorizado em sua conta, mantenha o token da autenticação seguro. A SID de sua conta e o token de autenticação estão visíveis no [Console do Twilio][twilio_console], nos campos rotulados **ACCOUNT SID** e **AUTH TOKEN**, respectivamente.

## <a id="create_app"></a>Criar um Aplicativo Python
Um aplicativo Python que usa o serviço do Twilio e está em execução no Azure não é diferente de qualquer outro aplicativo Python que usa o serviço do Twilio. Embora os serviços da Twilio sejam baseados em REST e possam ser chamados no Python de várias maneiras, este artigo se concentra em como usar os serviços da Twilio com a [Biblioteca da Twilio para Python no GitHub][twilio_python]. Para obter mais informações sobre como usar a biblioteca da Twilio para Python, consulte [http://readthedocs.org/docs/twilio-python/en/latest/index.html][twilio_lib_docs].

Primeiro, [configure uma nova VM Linux do Azure][azure_vm_setup] para agir como um host para o novo aplicativo Web do Python. Quando a Máquina Virtual está em execução, você precisará expor seu aplicativo em uma porta pública, conforme descrito abaixo.

### <a name="add-an-incoming-rule"></a>Adicionar Uma Regra de Entrada
  1. Vá para a página [Grupo de Segurança de Rede][azure_nsg].
  2. Selecione o grupo de segurança de rede que corresponde à sua máquina virtual.
  3. Adicione uma **regra de saída** para a **porta 80**. Certifique-se de permitir a entrada de qualquer endereço.

### <a name="set-the-dns-name-label"></a>Definir o rótulo do Nome DNS
  1. Vá para a página [Os Endereços IP Públicos][azure_ips].
  2. Selecione o IP Público que corresponde à sua máquina virtual.
  3. Defina o **Rótulo de Nome DNS** na seção **Configuração**. No caso deste exemplo, você verá algo semelhante a *rótulo-do-seu-domínio*.centralus.cloudapp.azure.com

Assim que você for capaz de se conectar à máquina virtual por meio de SSH, você poderá instalar a estrutura da Web de sua escolha (as duas mais conhecidas do Python são [Flask](http://flask.pocoo.org/) e [Django](https://www.djangoproject.com)). Você pode instalar qualquer um deles apenas executando o comando `pip install`.

Tenha em mente que podemos configurar a máquina virtual para permitir tráfego apenas na porta 80. Assim, certifique-se de configurar o aplicativo para usar essa porta.

## <a id="configure_app"></a>Configurar seu aplicativo para usar bibliotecas Twilio
Você pode configurar seu aplicativo para usar a biblioteca do Twilio para Python de duas maneiras:

* Instalar a biblioteca da Twilio para Python como um pacote Pip. Ela pode ser instalada com os seguintes comandos:
   
        $ pip install twilio

    -OU-

* Baixar a biblioteca do Twilio para o Python do GitHub ([https://github.com/twilio/twilio-python][twilio_python]) e instale-a assim:

        $ python setup.py install

Depois de instalar a biblioteca do Twilio para o Python, você pode então `import`-lo em seus arquivos de Python:

        import twilio

Para obter mais informações, consulte [https://github.com/twilio/twilio-python/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Como fazer uma chamada externa
A seguir você verá como fazer uma chamada de saída. Esse código também usa um site fornecido pelo Twilio para retornar a resposta TwiML (Linguagem de Marcação do Twilio). Substitua os valores dos números telefônico **from_number** e **to_number** e certifique-se de verificar o número telefônico **from_number** de sua conta do Twilio antes de executar o código.

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "http://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

Como mencionado, esse código utiliza um site fornecido pelo Twilio para retornar a resposta de TwiML. Em vez disso, você pode usar seu próprio site para fornecer a resposta TwiML. Para obter mais informações, consulte [Como fornecer respostas TwiML em seu próprio site](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Como enviar uma mensagem de SMS
O conteúdo abaixo mostra como enviar uma mensagem SMS usando a classe `TwilioRestClient`. O número **from_number** é fornecido pelo Twilio para contas de avaliação para enviar mensagens SMS. O número **to_number** deve ser verificado para sua conta de Twilio antes de executar o código.

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
    to_number = "NNNNNNNNNNN"
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Send the SMS message.
    message = client.messages.create(to=to_number,
                                     from_=from_number,
                                     body=message)

## <a id="howto_provide_twiml_responses"></a>Como fornecer respostas TwiML de seu próprio site
Quando o aplicativo inicia uma chamada para a API do Twilio, o Twilio enviará a solicitação a uma URL que deve retornar uma resposta TwiML. O exemplo acima usa a URL fornecida pelo Twilio [http://twimlets.com/message][twimlet_message_url]. (Embora o TwiML tenha sido criado para uso do Twilio, você pode exibi-lo em seu navegador. Por exemplo, clique em [http://twimlets.com/message][twimlet_message_url] para ver um elemento `<Response>` vazio. Como outro exemplo, clique em [http://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] para ver um elemento `<Response>` que contém um elemento `<Say>`.)

Em vez de contar com a URL fornecida pela Twilio, você pode criar seu próprio site que retorne respostas HTTP. Você pode criar o site em qualquer linguagem que retorna respostas XML; este tópico pressupõe que você usará Python para criar o TwiML.

Os exemplos a seguir terão como saída uma resposta em TwiML que diz **Hello World** na chamada.

Com o Flash:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

Com o Django:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

Como você pode ver no exemplo acima, a resposta de TwiML é simplesmente um documento XML. A biblioteca da Twilio para Python contém classes que geram TwiML para você. O exemplo a seguir produz a resposta equivalente conforme mostrado acima, mas usa o módulo `twiml` na biblioteca da Twilio para Python:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

Para obter mais informações sobre TwiML, consulte [https://www.twilio.com/docs/api/twiml][twiml_reference].

Uma vez que o aplicativo Python esteja configurado para fornecer respostas TwiML, use a URL do aplicativo como a URL passada para o método `client.calls.create`. Por exemplo, se você tiver um aplicativo Web chamado **MyTwiML** implantado em uma serviço hospedado do Azure, você poderá usar a URL como webhook, conforme mostrado no exemplo a seguir:

    from twilio.rest import TwilioRestClient

    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"
    from_number = "NNNNNNNNNNN"
    to_number = "NNNNNNNNNNN"
    url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url)
    print(call.sid)

## <a id="AdditionalServices"></a>Como Usar os Serviços Adicionais do Twilio
Além dos exemplos mostrados aqui, o Twilio oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do Twilio do aplicativo Azure. Para obter detalhes completos, consulte a [Documentação da API do Twilio][twilio_api].

## <a id="NextSteps"></a>Próximas etapas
Agora que você aprendeu as noções básicas do serviço Twilio, siga estes links para saber mais:

* [Diretrizes de segurança do Twilio][twilio_security_guidelines]
* [Código de Exemplo e Guias de Procedimentos do Twilio][twilio_howtos]
* [Tutoriais do Guia de início rápido do Twilio][twilio_quickstarts]
* [Twilio no GitHub][twilio_on_github]
* [Fale com o suporte do Twilio][twilio_support]

[special_offer]: http://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-python/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
