# Tecnologia de proteção e segurança aplicada

## Criptografia e proteção de dados

### Fundamentos
Payment Card Industry Data Security Standard (PCI-DSS)
Deve-se adotar algoritmos públicos
Paradigmas de criptografia - simétrica e assimétrica.

### Criptografia moderna e gerenciamento de chaves
National Institute of Standards and Technology (NIST)
Simétrica - Advanced Encryption Standard (AES) - padrao global incontestável
Assimétrica - Rivest-Shamir-Adleman (RSA) e Eliptic Curve Cryptography (ECC)
Harvest now, decrypt later (tipo de atack) 
Pós Quantica - Lattices
ciclo de vida das chaves tbm pode ser um problema
Hardware Security Modules (HSM) ou Key Management Service (KMS)

### Aplicação da criptografia na proteção de dados em respouso e em trânsito

#### Estratégias de proteção de dados em repouso
Full Disk Encryption (FDE)
Transparent Data Encryption (TDE)
Application-Level Encryption (ALE)

#### Garantias de confidencialidade para dados em trânsito
Sniffing and Man-in-the-Middle (MitM)
Transport Layer Security (TLS)
Internet Protocol Security (IPsec)
TLS Break and Inspect


## Integridade e autenticação digital

### Funções hash e mecanismos de verificação de integridade
atua como um algoritmo deterministico
Hash-based Message Authentication Code (HMAC)
pre-image resistence, second pre-image resistence and collision resistence
MD5 - quebrado em 2000
SHA-1 quebrado em 2017 (SHAttered)
SHA-3 and sponge construction

#### Aplicacoes praticas
- forense digital - gerar hash SHA-256 da imagem e trabalhar com a copia.
- utilizar ferramendas como OpenSSL para validar hashs de atualizacao de ferramentas

#### Impactos
- desempenho computacional
- gerenciamentos  de vulnerabilidades em sistemas legados

### Assinaturas digitais e autenticação
Assinatura digital
Infraestrutura de Chaves Publicas (ICP) ou Public Key Infrastructure (KPI)
Autoridade Certificadora (AC)

#### Desafios na revogação de certificados
Certificate Revocation Lists (CRLs)
Online Certificate Status Protocol (OCSP)
OCSP Stapling
Automated Certificate Management Environment (ACME)

### Armazenamento seguro de credenciais e proteção de senhas
Salting and peppering
Key Derivation Fuctions (KDF)
NTDS.dit

## Identidade e controle de acesso
Identity and Access Management (IAM)
Sistema de Prevenção de Intrusão (IPS)
Joiner, Mover, Leaver (JML)

Federação de Identidade e Single Sign-On (SSO)
Security Assertion Markup Language (SAML) 2.0
OAuth 2.0 and OpenID Connect (OIDC)

### Modelos formais de controle de acesso
Discretionary Access Control (DAC)
Role-Based Access Control (RBAC)
Attribute-Based Access Control (ABAC)
Principle of Least Privilege (PoLP)
Policy Decision Point (PDP)
Policy Enforcement Points (PEPs)
Multi-factor Autenticaiton (MFA)
One-Time Password (OTP)
TOTP and Adversary-in-the-Middle (AiTM)

### Gestão de contas privilegiadas e acesso seguro aos recursos organizacionais
Privileged Access Management (PAM)
Password Vault
Zero Trust Network Access (ZTNA)


## Infraestrutura e protocolos seguros
