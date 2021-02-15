---
title: Entidades nomeadas para saúde
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 10/02/2020
ms.author: aahi
ms.openlocfilehash: 614d0fe69cee88791559758d5e08dda66672669b
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097246"
---
[Análise de texto para integridade](../../how-tos/text-analytics-for-health.md) detecta os conceitos médicos nas categorias a seguir.  (Observe que somente o texto em inglês tem suporte nesta visualização de contêiner e apenas uma única versão de modelo é fornecida em cada imagem de contêiner.)


| Categoria  | Descrição  |
|---------|---------|
| [Anatomia](#anatomy) | conceitos que capturam informações sobre o corpo e sistemas, sites, locais ou regiões. |
 | [DADOS demográficos](#demographics) | conceitos que capturam informações sobre sexo e idade. |
 | [EXAME](#examinations) | conceitos que capturam informações sobre testes e procedimentos de diagnóstico. |
 | [GENOMICS](#genomics) | conceitos que capturam informações sobre genes e variantes. |
 | [EMPRESAS](#healthcare) | conceitos que capturam informações sobre eventos administrativos, ambientes de cuidado e prestações de saúde. |
 | [CONDIÇÃO MÉDICA](#medical-condition) | conceitos que capturam informações sobre diagnósticos, sintomas ou sinais. |
 | [MEDICAMENTOS](#medication) | conceitos que capturam informações sobre medicamentos, incluindo nomes medicamentos, classes, dosage e rota de administração. |
 | [RG](#social) | conceitos que capturam informações sobre aspectos sociais de medicinaidade relevantes, como a relação de família. |
 | [TRATAMENTOS](#treatment) | conceitos que capturam informações sobre procedimentos therapeutic. |
  
Cada categoria pode incluir dois grupos de conceito:

* **Entidades** – termos que capturam conceitos médicos como diagnóstico, nome do medicamentos ou nome do tratamento.  Por exemplo, *bronchitis* é um diagnóstico e *Aspirin* é um nome medicamentos.  As menção nesse grupo podem estar vinculadas a uma ID de conceito de UMLS.
* **Atributos** -frases que fornecem mais informações sobre a entidade detectada, por exemplo, *severo* é um qualificador de condição para *bronchitis* ou *81 mg* é um dosage para *Aspirin*.  As menção nesta categoria não serão vinculadas a uma ID de conceito UMLS.

Além disso, o serviço reconhece as relações entre os diferentes conceitos, incluindo relações entre atributos e entidades, por exemplo, *direção* para a *estrutura de corpo* ou *dosage* para *medicamentos nome* e relações entre entidades, por exemplo, na detecção de abreviação.

## <a name="anatomy"></a>Anatomia

### <a name="entities"></a>Entidades

Sistemas de **BODY_STRUCTURE** corpo, locais ou regiões de anatômicas e sites de corpo. Por exemplo, ARM, joelho, abdomen, nariz, liver, Head, respiratory System, lymphocytes.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Um exemplo da entidade de estrutura do corpo.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Um exemplo expandido da entidade de estrutura do corpo.":::

### <a name="attributes"></a>Atributos

Termos **direcionais** , como: esquerda, lateral, superior, posteriores, que caracteriza uma estrutura de corpo.

:::image type="content" source="../../media/ta-for-health/anatomy-attributes.png" alt-text="Um exemplo de um atributo direcional.":::

### <a name="supported-relations"></a>Relações com suporte

* **DIRECTION_OF_BODY_STRUCTURE**

## <a name="demographics"></a>Dados demográficos

### <a name="entities"></a>Entidades

**Idade** – todos os termos e frases do Age, incluindo aqueles de um paciente, membros da família e outros. Por exemplo, 40-ano-antigo, 51 Yo, 3 meses antigos, adulto, Infant, idosa, Young, menor, médio.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Um exemplo de uma entidade etária.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Outro exemplo de uma entidade etária.":::


**Gênero** -termos que divulgam o gênero do assunto. Por exemplo, masculino, feminino, mulher, cavalheiro, senhora.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Um exemplo de uma entidade de gênero.":::

### <a name="attributes"></a>Atributos

**RELATIONAL_OPERATOR** -frases que expressam a relação entre uma entidade demográfica e informações adicionais.

:::image type="content" source="../../media/ta-for-health/relational-operator.png" alt-text="Um exemplo de um operador relacional.":::

## <a name="examinations"></a>Análises

### <a name="entities"></a>Entidades

**EXAMINATION_NAME** – procedimentos e testes de diagnóstico. Por exemplo, MRI, ECG, teste de HIV, Hemoglobin, contagem de platelets, sistemas de escala como *Bristol Stool Scale*.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Um exemplo de uma entidade de exame.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="Outro exemplo de uma entidade de nome de exame.":::

### <a name="attributes"></a>Atributos

**Direção** – termos direcionais que caracterizam um exame.

:::image type="content" source="../../media/ta-for-health/exam-direction-attribute.png" alt-text="Um exemplo de um atributo de direção com uma entidade de nome de exame.":::

**MEASUREMENT_UNIT** – a unidade do exame. Por exemplo, em *hemoglobin > 9,5 g/dl*, o termo *g/dl* é a unidade para o teste *Hemoglobin* .

:::image type="content" source="../../media/ta-for-health/exam-unit-attribute.png" alt-text="Um exemplo de um atributo de unidade de medida com uma entidade de nome de exame.":::

**MEASUREMENT_VALUE** – o valor do exame. Por exemplo, em *hemoglobin > 9,5 g/dl*, o termo *9,5* é o valor para o teste *Hemoglobin* .

:::image type="content" source="../../media/ta-for-health/exam-value-attribute.png" alt-text="Um exemplo de um atributo de valor de medição com uma entidade de nome de exame.":::

**RELATIONAL_OPERATOR** – frases que expressam a relação entre um exame e informações adicionais. Por exemplo, o valor de medição necessário para um exame de destino.

:::image type="content" source="../../media/ta-for-health/exam-relational-operator-attribute.png" alt-text="Um exemplo de um operador relacional com uma entidade de nome de exame.":::

**Tempo** – termos temporais relacionados ao início e/ou comprimento (duração) de um exame. Por exemplo, quando o teste ocorreu.

:::image type="content" source="../../media/ta-for-health/exam-time-attribute.png" alt-text="Um exemplo de um atributo de tempo com uma entidade de nome de exame.":::

### <a name="supported-relations"></a>Relações com suporte

+ **DIRECTION_OF_EXAMINATION**
+   **RELATION_OF_EXAMINATION**
+   **TIME_OF_EXAMINATION**
+   **UNIT_OF_EXAMINATION**
+   **VALUE_OF_EXAMINATION**

## <a name="genomics"></a>Genomics

### <a name="entities"></a>Entidades

**Gene** – todas as menção de genes. Por exemplo, MTRR, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Um exemplo de uma entidade de Gene.":::

**Variante** – todas as menção de gene variações. Por exemplo, c. 524C>T, (MTRR): r.1462_1557del96
  
## <a name="healthcare"></a>Serviços de saúde

### <a name="entities"></a>Entidades
  
**ADMINISTRATIVE_EVENT** – eventos relacionados ao sistema de saúde, mas de natureza administrativa/semiadministrativa. Por exemplo, registro, admissão, avaliação, entrada de estudo, transferência, descarga, hospitalização, hospital permanecer. 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="Um exemplo de uma entidade de evento de saúde.":::

**CARE_ENVIRONMENT** – um ambiente ou local onde os pacientes são considerados cuidados. Por exemplo, sala de emergência, escritório do médico, unidade cardio, Hospice, hospital.

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="Esta captura de tela mostra um exemplo de uma entidade de ambiente de saúde.":::

**HEALTHCARE_PROFESSION** – um profissional de saúde licenciado ou não licenciado. Por exemplo, dentista, Pathologist, neurologist, radiologist, Pharmacist, nutritionist, Physical therapist, chiropractor.

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="Esta captura de tela mostra outro exemplo de uma entidade de ambiente de saúde.":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="Outro exemplo de uma entidade de ambiente de saúde.":::

## <a name="medical-condition"></a>Condição médica

### <a name="entities"></a>Entidades

**Diagnóstico** – doença, síndrome, envenenamento. Por exemplo, mama câncer, Alzheimer, HTN, CHF, lesão de cabo de giro.

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="Um exemplo de uma entidade de condição médica.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="Outro exemplo de uma entidade de condição médica.":::

**SYMPTOM_OR_SIGN** – evidência subjetiva ou objetiva de doenças ou outros diagnósticos. Por exemplo, conjunto dor, dor de cabeça, desmaios, irritação, SOB, abdomen era flexível, boa bowel Sounds, bem nourished.

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="Um exemplo de um sinal de condição médica ou uma entidade de sintoma.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="Outro exemplo de uma entidade de sinal de condição médica ou de sintoma.":::

### <a name="attributes"></a>Atributos

Termos de **CONDITION_QUALIFIER** qualidade usados para descrever uma condição médica. Todas as subcategorias a seguir são consideradas qualificadores:

1.  Expressões relacionadas a tempo: são termos que descrevem a dimensão de tempo qualitativamente, como repentina, acento agudo, crônicos, permanente. 
2.  Expressões de qualidade: são termos que descrevem a "natureza" da condição médica, como a gravação, nítida.
3.  Expressões de severidade: grave, leve, um pouco, não controlado.
4.  Expressões Extensivity: local, focal, difuso.
5.  Expressões de radiação: radiates, radiação.
6.  Escala de condição: em alguns casos, uma condição é caracterizada por uma escala, que é uma lista de valores ordenada finito. Por exemplo, pacientes com o Stage III pancreatic câncer.
7.  Curso de condição: um termo relacionado ao curso ou à progressão de uma condição, como aprimoramento, deterioração, resolução, remissidade. 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="Um exemplo de um atributo de qualificador de condição e uma entidade de diagnóstico.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="Outro exemplo de um atributo de qualificador de condição e uma entidade de diagnóstico.":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="Um exemplo de um atributo de qualificador de condição com entidades sintoma e medicamentos.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="Esta captura de tela mostra outro exemplo de um atributo de qualificador de condição com uma entidade de diagnóstico.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="Esta captura de tela mostra um exemplo adicional de um atributo de qualificador de condição com uma entidade de diagnóstico.":::

Termos direcionais de **direção** que caracterizam uma condição médica de corpo.

:::image type="content" source="../../media/ta-for-health/medical-condition-direction-attribute.png" alt-text="Um exemplo de um atributo de direção com uma entidade de condição médica.":::

**Frequência** -com que frequência uma condição médica ocorreu, ocorre ou deve ocorrer.

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute.png" alt-text="Um exemplo de um atributo Frequency com uma entidade de condição médica.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute-2.png" alt-text="Outro exemplo de um atributo de direção com uma entidade de sintoma ou sinal.":::

**MEASUREMENT_UNIT** -a unidade que caracteriza uma condição médica. Por exemplo, em *1,5 X2X1 cm tumor*, o termo *cm* é a unidade de medida para o *tumor*. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-unit-attribute.png" alt-text="Um exemplo de um atributo de unidade de medida com entidade de condição médica.":::

**MEASUREMENT_VALUE** -o valor que caracteriza uma condição médica. Por exemplo, em *1,5 X2X1 cm tumor*, o termo *1.5 X2X1* é o valor de medida para o *tumor*. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-value-attribute.png" alt-text="Captura de tela mostra um exemplo de um atributo de direção com uma entidade sintoma ou sinal.":::

**RELATIONAL_OPERATOR** -frases que expressam a relação entre informações adicionais da condição médica. Por exemplo, tempo ou valor de medição. 

:::image type="content" source="../../media/ta-for-health/medical-condition-relational-operator.png" alt-text="Captura de tela mostra outro exemplo de um atributo de direção com uma entidade sintoma ou sinal.":::

**Tempo** -termos temporais relacionados ao início e/ou comprimento (duração) de uma condição médica. Por exemplo, quando um sintoma foi iniciado (início) ou quando ocorreu uma doença.

:::image type="content" source="../../media/ta-for-health/medical-condition-time-attribute.png" alt-text="Captura de tela mostra um exemplo adicional de um atributo de direção com uma entidade sintoma ou sinal.":::

### <a name="supported-relations"></a>Relações com suporte

+ **DIRECTION_OF_CONDITION**
+   **QUALIFIER_OF_CONDITION**
+   **TIME_OF_CONDITION**
+   **UNIT_OF_CONDITION**
+   **VALUE_OF_CONDITION**

## <a name="medication"></a>Medicamentos

### <a name="entities"></a>Entidades

**MEDICATION_CLASS** – um conjunto de medicamentos que têm um mecanismo de ação semelhante, um modo de ação relacionado, uma estrutura química semelhante e/ou são usados para tratar a mesma doença. Por exemplo, ACE Inhibitor, opioid, antibióticos, aliviadores problemáticos.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Um exemplo de uma entidade de classe medicamentos.":::

**MEDICATION_NAME** – medicamentos menciona, incluindo nomes de marca com direitos autorais e nomes que não são da marca. Por exemplo, Advil, Ibuprofen.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Um exemplo de uma entidade de nome de medicamentos.":::

### <a name="attributes"></a>Atributos

**DOSAGE** -quantidade de medicamentos ordenados. Por exemplo, Turbine sodium Chloride Solution *1000 ml*.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Um exemplo de um atributo medicamentos dosage.":::

**Frequência** -com que frequência um medicamentos deve ser obtido.

:::image type="content" source="../../media/ta-for-health/medication-frequency.png" alt-text="Um exemplo de um atributo de frequência medicamentos.":::

:::image type="content" source="../../media/ta-for-health/medication-frequency-2.png" alt-text="Outro exemplo de um atributo de frequência medicamentos.":::

**MEDICATION_FORM** -a forma do medicamentos. Por exemplo, solução, Pill, cápsula, Tablet, patch, gel, colar, espuma, spray, quedas, sorvete, Syrup.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Um exemplo de um atributo de formulário medicamentos.":::

**MEDICATION_ROUTE** -o método de administração de medicamentos. Por exemplo, oral, vaginal, IV, epidural, tópicas, Inhaled.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Um exemplo de um atributo de rota medicamentos.":::

**RELATIONAL_OPERATOR** -frases que expressam a relação entre medicamentos e informações adicionais. Por exemplo, o valor de medição necessário.

:::image type="content" source="../../media/ta-for-health/medication-relational-operator.png" alt-text="Captura de tela mostra um exemplo de um atributo de operador relacional com uma entidade medicamentos.":::

:::image type="content" source="../../media/ta-for-health/medication-time.png" alt-text="Captura de tela mostra outro exemplo de um atributo de operador relacional com uma entidade medicamentos.":::

### <a name="supported-relations"></a>Relações com suporte

+ **DOSAGE_OF_MEDICATION**
+   **FORM_OF_MEDICATION**
+   **FREQUENCY_OF_MEDICATION**
+   **ROUTE_OF_MEDICATION**
+   **TIME_OF_MEDICATION**

## <a name="social"></a>Social

### <a name="entities"></a>Entidades

**FAMILY_RELATION** – menciona os parentes da família do assunto. Por exemplo, pai, filha, irmãos, pais.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="Captura de tela mostra outro exemplo de um atributo de tempo de tratamento.":::

## <a name="treatment"></a>Tratamento

### <a name="entities"></a>Entidades

**TREATMENT_NAME** – procedimentos therapeutic. Por exemplo, joelho Replacement cirurgia, Bone Marrow transplant, TAVI, dieta.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Um exemplo de uma entidade de nome de tratamento.":::

### <a name="attributes"></a>Atributos

Termos direcionais de **direção** que caracterizam um tratamento.

:::image type="content" source="../../media/ta-for-health/treatment-direction.png" alt-text="Captura de tela mostra um exemplo de um atributo de direção de tratamento.":::

**Frequência** -com que frequência um tratamento ocorre ou deve ocorrer.

:::image type="content" source="../../media/ta-for-health/treatment-frequency.png" alt-text="Captura de tela mostra outro exemplo de um atributo de direção de tratamento.":::
 
**RELATIONAL_OPERATOR** -frases que expressam a relação entre tratamento e informações adicionais.  Por exemplo, quanto tempo foi passado do procedimento anterior.

:::image type="content" source="../../media/ta-for-health/treatment-relational-operator.png" alt-text="Um exemplo de um atributo de operador relacional de tratamento.":::

**Tempo** -termos temporais relacionados ao início e/ou comprimento (duração) de um tratamento. Por exemplo, a data em que o tratamento foi fornecido.

:::image type="content" source="../../media/ta-for-health/treatment-time.png" alt-text="Captura de tela mostra um exemplo de um atributo de tempo de tratamento.":::

### <a name="supported-relations"></a>Relações com suporte

+ **DIRECTION_OF_TREATMENT**
+   **TIME_OF_TREATMENT**
+   **FREQUENCY_OF_TREATMENT**
