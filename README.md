# Desafio Final: Cálculo de Frete

## Cenário
Entendemos os fundamentos e funcionamento de uma Trigger, e para consolidar esse conhecimento adquirido, vamos aplicá-lo em um cenário real.

Dessa vez, o nosso desafio será relacionado a Frete. Os requisitos do desafio serão descritos na forma de EQPA, uma forma bastante utilizada no meio do desenvolvimento de software.

As descrições contam com:

- **( E )** Quem quer o requisito; 
- **( Q )** Qual o requisito; 
- **( P )** Qual é o objetivo da execução do requisito; 
- **( A )** Critério de aceite.

Como macro divisão, vamos estabelecer três etapas:
- Cenário
- Requisitos de Modelagem
- Requisitos de Desenvolvimento

Conhecer o cenário completo da aplicação é essencial para que possamos desenvolver da melhor forma possível, enxergando o
projeto como um todo e não apenas um trecho isolado.

## Cenário: Frete
Uma empresa está enfrentando problemas em relação ao frete de seus pedidos no que se refere a seleção dos melhores centros
de distribuição e também ao cálculo do valor de frete. A fim de solucionar essa problemática, a empresa decidiu realizar a
implementação de um CRM e contratou uma consultoria para auxiliar nesse importante processo. O foco desse projeto é fazer
com que a empresa tenha um sistema de pedidos com cálculo e seleção de frete automatizado, robusto e único.

Entendido o cenário, vamos aos requisitos específicos.

> **Antes de iniciarmos esse desafio, devemos criar um novo ambiente de desenvolvimento (org), para que assim tudo que fizemos nos desafios anteriores não afetem o desenvolvimento a partir de agora.**

## Requisitos de Modelagem
Agora vamos aos nossos requisitos específicos de modelagem.

## Cadastro de Contas

**E**: Equipe de Negócios
**Q**: Cadastrar Clientes no Sistema
**P**: Manter a base de Clientes atualizada na plataforma.
**A**:
Aceito cadastrar o cliente informando seus dados básicos, como o Nome, e Endereço de entrega. O endereço de entrega deverá
ser composto por CEP, Cidade e Estado obrigatoriamente. Contas que não possuam todos esses dados não devem ser aceitas pelo sistema.

## Cadastro de Parâmetros Frete

**E**: Equipe de Negócios
**Q**: Cadastrar os Parâmetros de Frete
**P**: Realizar e associar o cálculo de frete adequadamente aos pedidos do e-commerce.
**A**:
Aceita cadastrar o frete com suas principais chaves e informações de preço. O frete tem como chave a **localidade de entrega**, podendo ser em três níveis:

- Frete por Estado
- Frete por Cidade
- Frete por CEP

O frete possui três valores diferentes e podem ser aplicados de acordo com o pedido:

1. Frete por Volume
2. Frete por Peso
3. Frete Base

O frete deve obrigatoriamente possuir as três vertentes de preço, diferente disso não deverão ser aceitos pelo sistema.

Temos uma regra de especificidades: Fretes por CEP são mais específicos que Fretes por Cidade, que por sua vez são mais
específicos que Fretes por Estado sendo que no cadastro de frete, **apenas um desses campos de localidade deve ser
preenchido**, e não os três. Fretes que não possuam o campo de localidade cadastrados não deverão ser aceitos pelo sistema.

## Cadastro de Pedidos
**E**: Equipe de Negócios
**Q**: Cadastrar os Pedidos de Vendas para os Clientes
**P**: Manter o funcionamento dos pedidos no e-commerce.
**A**:
Aceita cadastrar o pedido informando de qual cliente o pedido pertence. **O endereço de entrega do pedido deverá ser o
endereço de entrega relativo do seu cliente.**

O local de origem do Pedido **(Centro de Distribuição)** deverá ser atrelado automaticamente pelo sistema seguindo a **"Lógica
de Otimização de Seleção do Centro de Distribuição".**

Se alguma informação crucial do Pedido for alterada, o Centro de Distribuição selecionado poderá sofrer uma realocação, e seu
Frete poderá ser recalculado. Se houver alguma atualização nos itens do pedido (atualização de itens já existentes deleções,
inserções de novos), o Centro de Distribuição do pedido poderá sofrer uma realocação, e seu frete também poderá ser
recalculado.

Enquanto o pedido estiver em status de **"Draft"**, os campos poderão ser atualizados. Após ser finalizado, o pedido deve ser
bloqueado para alterações, assim como os itens do pedido.


O Cliente relativo ao Pedido não pode ser alterado em momento algum. O Centro de Distribuição do pedido também não pode
ser alterado de maneira manual em momento algum.

Para cadastro do Item do Pedido, seus dados primordiais, como Produto e Quantidade devem ser informados obrigatoriamente.
Também é obrigatório informar o **Volume em Metros Cúbicos do item, e seu Peso em Quilogramas.**

Itens que não contenham essas informações não deverão ser aceitos pela plataforma.

As **especificações** da modelagem proposta estão na seguinte planilha:

**State (Custom)**

![enter image description here](https://user-images.githubusercontent.com/32878844/202536949-7f7f0d85-c103-48a5-97b9-7c576a40c4a7.jpg)

**City (Custom)**

![enter image description here](https://user-images.githubusercontent.com/32878844/202537206-13cff211-39b0-45b6-b5f2-17615bda497e.jpg)

**DistributionCenter (Custom)**

![enter image description here](https://user-images.githubusercontent.com/32878844/202537533-71047d3d-7027-4b0d-8f8d-afaa5505f8c3.jpg)

**Freight (Custom)**

![enter image description here](https://user-images.githubusercontent.com/32878844/202537660-6bfe7464-b975-4efc-acbb-a2482d52ffa1.jpg)

**Account (Standard)**

![enter image description here](https://user-images.githubusercontent.com/32878844/202537787-698570fd-4d01-4752-8e1a-3ea96d35074a.jpg)

**Order (Standard)**

![enter image description here](https://user-images.githubusercontent.com/32878844/202537894-2e91bc0e-2618-4b1e-9137-97b71ff8e6a0.jpg)

**Orderltem (Standard)**

![enter image description here](https://user-images.githubusercontent.com/32878844/202538011-445aac98-3541-46df-a523-28df6620e4a9.jpg)

# Requisitos de Desenvolvimento

## Regra de Busca do Frete

**E**: Equipe de Negócios
**Q**: Aplicar a lógica para a busca automática do frete nos pedidos do E-commerce
**P**: Encontrar fretes condizentes aos Pedidos
**A**:
 
Aceito que o sistema seja capaz de buscar fretes que se enquadrem adequadamente aos pedidos do E-commerce. Para
encontrar uma linha de parametrização de frete, o sistema deve seguir o seguinte procedimento:

Todos os parâmetros de frete possuem um indicador de localidade: CEB cidade, ou estado. Todos os pedidos possuem
obrigatoriamente três indicadores de localidade: CEP, Cidade, e Estado.

O sistema deverá buscar por fretes que possuem indicadores de localidade condizentes com o pedido corrente.

O sistema deverá aplicar níveis de prioridade:

- Prioridade 1: Encontrar um parâmetro de frete que enquadre CEP
- Prioridade 2: Encontrar um parâmetro de frete que enquadre cidade
- Prioridade 3: Encontrar um parâmetro de frete que enquadre estado

Ou seja, caso encontremos mais de uma linha de frete que se enquadre em nosso pedido, devemos utilizar o nosso critério de
prioridade para selecionar a linha mais específica de frete.

Parâmetros de frete que se enquadrem em qualquer um dos três níveis de prioridade são elegíveis para serem associados ao
pedido.

As prioridades entram em vigor para definição do frete mais prioritário na **"Lógica de Otimização da Seleção do Centro de
Distribuição".**

## Regra de Cálculo do Frete

**E**: Equipe de Negócios
**Q**: Aplicar a lógica para o cálculo automático de frete nos pedidos do E-commerce
**P**: Calcular adequadamente o valor de transporte do pedido.
**A**:

Aceito que o sistema seja capaz de calcular o frete adequadamente para pedidos do E-commerce.

Ao encontrar uma **linha de parametrização de frete**, o sistema deverá seguir o seguinte procedimento para encontrar o valor de frete no pedido referente:

Todos os parâmetros de frete possuem três vertentes de preço: volume, peso e quantidade de produto, que seria o frete base.
Todos os itens do pedido possuem três possibilidades de cálculo: o volume total do item, peso total do item e quantidade total
de produtos.

O sistema deverá somar todas as informações dos produtos do pedido e aplicar seu multiplicador relativo.

> 1. Para realizar o cálculo do frete por volume, vamos fazer o seguinte: somar o volume total de todos os produtos do pedido, e multiplicar pelo frete por volume do parâmetro encontrado. Ou seja: (Soma(volume do item do pedido)) * frete por volume
> 
> 2. Para fazer o cálculo do frete por peso: somar o peso total de todos os produtos do pedido e, multiplicar pelo frete por peso do parâmetro encontrado: (Soma(peso do item do pedido)) frete por peso
> 
> 3. Para fazer o cálculo do frete base: somar a quantidade total de todos os produtos do pedido, e multiplicar pelo frete base do
> parâmetro. Ou seja: (Soma(quantidade do item do pedido)) frete base


Com os três cálculos já realizados, a vertente de frete escolhida deve ser **a maior dentre os três tipos calculados** e este é o valor
que deverá ser considerado para a "competição de frete" e, consequentemente, o valor que será tomado como valor de frete
do pedido.

## Lógica de Otimização da Seleção do Centro de Distribuição

**E**: Equipe de Negócios
**Q**: Realizar uma lógica para o cálculo automático de frete nos pedidos do E-commerce
**P**: Calcular adequadamente o valor de transporte do pedido.
**A**:

Aceito que o sistema seja capaz de calcular o frete adequadamente para os pedidos do E-commerce.

O sistema deverá **"competir" os fretes elegíveis encontrados** para definir qual **Centro de Distribuição e Frete** será associado no pedido, seguindo o seguinte procedimento:

Os fretes deverão ser agrupados por **Centro de Distribuição**, e o Frete mais específico de cada Centro será elegível para a
competição (já com sua maior vertente de frete calculada).

A competição de frete consiste na seguinte regra:

- O centro de distribuição que possui o **menor valor de frete**, será o CD escolhido para este pedido.
- Consequentemente, o frete **mais específico** deste centro de distribuição, será escolhido para o pedido.

Assim, finalizamos os requisitos específicos de desenvolvimento. Agora, já analisado o cenário, a modelagem e o desenvolvimento, podemos partir para a codificação.

Conforme os **EQPA's** apresentados, desenvolva o projeto.

## Estruturando a construção da trigger

A trigger a ser construída deverá ser disparada toda vez que itens forem incluídos ou alterados no pedido. Lembrando que a
trigger deve estar preparada para calcular vários pedidos de uma única vez.

Antes de começarmos a identificação do frete, precisamos primeiro calcular os **valores totais** de Volume, Peso e quantidade **para cada pedido** que sofreu alteração.

Na sequência, para identificarmos o Centro de Distribuição com menor preço, temos que encontrar o registro de frete mais
específico de cada CD, utilizando as informações do local de entrega (Estado, Cidade, CEP) das contas referentes a todos os
pedidos que sofreram alteração.

Para isso, precisamos identificar o registro de frete mais específico segundo a regra:

**CEP > Cidade > Estado**

Assim, se o Centro de Distribuição possuir um registro de frete com mesmo **CEP** da conta, este será o escolhido, senão,
procuraremos um com a mesma **Cidade** da conta e por último, caso nenhum requisito tenha sido atendido, selecionaremos um registro de frete com valor de **Estado** igual ao da conta.

![enter image description here](https://s3.sa-east-1.amazonaws.com/static.pragma.school/images/V02C04J04T09S03I01.png)

Após a escolha do registro de frete mais específico para cada centro de distribuição, deve-se calcular os valores referentes às
modalidades de frete, custos de frete por **Volume**, por **Peso** e por **Quantidade**. Utilizaremos o valor mais elevado dentre os três para cobrir os custos de transporte referente àquele centro de distribuição.

Por último, iremos ranquear os centros de distribuição de acordo com os valores de fretes calculados, onde o centro de
distribuição com o **menor valor** será vinculado à ordem.

![enter image description here](https://s3.sa-east-1.amazonaws.com/static.pragma.school/images/V02C04J04T09S03I02.png)
#
# Desafio Final: Teste Unitário

# Teste Unitário de Trigger

## Associação de Frete ao inserir um Produto.

Antes de começarmos a sequência de teste é importante incluirmos todos os registros de **Frete** e **Pedidos**, isso nos auxiliará a visualizar melhor os testes e na obtenção dos resultados corretos.

Crie uma **Conta** com nome **Achieve Organic** com o endereço para **Curitiba - PR, CEP: 93542-231** (os registros de Estado e Cidade devem ser previamente criados nos seus respectivos objetos) crie um **Contrato** associado a essa **Conta**.

Ao fazer o teste unitário, não conseguimos inserir um **Pedido** com **Produtos** instantaneamente. Portanto, vamos criar um
**Pedido** e logo adicionar itens a ele.

Para conseguir que o melhor **Frete** do **Pedido** seja associado a ele, deve no mínimo cumprir os requisitos de compatibilidade de **Frete**. Para isso, criamos o **Centro de Distribuição Bulk Demand*** **(complete o campo Estado com Paraná), e associamos dois** Fretes* *.

![enter image description here](https://s3.sa-east-1.amazonaws.com/static.pragma.school/images/V02C04J04T10S01I01.png)

![enter image description here](https://s3.sa-east-1.amazonaws.com/static.pragma.school/images/V02C04J04T10S01I02.png)

Iremos adicionar um **Produto** ao **Pedido**, deve conter os seguintes itens:

![enter image description here](https://user-images.githubusercontent.com/32878844/202541609-69c020ae-e071-4e87-bd45-7b9defe0d5c3.jpg)

O programa deverá preencher os campos de **Freight** com o **FRETE 1** (no nome automático Salesforce F-0034, lembrando que este numeração muda em cada Org), pois esse é o único que se encaixa na localização da **Conta** da **Achieve Organic** deverá ser preenchido também o campo **Total Freight** com o valor de R$1300,00.

![enter image description here](https://s3.sa-east-1.amazonaws.com/static.pragma.school/images/V02C04J04T10S01I03.png)

## Coloque em Prática!

Nesta etapa iremos testar se seu código atende aos requisitos mínimos para realização de um teste unitário, que prevê uma
associação simples de Frete ao Pedido. Antes de apertar o botão, confira se os testes manuais mencionados acima estão de
acordo com o esperado.
