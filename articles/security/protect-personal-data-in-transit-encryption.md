---
title: "Proteger dados pessoais em trânsito com a criptografia no Azure | Microsoft Docs"
description: Usando a criptografia no Azure para proteger dados pessoais
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 461ddfda796bfe6639e27f6c4cd53e82b4e397d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-encryption-technologies-protect-personal-data-in-transit-with-encryption"></a>Tecnologias de criptografia do Azure: proteger dados pessoais em trânsito com criptografia

Este artigo ajudará você a entender e usar as tecnologias de criptografia do Azure para proteger dados em trânsito. 

A proteção da privacidade de dados pessoais durante seu tráfego pela rede é uma parte essencial de uma estratégia de segurança de proteção avançada de várias camadas. A criptografia em trânsito foi projetada para impedir que um invasor que intercepta as transmissões consiga exibir ou usar os dados.

## <a name="scenario"></a>Cenário

Uma empresa de cruzeiro de grande porte, com sede nos Estados Unidos, está expandindo suas operações para oferecer roteiros nos mares Mediterrâneo, Adriático e Báltico, bem como nas Ilhas Britânicas. Para dar suporte a esses esforços, ela adquiriu várias linhas de cruzeiro menores localizadas na Itália, na Alemanha, na Dinamarca e no Reino Unido. 

A empresa usa o Microsoft Azure para armazenar dados corporativos na nuvem. Eles incluem informações de identificação pessoal, como nomes, endereços, números de telefone e informações de cartão de crédito de sua base global de clientes. Também incluem informações tradicionais de Recursos Humanos, como endereços, números de telefone, números de identificação fiscal e outras informações sobre os funcionários da empresa em todas as localizações. A linha de cruzeiro também mantém um banco de dados grande de membros do programa de recompensa e fidelidade que inclui informações pessoais para acompanhamento das relações com os clientes atuais e anteriores.

Os dados pessoais de clientes são inseridos no banco de dados em escritórios remotos da empresa e nos agentes de viagem localizados no mundo todo. Os documentos que contêm informações de clientes são transferidos pela rede para o armazenamento do Azure.

## <a name="problem-statement"></a>Problema declarado

A empresa deve proteger a privacidade dos dados pessoais de clientes e funcionários enquanto eles estão em trânsito entre os serviços do Azure.

## <a name="company-goal"></a>Meta da empresa

A meta da empresa é garantir que os dados pessoais sejam criptografados quando estiverem fora do disco. Caso pessoas não autorizadas interceptem dados pessoais fora do disco, eles devem estar em um formato que os tornará ilegíveis. A aplicação da criptografia deve ser fácil ou completamente transparente, para os usuários e administradores.

## <a name="solutions"></a>Soluções

Os serviços do Azure fornecem várias ferramentas e tecnologias para ajudá-lo a proteger dados pessoais em trânsito.

### <a name="azure-storage"></a>Armazenamento do Azure

Os dados armazenados na nuvem devem viajar do cliente, que pode estar localizado fisicamente em qualquer lugar do mundo, para o data center do Azure. Quando os dados são recuperados pelos usuários, eles viajam novamente, na direção oposta. Os dados em trânsito pela Internet pública sempre correm o risco de interceptação por invasores. É importante proteger a privacidade de dados pessoais usando a criptografia no nível de transporte para protegê-los enquanto eles se movem entre as localizações.

O protocolo HTTPS fornece um canal de comunicação seguro, criptografado pela Internet. O HTTPS deve ser usado para acessar objetos no Armazenamento do Azure e ao chamar APIs REST. Imponha o uso do protocolo HTTPS ao usar [SAS](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) (Assinaturas de Acesso Compartilhado) para delegar o acesso aos objetos do Armazenamento do Azure. Há dois tipos de SAS: SAS de Serviço e SAS de Conta.

#### <a name="how-do-i-construct-a-service-sas"></a>Como fazer para construir uma SAS de Serviço?

Uma SAS de serviço delega acesso a um recurso em apenas um dos serviços de armazenamento (serviço blob, fila, tabela ou arquivo). Para construir uma SAS de Serviço, faça o seguinte:

1. Especifique o campo de versão assinado

2. Especifique o recurso assinado (somente serviço Blob e Arquivo)

3. Especifique os parâmetros de consulta para substituir os cabeçalhos de resposta (somente serviço Blob e Arquivo)

4. Especifique o nome da tabela (somente serviço Tabela)

5. Especifique a política de acesso

6. Especifique o intervalo de validade da assinatura

8. Especifique as permissões

9. Especifique o endereço IP ou o intervalo de IP

10. Especifique o protocolo HTTP

11. Especifique os intervalos de acesso da tabela

12. Especifique o identificador assinado

13. Especifique a assinatura

Para obter instruções mais detalhadas, consulte [Construindo uma SAS de Serviço](https://docs.microsoft.com/rest/api/storageservices/Constructing-a-Service-SAS?redirectedfrom=MSDN).

#### <a name="how-do-i-construct-an-account-sas"></a>Como fazer para construir uma SAS de Conta?

Uma SAS de Conta delega acesso a recursos em um ou mais dos serviços de armazenamento. Você também pode delegar acesso a operações de leitura, gravação e exclusão em contêineres de blob, tabelas, filas e compartilhamentos de arquivos que não são permitidos com um SAS de serviço. A construção de uma SAS de Conta é semelhante a de uma SAS de Serviço. Para obter instruções detalhadas, consulte [Construindo uma SAS de Conta.](https://docs.microsoft.com/rest/api/storageservices/Constructing-an-Account-SAS?redirectedfrom=MSDN)

#### <a name="how-do-i-enforce-https-when-calling-rest-apis"></a>Como fazer para impor o HTTPS ao chamar APIs REST?

Para impor o uso do HTTPS ao chamar APIs REST para acessar objetos em contas de armazenamento, habilite o recurso Transferência Segura Obrigatória na conta de armazenamento. 

1. No portal do Azure, selecione **Criar Conta de Armazenamento** ou em uma conta de armazenamento existente, selecione **Configurações** e, em seguida, **Configuração**.

2. Em **Transferência Segura Obrigatória**, selecione **Habilitada**.

![Criando uma conta de armazenamento](media/protect-personal-data-in-transit-encryption/create-storage-account.png)

Para obter instruções mais detalhadas, incluindo como habilitar o recurso Transferência Segura Obrigatória de forma programática, consulte [Exigir a Transferência Segura](https://docs.microsoft.com/azure/storage/storage-require-secure-transfer).

#### <a name="how-do-i-encrypt-data-in-azure-file-storage"></a>Como fazer para criptografar dados no Armazenamento de Arquivos do Azure?

Para criptografar dados em trânsito com o [Armazenamento de Arquivos do Azure](https://docs.microsoft.com/azure/storage/storage-file-how-to-use-files-portal), use o SMB 3.x com o Windows 8, 8.1 e 10 e com o Windows Server 2012 R2 e o Windows Server 2016. Quando você estiver usando o serviço de Arquivos do Azure, qualquer conexão sem criptografia falhará quando "Transferência segura obrigatória" estiver habilitada. Isso inclui cenários usando SMB 2.1, SMB 3.0 sem criptografia e alguns tipos do cliente Linux SMB.

#### <a name="azure-client-side-encryption"></a>Criptografia do Lado do Cliente do Azure

Outra opção para proteger dados pessoais enquanto estão sendo transferidos entre um aplicativo cliente e o Armazenamento do Azure é a [Criptografia do Lado do Cliente](https://docs.microsoft.com/azure/storage/storage-client-side-encryption). Os dados são criptografados antes de serem transferidos para o Armazenamento do Azure e, ao recuperar os dados no Armazenamento do Azure, eles são descriptografados depois de serem recebidos no lado do cliente.

### <a name="azure-site-to-site-vpn"></a>VPN site a site do Azure

Uma maneira eficiente para proteger dados pessoais em trânsito entre uma rede corporativa ou um usuário e a rede virtual do Azure é usar uma VPN (Rede Virtual Privada) [site a site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) ou [ponto a site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal). Uma conexão VPN cria um túnel criptografado seguro pela Internet.

#### <a name="how-do-i-create-a-site-to-site-vpn-connection"></a>Como fazer para criar uma conexão VPN site a site?

Uma VPN site a site conecta vários usuários na rede corporativa com o Azure. Para criar uma conexão site a site no portal do Azure, faça o seguinte:

1. Crie uma rede virtual.

2. Especifique um servidor DNS.

3. Crie a sub-rede de gateway.

4. Crie o gateway de VPN. 

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-01.png)

5. Crie o gateway de rede local.

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-02.png)

6. Configure o dispositivo VPN.

7. Crie a conexão VPN.

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-03.png)

8. Verifique a conexão VPN.

Para obter instruções mais detalhadas sobre como criar uma conexão site a site no portal do Azure, consulte [Criar uma conexão Site a Site no Portal do Azure.] (https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

#### <a name="how-do-i-create-a-point-to-site-vpn-connection"></a>Como fazer para criar uma conexão VPN ponto a site?

Uma VPN Ponto a Site cria uma conexão segura de um computador cliente individual com uma rede virtual. Isso é útil quando você deseja se conectar ao Azure em um local remoto, como de casa, um hotel ou um centro de conferências. Para criar uma conexão ponto a site no portal do Azure:

1. Crie uma rede virtual.

2. Adicione uma sub-rede de gateway.

3. Especifique um servidor DNS. (opcional)

4. Crie um gateway de rede virtual.

5. Gere certificados.

6. Adicione o pool de endereços de cliente.

7. Carregue os dados de certificado público do certificado raiz.

8. Gere e instale o pacote de configuração de cliente VPN.

9. Instale um certificado do cliente exportado.

10. Conecte-se ao Azure.

11. Verifique a conexão.

Para obter instruções mais detalhadas, consulte [Configurar uma conexão Ponto a Site a uma VNet usando a autenticação de certificado: Portal do Azure.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)

### <a name="ssltls"></a>SSL/TLS

A Microsoft recomenda sempre usar protocolos SSL/TLS para a troca de dados em diferentes localizações. As organizações que optam por usar o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) para mover conjuntos de dados grandes em um link de WAN dedicado de alta velocidade também podem criptografar os dados no nível do aplicativo usando o SSL/TLS ou outros protocolos para proteção extra.

### <a name="encryption-by-default"></a>Criptografia por padrão

A Microsoft usa a criptografia para proteger dados em trânsito entre clientes e serviços de nuvem do Azure. Se você estiver interagindo com o Armazenamento do Azure pelo Portal do Azure, todas as transações ocorrerão via HTTPS.

O [protocolo TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) é o protocolo com o qual os data centers da Microsoft tentarão negociar com os sistemas cliente que se conectam aos serviços em nuvem da Microsoft. O TLS fornece autenticação forte, privacidade de mensagem e integridade (habilita a detecção de adulteração, interceptação e falsificação de mensagens), interoperabilidade, flexibilidade de algoritmo, facilidade de implantação e uso.

O [PFS](https://en.wikipedia.org/wiki/Forward_secrecy) (Perfect Forward Secrecy) também é utilizado para que cada conexão entre os sistemas cliente dos clientes e os serviços de nuvem da Microsoft usem chaves exclusivas. As conexões com os serviços em nuvem da Microsoft também aproveitam tamanhos de chave de criptografia de 2.048 bits baseada em RSA. A combinação do TLS, tamanhos de chave RSA de 2.048 bits e o PFS torna muito mais difícil para alguém interceptar e acessar dados em trânsito entre os clientes e os serviços em nuvem da Microsoft.

Os dados em trânsito são Always Encrypted no [Data Lake Store] (https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview). Além de criptografar os dados antes de armazenar em mídia persistente, os dados são sempre protegidos em trânsito usando HTTPS. HTTPS é o único protocolo com suporte para as interfaces REST do Data Lake Store.

## <a name="summary"></a>Resumo

A empresa pode atingir sua meta de proteger dados pessoais e a privacidade desses dados impondo conexões HTTPS ao Armazenamento do Azure, usando Assinaturas de Acesso Compartilhado e habilitando o recurso Transferência Segura Obrigatória nas contas de armazenamento. Ela também pode proteger os dados pessoais usando conexões SMB 3.0 e implementando a criptografia do lado do cliente. As conexões VPN site a site da rede corporativa para a rede virtual do Azure e as conexões VPN ponto a site dos usuários individuais criarão um túnel seguro por meio do qual os dados pessoais podem viajar com segurança. As práticas de criptografia padrão da Microsoft protegem ainda mais a privacidade de dados pessoais.

## <a name="next-steps"></a>Próximas etapas

- [Melhores práticas de segurança de dados e criptografia do Azure](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices)

- [Planejamento e design do Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

- [Perguntas frequentes de gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq)

- [Comprar e configurar um certificado SSL para o Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site)
