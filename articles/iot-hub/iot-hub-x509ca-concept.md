---
title: "Conceitos de segurança X.509 do Hub IoT do Azure | Microsoft Docs"
description: "Conceito – compreender os certificados de autoridade de certificação de valor X.509 na fabricação de dispositivo IoT e autenticação."
services: iot-hub
documentationcenter: .net
author: eustacea
manager: arjmands
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 34d4be431b76d5ba8258d932cb21ed6f4818863e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Entendimento conceitual de certificados de AC X.509 no setor de IoT

Este artigo descreve o valor do uso de certificados de autoridade de certificação (AC) X.509 na fabricação de dispositivos de IoT e autenticação no Hub IoT.  Ele inclui informações sobre a configuração da cadeia de fornecimento e realça as vantagens.

Este artigo descreve:

* O que são certificados de AC X.509 e como obtê-los
* Como registrar seu certificado de autoridade de certificação X.509 no Hub IoT
* Como configurar uma cadeia de fornecimento de fabricação para autenticação baseada na AC X.509
* Como os dispositivos assinados com a AC X.509 se conectam ao Hub IoT

## <a name="overview"></a>Visão geral

A autenticação da AC (autoridade de certificação) X.509 é uma abordagem para autenticar dispositivos no Hub IoT usando um método que simplifica bastante o gerenciamento de ciclo de vida e criação da identidade do dispositivo na cadeia de fornecimento.

Um atributo distinto da autenticação de AC X.509 é uma relação de um para muitos que um certificado de autoridade de certificação tem com seus dispositivos downstream.  Essa relação habilita o registro de qualquer quantidade de dispositivos no Hub IoT registrando um certificado de autoridade de certificação X.509 de uma vez, caso contrário, certificados de dispositivo exclusivos devem ser previamente registrados para cada dispositivo antes que um dispositivo possa se conectar. Essa relação de um para muitos também simplifica as operações de gerenciamento do ciclo de vida de certificados de dispositivo.

Outro atributo importante da autenticação de AC X.509 é a simplificação da logística da cadeia de fornecimento.  A autenticação segura de dispositivos requer que cada dispositivo tenha um segredo único como uma chave como base para a relação de confiança. Na autenticação baseada em certificados, esse segredo é uma chave privada. Um fluxo de dados de fabricação de dispositivo típico envolve várias etapas e responsáveis.  Gerenciar com segurança chaves privadas de dispositivo em vários responsáveis e manter a confiança é difícil e caro.  Usar autoridades de certificação resolve esse problema, inscrevendo cada responsável em uma cadeia de confiança de criptografia em vez de confiar a confiança deles com chaves privadas de dispositivo.  Cada responsável, por sua vez, inscreve dispositivos em sua respectiva etapa do processo do fluxo de fabricação.  O resultado geral é uma cadeia de fornecimento ideal com responsabilidade interna por meio do uso da cadeia de confiança de criptografia.  Vale a pena observar que esse processo resulta na maior parte da segurança quando os dispositivos protegem suas chaves privadas exclusivas.  Para esse fim, recomendamos enfaticamente o uso de HSM (Módulos Seguros de Hardware) capaz de gerar internamente chaves privadas que nunca verão a luz do dia.

Este artigo oferece uma exibição de ponta a ponta do uso da autenticação de AC X.509, da configuração da cadeia de fornecimento à conexão do dispositivo, ao fazer uso de um exemplo do mundo real para solidificar a compreensão.

## <a name="introduction"></a>Introdução

O certificado de autoridade de certificação X.509 é um certificado digital cujo proprietário pode assinar outros certificados.  Esse certificado digital é X.509 porque está em conformidade com uma formatação padrão de certificado prescrita pelo padrão RFC 5280 da IETF e é de uma autoridade de certificação (AC) porque seu proprietário pode assinar outros certificados.

O uso de AC X.509 é mais bem compreendido em relação a um exemplo concreto.  Considere a empresa X, um fabricante de widgets inteligentes X projetados para a instalação profissional. A empresa X terceiriza a instalação e a produção.  Ela contrata o fabricante Y para fabricar o widget inteligente X e contrata o provedor de serviço Técnico Z para instalar. A Empresa X deseja que o widget inteligente X seja enviado diretamente do fabricante Y para o Técnico-Z para a instalação e que ele se conecte diretamente à instância do Hub IoT da Empresa X após a instalação sem intervenção da Empresa X. Para fazer isso, a Empresa X precisará concluir algumas operações de configuração única para preparar o Widget inteligente X para conexão automática.  Com o cenário de ponta a ponta em mente, o restante deste artigo é estruturado da seguinte maneira:

* Adquirir o Certificado de Autoridade de Certificação X.509

* Registrar o Certificado de Autoridade de Certificação X.509 no Hub IoT

* Inscrever dispositivos em uma cadeia de certificados de confiança

* Conexão de dispositivo

## <a name="acquire-the-x509-ca-certificate"></a>Adquirir o Certificado de Autoridade de Certificação X.509

A Empresa X tem a opção de adquirir um Certificado de Autoridade de Certificação X.509 de uma autoridade de certificado raiz pública ou criando um através de um processo autoassinado.  Uma opção seria ideal em relação à outra, dependendo do cenário da aplicação.  Independentemente da opção, o processo envolve duas etapas fundamentais, gerar um par de chaves públicas/privadas e assinar a chave pública em um certificado.

![img-csr-flow](./media/csr-flow.png)

Detalhes sobre como realizar essas etapas diferem com vários provedores de serviço.

### <a name="purchasing-an-x509-ca-certificate"></a>Adquirir um Certificado de Autoridade de Certificação X.509

Adquirir um Certificado de Autoridade de Certificação tem a vantagem de ter uma AC raiz bem conhecida atuando como um terceiro confiável para garantir a legitimidade de dispositivos IoT quando os dispositivos se conectarem. A Empresa X poderá escolher esta opção se seu objetivo for que o Widget inteligente X interaja com produtos ou serviços de terceiros após a conexão inicial com o Hub IoT.

Para adquirir um Certificado de Autoridade de Certificação X.509, a Empresa X escolherá um provedor de serviços de certificados raiz. Uma pesquisa na Internet pela frase “AC raiz” produzirá ótimos clientes potenciais.  A AC raiz orientará a Empresa X sobre como criar o par de chaves públicas/privadas e como gerar uma CSR (Solicitação de assinatura de certificado) para seus serviços.  Uma CSR é o processo formal de se candidatar para um certificado de uma autoridade de certificação.  O resultado desta aquisição é um certificado para uso como um certificado de autoridade.  Por causa da ubiquidade de certificados X.509, é provável que o certificado tenha sido formatado corretamente de acordo com o padrão RFC 5280 da IETF.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Criar um Certificado de Autoridade de Certificação X.509 autoassinado

O processo para criar um Certificado de Autoridade de Certificação X.509 autoassinado é semelhante a comprar com a exceção de que envolve um assinante de terceiros, como a autoridade de certificação raiz. Em nosso exemplo, a Empresa X assinará o seu certificado de autoridade em vez de uma autoridade de certificação raiz.  A Empresa X pode escolher essa opção para teste até que esteja pronta para adquirir um certificado de autoridade. A Empresa X também pode usar um Certificado de Autoridade de Certificação X.509 autoassinado na produção, se o Widget inteligente X não for destinado a se conectar a todos os serviços de terceiros de fora do Hub IoT.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Registrar o certificado X.509 no Hub IoT

A Empresa X precisa registrar a AC X.509 no Hub IoT onde ela servirá para autenticar o Widget inteligente X como à medida que ele se conecta.  Este é um processo único que habilita a capacidade de autenticar e gerenciar qualquer quantidade de dispositivos de Widget inteligente X.  Esse processo é único devido a uma relação de um para muitos entre dispositivos e o certificado de autoridade e também é uma das principais vantagens de usar o método de autenticação de AC X.509.  A alternativa é carregar impressões digitais do certificado individuais para cada dispositivo do Widget inteligente X, aumentando assim os custos operacionais.

Registrar o Certificado de Autoridade de Certificação X.509 é um processo de duas etapas, o upload do certificado e prova de posse do certificado.

![img-pop-flow](./media/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>Upload do Certificado de Autoridade de Certificação X.509

O processo de upload do Certificado de Autoridade de Certificação X.509 é apenas isso, carregar o Certificado de Autoridade de Certificação no Hub IoT.  O Hub IoT espera o certificado em um arquivo. A Empresa X simplesmente carrega o arquivo de certificado. O arquivo de certificado NÃO DEVE em nenhuma circunstância conter nenhuma chave privada.  Práticas recomendadas de padrões que regem a PKI (Infraestrutura de chave pública) exigem que o conhecimento de dados privados da Empresa X nesse caso resida exclusivamente dentro da Empresa X.

### <a name="proof-of-possession-of-the-certificate"></a>Prova de posse do certificado

O Certificado de Autoridade de Certificação X.509, assim como qualquer certificado digital, é de informações públicas que são suscetíveis à interceptação.  Dessa maneira, um interceptador pode interceptar um certificado e tentar carregá-lo como seu próprio.  Em nosso exemplo, o Hub IoT gostaria de garantir que o Certificado de Autoridade de Certificação que a Empresa X está carregando realmente pertence à Empresa X. Ele o faz ao desafiar a Empresa X a provar que ela, de fato, tem o certificado por meio de um [fluxo de PoP (Prova de posse)](https://tools.ietf.org/html/rfc5280#section-3.1). O fluxo de prova de posse envolve o Hub IoT gerando um número aleatório a ser assinado pela Empresa-X usando sua chave privada.  Se a Empresa X seguiu as práticas recomendadas de PKI e protegeu sua chave privada, então, somente ela estaria na posição de responder corretamente ao desafio de prova de posse.  O Hub IoT prossegue para registrar o Certificado de Autoridade de Certificação X.509 após uma resposta bem-sucedida do desafio de prova de posse.

Uma resposta bem-sucedida para o desafio de prova de posse do Hub IoT conclui o registro da AC X.509.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Inscrever dispositivos em uma cadeia de certificados de confiança

O IoT requer que cada dispositivo possua uma identidade exclusiva.  Essas identidades estão na forma de certificados para esquemas de autenticação baseados em certificado.  Em nosso exemplo, isso significa que cada Widget inteligente X deve ter um certificado de dispositivo exclusivo.  Como a Empresa X se prepara para isso em sua cadeia de fornecimento?

Uma maneira de fazer isso é gerar previamente certificados para Widgets inteligentes X e passar a confiança do conhecimento de chaves privadas do dispositivo exclusivas correspondentes com parceiros da cadeia de fornecimento.  Para a Empresa X, isso significa confiar na Fábrica Y e no Técnico Z.  Embora esse seja um método válido, ele acompanha desafios que devem ser superados para garantir a confiança da seguinte maneira:

1. Ter de compartilhar as chaves privadas de dispositivo com parceiros da cadeia de fornecimento, além de ignorar práticas recomendadas de PKI de nunca compartilhar as chaves privadas, torna a criação da relação de confiança na cadeia de fornecimento cara.  Isso significa que sistemas de capital como salas de segurança para armazenar chaves privadas de dispositivo e processos como auditorias de segurança periódica precisam ser instalados.  Ambos aumentam o custo para a cadeia de fornecimento.

2. Responsabilizar-se com segurança por dispositivos na cadeia de fornecimento e posteriormente gerenciá-los na implantação se torna uma tarefa individual para cada par de chave para dispositivo do ponto de geração do certificado exclusivo do dispositivo (portanto, a chave privada,) até a desativação do dispositivo. Isso impede o gerenciamento de grupo de dispositivos, a menos que o conceito de grupos seja explicitamente criado no processo de alguma forma. Portanto, a responsabilidade segura e o gerenciamento de ciclo de vida do dispositivo tornam-se um fardo enorme para as operações.  Em nosso exemplo, a Empresa X assumiria esse fardo.

A autenticação de Certificado de Autoridade de Certificação X.509 oferece soluções elegantes para os desafios listados com o uso de cadeias confiáveis.  Uma cadeia de certificados resulta de uma AC assinando uma AC intermediária que, por sua vez, assina outra AC intermediária e assim por diante até que uma AC intermediária final assina um dispositivo.  Em nosso exemplo, a Empresa X assina a Fábrica-Y, que, por sua vez, assina o Técnico Z que assina finalmente o Widget inteligente X.

![img-cert-chain-hierarchy](./media/cert-chain-hierarchy.png)

A cascata de certificados acima na cadeia apresenta a transmissão de autoridade lógica.  Muitas cadeias de fornecimento seguem essa transmissão lógica em que cada AC intermediária é inserida na cadeia enquanto recebe todos os certificados de AC de upstream e a última AC intermediária finalmente assina cada dispositivo e insere todos os certificados de autoridade da cadeia no dispositivo. Isso é comum quando a empresa de fabricação contratada com uma hierarquia de fábricas contrata uma fábrica específica para fazer a fabricação.  Embora a hierarquia possa ter diversos níveis (por exemplo, por geografia/tipo de produto/linha de produção), somente a fábrica no final interage com o dispositivo, mas a cadeia é mantida desde o topo da hierarquia.

Cadeias alternativas podem ter uma interação de AC intermediária com o dispositivo diferente, nesse caso, AC que interage com o dispositivo injeta o conteúdo da cadeia de certificados nesse ponto.  Modelos híbridos também são possíveis em que somente algumas das AC têm interação física com o dispositivo.

Em nosso exemplo, tanto a Fábrica Y quanto o Técnico Z interagem com o Widget inteligente X.  Embora a Empresa X seja a proprietária do Widget inteligente X, na verdade, ela não interage fisicamente com ele na cadeia de fornecimento.  A cadeia de certificados de confiança para o Widget inteligente X, portanto, é composta pela Empresa X assinando a Fábrica Y, que, por sua vez, assina o Técnico Z, que fornecerá a assinatura final para o Widget inteligente X. A fabricação e a instalação do Widget inteligente X incluem a Fábrica Y e o Técnico Z usando seus respectivos certificados de AC intermediária para assinar cada Widget inteligente X. O resultado final de todo esse processo é Widgets inteligentes X com certificados de dispositivo exclusivos e a cadeia de certificados de confiança avançando até o certificado de Autoridade de certificação da Empresa X.

![img-cert-mfr-chain](./media/cert-mfr-chain.png)

Esse é um bom ponto para analisar o valor do método de AC X.509.  Em vez de gerar previamente e transmitir os certificados para cada Widget inteligente X na cadeia de fornecimento, a Empresa X assina a Fábrica-Y apenas uma vez.  Em vez de precisar controlar todos os dispositivos em todo o ciclo de vida de dispositivos, a Empresa X não precisa controlar e gerenciar dispositivos por meio de grupos que naturalmente surgem do processo de cadeia de fornecimento, por exemplo, dispositivos instalados pelo Técnico Z depois de julho de algum ano.

Por último, mas não menos importante, o método de AC de autenticação introduz a responsabilidade segura sobre a cadeia de fornecimento de fabricação do dispositivo. Por causa do processo de cadeia de certificados, as ações de cada membro da cadeia são registradas criptograficamente e podem ser verificadas.

Esse processo depende de determinadas suposições que devem ser apresentadas para fins de integridade.  Isso requer a criação independente do par de chaves pública/privada exclusivo do dispositivo e que a chave privada seja protegida no dispositivo.  Felizmente, existem chips de silicone seguros na forma de HSM (Módulos Seguros de Hardware) capazes de gerar chaves internamente e proteger as chaves privadas.  A Empresa X somente precisa adicionar um desses chips na lista de materiais de componentes do Widget inteligente X.

## <a name="device-connection"></a>Conexão de Dispositivo

As seções anteriores acima foram criadas para a conexão do dispositivo.  Apenas registrando um certificado de autoridade de certificação X.509 no Hub IoT uma vez, como possivelmente milhões de dispositivos se conectam e são autenticados pela primeira vez?  Simples; por meio do mesmo upload de certificado e do fluxo de prova de posse de que falamos anteriormente com o registro do certificado de autoridade de certificação X.509.

Dispositivos fabricados para autenticação de AC X.509 são equipados com certificados de dispositivo exclusivos e uma cadeia de certificados de sua respectiva cadeia de fornecimento de fabricação.  A conexão do dispositivo, mesmo pela primeira vez, acontece em um processo de duas etapas: o upload da cadeia de certificados e a prova de posse.

Durante o upload da cadeia de certificados, o dispositivo carrega seu certificado de dispositivo exclusivo junto com a cadeia de certificados instalados em seu Hub IoT.  Usando o certificado de autoridade de certificação X.509 previamente registrado, o Hub IoT pode validar criptograficamente algumas coisas, que a cadeia de certificados carregada é consistente internamente e que a cadeia foi originada pelo proprietário válido do certificado de autoridade de certificação X.509.  Somente após o processo de registro da AC X.509 o Hub IoT pode iniciar um processo de resposta do desafio de prova de posse para determinar que a cadeia e, portanto, o certificado de dispositivo realmente pertencem ao dispositivo que os carrega.  Isso é feito através da geração de um desafio aleatório a ser assinado pelo dispositivo usando sua chave privada para validação pelo Hub IoT.   Uma resposta bem-sucedida aciona o Hub IoT para aceitar o dispositivo como autêntico e conceda a ele a conexão.

Em nosso exemplo, cada Widget inteligente X deve carregar seu certificado de dispositivo exclusivo junto com certificados de AC X.509 da Fábrica Y e do Técnico Z e, em seguida, responder ao desafio de prova de posse do Hub IoT.

![img-device-pop-flow](./media/device-pop-flow.png)

Observe que a base da confiança depende da proteção de chaves privadas, incluindo as chaves privadas de dispositivo.  Portanto, nunca é demais enfatizar a importância de chips de silicone seguros na forma de HSM (Módulos Seguros de Hardware) para proteger as chaves privadas de dispositivo e da prática recomendada geral de nunca compartilhar nenhuma chave privada, como quando uma fábrica confia em outra sua chave privada.

