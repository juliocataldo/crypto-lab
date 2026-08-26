# 🔐 Cripto Lab — CTF de Hash e Criptografia (60 min)

**Servidor Docker com 5 desafios práticos de Hash, Criptografia Simétrica e Assimétrica para alunos capturarem FLAGS.**

- **Nível:** Intermediário → Avançado
- **Duração:** 60 minutos (5 desafios progressivos)
- **Linguagem:** Português (BR)
- **Formato:** CTF (Capture The Flag)

---

## 📖 O que é?

Laboratório prático onde alunos exploram os **três pilares da criptografia moderna**:

1. **Easy (10 min):** Hash simples (MD5) → Rainbow tables
2. **Medium (10 min):** Hash com salt (SHA-256) → Força bruta limitada
3. **Hard (15 min):** Criptografia Simétrica (AES-256) → Quebra de chave
4. **Expert (15 min):** Criptografia Assimétrica (RSA) → Fatoração
5. **Master (10 min):** Alunos criam sistema de encriptação completo

**Objetivo pedagógico:**
- Entender diferença entre Hash, Criptografia Simétrica e Assimétrica
- Conhecer ferramentas reais (hashcat, openssl, gpg)
- Praticar ataques contra cada mecanismo
- Defender contra cada tipo de ataque

---

## 🚀 Quick Start (Para Professor)

### Pré-requisitos
- Docker instalado (`docker --version`)
- Conexão internet (primeira vez)

### Build & Run (30 segundos)

```bash
# 1. Build da imagem (primeira vez - ~5 min)
docker build -t jaycewilliam/cripto-lab:latest .

# 2. Rodar container
docker run -it -p 8080:80 --rm --name cripto-lab jaycewilliam/cripto-lab:latest

# 3. Acessar no navegador
# Abra: http://localhost:8080
```

### Versão simplificada (se já tiver no Docker Hub)
```bash
docker run -it -p 8080:80 --rm jaycewilliam/cripto-lab:latest
```

### Verificar se está rodando
```bash
# Em outro terminal
curl http://localhost:8080
# Esperado: página HTML com desafios
```

### Parar
```bash
# Ctrl+C no terminal ou:
docker stop cripto-lab
```

---

## 🎯 Roteiro de 60 Minutos

### ⏱️ Fase 1: Introdução (5 min)
- Aluno acessa `http://localhost:8080`
- Lê os 5 desafios
- Baixa arquivos necessários

---

### 🟢 Fase 2: Easy Challenge (10 min)
**Tipo:** Hash MD5 (inseguro)  
**Arquivo:** `hash_md5.txt` contém: `5d41402abc4b2a76b9719d911017c592`

**Passos:**
```bash
# 1. Tentar rainbow table online
# Acessa: https://md5.gromweb.com/?md5=5d41402abc4b2a76b9719d911017c592

# Ou usar hashcat offline
echo "5d41402abc4b2a76b9719d911017c592" > hashes.txt
hashcat -m 0 hashes.txt /usr/share/dict/rockyou.txt

# 2. Documentar:
# - Qual era o valor original (plaintext)?
# - Quanto tempo levou?
```

**Expected Output:**
```
FLAG{hash_md5_descoberto_hello}
```

**Conceito:** MD5 é tão rápido que qualquer computador moderno quebra em milissegundos.

---

### 🟡 Fase 3: Medium Challenge (10 min)
**Tipo:** Hash SHA-256 com salt  
**Arquivo:** `hash_sha256_salt.txt` contém:  
```
salt: abc123xyz
hash: 8ca46e5c0d5d2e9b4f2f7d8a6c1b3e5a7f9c2d4e6f8a0b1c3d5e7f9a1b3c5
```

**Passos:**
```bash
# 1. Reconhecer que tem salt
cat hash_sha256_salt.txt

# 2. Criar wordlist customizada com salt
# Arquivo: wordlist_salted.txt
# Conteúdo: abc123xyzsenha1
#           abc123xyzsenha123
#           abc123xyzpassword
#           abc123xyzadmin

# 3. Gerar hashes de teste
echo -n "abc123xyzsenha1" | sha256sum
# Comparar com o hash

# 4. Quando achar, extrair plaintext
# Plaintext: "senha1" (+ salt "abc123xyz")

# 5. Documentar:
# - Qual era o plaintext?
# - Como o salt aumentou complexidade?
```

**Expected Output:**
```
FLAG{sha256_com_salt_descoberto_senha1}
```

**Conceito:** Salt torna rainbow tables inúteis, força ataques por dicionário.

---

### 🔴 Fase 4: Hard Challenge (15 min)
**Tipo:** Criptografia Simétrica (AES-256-CBC)  
**Arquivo:** `criptografado.bin` (arquivo binário encriptado)  
**Dado:** `Chave: "minha_chave_secreta_32_bytes_ok"` (em base64 no arquivo)

**Passos:**
```bash
# 1. Examinar arquivo
file criptografado.bin
hexdump -C criptografado.bin | head

# 2. Tentar descriptografar com OpenSSL
# Testar chaves comuns
openssl enc -aes-256-cbc -d -in criptografado.bin -K \
  $(echo -n "minha_chave_secreta_32_bytes_ok" | xxd -p) -iv 0 -nopad

# 3. Se falhar, tentar força bruta contra pequeno wordlist
for chave in $(cat wordlist_keys.txt); do
  openssl enc -aes-256-cbc -d -in criptografado.bin \
    -K $(echo -n "$chave" | xxd -p) -iv 0 -nopad 2>/dev/null && echo "ENCONTRADA: $chave"
done

# 4. Quando descriptografar, ler plaintext
cat arquivo_descriptografado.txt

# 5. Documentar:
# - Qual chave funcionou?
# - Tamanho da chave AES-256?
# - Por que força bruta é impraticável aqui?
```

**Expected Output:**
```
FLAG{aes256_quebrada_chave_revelada_mensagem_confidencial}
```

**Conceito:**
- Força bruta é impraticável contra AES-256 real
- Mas se a chave vem de senha fraca = vulnerável
- Neste lab, chave é conhecida (pedagogia)

---

### 🔵 Fase 5: Expert Challenge (15 min)
**Tipo:** Criptografia Assimétrica (RSA)  
**Arquivos:**
```
chave_publica.pem — chave pública do professor
mensagem_encriptada.txt — mensagem encriptada com chave pública
numeros_rsa.txt — contém N (módulo), e (expoente público)
```

**Passos:**
```bash
# 1. Examinar chave pública
openssl rsa -in chave_publica.pem -text -noout

# 2. Tentar descriptografar (sem chave privada)
openssl rsautl -decrypt -in mensagem_encriptada.txt -inkey chave_publica.pem -pubin
# Esperado: FALHA (só chave privada decripta)

# 3. Tentar fatoração do módulo N
# Arquivo: numeros_rsa.txt contém N pequeno
# Usar ferramenta: factordb-pycli ou yafu

python3 -c "
N = <copiar N de numeros_rsa.txt>
# Fatorar N = p * q (pequeno demais para lab)
# Resultado: p=61, q=53 (exemplo)
# Então: phi = (61-1)*(53-1) = 3120
# Calcular d: d = e^-1 mod phi
"

# 4. Reconstruir chave privada e descriptografar
# (Lab fornece script helper)
python3 gera_chave_privada.py --N <N> --e 65537
# Saída: chave_privada.pem

openssl rsautl -decrypt -in mensagem_encriptada.txt -inkey chave_privada.pem

# 5. Documentar:
# - Qual era p e q?
# - Por que RSA pequeno é fraco?
# - Tamanho moderno de RSA?
```

**Expected Output:**
```
FLAG{rsa_fatorado_chave_privada_reconstruida_segredo_revelado}
```

**Conceito:**
- RSA é baseado em dificuldade de fatoração
- Chaves pequenas (~512 bits) são fatoráveis
- Chaves modernas (~2048-4096 bits) são impraticáveis

---

### 🟣 Fase 6: Master Challenge (Bonus - 10 min)
**Tarefa:** Virar o defensor — criar sistema seguro de encriptação

**Passos:**
```bash
# Desafio: Encriptar mensagem com TRÊS camadas
# 1. Gerar chave RSA (assimétrica)
openssl genrsa -out minha_chave_privada.pem 2048
openssl rsa -in minha_chave_privada.pem -pubout > minha_chave_publica.pem

# 2. Gerar chave AES-256 (simétrica)
openssl rand -hex 32 > chave_aes.hex

# 3. Hash (integridade) + Criptografia em camadas
echo "Mensagem super secreta" > mensagem.txt

# Hash da mensagem original
sha256sum mensagem.txt > hash_original.txt

# Encriptar com AES-256
openssl enc -aes-256-cbc -in mensagem.txt -out mensagem.aes -K \
  $(cat chave_aes.hex) -iv 0

# Encriptar chave AES com RSA público
openssl rsautl -encrypt -inkey minha_chave_publica.pem -pubin \
  -in chave_aes.hex -out chave_aes.enc

# 4. Enviar para colega:
# - mensagem.aes (encriptada com AES)
# - chave_aes.enc (chave AES encriptada com RSA público)
# - minha_chave_publica.pem (sua chave pública)

# 5. Colega descriptografa:
# a) Descriptografa chave AES com chave privada
openssl rsautl -decrypt -inkey sua_chave_privada.pem -in chave_aes.enc -out chave_aes_descriptografada.hex

# b) Descriptografa mensagem com AES
openssl enc -aes-256-cbc -d -in mensagem.aes -out mensagem_descriptografada.txt \
  -K $(cat chave_aes_descriptografada.hex) -iv 0

# c) Verifica integridade
sha256sum -c hash_original.txt

echo "Mensagem segura entregue!"
```

**Expected Output:**
```
FLAG{sistema_de_encriptacao_em_tres_camadas_implementado_com_sucesso}
```

**Conceito:**
- **Defesa em profundidade:** múltiplas camadas = mais segurança
- **Simétrica rápida:** AES para dados grandes
- **Assimétrica segura:** RSA para trocar chaves
- **Integridade:** Hash para detectar tampering
- **Real-world:** Assim funcionam PGP, TLS, Signal, WhatsApp

---

## 🛠️ Ferramentas Dentro do Container

Todas já instaladas:
- **openssl** → Encriptação, geração de chaves, certificados
- **hashcat** → Quebra de hashes (GPU-accelerated)
- **john** → Quebra de senhas (CPU)
- **xxd** → Análise hexadecimal
- **python3 + pycryptodome** → Scripts de criptografia customizados
- **gpg** → Encriptação PGP
- **nginx** → Servidor web (serva os arquivos)

---

## 📊 Estrutura da Imagem Docker

```
┌─────────────────────────────────────────┐
│       Alpine Linux 3.18 (~400MB)       │
├─────────────────────────────────────────┤
│ • Nginx (web server)                    │
│ • OpenSSL + libssl                      │
│ • Hashcat + John the Ripper            │
│ • Python3 + pycryptodome               │
│ • GnuPG (PGP)                          │
│ • Ferramentas: xxd, binutils, coreutils│
├─────────────────────────────────────────┤
│ PORTA: 8080 → http://localhost:8080    │
└─────────────────────────────────────────┘
```

**Tamanho:** ~400-500 MB

---

## 🐛 Troubleshooting

| Problema | Solução |
|----------|---------|
| ❌ `Cannot connect to localhost:8080` | Container não subiu. Rode: `docker logs cripto-lab` |
| ❌ `docker: command not found` | Docker não instalado. Instale Docker Desktop |
| ❌ `openssl: command not found` | Erro no container. Cheque: `docker ps` |
| ❌ `Permission denied: port 8080` | Porta 8080 ocupada. Mude: `docker run -p 8081:80 ...` |
| ❌ `hashcat fails on CPU` | Normal, hashcat é GPU-focused. Use `john` ou `hashcat --workload-profile=1` |
| ❌ `Chave privada não encontrada` | Lab fornece script `gera_chave_privada.py` |

---

## 📋 Checklist para Professor

- [ ] Docker instalado
- [ ] Imagem buildada ou puxada do Docker Hub
- [ ] Porta 8080 disponível
- [ ] Comunicou alunos: acessar http://localhost:8080
- [ ] Alunos têm Docker Desktop instalado (Windows/Mac)
- [ ] Preparou local para coleta de FLAGS

---

## 🎓 Conceitos-chave para Alunos

### Hash vs. Criptografia
| | Hash | Simétrica | Assimétrica |
|---|---|---|---|
| **Reversível?** | ❌ Não (one-way) | ✅ Sim (ambos lados) | ✅ Sim (lados diferentes) |
| **Integridade?** | ✅ Detecta tampering | ❌ Não | ✅ Sim (assinatura) |
| **Velocidade** | 🚀 Rápido | 🚀 Rápido | 🐢 Lento |
| **Chave?** | ❌ Não | ✅ 1 chave compartilhada | ✅ 2 chaves (pub+priv) |
| **Exemplo** | SHA-256, MD5 | AES-256 | RSA, ECC |
| **Uso** | Senhas, certificados | Dados em repouso | Troca de chaves |

### Hierarquia de Segurança

```
┌─────────────────────────────────────────────┐
│ 1. Hash seguro com salt (SHA-256 + bcrypt) │ ← Senhas
├─────────────────────────────────────────────┤
│ 2. Criptografia Simétrica (AES-256)        │ ← Dados em repouso
├─────────────────────────────────────────────┤
│ 3. Criptografia Assimétrica (RSA 2048+)    │ ← Trocar chaves
├─────────────────────────────────────────────┤
│ 4. Autenticação (HMAC, Assinatura digital) │ ← Origem confiável
└─────────────────────────────────────────────┘
```

### Por que isso importa?
- **Hash:** Proteção de senhas (irreversível)
- **Simétrica:** Confidencialidade rápida (dados, disco, rede)
- **Assimétrica:** Confiança sem compartilhar chaves (https, email seguro)
- **Combinadas:** Real-world (TLS usa ambas!)

---

## 🔐 Senhas/Chaves dos Desafios (Para Professor)

| Desafio | Tipo | Plaintext/Chave | Dificuldade |
|---------|------|---|------------|
| 1 | MD5 | `hello` | Easy |
| 2 | SHA256+salt | `senha1` (salt: `abc123xyz`) | Medium |
| 3 | AES-256-CBC | `minha_chave_secreta_32_bytes_ok` | Hard |
| 4 | RSA | N pequeno (61 x 53) | Expert |
| 5 | Hybrid | Aluno define | Bonus |

---

## 📚 Aprofundamento (Pós-Lab)

### 1. Por que AES-256 não quebra?
```
2^256 combinações possíveis
= 115.7 x 10^75 possibilidades

Computador mais rápido: 10^18 tentativas/segundo
Tempo estimado: 3.6 x 10^51 anos
Idade do universo: 13.8 x 10^9 anos
```
Conclusão: **Impraticável mesmo em supercomputadores**

### 2. Ataques práticos contra Hash
```bash
# Rainbow table: pré-computar hashes
# Mitigation: Use salt!

# Força bruta: tentar todas as senhas
# Mitigation: Use bcrypt com custo adaptativo!

# Colisão: encontrar 2 valores com mesmo hash
# Mitigation: MD5 deprecated! Use SHA-256 ou SHA-3
```

### 3. Ataques contra RSA
```bash
# Side-channel attack: analisa tempo/poder
# Padding oracle: explora erro na descriptografia
# Fatoração: quebra N em p*q (impraticável em RSA moderno)
```

### 4. Defesa em Produção
- Sempre usar HTTPS (TLS)
- Armazenar senhas com bcrypt/argon2
- Usar AES-256 para dados sensíveis
- RSA 2048+ para chaves
- Renovar chaves regularmente

---

## 🚀 Deploy no Docker Hub (Para Professor)

```bash
# 1. Build
docker build -t jaycewilliam/cripto-lab:latest .

# 2. Push
docker push jaycewilliam/cripto-lab:latest

# 3. Verificar
docker pull jaycewilliam/cripto-lab:latest
```

Depois alunos usam:
```bash
docker run -it -p 8080:80 --rm jaycewilliam/cripto-lab:latest
```

---

## 📝 Licença

MIT — Uso livre em contexto educacional

---

## 👨‍🏫 Instructor Notes

- **Tempo real:** ~60-75 min (alguns alunos levam mais em RSA)
- **Dificuldade:** Médio-Avançado (conceitos matemáticos em RSA)
- **Diferencial:** Alunos bons entendem por que cada mecanismo é usado, tentam ataques reais, exploram matemática de RSA
- **Integração:** Parte do semestre de segurança (combina com Stego Lab e Tor Lab para 120+ min)
- **Engajamento:** CTF-style + três paradigmas (hash/simétrica/assimétrica) = entendimento completo

---

**Desenvolvido para FIAP — Cybersecurity Hacker Mindset | 2026**
