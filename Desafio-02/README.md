# 🐳 Desafio 02 — CTF Prático com Docker

**Capture The Flag: Quebre hashes, criptografias e capture FLAGS!**

Desenvolvido para FIAP — Cybersecurity Hacker Mindset

---

## 📚 Sobre Este Desafio

Servidor Docker com **5 desafios progressivos** de criptografia. Cada um explora uma técnica diferente:

1. **Easy:** Hash MD5 (inseguro) → Usar rainbow tables online
2. **Medium:** Hash SHA-256 + Salt → Força bruta com hashcat/john
3. **Hard:** Criptografia Simétrica (AES-256) → Quebra de chave
4. **Expert:** Criptografia Assimétrica (RSA) → Fatoração de primos
5. **Master:** Criar seu próprio sistema de encriptação

---

## 🚀 Quick Start (2 Minutos)

```bash
# 1. Rodar Docker
docker-compose up

# 2. Abrir navegador
# http://localhost:8080

# 3. Seguir os desafios na interface web
```

Se Docker não estiver instalado, veja [Desafio-01](../Desafio-01/PARTE0_SETUP.md).

---

## 🎯 Como Funciona

### Acesso Web
A interface em `http://localhost:8080` mostra:
- ✅ 5 desafios com descrição
- ✅ Arquivos para download (hash, encriptado, etc)
- ✅ Campo para enviar FLAG
- ✅ Feedback instantâneo (correto/incorreto)

### Ferramentas Disponíveis

Dentro do container:

```bash
# Ver ferramentas
which openssl hashcat john gpg python3

# Usar OpenSSL
openssl enc -d -aes-256-cbc -in file.enc -p

# Usar Hashcat
hashcat -m 1400 hash.txt wordlist.txt

# Usar John
john --format=sha256crypt hash.txt
```

---

## 🏆 Desafios Detalhados

### Desafio 1: Easy — MD5 (10 min)

**Arquivo:** `hash_md5.txt` contém um hash MD5

**O que fazer:**
1. Baixe o arquivo do navegador
2. Use uma ferramenta online (rainbow table) para quebrar
3. Envie a resposta (senha original)

**Ferramentas sugeridas:**
- https://crackstation.net/ (online)
- `hashcat -m 0 hash.txt wordlist.txt` (offline)

**Conceito:** MD5 é rápido → fácil de quebrar com força bruta

---

### Desafio 2: Medium — SHA-256 + Salt (10 min)

**Arquivo:** `hash_sha256_salt.txt`

**O que fazer:**
1. Identifique: é um hash SHA-256 com salt
2. Use `john` ou `hashcat` com wordlist
3. Capture a senha/FLAG

**Comando exemplo:**
```bash
john --format=sha256crypt hash.txt --wordlist=wordlist.txt
# ou
hashcat -m 1400 hash.txt wordlist.txt
```

**Conceito:** Salt dificulta rainbow tables, mas força bruta continua possível

---

### Desafio 3: Hard — AES-256 Simétrica (15 min)

**Arquivo:** `mensagem_encriptada.txt` (binário encriptado)

**O que fazer:**
1. Descriptografe usando OpenSSL
2. A chave está em... (dica no desafio web)
3. Extraia a FLAG

**Comando:**
```bash
openssl enc -d -aes-256-cbc -in mensagem_encriptada.txt -p -k "chave_aqui" -md sha256
```

**Conceito:** Criptografia simétrica = rápida, mas requer chave compartilhada

---

### Desafio 4: Expert — RSA Assimétrica (15 min)

**Arquivo:** `numeros_rsa.txt` (N e E públicos)

**O que fazer:**
1. Fatore N (semiprimo)
2. Calcule D (chave privada)
3. Descriptografe a mensagem

**Ferramentas:**
```bash
# Fatorar N
python3 -c "import factordb; print(factordb.factordb(N).get_factor_list())"

# Ou usar ferramenta online
# https://www.alpertron.com.ar/ECM.HTM
```

**Conceito:** RSA segurança depende de fatoração ser difícil (N grande)

---

### Desafio 5: Master — Criar Sistema (10 min)

**O que fazer:**
1. Implemente um sistema completo
2. Use OpenSSL ou pycryptodome
3. Encripte uma mensagem
4. Descriptografe e capture FLAG

**Script exemplo:**
```python
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes

key = b'chave_32_bytes_!' * 2  # 32 bytes para AES-256
cipher = AES.new(key, AES.MODE_EAX)
ciphertext, tag = cipher.encrypt_and_digest(b'mensagem')

# Descriptografar
cipher = AES.new(key, AES.MODE_EAX, nonce=cipher.nonce)
plaintext = cipher.decrypt_and_verify(ciphertext, tag)
```

**Conceito:** Aplicar toda teoria em implementação prática

---

## 📊 Cronograma

```
Desafio 1 (Easy)    → 10 min  (rainbow tables)
Desafio 2 (Medium)  → 10 min  (force brute)
Desafio 3 (Hard)    → 15 min  (AES decrypt)
Desafio 4 (Expert)  → 15 min  (RSA factor)
Desafio 5 (Master)  → 10 min  (implement)
──────────────────────────────
TOTAL               → 60 min
```

---

## 💡 Dicas de Sucesso

### 1. Primeira Vez?
- Comece com Desafio 1 (muito fácil)
- Progresso natural até Desafio 5
- Use dicas da interface web

### 2. Stuck?
- Releia o enunciado do desafio
- Procure pela "dica" (geralmente há um)
- Teste com exemplos pequenos primeiro

### 3. Ferramentas Offline
```bash
# Dentro do container (docker exec)
docker exec cripto-lab bash

# Agora você tem:
openssl, hashcat, john, gpg, python3 com pycryptodome
```

### 4. Documentação
- OpenSSL: `man openssl`
- Hashcat: `hashcat -h`
- John: `john --help`

---

## 🚨 Troubleshooting

| Problema | Solução |
|----------|---------|
| Port 8080 ocupada | `docker-compose -p 8081:80 up` (muda para 8081) |
| Página não carrega | Aguarde 10 segundos, recarregue (nginx init) |
| Docker não encontra arquivo | Verifique `challenges/` tem os arquivos |
| Ferramentas não encontradas | Entre no container: `docker exec -it cripto-lab bash` |

---

## 📁 Estrutura de Arquivos

```
Desafio-02/
├── Dockerfile          (imagem com ferramentas)
├── docker-compose.yml  (orquestração)
├── index.html          (interface web)
├── nginx.conf          (configuração servidor)
├── challenges/         (desafios)
│   ├── hash_md5.txt
│   ├── hash_sha256_salt.txt
│   ├── mensagem_encriptada.txt
│   ├── numeros_rsa.txt
│   └── ...
└── README.md           (este arquivo)
```

---

## 🎓 O Que Você Aprenderá

✅ Diferença entre Hash e Criptografia  
✅ Porque MD5 é inseguro  
✅ Como salt protege hashes  
✅ Criptografia simétrica (AES) na prática  
✅ Criptografia assimétrica (RSA) e fatoração  
✅ Implementar sistemas criptográficos reais  

---

## 🔔 Após Completar

- [ ] Capturou todas as 5 FLAGS?
- [ ] Entende diferenças entre os 3 mecanismos?
- [ ] Consegue explicar cada ataque?
- [ ] Sabe quando usar qual técnica?

**Próximo passo:** Revisar [Desafio-01](../Desafio-01/) para aprofundar teoria

---

**Boa sorte! 🎯**

Desenvolvido para FIAP | 2026
