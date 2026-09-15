# Criptografia e proteção de dados

A criptografia é um dos mecanismos mais importantes para proteger informações
contra leitura, alteração e falsificação. No entanto, escolher um algoritmo
forte não é suficiente. A proteção depende também de onde a criptografia é
aplicada, de como as chaves são geradas e armazenadas e de quem pode utilizá-las.

Uma estratégia de segurança madura combina criptografia simétrica e assimétrica
em diferentes camadas do ambiente. A criptografia simétrica é eficiente para
proteger grandes volumes de dados. A criptografia assimétrica resolve problemas
de distribuição de chaves, autenticação e assinatura digital. Na prática, os
dois paradigmas trabalham juntos.

Este artigo apresenta como essa combinação pode ser aplicada às camadas física,
lógica, de aplicação e de negócio, considerando dados em repouso, em trânsito e
em uso.

## O papel das chaves simétricas e assimétricas

Na criptografia simétrica, a mesma chave — ou um segredo compartilhado
equivalente — é usada para cifrar e decifrar os dados. Algoritmos como o
Advanced Encryption Standard (AES) são rápidos, amplamente estudados e
adequados para discos, bancos de dados, arquivos, backups e mensagens. O
principal desafio é distribuir e proteger o segredo: qualquer pessoa que tenha
a chave pode, em princípio, decifrar o conteúdo.

Na criptografia assimétrica, cada identidade possui um par de chaves
matematicamente relacionado: uma chave privada, que deve permanecer secreta, e
uma chave pública, que pode ser distribuída. RSA e criptografia de curvas
elípticas (ECC) são exemplos conhecidos. A chave pública pode ser usada para
proteger um segredo destinado ao proprietário da chave privada, enquanto a
chave privada pode produzir assinaturas digitais verificáveis com a chave
pública.

A criptografia assimétrica exige mais processamento e não costuma ser a melhor
opção para cifrar arquivos grandes. Por isso, protocolos modernos utilizam um
modelo híbrido:

1. uma chave simétrica temporária, chamada chave de sessão ou de dados, cifra o
   conteúdo;
2. a chave pública do destinatário protege essa chave simétrica;
3. o destinatário usa sua chave privada para recuperar a chave de sessão;
4. o conteúdo é decifrado com o algoritmo simétrico.

Esse padrão, conhecido como *envelope encryption*, combina desempenho com uma
forma segura de estabelecer confiança entre as partes.

## Uma arquitetura de proteção em camadas

Criptografar apenas o perímetro da rede ou apenas o disco não protege todos os
cenários. Um atacante que obtenha acesso ao sistema operacional, a uma conta de
aplicação ou a um backup pode encontrar os dados em outra camada. A defesa deve
ser distribuída de acordo com o risco e com o ciclo de vida da informação.

### Camada física

A camada física inclui servidores, dispositivos de armazenamento, notebooks,
mídias removíveis e ambientes de datacenter. O objetivo principal é reduzir o
impacto de perda, furto ou descarte incorreto de equipamentos.

A Full Disk Encryption (FDE) cifra o volume inteiro e é especialmente útil para
estações de trabalho e notebooks. Ela protege os dados quando o equipamento
está desligado, mas não substitui controles de autenticação: depois que o
sistema é inicializado e o usuário está autenticado, os arquivos podem ficar
disponíveis para os processos autorizados.

Em servidores, a FDE deve ser combinada com controle de acesso ao datacenter,
inventário de ativos, proteção contra inicialização não autorizada e descarte
seguro de mídias. A chave de desbloqueio não deve ser armazenada em texto puro
junto ao equipamento. Quando possível, deve ser protegida por um módulo
criptográfico, por uma plataforma de gerenciamento de chaves ou por uma
infraestrutura de inicialização confiável.

Nesse nível, a criptografia simétrica é a escolha natural pelo volume de dados.
A criptografia assimétrica aparece no gerenciamento da chave de volume, na
autenticação de componentes e na assinatura de firmware e atualizações.

### Camada lógica e de infraestrutura

A camada lógica compreende sistemas operacionais, máquinas virtuais,
contêineres, bancos de dados, serviços de armazenamento e redes internas. O
controle não deve depender de uma única barreira: uma conta comprometida não
deveria permitir acesso irrestrito a todas as informações.

Para dados em repouso, a Transparent Data Encryption (TDE) pode cifrar arquivos
de banco de dados e seus registros sem exigir mudanças imediatas em todas as
aplicações. Ela é útil contra o acesso direto aos arquivos ou aos discos, mas
não impede que uma consulta autorizada retorne dados em claro. Por isso, deve
ser acompanhada por controle de privilégios, auditoria e, quando necessário,
criptografia no nível da aplicação.

A rede interna também precisa ser tratada como um ambiente potencialmente
hostil. TLS protege sessões entre clientes e serviços, enquanto IPsec pode
proteger comunicações entre redes ou hosts. Esses protocolos usam criptografia
assimétrica para autenticação e negociação inicial e criptografia simétrica para
transportar o fluxo de dados com eficiência.

O recurso *break and inspect* de TLS pode ser necessário em ambientes
corporativos para inspeção autorizada de tráfego, mas introduz riscos
consideráveis. Os certificados e as chaves da autoridade de inspeção passam a
ser extremamente sensíveis, e sistemas que transportam informações pessoais,
financeiras ou de saúde podem exigir exceções e controles específicos. A
inspeção deve ser documentada, limitada ao objetivo legítimo e protegida contra
uso indevido.

### Camada de aplicação e dados

A aplicação conhece o significado dos dados e, portanto, pode aplicar proteção
mais precisa. A Application-Level Encryption (ALE) é indicada para informações
que exigem isolamento mesmo quando o banco de dados, o backup ou a conta de
infraestrutura são comprometidos.

Exemplos incluem documentos, tokens, dados pessoais, chaves de API e números de
identificação. A aplicação pode cifrar esses campos antes de persistir o valor,
usando uma chave simétrica de dados. A chave, por sua vez, é protegida por uma
chave assimétrica ou por uma chave mestra mantida em um KMS ou HSM.

É importante definir se o sistema precisará pesquisar, ordenar ou indexar o
conteúdo cifrado. A cifra autenticada, como AES-GCM, protege confidencialidade e
integridade, mas produz valores que não podem ser comparados diretamente com o
texto original. Quando a pesquisa for indispensável, deve-se avaliar um índice
separado baseado em um hash com chave, com cuidado para não revelar padrões ou
permitir enumeração de valores. Cifrar uma coluna não autoriza a aplicação a
manter a chave em seu código-fonte ou em um arquivo de configuração público.

Além da confidencialidade, a aplicação deve verificar integridade e origem. Uma
mensagem cifrada precisa ser autenticada para que alterações sejam detectadas.
Assinaturas digitais são apropriadas quando é necessário provar que um
documento, pacote ou transação foi produzido por uma determinada chave privada.

### Camada de negócio

Na camada de negócio, a pergunta deixa de ser apenas “como cifrar?” e passa a
ser “quem pode usar esta informação, para qual finalidade e por quanto tempo?”.
Classificação de dados, segregação de funções, retenção e rastreabilidade devem
orientar a arquitetura criptográfica.

Uma transação financeira, por exemplo, pode usar TLS para proteger o transporte,
ALE para proteger campos sensíveis no armazenamento e uma assinatura digital
para garantir autoria e não repúdio de um documento. O acesso à chave pode
depender do papel do usuário, do serviço que está fazendo a chamada, da
localização, do estado do dispositivo e da aprovação de uma segunda pessoa.

O PCI-DSS, entre outros referenciais, reforça a necessidade de proteger dados
de pagamento, restringir o acesso às chaves e manter controles de auditoria.
Criptografia não deve ser usada para mascarar uma política de acesso deficiente:
se todos os serviços compartilham a mesma chave, o isolamento entre áreas deixa
de existir.

## Gestão do ciclo de vida das chaves

Uma chave forte armazenada de forma inadequada transforma uma boa escolha
criptográfica em uma falsa sensação de segurança. O ciclo de vida deve incluir:

- geração com fonte de aleatoriedade adequada;
- identificação, classificação e associação a um proprietário;
- armazenamento protegido e controle de acesso;
- distribuição segura, sem exposição em código, logs ou mensagens;
- rotação periódica e rotação imediata em caso de suspeita;
- revogação e substituição de chaves comprometidas;
- recuperação de chaves necessárias para continuidade do negócio;
- destruição verificável quando a retenção deixar de ser necessária.

O KMS centraliza políticas, permissões, rotação e auditoria. Um Hardware
Security Module (HSM) oferece uma fronteira de proteção baseada em hardware e
pode impedir que a chave privada seja exportada. Esses recursos não eliminam a
necessidade de governança: permissões excessivas, falta de segregação e
credenciais de serviço expostas continuam sendo problemas.

Uma hierarquia comum separa a chave mestra, as chaves de criptografia de dados e
as chaves de sessão. A chave mestra protege apenas outras chaves; as chaves de
dados protegem os conteúdos; as chaves de sessão têm vida curta e reduzem o
impacto de uma exposição. Esse desenho facilita a rotação sem reprocessar
todos os dados imediatamente.

## Ameaças e decisões atuais

O ataque *harvest now, decrypt later* consiste em capturar dados cifrados hoje
para tentar decifrá-los no futuro, quando houver avanços computacionais ou
quebra de chaves. Informações que precisam permanecer confidenciais por muitos
anos devem considerar a vida útil do dado, a validade dos algoritmos e a
possibilidade de migração para criptografia pós-quântica, incluindo famílias
baseadas em reticulados (*lattices*).

Também é necessário evitar algoritmos proprietários ou parâmetros
desnecessariamente obscuros. Algoritmos públicos, revisados e recomendados por
referenciais como o NIST facilitam auditoria, interoperabilidade e substituição
planejada. A segurança deve depender da chave e da implementação correta, não
do segredo do algoritmo.

## Boas práticas para uma proteção consistente

Uma implementação equilibrada pode seguir estas orientações:

1. classificar os dados e definir o tempo de confidencialidade exigido;
2. proteger dados em trânsito com TLS configurado de forma atualizada e
   autenticação adequada;
3. usar criptografia simétrica autenticada para o conteúdo e assimétrica para
   troca de chaves, autenticação e assinaturas;
4. aplicar FDE, TDE e ALE de acordo com o risco, sem tratá-las como alternativas
   excludentes;
5. manter as chaves fora do código e dos bancos de dados protegidos por elas;
6. centralizar operações sensíveis em KMS ou HSM, com menor privilégio e
   auditoria;
7. testar restauração de backups e recuperação de chaves antes de uma crise;
8. planejar rotação, revogação e migração criptográfica desde o início;
9. monitorar uso anômalo de chaves e registrar operações sem expor segredos;
10. revisar periodicamente protocolos, bibliotecas, certificados e dependências.

## Conclusão

A criptografia eficaz é uma arquitetura, não uma função isolada. A proteção
física reduz o risco de perda de dispositivos; a camada lógica limita o acesso
à infraestrutura; a aplicação preserva campos sensíveis; e a camada de negócio
define finalidade, responsabilidade e rastreabilidade.

Chaves simétricas devem proteger dados com eficiência, enquanto chaves
assimétricas devem estabelecer confiança, proteger chaves simétricas e produzir
assinaturas verificáveis. Quando esse uso é combinado com gestão rigorosa do
ciclo de vida, controles de acesso e protocolos bem configurados, a
organização deixa de apenas “cifrar dados” e passa a construir uma proteção
coerente para toda a informação.

## Referências

- National Institute of Standards and Technology (NIST). *Cryptographic
  Standards and Guidelines*.
- NIST. *Recommendation for Key Management: Part 1 — General*.
- Payment Card Industry Security Standards Council. *PCI DSS*.
- IETF. *Transport Layer Security (TLS)*.
- IETF. *Internet Protocol Security (IPsec)*.
