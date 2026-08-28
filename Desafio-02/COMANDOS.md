# 📎 Dicionário de Comandos — Cripto Lab

Referência rápida com os comandos mais usados no dia a dia do lab. Todos testados de verdade neste ambiente (container `cripto-lab`, Alpine Linux).

---

## 🐳 Docker — subir, derrubar, inspecionar

```bash
# Subir o lab (build + start)
docker compose up -d --build

# Ver se está rodando
docker ps --filter name=cripto-lab

# Logs do container
docker logs cripto-lab

# Parar e remover
docker compose down

# Parar, remover container E a imagem (rebuild limpo)
docker compose down --rmi local
docker compose up -d --build
```

> Use `docker compose` (com espaço). `docker-compose` (com hífen) é a versão antiga/deprecada do binário.

---

## 💻 Entrar no container

```bash
# Shell completo (bash)
docker exec -it cripto-lab bash

# Shell mínimo (sh) — mesma coisa, sem histórico/autocomplete
docker exec -it cripto-lab sh
```

---

## 📁 Onde as coisas ficam dentro do container

| Path | O que é | Gravável? |
|------|---------|-----------|
| `/var/www/html/challenges/` | Arquivos dos desafios (o que o navegador baixa) | ❌ Não — montado `:ro` no `docker-compose.yml` |
| `/var/www/html/index.html` | A interface web | ❌ Não |
| `/root/.john/john.pot` | Cache de hashes já quebrados pelo `john` | — |
| `/root/.john/john.log` | Log de execuções do `john` | — |
| `/tmp/` | Área de trabalho livre | ✅ Sim |

**Por isso:** sempre copie os arquivos de `challenges/` para `/tmp` antes de tentar criar/escrever algo:

```bash
mkdir -p /tmp/lab && cd /tmp/lab
cp /var/www/html/challenges/*.txt .
```

Se tentar escrever direto em `/var/www/html/challenges/`, o erro é:
```
bash: arquivo.txt: Read-only file system
```

---

## 🔓 Desafio 1 — MD5 (`john`)

```bash
john --format=raw-md5 hash_md5.txt --wordlist=wordlist_comum.txt

# Ver senha já quebrada (se já rodou antes)
john --show --format=raw-md5 hash_md5.txt

# Resetar o cache pra rodar do zero
rm -f ~/.john/john.pot
```

## 🔓 Desafio 2 — SHA-256 + Salt (bash + `sha256sum`)

```bash
HASH_ALVO="<cole o hash do arquivo>"
for w in $(cat wordlist_comum.txt); do
  [ "$(echo -n "abc123xyz$w" | sha256sum | cut -d' ' -f1)" = "$HASH_ALVO" ] && echo "ACHOU: $w"
done

# Verificar uma candidata manualmente
echo -n "abc123xyz<candidata>" | sha256sum
```

## 🔓 Desafio 3 — AES-256-CBC (`openssl`)

```bash
openssl enc -d -aes-256-cbc -pbkdf2 -in criptografado.bin -k "<tentativa>"
```

## 🔓 Desafio 4 — RSA (`python3` + `sympy`)

```bash
# Fatorar N
python3 -c "from sympy import factorint; print(factorint(3233))"

# Decifrar os blocos (depois de ter p, q, e calcular d)
python3 -c "
p, q = 61, 53
d = pow(65537, -1, (p-1)*(q-1))
blocks = [int(x) for x in open('mensagem_encriptada_rsa.txt').readlines()[2].strip().split(',')]
print(''.join(chr(pow(c, d, 3233)) for c in blocks))
"
```

## 🔓 Desafio 5 — Sistema Híbrido (`openssl`)

```bash
openssl genrsa -out chave_privada.pem 2048
openssl rsa -in chave_privada.pem -pubout -out chave_publica.pem
openssl rand -hex 32 > chave_aes.hex
openssl enc -aes-256-cbc -pbkdf2 -in msg.txt -out msg.aes -k "$(cat chave_aes.hex)"
```

---

## 🛠️ Verificar ferramentas instaladas

```bash
which openssl john python3 gpg
python3 -c "import sympy, Crypto; print('sympy', sympy.__version__)"
```

---

## 🚨 Erros comuns

| Erro | Causa | Solução |
|------|-------|---------|
| `Read-only file system` | Tentou escrever em `/var/www/html/challenges/` | Copie os arquivos para `/tmp` primeiro |
| `No password hashes left to crack` (john) | Hash já foi quebrado antes (cache) | `john --show ...` pra ver, ou `rm -f ~/.john/john.pot` pra resetar |
| `bad decrypt` (openssl) | Senha/chave errada | Revise a dica do desafio, não é arquivo corrompido |
| `hashcat: command not found` | `hashcat` não existe nesta imagem (sem pacote pra Alpine/musl) | Use `john` com `wordlist_comum.txt` |
| Loop do Desafio 2 não imprime nada | Ou a senha não está na wordlist, ou o hash foi copiado errado | Confira se copiou os 64 caracteres do hash sem espaços/quebras |
| Porta 8080 não responde / conteúdo errado | Outro processo já está usando a porta 8080 no host | Confira com `netstat`/`ss -tlnp \| grep 8080` antes de mudar a porta em `docker-compose.yml` |

---

Desenvolvido para FIAP — Cybersecurity Hacker Mindset | 2026
