---
title: Criptografia - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: "atenuações de ameaças expostas na Ferramenta de Modelagem de Ameaças"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 96e74371fe51a8050a91c86215e3eefab07bbed8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="security-frame-cryptography--mitigations"></a>Estrutura de segurança: Criptografia | Atenuações 
| Produto/serviço | Artigo |
| --------------- | ------- |
| **Aplicativo Web** | <ul><li>[Usar somente codificações de bloco simétricas e comprimentos de chave aprovados](#cipher-length)</li><li>[Usar modos de codificação de bloco aprovados e vetores de inicialização para codificações simétricas](#vector-ciphers)</li><li>[Usar algoritmos assimétricos, comprimentos de chave e preenchimentos aprovados](#padding)</li><li>[Usar um número aleatório de geradores aprovados](#numgen)</li><li>[Não usar codificações de fluxo simétricas](#stream-ciphers)</li><li>[Usar algoritmos MAC, HMAC e de hash com chave](#mac-hash)</li><li>[Usar apenas as funções aprovadas do hash criptográfico](#hash-functions)</li></ul> |
| **Banco de dados** | <ul><li>[Usar algoritmos de criptografia forte para criptografar dados no banco de dados](#strong-db)</li><li>[Os pacotes do SSIS devem ser criptografados e assinados digitalmente](#ssis-signed)</li><li>[Adicionar assinatura digital a protegíveis de banco de dados críticos](#securables-db)</li><li>[Usar EKM do SQL Server para proteger chaves de criptografia](#ekm-keys)</li><li>[Usar o recurso AlwaysEncrypted para não revelar as chaves de criptografia ao mecanismo de banco de dados](#keys-engine)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Armazenar chaves de criptografia com segurança no dispositivo IoT](#keys-iot)</li></ul> | 
| **Gateway de Nuvem IoT** | <ul><li>[Gerar uma chave simétrica aleatória com um tamanho suficiente para autenticação no Hub IoT](#random-hub)</li></ul> | 
| **Cliente móvel do Dynamics CRM** | <ul><li>[Garantir que uma política de gerenciamento de dispositivos esteja aplicada, que ela exija um PIN de uso e permita a limpeza remota do dispositivo](#pin-remote)</li></ul> | 
| **Cliente do Outlook do Dynamics CRM** | <ul><li>[Garantir que uma política de gerenciamento de dispositivos esteja aplicada, que exija PIN, senha ou bloqueio automático e criptografe todos os dados (por exemplo, o BitLocker)](#bitlocker)</li></ul> | 
| **Identity Server** | <ul><li>[Garantir que as chaves de assinatura sejam trocadas quando o Servidor de identidade for usado](#rolled-server)</li><li>[Garantir que ID de cliente e segredo do cliente criptograficamente fortes sejam usados no Identity Server](#client-server)</li></ul> | 

## <a id="cipher-length"></a>Usar somente codificações de bloco simétricas e comprimentos de chave aprovados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | <p>Os produtos devem usar somente essas codificações de bloco simétricas e os comprimentos de chave associados que foram explicitamente aprovados pelo Supervisor de criptografia em sua organização. Os algoritmos simétricos aprovados na Microsoft incluem as seguintes codificações de bloco:</p><ul><li>Para um novo código, AES-128, AES 192 e AES-256 são aceitáveis.</li><li>Para garantir a compatibilidade com versões anteriores do código existente, a chave tripla 3DES é aceitável.</li><li>Para os produtos que usam codificações de bloco simétricas:<ul><li>A criptografia AES (Advanced Encryption Standard) é necessária para novos códigos.</li><li>O padrão de criptografia 3DES (Triple Data Encryption Standard) com três chaves é permitido em códigos existentes e para garantir a compatibilidade com versões anteriores.</li><li>Todas as outras codificações de bloco, incluindo RC2, DES, 3DES com duas chaves, DESX e Skipjack, só podem ser usadas para descriptografar os dados antigos e devem ser substituídas se utilizadas para criptografia.</li></ul></li><li>Para algoritmos de criptografia de bloco simétrica, é preciso ter um comprimento mínimo de chave de 128 bits. O algoritmo de criptografia de bloco único recomendado para o novo código é o AES (AES-128, AES-192 e AES-256 são aceitáveis).</li><li>O 3DES com três chaves é atualmente aceitável se já estiver em uso no código existente. É recomendável mudar para AES. Os algoritmos DESX, DES, RC2 e Skipjack não são mais considerados seguros. Eles só devem ser usados para descriptografar os dados existentes por conta de sua compatibilidade com versões anteriores. Depois disso, os dados devem ser criptografados novamente com uma codificação de bloco recomendada.</li></ul><p>Observe que todas as codificações de bloco simétricas devem ser usadas com um modo de codificação aprovado, que exige o uso de um vetor de inicialização (IV) apropriado. Normalmente, um IV apropriado é um número aleatório e nunca um valor constante.</p><p>O uso de algoritmos de criptografia herdados ou não aprovados e de comprimentos de chave menores para ler dados existentes (em vez de gravar novos dados) pode ser permitido após a revisão dos especialistas em criptografia de sua organização. No entanto, você deve solicitar uma exceção para esse requisito. Além disso, em implantações empresariais, os produtos devem avisar os administradores quando uma criptografia fraca for usada para ler dados. Esses avisos devem ser explicativos e acionáveis. Em alguns casos, pode ser apropriado deixar que a Política de Grupo controle o uso de criptografia fraca.</p><p>Algoritmos do .NET permitidos para agilidade criptográfica gerenciada (em ordem de preferência)</p><ul><li>AesCng (em conformidade com FIPS)</li><li>AuthenticatedAesCng (em conformidade com FIPS)</li><li>AESCryptoServiceProvider (em conformidade com FIPS)</li><li>AESManaged (sem conformidade com FIPS)</li></ul><p>Observe que nenhum desses algoritmos pode ser especificado com o método `SymmetricAlgorithm.Create` ou `CryptoConfig.CreateFromName` sem que o arquivo machine.config seja alterado. Observe também que o AES nas versões do .NET anteriores ao .NET 3.5 é denominado `RijndaelManaged`, e que `AesCng` e `AuthenticatedAesCng` estão disponíveis no CodePlex e exigem CNG no sistema operacional subjacente.</p>

## <a id="vector-ciphers"></a>Usar modos de codificação de bloco aprovados e vetores de inicialização para codificações simétricas

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Todas as codificações de bloco simétricas devem ser usadas com um modo de decodificação simétrica aprovado. Os únicos modos aprovados são CBC e CTS. Em particular, o modo de livro de código eletrônico (ECB) de operação deve ser evitado. Seu uso exige a revisão dos especialistas em criptografia de sua organização. Todos os usos de OFB, CFB, CTR, CCM e GCM, ou de qualquer outro modo de criptografia, devem revisados pelos especialistas em criptografia de sua organização. Reutilizar o mesmo vetor de inicialização (IV) com codificações de bloco em "modos de codificações de streaming", como CTR, pode fazer com que dados criptografados sejam revelados. Todas as codificações de bloco simétricas também devem ser usadas com IV apropriado, que é um número aleatório criptograficamente forte e nunca um valor constante. |

## <a id="padding"></a>Usar algoritmos assimétricos, comprimentos de chave e preenchimentos aprovados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | <p>O uso de algoritmos criptográficos proibidos gera um risco significativo para a segurança do produto e deve ser evitado. Os produtos devem usar somente os algoritmos de criptografia, os comprimentos de chave associados e o preenchimento que foram explicitamente aprovados pelos especialistas em criptografia de sua organização.</p><ul><li>**RSA -** pode ser usada para assinatura, troca de chaves e criptografia. A criptografia RSA deve usar somente os modos de preenchimento OAEP ou RSA-KEM. O código existente pode usar o modo de preenchimento PKCS #1 v 1.5 somente para fins de compatibilidade. O uso do preenchimento nulo é expressamente proibido. É necessário usar chaves com 2048 bits ou mais para um novo código. O código existente pode oferecer suporte a chaves com menos de 2048 bits somente para garantir a compatibilidade com versões anteriores após a revisão dos especialistas em criptografia de sua organização. As chaves com menos de 1024 só podem ser usadas para descriptografar ou verificar dados antigos e devem ser substituídas para operações de criptografia ou assinatura.</li><li>**ECDSA -** pode ser usada somente para assinatura. É necessário o ECDSA com chaves de 256 bits ou mais para um novo código. As assinaturas baseadas em ECDSA devem usar uma das três curvas NIST aprovadas (P-256, P-384 ou P521). As curvas que foram minuciosamente analisada poderão ser usadas somente após uma revisão dos especialistas em criptografia de sua organização.</li><li>**ECDH -** pode ser usada somente para troca de chaves. É necessário o ECDH com chaves de 256 bits ou mais para um novo código. As trocas de chaves baseadas em ECDH devem usar uma das três curvas NIST aprovadas (P-256, P-384 ou P521). As curvas que foram minuciosamente analisada poderão ser usadas somente após uma revisão dos especialistas em criptografia de sua organização.</li><li>**DSA -** pode ser aceitável após revisão e aprovação dos especialistas em criptografia de sua organização. Entre em contato com o consultor de segurança para agendar uma revisão com os especialistas em criptografia de sua organização. Se o uso da DSA for aprovado, você precisará proibir o uso de chaves com menos de 2048 bits de comprimento. A CNG oferece suporte a chaves com 2048 bits ou mais a partir do Windows 8.</li><li>**Diffie-Hellman -** pode ser usada somente para o gerenciamento de chaves de sessão. É necessário usar chaves com 2048 bits ou mais para um novo código. O código existente pode dar suporte a chaves com menos de 2048 bits somente para garantir a compatibilidade com versões anteriores, após um exame do Conselho de criptografia de sua organização. Chaves com menos de 1024 bits não podem ser usadas.</li><ul>

## <a id="numgen"></a>Usar um número aleatório de geradores aprovados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | <p>Os produtos devem usar um número aleatório de geradores aprovados. Funções pseudoaleatórias, como o aleatório da função de tempo de execução C, a classe System.Random do .NET Framework ou funções de sistema, como GetTickCount, nunca devem ser usadas em um código como esse. O uso do algoritmo do gerador de número aleatório de curva elíptica dupla (DUAL_EC_DRBG) é proibido.</p><ul><li>**CNG-** BCryptGenRandom (é recomendável usar o sinalizador BCRYPT_USE_SYSTEM_PREFERRED_RNG, exceto se o chamador executar um IRQL maior que 0 [PASSIVE_LEVEL])</li><li>**CAPI-** cryptGenRandom</li><li>**Win32/64-** RtlGenRandom (as novas implementações devem usar BCryptGenRandom ou CryptGenRandom) * rand_s * SystemPrng (para o modo kernel)</li><li>**.NET-** RNGCryptoServiceProvider ou RNGCng</li><li>**Aplicativos da Windows Store-** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom ou .GenerateRandomNumber</li><li>**Apple OS X (10.7+)/iOS(2.0+)-** int SecRandomCopyBytes (SecRandomRef random, size_t count, uint8_t *bytes )</li><li>**Apple OS X (<10.7)-** Usar /dev/random para recuperar números aleatórios</li><li>**Java(including Google Android Java Code) –** java.security.SecureRandom class. (observe que para o Android 4.3 (Jelly Bean), os desenvolvedores devem seguir a solução alternativa recomendada para o Android e atualizar os aplicativos para inicializar explicitamente a PRNG com entropia de /dev/urandom ou /dev/random)</li></ul>|

## <a id="stream-ciphers"></a>Não usar codificações de fluxo simétricas

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Codificações de stream simétricas, como RC4, não devem ser usadas. Em vez de codificações de stream simétricas, os produtos devem usar uma codificação de bloco, especificamente a AES com uma chave de pelo menos 128 bits. |

## <a id="mac-hash"></a>Usar algoritmos MAC, HMAC e de hash com chave

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | <p>Os produtos devem usar somente algoritmos de código de autenticação de mensagem (MAC) ou de código de autenticação de mensagem baseada em hash (HMAC) aprovados.</p><p>Um código de autenticação de mensagem (MAC) é um trecho de informação anexado a uma mensagem que permite ao destinatário verificar a autenticidade do remetente e a integridade da mensagem usando uma chave secreta. O uso de qualquer MAC baseado em hash ([HMAC](http://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) ou [MAC baseado em codificação de bloco](http://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) é permitido, desde que todos os hash subjacente ou algoritmos de criptografia simétrica também estejam aprovados para uso. Isso atualmente inclui as funções HMAC-SHA2 (HMAC-SHA256, HMAC-SHA384 e HMAC-SHA512) e os MACs baseados em codificação de bloco CMAC/OMAC1 e OMAC2 (esses são baseados na AES).</p><p>O uso de HMAC-SHA1 pode ser permitido para garantir a compatibilidade com a plataforma, mas você precisará emitir uma exceção para esse procedimento e ser submetido à revisão dos especialistas em criptografia de sua organização. Não é permitido truncar HMACs para menos de 128 bits. Usar métodos de clientes para hash de chave e dados não é permitido. Eles precisam ser revisados pelos especialistas em criptografia de sua organização antes do serem usados.</p>|

## <a id="hash-functions"></a>Usar apenas as funções aprovadas do hash criptográfico

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | <p>Os produtos devem usar a família SHA-2 de algoritmos de hash (SHA256, SHA384 e SHA512). Se for necessário usar um hash mais curto, com um comprimento de saída de 128 bits para ajustá-lo a uma estrutura de dados criada com o hash MD5 menor em mente, as equipes de produtos podem truncar um dos hashes SHA2 (normalmente o SHA256). Observe que o SHA384 é uma versão truncada do SHA512. Por motivos de segurança, não é permitido truncar hashes criptográficos para menos de 128 bits. O novo código não deve usar os algoritmos de hash MD2, MD4, MD5, SHA-0, SHA-1 ou RIPEMD. É possível realizar colisões de hash computacionalmente para esses algoritmos, o que efetivamente os quebrará.</p><p>Algoritmos de hash do .NET permitidos para agilidade criptográfica gerenciada (em ordem de preferência)</p><ul><li>SHA512Cng (em conformidade com FIPS)</li><li>SHA384Cng (em conformidade com FIPS)</li><li>SHA256Cng (em conformidade com FIPS)</li><li>SHA512Managed (sem conformidade com FIPS) (usar SHA512 como nome de algoritmo em chamadas para HashAlgorithm.Create ou CryptoConfig.CreateFromName)</li><li>SHA384Managed (sem conformidade com FIPS) (usar SHA384 como nome de algoritmo em chamadas para HashAlgorithm.Create ou CryptoConfig.CreateFromName)</li><li>SHA256Managed (sem conformidade com FIPS) (usar SHA256 como nome de algoritmo em chamadas para HashAlgorithm.Create ou CryptoConfig.CreateFromName)</li><li>SHA512CryptoServiceProvider (em conformidade com FIPS)</li><li>SHA256CryptoServiceProvider (em conformidade com FIPS)</li><li>SHA384CryptoServiceProvider (em conformidade com FIPS)</li></ul>| 

## <a id="strong-db"></a>Usar algoritmos de criptografia forte para criptografar dados no banco de dados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Escolhendo um algoritmo de criptografia](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Etapas** | Os algoritmos de criptografia definem as transformações de dados que não podem ser facilmente revertidas por usuários não autorizados. O SQL Server permite que administradores e desenvolvedores escolham entre vários algoritmos, incluindo DES, Triplo DES, TRIPLE_DES_3KEY, RC2, RC4, RC4 de 128 bits, DESX, AES de 128 bits, AES de 192 bits e AES de 256 bits. |

## <a id="ssis-signed"></a>Os pacotes do SSIS devem ser criptografados e assinados digitalmente

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Identificar a origem dos pacotes com assinaturas digitais](https://msdn.microsoft.com/library/ms141174.aspx), [Atenuação de ameaças e vulnerabilidades (Integration Services)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Etapas** | A origem de um pacote é a pessoa ou a organização que criou o pacote. Executar um pacote de uma origem desconhecida ou não confiável pode ser arriscado. Para evitar que os pacotes do SSIS sejam inadvertidamente alterados, use assinaturas digitais. Para garantir a confidencialidade dos pacotes durante seu trânsito/armazenamento, os pacotes do SSIS precisam ser criptografados |

## <a id="securables-db"></a>Adicionar assinatura digital a protegíveis de banco de dados críticos

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [ADD SIGNATURE (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Etapas** | Quando for necessário verificar a integridade de um banco de dados crítico protegível, use assinaturas digitais. Os protegíveis de um banco de dados, como um procedimento armazenado, uma função, um assembly ou um disparo, podem ser assinados digitalmente. Abaixo está um exemplo de quando isso pode ser útil: digamos que um ISV (fornecedor independente de software) ofereceu suporte a um software distribuído para um de seus clientes. Antes de fornecer o suporte, o ISV pode querer ter certeza de que um protegível do banco de dados no software não foi alterado por engano ou de forma maliciosa. Se o protegível estiver assinado digitalmente, o ISV poderá verificar sua assinatura digital e validar sua integridade.| 

## <a id="ekm-keys"></a>Usar EKM do SQL Server para proteger chaves de criptografia

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Gerenciamento extensível de chaves (EKM) do SQL Server](https://msdn.microsoft.com/library/bb895340), [Gerenciamento extensível de chaves usando o Azure Key Vault (SQL Server)](https://msdn.microsoft.com/library/dn198405) |
| **Etapas** | O gerenciamento extensível de chaves do SQL Server permite que as chaves de criptografia que protegem os arquivos de banco de dados sejam armazenadas em um dispositivo externo, como um cartão inteligente, dispositivo USB ou um módulo EKM/HSM. Isso também permite proteger os dados dos administradores de banco de dados (exceto dos membros do grupo sysadmin). Os dados podem ser criptografados com chaves de criptografia às quais somente o usuário do banco de dados tem acesso no módulo externo de EKM/HSM. |

## <a id="keys-engine"></a>Usar o recurso AlwaysEncrypted para não revelar as chaves de criptografia ao mecanismo de banco de dados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | Versão do SQL - V12, MsSQL2016 |
| **Referências**              | [Sempre criptografado (mecanismo de banco de dados)](https://msdn.microsoft.com/library/mt163865) |
| **Etapas** | O Always Encrypted é um recurso para proteger dados confidenciais, como números de cartão de crédito ou números de identificação nacional (por exemplo, os números do seguro social dos EUA), armazenados nos Bancos de dados SQL do Azure ou no SQL Server. Esse recurso permite que os clientes criptografem dados confidenciais em aplicativos clientes e nunca revelem as chaves de criptografia para o mecanismo de banco de dados (Banco de dados SQL ou SQL Server). Consequentemente, o Always Encrypted diferencia os proprietários dos dados (que podem vê-los) de quem gerencia os dados (mas não deve ter acesso). |

## <a id="keys-iot"></a>Armazenar chaves de criptografia com segurança no dispositivo IoT

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Sistema operacional do dispositivo - Windows IoT Core, Conectividade do dispositivo - SDKs do dispositivo IoT do Azure |
| **Referências**              | [TPM no Windows IoT Core](https://developer.microsoft.com/windows/iot/docs/tpm), [Configurar TPM no Windows IoT Core](https://developer.microsoft.com/windows/iot/win10/setuptpm), [TPM do SDK do dispositivo IoT do Azure](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Etapas** | Armazene chaves privadas simétricas ou de certificado com segurança em um armazenamento de hardware protegido, um TPM ou chips de cartão inteligente. O Windows 10 IoT Core dá suporte ao usuário de um TPM e há vários TPMs compatíveis que podem ser usados: https://developer.microsoft.com/windows/iot/win10/tpm. É recomendável usar um TPM Discreto ou de Firmware. Um TPM de Software deve ser usado apenas para fins de testes e desenvolvimento. Depois que um TPM estiver disponível e as chaves forem provisionadas nele, o código que gera o token deve ser escrito sem conter informações confidenciais. | 

### <a name="example"></a>Exemplo
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Como podemos ver, a chave primária do dispositivo não está presente no código. Em vez disso, ela está armazenada no slot 0 do TPM. O dispositivo do TPM gera um token SAS de curta duração que é usado para estabelecer uma conexão com o Hub IoT. 

## <a id="random-hub"></a>Gerar uma chave simétrica aleatória com um tamanho suficiente para autenticação no Hub IoT

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Nuvem IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Opção de gateway - Hub IoT do Azure |
| **Referências**              | N/D  |
| **Etapas** | O Hub IoT contém um Registro de Identidade de dispositivo e, ao provisionar um dispositivo, gera automaticamente uma chave simétrica aleatória. É recomendável usar esse recurso do Registro de Identidade do Hub IoT do Azure para gerar a chave usada para autenticação. O Hub IoT também permite que uma chave seja especificada durante a criação do dispositivo. Se uma chave é gerada fora do Hub IoT durante o provisionamento do dispositivo, é recomendável criar uma chave simétrica aleatória ou com pelo menos 256 bits. |

## <a id="pin-remote"></a>Garantir que uma política de gerenciamento de dispositivos esteja aplicada, que ela exija um PIN de uso e permita a limpeza remota do dispositivo

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente móvel do Dynamics CRM | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Garanta que uma política de gerenciamento de dispositivos esteja aplicada, que ela exija um PIN de uso e permita a limpeza remota do dispositivo. |

## <a id="bitlocker"></a>Garantir que uma política de gerenciamento de dispositivos esteja aplicada, que exija PIN, senha ou bloqueio automático e criptografe todos os dados (por exemplo, o BitLocker)

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente do Outlook do Dynamics CRM | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Garanta que uma política de gerenciamento de dispositivos esteja aplicada, que exija PIN, senha ou bloqueio automático e criptografe todos os dados (por exemplo, o BitLocker). |

## <a id="rolled-server"></a>Garantir que as chaves de assinatura sejam trocadas quando o Servidor de identidade for usado

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de Identidade | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Servidor de identidade - chaves, assinaturas e criptografia](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Etapas** | Garanta que as chaves de assinatura sejam trocadas quando o Servidor de identidade for usado. O conteúdo do link na seção Referências explica como isso deve ser planejado sem causar interrupções em aplicativos que dependem do Servidor de identidade. |

## <a id="client-server"></a>Garantir que ID de cliente e segredo do cliente criptograficamente fortes sejam usados no Identity Server

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de Identidade | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | <p>Garantir que ID de cliente e segredo do cliente criptograficamente fortes sejam usados no Identity Server. As seguintes diretrizes devem ser usadas durante a geração de um segredo e uma ID de cliente:</p><ul><li>Gerar um GUID aleatório como o ID do cliente</li><li>Gerar uma chave de 256 bits criptograficamente aleatória como o segredo</li></ul>|