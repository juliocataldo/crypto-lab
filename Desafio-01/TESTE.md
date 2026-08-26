# 🧪 Guia de Teste — Cripto Lab

Este arquivo descreve como testar os desafios localmente antes de rodar o Docker.

## 📋 Pré-requisitos

```bash
# Python 3.8+
python3 --version

# OpenSSL
openssl version

# Ferramentas (opcionais)
apt-get install hashcat john xxd  # Linux
brew install hashcat john         # macOS
```

## 🎯 Testar Desafios Localmente

### 1️⃣ Gerar Desafios

```bash
# Execute este script para gerar os arquivos de desafio
python3 gera_desafios.py
```

Isso criará:
- `challenges/hash_md5_valor.txt` — Hash MD5
- `challenges/hash_sha256_valor.txt` — Hash SHA256+salt
- `challenges/criptografado.bin` — Arquivo AES-256
- `challenges/numeros_rsa_valores.txt` — Parâmetros RSA
- `challenges/chave_publica.pem` — Chave pública
- `challenges/mensagem_encriptada_rsa.txt` — Mensagem encriptada

### 2️⃣ Testar Hash MD5

```bash
# Gerar o hash
PLAINTEXT="hello"
MD5_HASH=$(echo -n "$PLAINTEXT" | md5sum | cut -d' ' -f1)
echo "Hash: $MD5_HASH"

# Quebrar com hashcat
echo "$MD5_HASH" > test_md5.txt
hashcat -m 0 test_md5.txt /usr/share/dict/rockyou.txt

# Ou com John
john test_md5.txt
```

### 3️⃣ Testar Hash SHA256+Salt

```bash
# Gerar hash com salt
SALT="abc123xyz"
PLAINTEXT="senha1"
SHA256_HASH=$(echo -n "${SALT}${PLAINTEXT}" | sha256sum | cut -d' ' -f1)
echo "Hash: $SHA256_HASH"

# Testar força bruta
for pwd in senha senha1 password admin test; do
  TESTE=$(echo -n "${SALT}${pwd}" | sha256sum | cut -d' ' -f1)
  if [ "$TESTE" = "$SHA256_HASH" ]; then
    echo "✓ Encontrado: $pwd"
  fi
done
```

### 4️⃣ Testar AES-256

```bash
# Preparar dados
CHAVE="minha_chave_secreta_32_bytes_ok"
PLAINTEXT="Mensagem super secreta do laboratório!"

# Encriptar
echo -n "$PLAINTEXT" > plaintext.txt
openssl enc -aes-256-cbc -in plaintext.txt -out criptografado.bin \
  -K $(echo -n "$CHAVE" | xxd -p) -iv 0

# Descriptografar
openssl enc -aes-256-cbc -d -in criptografado.bin \
  -K $(echo -n "$CHAVE" | xxd -p) -iv 0
```

### 5️⃣ Testar RSA

```bash
# Gerar chave privada
python3 gera_chave_privada.py --N 3233 --e 65537 --p 61 --q 53

# Verificar chave
openssl rsa -in chave_privada.pem -text -noout
```

## 🐳 Testar com Docker

### Build

```bash
# Build da imagem
docker build -t cripto-lab:test .

# Rodar container
docker run -it -p 8080:80 --name cripto-lab cripto-lab:test

# Em outro terminal:
curl http://localhost:8080
```

### Parar e Limpar

```bash
docker stop cripto-lab
docker rm cripto-lab
```

## ✅ Checklist Antes do Deploy

- [ ] `gera_desafios.py` cria todos os arquivos
- [ ] Todos os hashes podem ser quebrados com as ferramentas fornecidas
- [ ] AES encripta e desencripta corretamente
- [ ] RSA pequeno é fatorável
- [ ] Docker build completa sem erros
- [ ] Nginx serve http://localhost:8080
- [ ] Arquivos estão acessíveis em /challenges/

## 🚨 Troubleshooting

| Problema | Solução |
|----------|---------|
| `gera_desafios.py: ModuleNotFoundError: pycryptodome` | `pip install pycryptodome` |
| `hashcat not found` | Instale: `apt-get install hashcat` |
| `docker command not found` | Instale Docker Desktop |
| `Port 8080 in use` | Mude: `docker run -p 8081:80 ...` |
| `nginx: command not found` | Estou dentro do container? |

---

**Para rodar tudo rapidamente:**

```bash
# 1. Gerar desafios
python3 gera_desafios.py

# 2. Build Docker
docker build -t cripto-lab:latest .

# 3. Rodar
docker run -it -p 8080:80 --rm cripto-lab:latest

# 4. Acessar
open http://localhost:8080  # macOS
xdg-open http://localhost:8080  # Linux
start http://localhost:8080  # Windows
```

---

**Desenvolvido para FIAP | 2026**
