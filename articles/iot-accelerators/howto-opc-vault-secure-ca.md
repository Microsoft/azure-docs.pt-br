---
title: Como executar o serviço de gerenciamento de certificados do cofre OPC com segurança – Azure | Microsoft Docs
description: Descreve como executar o serviço de gerenciamento de certificados do cofre OPC com segurança no Azure e revisa outras diretrizes de segurança a serem consideradas.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f54467c03a67797d52ac499ca9ba455c0f75e240
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645950"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>Execute o serviço de gerenciamento de certificados do cofre OPC com segurança

> [!IMPORTANT]
> Enquanto atualizamos este artigo, confira [IoT Industrial do Azure](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

Este artigo explica como executar o serviço de gerenciamento de certificados do cofre do OPC com segurança no Azure e revisa outras diretrizes de segurança a serem consideradas.

## <a name="roles"></a>Funções

### <a name="trusted-and-authorized-roles"></a>Funções confiáveis e autorizadas

O microserviço do cofre OPC permite que funções distintas acessem várias partes do serviço.

> [!IMPORTANT]
> Durante a implantação, o script adiciona apenas o usuário que executa o script de implantação como um usuário para todas as funções. Para uma implantação de produção, você deve examinar essa atribuição de função e reconfigurar adequadamente seguindo as diretrizes abaixo. Essa tarefa requer atribuição manual de funções e serviços no portal de aplicativos empresariais do Azure Active Directory (Azure AD).

### <a name="certificate-management-service-roles"></a>Funções do serviço de gerenciamento de certificados

O microserviço do compartimento OPC define as seguintes funções:

- **Leitor**: por padrão, qualquer usuário autenticado no locatário tem acesso de leitura. 
  - Acesso de leitura a aplicativos e solicitações de certificado. Pode listar e consultar aplicativos e solicitações de certificado. Além disso, as informações de descoberta de dispositivo e os certificados públicos estão acessíveis com acesso de leitura.
- **Writer**: a função de gravador é atribuída a um usuário para adicionar permissões de gravação para determinadas tarefas. 
  - Acesso de leitura/gravação a aplicativos e solicitações de certificado. Pode registrar, atualizar e cancelar o registro de aplicativos. Pode criar solicitações de certificado e obter certificados e chaves privadas aprovadas. Também pode excluir chaves privadas.
- **Aprovador**: a função de aprovador é atribuída a um usuário para aprovar ou rejeitar solicitações de certificado. A função não inclui nenhuma outra função.
  - Além da função de aprovador para acessar a API de microserviço do compartimento OPC, o usuário também deve ter a permissão de assinatura de chave no Azure Key Vault para poder assinar os certificados.
  - A função de gravador e aprovador deve ser atribuída a usuários diferentes.
  - A função principal do aprovador é a aprovação da geração e rejeição de solicitações de certificado.
- **Administrador**: a função de administrador é atribuída a um usuário para gerenciar os grupos de certificados. A função não dá suporte à função de aprovador, mas inclui a função de gravador.
  - O administrador pode gerenciar os grupos de certificados, alterar a configuração e revogar certificados de aplicativos emitindo uma nova CRL (lista de certificados revogados).
  - Idealmente, as funções de gravador, Aprovador e administrador são atribuídas a diferentes usuários. Para segurança adicional, um usuário com a função de aprovador ou de administrador também precisa de permissão de assinatura de chave no Key Vault, para emitir certificados ou para renovar um certificado de autoridade de certificação do emissor.
  - Além da função de administração de microserviço, a função inclui, mas não está limitada a:
    - Responsabilidade pela administração da implementação das práticas de segurança da autoridade de certificação.
    - Gerenciamento da geração, revogação e suspensão de certificados. 
    - Gerenciamento do ciclo de vida da chave de criptografia (por exemplo, a renovação das chaves da AC do emissor).
    - Instalação, configuração e manutenção de serviços que operam a autoridade de certificação.
    - A operação diária dos serviços. 
    - Backup e recuperação de CA e banco de dados.

### <a name="other-role-assignments"></a>Outras atribuições de função

Considere também as seguintes funções quando você estiver executando o serviço:

- Proprietário de negócios do contrato de aquisição de certificado com a autoridade de certificação raiz externa (por exemplo, quando o proprietário adquire certificados de uma CA externa ou opera uma autoridade de certificação subordinada a uma AC externa).
- Desenvolvimento e validação da autoridade de certificação.
- Revisão de registros de auditoria.
- Funcionários que ajudam a dar suporte à autoridade de certificação ou a gerenciar os recursos físicos e de nuvem, mas não são diretamente confiáveis para executar operações de CA, estão na função *autorizada* . O conjunto de tarefas que as pessoas na função autorizada tem permissão para executar também devem ser documentados.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>Examine as associações de funções confiáveis e autorizadas trimestral

Examine a associação de funções confiáveis e autorizadas pelo menos trimestral. Verifique se o conjunto de pessoas (para processos manuais) ou identidades de serviço (para processos automatizados) em cada função é mantido em um mínimo.

### <a name="role-separation-between-certificate-requester-and-approver"></a>Separação de funções entre o solicitante de certificado e o aprovador

O processo de emissão de certificado deve impor a separação de funções entre o solicitante de certificado e as funções de aprovador de certificado (pessoas ou sistemas automatizados). A emissão de certificado deve ser autorizada por uma função de aprovador de certificado que verifica se o solicitante de certificado está autorizado a obter certificados. As pessoas que mantêm a função de aprovador de certificados devem ser uma pessoa autorizada formalmente.

### <a name="restrict-assignment-of-privileged-roles"></a>Restringir a atribuição de funções com privilégios

Você deve restringir a atribuição de funções com privilégios, como autorizar a associação do grupo Administradores e aprovadores, a um conjunto limitado de funcionários autorizados. Qualquer alteração de função privilegiada deve ter acesso revogado dentro de 24 horas. Por fim, revise as atribuições de função com privilégios trimestralmente e remova as atribuições desnecessárias ou expiradas.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>As funções com privilégios devem usar a autenticação de dois fatores

Use a autenticação multifator (também chamada de autenticação de dois fatores) para entradas interativas de aprovadores e administradores para o serviço.

## <a name="certificate-service-operation-guidelines"></a>Diretrizes de operação do serviço de certificado

### <a name="operational-contacts"></a>Contatos operacionais

O serviço de certificado deve ter um plano de resposta de segurança atualizado no arquivo, que contém contatos de resposta a incidentes operacionais detalhados.

### <a name="security-updates"></a>Atualizações de segurança

Todos os sistemas devem ser continuamente monitorados e atualizados com as últimas atualizações de segurança.

> [!IMPORTANT]
> O repositório GitHub do serviço de cofre do OPC é atualizado continuamente com patches de segurança. Monitore essas atualizações e aplique-as ao serviço em intervalos regulares.

### <a name="security-monitoring"></a>Monitoramento de segurança

Assine ou implemente o monitoramento de segurança apropriado. Por exemplo, assine uma solução de monitoramento central (como a central de segurança do Azure ou a solução de monitoramento de Microsoft 365) e configure-a adequadamente para garantir que os eventos de segurança sejam transmitidos para a solução de monitoramento.

> [!IMPORTANT]
> Por padrão, o serviço de cofre do OPC é implantado com o [aplicativo Azure insights](../azure-monitor/app/devops.md) como uma solução de monitoramento. É altamente recomendável adicionar uma solução de segurança como a [central de segurança do Azure](https://azure.microsoft.com/services/security-center/) .

### <a name="assess-the-security-of-open-source-software-components"></a>Avaliar a segurança de componentes de software livre

Todos os componentes de software livre usados em um produto ou serviço devem ser livres de vulnerabilidades de segurança moderadas ou mais.

> [!IMPORTANT]
> Durante as compilações de integração contínua, o repositório GitHub do serviço de cofre do OPC examina todos os componentes em busca de vulnerabilidades. Monitore essas atualizações no GitHub e aplique-as ao serviço em intervalos regulares.

### <a name="maintain-an-inventory"></a>Manter um inventário

Mantenha um inventário de ativos para todos os hosts de produção (incluindo máquinas virtuais persistentes), dispositivos, todos os intervalos de endereços IP internos, VIPs e nomes de domínio DNS públicos. Sempre que você adicionar ou remover um sistema, endereço IP do dispositivo, VIP ou domínio DNS público, deverá atualizar o inventário dentro de 30 dias.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Inventário da implantação de produção do microserviço do cofre OPC do Azure padrão 

No Azure:
- **Plano do serviço de aplicativo**: plano do serviço de aplicativo para hosts de serviço. Padrão S1.
- **Serviço de aplicativo** para o microserviço: o host do serviço de cofre do OPC.
- **Serviço** de aplicativo para aplicativo de exemplo: o host de aplicativo de exemplo do cofre OPC.
- **Key Vault Standard**: para armazenar segredos e chaves de Azure Cosmos DB para os serviços Web.
- **Key Vault Premium**: para hospedar as chaves de AC do emissor, para o serviço de assinatura e para a configuração do cofre e o armazenamento de chaves privadas do aplicativo.
- **Azure Cosmos DB**: banco de dados para solicitações de aplicativo e certificado. 
- **Application insights**: (opcional) monitoramento de solução para serviço Web e aplicativo.
- **Registro de aplicativo do Azure ad**: um registro para o aplicativo de exemplo, o serviço e o módulo de borda.

Para os serviços de nuvem, todos os nomes de host, grupos de recursos, nome de recurso, IDs de assinatura e IDs de locatário usados para implantar o serviço devem ser documentados. 

No Azure IoT Edge ou em um servidor de IoT Edge local:
- **Módulo do IOT Edge do OPC Vault**: para dar suporte a um servidor de descoberta global de OPC UA de rede de fábrica. 

Para os dispositivos IoT Edge, os nomes de host e os endereços IP devem ser documentados. 

### <a name="document-the-certification-authorities-cas"></a>Documentar as autoridades de certificação (CAs)

A documentação da hierarquia da autoridade de certificação deve conter todas as CAs operadas. Isso inclui todas as CAS subordinadas relacionadas, as CAs pai e as CAs raiz, mesmo quando elas não são gerenciadas pelo serviço. Em vez de documentação formal, você pode fornecer um conjunto completo de todos os certificados de autoridade de certificação não expirados.

> [!NOTE]
> O aplicativo de exemplo do cofre OPC dá suporte ao download de todos os certificados usados e produzidos no serviço para documentação.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Documentar os certificados emitidos por todas as autoridades de certificação (CAs)

Forneça um conjunto completo de todos os certificados emitidos nos últimos 12 meses.

> [!NOTE]
> O aplicativo de exemplo do cofre OPC dá suporte ao download de todos os certificados usados e produzidos no serviço para documentação.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>Documentar o procedimento operacional padrão para excluir com segurança as chaves de criptografia

Durante o tempo de vida de uma autoridade de certificação, a exclusão de chave pode ocorrer raramente. É por isso que nenhum usuário tem Key Vault direito de exclusão de certificado atribuído e por que não há APIs expostas para excluir um certificado de autoridade de certificação do emissor. O procedimento operacional padrão manual para excluir com segurança as chaves de criptografia da autoridade de certificação só está disponível acessando Key Vault diretamente no portal do Azure. Você também pode excluir o grupo de certificados em Key Vault. Para garantir a exclusão imediata, desabilite a funcionalidade de [exclusão reversível Key Vault](../key-vault/general/soft-delete-overview.md) .

## <a name="certificates"></a>Certificados

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>Os certificados devem estar em conformidade com o perfil de certificado mínimo

O serviço de cofre do OPC é uma AC online que emite certificados de entidade final para assinantes. O microserviço do compartimento OPC segue essas diretrizes na implementação padrão.

- Todos os certificados devem incluir os seguintes campos X. 509, conforme especificado abaixo:
  - O conteúdo do campo de versão deve ser v3. 
  - O conteúdo do campo serialNumber deve incluir pelo menos 8 bytes de entropia obtido de um gerador de número aleatório de FIPS (Federal Information Processing Standards) 140 aprovado.<br>
    > [!IMPORTANT]
    > O número de série do cofre OPC é, por padrão, 20 bytes e é obtido do gerador de número aleatório criptográfico do sistema operacional. O gerador de número aleatório é o FIPS 140 aprovado em dispositivos Windows, mas não no Linux. Considere isso ao escolher uma implantação de serviço que usa VMs Linux ou contêineres do Docker Linux, em que a tecnologia subjacente OpenSSL não é o FIPS 140 aprovado.
  - Os campos issuerUniqueID e subjectUniqueID não devem estar presentes.
  - Os certificados de entidade final devem ser identificados com a extensão de restrições básicas, de acordo com a RFC 5280 da IETF.
  - O campo pathLenConstraint deve ser definido como 0 para o certificado de AC emissora. 
  - A extensão de uso estendido de chave deve estar presente e deve conter o conjunto mínimo de OIDs (identificadores de objeto de uso de chave estendida). O OID anyExtendedKeyUsage (2.5.29.37.0) não deve ser especificado. 
  - A extensão de CDP (ponto de distribuição de CRL) deve estar presente no certificado de autoridade de certificação do emissor.<br>
    > [!IMPORTANT]
    > A extensão de CDP está presente em certificados de AC do cofre OPC. No entanto, os dispositivos OPC UA usam métodos personalizados para distribuir CRLs.
  - A extensão de acesso às informações da autoridade deve estar presente nos certificados do Assinante.<br>
    > [!IMPORTANT]
    > A extensão de acesso às informações da autoridade está presente nos certificados do assinante do OPC Vault. No entanto, os dispositivos OPC UA usam métodos personalizados para distribuir informações de AC do emissor.
- Algoritmos assimétricos aprovados, comprimentos de chave, funções de hash e modos de preenchimento devem ser usados.
  - O RSA e o SHA-2 são os únicos algoritmos com suporte.
  - A RSA pode ser usada para criptografia, troca de chaves e assinatura.
  - A criptografia RSA deve usar somente os modos de preenchimento OAEP, RSA-KEM ou RSA-PSS.
  - São necessários comprimentos de chave maiores ou iguais a 2048 bits.
  - Use a família SHA-2 de algoritmos de hash (SHA256, SHA384 e SHA512).
  - As chaves de AC raiz RSA com um tempo de vida típico maior ou igual a 20 anos devem ser de 4096 bits ou mais.
  - As chaves de AC do emissor RSA devem ter pelo menos 2048 bits. Se a data de expiração do certificado de autoridade de certificação for posterior a 2030, a chave da autoridade de certificação deverá ser de 4096 bits ou mais.
- Tempo de vida do certificado
  - Certificados de autoridade de certificação raiz: o período máximo de validade do certificado para autoridades de certificação raiz não deve exceder 25 anos.
  - Certificados de CA do emissor online ou da subautoridade de certificação: o período máximo de validade do certificado para CAs que estão online e emitem somente certificados do assinante não devem exceder 6 anos. Para essas CAs, a chave de assinatura privada relacionada não deve ser usada por mais de três anos para emitir novos certificados.<br>
    > [!IMPORTANT]
    > O certificado do emissor, como é gerado no microserviço do cofre do OPC padrão sem AC raiz externa, é tratado como uma subautoridade de certificação online, com requisitos e vidas de vida correspondentes. O tempo de vida padrão é definido como 5 anos, com um comprimento de chave maior ou igual a 2048.
  - Todas as chaves assimétricas devem ter um tempo de vida máximo de 5 anos e um tempo de vida de 1 ano recomendado.<br>
    > [!IMPORTANT]
    > Por padrão, os tempos de vida dos certificados de aplicativo emitidos com o cofre OPC têm um tempo de vida de dois anos e devem ser substituídos a cada ano. 
  - Sempre que um certificado é renovado, ele é renovado com uma nova chave.
- Extensões específicas do OPC UA em certificados de instância do aplicativo
  - A extensão subjectAltName inclui o URI do aplicativo e os nomes de host. Eles também podem incluir endereços FQDN, IPv4 e IPv6.
  - O KeyUsage inclui digitalSignature, nonrepúdio, keyEncipherment e dataEncipherment.
  - O extendedKeyUsage inclui serverAuth e clientAuth.
  - O authorityKeyIdentifier é especificado em certificados assinados.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>Chaves de CA e certificados devem atender aos requisitos mínimos

- **Chaves privadas**: chaves RSA devem ter pelo menos 2048 bits. Se a data de expiração do certificado de autoridade de certificação for posterior a 2030, a chave da autoridade de certificação deverá ser de 4096 bits ou mais.
- **Tempo de vida**: o período máximo de validade do certificado para CAS que estão online e que executam somente certificados de assinante não devem exceder 6 anos. Para essas CAs, a chave de assinatura privada relacionada não deve ser usada por mais de três anos para emitir novos certificados.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>As chaves de CA são protegidas usando módulos de segurança de hardware

O OpcVault usa Azure Key Vault Premium e as chaves são protegidas por HSM (módulos de segurança de hardware) do FIPS 140-2 nível 2. 

Os módulos de criptografia que Key Vault usam, sejam eles validados por FIPS ou software. As chaves criadas ou importadas como protegidas por HSM são processadas dentro de um HSM, validadas para o FIPS 140-2 nível 2. As chaves criadas ou importadas como protegidas por software são processadas dentro de módulos criptográficos validados para o FIPS 140-2 nível 1.

## <a name="operational-practices"></a>Práticas operacionais

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Documentar e manter as práticas de PKI operacional padrão para registro de certificado

Documente e mantenha os procedimentos operacionais padrão (SOPs) para saber como o CAs emite certificados, incluindo: 
- Como o assinante é identificado e autenticado. 
- Como a solicitação de certificado é processada e validada (se aplicável, inclua também como a renovação de certificado e as solicitações de rechaveamento são processadas). 
- Como os certificados emitidos são distribuídos para os assinantes. 

O SOP de microserviço do cofre OPC é descrito na [arquitetura do cofre OPC](overview-opc-vault-architecture.md) e [gerencia o serviço de certificado do cofre do OPC](howto-opc-vault-manage.md). As práticas seguem a "especificação de arquitetura unificada do OPC, parte 12: descoberta e serviços globais".


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Documentar e manter as práticas de PKI operacional padrão para revogação de certificado

O processo de revogação de certificado é descrito na [arquitetura do cofre OPC](overview-opc-vault-architecture.md) e [gerencia o serviço de certificado do cofre do OPC](howto-opc-vault-manage.md).
    
### <a name="document-ca-key-generation-ceremony"></a>Cerimônia de geração de chave de CA de documento 

A geração de chave de AC do emissor no microserviço do cofre OPC é simplificada, devido ao armazenamento seguro em Azure Key Vault. Para obter mais informações, consulte [gerenciar o serviço de certificado do cofre do OPC](howto-opc-vault-manage.md).

No entanto, quando você estiver usando uma autoridade de certificação raiz externa, uma cerimônia de geração de chave de CA deverá aderir aos requisitos a seguir.

A cerimônia de geração de chave de CA deve ser executada em um script documentado que inclui pelo menos os seguintes itens: 
- Definição de funções e responsabilidades do participante.
- Aprovação para conduta da cerimônia de geração de chave de CA.
- Hardware criptográfico e materiais de ativação necessários para a cerimônia.
- Preparação de hardware (incluindo atualização e desconexão de informações de ativo/configuração).
- Instalação do sistema operacional.
- Etapas específicas executadas durante a cerimônia de geração de chave de CA, como: 
  - Instalação e configuração do aplicativo de CA.
  - Geração de chave de CA.
  - Backup da chave de CA.
  - Assinatura de certificado de autoridade de certificação.
  - Importação de chaves assinadas no HSM protegido do serviço.
  - Desligamento do sistema da AC.
  - Preparação dos materiais para armazenamento.


## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como gerenciar com segurança o cofre OPC, você pode:

> [!div class="nextstepaction"]
> [Proteger dispositivos OPC UA com o cofre OPC](howto-opc-vault-secure.md)