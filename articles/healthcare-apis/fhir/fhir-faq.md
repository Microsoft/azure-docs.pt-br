---
title: Perguntas frequentes sobre os serviços FHIRs no Azure – API do Azure para FHIR
description: Obtenha respostas para perguntas frequentes sobre a API do Azure para FHIR, como o local de armazenamento de dados por trás de APIs do FHIR e suporte de versão.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 22d9df80bce6e79f0058567f8df76935e431e598
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017683"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Perguntas frequentes sobre a API do Azure para FHIR

## <a name="azure-api-for-fhir-the-basics"></a>API do Azure para FHIR: Noções básicas

### <a name="what-is-fhir"></a>O que é o FHIR?
Os recursos de interoperabilidade do Fast Healthcare (FHIR-pronunciado "incêndio") são um padrão de interoperabilidade destinado a habilitar a troca de dados de saúde entre diferentes sistemas de integridade. Esse padrão foi desenvolvido pela organização HL7 e está sendo adotado pelas organizações de saúde em todo o mundo. A versão mais atual do FHIR disponível é R4 (versão 4). A API do Azure para FHIR dá suporte a R4 e também dá suporte à versão anterior STU3 (Standard para avaliação, use 3). Para obter mais informações sobre o FHIR, visite [HL7.org](http://hl7.org/fhir/summary.html).

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Os dados estão por trás das APIs do FHIR armazenadas no Azure?

Sim, os dados são armazenados em bancos de dados gerenciados no Azure. A API do Azure para FHIR não fornece acesso direto ao armazenamento de dados subjacente.

### <a name="what-identity-provider-do-you-support"></a>A qual provedor de identidade você dá suporte?

Atualmente, damos suporte a Microsoft Azure Active Directory como o provedor de identidade.

### <a name="what-is-the-recovery-point-objective-rpo-for-the-azure-api-for-fhir"></a>Qual é o RPO (objetivo de ponto de recuperação) para a API do Azure para FHIR?
A API do Azure para FHIR é apoiada por Cosmos DB como nosso provedor de persistência. Por isso, o RPO para o serviço é igual a [Cosmos dB (região única)](../../cosmos-db/consistency-levels.md) e é < de 240 minutos.

### <a name="what-fhir-version-do-you-support"></a>A qual versão do FHIR você dá suporte?

Damos suporte às versões 4.0.0 e 3.0.1 tanto na API do Azure para FHIR (PaaS) quanto no servidor FHIR para o Azure (código aberto).

Para obter detalhes, consulte [recursos com suporte](fhir-features-supported.md). Leia sobre o que mudou entre as versões do FHIR (ou seja, STU3 para R4) no [histórico de versão para HL7 FHIR](https://hl7.org/fhir/R4/history.html).

O conector do Azure IoT para FHIR (visualização) atualmente dá suporte apenas à versão R4 do FHIR e só é visível em instâncias R4 da API do Azure para FHIR.

### <a name="whats-the-difference-between-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Qual é a diferença entre "Microsoft FHIR Server para Azure" e "API do Azure para FHIR"?

A API do Azure para FHIR é uma versão hospedada e gerenciada do Microsoft FHIR Server de software livre para o Azure. No serviço gerenciado, a Microsoft fornece todas as atualizações e manutenção. 

Ao executar o servidor FHIR para o Azure, você tem acesso direto aos serviços subjacentes, mas é responsável por manter e atualizar o servidor e todo o trabalho de conformidade necessário se você estiver armazenando PHI dados.

Para um ponto de vista de desenvolvimento, todos os recursos que não se aplicam apenas ao serviço gerenciado são primeiro implantados no Microsoft FHIR Server de software livre para o Azure. Depois de ser validado em código-fonte aberto, ele será liberado para a solução de PaaS API do Azure para FHIR. O tempo entre a versão em software livre e PaaS depende da complexidade do recurso e de outras prioridades de roteiro. Esse é o mesmo processo para todos os nossos serviços, como o conector IoT do Azure para FHIR (versão prévia).

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Onde posso ver o que está lançando na API do Azure para FHIR?

Para ver algumas das novidades na API do Azure para FHIR, consulte a [versão](https://github.com/microsoft/fhir-server/releases) do servidor FHIR de código-fonte aberto. A partir de novembro de 2020, marcamos itens com o Azure-API-for-FHIR se o item de código-fonte aberto for liberado para o serviço gerenciado. Em geral, esses recursos estão disponíveis duas semanas depois que estão na página de lançamento em código-fonte aberto. Também incluímos instruções sobre como testar a compilação [aqui] ( https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) se você quiser testar em seu próprio ambiente. Estamos avaliando como compartilhar melhor as atualizações de serviço gerenciados adicionais.

### <a name="in-which-regions-is-azure-api-for-fhir-available"></a>Em quais regiões a API do Azure para FHIR está disponível?

Atualmente, temos disponibilidade geral para o público e o governo em [várias regiões geográficas](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=non-regional,us-east,us-east-2,us-central,us-north-central,us-south-central,us-west-central,us-west,us-west-2,canada-east,canada-central,usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia). Para obter informações sobre os serviços de nuvem do governo na Microsoft, confira os [Serviços do Azure por FedRAMP](../../azure-government/compliance/azure-services-in-fedramp-auditscope.md).

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Onde posso ver o que está lançando na API do Azure para FHIR?

Para ver algumas das novidades na API do Azure para FHIR, consulte a [versão](https://github.com/microsoft/fhir-server/releases) do servidor FHIR de código-fonte aberto. Trabalhamos para marcar itens com o Azure-API-for-FHIR se eles forem liberados para o serviço gerenciado e estiverem geralmente disponíveis duas semanas depois que estiverem na página de lançamento em código aberto. Também incluímos instruções sobre como testar a compilação [aqui](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) se você quiser testar em seu próprio ambiente. Estamos avaliando como compartilhar melhor as atualizações de serviço gerenciados adicionais.

### <a name="what-is-smart-on-fhir"></a>O que é inteligente no FHIR?

SMART (aplicativos médicos substituíveis e tecnologia reutilizável) em FHIR é um conjunto de especificações abertas para integrar aplicativos de parceiros com servidores FHIR e outros sistemas de ti de saúde, como registros eletrônicos de integridade e trocas de informações de integridade. Ao criar um aplicativo inteligente no FHIR, você pode garantir que seu aplicativo possa ser acessado e utilizado por uma infinidade de sistemas diferentes.
Autenticação e API do Azure para FHIR. Para saber mais sobre o SMART, visite o [Smart Health IT](https://smarthealthit.org/).

### <a name="where-can-i-find-what-version-of-fhir-is-running-on-my-database"></a>Onde posso encontrar qual versão do FHIR está em execução no meu banco de dados. 

Você pode encontrar a versão exata do FHIR exposta na instrução Capability na propriedade "fhirVersion".

## <a name="fhir-implementations-and-specifications"></a>Especificações e implementações de FHIR

### <a name="can-i-create-a-custom-fhir-resource"></a>Posso criar um recurso personalizado do FHIR?

Não permitimos recursos de FHIR personalizados. Se precisar de um recurso FHIR personalizado, você poderá criar um recurso personalizado sobre o [recurso básico](http://www.hl7.org/fhir/basic.html) com extensões. 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>Há suporte para [extensões](https://www.hl7.org/fhir/extensibility.html) na API do Azure para FHIR?

Permitimos que você carregue dados JSON FHIR válidos no servidor. Se você quiser armazenar a definição de estrutura que define a extensão, poderá salvá-la como um recurso de definição de estrutura. No momento, não é possível Pesquisar nas extensões.

### <a name="what-is-the-limit-on-_count"></a>Qual é o limite de _count?

O limite atual de _count é 100. Se você definir _count para mais de 100, receberá um aviso no grupo que apenas 100 registros serão mostrados.

### <a name="are-there-any-limitations-on-the-group-export-functionality"></a>Há alguma limitação sobre a funcionalidade de exportação de grupo?

Para a exportação de grupo, só exportamos as referências incluídas do grupo, e não todas as características do [recurso de grupo](https://www.hl7.org/fhir/group.html).

### <a name="can-i-post-a-bundle-to-the-azure-api-for-fhir"></a>Posso postar um pacote na API do Azure para FHIR?

Atualmente, damos suporte a [pacotes de lote](https://www.hl7.org/fhir/valueset-bundle-type.html) de lançamento, mas não dão suporte a pacotes de transação de lançamento na API do Azure para FHIR. Você pode usar o servidor FHIR de software livre apoiado pelo SQL para lançar pacotes de transação.

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-azure-api-for-fhir"></a>Como posso obter todos os recursos para um único paciente na API do Azure para FHIR?

Damos suporte à [pesquisa de compartimento](https://www.hl7.org/fhir/compartmentdefinition.html) na API do Azure para FHIR. Isso permite que você obtenha todos os recursos relacionados a um paciente específico. Observe que o compartimento agora inclui todos os recursos relacionados ao paciente, mas não o próprio paciente, para que você também precise Pesquisar para obter o paciente se precisar do recurso do paciente em seus resultados.

Alguns exemplos disso estão abaixo:

* OBTER paciente/<id>/*
* OBTER paciente/ <id> /Observation
* OBTER paciente/ <id> /Observation? código = 8302-2

### <a name="what-is-the-default-sort-when-searching-for-resources-in-azure-api-for-fhir"></a>Qual é a classificação padrão ao pesquisar recursos na API do Azure para FHIR?

Damos suporte à classificação pela data da última atualização: _sort = _lastUpdated. Para obter mais informações sobre outros parâmetros de pesquisa com suporte, Confira nossa [página de recursos com suporte](fhir-features-supported.md#search).

### <a name="does-the-azure-api-for-fhir-support-everything"></a>A API do Azure para FHIR dá suporte a $everything? 

Não. Neste momento, não damos suporte a $everything. No entanto, isso pode ser obtido com duas chamadas à API. Por exemplo, para obter o paciente $ tudo, você pode primeiro pegar o registro do paciente usando/Patient/[ID] e, em seguida, uma segunda chamada para recuperar todos os dados do paciente usando/Patient/[ID]/*.

Você pode ver mais detalhes nesta [postagem da Comunidade](https://chat.fhir.org/#narrow/stream/179166-implementers/topic/.24everything.20with.20_type). 

### <a name="how-does-export-work"></a>Como funciona $export?

$export faz parte da especificação FHIR: https://hl7.org/fhir/uv/bulkdata/export/index.html . Se o serviço FHIR estiver configurado com uma identidade gerenciada e uma conta de armazenamento, e se a identidade gerenciada tiver acesso a essa conta de armazenamento, você poderá simplesmente chamar $export na API FHIR e todos os recursos do FHIR serão exportados para a conta de armazenamento. Para obter mais informações, confira nosso [artigo sobre $Export](export-data.md).

### <a name="is-de-identified-export-available-at-patient-and-group-level-as-well"></a>A exportação desidentificada está disponível em nível de paciente e grupo também?
Atualmente, a exportação anônima tem suporte apenas em uma exportação completa do sistema (/$export) e não na exportação do paciente (/patient/$export). Estamos trabalhando para disponibilizá-lo no nível do paciente também.

## <a name="using-azure-api-for-fhir"></a>Usando a API do Azure para FHIR

### <a name="how-do-i-enable-log-analytics-for-azure-api-for-fhir"></a>Como fazer habilitar o log Analytics para a API do Azure para FHIR?

Habilitamos o log de diagnóstico e permitimos a revisão de consultas de exemplo para esses logs. Para obter detalhes sobre como habilitar logs de auditoria e consultas de exemplo, confira [esta seção](enable-diagnostic-logging.md). Se você quiser incluir informações adicionais nos logs, confira [usando cabeçalhos HTTP personalizados](use-custom-headers.md).

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>Onde posso ver alguns exemplos de como usar a API do Azure para FHIR em um fluxo de trabalho?

Temos uma coleção de arquiteturas de referência disponíveis na [página do GitHub da arquitetura de integridade](https://github.com/microsoft/health-architectures).

### <a name="where-can-i-see-an-example-of-connecting-a-web-application-to-azure-api-for-fhir"></a>Onde posso ver um exemplo de como conectar um aplicativo Web à API do Azure para FHIR?

Temos uma [página do GitHub de arquitetura de integridade](https://aka.ms/health-architectures) que contém aplicativos de exemplo e cenários. Ele ilustra como conectar um aplicativo Web à API do Azure para FHIR.  

## <a name="azure-api-for-fhir-features-and-services"></a>API do Azure para serviços e recursos do FHIR 

### <a name="is-there-a-way-to-encrypt-my-data-using-my-personal-key-not-a-default-key"></a>Há uma maneira de criptografar meus dados usando minha chave pessoal, não uma chave padrão?

Sim, a API do Azure para FHIR permite configurar chaves gerenciadas pelo cliente, aproveitando o suporte do Cosmos DB. Para obter mais informações sobre como criptografar seus dados com uma chave pessoal, confira [esta seção](customer-managed-key.md).

## <a name="azure-api-for-fhir-preview-features"></a>API do Azure para FHIR: recursos de visualização

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>Posso configurar a capacidade de dimensionamento para o conector do Azure IoT para FHIR (versão prévia)?

Como o conector do Azure IoT para FHIR é gratuito durante a visualização pública, sua capacidade de dimensionamento é fixa e limitada. O conector IoT do Azure para a configuração FHIR disponível na visualização pública é esperado para fornecer uma taxa de transferência de cerca de 200 mensagens por segundo. Alguma forma de configuração de capacidade de recursos será disponibilizada em GA (disponibilidade geral).

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Por que não consigo instalar o conector do Azure IoT para FHIR (versão prévia) quando o link privado está habilitado na API do Azure para FHIR?

O conector IoT do Azure para FHIR não dá suporte à funcionalidade de link privado no momento. Portanto, se você tiver o link privado habilitado na API do Azure para FHIR, não será possível instalar o conector do Azure IoT para FHIR e vice-versa. Essa limitação deve desaparecer quando o conector do Azure IoT para FHIR estiver disponível para GA (disponibilidade geral).
