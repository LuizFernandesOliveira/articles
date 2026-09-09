# Fundamentos de estilos arquiteturais

## Introdução

Um estilo arquitetural é uma forma recorrente de organizar os componentes de um
sistema, suas responsabilidades e as relações entre eles. Ele funciona como um
conjunto de restrições e decisões que orienta a construção da solução, mas não é
um código pronto para ser copiado.

Estilos arquiteturais estão relacionados aos *design patterns*, porém atuam em
níveis diferentes. Um padrão de projeto resolve um problema mais localizado,
como a criação de objetos ou a comunicação entre classes. Já um estilo
arquitetural define a estrutura geral da aplicação: onde as responsabilidades
ficam, como os módulos se comunicam e como o sistema evolui e é operado.

A escolha de um estilo deve considerar os requisitos do negócio, o volume de
dados, a necessidade de escala, a disponibilidade esperada e a capacidade
operacional da equipe. Não existe uma arquitetura universalmente melhor; existe
uma arquitetura mais adequada para um determinado contexto.

## Arquitetura cliente-servidor (duas camadas)

Na arquitetura cliente-servidor, o cliente solicita recursos ou operações e o
servidor processa essas solicitações e devolve uma resposta. O cliente costuma
concentrar a interface e parte da interação com o usuário, enquanto o servidor
centraliza o processamento e o acesso aos dados.

Uma API HTTP ou REST é um exemplo comum dessa organização. Um navegador ou
aplicativo móvel atua como cliente e envia uma requisição para um servidor, que
valida os dados, executa a operação e retorna uma resposta, geralmente em JSON.

### Vantagens

- Modelo simples de entender e implementar.
- Regras e dados podem ser centralizados no servidor.
- Clientes diferentes podem consumir a mesma API.

### Cuidados

- O servidor pode se tornar um ponto único de falha ou de saturação.
- A comunicação depende da rede e pode sofrer com latência e indisponibilidade.
- Um cliente muito acoplado ao formato da API pode dificultar sua evolução.

## Arquitetura em três camadas (*three-tier*)

A arquitetura em três camadas separa o sistema em responsabilidades bem
definidas:

1. **Camada de apresentação:** recebe as interações do usuário, apresenta
   informações e traduz as entradas para o formato esperado pela aplicação.
2. **Camada de lógica de negócio:** implementa regras, validações, casos de uso
   e decisões do domínio. É a camada que define o que o sistema pode fazer.
3. **Camada de dados:** persiste e consulta informações, encapsulando o acesso
   ao banco de dados ou a outros mecanismos de armazenamento.

Essa separação reduz o acoplamento e permite substituir uma camada com menor
impacto nas demais. Por exemplo, a mesma lógica de negócio pode ser exposta
por uma API REST, uma interface web ou um processo em lote.

É importante não confundir separação lógica com separação física. As três
camadas podem executar no mesmo processo ou em servidores diferentes. Separá-las
fisicamente aumenta as possibilidades de escala e isolamento, mas também
introduz custos de rede, observabilidade e operação.

## Arquitetura monolítica e distribuída

### Monolítica

Em uma aplicação monolítica, os principais componentes são empacotados e
implantados como uma única unidade. Isso não significa necessariamente que o
código seja desorganizado: um monólito pode ter módulos bem definidos e
fronteiras internas claras.

O monólito costuma ser uma boa opção para começar um produto, pois simplifica
o desenvolvimento, os testes, a implantação e a depuração. Com o crescimento,
porém, uma mudança pequena pode exigir a implantação de toda a aplicação, e
partes com necessidades diferentes de escala podem ficar presas ao mesmo ciclo
de entrega.

### Distribuída

Em uma arquitetura distribuída, os componentes executam em processos ou
máquinas diferentes e se comunicam por uma rede. Serviços independentes,
filas, bancos de dados separados e sistemas de processamento assíncrono são
exemplos dessa abordagem.

Essa arquitetura permite escalar componentes de forma independente e isolar
falhas, mas adiciona complexidade. É necessário lidar com latência, falhas
parciais, descoberta de serviços, versionamento de contratos, segurança e
observabilidade. Distribuir um sistema não resolve automaticamente problemas
de desempenho ou organização; a distribuição deve atender a uma necessidade
real.

## As oito falácias da computação distribuída

As falácias são suposições incorretas que frequentemente aparecem quando um
sistema é projetado como se a rede fosse uma chamada local. Elas ajudam a
identificar riscos antes que se transformem em falhas de produção:

1. **A rede é confiável.** Pacotes podem ser perdidos, duplicados ou entregues
   fora de ordem. Operações importantes precisam de confirmação, repetição
   segura e tratamento de erros.
2. **A latência é zero.** Toda comunicação remota leva tempo. Chamadas em
   excesso podem tornar uma operação lenta mesmo quando cada serviço responde
   rapidamente.
3. **A largura de banda é infinita.** Dados grandes consomem capacidade e
   podem causar congestionamento. Payloads devem ser dimensionados e, quando
   apropriado, paginados ou comprimidos.
4. **A rede é segura.** O tráfego deve ser protegido e cada serviço deve
   autenticar e autorizar as solicitações. A rede interna também precisa ser
   tratada como um ambiente potencialmente hostil.
5. **A topologia não muda.** Serviços, endereços, rotas e réplicas podem mudar
   por causa de escalabilidade, falhas ou manutenção. A aplicação não deve
   depender de endereços fixos.
6. **Existe um único administrador.** Um sistema distribuído normalmente
   atravessa equipes, provedores, regiões e organizações com políticas
   diferentes.
7. **O custo de transporte é zero.** Comunicação remota consome rede, CPU,
   armazenamento e tempo de operação. O custo financeiro e operacional deve
   fazer parte da decisão arquitetural.
8. **A rede é homogênea.** Diferentes protocolos, versões, provedores e
   ambientes podem coexistir. Contratos explícitos e compatibilidade são
   necessários para reduzir esse acoplamento.

## Logging e observabilidade distribuída

Em um sistema distribuído, uma requisição pode atravessar vários serviços.
Registrar apenas uma mensagem local torna difícil reconstruir o caminho da
operação e descobrir onde ocorreu uma falha.

Uma estratégia de logging distribuído deve:

- centralizar ou agregar logs em uma plataforma pesquisável;
- usar níveis de log adequados, evitando excesso de ruído;
- incluir um identificador de correlação ou de trace para acompanhar uma
  requisição entre serviços;
- registrar contexto útil, como serviço, ambiente, operação, resultado e
  duração;
- redigir ou remover dados sensíveis antes do armazenamento.

Logs são apenas uma parte da observabilidade. Métricas mostram tendências e
indicadores como taxa de erro e latência, enquanto traces mostram a sequência de
chamadas de uma operação distribuída. Em conjunto, esses sinais reduzem o
tempo necessário para detectar, diagnosticar e corrigir incidentes.

## Transações distribuídas

Uma transação local pode contar com as garantias de atomicidade, consistência,
isolamento e durabilidade oferecidas por um único banco de dados. Quando uma
operação altera dados em vários serviços ou bancos, manter essas garantias se
torna mais difícil: uma etapa pode ser concluída enquanto outra falha, gerando
uma falha parcial.

Antes de distribuir uma transação, é importante avaliar se os dados podem
pertencer ao mesmo serviço ou se a operação pode ser modelada de forma
assíncrona. Quando a distribuição é necessária, algumas alternativas são:

- **Outbox transacional:** grava a alteração de negócio e o evento a ser
  publicado na mesma transação local; um processo posterior entrega o evento.
- **Saga:** divide a operação em transações locais e define ações de
  compensação para desfazer efeitos já aplicados.
- **Idempotência e retentativas:** permitem repetir mensagens ou operações sem
  gerar efeitos duplicados.
- **Consistência eventual:** aceita que diferentes partes do sistema possam
  ficar temporariamente divergentes, desde que converjam para um estado válido.

O modelo BASE — *Basically Available, Soft state, Eventually consistent* —
resume uma abordagem comum em sistemas distribuídos: priorizar disponibilidade,
aceitar que o estado possa mudar enquanto é processado e permitir que a
consistência seja alcançada ao longo do tempo. Essa escolha deve ser explícita,
pois nem todo domínio aceita consistência eventual; operações financeiras,
estoque e autorização, por exemplo, podem exigir garantias mais fortes.