---
title: Práticas de segurança para fabricantes – serviço de provisionamento de dispositivos do Azure IoT
description: Visões gerais das práticas de segurança comuns para OEMs e fabricantes de dispositivos que preparam dispositivos para se registrarem no DPS (serviço de provisionamento de dispositivos) do Azure IoT.
author: timlt
ms.author: timlt
ms.date: 3/02/2020
ms.topic: conceptual
ms.service: iot-dps
ms.custom: iot-p0-scenario, iot-devices-deviceOEM
ms.reviewer: nberdy
ms.openlocfilehash: 788738082cbf9995fb2f7282bc3f574903275528
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90527200"
---
# <a name="security-practices-for-azure-iot-device-manufacturers"></a>Práticas de segurança para fabricantes de dispositivos IoT do Azure
À medida que mais fabricantes lançam dispositivos IoT, é útil identificar diretrizes relacionadas a práticas comuns. Este artigo resume as práticas de segurança recomendadas a serem consideradas quando você fabrica dispositivos para uso com o serviço de provisionamento de dispositivos IoT do Azure (DPS).  

> [!div class="checklist"]
> * Selecionando opções de autenticação de dispositivo
> * Instalando certificados em dispositivos IoT
> * Integração de um Trusted Platform Module (TPM) ao processo de fabricação

## <a name="selecting-device-authentication-options"></a>Selecionando opções de autenticação de dispositivo
O objetivo final de qualquer medida de segurança de dispositivo IoT é criar uma solução de IoT segura. Mas problemas como limitações de hardware, custo e nível de experiência de segurança afetam as opções escolhidas. Além disso, sua abordagem de segurança afeta como seus dispositivos IoT se conectam à nuvem. Embora haja [vários elementos da segurança de IOT](https://www.microsoft.com/research/publication/seven-properties-highly-secure-devices/) a considerar, um elemento de chave que cada cliente encontra é o tipo de autenticação a ser usado. 

Três tipos de autenticação amplamente usados são certificados X. 509, TPM (Trusted Platform modules) e chaves simétricas. Embora existam outros tipos de autenticação, a maioria dos clientes que criam soluções no Azure IoT usam um desses três tipos. O restante deste artigo pesquisa prós e contras do uso de cada tipo de autenticação.

### <a name="x509-certificate"></a>Certificado X.509
Os certificados X. 509 são um tipo de identidade digital que você pode usar para autenticação. O padrão de certificado X. 509 está documentado em [IETF RFC 5280](https://tools.ietf.org/html/rfc5280). No Azure IoT, há duas maneiras de autenticar certificados:
- Digitais. Um algoritmo de impressão digital é executado em um certificado para gerar uma cadeia de caracteres hexadecimal. A cadeia de caracteres gerada é um identificador exclusivo para o certificado. 
- Autenticação de CA com base em uma cadeia completa. Uma cadeia de certificados é uma lista hierárquica de todos os certificados necessários para autenticar um certificado de entidade final (EE). Para autenticar um certificado do EE, é necessário autenticar cada certificado na cadeia, incluindo uma AC raiz confiável. 

Prós para X. 509:
- X. 509 é o tipo de autenticação mais seguro com suporte no Azure IoT.
- X. 509 permite um alto nível de controle para fins de gerenciamento de certificados.
- Muitos fornecedores estão disponíveis para fornecer soluções de autenticação baseadas em X. 509.

Contras de X. 509:
- Muitos clientes podem precisar contar com fornecedores externos para seus certificados.
- O gerenciamento de certificados pode ser dispendioso e agregado ao custo total da solução.
- O gerenciamento do ciclo de vida do certificado pode ser difícil se a logística não for bem considerada. 

### <a name="trusted-platform-module-tpm"></a>TPM (Trusted Platform Module)
O TPM, também conhecido como [ISO/IEC 11889](https://www.iso.org/standard/66510.html), é um padrão para gerar e armazenar chaves criptográficas com segurança. O TPM também se refere a um dispositivo de e/s físico ou virtual que interage com módulos que implementam o padrão. Um dispositivo TPM pode existir como hardware discreto, hardware integrado, um módulo baseado em firmware ou um módulo baseado em software. 

Há duas diferenças principais entre TPMs e chaves simétricas: 
- Os chips do TPM também podem armazenar certificados X. 509.
- O atestado de TPM no DPS usa a chave de endosso do TPM (EK), uma forma de autenticação assimétrica. Com a autenticação assimétrica, uma chave pública é usada para criptografia e uma chave privada separada é usada para descriptografia. Por outro lado, as chaves simétricas usam a autenticação simétrica, em que a chave privada é usada para criptografia e descriptografia. 

Prós para TPM:
- Os TPMs são incluídos como hardware padrão em muitos dispositivos Windows, com suporte interno para o sistema operacional. 
- O atestado de TPM é mais fácil de proteger do que o atestado de chave simétrica baseada em token de SAS (assinatura de acesso compartilhado).
- Você pode facilmente expirar e renovar, ou lançar, as credenciais do dispositivo. O DPS automaticamente reverte as credenciais do Hub IoT sempre que um dispositivo TPM está prestes a ser reprovisionado.

Contras do TPM: 
- TPMs são complexos e podem ser difíceis de usar. 
- O desenvolvimento de aplicativos com TPMs é difícil, a menos que você tenha um TPM físico ou um emulador de qualidade.
- Talvez seja necessário recriar o painel do seu dispositivo para incluir um TPM no hardware. 
- Se você reverter o EK em um TPM, ele destruirá a identidade do TPM e criará um novo. Embora o chip físico permaneça o mesmo, ele tem uma nova identidade em sua solução de IoT.

### <a name="symmetric-key"></a>Chave simétrica
Com chaves simétricas, a mesma chave é usada para criptografar e descriptografar mensagens. Como resultado, a mesma chave é conhecida pelo dispositivo e pelo serviço que a autentica. O Azure IoT dá suporte a conexões de chave simétrica baseadas em token SAS. A autenticação de chave simétrica requer uma responsabilidade de proprietário significativa para proteger as chaves e obter um nível igual de segurança com a autenticação X. 509. Se você usar chaves simétricas, a prática recomendada é proteger as chaves usando um módulo de segurança de hardware (HSM).

Prós para chave simétrica:
- O uso de chaves simétricas é a maneira mais simples e econômica de começar a usar a autenticação.
- O uso de chaves simétricas simplifica seu processo porque não há nada extra para gerar. 

Contras de chave simétrica: 
- As chaves simétricas assumem um grau significativo de esforço para proteger as chaves. A mesma chave é compartilhada entre o dispositivo e a nuvem, o que significa que a chave deve ser protegida em dois locais. Por outro lado, o desafio com os certificados TPM e X. 509 está comprovando a posse da chave pública sem revelar a chave privada.
- As chaves simétricas facilitam o acompanhamento de práticas de segurança inadequadas. Uma tendência comum com chaves simétricas é embutir em código as chaves não criptografadas nos dispositivos. Embora essa prática seja conveniente, ela deixa as chaves vulneráveis. Você pode mitigar alguns riscos armazenando com segurança a chave simétrica no dispositivo. No entanto, se sua prioridade for, em última análise, segurança, em vez de conveniência, use certificados X. 509 ou TPM para autenticação. 

### <a name="shared-symmetric-key"></a>Chave simétrica compartilhada
Há uma variação de autenticação de chave simétrica conhecida como chave simétrica compartilhada. Essa abordagem envolve o uso da mesma chave simétrica em todos os dispositivos. A recomendação é evitar o uso de chaves simétricas compartilhadas em seus dispositivos. 

Pro para chave simétrica compartilhada:
- Simples de implementar e barato para produzir em escala. 

Contras de chave simétrica compartilhada: 
- Altamente vulnerável a ataques. O benefício da implementação fácil é muito superado pelo risco. 
- Qualquer pessoa pode representar seus dispositivos se eles obtiverem a chave compartilhada.
- Se você depender de uma chave simétrica compartilhada que se torna comprometida, provavelmente perderá o controle dos dispositivos. 

### <a name="making-the-right-choice-for-your-devices"></a>Fazendo a escolha certa para seus dispositivos
Para escolher um método de autenticação, certifique-se de considerar os benefícios e os custos de cada abordagem para seu processo de fabricação exclusivo.  Para a autenticação de dispositivo, geralmente há uma relação inversa entre a segurança de uma determinada abordagem e a sua conveniência.  

## <a name="installing-certificates-on-iot-devices"></a>Instalando certificados em dispositivos IoT
Se você usar certificados X. 509 para autenticar seus dispositivos IoT, esta seção oferece orientação sobre como integrar certificados ao seu processo de fabricação. Você precisará tomar várias decisões.  Isso inclui decisões sobre variáveis de certificado comuns, quando gerar certificados e quando instalá-los. 

Se você estiver acostumado a usar senhas, poderá perguntar por que não é possível usar o mesmo certificado em todos os seus dispositivos, da mesma forma que você poderá usar a mesma senha em todos os seus dispositivos. Primeiro, usar a mesma senha em qualquer lugar é perigoso. A prática expôs as empresas a grandes ataques de DDoS, incluindo aquela que desligou o DNS na costa leste dos EUA há vários anos. Nunca use a mesma senha em qualquer lugar, mesmo com contas pessoais. Segundo, um certificado não é uma senha, é uma identidade exclusiva. Uma senha é como um código secreto que qualquer pessoa pode usar para abrir uma porta em um prédio seguro.  É algo que você sabe, e pode fornecer a senha a qualquer pessoa para obter a entrada.  Um certificado é como uma licença de driver com sua foto e outros detalhes, que você pode mostrar a uma proteção para entrar em um edifício seguro. Ele está vinculado a quem você está.  Desde que a proteção corresponda com precisão às pessoas com as licenças do driver, somente você pode usar sua licença (identidade) para obter a entrada. 

### <a name="variables-involved-in-certificate-decisions"></a>Variáveis envolvidas em decisões de certificado
Considere as variáveis a seguir e como cada uma afeta o processo de fabricação geral. 

#### <a name="where-the-certificate-root-of-trust-comes-from"></a>De onde provém a raiz do certificado de confiança
Pode ser dispendioso e complexo gerenciar uma PKI (infraestrutura de chave pública).  Especialmente se sua empresa não tiver nenhuma experiência para gerenciar uma PKI. As opções são:
- Use uma PKI de terceiros. Você pode comprar certificados de assinatura intermediários de um fornecedor de certificado de terceiros. Ou você pode usar uma autoridade de certificação (CA) privada. 
- Use uma PKI autogerenciada. Você pode manter seu próprio sistema PKI e gerar seus próprios certificados.
- Use o serviço de segurança [Azure Sphere](https://azure.microsoft.com/services/azure-sphere/) . Essa opção se aplica somente a dispositivos Azure Sphere. 

#### <a name="where-certificates-are-stored"></a>Onde os certificados são armazenados
Há alguns fatores que afetam a decisão sobre onde os certificados são armazenados. Esses fatores incluem o tipo de dispositivo, as margens de lucro esperadas (se você pode pagar armazenamento seguro), recursos de dispositivo e a tecnologia de segurança existente no dispositivo que você pode usar. Considere as seguintes opções:
- Em um módulo de segurança de hardware (HSM). O uso de um HSM é altamente recomendado. Verifique se o painel de controle do seu dispositivo já tem um HSM instalado. Se você souber que não tem um HSM, trabalhe com seu fabricante de hardware para identificar um HSM que atenda às suas necessidades.
- Em um local seguro no disco, como um sistema de execução confiável ("t").
- No sistema de arquivos local ou em um repositório de certificados. Por exemplo, o repositório de certificados do Windows. 

#### <a name="connectivity-at-the-factory"></a>Conectividade na fábrica
A conectividade na fábrica determina como e quando você obterá os certificados para instalar nos dispositivos. As opções de conectividade são as seguintes:
- Conectividade. Ter a conectividade ideal, simplifica o processo de geração de certificados localmente. 
- Sem conectividade. Nesse caso, você usa um certificado assinado de uma autoridade de certificação para gerar certificados de dispositivo localmente e offline. 
- Sem conectividade. Nesse caso, você pode obter certificados que foram gerados antes do tempo. Ou você pode usar uma PKI offline para gerar certificados localmente.

#### <a name="audit-requirement"></a>Requisito de auditoria
Dependendo do tipo de dispositivos que você produz, você pode ter um requisito normativo para criar uma trilha de auditoria de como as identidades de dispositivo são instaladas em seus dispositivos. A auditoria adiciona um custo de produção significativo. Portanto, na maioria dos casos, faça isso apenas se necessário. Se você não tiver certeza se uma auditoria é necessária, verifique com o departamento jurídico da sua empresa. As opções de auditoria são: 
- Não é um setor confidencial. Nenhuma auditoria é necessária.
- Setor confidencial. Os certificados devem ser instalados em uma sala segura de acordo com os requisitos de certificação de conformidade. Se você precisar de uma sala segura para instalar certificados, provavelmente já saberá como os certificados são instalados em seus dispositivos. E você provavelmente já tem um sistema de auditoria em vigor. 

#### <a name="length-of-certificate-validity"></a>Duração da validade do certificado
Assim como a licença de um driver, os certificados têm uma data de expiração que é definida quando são criadas. Aqui estão as opções de duração da validade do certificado:
- Renovação não necessária.  Essa abordagem usa um período de renovação longo, portanto, você nunca precisará renovar o certificado durante o tempo de vida do dispositivo. Embora essa abordagem seja conveniente, também é arriscada.  Você pode reduzir o risco usando o armazenamento seguro como um HSM em seus dispositivos. No entanto, a prática recomendada é evitar o uso de certificados de vida longa.
- Renovação necessária.  Você precisará renovar o certificado durante o tempo de vida do dispositivo. O comprimento da validade do certificado depende do contexto e você precisará de uma estratégia para a renovação.  A estratégia deve incluir onde você está recebendo certificados e o tipo de funcionalidade over-the-Air que seus dispositivos precisam usar no processo de renovação. 

### <a name="when-to-generate-certificates"></a>Quando gerar certificados
Os recursos de conectividade da Internet em sua fábrica afetarão o processo de geração de certificados. Você tem várias opções para quando gerar certificados: 

- Certificados pré-carregados.  Alguns fornecedores do HSM oferecem um serviço Premium no qual o fornecedor do HSM instala certificados para o cliente. Primeiro, os clientes fornecem ao fornecedor do HSM acesso a um certificado de autenticação. Em seguida, o fornecedor do HSM instala certificados assinados por esse certificado de autenticação em cada HSM que o cliente compra. Tudo o que o cliente precisa fazer é instalar o HSM no dispositivo. Embora esse serviço adicione custos, ele ajuda a simplificar o processo de fabricação.  E resolve a questão de quando instalar certificados.
- Certificados gerados pelo dispositivo.  Se os dispositivos gerarem certificados internamente, você deverá extrair o certificado X. 509 público do dispositivo para registrá-lo no DPS. 
- Fábrica conectada.  Se sua fábrica tiver conectividade, você poderá gerar certificados de dispositivo sempre que precisar deles.
- Fábrica offline com sua própria PKI. Se sua fábrica não tiver conectividade e você estiver usando sua própria PKI com suporte offline, você poderá gerar os certificados quando precisar deles.
- Fábrica offline com PKI de terceiros. Se sua fábrica não tiver conectividade e você estiver usando uma PKI de terceiros, você deverá gerar os certificados antecipadamente. E será necessário gerar os certificados de um local que tenha conectividade. 

### <a name="when-to-install-certificates"></a>Quando instalar certificados
Depois de gerar certificados para seus dispositivos IoT, você pode instalá-los nos dispositivos. 

Se você usar certificados pré-carregados com um HSM, o processo será simplificado. Depois que o HSM é instalado no dispositivo, o código do dispositivo pode acessá-lo. Em seguida, você chamará as APIs do HSM para acessar o certificado armazenado no HSM. Essa abordagem é a mais conveniente para seu processo de fabricação. 

Se você não usar um certificado pré-carregado, deverá instalar o certificado como parte do processo de produção. A abordagem mais simples é instalar o certificado no HSM ao mesmo tempo em que você pisca a imagem inicial do firmware. Seu processo deve adicionar uma etapa para instalar a imagem em cada dispositivo. Após essa etapa, você pode executar verificações de qualidade finais e outras etapas, antes de empacotar e enviar o dispositivo. 

Há ferramentas de software disponíveis que permitem executar o processo de instalação e a verificação de qualidade final em uma única etapa. Você pode modificar essas ferramentas para gerar um certificado ou para efetuar pull de um certificado de um repositório de certificados gerado previamente. Em seguida, o software pode instalar o certificado onde você precisa instalá-lo. As ferramentas de software desse tipo permitem que você execute a fabricação de qualidade de produção em escala. 

Depois que você tiver certificados instalados em seus dispositivos, a próxima etapa será aprender a registrar os dispositivos com o [DPS](about-iot-dps.md). 

## <a name="integrating-a-tpm-into-the-manufacturing-process"></a>Integrando um TPM ao processo de fabricação
Se você usar um TPM para autenticar seus dispositivos IoT, esta seção oferecerá diretrizes. A orientação aborda os dispositivos do TPM 2,0 amplamente usados que têm suporte à chave HMAC (código de autenticação de mensagem) baseado em hash. A especificação do TPM para chips TPM é um padrão ISO mantido pelo Trusted Computing Group. Para obter mais informações sobre o TPM, consulte as especificações para [tpm 2,0](https://trustedcomputinggroup.org/tpm-library-specification/) e [ISO/IEC 11889](https://www.iso.org/standard/66510.html). 

### <a name="taking-ownership-of-the-tpm"></a>Apropriando-se do TPM
Uma etapa crítica na fabricação de um dispositivo com um chip TPM é apropriar-se do TPM. Essa etapa é necessária para que você possa fornecer uma chave para o proprietário do dispositivo. A primeira etapa é extrair a chave de endosso (EK) do dispositivo. A próxima etapa é realmente reivindicar a propriedade. O modo de fazer isso depende de qual TPM e sistema operacional você usa. Se necessário, entre em contato com o fabricante do TPM ou com o desenvolvedor do sistema operacional do dispositivo para determinar como reivindicar a propriedade. 

Em seu processo de fabricação, você pode extrair a propriedade de EK e reivindicação em momentos diferentes, o que adiciona flexibilidade. Muitos fabricantes tiram proveito dessa flexibilidade adicionando um módulo de segurança de hardware (HSM) para aprimorar a segurança de seus dispositivos. Esta seção fornece orientação sobre quando extrair a EK, quando reivindicar a propriedade do TPM e considerações para integrar essas etapas em uma linha do tempo de fabricação. 

> [!IMPORTANT]
> As diretrizes a seguir pressupõem que você use um TPM discreto, de firmware ou integrado. Em locais onde ele é aplicável, a orientação adiciona observações sobre o uso de um TPM não discreto ou de software. Se você usar um TPM de software, poderá haver etapas adicionais que essas diretrizes não incluem. O software TPMs tem várias implementações que estão além do escopo deste artigo.  Em geral, é possível integrar um TPM de software na linha de tempo de fabricação geral a seguir. No entanto, embora um TPM emulado de software seja adequado para protótipos e testes, ele não pode fornecer o mesmo nível de segurança que um TPM discreto, de firmware ou integrado. Como uma prática geral, evite usar um TPM de software em produção.

### <a name="general-manufacturing-timeline"></a>Linha do tempo de fabricação geral
A linha de tempo a seguir mostra como um TPM passa por um processo de produção e termina em um dispositivo. Cada processo de fabricação é exclusivo e essa linha do tempo mostra os padrões mais comuns. A linha do tempo oferece orientação sobre quando executar determinadas ações com as chaves. 

#### <a name="step-1-tpm-is-manufactured"></a>Etapa 1: o TPM é fabricado
- Se você comprar o TPMs de um fabricante para uso em seus dispositivos, confira se eles extrairão as chaves de endosso públicas (EK_pubs) para você. Será útil se o fabricante fornecer a lista de EK_pubs com os dispositivos enviados. 
    > [!NOTE]
    > Você pode conceder ao fabricante do TPM acesso de gravação à sua lista de registro usando políticas de acesso compartilhado em seu serviço de provisionamento.  Essa abordagem permite que eles adicionem o TPMs à sua lista de registro para você.  Mas isso está no início do processo de fabricação e requer confiança no fabricante do TPM. Faça isso por sua conta e risco. 

- Se você fabrica o TPMs para vender para fabricantes de dispositivos, considere dar aos seus clientes uma lista de EK_pubs junto com seus TPMs físicos.  Fornecer aos clientes EK_pubs salva uma etapa em seu processo. 
- Se você fabrica o TPMs para usar com seus próprios dispositivos, identifique qual ponto em seu processo é o mais conveniente para extrair o EK_pub. Você pode extrair o EK_pub em qualquer um dos pontos restantes na linha do tempo. 

#### <a name="step-2-tpm-is-installed-into-a-device"></a>Etapa 2: o TPM é instalado em um dispositivo
Neste ponto do processo de produção, você deve saber em qual instância de DPS o dispositivo será usado. Como resultado, você pode adicionar dispositivos à lista de registro para provisionamento automatizado. Para obter mais informações sobre o provisionamento automático de dispositivos, consulte a [documentação do DPS](about-iot-dps.md).
- Se você ainda não extraiu o EK_pub, agora é um bom momento para fazer isso. 
- Dependendo do processo de instalação do TPM, essa etapa também é um bom momento para apropriar-se do TPM. 

#### <a name="step-3-device-has-firmware-and-software-installed"></a>Etapa 3: o dispositivo tem firmware e software instalados
Neste ponto do processo, instale o cliente do DPS junto com o escopo da ID e a URL global para provisionamento.
- Agora é a última chance de extrair o EK_pub. Se um terceiro instalar o software em seu dispositivo, será uma boa ideia extrair o EK_pub primeiro.
- Esse ponto no processo de fabricação é ideal para apropriar-se do TPM.  
    > [!NOTE]
    > Se você estiver usando um TPM de software, poderá instalá-lo agora.  Extraia o EK_pub ao mesmo tempo.

#### <a name="step-4-device-is-packaged-and-sent-to-the-warehouse"></a>Etapa 4: o dispositivo é empacotado e enviado para o depósito
Um dispositivo pode ficar em um depósito por 6-12 meses antes de ser implantado. 

#### <a name="step-5-device-is-installed-into-the-location"></a>Etapa 5: o dispositivo é instalado no local
Depois que o dispositivo chega em seu local final, ele passa pelo provisionamento automatizado com o DPS.

Para obter mais informações, consulte [provisionamento](about-iot-dps.md#provisioning-process) e [atestado de TPM](concepts-tpm-attestation.md). 

## <a name="resources"></a>Recursos

Além das práticas de segurança recomendadas neste artigo, o Azure IoT fornece recursos para ajudar na seleção de um hardware seguro e na criação de implantações de IoT seguras: 
- Recomendações de [segurança](../iot-fundamentals/security-recommendations.md) do Azure IOT para guiar o processo de implantação. 
- A [central de segurança do Azure](https://azure.microsoft.com/services/security-center/) oferece um serviço para ajudar a criar implantações de IOT seguras. 
- Para obter ajuda com a avaliação do seu ambiente de hardware, consulte o White Paper [avaliando sua segurança de IOT](https://download.microsoft.com/download/D/3/9/D3948E3C-D5DC-474E-B22F-81BA8ED7A446/Evaluating_Your_IOT_Security_whitepaper_EN_US.pdf). 
- Para obter ajuda com a seleção de hardware seguro, consulte [o hardware seguro correto para sua implantação de IOT](https://download.microsoft.com/download/C/0/5/C05276D6-E602-4BB1-98A4-C29C88E57566/The_right_secure_hardware_for_your_IoT_deployment_EN_US.pdf). 