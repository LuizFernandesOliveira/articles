# Integridade e autenticação digital

Integridade e autenticação são propriedades fundamentais da segurança da
informação. A integridade permite detectar alterações indevidas em dados,
mensagens, arquivos e transações. A autenticação permite verificar a identidade
de uma pessoa, sistema ou serviço antes de conceder acesso ou aceitar uma
operação.

Essas propriedades são relacionadas, mas não são equivalentes. Um hash pode
indicar que um arquivo foi alterado, mas não prova quem o produziu. Uma
assinatura digital pode associar uma mensagem a uma chave privada, mas só é
confiável quando a chave pública está vinculada à identidade correta. Uma
arquitetura segura combina mecanismos criptográficos, gestão de identidades,
controle de acesso e processos de validação.

## O que é integridade

Integridade é a garantia de que uma informação não foi modificada de forma
indevida, acidental ou maliciosa. A alteração pode acontecer durante o
transporte, no armazenamento, no processamento ou durante uma atualização de
software.

O objetivo não é impedir toda alteração. Sistemas legítimos precisam editar
documentos, atualizar registros e substituir versões. O objetivo é permitir
que alterações autorizadas sejam identificadas e que alterações não autorizadas
sejam rejeitadas ou investigadas.

Alguns exemplos de perda de integridade são:

- um arquivo baixado que foi substituído por uma versão maliciosa;
- uma mensagem de pagamento cujo valor foi alterado no caminho;
- um registro de auditoria modificado para esconder uma atividade;
- um pacote de software ou firmware adulterado antes da instalação;
- uma credencial armazenada com valor diferente daquele originalmente criado.

## Funções hash e verificação de integridade

Uma função hash criptográfica transforma uma entrada de tamanho arbitrário em
uma saída de tamanho fixo, chamada resumo ou *digest*. Para a mesma entrada, o
resultado é determinístico. Uma pequena alteração no conteúdo deve produzir um
resumo completamente diferente.

Uma função hash adequada deve oferecer, entre outras, estas propriedades:

- **resistência à pré-imagem:** dado um resumo, deve ser inviável encontrar uma
  entrada que o produza;
- **resistência à segunda pré-imagem:** dada uma entrada, deve ser inviável
  encontrar outra entrada com o mesmo resumo;
- **resistência à colisão:** deve ser inviável encontrar duas entradas
  diferentes com o mesmo resumo.

Para verificar um arquivo, o remetente ou fornecedor publica o hash esperado.
O destinatário calcula o hash do arquivo recebido e compara os valores. Se os
resumos forem diferentes, o conteúdo não é igual ao conteúdo de referência.
Essa comparação, sozinha, não garante a autenticidade do arquivo: um atacante
que consiga substituir o arquivo também pode tentar substituir o hash publicado.

MD5 não deve ser usado para segurança, pois colisões práticas já foram
demonstradas. SHA-1 também foi descontinuado para usos que dependem de
resistência a colisões, especialmente após o ataque SHAttered, divulgado em
2017. Famílias atuais, como SHA-256 e SHA-3, devem ser escolhidas conforme os
requisitos do protocolo e as recomendações vigentes.

### Aplicações práticas

Hashes podem ser utilizados para:

- verificar a integridade de imagens forenses, trabalhando sobre uma cópia sem
  alterar a evidência original;
- conferir arquivos de atualização e instaladores;
- identificar versões de artefatos em pipelines de desenvolvimento;
- detectar mudanças em configurações ou arquivos críticos;
- construir identificadores de conteúdo e registros de auditoria.

O hash deve ser obtido de uma fonte confiável. Consultar o valor esperado no
mesmo servidor ou canal usado para baixar o arquivo reduz significativamente o
benefício da verificação.

## HMAC e autenticação de mensagens

Um hash público não prova a origem do conteúdo. Para autenticar uma mensagem
entre partes que compartilham um segredo, utiliza-se um Hash-based Message
Authentication Code (HMAC). O HMAC combina uma função hash com uma chave secreta
e produz um código que depende tanto da mensagem quanto do segredo.

O destinatário recalcula o HMAC com a mesma chave e compara o resultado usando
uma comparação em tempo constante, quando aplicável. Se o valor coincidir, há
evidência de que a mensagem não foi alterada e de que foi produzida por alguém
que conhece a chave compartilhada.

O HMAC não oferece não repúdio, porque as duas partes conhecem o mesmo segredo.
Qualquer uma delas poderia ter produzido o código. Também é necessário proteger
a chave contra exposição e definir sua rotação, revogação e distribuição.

Em protocolos novos, deve-se preferir uma construção padronizada e evitar
combinações improvisadas, como concatenar uma senha diretamente ao conteúdo
antes de calcular um hash. Quando confidencialidade e integridade forem
necessárias, cifras autenticadas, como AES-GCM ou ChaCha20-Poly1305, normalmente
oferecem uma solução mais apropriada.

## Autenticação digital

Autenticação digital é o processo de verificar se uma entidade é realmente quem
afirma ser. A entidade pode ser uma pessoa, uma aplicação, um dispositivo, um
servidor ou outro serviço.

Os fatores de autenticação costumam ser classificados em:

- **algo que a pessoa sabe:** senha, PIN ou segredo;
- **algo que a pessoa possui:** chave de segurança, certificado ou dispositivo;
- **algo que a pessoa é:** característica biométrica;
- **contexto adicional:** localização, estado do dispositivo ou comportamento.

A autenticação multifator combina fatores independentes. O uso de uma senha e
de um segundo código enviado pelo mesmo canal pode melhorar a segurança, mas
não tem a mesma resistência de uma chave física ou de uma credencial
criptográfica contra phishing e interceptação.

Autenticação não é autorização. A primeira verifica a identidade; a segunda
define quais recursos essa identidade pode acessar e quais operações pode
executar. Confundir esses conceitos pode levar um sistema a autenticar
corretamente um usuário e, ainda assim, permitir privilégios excessivos.

## Assinaturas digitais

Uma assinatura digital é criada com a chave privada do signatário e verificada
com a chave pública correspondente. Em geral, o sistema calcula o resumo da
mensagem e assina esse resumo, em vez de assinar diretamente todo o conteúdo.

Uma verificação bem-sucedida oferece três garantias principais:

1. **integridade:** o conteúdo verificado corresponde ao conteúdo assinado;
2. **autenticidade criptográfica:** a assinatura foi produzida pela chave
   privada correspondente à chave pública usada na verificação;
3. **evidência de autoria:** existe uma associação técnica entre a operação e o
   titular da chave, desde que a chave tenha sido corretamente vinculada,
   protegida e utilizada sob controles adequados.

A assinatura não cifra o conteúdo. Qualquer pessoa que tenha acesso à mensagem
e à chave pública pode, em princípio, ler o conteúdo. Quando a informação
também precisa permanecer confidencial, a assinatura deve ser combinada com
criptografia.

Assinaturas digitais são usadas em documentos, pacotes de software,
certificados, atualizações de firmware, transações e registros de sistemas.
Algoritmos, tamanhos de chave e formatos devem seguir padrões atuais, pois a
segurança depende também da implementação, do modo de operação e da validação
dos parâmetros.

## Infraestrutura de chaves públicas

A Public Key Infrastructure (PKI), ou Infraestrutura de Chaves Públicas (ICP),
é o conjunto de políticas, processos, pessoas e componentes técnicos usados
para criar, distribuir, validar, renovar e revogar certificados digitais.

Um certificado digital associa uma identidade a uma chave pública. Essa
associação é assinada por uma Autoridade Certificadora (AC), que atua dentro de
uma hierarquia de confiança. O cliente valida a cadeia até uma autoridade raiz
confiável e verifica se o certificado é válido para o uso pretendido, para o
nome correto e para o período correspondente.

A confiança em uma PKI depende de mais do que a assinatura da AC. É necessário
verificar:

- a cadeia de certificação;
- o período de validade;
- o uso previsto da chave e do certificado;
- o nome ou identidade associada;
- a revogação;
- a proteção da chave privada;
- os algoritmos e parâmetros permitidos.

Um certificado válido não transforma automaticamente qualquer conteúdo em
confiável. Ele confirma uma associação entre uma identidade e uma chave dentro
de um contexto e de uma política específicos.

### Revogação e ciclo de vida dos certificados

Um certificado pode precisar ser revogado antes do vencimento quando a chave
privada é exposta, a identidade deixa de ser válida ou o certificado foi
emitido incorretamente. Os mecanismos mais comuns são:

- **Certificate Revocation Lists (CRLs):** listas publicadas periodicamente
  pelas autoridades certificadoras;
- **Online Certificate Status Protocol (OCSP):** consultas sobre o status de um
  certificado;
- **OCSP stapling:** o servidor entrega uma resposta OCSP recente junto com a
  conexão, reduzindo consultas diretas do cliente à AC;
- **Automated Certificate Management Environment (ACME):** automação da
  emissão e renovação de certificados, especialmente em serviços web.

A validade também deve considerar o instante da assinatura. Em documentos de
longa duração, carimbos do tempo, políticas de preservação e evidências da
cadeia podem ser necessários para demonstrar que a assinatura era válida
quando foi produzida.

## Armazenamento seguro de credenciais

Senhas não devem ser armazenadas em texto puro nem protegidas com um hash rápido
de uso geral. Se o banco de dados for exposto, um atacante deve encontrar
resistência suficiente para não testar grandes quantidades de candidatos em
curto tempo.

Para senhas, deve-se usar uma função de derivação lenta e adaptativa, como
Argon2id, scrypt, bcrypt ou PBKDF2, com parâmetros atuais e um salt aleatório
exclusivo para cada senha. O salt não precisa ser secreto; ele impede que a
mesma senha produza sempre o mesmo valor e dificulta o uso de tabelas
pré-computadas.

Um sistema de armazenamento de credenciais deve:

- limitar tentativas de autenticação e detectar comportamento anômalo;
- evitar registrar senhas, tokens e chaves em logs;
- proteger tokens de sessão e permitir sua revogação;
- usar armazenamento seguro do sistema operacional ou hardware quando
  disponível;
- separar credenciais de produção, teste e desenvolvimento;
- aplicar menor privilégio aos serviços que consultam credenciais;
- oferecer recuperação de conta sem criar um caminho mais fraco que o login.

Criptografar um banco de dados não substitui o hash de senhas. A criptografia
protege o armazenamento quando a chave permanece inacessível, enquanto o hash
adaptativo reduz o impacto de uma exposição do banco e não permite recuperar a
senha original diretamente.

## Integridade em sistemas e transações

Integridade deve ser tratada ao longo de todo o ciclo de vida da informação.
Uma mensagem pode estar protegida durante o transporte e ser alterada depois
que chega ao servidor. Da mesma forma, um arquivo pode ter assinatura válida,
mas ser instalado por um processo que não verifica a cadeia de confiança.

Controles importantes incluem:

1. proteger canais de comunicação com TLS e validar corretamente os certificados;
2. autenticar mensagens com HMAC ou uma cifra autenticada quando houver segredo
   compartilhado;
3. verificar assinaturas e hashes antes de instalar, importar ou executar
   conteúdos;
4. aplicar controle de acesso e segregação de funções às alterações;
5. registrar quem alterou o dado, quando, qual versão foi criada e qual
   aprovação foi utilizada;
6. proteger logs contra alteração e encaminhá-los para armazenamento com acesso
   restrito;
7. testar restauração, rotação de chaves e revogação em condições controladas.

Em transações, também é importante evitar repetição de mensagens válidas. Nonces,
contadores, identificadores únicos e janelas de validade ajudam a impedir que
um atacante capture uma requisição autenticada e a reproduza posteriormente.

## Limitações e decisões de projeto

Nenhum mecanismo isolado resolve todos os riscos. Um hash não autentica a
origem; um HMAC exige um segredo compartilhado; uma assinatura depende da
proteção da chave privada e da confiança no certificado; uma senha forte ainda
pode ser entregue voluntariamente a uma página de phishing.

A escolha deve considerar:

- quem precisa verificar a informação;
- se as partes compartilham um segredo;
- se é necessário provar autoria perante terceiros;
- quanto tempo a evidência precisará ser validada;
- qual é o impacto de uma chave comprometida;
- como ocorrerão rotação, revogação e recuperação;
- quais padrões e requisitos regulatórios se aplicam.

Também é necessário planejar a migração criptográfica. Algoritmos obsoletos,
chaves expostas e certificados expirados devem ser substituídos sem interromper
serviços críticos. Informações que precisam permanecer válidas por muitos anos
podem exigir formatos com suporte a múltiplos algoritmos e evidências de
preservação.

## Boas práticas

Uma implementação consistente pode seguir estas orientações:

1. separar claramente integridade, autenticidade, confidencialidade e
   autorização;
2. utilizar SHA-256, SHA-3 ou outra função aprovada para resumos, evitando MD5 e
   SHA-1 em novos projetos;
3. preferir HMAC ou cifras autenticadas a construções criptográficas próprias;
4. proteger chaves privadas em KMS, HSM ou armazenamento seguro equivalente;
5. validar toda a cadeia de certificados e o uso pretendido da chave;
6. automatizar emissão, renovação e revogação sem ignorar auditoria;
7. armazenar senhas com Argon2id, scrypt, bcrypt ou PBKDF2, sempre com salt
   exclusivo e parâmetros revisados;
8. aplicar autenticação multifator resistente a phishing para acessos sensíveis;
9. proteger contra replay com nonces, expiração e identificadores únicos;
10. testar cenários de comprometimento, recuperação, rotação e substituição de
    algoritmos.

## Conclusão

Integridade e autenticação digital formam a base para confiar em informações e
operações realizadas por sistemas conectados. Funções hash ajudam a detectar
alterações, HMACs autenticam mensagens entre partes que compartilham segredos,
assinaturas digitais associam conteúdo a chaves privadas e uma PKI organiza a
confiança entre identidades e chaves públicas.

Esses mecanismos só produzem segurança real quando são acompanhados por
armazenamento adequado de credenciais, controle de acesso, validação de
certificados, proteção de chaves e processos de revogação. A confiança deve
ser verificável, limitada ao contexto necessário e mantida durante todo o
período em que a informação precisar ser utilizada.

## Referências

- National Institute of Standards and Technology (NIST). *Secure Hash Standard
  (SHS)*.
- NIST. *Digital Identity Guidelines*.
- NIST. *Recommendation for Key Management*.
- IETF. *Internet X.509 Public Key Infrastructure Certificate and CRL Profile*.
- IETF. *HMAC: Keyed-Hashing for Message Authentication*.
- OWASP. *Password Storage Cheat Sheet*.
