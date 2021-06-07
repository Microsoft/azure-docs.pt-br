---
title: Segurança para atualização de dispositivo para o Hub IoT do Azure | Microsoft Docs
description: Entenda como a atualização de dispositivo para o Hub IoT garante que os dispositivos sejam atualizados com segurança.
author: lichris
ms.author: lichris
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub
ms.openlocfilehash: 86b2dbe6a28d1440f93788eb40e133d9b62d3f0c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489422"
---
# <a name="device-update-security-model"></a>Modelo de segurança de atualização de dispositivo

A atualização de dispositivo para o Hub IoT oferece um método seguro para implantar atualizações de firmware, imagens e aplicativos do dispositivo em seus dispositivos IoT. O fluxo de trabalho fornece um canal seguro de ponta a ponta com um modelo de cadeia de custódia completo que um dispositivo pode usar para provar que uma atualização é confiável, não modificada e intencional.

Cada etapa no fluxo de trabalho de atualização do dispositivo é protegida por meio de vários processos e recursos de segurança para garantir que cada etapa no pipeline execute uma entrega segura para a próxima. O cliente de atualização de dispositivo identifica e gerencia corretamente todas as solicitações de atualização ilegítimos. O cliente também verifica cada download para garantir que o conteúdo seja confiável, não modificado e seja intencional.

## <a name="for-solution-operators"></a>Para operadores de solução

Como os operadores de solução importam atualizações para sua instância de atualização de dispositivo, o serviço carrega e verifica os arquivos binários de atualização para garantir que eles não foram modificados ou trocados por um usuário mal-intencionado. Depois de verificado, o serviço de atualização de dispositivo gera um [manifesto de atualização](./update-manifest.md) interno com hashes de arquivo do manifesto de importação e outros metadados. Esse manifesto de atualização é então assinado pelo serviço de atualização de dispositivo.

Quando o operador de solução solicita a atualização de um dispositivo, uma mensagem assinada é enviada pelo canal do Hub IoT protegido para o dispositivo. A assinatura da solicitação é validada pelo agente de atualização de dispositivo do dispositivo como autêntico. 

Qualquer download binário resultante é protegido por meio da validação da assinatura de manifesto de atualização. O manifesto de atualização contém os hashes de arquivo binários, portanto, depois que o manifesto for confiável, o agente de atualização de dispositivo confiará nos hashes e os corresponderá aos binários. Depois que o binário de atualização for baixado e verificado, ele será enviado para o instalador no dispositivo.

## <a name="for-device-builders"></a>Para construtores de dispositivos

Para garantir que o serviço de atualização de dispositivo seja dimensionado para dispositivos simples e de baixo desempenho, o modelo de segurança usa chaves assimétricas brutas e assinaturas brutas. Eles usam formatos baseados em JSON, como tokens Web JSON & chaves da Web JSON.

### <a name="securing-update-content-via-the-update-manifest"></a>Protegendo o conteúdo de atualização por meio do manifesto de atualização

O manifesto de atualização é validado usando duas assinaturas. As assinaturas são criadas usando uma estrutura que consiste em chaves de *assinatura* e chaves *raiz* .

O agente de atualização de dispositivo inseriu chaves públicas que são usadas para todos os dispositivos compatíveis com a atualização do dispositivo. Essas são as chaves *raiz* . As chaves privadas correspondentes são controladas pela Microsoft.

A Microsoft também gera um par de chaves pública/privada que não está incluído no agente de atualização de dispositivo ou armazenado no dispositivo. Essa é a chave de *assinatura* .

Quando uma atualização é importada para a atualização do dispositivo para o Hub IoT e o manifesto de atualização é gerado pelo serviço, o serviço assina o manifesto usando a chave de assinatura e inclui a própria chave de assinatura, que é assinada por uma chave raiz. Quando o manifesto de atualização é enviado para o dispositivo, o agente de atualização de dispositivo recebe os seguintes dados de assinatura:

1. O próprio valor da assinatura.
2. O algoritmo usado para gerar #1.
3. As informações de chave pública da chave de assinatura usada para gerar #1.
4. A assinatura da chave de assinatura pública no #3.
5. A ID da chave pública da chave raiz usada para gerar #3.
6. O algoritmo usado para gerar #4.

O agente de atualização de dispositivo usa as informações definidas acima para validar que a assinatura da chave de assinatura pública seja assinada pela chave raiz. Em seguida, o agente de atualização de dispositivo valida se a assinatura do manifesto de atualização está assinada pela chave de assinatura. Se todas as assinaturas estiverem corretas, o manifesto de atualização será confiável para o agente de atualização de dispositivo. Como o manifesto de atualização inclui os hashes de arquivo que correspondem aos próprios arquivos de atualização, os arquivos de atualização também podem ser confiáveis se os hashes corresponderem.

Ter chaves raiz e de assinatura permite que a Microsoft lance periodicamente a chave de assinatura, uma prática recomendada de segurança.

### <a name="json-web-signature-jws"></a>Assinatura da Web JSON (JWS)

O `updateManifestSignature` é usado para garantir que as informações contidas no `updateManifest` não tenham sido adulteradas. O `updateManifestSignature` é produzido usando uma assinatura Web JSON com chaves Web JSON, permitindo a verificação de origem. A assinatura é uma cadeia de caracteres codificada Base64Url com três seções delineada por ".".  Consulte os [métodos auxiliares jws_util. h](https://github.com/Azure/iot-hub-device-update/tree/main/src/utils/jws_utils) para analisar e verificar chaves e tokens JSON.

A assinatura da Web JSON é um [padrão de IETF proposto](https://tools.ietf.org/html/rfc7515) amplamente usado para assinar conteúdo usando estruturas de dados baseadas em JSON. É uma maneira de garantir a integridade dos dados, verificando a assinatura dos dados. Mais informações podem ser encontradas na [RFC 7515](https://www.rfc-editor.org/info/rfc7515)(assinatura Web JSON) do JWS.

### <a name="json-web-token"></a>Token Web JSON

Os tokens Web JSON são um método aberto e [padrão](https://tools.ietf.org/html/rfc7519) do setor para representar declarações com segurança entre duas partes.

### <a name="root-keys"></a>Chaves raiz

Cada dispositivo de atualização de dispositivo contém um conjunto de chaves raiz. Essas chaves são a raiz de confiança para todas as assinaturas de atualização de dispositivo. Qualquer assinatura deve ser encadeada por meio de uma dessas chaves raiz para ser considerada legítima.

O conjunto de chaves raiz será alterado ao longo do tempo, pois é adequado para girar periodicamente as chaves de assinatura para fins de segurança. Como resultado, o software do agente de atualização de dispositivo precisará se atualizar com o conjunto mais recente de chaves raiz. 

### <a name="signatures"></a>Assinaturas

Todas as assinaturas serão acomodadas por uma chave de assinatura (pública) assinada por uma das chaves raiz. A assinatura identificará qual chave raiz foi usada para assinar a chave de assinatura. 

Um agente de atualização de dispositivo deve validar as assinaturas primeiro Validando que a assinatura da chave de assinatura (pública) é apropriada, válida e assinada por uma das chaves raiz aprovadas. Depois que a chave de assinatura for validada com êxito, a própria assinatura poderá ser validada usando a chave pública de assinatura agora confiável.

As chaves de assinatura são giradas em uma cadência muito mais rápida do que as chaves raiz, portanto, espere mensagens assinadas por várias chaves de assinatura diferentes. 

A revogação de uma chave de assinatura é gerenciada pelo serviço de atualização de dispositivo, de modo que os usuários não devem tentar armazenar as chaves de assinatura em cache. Sempre use a chave de assinatura que acompanha uma assinatura.

### <a name="receiving-updates"></a>Recebendo atualizações

Solicitações de atualização recebidas por um agente de atualização de dispositivo conterá um documento de UM (manifesto de atualização assinado). O agente deve validar se a assinatura do UM está correta e intacta. Isso é feito Validando que a chave de assinatura da assinatura de um foi assinada por uma chave de raiz apropriada. Depois de concluído, o agente valida a assinatura de UM em relação à chave de assinatura.

Depois que a assinatura de UM tiver sido validada, o agente de atualização de dispositivo poderá confiar nela como uma "fonte de verdade". Todas as outras relações de confiança de segurança são provenientes dessa fonte. 

O UM contém URLs e hashes de arquivo de conteúdo para download e instalação. Depois que o agente tiver baixado um binário de atualização, ele deverá validar a atualização em relação ao hash de arquivo encontrado no UM. Isso fornece um modelo de confiança transitiva para a validação de download. Não apenas garante que o conteúdo está intacto (não modificado), mas também confirma que o que foi baixado foi realmente o que pretendia ser baixado. 

### <a name="securing-the-device"></a>Protegendo o dispositivo

É importante garantir que os ativos de segurança relacionados à atualização do dispositivo estejam devidamente protegidos e protegidos em seu dispositivo. Ativos como chaves raiz precisam ser protegidos contra modificação. Há várias maneiras de fazer isso, como usar dispositivos de segurança (TPM, SGX, HSM, outros dispositivos de segurança) ou até mesmo embuti-los no agente de atualização de dispositivo. O último requer que o código do agente de atualização do dispositivo seja assinado digitalmente e o suporte à integridade do código do sistema esteja habilitado para proteger contra modificações mal-intencionadas do código do agente.

Medidas de segurança adicionais podem ser garantidas, como garantir que a entrega de componente para componente seja executada de forma segura. Por exemplo, registrar uma conta isolada específica para executar os vários componentes. E limitar as comunicações baseadas em rede (por exemplo, chamadas à API REST) apenas para localhost.

**[Próxima etapa: Saiba mais sobre como a atualização de dispositivo usa o RBAC do Azure](.\device-update-control-access.md)**