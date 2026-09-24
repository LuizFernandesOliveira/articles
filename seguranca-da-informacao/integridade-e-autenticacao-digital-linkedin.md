# Integridade e autenticação digital: a base da confiança no mundo conectado

Em segurança da informação, proteger um dado não significa apenas impedir que
terceiros o leiam. Também precisamos garantir que ele não foi alterado e que a
entidade que o produziu é realmente quem afirma ser.

É nesse contexto que entram a **integridade** e a **autenticação digital**.

A integridade permite detectar alterações indevidas em arquivos, mensagens,
transações e registros. Funções hash, como SHA-256 e SHA-3, geram uma
“impressão digital” do conteúdo. Se o arquivo mudar, o hash também muda.

Mas um hash, sozinho, não comprova a origem do arquivo. Para isso, podemos usar:

- **HMAC**, quando as partes compartilham um segredo;
- **assinaturas digitais**, quando é necessário vincular o conteúdo a uma
  chave privada;
- **certificados digitais e PKI**, para associar uma chave pública a uma
  identidade confiável.

A autenticação digital verifica se uma pessoa, sistema ou serviço é quem afirma
ser. Senhas continuam presentes, mas devem ser combinadas com outros fatores,
como aplicativos autenticadores, chaves de segurança ou biometria. Para
armazenar senhas, o correto é utilizar funções adaptativas como Argon2id,
scrypt, bcrypt ou PBKDF2 — nunca texto puro, MD5 ou SHA-1.

Também é importante lembrar que:

- autenticação não é autorização;
- assinatura digital não substitui criptografia;
- certificado válido não significa que toda operação é segura;
- chaves privadas precisam de proteção, rotação e revogação;
- mensagens autenticadas devem possuir mecanismos contra replay.

Segurança digital não depende de um único algoritmo. Ela resulta da combinação
entre criptografia, gestão de identidades, controle de acesso, auditoria e
processos bem definidos.

No fim, a pergunta essencial é: **podemos confiar que essa informação está
íntegra e veio realmente de quem deveria enviá-la?**

#SegurancaDaInformacao #Ciberseguranca #Criptografia #AutenticacaoDigital
#IntegridadeDeDados #TransformacaoDigital
