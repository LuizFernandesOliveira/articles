# Pensamento orientado a componentes

Projetar um sistema orientado a componentes é decidir como dividir o software em unidades que possam ser compreendidas, desenvolvidas, testadas, implantadas e evoluídas com o menor acoplamento possível.

Essa divisão não é apenas uma organização visual de pastas ou pacotes. Um componente representa uma fronteira arquitetural: ele concentra responsabilidades relacionadas, expõe contratos para o restante do sistema e pode possuir um ciclo de vida próprio. A qualidade dessa divisão influencia diretamente a manutenção, a capacidade de mudança e o custo operacional da aplicação.

O objetivo não é criar o maior número possível de componentes, nem transformar todo sistema em uma arquitetura distribuída. O objetivo é encontrar fronteiras que expressem as responsabilidades do domínio e que continuem úteis à medida que o produto evolui.

## O que é um componente?

Um componente é uma unidade modular do sistema que reúne uma ou mais responsabilidades relacionadas e oferece seus serviços por meio de uma interface ou contrato bem definido.

Ele pode ser entendido como a manifestação física de um módulo. Enquanto um módulo é principalmente uma unidade lógica de organização do código, o componente é uma unidade que pode ser empacotada, versionada, testada ou implantada de maneira relativamente independente.

Dependendo do contexto, um componente pode assumir diferentes formas:

- **Biblioteca:** unidade reutilizável, geralmente incorporada ao processo da aplicação. É adequada para funcionalidades comuns, como validações, serialização ou acesso a uma capacidade compartilhada.
- **Subsistema ou camada:** agrupamento de classes e módulos que colaboram para cumprir uma responsabilidade técnica ou de negócio dentro da mesma aplicação.
- **Serviço:** unidade autônoma executada em seu próprio processo ou ambiente de execução. É útil quando uma capacidade precisa de isolamento operacional, escala independente ou ciclo de entrega próprio.

Essas formas não são níveis obrigatórios de uma mesma hierarquia. Um componente pode começar como um pacote dentro de um monólito e, caso surja uma necessidade real, tornar-se um serviço separado. A fronteira lógica deve ser definida antes da decisão de implantação.

## O que define uma boa fronteira?

Uma boa fronteira de componente combina **alta coesão** e **baixo acoplamento**:

- suas partes mudam pelas mesmas razões;
- suas responsabilidades formam um conjunto compreensível;
- suas dependências externas são explícitas;
- seu contrato é menor e mais estável do que sua implementação;
- alterações internas não exigem mudanças espalhadas por todo o sistema.

A fronteira também deve refletir decisões importantes do produto. Componentes não são apenas contêineres de código: eles representam quem é responsável por determinada capacidade, quais dados essa capacidade controla e como outras partes do sistema podem utilizá-la.

Uma pergunta útil é: **se uma regra de negócio mudar, quais partes do sistema deveriam ser alteradas juntas?** Essas partes tendem a pertencer ao mesmo componente. Outra pergunta é: **quais mudanças deveriam permanecer isoladas?** As respostas ajudam a revelar fronteiras melhores do que a simples organização por tipo de classe.

## Quem define e quem refina os componentes?

### O papel do arquiteto

O arquiteto define a visão de alto nível da arquitetura. Ele propõe as principais fronteiras, avalia dependências entre componentes e verifica se a estrutura atende aos requisitos funcionais e não funcionais, como segurança, disponibilidade, desempenho e escalabilidade.

Essa definição não deve ser tratada como um desenho imutável. O papel do arquiteto também inclui estabelecer critérios para revisar as fronteiras quando novas informações surgirem.

### O papel do desenvolvedor

O desenvolvedor refina os componentes em módulos, classes, interfaces, tabelas, endpoints e fluxos de código. Durante esse trabalho, ele identifica acoplamentos ocultos, responsabilidades mal distribuídas e regras que foram atribuídas ao componente errado.

Na prática, a arquitetura é construída por ciclos de colaboração. O arquiteto orienta as fronteiras; o time de desenvolvimento testa se elas são viáveis no código; e ambos ajustam o desenho com base no aprendizado obtido.

## Estratégias de particionamento arquitetural

Não existe uma única forma correta de dividir um sistema. Duas estratégias são especialmente comuns.

### Particionamento por camadas

No particionamento por camadas, os componentes são organizados segundo capacidades técnicas, como apresentação, aplicação, domínio e infraestrutura. Cada camada concentra um tipo de responsabilidade e se comunica com as demais por regras de dependência estabelecidas.

**Vantagens:**

- facilita a localização de responsabilidades técnicas;
- permite reutilizar implementações comuns;
- é familiar para a maioria das equipes;
- pode ser uma boa opção para sistemas pequenos e simples.

**Riscos:**

- uma única funcionalidade pode atravessar várias camadas;
- mudanças de negócio podem exigir alterações em muitos componentes;
- a reutilização técnica pode esconder dependências excessivas;
- o acoplamento global tende a crescer quando todas as funcionalidades compartilham as mesmas camadas.

### Particionamento por domínio

No particionamento por domínio, os componentes são organizados por capacidades de negócio ou por grupos de casos de uso. Cada componente concentra as regras, os dados e os fluxos necessários para uma parte do domínio.

**Vantagens:**

- aumenta a coesão de negócio;
- aproxima o código dos processos reais da organização;
- facilita a evolução independente de capacidades;
- reduz o impacto de mudanças localizadas.

**Riscos:**

- algumas capacidades técnicas podem ser implementadas mais de uma vez;
- limites de domínio mal definidos podem gerar sobreposição;
- a comunicação entre componentes exige contratos mais claros;
- o desenho inicial pode demandar mais conhecimento do negócio.

Em muitos sistemas, uma combinação é a melhor escolha: a divisão principal pode ser orientada ao domínio, enquanto cada componente utiliza camadas internas para organizar seus detalhes técnicos.

## Como descobrir os componentes?

A identificação de componentes é um processo iterativo. O objetivo inicial não é obter a divisão perfeita, mas criar uma hipótese que possa ser validada e refinada.

### 1. Identifique as principais capacidades

Comece pelos objetivos do sistema, pelos casos de uso e pelos fluxos de trabalho. Liste as capacidades que o produto precisa oferecer, evitando começar pelas tabelas do banco ou pelas classes já existentes.

Exemplos de capacidades podem incluir cadastro de clientes, processamento de pagamentos, gestão de pedidos, emissão de documentos e notificações.

### 2. Mapeie atores, ações e resultados

Uma técnica derivada da abordagem de atores e ações consiste em perguntar:

- quem interage com o sistema?
- qual ação esse ator executa?
- qual resultado espera obter?
- quais regras precisam ser respeitadas?

As respostas ajudam a agrupar comportamentos que participam do mesmo objetivo. Essa técnica é particularmente útil quando os requisitos estão descritos como histórias de usuário.

### 3. Explore eventos e workflows

O **Event Storming** ajuda a descobrir eventos relevantes do negócio, como `PedidoCriado`, `PagamentoAprovado` ou `EntregaDespachada`. A partir deles, é possível identificar comandos, políticas, agregados e capacidades que participam do fluxo.

Uma abordagem orientada a workflows segue raciocínio semelhante, mas pode ser aplicada de maneira mais ampla, mesmo quando o processo não é descrito formalmente por eventos. O foco está na sequência de atividades, decisões e resultados necessários para concluir um trabalho.

### 4. Aloque requisitos aos componentes

Associe cada requisito, caso de uso ou história de usuário a um componente responsável por implementá-lo. Nesse momento, podem surgir três situações:

- um componente concentra responsabilidades demais e precisa ser dividido;
- dois componentes dependem tanto um do outro que talvez pertençam à mesma fronteira;
- uma funcionalidade atravessa componentes e exige um contrato explícito.

O mapeamento deve ser visível para a equipe. Uma tabela simples de requisitos e componentes já ajuda a revelar lacunas e sobreposições.

### 5. Analise papéis e responsabilidades

Para cada componente, descreva:

- qual problema ele resolve;
- quais regras ele controla;
- quais dados ele é responsável por manter;
- quais contratos oferece;
- de quais outros componentes depende.

Se a descrição de um componente usa muitos “e”, provavelmente há responsabilidades distintas misturadas. Se ninguém consegue explicar sua finalidade em uma frase, sua fronteira pode estar mal definida.

### 6. Considere os requisitos não funcionais

A divisão funcional precisa ser confrontada com as características arquiteturais do sistema. Avalie, por exemplo:

- componentes que exigem escalabilidade diferente;
- dados que precisam de isolamento ou proteção adicional;
- partes que têm requisitos distintos de disponibilidade;
- funcionalidades que serão reutilizadas por múltiplos canais;
- limites de latência e volume de comunicação;
- componentes com ciclos de entrega ou ownership diferentes.

Uma funcionalidade reutilizada por contextos com necessidades incompatíveis pode precisar ser separada. O mesmo vale para um componente que combina uma parte crítica e de alta disponibilidade com outra que pode tolerar indisponibilidade.

### 7. Refine e itere

Depois de implementar ou simular alguns fluxos, reavalie as fronteiras. A arquitetura deve ser ajustada quando surgirem novas informações, e não defendida como se fosse uma decisão definitiva.

O processo é cíclico:

1. propor fronteiras;
2. atribuir responsabilidades;
3. verificar dependências e cenários de mudança;
4. testar a solução no código;
5. ajustar a divisão.

## Granularidade: componentes pequenos ou grandes?

### Fine-grained e coarse-grained

Componentes **fine-grained** são pequenos e especializados. Eles podem facilitar a substituição e o reuso, mas aumentam o número de contratos e de dependências que precisam ser coordenados.

Componentes **coarse-grained** agrupam mais responsabilidades. Eles reduzem a quantidade de comunicação entre unidades, porém podem se tornar difíceis de compreender, testar e modificar.

Os dois extremos são problemáticos:

- componentes pequenos demais geram fragmentação, chamadas em excesso e dificuldade para acompanhar um fluxo;
- componentes grandes demais acumulam responsabilidades, aumentam o acoplamento interno e dificultam mudanças independentes.

### Heurísticas para ajustar a granularidade

Não existe uma fórmula exata, mas algumas perguntas ajudam:

- as partes do componente mudam pelas mesmas razões?
- o componente pode ser explicado como uma única capacidade?
- seus consumidores precisam de todas as funcionalidades que ele oferece?
- a comunicação entre componentes é mais complexa do que o benefício do isolamento?
- o componente possui dados e regras que fazem sentido juntos?
- há requisitos de escala, segurança ou disponibilidade que recomendam separá-lo?

A granularidade adequada é aquela que mantém a coesão alta, torna as dependências explícitas e reduz o custo das mudanças mais prováveis.

## Anti-pattern: a armadilha das entidades

Um erro comum é criar componentes espelhando as entidades do banco de dados: `Cliente`, `Pedido`, `Produto` e `Pagamento`. Embora as entidades sejam importantes, elas não representam necessariamente as fronteiras dos processos do sistema.

Essa abordagem é conhecida como **entity trap** porque organiza a arquitetura em torno da estrutura de dados, ignorando os fluxos de trabalho e as regras que transformam esses dados. Uma única operação de negócio pode precisar atravessar várias entidades, e uma entidade pode participar de processos com regras completamente diferentes.

Em vez de perguntar apenas “quais tabelas existem?”, pergunte:

- quais decisões o sistema precisa tomar?
- quais regras mudam juntas?
- qual componente é responsável por garantir cada invariável?
- quais operações fazem parte do mesmo processo?

As entidades podem continuar existindo dentro dos componentes, mas não devem determinar sozinhas os limites arquiteturais.

## Componente lógico e unidade de implantação

Uma fronteira lógica não precisa ser imediatamente transformada em um processo separado. É possível manter diversos componentes dentro de um único monólito, desde que seus contratos e dependências sejam respeitados.

Essa separação entre arquitetura lógica e implantação é importante porque permite evoluir com segurança. O time pode começar com uma unidade simples de implantação e, posteriormente, extrair um componente quando houver uma necessidade comprovada de:

- escalar uma capacidade de forma independente;
- isolá-la por segurança ou confiabilidade;
- permitir ciclos de entrega diferentes;
- reduzir o impacto de falhas;
- atender a requisitos operacionais específicos.

A extração de um serviço deve ser consequência de uma fronteira bem compreendida, não uma tentativa de resolver desorganização interna por meio de distribuição.

## Monólito ou sistema distribuído?

### Monólito

No monólito, os componentes são executados como uma unidade de implantação, normalmente com um processo principal e, em muitos casos, um banco de dados compartilhado.

**Pontos fortes:**

- implantação e operação mais simples;
- comunicação interna rápida;
- desenvolvimento local mais direto;
- menor custo inicial de infraestrutura.

**Pontos de atenção:**

- componentes podem se acoplar por meio de chamadas diretas ou acesso indevido a dados;
- uma falha ou implantação pode afetar o sistema inteiro;
- escalar uma capacidade específica pode ser mais difícil;
- os limites precisam ser protegidos por disciplina e automação.

### Sistema distribuído

Em um sistema distribuído, diferentes componentes são executados como unidades independentes e se comunicam por APIs, mensageria ou outros protocolos.

**Pontos fortes:**

- implantação, escala e disponibilidade podem ser independentes;
- falhas podem ser isoladas com maior precisão;
- equipes podem possuir ownership e ciclos de entrega distintos.

**Pontos de atenção:**

- a implantação e a observabilidade são mais complexas;
- surgem latência, falhas de rede e problemas de consistência;
- contratos precisam ser versionados e monitorados;
- testes de integração e operação exigem mais investimento.

Componentes bem definidos podem existir em qualquer uma das duas opções. A decisão deve considerar o contexto, a maturidade operacional e os requisitos do sistema, e não seguir uma preferência arquitetural da moda.

## Um roteiro prático de validação

Antes de consolidar uma divisão, avalie cada componente com este checklist:

- sua responsabilidade pode ser descrita de forma clara?
- suas regras e dados têm alta coesão?
- as dependências estão explícitas e são poucas?
- o contrato representa uma necessidade real dos consumidores?
- as mudanças mais prováveis ficarão localizadas?
- os requisitos não funcionais foram considerados?
- a solução evita duplicação sem criar acoplamento excessivo?
- a unidade de implantação escolhida é necessária ou apenas conveniente?

Também é útil registrar as decisões arquiteturais e os motivos que as sustentam. Quando o contexto mudar, a equipe poderá revisar a decisão com base em evidências, em vez de depender de suposições ou de memória institucional.

## Conclusão

Pensar de forma orientada a componentes é estruturar o software em blocos modulares com responsabilidades claras, contratos explícitos e limites capazes de absorver mudanças.

O trabalho começa pela compreensão das capacidades e dos workflows do negócio, passa pela análise de coesão, acoplamento e requisitos não funcionais, e termina em um ciclo contínuo de refinamento. A implantação pode permanecer em um monólito ou evoluir para um sistema distribuído; o mais importante é que a decisão seja sustentada por necessidades reais.

Uma arquitetura de componentes bem definida não elimina a complexidade. Ela a organiza em fronteiras compreensíveis, permitindo que o sistema evolua sem transformar cada mudança em uma alteração global.
