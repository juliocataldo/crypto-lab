# 🐳 Desafio 02 — CTF Prático com Docker

**Capture The Flag: Quebre hashes, criptografias e capture FLAGS!**

Desenvolvido para FIAP — Cybersecurity Hacker Mindset

---

## 📚 Sobre Este Desafio

Servidor Docker com **5 desafios progressivos** de criptografia. Cada um explora uma técnica diferente:

1. **Easy:** Hash MD5 (inseguro) → Força bruta offline com `john` + wordlist
2. **Medium:** Hash SHA-256 + Salt → Força bruta offline com script `bash`
3. **Hard:** Criptografia Simétrica (AES-256) → Descriptografar com `openssl`
4. **Expert:** Criptografia Assimétrica (RSA) → Fatoração com `sympy`
5. **Master:** Criar seu próprio sistema de encriptação

**Requisitos:** só Docker. Depois do `docker compose up`, **nenhuma ferramenta externa nem internet são necessários** — tudo (openssl, john, python3+sympy) já vem no container. Docker é necessário porque essas ferramentas não existem nativamente no Windows.

---

## 🚀 Quick Start (2 Minutos)

```bash
# 1. Clonar o repositório (pule se já estiver com o repo localmente)
git clone https://github.com/juliocataldo/crypto-lab.git
cd crypto-lab/Desafio-02

# 2. Rodar Docker (docker compose, com espaço, no Docker moderno)
docker compose up -d --build

# 3. Abrir navegador
# http://localhost:8080

# 4. Resolver os desafios e colar a FLAG no campo de cada card
#    (validação acontece no navegador; progresso fica salvo no localStorage)
```

Se Docker não estiver instalado, baixe o Docker Desktop: https://www.docker.com/products/docker-desktop/

> 📎 Cola no terminal e esquece comandos? Veja o [COMANDOS.md](COMANDOS.md) — dicionário rápido com `docker exec`, paths dentro do container e os comandos de cada desafio.

---

## 🎯 Como Funciona

### Acesso Web
A interface em `http://localhost:8080` mostra:
- ✅ 5 desafios com descrição, dificuldade e pontuação
- ✅ Arquivos para download (hash, encriptado, wordlist, etc)
- ✅ Campo de FLAG por desafio, com validação instantânea no navegador
- ✅ Barra de progresso e pontos, salvos no `localStorage` (persistem entre recargas)

### Ferramentas Disponíveis

Dentro do container (`docker exec -it cripto-lab sh`):

```bash
# Ver ferramentas
which openssl john python3
python3 -c "import sympy; print(sympy.__version__)"

# Usar OpenSSL
openssl enc -d -aes-256-cbc -pbkdf2 -in criptografado.bin -k "senha"

# Usar John (hash direto, sem salt)
john --format=raw-md5 hash_md5.txt --wordlist=wordlist_comum.txt

# Fatorar RSA
python3 -c "from sympy import factorint; print(factorint(3233))"
```

> `hashcat` **não está incluído** (sem pacote para Alpine/musl) — `john` + a wordlist embutida (`wordlist_comum.txt`) cobrem os Desafios 1 e 2 sem precisar de internet.

---

## 🏆 Desafios Detalhados

### Desafio 1: Easy — MD5 (10 min)

**Arquivos:** `hash_md5.txt` (hash MD5) + `wordlist_comum.txt` (wordlist local)

**O que fazer:**
1. Baixe os dois arquivos
2. Quebre o hash com `john` (offline, sem internet)
3. Envie `FLAG{<palavra_original>}`

**Comando (100% offline):**
```bash
john --format=raw-md5 hash_md5.txt --wordlist=wordlist_comum.txt
```

> 💡 **Dica:** se aparecer `No password hashes left to crack`, não é erro — o `john` guarda um cache (`~/.john/john.pot`) e esse hash já foi quebrado antes (por você ou por outra pessoa nesse mesmo container). Veja a senha já encontrada com `john --show --format=raw-md5 hash_md5.txt`, ou apague o cache (`rm -f ~/.john/john.pot`) pra rodar a quebra do zero.

**Conceito:** MD5 é rápido → fácil de quebrar com força bruta

---

### Desafio 2: Medium — SHA-256 + Salt (10 min)

**Arquivos:** `hash_sha256_salt.txt` + `wordlist_comum.txt`

**O que fazer:**
1. O salt vem no próprio arquivo; o hash é `sha256(salt + senha)`
2. `john` sozinho não aplica o salt automaticamente — use o loop abaixo
3. Capture `FLAG{<senha_original>}`

**Comando (100% offline, bash puro):**
```bash
HASH_ALVO="<cole o hash do arquivo>"
for w in $(cat wordlist_comum.txt); do
  [ "$(echo -n "abc123xyz$w" | sha256sum | cut -d' ' -f1)" = "$HASH_ALVO" ] && echo "ACHOU: $w"
done
```

> 💡 **Dica:** o loop roda em menos de 1 segundo e **não imprime nada se não achar** — silêncio não é erro. Se terminar sem achar, confira se copiou o hash completo (64 caracteres), sem espaços ou quebras de linha extras.

**Conceito:** Salt dificulta rainbow tables, mas força bruta continua possível

---

### Desafio 3: Hard — AES-256 Simétrica (15 min)

**Arquivo:** `criptografado.bin` (binário encriptado com AES-256-CBC)

**O que fazer:**
1. Descriptografe usando OpenSSL
2. A senha é o nome da instituição + o ano do curso, tudo minúsculo e sem espaço (dica no card do desafio)
3. A flag está no texto decifrado

**Comando:**
```bash
openssl enc -d -aes-256-cbc -pbkdf2 -in criptografado.bin -k "<sua_tentativa>"
```

> 💡 **Dica:** erro `bad decrypt`? É quase sempre **senha errada**, não arquivo corrompido — o `-pbkdf2` detecta isso de forma confiável. Revise o formato: minúsculo, sem espaço, instituição colada ao ano.

**Conceito:** Criptografia simétrica = rápida, mas requer chave compartilhada

---

### Desafio 4: Expert — RSA Assimétrica (15 min)

**Arquivos:** `numeros_rsa.txt` (N e e públicos) + `mensagem_encriptada_rsa.txt` (blocos cifrados) + `chave_publica.pem`

**O que fazer:**
1. Fatore N (semiprimo pequeno, propositalmente fraco)
2. Calcule phi(N) e depois d (chave privada)
3. Decifre cada bloco com `pow(c, d, N)` e converta com `chr()`

**Comando (100% offline, com `sympy`):**
```bash
python3 -c "from sympy import factorint; print(factorint(3233))"
python3 -c "
p, q = 61, 53
d = pow(65537, -1, (p-1)*(q-1))
blocks = [int(x) for x in open('mensagem_encriptada_rsa.txt').readlines()[2].strip().split(',')]
print(''.join(chr(pow(c, d, 3233)) for c in blocks))
"
```

> 💡 **Dica:** `factorint(3233)` devolve um dicionário tipo `{53: 1, 61: 1}` — as **chaves** desse dicionário são p e q, não os valores. Os blocos cifrados estão na 3ª linha de `mensagem_encriptada_rsa.txt`, separados por vírgula (ignore o texto explicativo acima deles).

**Conceito:** RSA segurança depende de fatoração ser difícil (N grande)

---

### Desafio 5: Master — Sistema Híbrido (10 min)

Sem flag fixa — é autoavaliado por um checklist na própria interface.

**O que fazer:**
1. Gere um par de chaves RSA
2. Encripte uma mensagem com AES-256
3. Proteja a chave AES com a chave pública RSA (envelope digital)
4. Decifre de volta e confirme que recupera a mensagem original

**Comandos:**
```bash
openssl genrsa -out chave_privada.pem 2048
openssl rand -hex 32 > chave_aes.hex
openssl enc -aes-256-cbc -pbkdf2 -in msg.txt -out msg.aes -k "$(cat chave_aes.hex)"
```

> 💡 **Dica:** RSA só consegue encriptar dados **menores que o tamanho da chave** — por isso você encripta a chave AES (32 bytes) com RSA, e não a mensagem inteira. É exatamente assim que TLS e PGP resolvem esse limite na prática.

**Conceito:** Defesa em profundidade — combinar hash, simétrica e assimétrica, como PGP/TLS fazem na prática

---

## 📊 Cronograma

```
Desafio 1 (Easy)    → 10 min  (john + wordlist)
Desafio 2 (Medium)  → 10 min  (força bruta com salt)
Desafio 3 (Hard)    → 15 min  (AES decrypt)
Desafio 4 (Expert)  → 15 min  (RSA factor)
Desafio 5 (Master)  → 10 min  (implement)
──────────────────────────────
TOTAL               → 60 min
```

---

## 🚨 Troubleshooting

| Problema | Solução |
|----------|---------|
| Build falha | Rode `docker compose up -d --build` (não `docker-compose`, deprecado nas versões novas do Docker Desktop) |
| Port 8080 ocupada | Outro processo (às vezes um `python -m http.server` esquecido) pode já estar usando a porta — confira com `netstat`/`ss` antes de mudar a porta |
| Página não carrega | Aguarde alguns segundos e recarregue |
| Docker não encontra arquivo | Verifique se `challenges/` tem os arquivos (rode `python3 gera_desafios.py` dentro de `challenges/` se estiverem faltando) |
| Ferramentas não encontradas | Entre no container: `docker exec -it cripto-lab sh` |

---

## 📁 Estrutura de Arquivos

```
Desafio-02/
├── Dockerfile          (imagem com ferramentas: nginx, openssl, john, python3+sympy+pycryptodome, gpg)
├── docker-compose.yml  (orquestração)
├── .dockerignore       (nunca publica gera_desafios.py nem RESPOSTAS.md)
├── index.html          (interface web — tema CTF, flags validadas no navegador)
├── nginx.conf          (configuração do servidor; bloqueia gabarito e gerador)
├── COMANDOS.md         (dicionário rápido de comandos)
├── challenges/
│   ├── gera_desafios.py          (gerador — não versionado, roda local)
│   ├── hash_md5.txt
│   ├── hash_sha256_salt.txt
│   ├── wordlist_comum.txt
│   ├── criptografado.bin
│   ├── numeros_rsa.txt
│   ├── mensagem_encriptada_rsa.txt
│   ├── chave_publica.pem
│   └── README.md
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

- [ ] Capturou as 4 FLAGS (Desafios 1-4)?
- [ ] Completou o checklist do Desafio 5 (sistema híbrido)?
- [ ] Entende diferenças entre os 3 mecanismos?
- [ ] Consegue explicar cada ataque?
- [ ] Sabe quando usar qual técnica?

**Próximo passo:** Discutir em grupo como cada ataque poderia ser mitigado em produção

---

**Boa sorte! 🎯**

Desenvolvido para FIAP | 2026
