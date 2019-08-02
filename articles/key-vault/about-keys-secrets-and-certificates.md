---
title: Sobre chaves, segredos e certificados - Azure Key Vault
description: Visão geral dos detalhes de interface e desenvolvedor de REST do Azure Key Vault para chaves, segredos e certificados.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 52a0bc1b07ebf1aed55551e37ecc122ff393c0f7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703912"
---
# <a name="about-keys-secrets-and-certificates"></a>Sobre Chaves, Segredos e Certificados

O Azure Key Vault permite que aplicativos do Microsoft Azure e usuários armazenem e utilizem vários tipos de dados de chave/segredo:

- Chaves de criptografia: Dá suporte a vários tipos de chave e algoritmos e permite o uso de módulos de segurança de Hardware (HSM) para chaves de alto valor. 
- Segredos: Fornece armazenamento seguro de segredos, como senhas e cadeias de conexão de banco de dados.
- Certificados: Oferece suporte a certificados, que são criados sobre chaves e segredos e adicionam um recurso de renovação automática.
- Armazenamento do Microsoft Azure: Pode gerenciar chaves de uma conta de Armazenamento do Microsoft Azure para você. Internamente, o Key Vault pode listar (sincronizar) chaves com uma conta de Armazenamento do Azure e gerar novamente (gira) as chaves periodicamente. 

Para obter mais informações gerais sobre o Key Vault do Azure, consulte [O que é o Azure Key Vault?](/azure/key-vault/key-vault-whatis)

## <a name="azure-key-vault"></a>Cofre da Chave do Azure

As seções a seguir oferecem informações gerais aplicável durante a implementação do serviço Key Vault.

### <a name="supporting-standards"></a>Padrões de suporte

O JavaScript Object Notation (JSON) e especificações de criptografia e autenticação do objeto de JavaScript (JOSE) são informações importantes.  

-   [Chave da Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Criptografia de Web JSON (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Algoritmos da Web JSON (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [Assinatura da Web JSON (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="data-types"></a>Tipos de dados

Consulte as especificações JOSE para tipos de dados relevantes para as chaves, criptografia e assinatura.  

-   **algoritmo** - um algoritmo com suporte para uma operação de chave, por exemplo, RSA1_5  
-   **valor de texto cifrado** - octetos de texto cifrado, codificado usando Base64URL  
-   **valor de Digest** - a saída de um algoritmo de hash, codificado usando Base64URL  
-   **tipo de chave** - um dos tipos de chave com suporte, por exemplo, RSA (Rivest-Shamir-Adleman).  
-   **valor de texto sem formatação** - octetos de texto sem formatação, codificado usando Base64URL  
-   **valor de assinatura** - a saída de um algoritmo de assinatura, codificado usando Base64URL  
-   **base64URL** - um Base64URL [RFC4648] codificado valor binário  
-   **boolean** - seja true ou false  
-   **Identity** - uma identidade do Azure Active Directory (AAD).  
-   **IntDate** - um valor decimal de JSON que representa o número de segundos desde 1970-01-01T0:0:0Z UTC até a data/hora de UTC especificada. Consulte RFC3339 para obter detalhes sobre data/hora em geral e o UTC em particular.  

### <a name="objects-identifiers-and-versioning"></a>Objetos, identificadores e controle de versão

Objetos armazenados no Key Vault são submetidos ao controle de versão sempre que uma nova instância de um objeto é criada. Cada versão é atribuída a um identificador exclusivo e a uma URL. Quando um objeto é criado pela primeira vez, ele recebe um identificador de versão exclusivo e é marcado como a versão atual do objeto. A criação de uma nova instância com o mesmo nome de objeto fornece ao novo objeto um identificador de versão exclusivo, fazendo com que ele se torne a versão atual.  

Objetos no Key Vault podem ser endereçados usando o identificador atual ou um identificador específico da versão. Por exemplo, dada uma chave com o nome `MasterKey`, executar operações com o identificador atual faz com que o sistema use a versão mais recente disponível. Executar operações com o identificador de versão específico faz com que o sistema use essa versão específica do objeto.  

Os objetos são identificados de forma exclusiva no Key Vault usando uma URL. Não há dois objetos no sistema com a mesma URL, independentemente da localização geográfica. A URL completa para um objeto é chamada de Identificador do Objeto. A URL consiste em um prefixo que identifica o Key Vault, o tipo de objeto, o Nome do Objeto fornecido pelo usuário e uma Versão do Objeto. O nome do objeto é imutável e diferencia maiusculas de minúsculas. Identificadores que não incluem a versão do objeto são chamados de Identificadores de Base.  

Para mais informações, consulte [Autenticação, solicitações e respostas](authentication-requests-and-responses.md)

Um identificador de objeto tem o seguinte formato geral:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Sendo que:  

|||  
|-|-|  
|`keyvault-name`|O nome de um cofre de chaves no serviço do Microsoft Azure Key Vault.<br /><br /> Nomes de Cofre de Chaves são selecionados pelo usuário e são globalmente exclusivos.<br /><br /> O nome do Key Vault deve ser uma sequência de 3 a 24 caracteres que contenha somente 0 a 9, a a z, A a Z, e -.|  
|`object-type`|O tipo de objeto, "chaves" ou "segredos".|  
|`object-name`|Um `object-name` é um nome fornecido por usuário e deve ser exclusivo em um Cofre de Chaves. O nome deve ser uma sequência de 1 a 127 caracteres que contenha somente 0 a 9, a a z, A a Z, e -.|  
|`object-version`|Um `object-version` é um identificador de cadeia de caracteres de 32 caracteres gerado pelo sistema, que é opcionalmente usado para direcionar uma versão exclusiva de um objeto.|  

## <a name="key-vault-keys"></a>Chaves do Cofre de Chaves

### <a name="keys-and-key-types"></a>Chaves e tipos de chave

As chaves de criptografia no Key Vault são representadas como objetos de chave da Web JSON [JWK]. As especificações JWK/JWA base também são estendidas para habilitar tipos de chave exclusivos para a implementação do Key Vault. Por exemplo, a importação de chaves usando um empacotamento específico do fornecedor do HSM, permite um transporte seguro de chaves que só pode ser usado em HSMs do Key Vault.  

- **Chaves "Soft"** : Uma chave processada no software pelo Key Vault, mas criptografada em repouso usando uma chave do sistema que está em um HSM. Os clientes podem importar uma chave RSA ou EC (Curva Elíptica) existente ou solicitar que o Key Vault gere uma.
- **Chaves "Hard"** : Uma chave processada em um HSM (Hardware Security Module). Essas chaves são protegidas em um dos Mundos de Segurança do HSM do Key Vault (existe um Mundo de Segurança por geografia para manter o isolamento). Os clientes podem importar uma chave RSA ou EC, em formato flexível ou exportando de um dispositivo HSM compatível. Os clientes também podem solicitar ao Key Vault para gerar uma chave. Esse tipo de chave adiciona o atributo T para o JWK obter para transportar o material de chave do HSM.

     Para obter mais informações sobre fronteiras geográficas, consulte [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)  

O Key Vault oferece suporte apenas para chaves RSA e de Curva Elíptica. 

-   **EC**: Chave de curva elíptica "Soft".
-   **EC-HSM**: Chave de curva elíptica "Hard".
-   **RSA**: Chave "Soft" RSA.
-   **RSA-HSM**: Chave "Hard" RSA.

O Key Vault oferece suporte a chaves RSA dos tamanhos 2048, 3072 e 4096. O Key Vault oferece suporte a tipos de chave de Curva Elíptica P-256, P-384, P-521 e P-256K (SECP256K1).

### <a name="cryptographic-protection"></a>Proteção criptográfica

Os módulos de criptografia usados pelo Key Vault, seja HSM ou software, são validados pelo padrão FIPS. Você não precisa fazer nada especial para executar no modo FIPS. As chaves **criadas** ou **importadas** como protegidas por HSM são processadas dentro de um HSM, validado com o padrão FIPS 140-2 Nível 2. As chaves **criadas** ou **importadas** como protegidas por software são processadas dentro de módulos criptográficos, validados com o padrão FIPS 140-2 Nível 1. Para obter mais informações, confira [Chaves e tipos de chaves](#keys-and-key-types).

###  <a name="ec-algorithms"></a>Algoritmos EC
 Os seguintes identificadores de algoritmo são suportados com chaves EC e EC-HSM no Key Vault. 

#### <a name="curve-types"></a>Tipos de curva

-   **P-256** - A curva NIST P-256, definida em [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256_K** -curva a SEC SECP256K1, definida em [s 2: Parâmetros de Domínio de Curva Elíptica Recomendados](https://www.secg.org/sec2-v2.pdf).
-   **P-384** - A curva NIST P-384, definida em [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** - A curva NIST P-521, definida em [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>SIGN/VERIFY

-   **ES256** - resumos de ECDSA para SHA-256 e as chaves criadas com a curva p-256. Esse algoritmo é descrito em [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** - resumos de ECDSA para SHA-256 e as chaves criadas com curva P-256_K. Este algoritmo está pendente de padronização.
-   **ES384** - resumos de ECDSA para SHA-384 e as chaves criadas com a curva p-384. Esse algoritmo é descrito em [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** - ECDSA para SHA-512 resumos e as chaves criadas com a curva p-521. Esse algoritmo é descrito em [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>Algoritmos RSA  
 Os seguintes identificadores de algoritmo são suportados com chaves RSA e RSA-HSM no Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, CRIPTOGRAFAR/DESCRIPTOGRAFAR

-   **RSA1_5** - criptografia de chave RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA-OAEP** - RSAES usando Preenchimento de Criptografia Assimétrica Ideal (OAEP) [RFC3447], com os parâmetros padrão especificados por RFC 3447 na seção 2.1. Esses parâmetros padrão estão usando uma função de hash de SHA-1 e uma função de geração de máscara de MGF1 com SHA-1.  

#### <a name="signverify"></a>SIGN/VERIFY

-   **RS256** - RSASSA-PKCS-v1_5 usando SHA-256. O valor do resumo fornecido pelo aplicativo deve ser calculado usando SHA-256 e deve ter 32 bytes de comprimento.  
-   **RS384** - RSASSA-PKCS-v1_5 usando SHA-384. O valor do resumo fornecido pelo aplicativo deve ser calculado usando SHA-384 e deve ter 48 bytes de comprimento.  
-   **RS512** - RSASSA-PKCS-v1_5 usando SHA-512. O valor do resumo fornecido pelo aplicativo deve ser calculado usando SHA-512 e deve ter 64 bytes de comprimento.  
-   **RSNULL** - consulte [RFC2437], um caso de uso especializado para permitir determinados cenários TLS.  

###  <a name="key-operations"></a>Operações de chave

O Key Vault suporta as seguintes operações em objetos de chave:  

-   **Criar**: Permite que um cliente crie uma chave no Key Vault. O valor da chave é gerado pelo Key Vault e armazenado e não é liberado para o cliente. Chaves assimétricas podem ser criadas no Key Vault.  
-   **Importar**: Permite que um cliente importe uma chave existente para o Key Vault. Chaves assimétricas chaves podem ser importadas para o Key Vault através de diversos métodos diferentes de empacotamento dentro de um constructo JWK. 
-   **Atualização**: Permite que um cliente com permissões suficientes modifique os metadados (atributos de chave) associados com uma chave armazenada anteriormente no Key Vault.  
-   **Excluir**: Permite que um cliente com permissões suficientes exclua uma chave do Key Vault.  
-   **Lista**: Permite que um cliente liste todas as chaves em um determinado Key Vault.  
-   **Versões da lista**: Permite que um cliente liste todas as versões de uma determinada chave em um determinado Key Vault.  
-   **Obter**: Permite que um cliente recupere as partes públicas de uma determinada chave em um Key Vault.  
-   **Backup**: Exporta uma chave de forma protegida.  
-   **Restaurar**: Importa uma chave de backup anterior.  

Para obter mais informações, veja [Operações de chave na referência de API REST do Key Vault](/rest/api/keyvault).  

Quando uma chave tiver sido criada no Key Vault, as seguintes operações criptográficas podem ser executadas usando a chave:  

-   **Assinar e Verificar**: Estritamente, essa operação é, estritamente, "assinar hash" ou "verificar hash", já que o Key Vault não oferece suporte a hash de conteúdo como parte da criação da assinatura. Aplicativos devem fazer hash dos dados para se conectarem localmente e, em seguida, solicitar ao Key Vault a assinatura do hash. A verificação de hashes assinados é suportada como uma operação conveniente para aplicativos que podem não ter acesso ao material de chave [público]. Para melhor desempenho do aplicativo, verifique se as operações são executadas localmente.  
-   **Chave de Criptografia / Encapsulamento**: Uma chave armazenada no Key Vault pode ser usada para proteger outra chave, normalmente uma chave de criptografia de conteúdo simétrico (CEK). Quando a chave no Key Vault é assimétrica, a criptografia de chave é usada. Por exemplo, RSA-OAEP e as operações ENCAPSULAR CHAVE/DESENCAPSULAR CHAVE são equivalentes a CRIPTOGRAFAR/DESCRIPTOGRAFAR. Quando a chave no Key Vault for simétrica, é usado o encapsulamento de chave. Por exemplo, AES-KW. A operação ENCAPSULAR CHAVE é suportada como uma conveniência para aplicativos que podem não ter acesso ao material de chave [público]. Para melhor desempenho do aplicativo, as operações ENCAPSULAR CHAVE devem ser executadas localmente.  
-   **Criptografar e descriptografar**: Uma chave armazenada no Key Vault pode ser usada para criptografar ou descriptografar um único bloco de dados. O tamanho do bloco é determinado pelo tipo de chave e algoritmo de criptografia selecionado. A operação Criptografar é fornecida por conveniência para aplicativos que podem não ter acesso ao material de chave [público]. Para melhor desempenho do aplicativo, as operações de criptografia devem ser executadas localmente.  

Enquanto o uso de chaves assimétricas na operação ENCAPSULAR CHAVE/DESENCAPSULAR CHAVE possa parecer supérfluo (já que esta operação é equivalente a CRIPTOGRAFAR/DESCRIPTOGRAFAR), o uso de operações distintas é importante. A distinção fornece semântica e separação de autorização dessas operações e consistência quando outros tipos de chave são suportados pelo serviço.  

O Key Vault não dá suporte a operações de exportação. Quando uma chave é fornecida no sistema não pode ser extraída ou ter seu material de chave modificado. No entanto, os usuários do Key Vault podem exigir sua chave para outros casos de uso, tais como após ela ter sido excluída. Nesse caso, eles podem usar as operações de BACKUP e RESTAURAÇÃO para exportar/importar a chave de forma protegida. As chaves criadas pela operação de BACKUP não são utilizáveis fora do Key Vault. Como alternativa, a operação de IMPORTAR pode ser usada em várias instâncias do Key Vault.  

Os usuários podem restringir qualquer uma das operações de criptografia que o Key Vault suporta, por chave, usando a propriedade key_ops do objeto JWK.  

Para obter mais informações sobre objetos JWK, consulte [Chave de Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="key-attributes"></a>Atributos de chave

Além do material de chave, os seguintes atributos podem ser especificados. Em uma solicitação de JSON, a palavra-chave e as chaves, '{' '}', são necessárias, mesmo se não houver nenhum atributo especificado.  

- *habilitado*: booliano, opcional, o padrão é **true**. Especifica se a chave está habilitada e pode ser utilizada para operações de criptografia. O atributo *habilitado* é usado em conjunto com *nbf* e *exp*. Quando ocorre uma operação entre *nbf* e *exp*, ele só será permitido se *habilitado* estiver definido como **true**. As operações fora da janela *nbf* / *exp* não são automaticamente permitidas, exceto para determinados tipos de operação em [determinadas condições](#date-time-controlled-operations).
- *nbf*: IntDate, opcional, o padrão é agora. O atributo *nbf* (not before) identifica o tempo anterior que a chave NÃO DEVE ser usada para operações de criptografia, exceto para determinados tipos de operação em [determinadas condições](#date-time-controlled-operations). O processamento do atributo *nbf* requer que a data/hora atual DEVE ser posterior ou igual a data/hora não-anterior listada no atributo *nbf*. O Key Vault PODE fornecer alguma reserva pequena, normalmente não mais do que alguns minutos, para compensar distorção do relógio. Seu valor DEVE ser um número que contenha um valor de IntDate.  
- *exp*: IntDate, opcional, o padrão é “infinito”. O atributo *exp* (expiration time) identifica o tempo de expiração em que ou depois que a chave NÃO DEVE ser usada para operações de criptografia, exceto para determinados tipos de operação em [determinadas condições](#date-time-controlled-operations). O processamento do atributo *exp* requer que a data/hora atual DEVE ser anterior a data/hora de expiração listada no atributo *exp*. O Key Vault PODE fornecer alguma reserva pequena, geralmente não mais do que alguns minutos, para compensar distorção do relógio. Seu valor DEVE ser um número que contenha um valor de IntDate.  

Há mais atributos somente leitura que são incluídos em qualquer resposta que inclui os atributos de chave:  

- *criado*: IntDate, opcional. O atributo *criado* indica quando esta versão da chave foi criada. O valor é nulo para chaves criadas antes da adição deste atributo. Seu valor DEVE ser um número que contenha um valor de IntDate.  
- *atualizado*: IntDate, opcional. O atributo *atualizado* indica quando esta versão da chave foi atualizada. O valor é nulo para chaves que foram atualizadas antes da adição deste atributo. Seu valor DEVE ser um número que contenha um valor de IntDate.  

Para obter mais informações sobre IntDate e outros tipos de dados, consulte [Tipos de dados](#data-types)  

#### <a name="date-time-controlled-operations"></a>Operações de data e hora controladas

Chaves expiradas e ainda não válidas, fora da janela *nbf* / *exp*, funcionarão para operações **descriptografar**, **desencapsular** e **verificar** (não retornará 403, Proibido). A lógica para usar o estado de não válido ainda é permitir que uma chave seja testada antes do uso de produção. A lógica para usar o estado expirado é permitir operações de recuperação de dados que foram criados quando a chave era válida. Além disso, você pode desabilitar o acesso a uma chave usando políticas de Cofre de Chaves, ou atualizando o atributo de chave *habilitado* para **false**.

Para obter mais informações sobre tipos de dados, consulte [Tipos de dados](#data-types).

Para obter mais informações sobre outros atributos possíveis, consulte a [Chave de Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="key-tags"></a>Marcas de chave

Você pode especificar mais metadados específicos do aplicativo na forma de marcas. O Key Vault oferece suporte a até 15 marcas, cada uma delas pode ter um nome de 256 caracteres e um valor de 256 caracteres.  

>[!Note]
>As marcas podem ser lidas por um chamador se ele tiver a permissão *listar* ou *obter* para esse tipo de objeto; chaves, segredos ou certificados.

###  <a name="key-access-control"></a>Controle de acesso a chave

O controle de acesso para chaves gerenciados pelo Cofre de Chaves é fornecido no nível de um Cofre de Chaves que atua como o contêiner de chaves. A política de controle de acesso para chaves é diferente da política de controle de acesso para segredos no mesmo Key Vault. Os usuários podem criar um ou mais cofres para armazenar chaves e são solicitados a manter a segmentação e gerenciamento de chaves apropriados do cenário. O controle de acesso para chaves é independente do controle de acesso para segredos.  

As seguintes permissões podem ser concedidas, por usuário / serviço, na entrada de controle de acesso de chaves em um cofre. Essas permissões refletem com maior exatidão as operações permitidas em um objeto de chave.  Conceder acesso a uma entidade de serviço no cofre de chaves é uma operação onetime, e ela será mantida mesma para todas as assinaturas do Azure. Você pode usá-lo para implantar certificados quantos desejar. 

- Permissões para operações de gerenciamento de chaves
  - *obter*: Ler a parte pública de uma chave, além de seus atributos
  - *lista*: Listar as chaves ou versões de uma chave armazenada em um cofre de chaves
  - *atualizar*: Atualizar os atributos de uma chave
  - *criar*: Criar novas chaves
  - *importar*: Importar uma chave para um cofre de chaves
  - *excluir*: Excluir o objeto de chave
  - *recuperar*: Recuperar uma chave excluída
  - *backup*: Fazer backup de uma chave em um Key Vault
  - *restaurar*: Restaurar um backup da chave em um cofre de chave

- Permissões para operações criptográficas
  - *descriptografar*: Usar a chave para desproteger uma sequência de bytes
  - *criptografar*: Usar a chave para proteger uma sequência arbitrária de bytes
  - *unwrapKey*: Usar a chave para desproteger uma chave simétrica encapsulada
  - *wrapKey*: Usar a chave para proteger uma chave simétrica
  - *verificar*: Usar a chave para verificar resumos  
  - *sign*: Use a chave para assinar resumos
    
- Permissões para operações com privilégio
  - *limpar*: Limpar (exclui permanentemente) uma chave excluída

Para obter mais informações sobre como trabalhar com chaves, consulte [Operações de chave na referência de API REST do Key Vault](/rest/api/keyvault). Para obter informações sobre como estabelecer permissões, confira [Cofres – criar ou atualizar](/rest/api/keyvault/vaults/createorupdate) e [Cofres – atualizar política de acesso](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="key-vault-secrets"></a>Segredo do Cofre de Chaves 

### <a name="working-with-secrets"></a>Trabalhando com segredos

Da perspectiva do desenvolvedor, APIs Key Vault aceitam e retornam valores do segredo como cadeias de caracteres. Internamente, o Key Vault armazena e gerencia os segredos como sequências de octetos (bytes de 8 bits), com um tamanho máximo de 25k bytes cada. O serviço Key Vault não fornece a semântica para segredos. Ele simplesmente aceita os dados, criptografa-os, armazena-os e retorna um identificador secreto ("id"). O identificador pode ser usado para recuperar o segredo em um momento posterior.  

Para dados altamente confidenciais, os clientes devem considerar camadas adicionais de proteção de dados. Criptografar dados usando uma chave de proteção separada antes do armazenamento no Key Vault é um exemplo.  

O Key Vault também oferece suporte a um campo contentType para segredos. Os clientes podem especificar o tipo de conteúdo de um segredo para ajudar a interpretar os dados de segredo quando são recuperados. O comprimento máximo deste campo é de 255 caracteres. Não existem valores predefinidos. O uso sugerido é como uma dica para interpretar os dados de secredos. Por exemplo, uma implementação pode armazenar senhas e certificados como segredos e usar esse campo para fazer a diferenciação. Não existem valores predefinidos.  

### <a name="secret-attributes"></a>Atributos de segredos

Além dos dados de segredo, os seguintes atributos podem ser especificados:  

- *exp*: IntDate, opcional, o padrão é **infinito**. O atributo *exp* (tempo de expiração) identifica o tempo de expiração em que ou depois que os dados de segredo NÃO DEVEM ser recuperados, exceto em [determinadas situações](#date-time-controlled-operations). Esse campo é para fins **informativos** somente, visto que ele informa os usuários do serviço de cofre de que um segredo específico não pode ser usado. Seu valor DEVE ser um número que contenha um valor de IntDate.   
- *nbf*: IntDate, opcional, o padrão é **agora**. O atributo *nbf* (not before) identifica o tempo anterior que os dados de segredo NÃO DEVEM ser recuperados, exceto em [determinadas situações](#date-time-controlled-operations). Este documento serve apenas para fins **informativos**. Seu valor DEVE ser um número que contenha um valor de IntDate. 
- *habilitado*: booliano, opcional, o padrão é **true**. Esse atributo especifica se os dados do segredo podem ser recuperados. O atributo habilitado é usado em conjunto com *nbf* e *exp* quando ocorre uma operação entre *nbf* e *exp*, ele só será permitido se habilitado estiver definido como **true**. As operações fora da janela *nbf* e *exp* são automaticamente não permitidas, exceto em [determinadas situações](#date-time-controlled-operations).  

Há mais atributos somente leitura que são incluídos em qualquer resposta que inclui os atributos de segredo:  

- *criado*: IntDate, opcional. O atributo criado indica quando esta versão do segredo foi criada. Esse valor é nulo para segredos criados antes da adição deste atributo. Seu valor deve ser um número que contenha um valor de IntDate.  
- *atualizado*: IntDate, opcional. O atributo atualizado indica quando esta versão do segredo foi atualizada. Esse valor é nulo para segredos que foram atualizados antes da adição deste atributo. Seu valor deve ser um número que contenha um valor de IntDate.

#### <a name="date-time-controlled-operations"></a>Operações de data e hora controladas

Uma operação **obter** do segredo funcionará para segredos ainda não válidos e expirados, fora da janela *nbf* / *exp*. Chamar uma operação **obter** do segredo, para um segredo ainda não válido, pode ser usada para fins de teste. Recuperar (**obter**) um segredo expirado, pode ser usado para operações de recuperação.

Para obter mais informações sobre tipos de dados, consulte [Tipos de dados](#data-types).  

### <a name="secret-access-control"></a>Controle de acesso a segredo

O Controle de Acesso para segredos gerenciados pelo Key Vault é fornecido no nível do Key Vault que atua como o contêiner desses segredos. A política de controle de acesso para segredos é diferente da política de controle de acesso para chaves no mesmo Key Vault. Os usuários podem criar um ou mais cofres para armazenar segredos e são solicitados a manter a segmentação e gerenciamento de segredos apropriados do cenário.   

As seguintes permissões podem ser usadas, por entidade de segurança, na entrada de controle de acesso segredos em um cofre e refletem com maior exatidão as operações permitidas em um objeto de segredo:  

- Permissões para operações de gerenciamento de segredos
  - *obter*: Ler um segredo  
  - *lista*: Listar os segredos ou versões de um segredo armazenado em um Key Vault  
  - *set*: Criar um segredo  
  - *excluir*: Excluir um segredo  
  - *recuperar*: Recuperar um segredo excluído
  - *backup*: Fazer backup de um segredo em um Key Vault
  - *restaurar*: Restaura um backup do segredo em um Key Vault

- Permissões para operações com privilégio
  - *limpar*: Limpar (exclui permanentemente) um segredo excluído

Para obter mais informações sobre como trabalhar com segredos, veja [Operações de segredo na referência de API REST do Key Vault](/rest/api/keyvault). Para obter informações sobre como estabelecer permissões, confira [Cofres – criar ou atualizar](/rest/api/keyvault/vaults/createorupdate) e [Cofres – atualizar política de acesso](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Marcas de segredos  
Você pode especificar mais metadados específicos do aplicativo na forma de marcas. O Key Vault oferece suporte a até 15 marcas, cada uma delas pode ter um nome de 256 caracteres e um valor de 256 caracteres.  

>[!Note]
>As marcas podem ser lidas por um chamador se ele tiver a permissão *listar* ou *obter* para esse tipo de objeto; chaves, segredos ou certificados.

## <a name="key-vault-certificates"></a>Certificados do Cofre de Chaves

Os certificados do Cofre de Chaves oferecem suporte ao gerenciamento de seus certificados x509 e dos comportamentos a seguir:  

-   Permite que um proprietário de certificado crie um certificado por meio de um processo de criação de Cofre de Chaves ou pela importação de um certificado existente. Inclui certificados autoassinados e gerados por Autoridades de Certificação.
-   Permite que um proprietário de certificado do Cofre de Chaves implemente o armazenamento seguro e o gerenciamento de certificados X509 sem interagir com o material da chave privada.  
-   Permite que um proprietário de certificado crie uma política que direciona o Cofre de Chaves para gerenciar o ciclo de vida de um certificado.  
-   Permite que os proprietários de certificado forneçam informações de contato para a notificação sobre eventos de ciclo de vida de validade e renovação de certificados.  
-   Oferece suporte à renovação automática com emissores selecionados - provedores de certificado X509/autoridades de certificação parceiros do Cofre de Chaves.

>[!Note]
>Provedores/autoridades que não têm parceria também têm permissão, mas, não oferecerão suporte ao recurso de renovação automática.

### <a name="composition-of-a-certificate"></a>Composição de um certificado

Quando um certificado de Cofre de Chaves é criado, uma chave endereçável e o segredo também são criados com o mesmo nome. A chave de Cofre de Chaves permite operações de chave e o segredo do Cofre de Chaves permite a recuperação do valor do certificado como um segredo. Um certificado do Cofre de Chaves também contém metadados do certificado x509 público.  

O identificador e a versão de certificados é semelhante à de chaves e segredos. Uma versão específica de uma chave endereçável e segredo criados com a versão de certificado do Cofre de Chaves está disponível na resposta de certificado do Cofre de Chaves.
 
![Os certificados são objetos complexos](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Chave exportável ou não exportável

Quando um certificado de Key Vault é criado, ele pode ser recuperado do segredo endereçável com a chave privada no formato PFX ou PEM. A política usada para criar o certificado deve indicar que a chave é exportável. Se a política indicar não exportável, a chave privada não é uma parte do valor quando for recuperada como um segredo.  

A chave endereçável fica mais relevante com certificados KV não exportáveis. As operações da chave KV endereçável são mapeadas do campo *keyusage* da política de certificado KV usada para criar o certificado KV.  

Há suporte para dois tipos de chave – *RSA* ou *RSA HSM* com certificados. Exportável só é permitido com RSA, RSA HSM não tem suporte.  

### <a name="certificate-attributes-and-tags"></a>Marcas e atributos de certificado

Além dos metadados do certificado, uma chave endereçável e um segredo endereçável, um certificado do Key Vault também contém marcas e atributos.  

#### <a name="attributes"></a>Atributos

Os atributos de certificado são espelhados para atributos de chave endereçável e segredo criado quando o certificado KV é criado.  

Um certificado do Cofre de Chaves tem os seguintes atributos:  

-   *habilitado*: booliano, opcional, o padrão é **true**. Pode ser especificado para indicar se os dados de certificado podem ser recuperados como segredo ou operável como uma chave. Também usado em conjunto com *nbf* e *exp* quando ocorre uma operação entre *nbf* e *exp* e só será permitido se ”habilitado” estiver definido como true. As operações fora da janela *nbf* e *exp* são proibidas automaticamente.  

Há mais atributos somente leitura que são incluídos em resposta:

-   *criado*: IndDate indica quando esta versão do certificado foi criada.  
-   *atualizado*: IndDate indica quando esta versão do certificado foi atualizada.  
-   *exp*: IntDate: contém o valor da data de expiração do certificado x509.  
-   *nbf*: IntDate: contém o valor da data do certificado x509.  

> [!Note] 
> Se um certificado do Cofre de Chaves expirar, sua chave e segredo endereçáveis ficam inoperantes.  

#### <a name="tags"></a>Marcas

 O dicionário de pares de valor de chave especificado pelo cliente, semelhantes às marcas em chaves e segredos.  

 > [!Note]
> As marcas podem ser lidas por um chamador se ele tiver a permissão *listar* ou *obter* para esse tipo de objeto; chaves, segredos ou certificados.

### <a name="certificate-policy"></a>Política do certificado

Uma política de certificado contém informações sobre como criar e gerenciar o ciclo de vida de um certificado do Key Vault. Quando um certificado com a chave privada é importado para o Cofre de Chaves, uma política padrão é criada lendo o certificado x509.  

Quando um certificado do Key Vault é criado do zero, uma política precisa ser fornecida. A política especifica como criar esta versão do certificado do Key Vault ou a versão seguinte do certificado do Key Vault. Após o estabelecimento de uma política, não é necessária operações sucessivas para criar as próximas versões. Há apenas uma instância de uma política para todas as versões de um certificado de Key Vault.  

Em um nível alto, uma política de certificado contém as seguintes informações:  

-   Propriedades do Certificado X509: Contém o nome da entidade, os nomes alternativos da entidade e outras propriedades usadas para criar uma solicitação de certificado x509.  
-   Propriedades de chave: contém o tipo de chave, comprimento da chave e campos de chave reutilizáveis e exportáveis. Esses campos instruem o Cofre de Chaves sobre como gerar uma chave.  
-   Propriedades de segredo: contém propriedades secretas como tipo de conteúdo de segredo endereçável para gerar o valor de segredo, para recuperar o certificado como um segredo.  
-   Ações de tempo de vida: contém ações de tempo de vida do certificado KV. Cada ação de tempo de vida contém:  

     - Gatilho: especificado por meio de dias antes da expiração ou tempo de vida de porcentagem de alcance  

     - Ação: especificar o tipo de ação – *emailContacts* ou *renovar automaticamente*  

-   Emissor: Parâmetros sobre o emissor do certificado a ser usado para emitir certificados x509.  
-   Atributos de política: contém os atributos associados à política  

#### <a name="x509-to-key-vault-usage-mapping"></a>X509 para mapeamento de uso do Cofre de Chaves

A tabela a seguir representa o mapeamento de política de uso de chave x509 para operações de chave efetivas de uma chave criada como parte da criação de certificado do Cofre de Chaves.

|**Sinalizadores de Utilização de Chave x509**|**Operações de chave do Cofre de Chaves**|**Comportamento padrão**|
|----------|--------|--------|
|DataEncipherment|criptografar, descriptografar| N/D |
|DecipherOnly|descriptografar| N/D  |
|DigitalSignature|fazer logon, verificar| Padrão de Cofre de Chaves sem uma especificação de uso no momento da criação de certificado | 
|EncipherOnly|encrypt| N/D |
|KeyCertSign|fazer logon, verificar|N/D|
|KeyEncipherment|wrapKey, unwrapKey| Padrão de Cofre de Chaves sem uma especificação de uso no momento da criação de certificado | 
|Não repúdio|fazer logon, verificar| N/D |
|crlsign|fazer logon, verificar| N/D |

### <a name="certificate-issuer"></a>Emissor do certificado

Um objeto de certificado do Cofre de Chaves contém uma configuração usada para se comunicar com um provedor de emissor do certificado selecionado para solicitar certificados x509.  

-   Parceiros de Cofre de Chaves com as seguintes provedores de emissor do certificado para certificados SSL

|**Nome do Provedor**|**Locais**|
|----------|--------|
|DigiCert|Suporte em todos os locais de serviço de Cofre de Chaves na nuvem pública e o Azure Government|
|GlobalSign|Suporte em todos os locais de serviço de Cofre de Chaves na nuvem pública e o Azure Government|

Antes que um emissor de certificado possa ser criado em um Cofre de Chaves, as seguintes etapas 1 e 2 de pré-requisitos devem ser concluídas com êxito.  

1. Integrado aos provedores de Autoridade de Certificação (CA)  

    -   Um administrador da organização deve integrar sua empresa (ex. Contoso) com pelo menos um provedor de autoridade de certificação.  

2. O administrador cria as credenciais do solicitante para que o Cofre de Chaves registre (e renove) certificados SSL  

    -   Fornece a configuração a ser usado para criar um objeto de emissor do provedor no cofre de chaves  

Para obter mais informações sobre como criar objetos de emissor do portal de certificados, consulte o [blog de certificados de Cofre de Chave](https://aka.ms/kvcertsblog)  

O Cofre de Chaves permite a criação de vários objetos de emissor com configuração de provedor de emissor diferente. Quando um objeto de emissor é criado, seu nome pode ser referenciado em uma ou várias políticas de certificado. Referenciar o objeto emissor instrui o Cofre de Chaves para usar a configuração conforme especificado no objeto emissor ao solicitar o certificado x509 do provedor de autoridade de certificação durante a criação e renovação do certificado.  

Objetos de emissor são criados no cofre e só podem ser usados com certificados KV no mesmo cofre.  

### <a name="certificate-contacts"></a>Contatos de certificado

Contatos de certificado contêm informações de contato para enviar notificações disparadas por eventos de tempo de vida do certificado. As informações de contatos são compartilhadas por todos os certificados no cofre de chaves. Uma notificação é enviada a todos os contatos especificados para um evento para qualquer certificado no cofre de chaves.  

Se uma política de certificado for definida como renovação automática, uma notificação é enviada nos eventos a seguir.  

- Antes da renovação de certificado
- Após a renovação do certificado, indicar se o certificado foi renovado com êxito ou se houve um erro, exigindo a renovação manual do certificado.  

  Quando uma política de certificado for definida para ser renovada manualmente (somente por e-mail), uma notificação é enviada quando for o momento de renovar o certificado.  

### <a name="certificate-access-control"></a>Controle de acesso de certificado

 O controle de acesso para certificados é gerenciado pelo Key Vault e é fornecido no nível de um Key Vault que atua como o contêiner desses certificados. A política de controle de acesso para certificados é diferente da política de controle de acesso para chaves e segredos no mesmo Key Vault. Os usuários podem criar um ou mais cofres para armazenar certificados, para manter a segmentação e gerenciamento de certificados apropriados do cenário.  

 As seguintes permissões podem ser usadas, por entidade de segurança, na entrada de controle de acesso segredos em um cofre de chaves e refletem com maior exatidão as operações permitidas em um objeto de segredo:  

- Permissões para operações de gerenciamento do certificado
  - *obter*: Obtém a versão atual do certificado ou qualquer versão de um certificado 
  - *lista*: Lista os certificados atuais ou versões de um certificado  
  - *atualizar*: Atualizar um certificado
  - *criar*: Criar certificado do Key Vault
  - *importar*: Importar o material do certificado em um certificado de Key Vault
  - *excluir*: Excluir um certificado, sua política e todas as suas versões  
  - *recuperar*: Recuperar um certificado excluído
  - *backup*: Faz backup de um certificado em um Key Vault
  - *restaurar*: Restaurar um backup de certificado em um Key Vault
  - *managecontacts*: Gerenciar contatos de certificados do Key Vault  
  - *manageissuers*: Gerenciar as autoridades/emissores de certificado do Key Vault
  - *getissuers*: Obter as autoridades/emissores de um certificado
  - *listissuers*: Lista as autoridades/emissores de um certificado  
  - *setissuers*: Cria ou atualiza emissores de certificado do Key Vault  
  - *deleteissuers*: Exclui autoridades/emissores do certificado do Azure Key Vault  
 
- Permissões para operações com privilégio
  - *limpar*: Limpar (exclui permanentemente) um certificado excluído

Para obter mais informações, veja [Operações de certificado na referência de API REST do Key Vault](/rest/api/keyvault). Para obter informações sobre como estabelecer permissões, confira [Cofres – criar ou atualizar](/rest/api/keyvault/vaults/createorupdate) e [Cofres – atualizar política de acesso](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="azure-storage-account-key-management"></a>Gerenciamento da conta de armazenamento do Azure

O Key Vault pode gerenciar chaves de conta de armazenamento do Azure:

- Internamente, o Key Vault pode listar (sincronizar) chaves com uma conta de armazenamento do Azure. 
- O Key Vault gera novamente (gira) as chaves periodicamente.
- Os valores de chave nunca são retornados em resposta ao chamador.
- O Key Vault gerencia chaves tanto de contas de armazenamento quanto de contas de armazenamento clássicas.

Para obter mais informações, confira [Chaves de conta de armazenamento do Azure Key Vault](key-vault-ovw-storage-keys.md)

### <a name="storage-account-access-control"></a>Controle de acesso da conta de armazenamento

As seguintes permissões podem ser usadas ao autorizar um usuário ou entidade de segurança de aplicativo para executar operações em uma conta de armazenamento gerenciada:  

- Permissões para operações de definição de SaS e conta de armazenamento gerenciada
  - *obter*: Obtém informações sobre uma conta de armazenamento 
  - *lista*: Lista contas de armazenamento gerenciadas por um Key Vault
  - *atualizar*: Atualizar uma conta de armazenamento
  - *excluir*: Excluir uma conta de armazenamento  
  - *recuperar*: Recuperar uma conta de armazenamento excluída
  - *backup*: Faz backup de uma conta de armazenamento
  - *restaurar*: Restaurar um backup de conta de armazenamento em um Key Vault
  - *set*: Criar ou atualizar uma conta de armazenamento
  - *regeneratekey*: Regenerar um valor de chave especificado para uma conta de armazenamento
  - *getsas*: Obtém informações sobre uma definição de SAS para uma conta de armazenamento
  - *listsas*: Listar as definições de SAS de armazenamento para uma conta de armazenamento
  - *deletesas*: Exclui uma definição de SAS de uma conta de armazenamento
  - *setsas*: Cria ou atualiza uma nova definição de SAS/atributos para uma conta de armazenamento

- Permissões para operações com privilégio
  - *limpar*: Limpa (exclui permanentemente) uma conta de armazenamento gerenciada

Para obter mais informações, confira [Operações de conta de armazenamento na referência de API REST do Key Vault](/rest/api/keyvault). Para obter informações sobre como estabelecer permissões, confira [Cofres – criar ou atualizar](/rest/api/keyvault/vaults/createorupdate) e [Cofres – atualizar política de acesso](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Consulte também

- [Autenticação, solicitações e respostas](authentication-requests-and-responses.md)
- [Guia do Desenvolvedor do Cofre de Chaves](/azure/key-vault/key-vault-developers-guide)
